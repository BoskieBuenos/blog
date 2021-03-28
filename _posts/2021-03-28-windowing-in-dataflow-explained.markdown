---
title:  "Window i Watermark w Dataflow i Apache Beam"
date:   2021-03-28 20:00:00 +0200
categories: dataflow
---

**Apache Beam** jest implementacją modelu przetwarzania danych “**Dataflow**”. Ten model jest usystematyzowanym podejściem do przetwarzania danych. Jest to podejście, które pozwala zbalansować pewne kluczowe aspekty przetwarzania dużych ilości danych, których napływ jest nieograniczony w czasie oraz nie jest wymagane zachowanie żadnego ich porządku. Kluczowe aspekty takiego przetwarzania danych to **dokładność**, **opóźnienie** i **koszt**. Czyli przetwarzając takie dane chcemy otrzymać maksymalnie dokładne wyniki, z jak najmniejszym opóźnieniem od pojawienia się danych oraz jak najmniejszym kosztem obliczeń. Stosunkowo łatwo jest uzyskać dokładne wyniki drastycznie zwiększając koszty lub opóźnienie ich otrzymania. Problemem jest balans. Przetwarzając dane zgodnie z modelem “Dataflow” można go osiągnąć.

> The Dataflow Model: A Practical Approach to Balancing **Correctness**, **Latency**, and **Cost** in Massive-Scale, Unbounded, Out-of-Order Data Processing

## Kiedy kończą się nieskończone dane?

Wyobraźmy sobie, że chcemy obliczyć częstość słów w książce. Żeby to zrobić możemy zgrupować takie same słowa i policzyć rozmiar każdej z grup. Jest to bardzo proste dla jednej książki - uruchamiamy program liczący, otrzymujemy wynik i program się kończy. Problem pojawia się kiedy chcemy zrobić to samo dla różnych książek w dowolnym momencie. Dlaczego jest to problem? Prześledźmy dokładnie co się dzieje w programie:
1. Odczytanie każdego słowa z książki (i wprowadzenie go do pipeline’u);
2. Przypisanie dla każdego ze słów *Klucza* - *Kluczem* jest samo słowo, bo jest to cecha będąca podstawą obliczania częstości;
3. Grupowanie - zebranie słów o tych samych kluczach;
4. Obliczenie rozmiaru każdej z grup.

Hmm… W ten sposób policzymy częstość słów we wszystkich dostarczonych książkach. Musimy jakoś rozdzielić słowa pochodzące z pierwszej książki od słów z książki drugiej.

## Problem 1: Jak podzielić dane na porcje?

Książka 1 wydarzyła się w programie jako pierwsza, więc oznaczymy ją jako **EVENT 1**. Książka 2 wydarzyła się w programie jako druga, więc ją oznaczymy jako **EVENT 2**. Stworzymy w ten sposób własną przestrzeń czasu. Wartości z tej przestrzeni (i dowolnej innej przestrzeni czasu) pozwalają nam określić, które wydarzenie (event) z dwóch było pierwsze, a które drugie. To tak samo jak w rzeczywistym świecie - wydarzenie z 8:59 (*EVENT 8:59*) miało miejsce przed wydarzeniem z 9:00 (*EVENT 9:00*).

Model “Dataflow” pozwala porcjować dane, które mają być przetwarzane wspólnie, tylko w przestrzeni czasu. Można np. określić, że jedną porcją danych są dane, które wystąpiły w 10 sekundowym **oknie czasowym**, ale nie można określić, że przetwarzane porcje mają mieć N elementów. Można natomiast zdefiniować własną przestrzeń czasu! Dokładnie to zrobiliśmy w naszym przykładzie. Książka 1 wystąpiła w “czasie” 1, a książka 2 w “czasie” 2. Żeby przetworzyć je w osobnych porcjach, wystarczy przypisać książki do okien czasowych o rozmiarze <1.

<style>
code { font-family: monospace }
r { color: red; font-weight: bold; font-family: monospace }
g { color: green; font-weight: bold; font-family: monospace }
b { color: blue; font-weight: bold; font-family: monospace }
</style>

W tym momencie, z punktu widzenia modelu *Dataflow* nasze dane są przetwarzane w postaci trójwartościowych krotek typu (<r>value</r>, <g>event-time</g>, <b>window</b>):

```js
[ (książka_1, EVENT 1, [1,2)), (książka_2, EVENT 2, [2,3)) ]
```

...a po wczytaniu wyrazów z książek dane w programie będą wyglądać następująco:
```js
[ ("word 1", EVENT 1, [1,2))...("word N", EVENT 1, [1,2)),
("word N+1", EVENT 2, [2,3))...("word M", EVENT 2, [2,3)) ]
```

Dzięki temu, że rozdzieliliśmy dane na oddzielne porcje z pierwszego oraz drugiego okna czasowego, fragment programu obliczającego częstość odpowiedzialny za grupowanie, wie że grupy mają powstać na podstawie Klucza oraz okna czasowego. Nie wszystkie dane będą dostępne od razu do grupowania. Słowa będą napływać do buforu grupowania i zostaną zgrupowane dopiero, kiedy wszystkie słowa z książki dotrą do bufora. Ale kiedy wiadomo, że wszystkie słowa dotarły do bufora?

## Problem 2: Czy wszystkie dane z okna dotarły?

W naszym przykładzie wystarczającą informacją jest **watermark**. Watermark jest minimalną wartością event-time spośród danych, które pozostały do przetworzenia. Jeśli watermark jest późniejszy niż granica okna, to znaczy, że można przetworzyć dane w oknie.

Załóżmy, że treść pierwszej książki to `"Ala ma kota"`, a drugiej `"Jaś i Małgosia"`. Dane w programie będą wyglądać następująco:
```js
[ ("Ala", EVENT 1, [1,2)), ("ma", EVENT 1, [1,2)), ("kota", EVENT 1, [1,2)),
("Jaś", EVENT 2, [2,3)), ("i", EVENT 2, [2,3)), ("Małgosia", EVENT 2, [2,3)) ]
```

Po częściowym przetworzeniu danych, pozostało słowo *Ala* z pierwszej książki i *Małgosia* z drugiej:
```js
[ ("Ala", EVENT 1, [1, 2)),
("Małgosia", EVENT 2, [2, 3)) ]
```
```
Watermark: EVENT 1
```

Watermark ma wartość `EVENT 1`, ponieważ do przetworzenia pozostały dane z pierwszej książki. Po przetworzeniu słowa `"Ala"` stan programu zmienia się na poniższy:
```js
[ ("Małgosia", EVENT 2, [2, 3)) ]
```
```
Watermark: EVENT 2
```

Ponieważ wszystkie słowa z książki 1 zostały przetworzone, wartość **watermark** zmienia się, odzwierciedlając najmniejszy event-time spośród nieprzetworzonych danych. Do przetworzenia pozostało słowo `"Małgosia"`. Wartość *watermark* wynosi teraz `EVENT 2`, zatem przekroczyła granicę pierwszego okna. Oznacza to, że wszystkie dane z tego okna zostały przetworzone, są kompletne oraz można wykonać na nich grupowanie.

Powyższy przykład ma na celu zobrazowanie idei działania mechanizmu okien i wartości *watermark* w modelu *Dataflow*. Jest bardzo uproszczony, na pewno nie wyczerpuje tematu, a wręcz pomija wiele problemów i możliwych komplikacji, które występują w rzeczywistych zastosowaniach Dataflow. Zagadnienia te są obszernie opisane w publikacji autorów modelu Dataflow i do jej lektury zachęcam.

## Bibliografia ##

1. [Akidau, Tyler, et al. - The dataflow model: a practical approach to balancing correctness, latency, and cost in massive-scale, unbounded, out-of-order data processing. (2015)](https://storage.googleapis.com/pub-tools-public-publication-data/pdf/43864.pdf)

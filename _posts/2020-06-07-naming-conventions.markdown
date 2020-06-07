---
title:  "Nazwy zmiennych, notacje i konwencje nazewnicze"
date:   2020-06-07 22:30:00 +0200
categories: code-for-humans
---

Języki programowania można potraktować jak języki mówione, z bardziej usystematyzowaną gramatyką. Konsekwentne opisywanie pewnych modeli mentalnych odpowiednimi słowami sprawia, że rozmowa między osobami przebiega gładko. W przeciwnej sytuacji, kiedy strony posługują się wymieszanymi pojęciami, to nie będą w stanie się zrozumieć.

Języki programowania również służą do komunikacji - pomiędzy programistami. "Programs are meant to be read by humans and only incidentally for computers to execute". Każde rozszerzenie funkcjonalności, każde code review, każda refaktoryzacja, innymi słowy każdy dzień pracy nad kodem źródłowym, tworzonym przez zespół utwierdza mnie w prawdziwości przesłania płynącego z powyższego zdania autorstwa Donalda Knutha.

## Konwencja ##

Zanim przejdę do samych nazw, chciałbym zacząć od początku. Kod powinien być spójny pod względem stylu pisania na przestrzeni wszystkich plików źródłowych. To znaczy, że wszystkie elementy jak zmienne, funkcje, stałe, klasy, typy powinny wszędzie wyglądać tak samo. W przeciwnym wypadku programista będzie narażony na konieczność uczenia się stylu każdego pliku z kodem na nowo i przełączania się między tymi stylami w ramach jednego projektu.

Konwencji w ramach projektu powinno zostać ustalonych tyle, że w trakcie pracy nie będą się pojawiać chwile zawahania: "jak to powinno wyglądać?". Zacząć można od identyfikatorów (nazw) zmiennych, funkcji i klas. Konwencje często są powszechnie przyjęte przez środowiska programistów związanych z pewnymi językami. Pisząc kod w Javie powszechne jest nazywanie zmiennych i funkcji camelCasem, klas PascalCasem, a stałych SNAKE_CASE_ALL_CAPSem. W Pythonie zmienne i funkcje przyjęło się nazywać snake_casem. Następnie warto ustalić politykę dot. wcięć. Jeden tab, czy 2 lub 4 spacje? Co z wcięciami w wyrażeniach wielolinijkowych? I tak dalej...

Dzięki konwencjom ograniczony zostaje narzut na odnalezienie się w kodzie oraz uchwycenie znaczenia poszczególnych jego elementów. Mniej oczywistą zaletą, ale przynajmniej równie cenną jest to, że "zły kod widać od razu". Mając wystarczające doświadczenie w projekcie, błyskawicznie po otworzeniu pliku, bez jego czytania, można poczuć, że coś jest nie tak.

## Cel nazwy (co powinno przyświecać twórcy) ##

Na początku warto się zastanowić do czego służą identyfikatory i jakie zadania powinny spełniać. Można to potraktować jak checklistę pozwalającą w łatwy sposób sprawdzić czy nazwa jest poprawna i właściwie dobrana.

Identyfikatory powinny spełniać następujące role:
1. **Jednoznacznie i precyzyjnie** opisywać koncept który nazywają,
2. Opisywać koncept w **kontekście** domeny biznesowej procesu opisanego kodem,
3. **Spójnie** opisywać koncept który nazywają,
4. Opisywać koncept a nie go **~~definiować~~**.

Jednoznaczne i precyzyjne identyfikatory powinny dobrze przedstawiać koncept oraz być łatwe do rozróżnienia między sobą. Osadzenie nazw w kontekście domeny biznesowej ułatwi ogólne zrozumienie zaimplementowanego procesu. Zachowanie spójności nazewniczej pomiędzy różnymi częściami projektu ułatwi zrozumienie zależności między procesami i elementami domeny biznesowej. W końcu identyfikatory opisujące koncept a nie go definiujące stworzą pożądaną warstwę abstrakcji między przestrzenią pojęciową a szczegółami implementacyjnymi.

## **Jednoznacznie i precyzyjnie** opisywać koncept który nazywają ##

### Jednoznaczność ###
Jednoznaczność identyfikatora oznacza, że jego znaczenie jest oczywiste oraz od razu możliwe do powiązania z konkretnym bytem. To znaczy, że zmienne przechowujące informacje o podobnej naturze nie nazywają się `x1` i `x2`, `items1` i `items2` albo `employee1` i `employee2`. W takiej sytuacji należy zagłębić się w znaczenie danej wartości i wykorzystać jej unikalną, precyzyjnie określoną rolę w identyfikatorze, nazywając zmienne `viewport_x` i `window_x`, `imported_items` i `cached_items` albo `fired_employee` i `replacement_employee`.

Jednoznaczność często bywa złamana przez wykorzystanie w identyfikatorach słów o bogatym znaczeniu. Dwie osoby mogą taki identyfikator przypisać do zupełnie innych konceptów, a rozmawiając o nim całkowicie się nie rozumieć. Za przykład niech posłuży "source". W zależności od kontekstu, tak określoną wartość można rozumieć jako obiekt źródłowy, adres url źródła, tekst wejściowy, węzeł rodzic w drzewie, węzeł korzeń w drzewie, itd. Możliwych znaczeń jest wiele i co ważne, wiele z nich może oznaczać elementy obecne w kodzie w tym samym czasie. Mam na myśli, że całkowicie możliwa jest sytuacja kiedy dane pobrane z jakiegoś api (źródła), mogą być przechowywane w postaci drzewa, które ma korzeń (źródło), a każdy z liści ma rodzica (#wiadomo) - wszystkie te rzeczy są obecne w jednym fragmencie kodu.

Sytuacja może zrobić się jeszcze bardziej zagmatwana jeśli, przy odrobinie nieuwagi w kodzie z powyższego przykładu, obok siebie znajdą się wyrazy o bardzo zbliżonym znaczeniu: source, parent i context. Użycie synonimów w identyfikatorach utrudnia rozróżnienie elementów domeny biznesowej i może być źródłem nieporozumień między kolejnymi kontrybutorami. Powiedzmy, że autor pewnej funkcji nazwie korzeń drzewa "context" a węzeł będący bezpośrednim rodzicem "parent". Kolejny programista, którego zadaniem jest dodanie obsługi nowego przypadku biznesowego przez wspomnianą funkcję traktując "parent" jako korzeń drzewa, a "context" jako węzeł rodzic może wpaść w nie lada kłopoty...

### Łatwość zrozumienia ###
Konwencje, oprócz syntaktyki, mogą obejmować również dobór **form językowych** w zależności od funkcji identyfikatora. Warto je stosować z tych samych powodów - ułatwiają przyswojenie kodu przez czytającego przez odróżnienie metod, zmiennych, typów i klas od siebie.

Jeśli identyfikator opisuje funkcję, to z reguły składa się on z czasownika w formie rozkazującej. Np. `add`, `delete`, `split`, `trim`, `getName`. Zmienne są opisywane rzeczownikami w liczbie pojedynczej. Zmienną przechowującą kolekcję wartości można nazywać rzeczownikiem w liczbie mnogiej jeśli nie da się jej opisać słowem w liczbie pojedynczej, które ma sens w kontekście domeny biznesowej. Klasy i typy powinny być identyfikowane za pomocą "frazy nominalnej" (ang. noun phrase). Np. `String`, `Fruit`, `AuthorizationService`.

**Długość** to jeden z aspektów identyfikatora, który ma duże znaczenie w procesie jego przyswajania. Generalnie identyfikatory są nieczytelne kiedy są zbyt krótkie lub zbyt długie. Krótkie identyfikatory niosą zbyt małe znaczenie, przez co tracą na jednoznaczności. Długie wymagają "mozolnego" czytania. Badanie Phillipa Relfa wskazuje, że **identyfikatory powinny składać się z dwóch do czterech słów.** Warto starać się szukać tych o precyzyjnym znaczeniu, pozwalających zastąpić ogólny rzeczownik z precyzującym go przymiotnikiem. Np. `employee` zamiast `company_person`.

Precyzyjne nazwy składające się z ogólnych wyrazów mogą być niepotrzebnie długie i w natłoku wyrazów między sobą trudne do rozróżnienia. Np. `company_person_task_distribution_priority_relations`, które mogłoby być zastąpione przez `employee_authority_hierarchy`. W poniższym przykładzie widać trudność w rozróżnianiu identyfikatorów, kiedy są one bardzo długie:

```python
for relation_between_specific_values in relations_between_specific_values:
    processed_relations_between_specific_values.add(
        process_relation_between_specific_values(relation_between_specific_values)
    )
```

Można zauważyć wzrost przyswajalności powyższego kodu, kiedy ograniczymy długość nazw, mimo zachowania ich podobieństwa:

```python
for relation in relations:
    processed_relations.add(
        process_relation(relation)
    )
```

lub kiedy zmienimy długie nazwy tak, żeby były bardziej od siebie odróżnialne:

```python
for well_characterized_relation in relations_between_specific_values:
    processed_target_data_model.add(
        resolve_interaction_subjects_tuple(well_characterized_relation)
    )
```

Unikanie skracania nazw też jest dobrą praktyką. Nawet najbardziej oczywiste dla autora kodu skróty, mogą się okazać ambiwalentne dla kogoś innego. Skróty dodają obciążenie poznawcze na rzecz bardzo niepewnego zysku płynącego z mniejszej liczby liter. Wyjątkami mogą być pewne ustalone na łamach zespołu mnemoniki występujące w kodzie bardzo często.

## Opisywać koncept w **kontekście** domeny biznesowej procesu opisanego kodem ##

Pułapką abstrakcji można nazwać taki opis pewnej rzeczywistości, że utracona zostaje możliwość powiązania opisu z tą rzeczywistością. To oznacza, że im bardziej ogólne pojęcia zostaną użyte do opisu domeny biznesowej, tym trudniej będzie przełożyć te pojęcia na elementy tej domeny. Na przykład zmienna reprezentująca samochód może zostać nazwana `car`, ale również `vehicle`, `mean_of_transport`, `device`, `good` lub `object`. Należy pamiętać, że kod powinien być jak najłatwiejszy do zrozumienia. To oznacza: zrozumienie co się dzieje, jaki rzeczywisty proces reprezentuje, a także co jest przedmiotem tego procesu. Łatwiej jest zrozumieć o co chodzi jeśli "a car hits a car" niż "a device reacts with a device'.

Powyższa reguła również dotyczy modelowania domeny w programowaniu obiektowym. Należy stosować takie nazwy żeby były tylko tak ogólne jak to jest potrzebne.

## **Spójnie** opisywać koncept który nazywają ##

Tutaj mam na myśli nazywanie tych samych rzeczy dokładnie (sic!) tak samo w całym projekcie. Trzymając się tej zasady od razu wiadomo czy coś czymś jest czy tym czymś nie jest… 😵 Przykład. W jednym miejscu pracownik jest reprezentowany przez zmienną `company_person` a w innym `employee`. Kiedy w projekcie nie jest przestrzegana zasada spójności, to nie mamy pewności czy chodzi o ten sam byt czy o inny. Może się też nasuwać myśl:
> "Chyba chodzi o tego samego pracownika… Z drugiej strony zostało nazwane inaczej, więc pewnie coś jest na rzeczy. Prześledzę całe drzewo wyrażeń żeby to sprawdzić."

W ten sposób programista marnuje czas i energię na upewnienie się, że niczego nie przeoczył.

Taki sam problem może spowodować okazyjne skracanie części nazw. Nadal korzystając z przykładu pracowników: stosowanie raz `c_person`, a raz `company_person`. To samo dotyczy pomijania części z wyrazów składających się na identyfikator - `company_person` i `person`. Lub tasowanie słowami: `company_person` i `company_employee`.

## **Opisywać** koncept a nie go **~~definiować~~** ##

Identyfikator nie powinien być związany z implementacją. To znaczy, że określenia reprezentujące pewne koncepty (identyfikatory) powinny pozostać takie same, nawet jeśli proces opisywany nie jest w języku programowania tylko w języku naturalnym. Dzięki temu członkowie zespołu powinni być w stanie swobodnie rozmawiać o problemie.

Powyższą zasadę można sprowadzić do zakazu umieszczania informacji o reprezentowanym typie w identyfikatorze. Teraz na myśl wszystkim przychodzi pewnie notacja węgierska. Słusznie. Mimo że tak naprawdę jej wariant, który jest znienawidzony przez wszystkich jest wypaczeniem pierwotnego pomysłu, którego autorem jest Charles Simonyi. Cała historię świetnie opisał na swoim blogu Joel Spolsky, dlatego ja tego nie zrobię.

Przyjęło się uważać, że informacja o typie w identyfikatorze&nbsp;=&nbsp;notacja węgierska, ale to nieprawda. Informacją o typie może być również użycie słowa list w nazwie zmiennej przechowującej kolekcję (np. `items_list`).

Szczegóły implementacyjnie to nie tylko typ danych. Wyobraźmy sobie wysokopoziomową funkcję zwracającą czysty bufor (powiedzmy, że implementujemy serwis streamingowy). Zakładamy, że z punktu widzenia "użytkownika" takiej funkcji nie ma znaczenia czy zwrócona struktura danych jest nowa czy też jest strukturą wyczyszczoną i wykorzystaną ponownie. Nazwanie takiej funkcji `clear_buffer` lub `restore_buffer` nie jest dobrym pomysłem, ponieważ wiąże nazwę z konkretną implementacją takiej funkcji. Takie związanie grozi "rozsynchronizowaniem" się kodu przy zmianie implementacji funkcji lub typu danych.

## Podsumowując… ##

Podczas pisania kodu najważniejsi są programiści. To oni będą go czytać i o nim rozmawiać, modyfikować i naprawiać oraz sprawdzać i dokumentować. Powinni móc zrozumieć go najszybciej i najłatwiej jak to możliwe.

Wymyślone na szybko, złe nazwy mogą przysporzyć więcej kłopotu niż zysk płynący z tych kilkunastu sekund nie poświęconych na ich poprawny dobór. Wprowadzają zamieszanie i szum mogący całkowicie uniemożliwić zrozumienie kodu.

Odpowiednio dobrane nazewnictwo może spowodować, że kod sam będzie się dokumentował, a projekt zamiast przypominać wieżę babel będzie przypominał rozmowę między przyjaciółmi.

## Bibliografia ##

1. [Peter Hilton, Felienne Hermans - Naming guidelines for professional programmers (2017)](https://hilton.org.uk/presentations/naming-guidelines)  (Zbiór i przegląd wytycznych dot. nazewnictwa w kodzie)
2. [Bonita Sharif, Jonathan I. Maletic - An Eye Tracking Study on camelCase and under_score Identifier Styles (2010)](https://www.researchgate.net/publication/224159770_An_Eye_Tracking_Study_on_camelCase_and_under_score_Identifier_Styles) (Badania pokazujące lepszą czytelność snake_case względem camelCase)
3. [Phillip Relf - Source Code Readability Improvement Using Heuristic-Based Dynamic Error Reporting During Editing - doctoral thesis (2007)](https://opus.lib.uts.edu.au/handle/10453/37154)
4. [Joel Spolsky - Making Wrong Code Look Wrong (2005)](https://www.joelonsoftware.com/2005/05/11/making-wrong-code-look-wrong/) (m.in. o notacji węgierskiej)

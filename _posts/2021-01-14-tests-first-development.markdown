---
title:  "Pokaż mi że kod działa - Tests-First Development"
date:   2021-01-14 11:00:00 +0200
categories: quality
---

Skąd wiesz, że Twój kod działa? Możesz mieć taką pewność, tylko jeśli go uruchomisz i po kolei sprawdzisz wszystkie przypadki jego użycia. Ale! Sprawdzenia, które wykonałeś stają się niewiarygodne w momencie wprowadzenia kolejnej zmiany w programie. Nie ważne jak “daleko” od twojego kodu ona nastąpiła... 

Załóżmy, że pracujesz w zespole programistycznym i właśnie zaprogramowałeś nową funkcjonalność w waszym projekcie. Nawet jeśli uruchomiłeś swój kod i sprawdziłeś wszystkie przypadki jego użycia, to pozostaje Ci “jedynie” przekonanie recenzenta (aka code-reviewer), że twój kod rzeczywiście działa poprawnie. Gdybym to ja był tym recenzentem, to bym Ci nie uwierzył na słowo, ale sęk w tym, że to Ty nie powinieneś sobie uwierzyć w pierwszej kolejności. Każdy programista jest zwykłym człowiekiem, więc również popełnia błędy. Możesz wierzyć we własną nieomylność. Możesz nawet być nieomylnym, ale kiedy w grę wchodzą duże projekty, na scenę wkraczają oni - inni. Najprawdopodobniej nie będzie to All-Star Programming Dream Team, tylko raczej Ty, Marcin “programista 15k”, Łukasz “kończę o 17:00 i wychodzę”, no i oczywiście… Junior. Któreś z was na pewno coś sknoci i to zdecydowanie prędzej niż później. Najlepsze w oprogramowaniu jest to, że nawet jeśli Twój kod działa, to może przestać to robić, po zmianie zupełnie go niedotyczącej.

Wniosek z powyższego jest prosty. Przed wmerdżowaniem każdej zmiany do “mastera”, należy uruchomić wszystkie przypadki użycia, wszystkich dotychczas zaimplementowanych funkcji. Bez tego nie można mieć pewności, że kod działa poprawnie.

Stosując takie podejście i chcąc pisać działający kod, nie można dopuszczać do wdrażania oprogramowania pozbawionego testów. Szczególnie jeśli wymagania często się zmieniają i zapanowanie nad całością projektu jest utrudnione! Uważam, że traktowanie testów jako zła koniecznego nie jest właściwe. Wręcz przeciwnie! Sądzę, że pisanie testów może pomóc w czerpaniu przyjemności z programowania i znacząco ograniczyć frustrację.

Przede wszystkim nie powinno się testów dopisywać na końcu. Z reguły kiedy tak robiłem, wyglądało to następująco... Pisałem jakiś kod, który w mojej głowie miał realizować pewne operacje, aż w pewnym momencie stwierdziłem, że jest gotowe i działa. Następnie decydowałem, że “przeklikam” funkcjonalność, żeby pokazać sobie jaki to ja nie jestem nieomylny. Oczywiście nie działało, więc wracałem do pisania kodu. Cykl powtarzał się kilkakrotnie, a z każdym powtórzeniem byłem coraz bardziej sfrustrowany i coraz mniej zwracałem uwagę na to, jak wygląda kod. Kiedy w końcu rozwiązanie rzeczywiście działało, dopisywałem parę testów i kod oddawałem do recenzji. Po cichu miałem nadzieję, że kod będzie działał, ale pewności nie miałem, bo moje ostatnie “przeklikanie” ciężko już było nazwać rzetelnym. W trakcie recenzji oczywiście okazywało się, że coś jest nie tak.

Testy należy napisać na początku i to tak, żeby rzeczywiście sprawdzały wszystkie przypadki, corner-case’y i wymagania. Powinny być takie, że kiedy przechodzą, to z czystym sumieniem można powiedzieć, że sprawdzany przez nie kod działa poprawnie. 

Po napisaniu testów na początku, jesteś zdecydowanie lepiej przygotowany do pisania właściwego kodu. To jest druga korzyść płynącą ze stosowania podejścia "Tests First". Napisałeś testy, więc wiesz już dokładnie co jest Twoim celem oraz pomyślałeś o wszystkich przypadkach. Dzięki przeprowadzonej analizie jesteś w stanie dużo lepiej dobrać odpowiednią architekturę i odpowiednio podzielić kod na części (np. klasy), mające jasno określone odpowiedzialności.

W takich okolicznościach programowanie jest dużo przyjemniejsze, ponieważ towarzyszy mu poczucie podążania do jasnego celu i kontroli nad tym co się dzieje. Najważniejsze jest jednak to, że kiedy skończysz, to wiesz że Twój kod działa, a kiedy to się zmieni, dowiesz się o tym od razu.

+++
title = "Suckless po mojemu"
type = "post"
date = "2021-04-20T20:18:00+02:00"
+++
Tym razem znów o narzędziach z których korzystam. Zacznę od tego jak na to trafiłem. Oglądałem sobie tak z nudów youtuba i wpadł mi film Zmasło o tym, że zainstalował archa. Wtedy jeszcze nie byłem przekonany do tej dystrybucji, ale przejrzałem sobie co on tam ma - tiling managery do mnie nie przemawiają, ale zainteresował mnie st - simple terminal (lub jak inni wolą mowić suckless terminal). Zacząłem się interesować narzędziami suckless i miałem trochę mieszane uczucia. Z tego samego filmu dowiedziałem się też o kanale Luke'a Smitha. Gość opowiada fajne rzeczy o linuksie (czasem ogólnie, czasem specyficznie o archu), minimalistycznych narzedziach, filozofii wolnego oprogramowania - generalnie polecam. 

Luke Smith udostępnił kilka ciekawych modyfikacji do st i spodobały mi się. Niestety, przynajmniej jedna z nich jest łatwa do ogarnięcia tylko na archu, ale to już dla mnie nie problem 😀. Tak zacząłem korzystać z st w miejsce urxvt.

Może trochę powiem o suckless. Te narzędzia są dosyć specyficzne. Filozofia suckless to narzędzia proste, możliwie przejrzyście napisane, o jak najmniejszej ilości kodu (czyli bez niepotrzebnych funkcjonalności). Tu przyładowo o st - xterm ma ok. 65k linii kodu, przedewszystkim ze względu na wsparcie dla emulacji różnych archaicznych terminali. Rxvt-unicode lub inaczej urxvt ma ok. 32k linii kodu. st ma ok 7k linii kodu. Jednak nie wszystko jest takie piękne. Narzędzia suckless oszczędzają linie kodu rezygnując także z plików konfiguracyjnych. Konfiguracja we wszystkich programach odbywa się poprzez edycję pliku config.h i przebudowanie programu. Z tego wynika kolejny problem - nie da się tego spaczkować i umieścić np. w repozytorium archa lub debiana - bo nie można tego konfigurować.

I tu zaczyna się moje ale do suckless. O ile terminal konfiguruje się w zasadzie raz i tyle, to w pakiecie suckless jest program do prowadzenia prezentacji - sent. Niby fajne, ale aby zmienić kolor tła lub font w prezentacji należy przekompilować sent. To nie pasuje.

Suckless ma teź swoj manager okien, jednak jest to manager kafelkowy (chyba tak należałoby przetłumaczyć tiling), ale to mi nie leży i na razie zostanę przy openboksie.

Jednak kolejne narzędzie tu już co innego - dmenu. W skrócie - jest to dynamiczne menu, z którego bardzo miło korzysta się w skryptach. Obsługiwane oczywiście w całości klawiaturą. W którymś następnym wpisie opiszę skrypty, z których korzystam.

Podsumowując - część rozwiązań suckless jest rzeczywiście fajnych, a ich minimalizm naprawdę fajny. Jednak druga część tych narzędzi to suckmore - zdecydowanie odbiegające od reszty, niezbyt przemyślane. Mam wrażenie, że taki sent był napisany po to aby przeprowadzić jedną prezentację, ewentualnie - programista napisał go tylko dla siebie i pod siebie.

Tyle... 

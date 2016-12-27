+++
title = "ack"
type = "post"
draft = false
date = "2016-12-27T21:10:15+01:00"

+++

Przeszukiwanie kodu z konsoli jest problemem. Ja najczęściej używałem dziwnej składni 
```
find . -type f | while read l; do grep -Ri "System.err" $l && echo $l; done
```

Działać działa, ale jest nieeleganckie, nieefektywne, ma brzydkie wyjście, dużo błędnych wystąpień (pliki binarne, pliki gita/svna). Słowem - szybki hack.

Ze względu na moje lenistwo nie chciało mi się szukać niczego innego, a okazało się że było warto. O acku dowiedziałem się z prezentacji babuna. 

Teraz kilka krótkich informacji:

* pomija pliki niebędące kodem (jak np. pliki gita czy svna)
* jest szybki
* jest nastawiony na przeszukiwanie kodu - ale nie "promuje" żadnego języka
* jest przenośny - napisany w perlu, ja go uzywam na linuksie i widzie i nie mam problemu
* ułatwia przeszukiwanie tylko określonych zasobów - tu może kilka słów więcej.

Generalnie - rzadko kiedy mamy projekt robiony tylko używając jednej technologii. Do projektu javowego dodany SQL czy też moje najczęstsze połączenie ostatnimi czasy czyli xsl + xsl. Do tego wystarczy dodać prarametr
```
ack --java System.err
```
Coś takiego znajdzie wystąpienia System.err w plikach java, ale już pliki tekstowe odrzuci.

W razie czego - wiem że istnieje silver searcher (ag), ale jeszcze nie miałem czasu go sprawdzić :)

http://beyondgrep.com/

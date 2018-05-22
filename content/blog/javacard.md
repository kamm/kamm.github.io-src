+++
title = "Programowanie kart inteligentnych - część 0 - inicjalizacja"
type = "post"
draft = false
date = "2018-05-22T11:02:15+01:00"

+++

Zaczynam naukę programowania kart inteligentnych. Są to karty do przechowywania kluczy kryptograficznych, certyfikatów a także innych danych. Do tego celu potrzebowałem zamówić kilka takich kart.

<!--more-->

Po wpisaniu na aliexpress J2A040 znajdziemy ich wiele i w każdym ogloszeniu jest coś w stylu

```
We can only provide the TK default value (Transport key) ,like: .
Before you use JCOP Card,you need 2 commands for opening the JCOP21-40K card.
here is :
APDU:00A4040010( TK - KEY )
APDU:00F00000 ( open for Initialize )
If you don’t know how to use it ,please don’t order it! Otherwise, we will not be responsible for the return.
```

Dobra, rozumiem, że tak się zabezpieczają przed osobami, które myślą, że to od razu bedzie działać "automagicznie". Nie ma tak łatwo.

Po pierwsze karta jest zabezpieczona kluczem transportowym. Jest on na dołączonej płycie CD. Fajnie, chociaż poprawnie to klucz transportowy (dalje będę go po prostu nazywał TK) powinien być wysłany innym kanałem (np. mailem), ale mniejsza z tym - kart nie będę używał produkcyjnie, a bardziej do celów deweloperskich i testowych.

Po drugie karta jest niezainicjalizowana - chociaż tego nie jestem pewien, bo przedtawiała się jako Nigerian ID. Mniejsza z tym - kasujemy wszystko.

Do zabawy potrzebne są:

* czytnik kart - to akurat wiadome
* narzędzie gpshell https://sourceforge.net/projects/globalplatform/

Pełny skrypt do inicjalizacji:

```
mode_211
enable_trace
establish_context
card_connect

#Oblokowanie z klucza transportowego
send_apdu -sc 0 -APDU 00A4040010<TK>

#Wyczyszczenie karty
send_apdu -sc 0 -APDU 00F00000

#Ustawienie protokołu transmisji T=1
send_apdu -sc 0 -APDU C0D6012301DA
send_apdu -sc 0 -APDU C0D6014601DA
send_apdu -sc 0 -APDU C0D6012201FE
send_apdu -sc 0 -APDU C0D60124010F
send_apdu -sc 0 -APDU C0D60147010F

#Ustawienie ATR jako J 2 A 0 8 1 , T = 1
send_apdu -sc 0 -APDU c0d601370b0a4a32413038312C543D31
send_apdu -sc 0 -APDU c0d6015A0b0a4a32413038312C543D31

#Ustawienie klucza zarządczego na domyślną wartość 404142434445464748494a4b4c4d4e4f (tylko na potrzebny testowe/deweloperskie)
send_apdu -sc 0 -APDU c0d6030510404142434445464748494a4b4c4d4e4f
send_apdu -sc 0 -APDU c0d6032110404142434445464748494a4b4c4d4e4f
send_apdu -sc 0 -APDU c0d6033D10404142434445464748494a4b4c4d4e4f
```

Teraz można zacząć się bawić. Taka karta nie zostanie zablokowana - zawsze można wsyztsko skasować i wgrać ponownie. 

Na podstawie https://www.curriegrad2004.ca/2017/02/dealing-with-unfused-jcop-java-cards-sold-from-aliexpress-or-ebay/
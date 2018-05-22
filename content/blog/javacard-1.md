+++
title = "Programowanie kart inteligentnych - część 1 - pierwszy program"
type = "post"
draft = true
date = "2018-05-23T11:02:15+01:00"

+++

Czas zacząć programować. Karty, które mnie intereują i które zamówiłem programuje się w Javie. Są też inne, chyba dotnetowe, ale się nie znam. Zajmę się tylko kartami javoskimi, a dokładniej będę uzywał Java Card Kit w wersji 2.2.2. Do pobrania ze strony Oracla: [JavaCard SDK](http://www.oracle.com/technetwork/java/embedded/javacard/downloads/javacard-sdk-2043229.html).

Programy wgrane na kartę są apletami, ale nie ma to nic (poza nazwą) związku z java.applet.Applet. Tu mamy javacard.framework.Applet. Generalnie - javacard jest mocno ograniczoną javą. Nie ma longa, nie ma double'a, nie ma stringa. Łącznie javadoc do wersji 2.2.2 podaje, że istnieją 93 klasy, a w zasadzie to 60 klas i 33 interfejsy zdefiniowane w ramach biblioteki standardowej. Trochę mało. Dodatkowo ogranicza nas mała pamięć karty i wymagania aby działało to możliwie najszybciej. W efekcie programy wyglądają jak kod pisany dla mikrokontrolerów 8 bitowych.


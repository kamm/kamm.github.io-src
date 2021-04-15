+++
title = "screen - manager okien w terminalu tekstowym"
type = "post"
draft = false
date = "2021-04-13T16:37:15+02:00"
+++

Cześć.

Od czasu pojawienia się koronawirusa cała moja praca odbywa się zdalnie. Niestety, wiele osób narzeka na stabilność VPNa - tym się nie będę zajmował ani wnikał w powody i propozycje rozwiązania. Zaproponuję pewną protezę, która pomoże w tym przypadku a jednocześnie da kilka innych możliwości pracy na systemach uniksowych.

Generalnie pomysł jest prosty i jest obecny np. przy połączeniu pulpitem zdalnym - czyli gdy zostanie zerwane połączenie sieciowe z serwerem to sesja jest odłączana a nie zrywana. Oznacza to tyle, że gdy stracimy połączenie sieciowe to procesy uruchomione na pulpicie zdalnym cały czas działają i po pierwsze możemy je tak zostawić gdy chcemy pozostawić jakiś długo działający proces albo zabezpieczyć się przed właśnie takim zerwaniem połączenia - np. wykonujemy jakiś update na bazie danych lub budujemy coś mavenem. Tak samo jest gdy używamy VNC. Nie będę wnikał w inne rozwiązania bo ich nie znam.

Ale nie zawsze chcemy używać środowiska graficznego - do wielu rzeczy wystarcza terminal - czyli wystarczy nam połączenie ssh. Jednak przy połączeniu SSH jeżeli połączenie zostanie zerwane to proces zostanie ubity. Czyli zbudowanie na serwerze aplikacji mavenem, gdzie budowa trwa godzinę nigdy się nie uda, gdy VPN zrywa połączenie po 30 minutach.

I tu wchodzi cały na biało (he he) screen. Oczywiście, ktoś powie "tmux może to samo i nawet więcej". Tak, ale screen jest dużo prostszy w użyciu i do tego zastosowania wystarczy. Jednocześnie jest on dobrym wstępem do późniejszego użycia tmuxa.

Czym jest screen? Jak mówi _man_ jest to pełnoekranowy manager okien, który multipleksuje fizyczny terminal pomiędzy kilkoma procesami. 

No dobra, ale to chyba nadal nie rozwiązuje problemu?

Otóż _nic bardziej mylnego_. Taki zestaw okien można _odpiąć_ i rozłączyć się z SSH, a procesy w nim uruchomione nadal będą działać. Gdy sesja, w której był uruchomiony screen zostanie zabita to screen się odłączy. Później można ponownie takiego screena podłączyć i kontynuować pracę.

Najpierw instalacja, wprost z repozytorium odpowiedniego dla używanej dystrybucji.
```
sudo apt-get install screen
sudo pacman -S screen
```

Konfigurować nie ma co - jedynym powodem do utworzenia pliku konfiguracyjnego jaki miałem była chęć zmiany domyślnego skrótu klawiszowego - Ctrl+a - na coś innego. Ale to było związane z tym, że zacząłem używać tmuxa, jego domyślny skrót mi nie pasował (Ctrl+b), zmieniłem go na ten, do którego byłem przyzwyczajony w screenie więc screena też trzeba było zmienić, ale to rodziło kolejne problemy i przestałem tak grzebać.

Teraz czas na uruchomienie. Po prostu wpisujemy
```
screen
```

Czyli mamy Ctrl+a, polecenie które rozpoczyna właściwy skrót klawiszowy. Bo w końcu mamy różne skróty klawiszowe w różnych aplikacjach i chcielibyśmy móc z nich korzystać. O ile w vimie to nie problem, to już w emacsie używane są skróty z ctrl, a nawet bash ma ich kilka. Dlatego screen używa takich skrótów "kolejkowanych". Po wciśnięciu ```ctrl+a``` dajemy właściwy skrót (np. ```c```). Screen przyjmie je w dwóch możliwych wariantach i oba obsłuży tak samo - czyli ```ctrl+a c``` oraz ```ctrl+a ctrl+c``` - chodzi o to, czy puścimy ctrl przed drugim klawiszem czy nie. Dla screena to bez różnicy.

Najważniejszym skrótem jest ```ctrl+a d``` - jest to odpięcie screena, czyli zostawiamy go działającego i wychodzimy do punktu uruchomienia.

Aby wrócić do odpiętego screena wywołajmy
```
screen -r
```

Jeżeli screen jest gdzieś przypięty, np. w drugim xtermie to nie pozwoli się przypiąć. Wywołanie screen bez parametrów utworzy za to drugiego screena. Wtedy już nawet gdy oba będą odpięte to screen nie będzie wiedział do którego się podpiąć i wypisze listę - wtedy należy podać PID procesu do którego chcemy się podpiąć.

Aby odpiąć screena podpiętego w innym oknie (lub np. w wiszącej sesji ssh) wywołujemy ```screen -d```. A jeżeli chcemy od razu zakończyć też proces z którego został uruchomiony screen, który odpinamy (czyli np. zamknąć tamto okno xterma lub rozłączyć połączenie SSH) to używamy ```screen -D```.

Przydatne może być też ```screen -R```. Oznacza to, że screen ma się podłączyć do istniejącej sesji. Jeżeli jest kilka to wypisze możliwe do podłączenia. Ale jeżeli nie znajdzie żadnej (żadnej nie ma lub nie ma żadnej niepodłączonej) to utworzy nową. ```screen -RR``` zadziała podobnie, ale jeżeli znajdzie kilka niepodłączonych sesji to podłączy się do pierwszej z brzegu.

Opcje ```-r```, ```-R``` oraz ```-RR``` można łączyć z ```-d``` i z ```-D```. W takim wypadku uruchomienie ```screen -D -RR``` jest _najpotężniejsze_ - bo zawsze da jakąś sesję.

Teraz wróćmy do działającej sesji. Jeżeli zakończymy proces w screenie (ctrl+d, exit, logout, ...) to zamkniemy okno w screenie. Jeżeli zamkniemy ostatnie okno to zamkniemy całego screena.

Aby utworzyć nowe okno wywołujemy ```ctrl+a c```. Pierwsze okno ma numer 0, następne 1 itd. Przełączanie pomiędzy oknami osiągniemy używając ```ctrl+a 0```, ```ctrl+a 1``` itd. lub ```ctrl+a n``` i ```ctrl+a p```. Tak samo jak ```n``` zadziała ```spacja```, a w miejsce ```p``` możemy użyć ```backspace```.

Użycie ```ctrl+a w``` pokaże listę dostępnych okien.

Warto też wiedzieć jak zabić okno - czasem jakiś proces się zawiesi. W xtermie po prostu zamykamy okno, w screenie jest do tego ```ctrl+a k```.

Tak na prawdę to wszystko co potrzeba wiedzieć na starcie. No może jeszcze ```ctrl+a ?```, które zwróci przypisanie klawiszy. A, jeszcze - co jeżeli potrzebujemy użyć ```ctrl+a``` w aplikacji otwartej w screenie? używamy ```ctrl+a a```.

Z bardziej zaawansowanych rzeczy to zdarzyło mi się jeszcze korzystać z możliwości kopiowania w screenie przy użyciu klawiatury. Nie tyle do kopiowania ale do przewinięcia ekranu w górę. Służy do tego ```ctrl+a [```, następnie przesuwamy ekran strzałkami i wciskamy ```enter``` aby rozpocząć kopiowanie. Zaznaczamy obszar używając znów strzałek i wciskamy ponownie ```enter```. Teraz tekst jest skopiowany do schowka (ale screenowego, nie tego dostępnego przez np. xclip). do wklejenia służy ```ctrl+a ]```.

Innych funkcji chyba nie zdarzyło mi się używać, ale polecam mana do screena - jest chyba jednym z lepszych.

## Przenoszenie procesu do screena
Teraz coś trochę trudniejszego - jak przenieść działający proces do screena? Czasem okazuje się, że jednak proces będzie działał dłużej. Jak to zrobić? Będzie potrzebne dodatkowe narzędzie - ```reptyr```. Czyli

```
sudo apt-get install reptyr
sudo pacman -S reptyr
```
czy co tam macie u siebie. Teraz po kolei:

1. Zatrzymujemy proces (nie kończymy!!!) ```Ctrl+z```

2. Wznawiamy go w tle ```bg```

3. Odpinamy go od terminala ```disown %1```

4. Odpalamy screena ```screen```

5. Znajdujemy PID procesu (pojawił się wcześniej na ekranie albo szukamy przy uzyciu ps, pstree, pgrep, czegokolwiek)

6. Przypinamy proces do bieżącego terminala ```reptyr <pid>```

Reptyr może nie zadziałać - poleci komunikat
```
Unable to attach to pid 52543: Operation not permitted
The kernel denied permission while attaching. If your uid matches
the target's, check the value of /proc/sys/kernel/yama/ptrace_scope.
For more information, see /etc/sysctl.d/10-ptrace.conf
```
Generalnie to jest to zabezpieczenie, aby nie-root nie mógł robić ptrace na procesach nie-potomnych. Aby wyłączyć to zabezpieczenie odpalamy 
```
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```
co pozwoli na odpalenie reptyra, a po odpięciu screena możemy przywrócić domyślne ustawienie
```
echo 1 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```
Tym sposobem mamy proces przeniesiony do screena. Oczywiście łatwiej jest proces normalnie uruchomić w screenie, ale czasem nie bardzo możemy ubić proces i odpalić go na nowo, wtedy trzeba się trochę bardziej narobić...

Miłego screenowania!

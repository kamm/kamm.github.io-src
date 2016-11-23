+++
type = "post"
draft = false
date = "2016-11-23T22:35:52+01:00"
title = "Deweloperski Datapower"

+++

Datapower to XML Appliance. Zresztą mniejsza z tym co to jest, bo sam pewnie nie ogarniam wszystkich jego zastosowań i możliwości. Z mojej strony jest wykorzystywany jako środowisko do tworzenia wydajnych usług w oparciu o XSLT. Wszystko fajnie, ale pozostaje kwestia uruchomienia. Na maszynie fizycznej? Marnie - tam jest m. in. produkcja i nikt mi nie pozwoli robić tam developmentu. Na maszynie wirtualnej - spoko, mamy jedną sztukę licencji na wirtualkę. Ale - już parę razy miałem problem z innymi osobami. A to ktoś zmienił mi w czasie testów datasource'a, a to ktoś wywalił port http.

Ze względu na licencję nie mogłem sobie postawić lokalnego DP na lapku. A nawet gdybym postawił - to maszyna wirtualna startowała kilka minut, zajmowała sporo ramu i nie można na niej było zrobić i tak nic innego.

I tak się męczyłem aż poszedłem na szkolenie :) Szkolenie było nt. devops - co to jest, z czym to się je, jakie są używane narzędzia itp. Jednym z pokazanych narzędzi był docker. Nie rozumiałem jak on działał i generalnie nie miałem do niego przekonania - jestem zawsze sceptycznie nastawiony do "nowinek". W pewnej chwili kumpel wpisał sobie w konsoli
```
$ docker search datapower
```

i pojawiła się lista.
```
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
ibmcom/datapower                  IBM DataPower Gateway                           36
ramakrishnasanku/datapower_demo                                                   1
garytu/datapower                  Enable WebGUI for ibmcom/datapower              0
patrocinio/datapower                                                              0

```

Nie do końca rozumiałem co to ma być. Po chwili zostałem wyrwany ze szkolenia i musiałem coś z kimś pogadać. Trafiłem do nowych architektów i tam dziewczyna mi pokazuje odpalonego u siebie datapowera. Z dockera. Zebrałem szczękę z podłogi, wróciłęm na szkolenie, tym razem dając dockerowi szansę. Wszystko pięknie - jest ostępny na dockerze obraz zawierający datapowera. Najnowszą wersję - nowszą niż aktualnie mamy na produkcji. 

Pierwszy raz od lat przeczytałem licencję - obraz jest typowo do zastosowań deweloperskich i testów jednostkowych - czyli wszystko co mi jest potrzebne. 

Oczywiście, jako że pracuję na window$ie to docker mi nie zadziała bezpośrednio - muszę mieć wirtualkę z linuksem - jednak w takim rozwiązaniu ejst to znacznie lżejsze niż wirtualny DP. Nie jest to już wirtualka tylko na DP, ale też na wszystko co wymaga linuksa, a nie wystarcza tu cygwin.

Także jest to dosyć prosta i szybka opcja na postawienie sobie deweloperskiego datapowera na którym można sobie psuć do woli. Na dodatek nie mogę popsuć za dużo - w końcu gdy coś za bardzo napsuję to po prostu stawiam obraz od nowa. Z wirtualnymi jest trochę więcej zabawy i z reguły wpływa to na innych dewów, a wręcz dotyka admina środowiska dev. Na fizycznych to boję się myśleć - podobno był już przypadek, że jeden gość tak napsuł, że trzeba było wzywać serwis z IBMa. Zatem bezpieczniej mieć swoje środowisko u siebie :)



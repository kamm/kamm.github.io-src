+++
title = "Programowanie kart inteligentnych - część 1 - pierwszy program"
type = "post"
draft = false
date = "2018-05-23T10:52:15+02:00"
series = ["JavaCard"]

+++

Czas zacząć programować. Karty, które mnie intereują i które zamówiłem programuje się w Javie. Są też inne, chyba dotnetowe, ale się nie znam. Zajmę się tylko kartami javoskimi, a dokładniej będę uzywał Java Card Kit w wersji 2.2.2. Do pobrania ze strony Oracla: [JavaCard SDK](http://www.oracle.com/technetwork/java/embedded/javacard/downloads/javacard-sdk-2043229.html).

Programy wgrane na kartę są apletami, ale nie ma to nic (poza nazwą) związku z java.applet.Applet. Tu mamy javacard.framework.Applet. Generalnie - javacard jest mocno ograniczoną javą. Nie ma longa, nie ma double'a, nie ma stringa. Łącznie javadoc do wersji 2.2.2 podaje, że istnieją 93 klasy, a w zasadzie to 60 klas i 33 interfejsy zdefiniowane w ramach biblioteki standardowej. Trochę mało. Dodatkowo ogranicza nas mała pamięć karty i wymagania aby działało to możliwie najszybciej. W efekcie programy wyglądają jak kod pisany dla mikrokontrolerów 8 bitowych.

Do napisania i skompilowania kodu apletu będzie potrzeba:

* Java Card Kit, wersja 2.2.2
* Java SDK (wersja praktycznie dowolna, dokładniej >= 1.3)
* Apache Ant - co prawda mozna by się pobawić z mavenem, ale to sztuka dla sztuki
* gpshell w ścieżce uruchamiania (dla wygody)

Przyjrzyjmy się najprostszemu appletowi

```
package pl.kamm;

import javacard.framework.Applet;
import javacard.framework.APDU;
import javacard.framework.ISO7816;
import javacard.framework.ISOException;
import javacard.framework.Util;


public class HelloWorldApplet extends Applet {
    
    //Definicja klasy instrukcji
    protected static final byte CLA = (byte) 0xb0;

    //Definiacja identyfikatora instrukcji
    protected static final byte INS_HELLO = (byte) 0x01;

    //Obsługa instrukcji HELLO
    private void processHello(APDU apdu){
        byte buffer[] = apdu.getBuffer();

        //Sprawdzenie czy argumenty P1 i P2 są równe 0x00
        if (Util.getShort(buffer, ISO7816.OFFSET_P1) != (short) 0x0000){ 
            ISOException.throwIt(ISO7816.SW_INCORRECT_P1P2);
        }

        // Zbudowanie tablicy bajtów "Hello, world!"
        byte [] b = {0x48, 0x65, 0x6c, 0x6c, 0x6f, 0x2c, 0x20, 0x77, 0x6f, 0x72, 0x6c, 0x64, 0x21};

        //Skopiowanie do bufora
        Util.arrayCopy(b,(byte)0,buffer,(byte)0,(byte)b.length); 

        //Wysłanie bufora
        apdu.setOutgoingAndSend((short) 0, (short) b.length); 
    }

    public void process(APDU apdu) {
        //W przypadku operacji wybierania apletu nie rób nic
        if (selectingApplet()){
            return;
        }

        //Pobranie bufora danych
        byte buffer[] = apdu.getBuffer();

        //Obsługujemy tylko jedną, określoną klasę instrukcji
        if (buffer[ISO7816.OFFSET_CLA] != (CLA)){
            ISOException.throwIt(ISO7816.SW_CLA_NOT_SUPPORTED);
        }

        //Na podstawie identyfikatora instrukcji wywołujemy obsługującą ją funkcję
        switch (buffer[ISO7816.OFFSET_INS]){ 
            case INS_HELLO:
                processHello(apdu);
                break;
            default:
                ISOException.throwIt(ISO7816.SW_INS_NOT_SUPPORTED);
                break;
        }
    }

    private HelloWorldApplet(byte bArray[], short bOffset, byte bLength) {
    }

    public static void install(byte bArray[], short bOffset, byte bLength) {
        HelloWorldApplet ai = new HelloWorldApplet(bArray, bOffset, bLength);

        if (bArray[bOffset] == 0) {
            ai.register();
        } else {
            ai.register(bArray, (short) (bOffset + 1), bArray[bOffset]);
        }
    }
}

```

Najważniejszą funkcją appletu jest metoda process. Zajmuje się ona obsługą komendy APDU wysłanej z terminala. W tym wypadku jest obsługiwana jedna komenda APDU - o klasie 0xb0 i identyfikatorze instrukcji 0x01. Ta instrukcja wymaga jako parametrów P1 i P2 wartości 0x00 (są one wartościami jednobajtowymi, ale sprawdzenie odbywa się poprzez sprawdzenie wartości dwubajtowej, zawierającej zarówno P1 jak i P2). Nie potrzebuje ona dodatkowych danych, ale nie jest to sprawdzane - są one ignorowane. Po wywołaniu prawidłowym zachowanie jest zwrócenie tablicy bajtów, które w reprezentacji ASCII dają "Hello, world!".

Teraz trzeba to skompilować. Kompilacja jest całkiem normalna, trzeba tylko pamiętać o dodaniu jarów z JavaCard Kit (javacardframework.jar, api.jar).

```
  <path id="classpath.jcapi">
    <pathelement location="${env.JC_HOME}/api_export_files" />
    <pathelement location="${env.JC_HOME}/lib/javacardframework.jar" />
    <pathelement location="${env.JC_HOME}/lib/api.jar" />
  </path>
....
    <javac srcdir="${src.dir}"
      destdir="${build.dir}/bin"
      debug="true"
      optimize="true"
      includeantruntime="false"
      target="1.1"
      source="1.3">
      <classpath>
        <pathelement path="${build.dir}/bin"/>
      </classpath>
      <classpath refid="classpath.jcapi" />
    </javac>
```

Następnie trzeba przekonwertować pliki class do archiwum cap. cap jest zipem, podobnie do jara i ma podobną strukturę, ale przechowuje pliki przetworzone (nota bene też z rozszerzeniem cap, które już zipami nie są). Są to przetworzone pliki class. Taki plik juz można wgrać na kartę przy uzyciu np gpshell.

```
  <path id="classpath.jctools">
    <pathelement location="${env.JC_HOME}/lib/converter.jar" />
    <pathelement location="${env.JC_HOME}/lib/offcardverifier.jar" />
    <pathelement location="${env.JC_HOME}/lib/scriptgen.jar" />
    <pathelement location="${env.JC_HOME}/lib/apdutool.jar" />
    <pathelement location="${env.JC_HOME}/lib/apduio.jar" />
    <pathelement location="${env.JC_HOME}/lib/tools.jar" /> <!-- 3.0 -->
  </path>
....
    <java classname="com.sun.javacard.converter.Converter" fork="true" failonerror="true">
      <classpath refid="classpath.jcapi" />
      <classpath refid="classpath.jctools" />
      <arg value="-verbose" />
      <arg value="-classdir" />
      <arg value="${build.dir}/bin/" />
      <arg value="-out" />
      <arg value="JCA" />
      <arg value="CAP" />
      <arg value="EXP" />
      <arg value="-applet" />
      <arg value="${javacard.applet.aid}" />
      <arg value="${javacard.applet.name}" />
      <arg value="${javacard.package.name}" />
      <arg value="${javacard.package.aid}" />
      <arg value="${javacard.major.version}.${javacard.minor.version}" />
    </java>
```

Aby ułatwić sobie robotę mam utworzony skrypt w ancie, który odwala kompilację, konwersję, wygenerowanie skryptów wgrywających czy też samo wgranie na kartę lub do emulatora (o emulatorze kiedy indziej).

Aby wrzucić plik cap na kartę należy wydać kilka pojeceń. Jak kilka to najlepiej opakować to w skrypt, np taki

```
# gpshell script for jcop 2.4.1 applet load (default keys)
mode_211
enable_trace
establish_context
card_connect
select -AID A0000000030000
open_sc -security 1 -mac_key 404142434445464748494a4b4c4d4e4f -enc_key 404142434445464748494a4b4c4d4e4f
delete -AID bbbbccddee0101
delete -AID bbbbccddee01
install -file jcapp_1.cap -instParam 31323334 -sdAID a000000003000000 -priv 2
card_disconnect
card_connect
select -AID bbbbccddee0101
card_disconnect
release_context 
```

Kilka uwag - klucz do makowania i szysforwania (404142434445464748494a4b4c4d4e4f) został dla karty ustalony podczas inicjalizacji. Najpierw wybieramy aplet JCOS (JavaCardOparatingSystem), czyli A0000000030000. Kasujemy stary applet (bbbbccddee01) i jego instancję (bbbbccddee0101). Następnie instalujemy plik jcapp_1.cap. Po rozłączeniu i ponownym połączeniu wybieramy ten aplet aby go uruchomić.

No dobra, ale czy to działa? Aby to sprawdzić potrzebujemy kolejnego skryptu gpsh. 

```
mode_211
enable_trace
establish_context
card_connect

#Wybranie apletu
#send_apdu -sc 0 -APDU 00A4040007bbbbccddee010100
select -AID bbbbccddee0101

#Wysłanie komendy CLA=B0, INS=01, P1=00, P2=00, brak danych, spodziewamy się 13 bajtów
send_apdu -sc 0 -APDU B00100000D

card_disconnect
release_context
```

W tym skrypcie wybieramy aplet (zakomentowany jest drugi sposób) a następnie wywołujemy instrukcję CLA=B0, INS=01.

```
mode_211
enable_trace
establish_context
card_connect
select -AID bbbbccddee0101
Command --> 00A4040007BBBBCCDDEE0101
Wrapped command --> 00A4040007BBBBCCDDEE0101
Response <-- 9000
send_apdu -sc 0 -APDU B00100000D
Command --> B00100000D
Wrapped command --> B00100000D
Response <-- 48656C6C6F2C20776F726C64219000
send_APDU() returns 0x80209000 (9000: Success. No error.)
card_disconnect
release_context
```

Domyślnie gpshell ma włączone echo więc wszystkie polecenia są widoczne. Najważniejsza linia to 

```
Response <-- 48656C6C6F2C20776F726C64219000
```

Jest to zwrotna wartość APDU. Ta akurat ma 15 bajtów. Pierwsze 13 to "Hello, world!" z karty. Następnie są dwa bajty kodu błędu. są to 90 00, co w przypadku kart oznacz brak błędu. Fajnie, działa.

W następnych częściach napiszę mały program kliencki w javie oraz omówię APDU.  

Cały projekt dostępny na github http://github.com/kamm/jcapp_1


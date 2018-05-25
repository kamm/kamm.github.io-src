+++
title = "Programowanie kart inteligentnych - część 2 - komunikacja"
type = "post"
draft = false
date = "2018-05-25 08:54:15+02:00"
series = ["JavaCard"]

+++

W poprzednim odcinku utworzyłem bardzo prosty aplet na karcie i wywołałem go z poziomu gpshell. Jednak to jest dobre do testów. W końcu karta ma być wykorzystywana w kryptografii w konkretnej aplikacji.

Stworzyłem bardzo prostego klienta do apletu - [jcapp1client](https://github.com/kamm/jcapp1client). Aplikacja łączy się z pierwszy z brzegu czytnikiem, wybiera kartę do niego włożoną, wybiera aplet i wywołuje instrukcję B0 01. Nawet bez żadnego sprawdzania błędów, kodów wyjścia, nic. Prościej się chyba nieda.

```
public static void main( String[] args )
    {
        try {
            byte[] appletId = {(byte) 0xbb, (byte) 0xbb, (byte) 0xcc, (byte) 0xdd, (byte) 0xee, (byte) 0x01, (byte) 0x01};
            TerminalFactory factory = TerminalFactory.getDefault();
            CardTerminal terminal = factory.terminals().list().get(0);
            
            Card card = terminal.connect("*");
            CardChannel channel = card.getBasicChannel();

            CommandAPDU apdu = null;
            ResponseAPDU rapdu = null;
            apdu = new CommandAPDU(0x00, 0xA4, 0x04, 0x00, appletId);
            rapdu = channel.transmit(apdu);
            apdu = new CommandAPDU(0xB0, 0x01, 0x00, 0x00, 13);
            rapdu = channel.transmit(apdu);

            System.out.println(new String(rapdu.getData()));

            card.disconnect(false);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

Najpierw to uruchomiłem pod windowsem. Działa od ręki, żadnych zgrzytów. No może poza sytuacją gdy miałem podłączony drugi czytnik (jeden jest wbudowany w laptopa, drugi podłączyłem po USB). Wtedy lista zwracana przez factory.terminals().list() jest w kolejności raczej losowej.

Później spróbowałem to uruchomić pod linuksem. Ważne - czytnik mi działa pod linuksem, go potwierdziłem używaniem gpshell czy pcsc_scan. Jednak po uruchomieniu dostałem

```
$ java -cp target/jcappclient-1.0-SNAPSHOT.jar pl.kamm.jcapp1client.App                                                                                                                                                           ~/jcapp1client
java.lang.IndexOutOfBoundsException: Index: 0
        at java.util.Collections$EmptyList.get(Collections.java:4454)
        at pl.kamm.jcapp1client.App.main(App.java:18)
```

Szukałem, szukałem i znalazłem. Trzeba dodać namiary na bibliotekę libpcsclite

```
$ java -Dsun.security.smartcardio.library=/usr/lib/x86_64-linux-gnu/libpcsclite.so.1 -cp target/jcappclient-1.0-SNAPSHOT.jar pl.kamm.jcapp1client.App                                                                             ~/jcapp1client
Hello, world!
```

i zaczyna działać. Tyle na dzisiaj. Krótko.
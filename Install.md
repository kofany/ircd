# Instalacja IRCd.

## 1.1 Skrypt konfiguracyjny

Ten pakiet używa skryptu konfiguracyjnego GNU do swojej konfiguracji. Wystarczy rozpakować dystrybucję i uruchomić skrypt „configure”. Skrypt ten wywoła konfigurację, która zbada Twój system w poszukiwaniu ewentualnych specyficznych ustawień i skonfiguruje plik Makefile oraz plik z domyślnymi definicjami #define ($arch/setup.h).

Istnieje kilka opcji do „configure”, które mogą pomóc lub zmienić domyślne zachowanie:

- `--prefix=DIR` zmienia domyślny katalog, do którego ircd zostanie zainstalowany przy użyciu „make install”. Domyślnie jest to /usr/local
- `--sbindir=DIR` zmienia domyślny katalog, w którym znajdą się pliki wykonywalne systemowego administratora. Ważne jest, aby to odpowiednio ustawić. (domyślnie jest to prefix/sbin)
- `--logdir=DIR` zmienia domyślny katalog, w którym będą przechowywane pliki logów IRC. (domyślnie jest to prefix/var/log/ircd)
- `--sysconfdir=DIR` zmienia domyślny katalog, w którym będą przechowywane pliki konfiguracyjne serwera IRC. (domyślnie jest to prefix/etc)
- `--localstatedir=DIR` zmienia domyślny katalog, w którym będą przechowywane pliki stanu serwera IRC. (domyślnie jest to prefix/var/run)
- `--resconf=FILE` definiuje plik używany przez ircd do inicjalizacji swojego resolvera. (domyślnie jest to /etc/resolv.conf)
- `--zlib-include=DIR` określa katalog, w którym znajduje się plik nagłówkowy zlib.
- `--zlib-library=DIR` określa katalog, w którym znajduje się biblioteka zlib.
- `--zlib-prefix=DIR` określa prefiks lokalizacji zlib. Nadpisuje dwie poprzednie opcje. (Katalog nagłówkowy powinien znajdować się w prefix/include, a biblioteka w prefix/lib).
- `--with-zlib` to domyślna opcja. „configure” szuka na Twoim systemie biblioteki zlib. Jeśli zostanie znaleziona, ircd będzie linkowany z jej użyciem. To nie oznacza, że możesz używać kompresji linków serwera, do tego potrzebujesz również zdefiniować ZIP_LINKS (patrz sekcja poniżej).
- `--without-zlib` informuje „configure”, aby nie szukał biblioteki zlib. Zdefiniowanie tej opcji uniemożliwi używanie kompresji linków serwera.
- `--enable-ip6` Włącza obsługę IPv6 (patrz uwagi poniżej)
- `--enable-dsm` Włącza obsługę Dynamicznych Modułów Dzielonych dla iauth

## 1.2 Uwagi dla użytkowników Cygwin32

Demony z wersji 2.11.0 kompilują się poprawnie na systemach W32, które mają środowisko GNU-Win32 (http://www.cygnus.com/misc/gnu-win32/) skonfigurowane. W momencie wydania testy zostały przeprowadzone przy użyciu wersji b20.1 biblioteki Cygwin32.

Podczas kompilacji na takim systemie upewnij się, że dokładnie zastosowałeś się do notatek instalacyjnych Cygwin32. W szczególności upewnij się, że istnieją następujące pliki: /bin/cp.exe, /bin/mv.exe, /bin/rm.exe i /bin/sh.exe.

Ponadto, serwer IRC potrzebuje pliku resolv.conf do inicjalizacji resolvera. Ten plik może być umieszczony w dowolnym miejscu (patrz opcje configure) i zazwyczaj znajduje się w /etc na systemach UNIX.

Wreszcie, iauth jest automatycznie wyłączony. Nawet jeśli program iauth kompiluje się poprawnie, konieczne jest dodatkowe działanie, aby uzyskać działającą linię komunikacyjną między serwerem IRC a programem iauth.

## 1.3 Uwagi dotyczące obsługi IPv6

Jedyną częścią oprogramowania, która nie używa IPv6, jest wewnętrzny resolver serwera. Polega on na serwerach nazw określonych w „/etc/resolv.conf” jako adresy IPv4.

Ta wersja została przetestowana na następujących systemach IPv6: BSD/OS+KAME, Digital Unix, FreeBSD+KAME, Linux, NetBSD+INRIA.

Ze względu na to, że numeryczne adresy IPv6 zawierają znaki „:”, domyślny separator w pliku konfiguracyjnym serwera został zmieniony na „%”.

# 2. Plik config.h

Kolejnym krokiem jest zdefiniowanie opcji przed kompilacją. Robi się to poprzez edycję pliku „config.h” i zmianę różnych #DEFINE's.

## 2.1 DEBUGMODE

To powinno być zdefiniowane tylko do celów testowych i nigdy nie powinno być używane na serwerze produkcyjnym.

Zdefiniuj DEBUGMODE, jeśli chcesz zobaczyć informacje do debugowania ircd podczas działania demona. Zazwyczaj ta funkcja jest niezdefiniowana, ponieważ ircd generuje dużą ilość danych wyjściowych. DEBUGMODE musi być zdefiniowane, aby działały opcje wiersza polecenia -t lub -x. Zdefiniowanie tego powoduje duże obciążenie dla serwera, ponieważ wykonuje on dużą ilość autodiagnostyki podczas działania.

## 2.2 CACHED_MOTD

Serwer wysyła „motd” do każdego łączącego się klienta. Za każdym razem odczytuje go z dysku. To jest dość intensywne i może być niepożądane dla ruchliwych serwerów.

Zdefiniowanie CACHED_MOTD spowoduje, że serwer przechowuje „motd” w pamięci i odczytuje go z dysku tylko wtedy, gdy plik ulegnie zmianie podczas ponownego hashowania.

## 2.3 CHROOTDIR

Aby korzystać z funkcji CHROOTDIR, upewnij się, że jest ona zdefiniowana (#define'd) i że serwer działa jako root. Lepiej używać innych (zewnętrznych) sposobów konfiguracji środowiska chroot dla ircd i uruchamiać go stamtąd, nie wymagając uprawnień roota.

## 2.4 ENABLE_SUMMON, ENABLE_USERS

Dla świadomych bezpieczeństwa administratorów serwera, mogą chcieć pozostawić ENABLE_USERS niezdefiniowane, wyłączając polecenie USERS, które można użyć do pozyskania informacji tak samo jak finger. ENABLE_SUMMON przełącza, czy serwer będzie próbował przywoływać lokalnych użytkowników na irc, wysyłając wiadomość podobną do tej z talk(1) na terminal użytkownika.

## 2.5 SHOW_INVISIBLE_LUSERS, NO_DEFAULT_INVISIBLE

Na dużych sieciach IRC liczba użytkowników niewidocznych jest prawdopodobnie duża i jej raportowanie nie powoduje problemów. Aby to ułatwić i wpłynąć na to, SHOW_INVISIBLE_LUSERS służy do raportowania liczby niewidocznych użytkowników w poleceniu LUSERS dla wszystkich osób, a nie tylko operatorów. NO_DEFAULT_INVISIBLE define jest używany do przełączania automatycznego ukrywania klientów, gdy się rejestrują.

## 2.6 OPER_KILL, OPER_REHASH, OPER_RESTART, OPER_SET, LOCAL_KILL_ONLY

Trzy polecenia dostępne tylko dla operatorów, KILL, REHASH, RESTART i SET, mogą być wyłączone, aby zapobiec przypadkowym niepożądanym działaniom operatora. Aby dalej ograniczyć działania gości operatorów, można zdefiniować LOCAL_KILL_ONLY, aby umożliwić tylko KILLowanie lokalnie podłączonych klientów.

## 2.7 ZIP_LINKS, ZIP_LEVEL

Od wersji 2.9.3 serwera, połączenia między serwerami mogą być kompresowane za pomocą biblioteki zlib. Aby skompilować serwer z tą funkcją, MUSISZ mieć już skompilowany pakiet zlib (wersja 1.0 lub nowsza) i zdefiniować ZIP_LINKS w pliku config.h. Użycie kompresji dla połączeń między serwerami jest konfigurowane osobno w pliku ircd.conf dla każdego połączenia między serwerami. ZIP_LEVEL pozwala kontrolować poziom kompresji, który zostanie użyty. Wartości powyżej 5 zauważalnie zwiększą zużycie CPU przez serwer.

Pakiet zlib można znaleźć na stronie http://www.cdrom.com/pub/infozip/zlib/. Format danych używany przez bibliotekę zlib jest opisany w RFC (Request for Comments) od 1950 do 1952 w plikach ftp://ds.internic.net/rfc/rfc1950.txt (format zlib), rfc1951.txt (format deflate) i rfc1952.txt (format gzip). Te dokumenty są również dostępne w innych formatach na stronie ftp://ftp.uu.net/graphics/png/documents/zlib/zdoc-index.html

## 2.8 SLOW_ACCEPT

Ta opcja jest domyślnie niezdefiniowana, ale jest potrzebna na niektórych systemach operacyjnych. Tworzy ona sztuczne opóźnienie w przetwarzaniu przychodzących połączeń. Na danym porcie nie będzie przetwarzane więcej niż 1 połączenie co 2 sekundy.

Ponieważ jest niezdefiniowana, pozwala serwerowi przetwarzać połączenia tak szybko, jak tylko może, co może powodować problemy na niektórych systemach operacyjnych (takich jak SunOS) i może być nadużywane (szybkie masowe dołączanie clonebotów...), z tych powodów, jeśli zdecydujesz się pozostawić SLOW_ACCEPT niezdefiniowane, MUSISZ zdefiniować CLONE_CHECK.

## 2.9 CLONE_CHECK

Ta opcja jest domyślnie zdefiniowana i działa jako opakowanie, sprawdzając przychodzące połączenia wcześnie przed rozpoczęciem zapytania ident. Domyślnie serwer nie będzie akceptować więcej niż 10 połączeń z tego samego hosta w ciągu 2 sekund.

## 2.10 Inne #define's

Reszta zmienialnych przez użytkownika #define's powinna być raczej samoopisująca się w pliku config.h. *NIE* jest zalecane zmienianie żadnego z undefiniowanych plików po linii zawierającej „STOP STOP”.

# 3. Edycja pliku Makefile i kompilacja

Ten pakiet używa teraz GNU autoconf do sondowania Twojego systemu i generowania odpowiedniego pliku Makefile. Niemniej jednak, możesz go przeczytać, aby sprawdzić wartości wygenerowane przez skrypt configure. W szczególności, wszystkie nazwy plików i ścieżki do plików binarnych, plików dziennika, plików konfiguracyjnych itp. są tam zdefiniowane. Zaleca się korzystanie z opcji opisanych w sekcji „skrypt configure” zamiast edytować wygenerowany plik Makefile. Niemniej jednak te opcje nie zapewniają pełnej kontroli nad tymi wartościami, w takim przypadku musisz edytować plik Makefile bezpośrednio.

Aby zbudować pakiet, wpisz „make all”. Jeśli wszystko pójdzie dobrze, możesz go zainstalować, wpisując „make install”.

Jeśli masz problemy z kompilacją ircd, skopiuj Makefile.in do Makefile i edytuj plik Makefile odpowiednio.

# 4. Plik ircd.conf

Po zainstalowaniu programów ircd i irc, edytuj plik ircd.conf zgodnie z instrukcjami w tej sekcji i zainstaluj go w lokalizacji określonej w pliku config.h. W katalogu doc/ znajduje się przykładowy plik konfiguracyjny o nazwie example.conf.

Dodatek A (patrz INSTALL.appendix) opisuje różnice między adresami IP a nazwami hostów. Jeśli nie jesteś z tym zaznajomiony, prawdopodobnie powinieneś go przeglądnąć, zanim przejdziesz dalej.

Plik ircd.conf zawiera różne rekordy określające opcje konfiguracyjne. Typy rekordów są następujące:

Informacje o maszynie (M)
Informacje administracyjne (A)
Połączenia portowe (P)
Klasy połączeń (Y)
Połączenia klienta (I,i)
Uprawnienia operatora (O,o)
Wykluczone konta (K,k)
Połączenia serwera (C,c,N)
Odmawianie automatycznych połączeń (D)
Połączenia huba (H)
Połączenia liścia (L)
Ograniczenia wersji (V)
Wykluczone maszyny (Q)
Połączenia usługowe (S)
Serwer odbicia (B)
Z wyjątkiem typów „M” i „A” jest dozwolone mieć wiele rekordów tego samego typu. W niektórych przypadkach można mieć równoczesne rekordy. Ważne jest zaznaczenie, że zostanie użyty ostatni pasujący rekord. Jest to szczególnie przydatne przy konfigurowaniu rekordów I (połączenia klienta).

## 4.1 Informacje o maszynie

### Wprowadzenie
IRC musi poznać kilka rzeczy dotyczących Twojej witryny UNIX, a polecenie „M” określa te informacje dla IRC. Format tego polecenia jest następujący:

### Format
M:<Nazwa Serwera>:<Twój adres IP Internetowy>:<Lokalizacja geograficzna>:<Port>:<SID>
M
Polecenie „M” określa linię opisującą maszynę.

Nazwa Serwera
Nazwa Twojego serwera, dodając ewentualną domenę Internetową, która również może być obecna. Jeśli ta nazwa hosta może być rozwiązana, używany będzie znaleziony adres IP do połączeń wychodzących. W przeciwnym razie używany jest domyślny adres interfejsu hosta. Nazwa serwera nie może być pełną nazwą domenową (FQDN) innego hosta. (Oznacza to, że wszystkie połączenia wychodzące będą wykonywane z tego samego adresu IP, nawet jeśli Twój host ma kilka adresów IP).

Twój adres IP Internetowy
Jeśli maszyna, na której uruchamiasz serwer, ma kilka adresów IP, możesz określić, który adres IP ma być używany do połączeń wychodzących. To przesłoni „Nazwę Serwera”.

Zobacz także sekcje „Połączenia Portowe” i „Połączenia Serwera”.

Lokalizacja Geograficzna
Lokalizacja geograficzna jest używana do określenia, GDZIE ZNAJDUJE SIĘ TWÓJ SERWER, i daje ludziom z innych części świata dobry pomysł na Twoje położenie! Jeśli Twój serwer znajduje się w USA, zazwyczaj najlepiej jest powiedzieć: <MIASTO> <STAN>, USA. Na przykład dla Denver powiedziałbym: „Denver Colorado, USA”. Fińskie witryny (na przykład tolsun.oulu.fi) zazwyczaj podają coś w rodzaju „Oulu, Finlandia”.

Port
Określa port, na którym Twój serwer będzie nasłuchiwać na pakiety UDP od innych serwerów. Powinno to być portem, na którym inne serwery są ustawione do automatycznego łączenia. (Zobacz także opis pola portu w liniach połączeń).

SID
Określa identyfikator unikalny w sieci Twojego serwera. Musi zaczynać się od cyfry. Musi to być ustawione przy współpracy z innymi administratorami serwerów. Na IRCnet musisz skonsultować się z koordynatorem i/lub administratorami swoich rówieśników.

Przykład:
M:tolsun.oulu.fi::Oulu, Finlandia:6667:00PA

Ta linia oznacza: Nazwa mojego hosta to „tolsun.oulu.fi”, moja witryna znajduje się w „Oulu, Finlandia”, a mój SID to „00PA”.

M:orion.cair.du.edu::Denver Colorado, USA:6667:00PS

Ta linia oznacza: Nazwa mojego hosta to „orion.cair.du.edu”, moja witryna znajduje się w „Denver Colorado, USA”, a mój SID to „00PS”.
## 4.2 Informacje administracyjne

### Wprowadzenie
Linia „A” służy do przekazywania informacji administracyjnych o witrynie. Adres e-mail osoby zarządzającej serwerem powinien być zawarty tutaj na wypadek problemów.

### Format
A:<Twoje Imię/Lokalizacja>:<Twój Adres E-Mail>:<inne>::<nazwa sieci>
A
To określa rekord Administratora.

Twoje Imię i Lokalizacja
Użyj tego pola, aby podać swoje PEŁNE IMIĘ i miejsce na świecie, gdzie znajduje się Twoja maszyna. Upewnij się, że dodajesz swoje Miasto, Stan/Prowincję i Kraj.

Twój Adres Poczty Elektronicznej
Użyj tego pola, aby podać swój Adres Poczty Elektronicznej, najlepiej Adres Poczty Internetowej. Jeśli masz adres UUCP lub ARAPnet - dodaj go również. Upewnij się, że dodajesz dodatkowe informacje O Domenie, które są potrzebne, na przykład „mail jtrim@orion” prawdopodobnie nie będzie działać jako adres poczty do mnie, jeśli akurat jesteś w Alasce. Ale „mail jtrim@orion.cair.du.edu” zadziała, ponieważ wiesz, że „orion” jest częścią DOMENY „cair.du.edu”. Więc upewnij się, że dodajesz DOMENY do swoich adresów pocztowych.

Inne
To naprawdę jest pole INNE - możesz dodać, co chcesz.

Nazwa sieci
Użyj tego pola, aby ogłosić nazwę swojej sieci w numerach 005. Użyj tylko jednego słowa!

Przykład
(linia jest tylko jedną linią w pliku konfiguracyjnym, tutaj jest podzielona na dwie linie, aby była czytelniejsza):

A:Jeff Trim - Denver Colorado, USA:INET jtrim@orion.cair.du.edu UUCP {hao,isis}!udenva!jtrim:Terve! Heippa! Have you said hello in Finnish today?;)::IRCnet

Wyglądałoby to tak, gdyby zostało wydrukowane za pomocą polecenia /admin:

Jeff Trim - Denver Colorado, USA INET jtrim@orion.cair.du.edu UUCP {hao,isis}!udenva!jtrim Terve! Hei! Heippa! Have you said hello in Finnish today? ;)

Należy zauważyć, że rekord A nie może być podzielony na wiele linii; zazwyczaj będzie dłuższy niż 80 znaków i dlatego zawija się na ekranie.
## 4.3 Połączenia Portowe

### Wprowadzenie
Linia portowa dodaje serwerowi elastyczność w akceptowaniu połączeń. Poprzez użycie tej linii w pliku ircd.conf, można łatwo skonfigurować, na których portach serwer będzie nasłuchiwał na przychodzące połączenia. Obejmuje to zarówno porty internetowe, które obsługują połączenia przez sieć, jak i porty domeny Unix, które umożliwiają połączenia wewnątrz jednego komputera.

### Format
P:<Adres IP Internetowy>:<*>::<Port>:
P:<Katalog>:<*>:<*>:<Port>:
Porty Internetowe
Adres IP Internetowy
Jeśli komputer, na którym działa serwer, ma wiele adresów IP, możesz określić, który z tych adresów IP zostanie użyty do nasłuchiwania na przychodzące połączenia. Jeśli to pole pozostaje puste, serwer będzie nasłuchiwał na wszystkich dostępnych interfejsach sieciowych. To przydaje się, gdy serwer musi słuchać na wielu adresach IP na jednym komputerze. Patrz także na konfigurację maszyny i sekcje dotyczące połączeń serwera, aby odpowiednio skonfigurować wychodzące połączenia.

Przykład:
P:192.168.1.194:::6664:

Port
To pole określa numer portu, na którym serwer będzie oczekiwał na przychodzące połączenia. Porty to numery, które identyfikują konkretne usługi na komputerze. Na przykład, port 80 jest zwykle używany do obsługi stron internetowych (HTTP).

Porty Gniazda Unix
Katalog
Ścieżka podana w tym polu powinna być nazwą katalogu, w którym zostanie utworzone gniazdo Unix do nasłuchiwania na połączenia. Gniazdo Unix to rodzaj komunikacji międzyprocesowej na jednym komputerze. Serwer spróbuje utworzyć ten katalog przed utworzeniem gniazda Unix.

Port
Pole portu, gdy jest używane w połączeniu z ścieżką w linii P, jest nazwą pliku, który zostanie utworzony w katalogu podanym w pierwszym polu.

Przykład:
P:/tmp/.ircd:::6667:

Ten przykład tworzy gniazdo Unix o nazwie „6667” w katalogu /tmp/.ircd. Gniazdo Unix to plik używany do komunikacji między różnymi procesami na tym samym komputerze.

Uwaga
Aby uruchomić serwer, musisz mieć co najmniej jedną linię P określającą port, na którym serwer ma nasłuchiwać na połączenia. Jeśli jednak uruchamiasz serwer z poziomu usługi inetd (Internet Super Server), nie jest wymagane posiadanie własnej linii P w pliku konfiguracyjnym.

## 4.4 Klasy Połączeń

### Wprowadzenie
Aby umożliwić bardziej efektywne wykorzystanie MAXIMUM_LINKS, wprowadzono klasy połączeń. Wszystkie klienty należą do konkretnej klasy połączeń.

Każda linia dotycząca serwera powinna mieć taki sam numer jak szóste pole. Jeśli to pole jest pominięte, serwer domyślnie ustawia je na 0, korzystając z wartości domyślnych z pliku config.h.

Aby zdefiniować klasę połączeń, musisz zawrzeć linię Y: w pliku ircd.conf. Pozwala to zdefiniować częstotliwość pingowania, częstotliwość prób połączenia (dla serwerów) i maksymalną liczbę połączeń, które ta klasa może mieć.

Obecnie linia Y: MUSI pojawić się w pliku ircd.conf PRZED jej użyciem w jakikolwiek inny sposób.

### Format
Y:<Klasa>:<Częstotliwość Ping>:<Częstotliwość Połączenia>:<Maksymalne Połączenia>:<SendQ>:<Limit Lokalny>:<Limit Globalny>
Y
To określa rekord klasy.

Klasa
To numer klasy, który uzyskuje następujące atrybuty i powinien pasować do tego, który jest na końcu linii C/c/N/I/O/S.

Częstotliwość Ping
To pole określa, jak długo serwer pozwoli połączeniu pozostać "w milczeniu", zanim wyśle komunikat PING, aby upewnić się, że jest nadal aktywne. Chyba że jesteś pewien, co robisz, użyj wartości domyślnej, która znajduje się w pliku config.h.

Częstotliwość Połączenia
Zmieniając tę liczbę, zmieniasz, jak często Twój serwer sprawdza, czy może połączyć się z tym serwerem. Jeśli chcesz sprawdzać bardzo rzadko, użyj dużej wartości, ale jeśli to ważne połączenie, możesz chcieć mniejszej wartości, aby automatycznie się do niego podłączyć tak szybko, jak to możliwe.

Maksymalne Połączenia
To pole określa maksymalną liczbę połączeń, które ta klasa pozwoli z automatycznych połączeń (linie C). Użycie /CONNECT jest nadrzędne wobec tego ustawienia. Określa również maksymalną liczbę użytkowników w tej klasie dla linii I/O na jednej linii I/O.

SendQ
To pole określa wartość "SendQ" dla tej klasy. Jeśli tego pola nie ma, przydzielana jest wartość domyślna (z config.h).

Limit Lokalny
To pole służy do ograniczenia liczby jednoczesnych lokalnych połączeń. Format to <x>.<y>

x: określa maksymalną liczbę klientów z tego samego hosta (IP), którzy będą akceptowani.
y: określa maksymalną liczbę klientów z tego samego użytkownika@hosta (IP), którzy będą akceptowani. Przeczytaj poniższą uwagę.
Domyślnie nieustawiona wartość przyjmuje wartość 1 (jeden).
Limit Globalny
To pole ma takie samo zastosowanie jak pole "Limit Lokalny". Jednak liczba połączeń jest obliczana dla wszystkich klientów obecnych w sieci, a nie tylko dla lokalnych klientów.

Uwaga
Pominięcie dowolnych pól (za wyjątkiem SendQ i limitów) oznacza, że ich wartość wynosi 0 (ZERO)! Domyślna wartość pola SendQ jest dynamicznie ustalana. Limity domyślnie wynoszą 1.1 (jedno połączenie).

Uwaga
Jeśli planujesz korzystać z lokalnego ograniczenia użytkownika@hosta, przeczytaj poniższy tekst bardzo uważnie. Wartość "user" to odpowiedź identyfikatora dla połączenia. Jeśli nie podano odpowiedzi, domyślnie przyjmuje ona "unknown", co oznacza, że efektywne ograniczenie będzie dotyczyć hosta, a nie użytkownika@hosta. Ponadto niektóre serwery identyfikujące zwracają zaszyfrowane dane, które zmieniają się przy każdym połączeniu, co sprawia, że ograniczenie jest nieważne.

Uwaga
Tylko ograniczenie lokalne jest dokładne.

Uwaga
Jeśli zdefiniujesz limit globalny, powinieneś również zdefiniować limit lokalny (taki sam lub niższy), ponieważ nie spowoduje to dodatkowego zużycia CPU i bardziej dokładnie określi limit globalny.

Uwaga
Limity lokalne i globalne dotyczą tylko użytkowników (linie I), nie serwerów ani usług.

### Przykład
Y:23:120:300:5:800000:0:0: (klasa serwera)

To definiuje klasę 23, która pozwala na 5 automatycznych połączeń, które są sprawdzane co 300 sekund. Połączenie może pozostawać w milczeniu przez 120 sekund, zanim zostanie wysłane PING. UWAGA: pola 3 i 4 są w sekundach. SendQ jest ustawione na 800000 bajtów.

Y:1:60:0:50:20000:2:5: (klasa klienta)

W przypadku klasy klienta pola są interpretowane nieco inaczej. Ta klasa (numer 1) może być używana przez maksymalnie 50 użytkowników. Połączenia mogą pozostawać w milczeniu przez 60 sekund, zanim zostanie wysłany PING. SendQ jest ustawione na 20000 bajtów. Nowe połączenie w tej klasie będzie dozwolone tylko wtedy, gdy nie będzie więcej niż 2 inne lokalne połączenia z tego samego adresu IP lub więcej niż 5 innych połączeń w sieci z tej samej nazwy hosta.

Y:2:60:0:50:20000:2.1:5: (klasa klienta)

W przypadku klasy klienta pola są interpretowane nieco inaczej. Ta klasa (numer 1) może być używana przez maksymalnie 50 użytkowników. Połączenia mogą pozostawać w milczeniu przez 60 sekund, zanim zostanie wysłany PING. SendQ jest ustawione na 20000 bajtów. Nowe połączenie w tej klasie będzie dozwolone tylko wtedy, gdy nie będzie więcej niż 2 inne lokalne połączenia z tego samego adresu IP, 1 lokalne połączenie od tego samego użytkownika z tego samego adresu IP lub więcej niż 5 innych połączeń w sieci z tej samej nazwy hosta.

## 4.5 Połączenia klientów

Jak umożliwić klientom podłączanie się do twojego IRCD.

### Wprowadzenie
Klientem jest program, który łączy się z demonem ircd (ircd). Istnieją klienty napisane w C, GNU Emacs Lisp i wielu innych językach. Program "irc" to klient w języku C. Każda osoba, która rozmawia za pośrednictwem IRC, korzysta z własnego klienta.

Plik ircd.conf zawiera wpisy, które określają, które klienty mogą podłączać się do twojego demona IRC. Oczywiście chcesz, aby klienty z twojej własnej maszyny mogły się podłączać. Możesz też chcieć pozwolić klientom z innych witryn na podłączanie się. Te zdalne klienty będą korzystać z twojego serwera jako punktu połączenia. Wszystkie wiadomości wysyłane przez tych klientów będą przechodzić przez twoją maszynę.

### Format
I:<Adres docelowy hosta>:<Hasło>:<Nazwa docelowych hostów>:<Port>:<Klasa>:<Flagi>
Uwaga
Małe "i" jest równe flaga "R" w przypadku "I".

Adres docelowy hosta
Określa adres IP maszyny(-) uprawnionej(-ych) do podłączania. Jeśli przed rzeczywistym adresem IP pojawi się prefiks "user@", serwer wymaga, aby nazwa użytkownika zdalnego, zwracana przez serwer ident, była taka sama jak ta, która została podana przed znakiem "@". Dozwolone są symbole wieloznaczne, chyba że używany jest maska bitowa (np. 1.2.3.0/24). Maska bitowa jest zalecana zamiast symboli wieloznacznych, ponieważ jest bardziej dokładna.

Hasło
Hasło, które musi być podane przez klienta, aby zostać dopuszczonym do serwera.

Nazwa docelowych hostów
Określa nazwę hosta(-ów) maszyn, które mają prawo łączyć się z serwerem. Jeśli przed rzeczywistym adresem IP pojawi się prefiks "user@", serwer wymaga, aby nazwa użytkownika zdalnego, zwracana przez serwer ident, była taka sama jak ta, która została podana przed znakiem "@". Dozwolone są symbole wieloznaczne, ale lepiej pozostawić to pole puste i użyć maski bitowej w polu Adres Hosta.

To pole może być puste, ma wówczas specjalne znaczenie. Patrz poniżej.

Użycie tego pola do wymuszenia braku ustawionej nazwy hosta klienta jest przestarzałe, patrz flaga "N".

Port
Określa numer portu, dla którego ta linia konfiguracji jest ważna. Puste pole lub "0" pasuje do wszystkich portów.

Klasa
To pole powinno odnosić się do istniejącej klasy. Klasy połączeń są przydatne do ograniczenia liczby użytkowników dopuszczonych na serwerze.

Flagi
To pole zawiera flagi linii I:; flagi mają rozmiar jednego znaku, mogą być łączone i ich kolejność nie ma znaczenia.

R - ograniczone
D - ograniczone, gdy klient nie ma odwrotnego DNS
I - ograniczone, gdy klient nie ma identyfikatora.
E - klient jest zwolniony z linii K:
N - nie pokazuj rozwiązanej nazwy hosta (zakazy nadal pasują)
F - przejdź do kolejnej linii I:, jeśli hasło nie zostanie dopasowane
Uwaga
Ograniczona linia I: oznacza, że ​​klienci pasujący do takiej linii I nie będą mogli korzystać z uprawnień operatora (brak zmiany nicku/mode, brak wyrzucania). Tacy użytkownicy będą mieli swoją nazwę użytkownika poprzedzoną +, = lub -, w zależności od odpowiedzi identyfikatora.

Uwaga
Serwer najpierw sprawdza, czy nazwa hosta klienta (lub dowolne aliasy) pasuje do pola Nazwa Hosta docelowego. Jeśli zostanie znalezione dopasowanie, klient zostanie zaakceptowany. Jeśli nie, serwer sprawdza, czy adres IP klienta pasuje do pola Adres Hosta docelowego. Pasujące pole jest używane do ustawienia nazwy klienta: na przykład, jeśli klient pasuje do pola Adres Hosta docelowego, zostanie wyświetlony na IRC z adresem numerycznym (nawet jeśli ten adres jest rozpoznawalny). Jeśli pole Nazwa Hosta docelowego jest puste, zawsze używana jest nazwa hosta (jeśli jest dostępna).

### Przykłady
Na przykład, jeśli instalujesz IRC na tolsun.oulu.fi i chcesz pozwolić na przykład na to, żebyśmy przyjęli, że tworzysz ten plik dla tolsun i chcesz, aby Twoi własni klienci mogli łączyć się z Twoim serwerem, możesz dodać ten wpis do pliku:

I:::tolsun.oulu.fi::1

Jeśli chciałbyś pozwolić zdalnym klientom na łączenie się, możesz dodać następujące linie:

I:::*.edu.edu::1

Zezwalaj na łączenie się z dowolnymi klientami z maszyn, których nazwy kończą się na ".edu.edu", bez hasła.

I:128.214.6.100::nic.funet.fi::1

Zezwalaj na łączenie się klientom z maszyny o tym numerze IP. Dopasowanie numeryczne jest wystarczające, nie jest już wymagana nazwa.

I::secret:*.tut.fi::1

Zezwalaj na łączenie się klientom z maszyn pasujących do "*.tut.fi" za pomocą hasła "secret".

I:::*::1

Zezwalaj każdemu z dowolnego miejsca na łączenie się z twoim serwerem.

Jest to najprostszy sposób, ale pozwala to również ludziom na przykład na zrzucanie plików na Twój serwer lub na łączenie się z tysiącem (lub ile połączeń na proces pozwala Twój system operacyjny) klientów na Twoją maszynę i zajmowanie portów sieci. Oczywiście te same rzeczy można zrobić, po prostu telnetując na port SMTP Twojej maszyny (na przykład).

I:::*.fi:6667:1

Zezwalaj na łączenie się klientom z maszyn pasujących do "*.fi" na porcie 6667.

I:135.11.35.0/24::*.net::1

Zezwala klientom z maszyn, których nazwa hosta pasuje do "*.net" lub których adres IP znajduje się w bloku "135.11.35.0/24", na łączenie się z serwerem. Jeśli nazwa hosta nie pasuje do "*.net", to dla tych klientów używany jest adres IP, nawet jeśli nazwa hosta jest znana.

I:135.11.35.0/24::::1

Zezwala klientom z maszyn, których adres IP znajduje się w bloku "135.11.35.0/24", na łączenie się z serwerem. Jeśli nazwa hosta jest znana, jest ona używana jako adres dla tych klientów.

tag/NEW!!!/ Od wersji 2.11.0 serwera wprowadzono flagi linii I:.

## 4.6 Uprawnienia operatora

Jak stać się administratorem IRC na swojej witrynie

### Wprowadzenie
Aby stać się administratorem IRC, IRC musi wiedzieć, kto jest upoważniony do zostania operatorem i jakie są ich "Nick" i "Hasło".

### Format
O:<Nazwa Hosta DOCELOWEGO>:<Hasło>:<Nick>:<Port>:<Klasa>
O
Określa rekord operatora.

Uwaga
Jeśli używasz małej litery ("o"), oznacza to operatora globalnego. Wielka litera ("O") oznacza operatora lokalnego. Operator lokalny ma praktycznie te same uprawnienia co operator globalny, ale z pewnymi ograniczeniami.

Nazwa Hosta DOCELOWEGO
Informuje IRC, z którego hosta masz uprawnienia. Oznacza to, że powinieneś być zalogowany na tym hoście, kiedy ubiegasz się o uprawnienia operatora. Jeśli określisz "tolsun.oulu.fi", to IRC będzie oczekiwać, że twój KLIJENT będzie podłączony do "tolsun.oulu.fi" - kiedy ubiegasz się o UPRAWNIENIA OPERATORA z "tolsun.oulu.fi". Nie możesz być zalogowany na żadnym innym hoście i nie można używać UPRAWNIEN OPERATORA w tolsun, tylko wtedy, gdy jesteś podłączony do TOLSUN - jest to zabezpieczenie przed nieupoważnionymi witrynami.

Hasło
To jest twoje HASŁO AUTORYZACJI - to hasło informuje IRC, kim jesteś! Nigdy nie podawaj nikomu swojego hasła i zawsze zabezpiecz plik "ircd.conf" przed dostępem wszystkich innych użytkowników.

Nick
Twój zazwyczaj używany pseudonim - ale możesz to ustawić na co chcesz.

Port
Nie używany.

Klasa
Pole klasy powinno odnosić się do istniejącej klasy (najlepiej o niższym numerze niż dla odpowiedniej linii I) i określa maksymalną liczbę jednoczesnych użyczeń linii O dozwoloną przez pole max. połączeń w linii Y.

### Przykład
o:orion.cair.du.edu:pyunxc:Jeff::1

Jest OPERATOR na "orion.cair.du.edu", który może uzyskać uprawnienia operatora, jeśli poda hasło "pyunxc" i używa NICKNAME "Jeff".

Uwaga
Nazwa HOSTA akceptuje maski bitowe IP.

## 4.7 Wykluczone konta

Usuń niepożądanego użytkownika z IRC na swojej stronie.

### Wprowadzenie
Oczywiście, miejmy nadzieję, że nie będziesz musiał korzystać z tego polecenia. Niestety czasami użytkownik może stać się trudny do zarządzania, i to jest twoje jedyne rozwiązanie - polecenie KILL USER. TO POLECENIE DOTYCZY TYLKO TWOJEGO SERWERA - Jeśli ten użytkownik może połączyć się z innym SERWEREM gdziekolwiek indziej w sieci IRC, to musiałbyś skontaktować się z administratorem na tej stronie, aby wyłączyć dostęp z tego SERWERA IRCD.

### Format
K:<Nazwa Hosta>:<okres czasu (y)|komentarz>:<Użytkownik>:<port>:
k:<Nazwa Hosta>:<okres czasu (y)|komentarz>:<Autoryzacja>:<port>:
K
"K" informuje IRCD, że tworzysz wpis polecenia KILL USER.

Nazwa Hosta
W tym polu określasz Nazwę hosta lub adres IP (pojedynczy adres IP, notacja zastępcza lub notacja maski bitowej), z którego użytkownik się łączy. Jeśli chcesz USUNĄĆ połączenia z IRC z "orion.cair.du.edu", to wpisz "orion.cair.du.edu". Jeśli chcesz USUNĄĆ DOSTĘP DO WSZYSTKICH HOSTÓW, możesz użyć "*" (notacja zastępcza) i niezależnie od hosta, z którego UŻYTKOWNIK (określony w polu 4) się łączy, zostanie mu odmówiony dostęp.

Jeśli określisz adres IP, maskę IP lub maskę bitową IP, dopasuje to klientów łączących się z pasujących adresów, niezależnie od tego, czy są rozwiązywane, czy nie.

Możesz przedstawić adres IP, maskę IP lub maskę bitową IP jako "=" w przypadku, gdy dopasowywane są tylko pasujące nie rozwiązywane hosty.

okres czasu (y)|komentarz
Albo pozostaw to pole puste, albo dodaj komentarz, a następnie linia jest aktywna ciągle dla określonego użytkownika/hosta. Możesz także określić interwały, w których linia ma być aktywna, patrz przykłady poniżej.

Użytkownik
NICK użytkownika, którego chcesz usunąć z IRC. Na przykład "root".

Autoryzacja
Jeśli serwer identyfikacyjny użytkownika odpowiada typem INNYM (w przeciwieństwie do typu UNIX), odpowiedź nie jest używana do ustawienia nazwy użytkownika. (małe litery) linie k mogą być używane w tych przypadkach do odrzucenia użytkowników na podstawie odpowiedzi identyfikacyjnej.

To pole będzie porównywane z odpowiedzią serwera identyfikacyjnego. Ważne jest zauważyć, że odpowiedzi INNE są poprzedzone "-" przez ircd, podczas gdy odpowiedzi UNIX nie są.

Port
Port, na którym linia Kill będzie działać. 0 oznacza wszystkie porty.

### Przykłady
K:orion.cair.du.edu::jtrim:0:

Jeśli użytkownik "jtrim" łączy się z IRC z hosta "orion.cair.du.edu", NATYCHMIAST GO USUŃ z mojego IRCD.

k:*.stealth.net::-43589:0:

Jeśli użytkownik łączy się z dowolnego hosta, który ma sufiks "stealth.net" i jeśli identyfikacyjny serwer tego hosta zwraca "-43589" - NATYCHMIAST GO USUŃ z mojego IRCD.

K:*.cair.du.edu::root:0:

Jeśli użytkownik "root" łączy się z IRC z dowolnego hosta, który ma sufiks "cair.du.edu" - NATYCHMIAST GO USUŃ z mojego IRCD.

K:*::vijay:0:

Ta linia oznacza "NIE DBAM O TO, NA JAKIM HOŚCIE jest użytkownik "vijay", NIGDY nie pozwolę użytkownikowi "vijay" na zalogowanie się na mój IRCD."

K:*.oulu.fi:0800-1200,1400-1900:*:0:

To zabrania wszystkim użytkownikom z hostów z końcówką "oulu.fi" dostępu do twojego serwera między 8 a 12 rano oraz między 14 a 19 po południu. Użytkownicy zostaną wylogowani, jeśli są już zalogowani, gdy linia stanie się aktywna (otrzymają ostrzeżenie 5 minut wcześniej). Należy zauważyć, że wymaga to, aby ircd był skompilowany z odpowiednim #define!

K:192.11.35.0/24::*:0:

Ta linia zabrania wszystkim hostom, których adres IP jest z sieci "192.11.35.0/24", logowanie się na ircd.

K:=192.11.35.0/24::*:0:

Ta linia zabrania wszystkim hostom, których adres IP jest z sieci "192.11.35.0/24" i które nie zostały rozwiązane, aby zalogować się na ircd.

## 4.8 Połączenia serwerowe

Jak łączyć się z innymi serwerami, jak inne serwery mogą się z Tobą łączyć

OSTRZEŻENIE: Używane jako przykłady nazwy hostów są tylko przykładami i nie są przeznaczone do użycia (po prostu dlatego, że nie działają) w życiu rzeczywistym.

Teraz musisz zdecydować, Z KTÓRYMI hostami chcesz się połączyć i W JAKIEJ KOLEJNOŚCI chcesz się z nimi połączyć. W moim przykładzie załóżmy, że jestem na maszynie "rieska.oulu.fi" i chcę połączyć się z demonami IRC na 3 innych maszynach:

``garfield.mit.edu'' - Połączenie trzeciorzędne
``irc.nada.kth.se'' - Połączenie wtórne
``nic.funet.fi'' - Połączenie pierwotne
I preferuję połączenie z nimi w tej kolejności, oznacza to, że chcę najpierw spróbować połączyć się z ``nic.funet.fi'', następnie z ``irc.nada.kth.edu'' i na końcu z ``garfield.mit.edu''. Więc jeśli ``nic.funet.fi'' jest wyłączony lub nieosiągalny, program spróbuje połączyć się z ``irc.nada.kth.se''. Jeśli irc.nada.kth.se jest wyłączony, spróbuje połączyć się z garfieldem, i tak dalej.

PROSZĘ OGRANICZ liczbę hostów, do których będziesz próbował się połączyć, do 3. Ma to dwa główne powody:

1. aby oszczędzić swój serwer od dodatkowego obciążenia i opóźnień dla użytkowników
2. aby zaoszczędzić ruch sieciowy w internecie (pamiętaj o starym programie rwho, który miał problemy z ruchem sieciowym, gdy liczba maszyn wzrosła).

### Format
C:<Adres Hosta DOCelowego>:<Hasło>:<Nazwa Hosta DOCelowego>:<Port DOCELU>:<Klasa>:<Źródło IP>
na przykład:

C:nic.funet.fi:hasło:nic.funet.fi:6667:1

- lub -

C:128.214.6.100:hasło:nic.funet.fi:6667:1

- lub -

C:root@nic.funet.fi:hasło:nic.funet.fi:6667:1

C
To pole informuje program IRC, którą opcję konfigurujesz. "C" odpowiada opcji połączenia serwera.

Adres Hosta DOCelowego
Określa nazwę hosta lub adres IP maszyny, do której ma się połączyć. Jeśli ``user@'' jest przed rzeczywistą nazwą hosta lub adresem IP, serwer będzie wymagać, aby zdalna nazwa użytkownika zwracana przez serwer identyfikacyjny była taka sama, jak ta podana przed "@"

Hasło
Hasło do innego hosta. Hasło musi zawsze być obecne, aby linia została rozpoznana.

Nazwa Hosta DOCelowego
Pełna nazwa hosta maszyny docelowej. To jest nazwa, którą SERWER DOCELU zidentyfikuje się, gdy się z nim połączysz. Jeśli łączysz się z nic.funet.fi, otrzymasz "nic.funet.fi", i to właśnie powinieneś umieścić w tym polu.

Port DOCELU
Port INTERNETOWY, do którego chcesz się połączyć na maszynie DOCELU. Większość czasu zostanie ustawione na "6667". Jeśli to pole jest pozostawione puste, to nie będą podejmowane próby połączenia z hostem DOCELU, a Twój host będzie akceptował połączenia OD hosta DOCELU. Pole portu może zawierać 2 porty, oddzielone kropką. W tym przypadku pierwszy port jest używany podczas automatycznego łączenia, a drugi port jest używany do pingów UDP do serwera docelowego.

Klasa
Pole klasy powinno odnosić się do istniejącej klasy i określa maksymalną liczbę jednoczesnych użyć linii C dozwolonych za pośrednictwem pola max. links w linii Y.

Źródło IP
To pole określa źródłowy adres IP, który ma być używany do łączenia się z tym serwerem.

NOWOŚĆ!!!
Od wersji serwera 2.9.3 połączenia serwerowe mogą być kompresowane za pomocą biblioteki zlib. Aby zdefiniować połączenie skompresowane, musisz skompilować serwer z zdefiniowanym ZIP_LINKS i używać linii C z małymi literami.

NOWOŚĆ!!!
Od wersji serwera 2.11.0 dodano pole Źródło IP.

PRZYKŁADY:

C:nic.funet.fi::nic.funet.fi:6667:1
To oznacza: Połącz się z hostem "nic.funet.fi", bez hasła, i oczekuj, że ten serwer zidentyfikuje się jako "nic.funet.fi". Twój serwer połączy się z tym hostem na porcie 6667.

C:18.72.0.252:Jeff:garfield.mit.edu:6667:1:192.168.0.18
To oznacza: Połącz się z hostem o adresie "18.72.0.252", używając hasła "Jeff". Serwer DOCELOWY powinien zidentyfikować się jako "garfield.mit.edu". Połączysz się z portem internetowym 6667 na tym hoście. To połączenie będzie używać źródłowego adresu IP "192.168.0.18".

C:irc.nada.kth.se::irc.nada.kth.se:1
To oznacza: nie próbuj automatycznie łączyć się z "irc.nada.kth.se", ale jeśli "irc.nada.kth.se" zażąda połączenia, zezwól mu się połączyć.

Teraz wracając do naszego pierwotnego problemu, chcieliśmy, żeby NASZ serwer POŁĄCZYŁ się z 3 hostami: "nic.funet.fi", "irc.nada.kth.se" i "garfield.mit.edu" w tej kolejności. Dlatego, gdy wprowadzamy te wpisy do pliku, muszą być one zrobione odwrotnie, w kolejności odwrotnej do tego, w jakiej chcielibyśmy się z nimi połączyć.

Oto, jak to wyglądałoby, gdybyśmy najpierw połączyli się z "nic.funet.fi":

C:garfield.mit.edu::garfield.mit.edu:6667:1 C:irc.nada.kth.se::irc.nada.kth.se:6667:1 C:nic.funet.fi::nic.funet.fi:6667:1

Ircd spróbuje najpierw połączyć się z nic.funet.fi, a następnie z irc.nada i na końcu z garfield.

Wpisy wzajemne: Każdy wpis "C" wymaga odpowiadającego mu wpisu "N", który określa uprawnienia do połączeń z innymi hostami. Wpis "N" zawiera hasło, jeśli jest wymagane, aby inne hosty mogły się z Tobą połączyć. Te wpisy mają ten sam format co wpisy "C".

FORMAT
Format wpisu "NOCONNECT" w pliku "ircd.conf" wygląda następująco:

N:<Adres Hosta DOCELOWEGO>:<Hasło>:<Nazwa Hosta DOCELOWEGO>:<Maska Domeny>:<Klasa>
Załóżmy, że "garfield.mit.edu" łączy się z Twoim serwerem, a Ty chcesz umieścić uwierzytelnienie hasłem na "garfield". Wpis "N" wyglądałby tak:

N:garfield.mit.edu:golden:garfield.mit.edu::

Ten wpis mówi: oczekuj połączenia od hosta "garfield.mit.edu", oczekuj hasła logowania "golden", a host powinien zidentyfikować się jako "garfield.mit.edu".

N:18.72.0.252::garfield.mit.edu::

Ten wpis mówi: oczekuj połączenia od hosta "18.72.0.252", nie oczekuj hasła logowania. Łączący host powinien zidentyfikować się jako "garfield.mit.edu".

N
"Pole "N" odpowiada opcji Noconnect serwera.

Adres Hosta DOCELOWEGO
Określa nazwę hosta lub adres IP maszyny, z którą chcesz się połączyć. Jeśli przed rzeczywistą nazwą hosta lub adresem IP pojawi się "user@", serwer będzie wymagać, aby zdalna nazwa użytkownika zwracana przez serwer identyfikacyjny była taka sama jak ta podana przed "@".

Hasło
Hasło innego hosta. Hasło musi zawsze być obecne, aby linia została rozpoznana. Jeśli w pliku config.h zdefiniowano CRYPT_LINK_PASSWORD, to hasło musi być zaszyfrowane.

Nazwa Hosta DOCELOWEGO
Pełna nazwa hosta maszyny docelowej. To jest nazwa, którą serwer DOCELOWY zidentyfikuje się, gdy się z nim połączysz. Jeśli łączysz się z nic.funet.fi, otrzymasz "nic.funet.fi", i to właśnie powinieneś umieścić w tym polu.

Maska Domeny
Maska domeny, patrz niżej.

Klasa
Pole klasy powinno odnosić się do istniejącej klasy.

Maski domen
Aby zmniejszyć liczbę serwerów na IRCnet, wprowadzono maski DOMEN od wersji 2.6. Aby wyjaśnić użycie masek domen, weźmy na przykład taką:

*.de - maska domeny dopasowująca wszystkie maszyny w Niemczech.

Maski domen są przydatne, ponieważ WSZYSTKIE SERWERY w Niemczech (lub w innych obszarach domen) mogą być prezentowane jako jeden dla reszty świata. Wyobraź sobie 100 serwerów w Niemczech, to byłby niesamowity marnotrawstwo przepustowości sieci, aby rozgłaszać je wszystkie do wszystkich serwerów na świecie.

Maski domen są więc ogromną pomocą, ale jak ich używać?

Mogą być zdefiniowane w linii "N" dla danej konfiguracji, a zamiast "Maska Domeny" wpisujesz magiczną liczbę zwana liczbą masek.

Liczba masek mówi, ILE CZĘŚCI nazwy Twojego serwera ma zostać zastąpione maską. Na przykład nazwa Twojego serwera to "tolsun.oulu.fi", a Ty chcesz, żeby była reprezentowana jako "*.oulu.fi" dla "nic.funet.fi". W tym przypadku liczba masek wynosi 1, ponieważ tylko jedno słowo (tolsun) zostanie zastąpione maską.

Jeśli liczba masek wynosiłaby 2, maska domeny brzmiałaby "*.fi". Należy zauważyć, że używając maski domeny "*.fi", NIE MOŻESZ połączyć się z "nic.funet.fi", ponieważ spowodowałoby to kolizję nazw serwerów (*.fi pasuje do nic.funet.fi).

Radzę nie używać serwerów z maską domen, zanim dokładnie nie zrozumiesz, jak się ich używa. Są one głównie korzystne dla "backbonów" krajów i innych obszarów o wspólnych domenach.

## 4.9 Zabraniaj automatycznych połączeń

Wprowadzenie
Wpisy typu D (ang. D lines) zostały wprowadzone, aby umożliwić administratorom serwerów większą kontrolę nad tym, jak są wykonywane automatyczne połączenia. Prawdopodobnie będzie to przydatne tylko dla dużych sieci, które mają skomplikowane konfiguracje.

Format
D:<Zakazana Maska Serwera>:Klasa Zakazana:<Nazwa Serwera>:Klasa Serwera:
Zakazana Maska Serwera
To pole jest porównywane ze wszystkimi serwerami obecnie obecnymi w sieci.

Klasa Zakazana
Jeśli to pole zawiera numer klasy, zostanie dopasowane, jeśli w sieci obecny jest serwer należący do tej klasy. Należy zauważyć, że może to być prawdą dla każdego serwera, nawet tych, które nie są bezpośrednio podłączone.

Nazwa Serwera
To pole jest porównywane z nazwą serwera, do którego serwer chce się automatycznie połączyć.

Klasa Serwera
To pole jest używane do porównywania z klasą, do której należą serwery, dla których ustawione jest automatyczne połączenie.

Przykłady
D:*.edu::*.fi::

Nie łącz się automatycznie z żadnym serwerem ``*.fi'', jeśli którykolwiek serwer obecny w sieci pasuje do ``*.edu''.

D::2:eff.org:3:

Nie łącz się automatycznie z ``eff.org'', ani z żadnym serwerem należącym do klasy ``3'', jeśli serwer zdefiniowany jako należący do klasy ``2'' jest obecnie w sieci.

## 4.10 Połączenia typu Hub

Wprowadzenie
W przeciwieństwie do linii typu L, serwer implementuje także linie typu H, aby określić, które serwery mogą działać jako huby (węzły) i dla jakich serwerów mogą "hubować". Jeśli serwer ma dostarczyć tylko swoją nazwę (to znaczy działać jako samotny liść), to nie jest wymagana żadna linia H, w przeciwnym przypadku należy dodać linię H.

Format
H:<Maska Serwera>:<Maska SID>:<Nazwa Serwera>::
Maska Serwera
Wszystkie serwery, które są dozwolone za pomocą tej linii H, muszą pasować do maski podanej w tym polu.

Maska SID
SID wszystkich serwerów, które są dozwolone za pomocą tej linii H, muszą pasować do maski podanej w tym polu. Puste pole jest równe '*', co oznacza, że ​​dowolny SID może zostać wprowadzony.

Nazwa Serwera
To pole jest używane do dokładnego dopasowania nazwy serwera, przy czym znaki wieloznaczne traktowane są jako znaki literalne.

Przykłady
H:*.edu::*.bu.edu::

Pozwala serwerowi o nazwie ``*.bu.edu'' na wprowadzenie tylko serwerów, które pasują do maski nazw ``*.edu'', niezależnie od SID, który mają.

H:*:616*:eff.org::

Pozwala serwerowi ``eff.org'' na wprowadzanie (i działanie jako hub) dla każdego serwera, którego SID zaczyna się od ``616''.

Uwaga
Możliwe jest posiadanie i używanie wielu linii H (lub L) dla jednego serwera. Na przykład:

H:*.edu:*:*.bu.edu::
H:*.au:*:*.bu.edu::
jest dozwolone, podobnie jak

L:*.edu:*:*.au::
L:*.com:*:*.au::

## 4.11 Połączenia typu Leaf

Wprowadzenie
Aby zapobiec serwerom, które powinny działać tylko jako liście (leaf), przypadkowo stawaniu się węzłami (hub), wprowadzono linię L, dzięki której węzły mogą dowiedzieć się, które serwery powinny i nie powinny być traktowane jako liście. Serwer typu leaf powinien pozostać węzłem (node) przez cały okres swojego życia, podczas gdy jest podłączony do sieci serwerów IRC. Jest jednak dość łatwo źle skonfigurować serwer typu leaf, co może stworzyć problemy, stając się węzłem dwóch lub więcej serwerów i przestając być leafem. Linia L pozwala administratorowi serwera IRC typu "Hub" "zatrzymać" serwer, który ma działać jako leaf i próbuje stać się węzłem. Na przykład serwer typu leaf, który łączy się z innym serwerem, który nie ma dla niego linii L, zostanie rozłączony przez ten serwer, przywracając go jako leafa.

Format
L:<Maska Serwera>:*:<Nazwa Serwera>:<Maksymalna Głębokość>:
Maska Serwera
Maska serwerów, na których mają być używane cechy typu leaf, gdy serwer odbiera komunikaty SERVER. W tej rubryce można używać znaków wieloznacznych * i ?, które pomagają w dopasowaniu. Jeśli to pole jest puste, działa to tak samo, jak gdyby była jednym znakiem * (czyli dopasowanie wszystkiego).

Nazwa Serwera
Nazwa serwera, który jest podłączony do ciebie i dla którego chcesz narzucić cechy typu leaf.

Maksymalna Głębokość
Maksymalna dozwolona głębokość dla tego leafa, a jeśli nie jest określona, przyjmuje się wartość 1. Głębokość jest sprawdzana za każdym razem, gdy serwer odbiera komunikat SERVER. Liczba skoków do serwera jest polem, które jest sprawdzane w kontekście maksymalnej głębokości, a jeśli jest większa, połączenie do serwera, które uczyniło swojego leafa zbyt głęboko, zostaje zerwane. Aby linia L zadziałała, oba pola, 2 i 4, muszą pasować do nowego serwera wprowadzanego i serwera odpowiedzialnego za wprowadzenie tego nowego serwera.

## 4.12 Ograniczenia wersji

Wprowadzenie
Linie V są używane do ograniczania połączeń serwerów do Twojego serwera na podstawie ich wersji i opcji kompilacji.

Format
V:<Maska Wersji>:<Flagi>:<Maska Serwera>::
Maska Wersji
Pasujące ciągi numerów wersji zostaną odrzucone.

Flagi
Jeśli jakiekolwiek flagi określone w tej rubryce zostaną znalezione w ciągu flag partnera, połączenie zostanie odrzucone.

Maska Serwera
To pole jest używane do dopasowywania nazw serwerów. Linia V będzie używana dla serwerów pasujących do maski podanej w tym polu.

Typ Serwera
Zarówno Maska Wersji, jak i Flagi powinny być poprzedzone identyfikacją typu serwera. Ta implementacja używa id "IRC" (zaczynając od wersji 2.10).

Przykłady
V:IRC/021001*::*::

Zakazuje połączenia dowolnemu serwerowi "IRC" o wersji 2.10.1*.

V:IRC/021001*:IRC/D:*::

Zakazuje połączenia dowolnemu serwerowi "IRC" o wersji 2.10.1* lub skompilowanemu z zdefiniowaną opcją DEBUGMODE.

V:*/0209*::::

Zakazuje połączenia dowolnemu serwerowi korzystającemu z protokołu 2.9.

Uwaga
Możliwe jest posiadanie i używanie wielu linii V dla jednej maski serwera.

V:IRC/021001*::*::

V:IRC/021002*::*::

jest dozwolone.

Wersja Protokołu
Tylko pierwsze 4 cyfry numeru wersji są standardowe: definiują one wersję protokołu. Reszta ciągu zależy od implementacji; dopasowania do tej części powinny być używane z konkretną identyfikacją.

Flagi
nie są standardowe. Dlatego to pole powinno zawsze zawierać konkretną identyfikację.

## 4.13 Wykluczone maszyny

Zabranianie SERVEROM na twojej sieci IRC.

Wprowadzenie
W niektórych przypadkach administratorzy sieci napotykają na trudności w zarządzaniu siecią. Z różnych powodów możesz nie chcieć, aby pewien serwer był w twojej sieci (na przykład ze względu na otwarte luki w zabezpieczeniach, które otwiera dla każdego serwera, jeśli nie jest odpowiednio zabezpieczony). W takim przypadku powinieneś użyć linii Q w swoim serwerze. Gdy określisz nazwę serwera w linii Q, za każdym razem, gdy jakiś serwer próbuje wprowadzić na twoim serwerze inny serwer (pamiętaj, że wszystkie nazwy serwerów są rozgłaszane po sieci), ta nazwa jest sprawdzana pod kątem dopasowania do linii Q na twoim serwerze. Jeśli pasuje, twój serwer rozłącza połączenie. Należy zauważyć, że umieszczenie linii Q w twoim serwerze prawdopodobnie sprawi, że twój serwer zostanie sam (nie zostanie wprowadzony nowy serwer), chyba że inne serwery zgodziły się również na umieszczenie tej samej linii Q w swoich plikach konfiguracyjnych ircd.

Przykład
Q::z powodu luk w zabezpieczeniach:foo.bar.baz::

To polecenie wyklucza serwer o nazwie "foo.bar.baz", jako przyczynę podano luki w zabezpieczeniach (należy podać powód, to jest uprzejme). Pierwsze pole jest nieużywane, więc pozostaw je puste.

## 4.14 Połączenia usług

Wprowadzenie
Usługa to szczególny rodzaj klienta IRC. Nie ma on pełnych zdolności normalnego użytkownika, ale może zachowywać się w sposób bardziej aktywny niż zwykły klient.

Usługi nie są przeznaczone do użytku interaktywnego i lepiej sprawdzają się jako klienty automatyczne.

Format
S:<Maska Hosta CELU>:<Hasło>:<Nazwa Usługi>:<Typ Usługi>:<Klasa>
Maska Hosta CELU
Maska hosta powinna być ustawiona tak, aby pasowała do hosta(lub hostów), z których usługa będzie się łączyć. Może to być zarówno numer IP, jak i pełna nazwa (preferowana).

Hasło
To hasło, które musi być przekazane w poleceniu SERVICE.

Nazwa Usługi
Nazwa używana przez usługę. Usługi nie mają pseudonimów, ale mają stałą nazwę zdefiniowaną w linii S.

Typ Usługi
Typ usługi określa uprawnienia danej usługi. Bądź bardzo ostrożny w udzielaniu uprawnień. Typy usług można znaleźć w include/service.h

Klasa
Pole klasy powinno odnosić się do istniejącej klasy.

Uwagi
Usługa to niezbyt przydatny rodzaj klienta, nie może dołączać do kanałów ani wydawać pewnych poleceń, chociaż większość z nich jest dostępna. Usługi są odrzucane po wysłaniu nieznanego lub niedozwolonego polecenia. Usługi jednak nie podlegają kontroli nad nadmiernym ruchem i mogą uzyskać specjalne przywileje. Dlatego mądrze jest bardzo ostrożnie korzystać z linii S.

## 4.15 Serwer przekierowujący (bounce)

Wprowadzenie
To dostarcza sposób przekierowywania klientów na inny serwer. Te informacje są udostępniane klientom, którzy są odrzucani podczas próby połączenia, albo dlatego, że ich klasa połączenia jest pełna, albo serwer jest pełen, albo nie są upoważnieni do połączenia.

Format
B:<Klasa|Maska Hosta>::<Nazwa Serwera>:<Port>:
B
To określa rekord przekierowania.

Klasa|Maska Hosta
To pole określa, do którego klienta ma być stosowana ta linia konfiguracyjna. Może to być numer klasy połączenia, maska hosta, która ma pasować do nazwy hosta klienta, lub adres IP/maska/bitmaska, która ma pasować do adresu IP klienta.

Kiedy serwer jest całkowicie zajęty, odrzuca klientów z komunikatem "Wszystkie połączenia są w użyciu". W takim przypadku serwer nie przetwarza połączeń w ogóle i nie ma wiedzy o nazwie hosta klienta ani numerze klasy. W tych przypadkach to pole musi być puste.

Nazwa Serwera
To określa nazwę hosta serwera IRC, którą klient powinien używać.

Port
To określa port serwera IRC, do którego klient powinien się podłączyć.

Przykład
B:2::irc.stealth.net:6660:

Odrzuceni klienci z klasy 2 są informowani, że powinni użyć "irc.stealth.net" na porcie 6660.

B:*.fi::irc.funet.fi:6667:

Klienci z Finlandii powinni używać irc.funet.fi, gdy nie można ich przyjąć gdzie indziej.

B:::irc2.stealth.net:6667:

Gdy serwer jest całkowicie zajęty, klienci powinni używać serwera zapasowego "irc2.stealth.net" na porcie 6667.



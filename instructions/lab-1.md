# **Kriptografija i mrežna sigurnost - Lab 1**

## ARP spoofing attacks

U okviru vježbe upoznajemo se s osnovnim sigurnosnim prijetnjama i ranjivostima u računalnim mrežama. Analizirat ćemo ranjivost _Address Resolution Protocol_-a (ARP). Detaljan tehnički opis ranjivosti ARP mrežnog protokola dostupan je na web stranici predmeta.

Zadatak studenta je dešifrirati tekst/vic enkriptiran AES šifrom u CBC enkripcijskom modu. Ključ koji je potreban za dekripciju student treba otkriti u interakciji s odgovarajućim virtualnim serverom (kojeg kolokvijalno zovemo **crypto oracle**). Šifrirani tekst student može dohvatiti konzumiranjem REST API-ja koji je dokumentiran i dostupan na studentovom virtualnom web serveru. Student zadatak rješava u nekoliko koraka kako je navedeno u nastavku.

1. Otkriti IP adresu vlastitog virtualnog servera. Iskoristite činjnicu da jedan centralni server periodično šalje (_broadcast_) svim računalima na podmreži (`10.0.0.0/20`) korisne informacije.

2. Izvršiti ARP spoofing napad (_Kali Linux_) i otkriti tajni _cookie_ iz kojeg se izvodi dekripcijski ključ za dekripciju šifriranog teksta. Centralni server periodično šalje studentovom serveru `POST` zahtjeve na `/arp`. Studentov server na ovaj zahtjev vraća centralnom serveru tajni _cookie_. Sva komunikacija ide u _čisto_ (nije zaštićena).

    _Cookie_ možete pokušati dobiti na isti način kao i centralni server, no postoji jedno ograničenje: `POST` na `/arp` zahtjeva autentikacijski ključ koji zna samo server. Stoga je najednostavniji način otkrivanja _cookie_-ja zapravo _ARP spoofing_ napad.

3. Izvodite dekripcijski ključ na osnovu _cookie_-ija. Ključevi se izvode primjenom _password-based key derivation function v2 (`pkdfv2`)_. Parametri `pbkdfv2` funkcije koji su korišteni za izvođenje ključa možete pronaći u _[source code](/crypto-oracle)_-u (pogodite u kojoj datoteci :-).

4. Dohvatite šifrirani tekst s virtualnog servera slanjem `GET` zahtjeva na `/arp/challenge`, te ga dešifrirajte koristeći ključ iz prethodnog koraka. Za dekripciju možete koristiti i neki _online_ servis koji implementira AEC-CBC (mi smo uspješno testirali jedan takav); ili iskoristite nekakvu biblioteku koja implementira ovakav način zaštite.

5. Uživajte u dešifriranom vicu i podijelite ga s drugima :-)

Profesori će biti na raspolaganju za sva vaša pitanja i eventualne nedoumice.
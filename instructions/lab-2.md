# **Kriptografija i mrežna sigurnost - Lab 2**

## ECB mode vulnerabilities

_Electronic CodeBook (ECB)_ je način enkripcije poruka primjenom blok šifri kao što su DES/3DES, AES i dr. Budući da blok šifre rade s blokovima fiksne duljine (npr. **AES koristi 128-bitne blokove**), poruke koje su dulje od nominalne duljine bloka dane šifre moramo razbiti na više blokova prije enkripcije. U ECB modu, svaki blok se enkriptira/dekriptira odvojeno i neovisno od drugih blokova.

Neka je P _plaintext_ poruka duga _m_ blokova, P = P<sub>1</sub>,P<sub>2</sub>, ... ,P<sub>m</sub>. U ECB enkripcijskom modu odgovarajući _ciphertext_ dobije se kako slijedi (vidi priloženu sliku): C = C<sub>1</sub>,C<sub>2</sub>, ... ,C<sub>m</sub>, uz C<sub>i</sub> = E<sub>K</sub>(P<sub>i</sub>), za i=1...m.

<p align="center">
<img src="../img/ecb.PNG" alt="ECB encryption" width="400px" height="auto"/>
<br><br>
<em>Enkripcija u ECB modu</em>
</p>

U ovoj vježi pokazat ćemo da prikazan pristup enkripciji podataka ne osigurava povjerljivost poruke, iako koristimo siguran algoritam za enkripciju, AES, i tajan enkripcijski ključ.

Zadatak studenta je dešifrirati tekst/vic enkriptiran AES šifrom u CBC enkripcijskom modu. Ključ koji je potreban za dekripciju student treba otkriti u interakciji s odgovarajućim virtualnim serverom (kojeg kolokvijalno zovemo **_crypto oracle_**). Šifrirani tekst student može dohvatiti konzumiranjem REST API-ja koji je dokumentiran i dostupan na studentovom virtualnom web serveru.

### Opis REST API-ja

U ovoj vježbi student će slati sljedeće HTTP zahtjeve svom _crypto oracle_-u:

```Bash
POST /ecb HTTP/1.1
plaintext = 'moj plaintext'
```

Primjenom npr. `curl` alata (Kali Linux) navedeni zahtjev možete napraviti kako slijedi:

```Bash
curl -d plaintext='moj plaintext' -X POST http://10.0.0.x/ecb
```

_Crypto oracle_ (vaš REST server) uzima vaš _plaintext_, spaja ga s tajnim _cookie_-jem, enkriptira rezultat (tj. `plaintext + cookie`) primjenom AES šifre u ECB modu tajnim 256 bitnim ključem (`aes-256-ecb`) i vraća vam odgovarajući _ciphertext_.

```Bash
{"ciphertext":"65a192c1cdf3a75c344c3535b3fccb2366c636e07094726194bc7375a09ca672"}
```

NAPOMENA: Striktno govoreći, server će enkriptirati `plaintext + cookie + padding`; `aes-256-ecb` automatski dodaje _padding_, no ovaj detalj nije toliko relevantan za rješavanje zadatka.

Kao i u prethodnoj vježbi, konačan cilj je dekriptirati vic o Chuck Norrisu. Tekst vica enkriptiran je ključem izvedenim iz tajnog _cookie_-ja (`aes-256-cbc`), a možete ga dohvatiti kako slijedi:

```Bash
GET /ecb/challenge HTTP/1.1
```

### Kratki savjeti

1. Ranjivost ECB enkripcijskog moda proizlazi iz činjnice da jednostavno možete uočiti jesu li dva (potencijalno tajna) _plaintext_ bloka identična tako da uspoređujete odgovarajuće _ciphertext_ blokove. Budući da se radi o determinističkoj enkripciji, isti _plaintext_ blok rezultirat će istim _ciphertext_ blokom; ako se koristi isti enkripcijski ključ (naš slučaj).

2. Iskoristite prethodnu činjnicu i pokušajte ECB _crypto oracle_-u slati različite _plaintext_ poruke. Razmislite kako bi trebali varirati testne _plaintext_ poruke da bi vam ECB _oracle_ dao potencijalno korisnu informaciju.

3. **VAŽNO**: Tajni _cookie_ dug je 16B (128 bitova).

4. Koristite _primitivna_ sredstva poput olovke i papira te pokušajte sebi skicirati ovaj problem.

Uživajte u dešifriranom vicu i podijelite ga s drugima :-)
# **Kriptografija i mrežna sigurnost - Lab 3**

## CBC mode and predictable IVs

_Cipher Block Chaining (CBC)_ **probabilistički** je način enkripcije poruka primjenom blok šifri (npr., AES). Blok šifre rade s blokovima fiksne duljine; npr. AES koristi 128-bitne blokove. Poruke dulje od nominalne duljine bloka dane šifre enkriptiramo na način da poruku podijelimo na više blokova prije enkripcije. Za razliku od _nesigurnog_ ECB enkripcijskog moda, kod CBC enkripcijskog moda _plantext_ blokovi se ulančavaju (eng. _chaining_) kako je prikazano na slici u nastavku. 

<p align="center">
<img src="../img/cbc.PNG" alt="CBC encryption" width="400px" height="auto"/>
<br><br>
<em>Enkripcija u CBC modu</em>
</p>

Uz pretpostavku da se primjenjena blok šifra (npr. AES) ponaša kao **pseudo-random permutacija**, ulančavanjem se postiže _nasumičnost/randomizacija_ šifriranog _plaintext_-a. Uloga _inicijalizacijskog vektora (IV)_ je randomizacija prvog bloka _plaintext_-a. CBC enkripcijski mod **siguran je način enkripcije** uz određene pretpostavke; formalno govoreći, CBC mod zadovoljava svojstvo _Indistinguishability under Chosen Plaintext Attack (IND-CPA)_ - napadač ne može razlikovati koja od dvije poruke, po napadačevom izboru, je enkriptirana na osnovu _ciphertext_-a u igri koju smo opisali u okviru predavanja.

Važna pretpostavka za sigurnost CBC moda je **nepredvidivost/nasumičnost inicijalizacijskog vektora**. U nastavku je slikovito prikazana prednost CBC u odnosu na ECB mod; identična slika karakterizirana visokim stupnjem redudancije enkripritana je u ECB i CBC modu (primjenom AES blok šifre).

<p align="center">
<img src="../img/tux.png" alt="ECB vs CBC" width="400px" height="auto"/>
<br>
<em>ECB vs CBC</em>
</p>

### Predictable IV implies insecure CBC

U ovoj vježi pokazat ćemo _ranjivost CBC enkripcijskog moda u slučaju kad nije ispunjena pretpostavka o nepredvidivosti/nasumičnosti inicijalizacijskog vektora (IV)_.

**VAŽNO: Za razliku od prethodne dvije vježbe u okviru ove student treba otkriti odgovarajuću tajnu riječ koja je odabrana nasumično iz javno dostupne liste _wordlist.txt_; student ne dekriptira vic o Chuck Norris-u.**

Kao i kod ECB moda, student će implementirati _chosen-plaintext attack (CPA)_, odnosno slati će upite (tj. _plaintext_) _crypto oracle_-u koji će iste enkriptirati u CBC modu i potom vraćati enkriptirani _ciphertext_ natrag studentu. _Crypto oracle_ pri navedenoj enkripciji **bira IV na predvidiv način (ne nasumično)** pa student može predvidjeti IV pod kojim će njen/njegov _plaintext_ biti enkriptiran. Ranjivost ove implementacije proizlazi i iz činjnice da student može potpuno kontrolirati ulaz u enkripcijski algoritam tj., P<sub>1</sub>⊕IV (student treba razmisliti i istražiti kako).

Koristeći ovu činjenicu, kao i činjenicu da je enkripcijski algoritam deterministička funkcija, tj., isti _plaintext_ blok rezultirat će istim _ciphertext_ blokom, student može _relativno jednostavno_ testirati potencijalne riječi iz javnog rječnika _wordlist.txt_ slanjem istih _crypto oracle_-u, te usporediti dobivene odgovore s _challenge ciphertext_-om koji _oracle_ također vraća na upit (`GET cbc/iv/challenge`).

### Opis REST API-ja

U ovoj vježbi student će slati sljedeće HTTP zahtjeve svom _crypto oracle_-u:

```Bash
POST /cbc/iv HTTP/1.1
plaintext = 'moj plaintext'
```

Primjenom npr. `curl` alata (Kali Linux) navedeni zahtjev možete napraviti kako slijedi:

```Bash
curl -d plaintext='moj plaintext' -X POST http://10.0.0.x/cbc/iv
```

_Crypto oracle_ (vaš REST server) uzima vaš _plaintext_, dodaje _padding_ prema PKCS#7 standardu (vidjeti primjere u nastavku), te enkriptira `plaintext + padding` u CBC modu tajnim 256 bitnim ključem (`aes-256-cbc`) i vraća vam odgovarajući _ciphertext_ zajedno s odgovarajućim inicijalizacijskim vektorom (IV).

```Bash
{
    "iv":"1a2c3c2a9658864a6545174945f59583",
    "ciphertext":"896633eee8bbfbed8447d9b49ce5b595"
}
```

Cilj ove vježbe je otkriti tajnu riječ koja vam je nasumično dodjeljena iz rječnika _wordlist.txt_. Rječnik je javan; možete ga dohvatiti kako slijedi:

```Bash
GET /wordlist.txt HTTP/1.1
```

Osim rječnika, za uspješno rješavanje ovog izazova trebate i odgovarajući _challenge ciphertext_ koji _oracle_ također vraća na upit: 

```Bash
# Request
GET /cbc/iv/challenge HTTP/1.1

# Response
{
    "iv":"4a77dd877da00ac6f4f92ceb35e57578",
    "ciphertext":"40c92970bf1ebc8e12b1c450d8c5367b"
}
```

Ovaj _ciphertext_ i IV rezultat su enkripcije tajne riječi u CBC modu.

### Kratki savjeti

1. Koristite _primitivna_ sredstva poput olovke i papira te pokušajte sebi skicirati ovaj problem. Napišite matematički izraz za enkripciju u CBC modu.

2. Uvjerite sebe da možete predvidjeti buduće IV.

3. Riječi iz rječnika _wordlist.txt_ kraće su od 16 byte-a. Kao takve, _crypto oracle_ dodaje odgovarajući _padding_ neposredno prije njihove enkripcije. _Padding_ je opisan u PKCS#7 standardu:

    ```Bash
    # plaintext size: 1 byte
    plaintext: 00
    w/padding: 00:0f:0f:0f:0f:0f:0f:0f:0f:0f:0f:0f:0f:0f:0f:0f

    # plaintext size: 2 byte
    plaintext: 00:01
    w/padding: 00:01:0e:0e:0e:0e:0e:0e:0e:0e:0e:0e:0e:0e:0e:0e

    # plaintext size: 10 byte
    plaintext: 00:01:02:03:04:05:06:07:08:09
    w/padding: 00:01:02:03:04:05:06:07:08:09:06:06:06:06:06:06

    # plaintext size: 13 byte
    plaintext: 00:01:02:03:04:05:06:07:08:09:0a:0b:0c
    w/padding: 00:01:02:03:04:05:06:07:08:09:0a:0b:0c:03:03:03
    ```

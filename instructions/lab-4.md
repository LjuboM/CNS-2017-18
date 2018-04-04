# **Kriptografija i mrežna sigurnost - Lab 3**

## CTR mode and repeated IVs

_Counter (CTR) mode_ je način enkripcije kod kojeg se primjenom blok šifre (npr., AES) realizira funkcionalnost slijedne šifre (_stream cipher_). CTR mode generira _pseudo-slučajan_ niz ključeva na način da enkriptira odgovarajuće i sukcesivne vrijednosti brojača (_counter_). _Plaintext_ se zatim enkriptira jednostavnom _xor_ operacijom s generiranim _pseudo-slučajnim_ nizom ključeva.
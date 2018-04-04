# **Kriptografija i mrežna sigurnost - Lab 3**

## CTR mode and repeated IVs

_Counter (CTR) mode_ je način enkripcije koji primjenom blok šifri (npr., AES) realizira funkcionalnost slijednih šifri (_stream cipher_). CTR mode generira _pseudo-slučajan_ niz ključeva na način da enkriptira odgovarajuće vrijednosti brojača (_counter_). _Plaintext_ se enkriptira jednostavnom _xor_ operacijom s generiranim _pseudo-slučajnim_ nizom ključeva.
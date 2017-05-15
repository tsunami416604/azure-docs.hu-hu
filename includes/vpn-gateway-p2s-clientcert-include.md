Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Az ügyféltanúsítványokat a főtanúsítványból hozzák létre, majd telepítik az egyes ügyfélszámítógépekre. Ha nincs telepítve érvényes ügyféltanúsítvány, és az ügyfél megpróbál csatlakozni a virtuális hálózathoz, akkor a hitelesítés meghiúsul.

Létrehozhat egy egyedi tanúsítványt minden ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfélhez is. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy visszavonhat egyetlen tanúsítványt is. Ha azonban több ügyfél is ugyanazt az ügyféltanúsítványt használja, és Önnek vissza kell vonnia a tanúsítványt, az összes olyan ügyfél számára elő kell állítania és telepítenie kell új tanúsítványokat, amelyek az adott tanúsítványt használják a hitelesítéshez.

Ügyféltanúsítványokat a következő módszerekkel hozhat létre:

- **Vállalati tanúsítvány:**

  - Ha vállalati tanúsítványmegoldást használ, az általános „name@yourdomain.com” formátumban hozza létre az ügyféltanúsítványokat a „tartománynév\felhasználónév” formátum helyett.
  - Ellenőrizze, hogy az ügyféltanúsítvány azon a „felhasználói” tanúsítványsablonon alapul-e, amely használati listájának első helyén az „ügyfél-hitelesítés” áll, nem pedig az intelligens kártyás bejelentkezés vagy egyebek. A tanúsítvány ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és tekintse meg a *Részletek > Kibővített kulcshasználat* részt.

- **Önaláírt főtanúsítvány:** Ha egy önaláírt főtanúsítványból [az önaláírt főtanúsítványok pont–hely kapcsolatokhoz történő létrehozására](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert) vonatkozó utasítások segítségével hoz létre ügyféltanúsítványt, akkor automatikusan a létrehozásához használt számítógépre települ. Ha egy ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne telepíteni, akkor exportálnia kell azt. [A tanúsítványok exportálásához](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) kövesse a cikkben szereplő utasításokat.
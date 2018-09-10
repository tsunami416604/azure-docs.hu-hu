A szolgáltatás és az előfizetés típusa határozza meg a másodpercenként futtatható lekérdezések számát (QPS). Mindenképpen gondoskodjon arról, hogy az alkalmazás tartalmazza a kvótán belül maradáshoz szükséges logikát. Ha eléri vagy túllépi a QPS határértékét, a kérés meghiúsul, és a rendszer HTTP 429-es állapotkódot ad vissza. A válasz a `Retry-After` fejlécet is tartalmazza, amely azt adja meg, hogy mennyi ideig kell várnia egy újabb kérés elküldése előtt.

## <a name="denial-of-service-versus-throttling"></a>Szolgáltatásmegtagadás kontra szabályozás

A szolgáltatás különbséget tesz a szolgáltatásmegtagadásos (DoS-) támadások és a QPS-eltérések között. Ha DoS-támadást feltételez, a kérés sikeres lesz (200 OK HTTP-állapotkóddal). A választörzs azonban üresen marad.
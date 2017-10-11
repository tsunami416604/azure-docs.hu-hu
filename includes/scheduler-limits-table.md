A következő táblázat ismerteti a fő kvóták, korlátok, alapértelmezett és az Azure Scheduler szabályozások.

| Erőforrás | Korlát leírása |
| --- | --- |
| **Feladat mérete** |Feladat maximális mérete 16 KB-os. Ha egy PUT vagy egy javítás egy nagyobb, mint a működés felső korlátjának feladatot eredményez, 400 Hibás kérés állapotkód adja vissza. |
| **Kérelem URL-cím mérete** |A kérelem URL-cím maximális mérete 2048 karakter. |
| **Összesített fejléc mérete** |Maximális összesített fejléc mérete 4096 karakter. |
| **Fejléc száma** |A fejléc maximális száma 50 fejlécek. |
| **Törzs mérete** |Maximális törzs mérete: 8192 karakter. |
| **Ismétlődés span** |Maximális ismétlődésére span 18 hónap. |
| **A kezdési időpont idő** |"Idő kezdési idő" legfeljebb 18 hónap. |
| **Feladatelőzmények** |A feladatelőzmények tárolt maximális választörzs mérete 2048 bájt. |
| **Gyakoriság** |Az alapértelmezett maximális gyakoriság kvóta a tartalmaz szabad feladatgyűjteményt 1 óra és a szabványos feladatgyűjteményben 1 perc. A maximális gyakoriság nem konfigurálható egy kisebb, mint a maximális gyűjteményen. A feladatgyűjtemény levő összes feladatnak korlátozódnak értéke a feladatgyűjtemény állítható be. Ha egy feladat létrehozásakor egy nagyobb gyakorisággal, mint a feladatgyűjtemény maximális gyakorisága majd kérelem sikertelen lesz, és ütközés 409 állapotkódot. |
| **Feladatok** |Az alapértelmezett maximális feladatok kvóta tartalmaz szabad feladatgyűjteményt 5 feladatok és a szabványos feladatgyűjteményben 50 feladat. A maximális számú feladat lehet konfigurálni egy gyűjteményen. A feladatgyűjtemény levő összes feladatnak korlátozódnak értéke a feladatgyűjtemény állítható be. Ha több, mint a maximális feladatok kvóta feladat létrehozásához, majd a kérés nem teljesíthető 409 ütközés állapotkóddal. |
| **Feladatgyűjtemények** |Előfizetésenként feladatgyűjtemény maximális száma érték 200 000. |
| **Feladat előzményeinek megőrzése** |Legfeljebb 2 hónapra vagy akár az utolsó 1000 végrehajtások feladat előzményei őrződnek meg. |
| **Feladat befejeződött, és a hibás megőrzése** |Befejeződött, és a hibás feladatok 60 napig maradnak. |
| **Időtúllépés** |Nincs olyan statikus (nem konfigurálható) kérelmi időkorlátot. a HTTP-műveletek 60 másodperc. A hosszabb ideig futó műveletek kövesse a HTTP aszinkron protokollok; például egy 202 azonnal vissza, de továbbra is működik-e a háttérben. |


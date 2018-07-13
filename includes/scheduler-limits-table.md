Az alábbi táblázat ismerteti a fő kvóták, korlátozások, alapértékek és az Azure Schedulerben szabályozások.

| Erőforrás | Korlát leírása |
| --- | --- |
| **Feladat mérete** |Maximális mérete 16 KB. Ha egy PUT vagy JAVÍTÁST egy nagyobb, mint ezeket a korlátokat feladatot eredményez, 400 Hibás kérés állapotkódot adott vissza. |
| **Kérelem URL-cím mérete** |A kérelem URL-cím maximális mérete 2048 karakter. |
| **Összesített fejléc mérete** |Maximális összesített fejléc mérete 4096 karakter. |
| **Fejléc száma** |A fejléc maximális száma 50 fejlécek. |
| **Szervezet mérete** |Maximális törzs mérete 8192 karaktert. |
| **Ismétlődési tartomány** |Maximális ismétlődési span 18 hónap. |
| **Kezdés ideje** |"Idő, kezdési idő" maximum 18 hónap. |
| **Feladatelőzmények** |A feladatelőzmények tárolt maximális választörzs mérete 2048 bájt. |
| **Gyakoriság** |Az alapértelmezett maximális gyakoriság kvóta az ingyenes feladatgyűjtemények 1 órája és 1 perc, a standard feladatgyűjtemény. A maximális gyakoriság nem konfigurálható a feladatgyűjtemények kisebb, mint a maximális érték. Korlátozottak a feladatgyűjtemény minden feladata a feladatgyűjtemény a beállított érték. Ha hozzon létre egy feladatot egy nagyobb gyakorisággal, mint a feladatgyűjtemény maximális gyakoriságát megpróbálja majd kérelem sikertelen lesz, és ütközés 409 állapotkódot. |
| **Feladatok** |Az alapértelmezett maximális feladatok kvóta az ingyenes feladatgyűjtemények 5 feladat és a egy standard feladatgyűjtemény 50 feladatok. A feladatok maximális száma nem konfigurálható a feladatgyűjteményeket. Korlátozottak a feladatgyűjtemény minden feladata a feladatgyűjtemény a beállított érték. Ha tárolórétegeket próbál létrehozni, mint a maximális feladatok kvóta feladatot, akkor a kérelem sikertelen lesz 409 ütközés állapotkód. |
| **Feladatgyűjtemények** |Feladatgyűjtemény előfizetésenként legfeljebb 200 000. |
| **Feladat Előzmények megőrzése** |Feladat előzményei őrződnek meg, legfeljebb 2 hónapra, vagy akár a legutóbbi 1000 végrehajtások. |
| **A befejeződött vagy meghiúsult feladatok megőrzése** |A befejeződött vagy meghiúsult feladatok 60 napig megőrződnek. |
| **Időtúllépés** |Nincs olyan statikus (konfigurálható) kérés időtúllépése HTTP-műveletek esetében 60 másodperc. A hosszabb ideig futó műveletek esetében kövesse az aszinkron protokollok HTTP; például egy 202 azonnal vissza, de közben tovább dolgozhat a háttérben. |


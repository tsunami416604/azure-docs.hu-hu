---
title: Kérelemegységek létrehozását és átviteli sebesség – Azure Cosmos DB |} A Microsoft Docs
description: Ismerje meg, amíg annak ismertetése, adja meg, és megbecsülheti az Azure Cosmos DB kérelem egységekre vonatkozó követelményeket.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: rimman
ms.openlocfilehash: 23a3e629e12e2a4d417757c9fef5db804bb72c9e
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248754"
---
# <a name="throughput-and-request-units-in-azure-cosmos-db"></a>Teljesítmény- és kérésegységek, az Azure Cosmos DB

Az Azure Cosmos DB-erőforrások fizetendő díjat a kiosztott átviteli sebesség és a tárolás. Az Azure Cosmos DB-átviteli fejezzük ki, hogy **kérelemegység / másodperc (RU/s)**. Az Azure Cosmos DB támogatja a különböző API-k, amelyek különböző műveleteket, és a simple beolvassa és az összetett graph-lekérdezéseket. Minden egyes kérelem alapján a számítást, ami szükséges a kérés kiszolgálása összegének kérelemegység használ fel. A művelet kérelemegység száma nem determinisztikus. Nyomon követheti a válaszfejléc használatával az Azure Cosmos DB-ben minden művelet által felhasznált kérelemegységek számát. Kiszámítható teljesítmény elérése érdekében átviteli egység: 100 Kérelemegység/s kell lefoglalni. Az Azure Cosmos DB segítségével meg tudja becsülni az átviteli sebesség igényeinek [kérelem egység Számológép](https://www.documentdb.com/capacityplanner).

Az Azure Cosmos DB átviteli sebességet két granularitással is kioszthatja: 

1. **Az Azure Cosmos DB-tároló:** a tárolóban van fenntartva, csak az adott tároló számára kiosztott átviteli sebesség. A tároló szintjén throughput(RU/s) hozzárendelésekor a tárolók hozható létre **rögzített méretű** vagy **korlátlan**. 

  Rögzített méretű tárolók maximális átviteli sebesség legfeljebb 10 000 RU/s és 10 GB-os tárolási kapacitást kell. A korlátlan tároló létrehozásához meg kell adnia egy minimális 1000 RU/s átviteli sebességet és a egy [partíciókulcs](partition-data.md). Az adatok esetleg feloszthatók több partíciót, mert akkor válassza ki a partíciós kulcs, amely rendelkezik egy nagy számosságú (100 millió egyedi érték). Számos különböző értékeket a partíciókulcs kiválasztásával az Azure Cosmos DB biztosítja, hogy a kérések egy gyűjtemény, tábla és a graph egyenletesen vannak-e méretezve. 

2. **Az Azure Cosmos DB-adatbázishoz:** az adatbázis közösen használja, hogy az adatbázis belül a tárolók számára kiosztott átviteli sebességet. Az adatbázis szintjén átviteli kiépítésekor lehet váltani, amelyeket kifejezetten kizár egyes tárolók, és inkább oszthatnak ki átviteli kapacitásokat a ezeket a tárolókat a tároló szintjén. Adatbázis-szintű átviteli létrejön egy partíciókulccsal rendelkező összes gyűjtemény szükséges. Az adatbázis szintjén átviteli hozzárendelésekor a partíciókulccsal rendelkező ehhez az adatbázishoz tartozó tárolókat kell létrehozni, mert minden gyűjtemény egy **korlátlan** tároló.  

A kiosztott átviteli sebesség alapján, az Azure Cosmos DB foglal le a tároló(k) bérletét, és az elágazást adatokat tárolni a több partícióra kiterjedő növekedésével, fizikai partíciók. Az alábbi ábrán különböző szinteken kiépítési átviteli sebesség:

  ![Különálló tárolókat, és a tárolók beállított kiépítési kérelemegység](./media/request-units/provisioning_set_containers.png)

> [!NOTE] 
> Kiépítés átviteli sebességet a tároló szintjén és az adatbázis szintjén különálló ajánlatok, és mindkét közötti váltáskor szükséges adatok áttelepítése a forrás célhelyre. Ami azt jelenti, hogy hozzon létre egy új adatbázist vagy egy új gyűjteményt, és ezután telepítse át az adatok segítségével kell [tömeges végrehajtó könyvtár](bulk-executor-overview.md) vagy [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

## <a name="request-units-and-request-charges"></a>Kérelemegységek és a kérés díjak

Az Azure Cosmos DB az erőforrások lefoglalásával gyors és kiszámítható teljesítményt nyújt az alkalmazása átviteli sebességre vonatkozó igényeinek kielégítése érdekében. Az alkalmazás terhelése és a hozzáférési mintázatok idővel változnak. Az Azure Cosmos DB segítségével egyszerűen növelhető vagy csökkenthető az alkalmazás számára rendelkezésre álló, lefoglalt átviteli sebesség.

Az Azure Cosmos DB szolgáltatás számára fenntartott átviteli sebesség kérelemegység / másodperc feldolgozása van megadva. Átviteli sebesség pénznemként kérelemegység is felfoghatók. Fenntartott számos garantált kérelemegység másodpercalapú elszámolással, az alkalmazás számára elérhetővé válnak. Egyes műveletek az Azure Cosmos DB, beleértve a dokumentumok írása a lekérdezés végrehajtása, és a frissített egy dokumentumot, használ fel, a Processzor, memória és iops-t. Azt jelenti minden művelet kötelezettséggel jár kérelem, amely kérelemegység van kifejezve. Miután megértette a kérést egységekre vonatkozó díjakon és az alkalmazás átviteli sebességet megkövetelő befolyásoló tényezők, a lehető leghatékonyabban költséggel futtathatja az alkalmazást. 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>A globálisan elosztott adatbázisokat átviteli elkülönítés

Az adatbázis replikálása több régióban, az Azure Cosmos DB el vannak különítve átviteli sebesség, győződjön meg arról, hogy a kérelem egység használat egy adott régióban nem érinti unit-használaton kérelmet egy másik régióban. Például, ha az egyik régióra adatokat írni, és olvashatja az adatokat egy másik régióban, a kérelemegységek régióban egy az írási művelet végrehajtásához használt nem kell erről a kérelemegység az olvasási műveletet régióban b kérelem által használt egységek a makró nem felosztása ss a régió, amelyben az adatbázis telepítette. Minden egyes régió, amelyben a rendszer replikálja az adatbázist rendelkezik kiosztott kérelemegységek teljes száma. Globális replikációval kapcsolatos további információkért lásd: [hogyan globális adatterjesztés az Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Kérelem egység kapcsolatos szempontok
Megbecsülheti, hogy az üzembe helyezni a kérelemegységek számát, fontos figyelembe venni az alábbi változókat:

* **Konfigurációelem-méret**. Mérete nő, az adatok olvasására vagy írására, felhasznált kérelemegységek számát is növekszik.
* **Konfigurációelem-tulajdonság száma**. Feltéve, hogy az összes tulajdonságokkal, írása egy dokumentumot, a csomópont vagy a szervezet növelését a tulajdonság száma növekszik, felhasznált egységek indexelő alapértelmezett.
* **Az adatkonzisztencia**. Adatok konzisztenciamodellt kínál, például erős, PBS vagy használatakor a felhasználásuk hónapjára kérelemegységeket cikkek elolvasására.
* **Indexelt tulajdonságok**. Az egyes tárolók index szabályzatokhoz meghatározza, hogy mely tulajdonságok indexelt alapértelmezés szerint. A kérelem-egységek felhasználását az írási műveletek száma a indexelt tulajdonságok vagy Lusta indexelő engedélyezésével csökkentheti.
* **A dokumentum az indexelés**. Alapértelmezés szerint automatikusan indexelt egyes elemek. Ha úgy dönt, hogy az elemek egyes indexelje felhasznált kérelemegységek kevesebb.
* **Lekérdezési minták**. A lekérdezés összetettségétől hatással van egy művelet felhasznált kérelemegységek számát. A lekérdezés száma eredményeket, predikátumok, a predikátumok jellegét, felhasználó által definiált függvények száma, a forrásadatok mérete számát, és minden leképezések hatással vannak a lekérdezési műveletek költségét.
* **Parancsfájl-használat**. Lekérdezéseket, a tárolt eljárásokkal és eseményindítókkal végrehajtott műveletek összetettsége alapján kérelemegység felhasználni. Az alkalmazás fejlesztését, vizsgálja meg jobb megértése érdekében hogyan használ fel az egyes műveletek az kérelem egység kapacitás kérelemfejlécből díj.

## <a name="estimating-throughput-needs"></a>Teljesítménybeli becslése
Kérelemegység kérelemfeldolgozási költség normalizált mértékegysége. Egyetlen kérelemegység jelöli olvasásához szükséges feldolgozási kapacitása (keresztül önhivatkozást vagy azonosító) egy 1 KB-os elem, amely 10 egyedi tulajdonság értéket (kivéve a rendszer tulajdonságai) áll. (Beszúrás), létrehozására irányuló kérelem cseréje vagy törlése az azonos elem a szolgáltatásból további feldolgozást igényel, és ezáltal a további kérelemegység szükséges. 

> [!NOTE]
> Az alapkonfiguráció 1 kérelemegység egy 1 KB-os elem esetében, egy egyszerű GET önkiszolgáló csatlakozásonkénti vagy az elem azonosítója felel meg.
> 
> 

Például a következő hány kérelemegység cikkek három különböző méretű (1 KB, 4 KB-os és a 64 KB-os) és a két különböző teljesítmény-szinteket üzembe helyezni egy táblázat (500 olvasás/mp + 100 írási műveletek száma másodpercenként és 500-as olvasás/mp + 500 írási műveletek száma másodpercenként). Ebben a példában az adatok konzisztenciája a **munkamenet**, és az indexelési szabályzat úgy van beállítva **None**.

| Elem mérete | Olvasás/mp | Írások/másodperc | Kérelemegységek
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1000 RU/s
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3000 RU/s
| 4 KB | 500 | 100 | (500 * 1,3) + (100 * 7) = 1350 RU/s
| 4 KB | 500 | 500 | (500 * 1,3) + (500 * 7) = 4,150 RU/s
| 64 KB | 500 | 100 | (500 * 10) + (100 * 48) = 9,800 RU/s
| 64 KB | 500 | 500 | (500 * 10) + (500 * 48) = 29,000 RU/s

### <a name="use-the-request-unit-calculator"></a>A kérelem egység kalkulátor használata
Az átviteli sebesség becsléseket finomhangolása segítségével használhatja a webalapú [kérelem egység Számológép](https://www.documentdb.com/capacityplanner). A kalkulátor segítségével az Ön becsült költsége jellemző műveleteket, köztük a kérelem egység követelményei:

* Elemet hoz létre (írás)
* Elem beolvasása
* Elem törlése
* Elem frissítések

Az eszköz az adattárolási igények alapján az Ön által megadott minta elemek becslése támogatását is magában foglalja.

Az eszköz használatához:

1. Töltsön fel egy vagy több jellemző elemet (például egy minta-JSON-dokumentumot).
   
    ![Elemeket tölthet fel a kérés egység kalkulátor][2]
2. Ha meg szeretné becsülni az adattárolási követelmények, adja meg az elemek száma, (például dokumentumok, sorok vagy csúcspontok), amely várhatóan tárolni.
3. Adja meg a létrehozási, olvasási, frissítési és törlési műveleteket, amelyekre szüksége van (az másodpercenkénti alapján). A cikk a frissítési műveletek kérelem egységekre vonatkozó díjakon megbecsülni, töltse fel az 1. lépés, amely jellemző mező frissítések minta elem egy példányát. Például ha elem frissítések általában két tulajdonságainak módosítása nevű *lastLogin* és *userVisits*, másolja egy minta item, frissítse az értékeket két ezeknél a tulajdonságoknál és a másolt elem, majd feltölt.
   
    ![Adja meg az átviteli sebességet megkövetelő a kérelem egység kalkulátor][3]
4. Válassza ki **Calculate**, és vizsgálja meg az eredményeket.
   
    ![Kérelem egység Számológép eredményei][4]

> [!NOTE]
> Ha rendelkezik, amely jelentősen méretét és az indexelt tulajdonságok tekintetében különböznek konfigurációelem-típusok, töltse fel az egyes minta *típus* tipikus konfigurációelemet az eszközt, és számítani, az eredményeket.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Az Azure Cosmos DB díj válasz kérelemfejléc
Az Azure Cosmos DB szolgáltatás minden válasz tartalmaz egy egyéni fejlécet (`x-ms-request-charge`), amely tartalmazza az adott kéréshez tartozó felhasznált kérelemegységek. Az Azure Cosmos DB SDK-k használatával is elérheti ezt a fejlécet. A .NET SDK **RequestCharge** tulajdonsága a **ResourceResponse** objektum. A lekérdezésekhez az Azure Cosmos DB adatkezelő az Azure Portalon információkat kérelem díj végrehajtott lekérdezések. Megtudhatja, hogyan tehet szert, és a teljesítmény beállítása segítségével különböző többmodelles API-k [beállítása és az átviteli sebesség lekérdezése az Azure Cosmos DB](set-throughput.md) cikk.

Egy fenntartott adattovábbítási kapacitással, az alkalmazás számára szükséges mennyiségű becsléséről metódus jegyezze fel a kérés egységek használata után jellemző műveleteket futtat egy reprezentatív elem az alkalmazás által használt társított. Ezt követően becsülje meg a másodpercenként végrehajtásához várhatóan műveletek száma. Győződjön meg arról, is mérni, és tartalmazzák a tipikus lekérdezések és az Azure Cosmos DB parancsfájl használatát.

> [!NOTE]
> Ha rendelkezik, amely jelentősen méretét és az indexelt tulajdonságok tekintetében különböznek konfigurációelem-típusok, jegyezze fel a megfelelő művelet kérelem egységek használata után az egyes társított *típus* tipikus elem.
> 
> 

Ha például a lépéseket, előfordulhat, hogy az alábbiak:

1. Jegyezze fel a kérés egység díja (Beszúrás) létrehozása egy tipikus elemet. 
2. Jegyezze fel a kérés egységek használata után egy átlagos elem olvasása.
3. Jegyezze fel a kérés egységek használata után, az átlagos elem frissítése.
4. Jegyezze fel a kérés egységek használata után a cikk tipikus, gyakori lekérdezések.
5. Jegyezze fel a kérés egységek használata után bármilyen egyéni parancsfájlok (tárolt eljárások, eseményindítók, felhasználó által definiált függvények) az alkalmazás által használt.
6. A megadott becsült használatánál a műveletek másodpercenként futtatása várhatóan szükséges kérelemegység kiszámítása.

## <a name="a-request-unit-estimate-example"></a>Egy kérelem egység becslés példa
Vegye figyelembe a következő dokumentum, amely körülbelül 1 KB méretű:

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Dokumentumok vannak az Azure Cosmos DB minified, így a rendszer által számított – a dokumentum fenti mérete valamivel kisebb, mint 1 KB méretű.
> 
> 

Az alábbi táblázatban látható területének körülbelüli egységekre vonatkozó díjakon erre az elemre jellemző műveleteket. (A területének körülbelüli egységek használata után feltételezi, hogy a fiók konzisztenciaszint értéke **munkamenet** és az összes elem automatikusan indexelt.)

| Művelet | Kérelem egység díja |
| --- | --- |
| Elem létrehozása |~15 RU |
| Elem olvasása |~1 RU |
| Lekérdezés elem azonosítója |~2.5 RU |

Az alábbi táblázatban látható területének körülbelüli egységekre vonatkozó díjakon az alkalmazásban használt szokásos lekérdezések:

| Lekérdezés | Kérelem egység díja | visszaadott elemek száma |
| --- | --- | --- |
| Válassza ki az élelmiszer-azonosító alapján |~2.5 RU |1 |
| Válassza ki az élelmiszerek gyártó szerint |~7 RU |7 |
| Élelmiszer-csoport és a sorrend szerint súlyozással kiválasztása |~70 RU |100 |
| Válassza ki a felső 10 élelmiszerek élelmiszer-csoportban |~10 RU |10 |

> [!NOTE]
> Kérelem egységekre vonatkozó díjakon függ a visszaadott elemek száma.
> 
> 

Ezekkel az információkkal meg tudja becsülni a kérelem egységekre vonatkozó követelményeket, ehhez az alkalmazáshoz megadott műveleteket és lekérdezéseket, hogy másodpercenként várt száma:

| A művelet és lekérdezési | Becsült száma másodpercenként | Szükséges kérelemegység |
| --- | --- | --- |
| Elem létrehozása |10 |150 |
| Elem olvasása |100 |100 |
| Válassza ki az élelmiszerek gyártó szerint |25 |175 |
| Élelmiszer-csoport kiválasztása |10 |700 |
| Válassza ki a 10 leggyakoribb |15 |150 összesen |

Ebben az esetben várt egy átlagos átviteli követelményt 1,275 RU/s. Kerekítése a legközelebbi 100, akár Ön vennének igénybe 1,300 RU/s az az alkalmazás tároló (vagy tárolók készletét).

## <a id="RequestRateTooLarge"></a> Az Azure Cosmos DB szolgáltatás számára fenntartott átviteli sebesség korlátok túllépése
Egységek felhasználását kérés / másodperc gyakorisággal lesz kiértékelve. Olyan alkalmazások, amelyek túllépik a kiépített kérések egység mennyisége a kérések sebessége korlátozott a mindaddig, amíg az arány a kiosztott átviteli sebesség szint alá csökken. Ha egy rendszer sebessége korlátozott, a kiszolgáló előrelátó módon befejezi a kérelmet, `RequestRateTooLargeException` (HTTP-állapotkódot 429-es), és visszaadja a `x-ms-retry-after-ms` fejléc. A fejléc jelzi idő ezredmásodpercben, amely a felhasználónak várakoznia kell, mielőtt újra próbálkozna a kérelmet.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Ha használja a .NET Client SDK és a LINQ-lekérdezések, az esetek többségében nem kell foglalkozni a kivétel. A .NET Client SDK jelenlegi verziója implicit módon kezeli ezt a választ, tiszteletben tartja a kiszolgáló által megadott retry-after fejléccel, és próbáljon meg automatikusan újra a kérelmet. Kivéve, ha a fiók több ügyfélnek egyszerre használja, a következő újrapróbálkozás sikeres lesz.

Ha a kérések mennyisége felett összesítve működő egynél több ügyfél van, lehet, hogy elegendő az alapértelmezett újrapróbálkozási viselkedést és jelez az ügyfél egy `DocumentClientException` állapotú code 429-es az alkalmazáshoz. Ilyen esetben érdemes fontolja meg az újrapróbálkozási viselkedést és az alkalmazás hibakezelési rutinokat a logikai, vagy növelje az adatátviteli teljesítmény a tároló (vagy tárolók készletét).

## <a name="next-steps"></a>További lépések
 
- Ismerje meg, hogyan [beállítása és az átviteli sebesség lekérdezése az Azure Cosmos DB](set-throughput.md) Azure Portallal és az SDK-k használatával.
- Ismerje meg [teljesítmény és méretezhetőség tesztelése az Azure Cosmos DB](performance-testing.md).
- Az Azure Cosmos DB-adatbázisok fenntartott adattovábbítási kapacitással kapcsolatos további információkért lásd: [Azure Cosmos DB díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/) és [adatokat az Azure Cosmos DB particionálási](partition-data.md).
- Azure Cosmos DB kapcsolatos további információkért tekintse meg a [Azure Cosmos DB-dokumentációt](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png



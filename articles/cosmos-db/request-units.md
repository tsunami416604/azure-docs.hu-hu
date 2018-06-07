---
title: Egységek igényelhet, és megbecsülheti a teljesítmény - Azure Cosmos DB |} Microsoft Docs
description: További tudnivalók ismertetése, adja meg, és az Azure Cosmos Adatbázisba kérelem egység követelményeinek becslése.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 16ccda120aef0aa892bf365403f3f0bdc1209ca3
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823723"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB egység kérése

[Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) a Microsoft globálisan elosztott multimodel adatbázis. Az Azure Cosmos DB nem kell virtuális gépek kölcsönbe, szoftver központi telepítése vagy adatbázisok figyelése. Azure Cosmos DB üzemeltetett, és képes biztosítani a világszínvonalú rendelkezésre állását, teljesítményét és adatok védelme a Microsoft felső mérnökök folyamatosan figyeli. API-k egy szerkesztőprogramban, például használatával végezheti el az adatokat a [SQL](documentdb-introduction.md), [MongoDB](mongodb-introduction.md), és [tábla](table-introduction.md) API-kat, és a graph használatával a [Gremlin API](graph-introduction.md). Minden API-k összes natívan támogatott. 

Azure Cosmos DB pénzneme az *kérelem egység (RU)*. A kérelemegység nem kell olvasási/írási kapacitások vagy rendelkezés Processzor, memória és iops-érték. Azure Cosmos-adatbázis különböző API-k, amelyek különböző műveleteket támogatja, egyszerű közötti beolvassa és az összetett graph-lekérdezésekre. Mivel nem minden kérelemre egyenlő, kérelmek hozzá vannak rendelve a kérelem kiszolgálásához szükséges számítási mennyisége alapján kérelemegység normalizált mennyiségét. A száma kérelem művelet nem determinisztikus. Követheti, hogy bármely Azure Cosmos DB művelettel egy válasz fejléce felhasznált kérelem egységek száma. 

Kiszámítható teljesítmény elérése érdekében lefoglalni átviteli egységekben 100 RU/másodperc. Is [az átviteli sebesség meg kell becsülni](request-units.md#estimating-throughput-needs) az Azure Cosmos DB használatával [kérelem egység Számológép](https://www.documentdb.com/capacityplanner).

![Átviteli sebesség Számológép][5]

A cikk elolvasása után képes lesz a következő kérdések megválaszolásához:

* Mik azok a kérelemegység és az Azure Cosmos Adatbázisba kérelem díjak?
* Hogyan adjon meg egy tárolót vagy tárolók kérelem egység kapacitás az Azure Cosmos Adatbázisba?
* Hogyan becsléséhez, az alkalmazás kérelem egység van szüksége?
* Mi történik, ha szeretnék haladhatja meg a kérelem egység kapacitás egy tárolót vagy Azure Cosmos DB a tárolók?

Mivel az Azure Cosmos DB multimodel adatbázis, fontos vegye figyelembe, hogy ez a cikk összes adatmodellekben és API-k Azure Cosmos DB alkalmazandó. Ebben a cikkben az általános feltételek, például a *tároló* utal, hogy az általános gyűjteményben vagy graph és *elem* utal, hogy az általános egy tábla, a dokumentumot, a csomópont vagy az entitás.

## <a name="request-units-and-request-charges"></a>Kérelemegység és kérelem díjak

Azure Cosmos-adatbázis gyors, az alkalmazás átviteli igényeinek kielégítéséhez erőforrások lefoglalásával kiszámítható teljesítményt nyújt. Alkalmazás terhelés és hozzáférési minták változnak az idők. Azure Cosmos-adatbázis segítségével könnyen növeléséhez vagy csökkentéséhez fenntartott átviteli sebesség érhető el, hogy az alkalmazást.

Az Azure Cosmos DB fenntartott átviteli sebességet másodpercenkénti feldolgozási kérés egységben van megadva. Az eltolásokat tekintheti kérelemegység átviteli pénznemként. Garantált kérelemegység / másodperces időközönként az alkalmazás elérhető legyen több, akkor foglalhat le. Azure Cosmos DB, például egy dokumentum írása lévő egyes műveletek, a lekérdezés végrehajtása, és a dokumentumok frissítése, fel, Processzor, memória és iops-érték. Ez azt jelenti, hogy minden egyes művelet azt eredményezi azok háromszorosa kérelem járnak, amely kérelemegység van kifejezve. Miután megismerte a tényezőt jelent a kérelem egység díjakat és az alkalmazás átviteli követelmények, a lehető leghatékonyabban költséggel futtathatja az alkalmazást. 

Első lépések segítségével Azure Cosmos DB Programvezető Andrew Liu kérelem egység az alábbi videó ismerteti: <br /><br />

> [!VIDEO https://www.youtube.com/embed/stk5WSp5uX0]
> 
> 

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Átviteli sebesség elkülönítési globálisan elosztott adatbázisok

Ha több régióba replikálja az adatbázist, Azure Cosmos DB annak érdekében, hogy egy régióban kérelem unit-használaton nincs hatással a kérelem egy másik régióban unit-használaton átviteli elkülönítést is biztosít. Például ha adatírás egy régióhoz és egy másik régióban adatokat olvasni, a kérelem A régióban az írási művelet végrehajtásához használt mértékegység nem visz elhagyja a kérelemegység az olvasási művelet régióban b kérelem használt egységek makró nem felosztása ss a régiókban, ahol az adatbázis telepítése után. Minden egyes régió, ahol az adatbázis replikálódik rendelkezik kiépített kérelem egységek teljes száma. Globális replikációval kapcsolatos további információkért lásd: [miként ossza el a globális adatok Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Kérelem egység kapcsolatos szempontok
Kiépítését kérelem egységek számának becslése, fontos figyelembe venni a következő változókat:

* **Konfigurációelem-méret**. Mérete nő, olvasni vagy írni felhasznált kérelem egységek számát is növekszik.
* **Konfigurációelem-tulajdonság száma**. Feltéve, hogy alapértelmezett indexelő levő összes tulajdonság, írni a dokumentumot, a csomópont vagy a szervezet növelését a a tulajdonság számának növekedése használt mértékegységet.
* **Adatkonzisztencia**. Például az erős, vagy a kötött elavulási konzisztencia adatmodellekben használata esetén további kérelemegység felhasznált elemek olvasására.
* **Indexelt tulajdonságok**. Egy index házirendet minden egyes tároló meghatározza, hogy mely tulajdonságok indexelt alapértelmezés szerint. Az indexelt tulajdonságok számának korlátozása vagy engedélyezése a Lusta indexelő csökkentése érdekében a kérelem egység felhasználás az írási műveletek.
* **A dokumentum indexelő**. Alapértelmezés szerint minden elem automatikusan indexelt. Ha úgy dönt, hogy az elemek egy része nem index felhasznált kevesebb kérelemegység.
* **Lekérdezési minták**. Lekérdezés összetettsége van hatással, hány kérelemegység művelet végrehajtásánál. Lekérdezés száma az eredménye, predikátumok, a predikátum jellege, felhasználó által definiált függvények száma, a forrás adatok méretét a száma, és minden leképezések hatással lekérdezési műveletek költségét.
* **Parancsfájl-használati**. Csakúgy, mint a lekérdezéseket, a tárolt eljárások és eseményindítók végrehajtott műveletek összetettsége alapján kérelemegység felhasználni. Az alkalmazás elkészítéséhez, vizsgálja meg jobb megértése érdekében hogyan minden művelet feldolgozó kérelmet egység kapacitás kérelemfejléc kell fizetni.

## <a name="estimating-throughput-needs"></a>Átviteli sebesség igények becslése
A kérelem egység mérőszáma normalizált kérelemfeldolgozáshoz költség. Egy egyetlen kérelem egységet jelöli a feldolgozási kapacitás (keresztül self link vagy azonosítója) olvasására, amely 10 egyedi tulajdonság értékével (kivéve a rendszer tulajdonságai) áll egy 1 KB-os elem szükséges. Egy kérelem létrehozása (insert), cseréje vagy törlése azonos elem használ fel a szolgáltatásból további feldolgozás és ezáltal a további kérelemegység igényel. 

> [!NOTE]
> Az alapkonfiguráció 1 kérelem egység egy 1 KB-os elem által self link egyszerű GET vagy a cikk azonosítója felel meg.
> 
> 

Például itt található táblázatban láthatók, hány kérelemegység kiépítését elemek három különböző méretű (1 KB, 4 KB-os és 64 KB-os) és a két különböző teljesítményszintek (500 olvasás/másodperc + 100 írási műveletek másodpercenkénti száma és 500 olvasás/másodperc + 500 írás/másodperc). Ebben a példában az adatok konzisztenciájának értéke **munkamenet**, és az indexelési házirendet **nincs**.

| Elem mérete | Olvasási/másodperc | Írási műveletek másodpercenkénti száma | Kérelemegységek
| --- | --- | --- | --- |
| 1 KB | 500 | 100 | (500 * 1) + (100 * 5) = 1000 RU/mp
| 1 KB | 500 | 500 | (500 * 1) + (500 * 5) = 3000 RU/mp
| 4 KB | 500 | 100 | (500 * 1,3) + (100 * 7) = 1,350 RU/mp
| 4 KB | 500 | 500 | (500 * 1,3) + (500 * 7) = 4,150 RU/mp
| 64 KB | 500 | 100 | (500 * 10) + (100 * 48) = 9,800 RU/mp
| 64 KB | 500 | 500 | (500 * 10) + (500 * 48) = 29,000 RU/mp


### <a name="use-the-request-unit-calculator"></a>A kérelem egység Számológép használata
Segítségre van szüksége az átviteli sebesség becsléseket finomhangolásához, használhatja a webalapú [kérelem egység Számológép](https://www.documentdb.com/capacityplanner). A Számológép becsült segíthet a kérelem egységekre vonatkozó követelményeket a tipikus műveleteket, köztük:

* Elem hoz létre (írás)
* Elem beolvasása
* Elem törlése
* Elem frissítések

Az eszköz is támogatja az adattárolási igények kielégítésére az Ön által biztosított minta elemek becslése.

Az eszköz használatához:

1. Töltsön fel egy vagy több jellemző elemet (például egy minta-JSON-dokumentumot).
   
    ![Töltse fel a kérelem egység Számológép elemek][2]
2. Az adatok tárolási követelményeinek becslése, adja meg a teljes elemek száma (például a dokumentumok, sort vagy csúcsban), amelyek várhatóan tárolni.
3. Adja meg a létrehozási, olvasási, frissítési és törlési műveletekhez szükséges (a másodpercenként alapján). A kérelem egység díjak elem frissítési műveletek becsléséhez, töltse fel az 1. lépés, amely jellemző mező frissítések minta elem másolatának. Például, ha elem frissítések általában két tulajdonságainak módosítása nevű *lastLogin* és *userVisits*, minta másolása, és két tulajdonságokat értéket, majd töltse fel a másolt elem.
   
    ![Adja meg a kérelem egység Számológép átviteli követelmények][3]
4. Válassza ki **Calculate**, és vizsgálja meg az eredményeket.
   
    ![Kérelem egység Számológép eredményei][4]

> [!NOTE]
> Ha konfigurációelem-típusok jelentősen méretét és az indexelt tulajdonságok számának tekintetében különböznek, töltse fel az egyes minta *típus* tipikus elemet az eszközre, és számítani, az eredményeket.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Használja az Azure Cosmos DB kérelem kell fizetni válaszfejléc
Minden válasz az Azure Cosmos DB szolgáltatástól tartalmaz egy egyéni fejlécet (`x-ms-request-charge`), amely tartalmazza egy adott kérelem felhasznált kérelemegység. Ezt a fejlécet az Azure Cosmos DB SDK-k használatával is elérhető. A .NET SDK **RequestCharge** tulajdonsága a **ResourceResponse** objektum. A lekérdezések az Azure Cosmos DB adatkezelő az Azure portálon információival kérelem kell fizetni végrehajtott lekérdezések számára.

Egy fenntartott átviteli sebességet, az alkalmazás által igényelt mennyisége becslése módja rögzítése a szokásos tevékenységek futtatott egy reprezentatív elemet, az alkalmazás által használt társított kérelem egység kell fizetni. Ezt követően hajtsa végre a másodpercenként várhatóan műveletek számának becslése. Győződjön meg arról, is mérése és tipikus lekérdezések és Azure Cosmos DB parancsfájl használata.

> [!NOTE]
> Ha konfigurációelem-típusok jelentősen méretét és az indexelt tulajdonságok számának tekintetében különböznek, jegyezze fel a megfelelő műveletet kérelem egység kell fizetni társított minden egyes *típus* jellemző elem.
> 
> 

Például előfordulhat, hogy lépései a következők:

1. Jegyezze fel a kérelem egység költség (Beszúrás) létrehozásához egy jellemző elemet. 
2. Jegyezze fel a kérelem egység kell fizetni egy tipikus elem olvasása.
3. Jegyezze fel a kérelem egység felelős egy tipikus elem frissítése.
4. Jegyezze fel a kérelem egység kell fizetni tipikus, közös elem lekérdezések.
5. Jegyezze fel a kérelem egység kell fizetni az egyéni parancsfájlokhoz (tárolt eljárások, eseményindítók, felhasználó által definiált függvények) az alkalmazás által használt.
6. A megadott műveletek másodpercenkénti futtatásához várhatóan becsült száma szükséges kérelemegység kiszámításához.

## <a name="a-request-unit-estimate-example"></a>A kérelem egység becsült – példa
Vegye figyelembe a következő dokumentumot, amely körülbelül 1 KB méretű:

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
> Dokumentumok, a rendszer által számított – a dokumentum fenti mérete valamivel kisebb, mint 1 KB minified az Azure Cosmos Adatbázisba, a rendszer.
> 
> 

A következő táblázatban hozzávetőleges kérelem egység díjak ezt az elemet a tipikus műveleteihez. (A hozzávetőleges kérelem egység kell fizetni feltételezi, hogy a fiók konzisztencia szintje **munkamenet** és minden elem automatikusan indexelt.)

| Művelet | Egységköltség kérése |
| --- | --- |
| Elem létrehozása |~15 RU |
| Elem olvasása |~1 RU |
| Lekérdezés elem azonosítója |~2.5 RU |

Az alábbi táblázat hozzávetőleges kérést az alkalmazásban használt szokásos lekérdezések egység díjak:

| Lekérdezés | Egységköltség kérése | visszaadott elemek száma |
| --- | --- | --- |
| Válassza ki a étele-azonosító szerint |~2.5 RU |1 |
| Válassza ki a gyártó által élelmiszerek |~7 RU |7 |
| Jelöljön ki étele csoport és egy rendezési súlyozással |~70 RU |100 |
| Válassza ki a felső 10 élelmiszerek étele csoportban |~10 RU |10 |

> [!NOTE]
> A visszaadott elemek száma függően változhat a kérelem egység díjakat.
> 
> 

Az információ megbecsülheti a kérelem egységekre vonatkozó követelményeket az alkalmazáshoz, műveletek és a lekérdezések másodpercenként várt száma:

| A művelet/lekérdezés | Becsült száma másodpercenként | Szükséges kérelemegység |
| --- | --- | --- |
| Elem létrehozása |10 |150 |
| Elem olvasása |100 |100 |
| Válassza ki a gyártó által élelmiszerek |25 |175 |
| Válassza ki a étele csoport szerint |10 |700 |
| Válassza ki a felső 10 |15 |150 összesen |

Ebben az esetben várt 1,275 egy átlagos átviteli sebességgel követelményt RU/másodperc. Kerekítése a legközelebbi 100, akár 1,300 RU/mp a az alkalmazás tárolóhoz (vagy tárolók készlete) lenne kiépítéséhez.

## <a id="RequestRateTooLarge"></a> Az Azure Cosmos Adatbázisba meghaladó fenntartott átviteli sebességének korlátai
Kérelem egység fogyasztás ki lesz értékelve másodpercenként sebességgel. Az olyan alkalmazások, amelyek mérete meghaladja a kiépített kérelmek egység aránya kérelmek csak sebessége korlátozott mértékben a létesített átviteli sebesség szint alá süllyed. Amikor egy kérelem sebessége korlátozott, a kiszolgáló megelőző jelleggel karakterlánccal végződik-e a kérelem `RequestRateTooLargeException` (HTTP-állapotkód: 429), és adja vissza a `x-ms-retry-after-ms` fejléc. A fejléc azt az idő, ezredmásodpercben, hogy a felhasználónak meg kell várnia, mielőtt megpróbálná megismételni a kérelmet.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

A .NET SDK-ügyfél és a LINQ-lekérdezések használatakor, az esetek többségében soha nem kell a kivétel kezelése. A .NET SDK-ügyfél aktuális verziója implicit módon ki ezt a választ, tiszteletben tartja a kiszolgáló által megadott újrapróbálkozási után fejlécet, és automatikusan újrapróbálkozik a kérelmet. Kivéve, ha a fiók több ügyfélnek egyszerre használja, a következő újrapróbálkozási sikeres lesz.

Ha egynél több olyan ügyfél, a kérelmek aránya felett összesítve működő, lehet, hogy az alapértelmezett újrapróbálkozásra elegendő és az ügyfél jelez a `DocumentClientException` állapotú code 429 az alkalmazáshoz. Ilyen esetben érdemes figyelembe venni az újrapróbálási viselkedése és az alkalmazás hibakezelő rutinok a logikai vagy a tároló (vagy tárolók) kiosztott átviteli sebesség növelése.

## <a name="next-steps"></a>További lépések
 
- Megtudhatja, hogyan [beállítása és átviteli beolvasása az Azure Cosmos Adatbázisba](set-throughput.md) Azure-portál és az SDK-k használatával.
- További tudnivalók [teljesítmény és méretezhetőség Azure Cosmos DB tesztelték](performance-testing.md).
- Fenntartott átviteli sebességet Azure Cosmos Database adatbázisokkal kapcsolatos további információkért lásd: [Azure Cosmos DB árképzési](https://azure.microsoft.com/pricing/details/cosmos-db/) és [adatokat az Adatbázisba az Azure Cosmos particionálás](partition-data.md).
- Azure Cosmos DB kapcsolatos további tudnivalókért tekintse meg a [Azure Cosmos DB dokumentáció](https://azure.microsoft.com/documentation/services/cosmos-db/). 

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png


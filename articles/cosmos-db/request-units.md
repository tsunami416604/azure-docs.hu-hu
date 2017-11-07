---
title: "Kérelem egységek & átviteli - Azure Cosmos DB becslése |} Microsoft Docs"
description: "További tudnivalók ismertetése, adja meg, és az Azure Cosmos Adatbázisba kérelem egység követelményeinek becslése."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: mimig
ms.openlocfilehash: fc544a776293e94114d8c07d89df588a17aa1962
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="request-units-in-azure-cosmos-db"></a>Az Azure Cosmos DB egység kérése
Most már hozzáférhető: Azure Cosmos DB [kérelem egység Számológép](https://www.documentdb.com/capacityplanner). További információ: [megbecsülheti, az átviteli sebesség kell](request-units.md#estimating-throughput-needs).

![Átviteli sebesség Számológép][5]

## <a name="introduction"></a>Bevezetés
[Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) Microsoft globálisan elosztott több modellre adatbázis. Az Azure Cosmos DB nem kell virtuális gépek kölcsönbe, szoftver központi telepítése vagy adatbázisok figyelése. Azure Cosmos DB üzemeltetett, és képes biztosítani a világ osztály rendelkezésre állását, teljesítményét és adatok védelme a Microsoft felső mérnökök folyamatosan figyeli. Az adatok egy szerkesztőprogramban, például az SQL keresztül API-k használatával végezheti el a [DocumentDB API](documentdb-introduction.md), MongoDB API-k, [tábla API](table-introduction.md), és Gremlin keresztül a [Graph API](graph-introduction.md) -összes rendszer natív módon támogatott. Azure Cosmos DB pénzneme kérelem egység (RU). A RUs nem kell olvasási/írási kapacitások vagy rendelkezés Processzor, memória és iops-érték.

Azure Cosmos DB alkalmazásprogramozási támogatja a különböző műveletekkel, egyszerű olvasási műveletek közötti, és összetett graph lekérdezések írja. Mivel nem minden kérelemre egyenlő, hozzárendeli egy normalizált mennyisége **egységek kérelem** a kérelem kiszolgálásához szükséges számítási mennyisége alapján. A száma kérelem művelet nem determinisztikus, és nyomon követheti a válasz fejléce Azure Cosmos DB bármely művelet által felhasznált kérelem egységek száma. 

Kiszámítható teljesítmény elérése érdekében kell lefoglalni átviteli egységekben 100 RU/másodperc. 

A cikk elolvasása után képes lesz a következő kérdések megválaszolásához:  

* Mik azok a egységek kérése és díjak kérelem?
* Hogyan adja meg a kérelem egység kapacitás gyűjtemény?
* Hogyan becsléséhez, az alkalmazás kérelem egység van szüksége?
* Mi történik, ha szeretnék haladhatja meg a kérelem egység kapacitás gyűjtemény?

Mivel Azure Cosmos DB több modellre adatbázis, fontos megjegyezni, hogy ez a cikk egy gyűjtemény/dokumentum API-t egy grafikonon/csomópont egy grafikonon API és a tábla API egy tábla/entitás dokumentum hivatkozik. Ez a cikk hivatkozik egy gyűjteményt, a graph vagy a táblázat tárolója, fogalma és egy dokumentumot, csomópont vagy entitás elemet.

## <a name="request-units-and-request-charges"></a>Kérelemegység és kérelem díjak
Azure Cosmos-adatbázis által gyors és kiszámítható teljesítményt nyújt *foglalása* erőforrások teljesíteni kell az alkalmazás átviteli sebességére.  Alkalmazás betölteni, és a hozzáférési minták módosítása adott idő alatt, mert az Azure Cosmos DB lehetővé teszi könnyen növeléséhez vagy csökkentéséhez fenntartott átviteli sebesség érhető el, hogy az alkalmazást.

Az Azure Cosmos DB fenntartott átviteli kérelem egység / másodperc feldolgozása tekintetében van megadva. Az eltolásokat tekintheti kérelemegység átviteli pénznemként, amellyel meg *lefoglalni* az alkalmazás számára elérhető garantált kérelemegység másodpercenként egy mennyisége.  Minden Azure Cosmos DB - dokumentum írása, frissítése egy dokumentumot a lekérdezés végrehajtása - műveletet igényel, Processzor, memória és iops-érték.  Ez azt jelenti, hogy minden egyes művelet azt eredményezi azok háromszorosa egy *kell fizetni kérelem*, amelyhez van megadva *egységek kérelem*.  A kérelem egység díjak, az alkalmazás átviteli követelményeket, valamint befolyásoló tényezők megértéséhez lehetővé teszi az alkalmazás futtatása a lehető leghatékonyabban költség. A lekérdezés explorer egyben a core lekérdezés teszteléséhez csodálatos eszköz.

Azt javasoljuk, hogy Kezdésként tekintse meg az alábbi videót, ahol Aravind Ramachandran kérelemegység és a kiszámítható teljesítmény Azure Cosmos DB mutatja.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>Adja meg a kérelem egység kapacitás az Azure Cosmos DB
Egy új gyűjteményt, táblázat vagy graph indításakor, akkor az itt megadott kérelemegység (RU / másodperc) másodpercenként kívánt foglalt. A létesített átviteli sebesség alapján, Azure Cosmos DB foglal le, a gyűjtemény üzemeltetésére fizikai partíciók és elágazást/rebalances adatok között partíciók növekedésével azt.

Azure Cosmos-adatbázis egy partíciókulcsot kell határozni, ha a gyűjtemény kiépítése 2500 kérést egységek vagy újabb szükséges. A partíciós kulcs is szükség van a gyűjtemény átviteli sebességét túl 2500 kérést egységek méretezése a jövőben. Ezért erősen ajánlott konfigurálása egy [partíciókulcs](partition-data.md) függetlenül a kezdeti átviteli tárolója létrehozásakor. Az adatok kell kell-e osztani több partíciót, szükség egy partíciós kulcs, amely rendelkezik egy nagy számosságot (több millió különböző értékeket 100) kiválasztásához. A partíciós kulcs számos különböző értékekkel kiválasztásával, győződjön meg arról, hogy a gyűjtemény/tábla/graph és a kérelmek is méretezhető egységesen Azure Cosmos DB. 

> [!NOTE]
> A partíciós kulcs, a logikai határ, és nem egy fizikai egy. Emiatt nem kell külön partíciókulcs-értékek számának korlátozása. Valójában célszerűbb értékűeknek több partíciós kulcs kisebb, mint Azure Cosmos DB rendelkezik további terheléselosztási beállításai.

Íme egy kódrészletet 3000 kérelem egység / második .NET SDK használatával a gyűjtemény létrehozásához:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Azure Cosmos-adatbázis működik, a teljesítmény a Foglalás modellt. Ez azt jelenti, hogy kell fizetni az átviteli sebesség *fenntartott*, függetlenül attól, hogy átviteli mekkora aktívan *használt*. Az alkalmazás által könnyen méretezheti mennyisége fel és le terhelés, az adatok és a használati minták módosítása fenntartott RUs SDK-k, vagy használja a [Azure Portal](https://portal.azure.com).

Minden gyűjtemény/tábla/graph hozzá vannak rendelve egy `Offer` Azure Cosmos DB, amelynek metaadatait a létesített átviteli sebesség erőforrás. A megfelelő ajánlat erőforrás egy tároló keresése, akkor új átviteli értékű frissítése módosíthatja a kiosztott átviteli sebesség. Íme egy kódrészletet a módosítása a gyűjtemény átviteli 5 000 kérelemegység / második .NET SDK használatával:

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Ha megváltoztatja az átviteli sebesség, nincs hatással a következő rendelkezésre állási, a tároló. Az új fenntartott átviteli sebességet általában hatékony alkalmazásra, az új átviteli másodpercen belül.

## <a name="request-unit-considerations"></a>Kérelem egység kapcsolatos szempontok
Az Azure Cosmos DB tároló foglalása a kérelem egységek számának becslése, fontos figyelembe venni a következő változókat:

* **Konfigurációelem-méret**. Mivel mérete nő, olvasására vagy írására is növeli az adatok használt mértékegységet.
* **Konfigurációelem-tulajdonság száma**. Feltéve, hogy alapértelmezett indexelő levő összes tulajdonság írása a dokumentum/csomópont/entitás növelését a a tulajdonság számának növekedése használt mértékegységet.
* **Adatkonzisztencia**. Az erős, vagy a kötött elavulási konzisztencia szintek használata esetén további egységek felhasznált elemek olvasására.
* **Indexelt tulajdonságok**. Egy index házirendet minden egyes tároló meghatározza, hogy mely tulajdonságok indexelt alapértelmezés szerint. Az indexelt tulajdonságok számának korlátozása vagy engedélyezése a Lusta indexelő csökkentése érdekében a kérelem egység fogyasztás.
* **A dokumentum indexelő**. Alapértelmezés szerint minden elem automatikusan indexelt. Ha úgy dönt, hogy nem a elemek egy része az index felhasznált kevesebb kérelemegység.
* **Lekérdezési minták**. A lekérdezés összetettsége hatással van kérelem egységek művelet végrehajtásánál. A predikátum száma, a predikátum, leképezések, felhasználó által megadott függvények száma és mérete a forrás adatkészlet összes jellege befolyásolják a lekérdezési műveletek költségét.
* **Parancsfájl-használati**.  Csakúgy, mint a lekérdezéseket, a tárolt eljárások és eseményindítók végrehajtott műveletek összetettsége alapján kérelemegység felhasználni. Az alkalmazás elkészítéséhez, vizsgálja meg jobb megértése érdekében hogyan egyes műveletek nem használ-e kérelem egység kapacitás kérelemfejléc kell fizetni.

## <a name="estimating-throughput-needs"></a>Átviteli sebesség igények becslése
A kérelem egység mérőszáma normalizált kérelemfeldolgozáshoz költség. Egy egyetlen kérelem egységet jelöli a feldolgozási kapacitás egy egyetlen 1 KB cikk álló (kivéve a rendszer tulajdonságai) 10 egyedi tulajdonságértékek (keresztül self link vagy azonosítója) olvasásához szükséges. A kérelem létrehozása (insert), cseréje vagy azonos elem törlése fogyaszt, több folyamatot, a szolgáltatásból, és ezáltal több kérelemegység.   

> [!NOTE]
> Az alapkonfiguráció 1 kérelem egység egy 1 KB-os elem egy egyszerű GET self link vagy a cikk azonosítója felel meg.
> 
> 

Például egy táblázat következik hány kérelemegység kiépítését, három különböző elem mérete (1 KB, 4 KB-os és 64 KB-os) és két különböző teljesítményszintek az itt található (500 olvasás/másodperc + 100 írási műveletek másodpercenkénti száma és 500 olvasás/másodperc + 500 írás/másodperc). Az adatok konzisztenciájának a munkamenet lett konfigurálva, és az indexelési házirendet értékre lett beállítva.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Elem mérete</strong></p></td>
            <td valign="top"><p><strong>Olvasási/másodperc</strong></p></td>
            <td valign="top"><p><strong>Írási műveletek másodpercenkénti száma</strong></p></td>
            <td valign="top"><p><strong>Kérelemegységek</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1000 RU/mp</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3000 RU/mp</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB-OS</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1,350 RU/mp</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB-OS</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4,150 RU/mp</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800 RU/mp</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000 RU/mp</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>A kérelem egység Számológép használata
Az átviteli sebesség becsléseket finomhangolásához munkában, nincs webalapú [kérelem egység Számológép](https://www.documentdb.com/capacityplanner) használatával megbecsülheti a kérelem egységekre vonatkozó követelményeket a tipikus műveleteket, köztük:

* Elem hoz létre (írás)
* Elem beolvasása
* Elem törlése
* Elem frissítések

Az eszköz az adattárolási igények kielégítésére megadta minta cikkek alapján megbecsülheti támogatását is tartalmazza.

Az eszköz használata egyszerű:

1. Töltsön fel egy vagy több jellemző elemet.
   
    ![Töltse fel a kérelem egység Számológép elemek][2]
2. Adatok tárolási követelményeinek becslése, adja meg a várhatóan tárolni elemek összesített száma.
3. Adja meg az elemek száma létrehozása, olvasása, frissítése és törlési műveletek (a másodpercenként alapján) van szüksége. A kérelem egység díjak elem frissítési műveletek becsléséhez, töltse fel az 1. lépés a fenti tipikus mező frissítéseket tartalmazó minta elem másolatának.  Például ha elem frissítések általában két tulajdonságainak módosítása lastLogin és userVisits nevű majd egyszerűen a minta elem másolása, e két tulajdonság értéket, és töltse fel a másolt elem.
   
    ![Adja meg a kérelem egység Számológép átviteli követelmények][3]
4. Kattintson a kiszámításához, és vizsgálja meg az eredményeket.
   
    ![Kérelem egység Számológép eredményei][4]

> [!NOTE]
> Ha méretét és az indexelt tulajdonságok száma jelentősen eltérő típusú elemekre, majd töltse fel az egyes minta *típus* tipikus az eszköz elemet, és majd kiszámítja az eredményeket.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Használja az Azure Cosmos DB kérelem kell fizetni válaszfejléc
Minden válasz az Azure Cosmos DB szolgáltatástól tartalmaz egy egyéni fejlécet (`x-ms-request-charge`), amely tartalmazza a kéréshez használt kérelemegység. Ezt a fejlécet az Azure Cosmos DB SDK-k keresztül is érhető el. A .NET SDK RequestCharge a ResourceResponse objektum olyan osztályát.  A lekérdezések az Azure Cosmos DB lekérdezéskezelő az Azure portálon információival kérelem kell fizetni végrehajtott lekérdezések számára.

![A lekérdezés Explorer RU díjak vizsgálata][1]

Ennek a szem előtt, megbecsülheti a fenntartott átviteli sebességet, az alkalmazás által igényelt mérete, jegyezze fel a kérelem egység kell fizetni társított tipikus műveleteket futtatott egy reprezentatív elem, amelyet az alkalmazás, és ezután műveletek számának becslése egy módszert, amelyek várhatóan másodpercenként végez.  Győződjön meg arról, mérése és tipikus lekérdezések és Azure Cosmos DB parancsfájl használata is tartalmazza.

> [!NOTE]
> Ha konfigurációelem-típusok jelentősen méretét és az indexelt tulajdonságok számának tekintetében különböznek, majd jegyezze fel a megfelelő műveletet kérelem egység kell fizetni társított minden egyes *típus* jellemző elem.
> 
> 

Példa:

1. Jegyezze fel a kérelem egység költség (Beszúrás) létrehozásához egy jellemző elemet. 
2. Jegyezze fel a kérelem egység kell fizetni egy tipikus elem olvasása.
3. Jegyezze fel a kérelem egység felelős egy tipikus elem frissítése.
4. Jegyezze fel a kérelem egység kell fizetni tipikus, közös elem lekérdezések.
5. Jegyezze fel a kérelem egység ingyenesen elérhető bármely egyéni parancsfájlok (tárolt eljárások, eseményindítók, felhasználó által definiált függvények) használja ki az alkalmazást
6. A megadott műveletek másodpercenkénti futtatásához várhatóan becsült száma szükséges kérelemegység kiszámításához.

### <a id="GetLastRequestStatistics"></a>A MongoDB GetLastRequestStatistics parancshoz használható API
Mongodb-protokolltámogatással API támogatja egy egyéni parancs *getLastRequestStatistics*, a kérelem kell fizetni a megadott műveletek beolvasásakor.

Például a Mongo rendszerhéj hajtható végre a kérelem díjat ellenőrizni szeretné a műveletet.
```
> db.sample.find()
```

Ezután hajtsa végre a parancsot *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Ennek a szem előtt, megbecsülheti a fenntartott átviteli sebességet, az alkalmazás által igényelt mérete, jegyezze fel a kérelem egység kell fizetni társított tipikus műveleteket futtatott egy reprezentatív elem, amelyet az alkalmazás, és ezután műveletek számának becslése egy módszert, amelyek várhatóan másodpercenként végez.

> [!NOTE]
> Ha méretét és az indexelt tulajdonságok száma jelentősen eltérő típusú elemekre, majd jegyezze fel a megfelelő műveletet kérelem egység kell fizetni társított minden egyes *típus* jellemző elem.
> 
> 

## <a name="use-api-for-mongodbs-portal-metrics"></a>MongoDB a portál metrikáihoz API használata
A legegyszerűbben úgy beszerezni kérelem jó becslése egység költségek az API-MongoDB-adatbázist, hogy használja a [Azure-portálon](https://portal.azure.com) metrikákat. Az a *kérelem* és *kérelem kell fizetni* diagramokat, a kérelem egységek minden művelet nem használ-e, és hány kérelemegység használják ki egy másik viszonyítva felmérését kaphat.

![API-t a MongoDB portál metrikák][6]

## <a name="a-request-unit-estimation-example"></a>A kérelem egység becslés – példa
Vegye figyelembe a következő ~ 1 KB-os dokumentum:

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
> Dokumentumok Azure Cosmos DB, a rendszer minified szűrést, a rendszer a dokumentum fenti mérete valamivel kisebb, mint 1 KB.
> 
> 

Az alábbi táblázat hozzávetőleges kérelem egység költségekkel ezt az elemet (a hozzávetőleges kérelem egység kell fizetni feltételezi, hogy a fiók konzisztencia szintje "Munkamenet", és minden elem automatikusan indexelt) jellemző műveleteket:

| Művelet | Egységköltség kérése |
| --- | --- |
| Konfigurációelem létrehozása |~ 15 RU |
| Elem olvasása |~ 1 RU |
| Lekérdezés elem azonosítója |~2.5 RU |

Emellett az alábbi táblázatban hozzávetőleges kérést az alkalmazásban használt szokásos lekérdezések egység díjak:

| Lekérdezés | Egységköltség kérése | Visszaadott elemek száma |
| --- | --- | --- |
| Válassza ki a étele-azonosító szerint |~2.5 RU |1 |
| Válassza ki a gyártó által élelmiszerek |~ 7 RU |7 |
| Jelöljön ki étele csoport és egy rendezési súlyozással |~ 70 RU |100 |
| Válassza ki a felső 10 élelmiszerek étele csoportban |~ 10 RU |10 |

> [!NOTE]
> RU díjak visszaküldött elemek száma függően változhat.
> 
> 

Az információ megbecsülheti a RU követelmények az alkalmazáshoz, műveletek és a lekérdezések másodpercenként várt száma:

| A művelet/lekérdezés | Becsült száma másodpercenként | Szükséges RUs |
| --- | --- | --- |
| Konfigurációelem létrehozása |10 |150 |
| Elem olvasása |100 |100 |
| Válassza ki a gyártó által élelmiszerek |25 |175 |
| Válassza ki a étele csoport szerint |10 |700 |
| Válassza ki a felső 10 |15 |150 összesen |

Ebben az esetben egy 1,275 RU/s átlagos átviteli sebességgel követelmény várt.  Kerekítése a legközelebbi 100, akár volna 1300 RU/mp az alkalmazás gyűjtemény kiépítése.

## <a id="RequestRateTooLarge"></a>Az Azure Cosmos Adatbázisba meghaladó fenntartott átviteli sebességének korlátai
Visszahívása, hogy kérelem egység fogyasztás kiértékelhető legyen másodpercenkénti, ha a keret üres. Az olyan alkalmazások, amelyek mérete meghaladja a kiépített kérelmek egység aránya a tárolóhoz gyűjteményhez kérelmek szabályozott amíg sebessége a fenntartott szint alá süllyed. A szabályozási következik be, amikor a kiszolgáló megelőző jelleggel véget ér RequestRateTooLargeException (HTTP-állapotkód: 429) a kérelmet, és adja vissza a x-ms-újrapróbálkozási-után-ms-fejléc jelző idő ezredmásodpercben, amely a felhasználó megoldódhat előtt meg kell várnia a a kérést.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Ha a .NET SDK-ügyfél és a LINQ-lekérdezések, majd a legtöbbször ennek soha nem kell a .NET SDK-ügyfél aktuális verziója implicit módon ezt a választ ki, ez a kivétel kezelésére használ, tiszteletben tartja a kiszolgáló által megadott újrapróbálkozási után fejlécet, és újrapróbálkozik a a kérést. Kivéve, ha a fiók több ügyfélnek egyszerre használja, a következő újrapróbálkozási sikeres lesz.

Ha egynél több ügyfél összesítve felett a kérelmek aránya működő esetleg nem elegendő az alapértelmezett újrapróbálási viselkedése, és az ügyfél egy DocumentClientException állapotkóddal 429 kivételhibát az alkalmazáshoz. Például ez esetben érdemes újrapróbálási viselkedése és rutinok kezelése vagy a tároló a fenntartott átviteli sebesség növelése az alkalmazás hibás programot.

## <a id="RequestRateTooLargeAPIforMongoDB"></a>Mongodb-protokolltámogatással meghaladja a fenntartott átviteli sebességének korlátai API-ban.
Alkalmazások, amelyek mérete meghaladja a kiosztott kérelemegység gyűjtemény halmozódni fog, amíg a sebesség esik fenntartott szint alatt. A szabályozási esetén a háttér megelőző jelleggel véget ér a kérelmet egy *16500* hibakód - *túl sok kérelem*. Alapértelmezés szerint API-t a MongoDB automatikusan megpróbálja legfeljebb 10-szer visszatérése előtt egy *túl sok kérelem* hibakód. Ha sok kap *túl sok kérelem* hibakódok, akkor fontolja meg vagy hozzáadását újrapróbálási viselkedése a az alkalmazás hibakezelési rutinok vagy [a gyűjteményafenntartottátvitelisebességnövelése](set-throughput.md).

## <a name="next-steps"></a>Következő lépések
További információt az Azure Cosmos DB adatbázisok fenntartott átviteli sebességet, ismerheti meg ezeket az erőforrásokat:

* [Azure-beli árakról Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Az Azure Cosmos Adatbázisba az adatok particionálása](partition-data.md)

Azure Cosmos DB kapcsolatos további tudnivalókért tekintse meg az Azure Cosmos DB [dokumentáció](https://azure.microsoft.com/documentation/services/cosmos-db/). 

Első lépésként a méretezés és teljesítmény Azure Cosmos DB tesztelést, lásd: [teljesítmény- és Mérettesztelés az Azure Cosmos DB](performance-testing.md).

[1]: ./media/request-units/queryexplorer.png 
[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png
[6]: ./media/request-units/api-for-mongodb-metrics.png

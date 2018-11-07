---
title: Indexelési szabályzatok az Azure Cosmos DB |} A Microsoft Docs
description: Indexelő működésének megismerése az Azure Cosmos DB-hez. Ismerje meg, hogyan konfigurálja, és módosítsa az indexelési házirendet az Automatikus indexelés és jobb teljesítményt nyújt.
keywords: Indexelő működése, az automatikus indexelést, database indexelése
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: cd3b5f49788282b535f07c6f84bf7e4002132ab9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237587"
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Hogyan történik az adatok az Azure Cosmos DB indexelése?

Alapértelmezés szerint az összes Azure Cosmos DB-adatok indexelését. Bár sok ügyfél forduljon hozzánk, és lehetővé teszik az Azure Cosmos DB automatikusan kezelik az indexelő minden aspektusát, megadhat egy egyéni *indexelési szabályzat* gyűjtemények az Azure Cosmos DB létrehozása során. Az Azure Cosmos DB indexelési szabályzatok olyan, rugalmasabb és hatékonyabb, mint a másodlagos indexeket, amely az adatbázis-platformokhoz érhetők el. Az Azure Cosmos DB tervezheti meg és testre szabhatja az alakzat az index a séma rugalmasságát feláldozása nélkül. 

Ha indexelési útmutató az Azure Cosmos DB, fontos megérteni, hogy ha Ön kezeli az indexelési házirendet, végezhet részletes közti kompromisszummal indextárolási terheléssel jár, írási és lekérdezési adatforgalmat és a lekérdezési konzisztenciát.  

Az alábbi videó az Azure Cosmos DB Programigazgatója Andrew Liu azt mutatja be az Azure Cosmos DB Automatikus indexelés képességeit, és hogyan adhatja meg, és a indexelési házirend konfigurálása az Azure Cosmos DB-tároló. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

Ebben a cikkben azt tekintse meg zárja be az Azure Cosmos DB házirendeket, hogyan szabhatja testre az indexelési házirendet, és a kapcsolódó feláldozását indexelés. 

Ez a cikk elolvasása után is elérheti az alábbi kérdések megválaszolásához:

* Hogyan bírálhatom felül a tulajdonságok alapján belefoglalása vagy kizárása indexelő?
* Hogyan konfigurálhatok végleges frissítések az indexet?
* Hogyan konfigurálhatok ORDER BY vagy a tartomány-lekérdezések végrehajtása az indexelés?
* Hogyan tehetem módosításokat egy gyűjtemény indexelési házirendet?
* Hogyan összehasonlítása tárolás és teljesítmény különböző indexelési szabályzat?

## <a id="Indexing"></a> A cosmos DB-indexelő

Az adatbázisa indexei célja, hogy a különböző űrlapokban és az alakzatok-lekérdezések kiszolgálása a minimális erőforrás-használat (például CPU és a bemeneti és kimeneti) művelet során gondoskodik a magas átviteli sebességű és kis késése. A kiválasztott adatbázisok lekérdezése megfelelő indexe gyakran sokkal tervezés és a kísérletezés szükséges. Ez a megközelítés a séma nélküli adatbázis, ahol az adatok nem felelnek meg a szigorú sémát, és gyorsan haladásával nagy kihívást jelent. 

A Cosmos DB-indexelő alrendszer alakítottuk ki, amikor azt állítsa be az alábbi célok:

* Anélkül, hogy a séma Indexeljük a dokumentumokat: az indexelési alrendszer nem igényelnek semmilyen adatbázisséma-információk és győződjön meg arról, a dokumentumok séma feltételezésekre.  

* Hatékony és sokrétű hierarchikus és relációs lekérdezéseket támogatja: az index támogatja a Cosmos DB lekérdezési nyelv hatékonyan, beleértve a hierarchikus és relációs leképezések támogatását.  

* Az írások tartós kötet in face of konzisztens lekérdezések támogatása: nagy írási átviteli számítási feladatokhoz konzisztens lekérdezések, az index frissítése Növekményesen, hatékony és online mennyiségű írást esetén. A konzisztens index frissítése elengedhetetlen a konzisztencia szintjét, amelyben a felhasználó konfigurált a dokumentum-szolgáltatás, a lekérdezések kiszolgálása érdekében.  

* Több-bérlős támogatás: Adja meg a Foglalás-alapú modell az erőforrás-szabályozás bérlőn, index frissítéseit belül menjenek végbe a költségvetés rendszererőforrást (Processzor, memória és bemeneti/kimeneti műveletek száma másodpercenként) replikánként lefoglalva.  

* Tárolási hatékonyság: A költséghatékonyság, az index a lemezen indextárolási terheléssel jár a körülhatárolt és kiszámítható. Ez a rendkívül fontos, mert a Cosmos DB lehetővé teszi, hogy a fejlesztő, hogy a költség-alapú és kompromisszumot kínál a lekérdezési teljesítmény viszonyítva index terhelés között.  

## Gyűjtemény indexelési házirend testreszabása <a id="CustomizingIndexingPolicy"></a>  
Testre szabhatja a storage, írási és lekérdezési teljesítmény és a lekérdezési konzisztenciát közötti skálán letiltásával alapértelmezett indexelési szabályzat egy Azure Cosmos DB-gyűjteményen. Konfigurálhatja a következő szempontokat:

* **Bevonhat vagy kizárhat a dokumentumok és az elérési utak, és az indexből**. Zárja ki, vagy meghatározott dokumentumok tartalmazzák az indexben, helyezze be vagy cserélje le a dokumentumokat a gyűjtemény. Akkor is is bevonhat vagy kizárhat meghatározott JSON-tulajdonságokkal, más néven *elérési utak*, indexelendő dokumentumok index részét képező között. Elérési utak közé tartozik a helyettesítő karakterek mintái.
* **Konfigurálja a különféle index**. Az egyes belefoglalt elérési út adja meg az elérési út van szükség, egy gyűjtemény indexe típusát. Megadhatja, hogy az elérési út adatokat, a várható számítási feladatok és a numerikus vagy karakterlánc "pontosság." index típusa
* **Index frissítési módok konfigurálása**. Az Azure Cosmos DB támogatja az indexelő három mód: egységes, lassú, sem. Az indexelő üzemmódok indexelési szabályzat egy Azure Cosmos DB-gyűjtemények is beállíthatja. 

A Microsoft .NET következő kódrészletet bemutatja, hogyan egyéni indexelési szabályzat állítsa a gyűjtemény létrehozásakor. Ebben a példában a házirend-karakterláncok és a számok a tartomány indexszel rendelkező állítjuk, a maximális pontosság. Ez a szabályzat karakterláncok ORDER BY lekérdezések végrehajtásához használható.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> A REST API verziója 2015-06-03 megjelenésével megváltozott indexelési szabályzat JSON-sémájában. A JSON-séma indexelési szabályzat a kiadástól kezdve támogatja a tartomány indexek karakterláncok. .NET SDK-val 1.2.0-s vagy annál újabb és a Java, Python és Node.js SDK-k 1.1.0-s támogatja az új házirend-sémában. Az SDK korábbi verziói a REST API verziója 2015-04-08 használják. A korábbi séma az indexelési szabályzat támogatják.
> 
> Alapértelmezés szerint az Azure Cosmos DB indexeli a dokumentumokon belül minden karakterlánc tulajdonságai következetesen kivonatoló indexszel rendelkező. Indexelt dokumentumok belüli összes numerikus tulajdonság következetesen tartomány indexszel.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Az indexelési házirendet a portál testreszabása

Az Azure Portalon gyűjtemény indexelési házirendet módosíthatja: 

1. A portálon nyissa meg az Azure Cosmos DB-fiókot, és válassza ki a gyűjteményt. 
2. A bal oldali navigációs menüben válassza ki a **beállítások**, majd válassza ki **indexelési szabályzata**. 
3. A **indexelési szabályzata**, módosítsa az indexelési házirendet, és válassza **OK**. 

### Adatbázis-indexelés módok <a id="indexing-modes"></a>  
Az Azure Cosmos DB támogatja az indexelési házirendet egy Azure Cosmos DB-gyűjteményen keresztül konfigurálható három indexelési mód: egységes, lassú, sem.

**Egységes**: Ha egy Azure Cosmos DB-gyűjtemények házirend Consistent, a lekérdezések egy adott Azure Cosmos DB-gyűjteményen hajtsa végre a konzisztencia szintjét az a pont – olvasásokhoz megadott (erős, kötött elavulás, munkamenet és végleges). Az index frissítése szinkron módon, a dokumentum frissítése (insert, replace, update és delete egy dokumentumot egy Azure Cosmos DB-gyűjteményben) részeként.

Lemezírás teljesítménye cserébe lehetséges csökkentése konzisztens lekérdezések konzisztens indexelő támogatja. A csökkentési, az egyedi elérési utak indexelni kell függvény és a "konzisztenciaszint." Konzisztens az indexelő mód "írása gyorsan, azonnal query" számítási feladatokhoz készült.

**Lusta**: az index frissítése aszinkron módon ha egy Azure Cosmos DB-gyűjtemények videokártyának, vagyis ha a gyűjtemény átviteli kapacitás teljes mértékben nem használható a felhasználói kérések kiszolgálására.  Vegye figyelembe, hogy kaphat inkonzisztens, mert az adatok betöltött és lassan indexelt eredmények. Ez azt jelenti, hogy a lekérdezések száma és az adott lekérdezési eredmények lehetséges, hogy nem egységes és megismételhető, megadott idő. 

Az index általában betöltött utólagos módban van. Lusta indexelést, az élettartam (TTL) módosítja az index eldobása folyamatban van, majd újra létrehozza az eredmény. Ez lehetővé teszi az száma és a lekérdezési eredmények inkonzisztens egy ideig. A legtöbb Azure Cosmos DB-fiókok a konzisztens az indexelő módot kell használnia.

**Nincs**: egy gyűjteményt, amely tartalmaz egy index mód nincs társítva indexszel rendelkezik. Ez általában akkor használatos, ha az Azure Cosmos DB egy kulcs-érték tárolóként szolgál, és dokumentumok csak az ID tulajdonság által elért. 

> [!NOTE]
> Az indexelési szabályzat konfigurálása, sem rendelkezik a mellékhatása bármely meglévő index elvetését. Akkor használja, ha csak ID használatának megkövetelése a hozzáférési mintákat, vagy önhivatkozást.
> 
> 

Az alábbi táblázat a konzisztenciát a lekérdezések az indexelési üzemmód (Consistent és Lazy) konfigurálva ahhoz a gyűjteményhez, és a konzisztencia szintjét, a lekérdezés kéréshez megadott alapján. Ez minden olyan felhasználói felületének használatával végzett lekérdezésekre vonatkozik: REST API, SDK-k vagy a tárolt eljárásokkal és eseményindítókkal. 

|Konzisztencia|Az indexelő mód: konzisztens|Az indexelő mód: lassú|
|---|---|---|
|Erős|Erős|Végleges|
|Korlátozott frissesség|Korlátozott frissesség|Végleges|
|Munkamenet|Munkamenet|Végleges|
|Végleges|Végleges|Végleges|

Az Azure Cosmos DB a gyűjteményeket, amelyek egy sem mód indexelő rendelkezik végzett lekérdezésekhez hibát ad vissza. Lekérdezések továbbra is hajtható végre, az explicit keresztül vizsgálatok **x-ms-documentdb-enable-vizsgálat** fejléc a REST API-ban vagy a **EnableScanInQuery** beállítás kérése a .NET SDK használatával. A vizsgálatok nem támogatottak egyes lekérdezési funkciók, például az ORDER BY **EnableScanInQuery**.

Az alábbi táblázatban láthatók a konzisztencia az indexelési mód (Consistent, Lazy és None) alapuló lekérdezések során **EnableScanInQuery** van megadva.

|Konzisztencia|Indexelő mód: konzisztens|Indexelő mód: lassú|Indexelő mód: nincs|
|---|---|---|---|
|Erős|Erős|Végleges|Erős|
|Korlátozott frissesség|Korlátozott frissesség|Végleges|Korlátozott frissesség|
|Munkamenet|Munkamenet|Végleges|Munkamenet|
|Végleges|Végleges|Végleges|Végleges|

Az alábbi kód példa show koncentrálódik dokumentum konzisztens indexelése a .NET SDK használatával hogyan hozzon létre egy Azure Cosmos DB-gyűjteményben.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Index elérési utak
Az Azure Cosmos DB JSON-dokumentumokat és az index modelleket, fákat. A házirendek az elérési utak a fa belül hangolhassa. A dokumentumok kiválaszthatja a belefoglalása vagy kizárása indexelő elérési útjára. Ez jobb írási teljesítmény- és alacsonyabb index forgatókönyvekhez, ahol a lekérdezési mintáknak előre ismert kínálnak.

Index elérési utak a gyökér (/) kezdődő, és általában végén a? helyettesítő karakteres operátor. Ez azt jelzi, hogy nincsenek-e több lehetséges értékek a előtag. Például szolgálja ki a SELECT * FROM családok F WHERE F.familyName = "Andersen", /familyName/ egy index elérési útját meg kell adni? a gyűjtemény indexe házirendben.

Index elérési utakat is használhatja a \* helyettesítő operátor számára az elérési út rekurzív módon az előtag alapján viselkedésének megadása. Például/hasznos / * kizárása a hasznos adat tulajdonság található minden elemet az indexelő is használható.

Az alábbiakban a közös minta indexet-útvonalak megadása:

| Útvonal                | Leírás és használati eset                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | A gyűjtemény alapértelmezett elérési utat. A rekurzív, és a teljes dokumentum fa vonatkozik.                                                                                                                                                                                                                                   |
| / prop /?             | Index elérési út az alábbihoz hasonló-lekérdezések kiszolgálása érdekében szükséges (kivonatoló vagy tartomány típusú, illetve):<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop > 5<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop                                                                       |
| / prop / *             | A megadott címke alatti összes elérési utat index elérési útja. A következő lekérdezéseket működik<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop > 5<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop.nextprop = "érték"<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop         |
| / Kellékek / [] /?         | Index kell felelni az iteráció és a JOIN lekérdezéseket az Pole binárních hodnot fejlécekké például ["a", "b", "c"] elérési útja:<br><br>A címke a collection.props címkézése VÁLASSZA WHERE címke = "érték"<br><br>A gyűjtemény c ILLESZTÉSI címke IN c.props VÁLASSZA címke > 5 ahol címkézése                                                                         |
| [] /subprop/ /props/? | Iteráció kiszolgálásához szükséges index elérési út és a JOIN lekérdezéseket az objektumok, tömbök, például [{subprop: "a"}, {subprop: "b"}]:<br><br>A címke a collection.props címkézése VÁLASSZA WHERE tag.subprop = "érték"<br><br>Válassza ki címkét a gyűjtemény c ILLESZTÉSI címke IN c.props WHERE tag.subprop = "érték"                                  |
| / prop/subprop /?     | -Lekérdezések kiszolgálása érdekében szükség index elérési (kivonatoló vagy tartomány típusú, illetve):<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Állíthat be egyéni index elérési utak, akkor szükségesek, adja meg az alapértelmezett indexelési szabály a teljes dokumentum fa, amely speciális elérési helyén "/ *". 
> 
> 

Az alábbi példa egy adott elérési út tartományindexszel és a egy egyéni pontosság értéke 20 bájtos konfigurálja:

```
    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);
```

Az indexelés hozzáadásakor egy elérési utat, a számok és karakterláncok adott elérési útján belül indexelve. Ezért annak ellenére, hogy csak a karakterláncok indexelő határozza meg, az Azure Cosmos DB alapértelmezett definícióját, valamint számokat ad hozzá. Más szóval az Azure Cosmos DB van arra, hogy az elérési út kizárás az indexelési házirendet, de nem írja be a kizárási adott elérési úton. Példa, vegye figyelembe, hogy csak egy index mindkét útvonal van megadva (elérési útja = "/ *" és az elérési út = "/\"attr1\"/?"), de a szám adattípust is hozzá az eredményhez.

```
var indices = new[]{
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 }// <- note: only 1 index specified
                    },
                    Path =  "/*"
                },
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 } // <- note: only 1 index specified
                    },
                    Path =  "/\"attr1\"/?"
                }
            };...

            foreach (var index in indices)
            {
                documentCollection.IndexingPolicy.IncludedPaths.Add(index);
            }
```

Az index létrehozása eredménye:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        },
        {
            "path": "/\"attr\"/?",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        }
    ],
}
```

### <a name="index-data-types-kinds-and-precisions"></a>Index adattípusok, típusú és szükséges
Több lehetőség áll rendelkezésére az indexelési házirendet egy elérési út konfigurálásakor. Egy vagy több indexelő definíciót minden útvonalhoz adhatja meg:

* **Adattípus**: karakterlánc, szám, pont, Polygon vagy LineString (tartalmazhat elérési adattípus eszközönként csak egy bejegyzés).
* **Index típusa**: kivonata (egyenlőség lekérdezések), a tartomány (egyenlőség, tartomány vagy ORDER BY lekérdezések) vagy a Spatial (térbeli lekérdezések).
* **Pontosság**: egy kivonatot az index, ez az érték 1-től a sztringekkel és a számok 8-ra. Az alapértelmezett érték 3. Ez az érték egy tartományindexszel lehet -1 (maximális pontosság). 1 és 100 (maximális pontosság) karakterlánc vagy szám értékek munkafüzet változhat.

#### <a name="index-kind"></a>Index típusa
Az Azure Cosmos DB támogatja a kivonat index és a tartomány index típusú minden elérési utat, karakterlánc vagy szám adattípusok konfigurálható, illetve mindkettőt.

* **Kivonatoló** hatékony egyenlőség és JOIN lekérdezéseket támogat. A legtöbb használati esetek kivonatindexek pontossága nagyobb, mint az alapértelmezett érték 3 bájt nem szükséges. Az adattípus karakterlánc vagy szám lehet.
* **Tartomány** támogatja hatékony egyenlőség lekérdezéseket, a lekérdezések (használatával >, <>, =, < =,! =), és ORDER BY lekérdezések. Alapértelmezés szerint az ORDER By lekérdezések maximális pontosság (-1) is szükséges. Az adattípus karakterlánc vagy szám lehet.

Az Azure Cosmos DB is támogatja a térbeli index típusa minden elérési utat, amely a terjesztési pontok, Polygon, valamint LineString adattípusok adható meg. A megadott elérési úton értéke nem lehet például egy érvényes GeoJSON töredék `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Térbeli** támogatja a hatékony térbeli (belül és a távolság) lekérdezéseket. Az adattípus lehet pont, Polygon vagy LineString.

> [!NOTE]
> Az Azure Cosmos DB támogatja az automatikus indexelését, LineString, pontot és sokszög adattípusokat.
> 
> 

Az alábbiakban a támogatott index típusú és a lekérdezések, amelyek csak példák szolgáltatására használhatók:

| Index típusa | Leírás és használati eset                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Kivonat       | Ujjlenyomat-keresztül/prop /? (vagy vagy) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Kivonatoló keresztül/Kellékek / [] /? (és / vagy/Kellékek /) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki címkét a gyűjtemény c ILLESZTÉSI címke IN c.props WHERE címke = 5                                                                                                                       |
| Tartomány      | Tartomány/prop/keresztül? (vagy vagy) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop > 5<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop                                                                                                                                                                                                              |
| Térbeli     | Tartomány/prop/keresztül? (vagy vagy) segítségével hatékonyan szolgálja ki a következő lekérdezéseket:<br><br>Válassza ki a gyűjtemény c<br><br>HOL ST_DISTANCE (c.prop, {"type": "Pontot", "koordináták": [0.0, 10.0]}) < 40<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN(c.prop, {"type": "Polygon",...}) – az indexelést a pont van engedélyezve<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN({"type": "Point",...}, c.prop) – az indexelést a sokszög engedélyezve              |

Alapértelmezés szerint hibát ad vissza a lekérdezéseket. például tartomány operátorok > =, ha nincs (a bármely pontossági) tartomány index, hogy jelezze, hogy a lekérdezés kiszolgálása érdekében szükség lehet-e a vizsgálatot. Tartomány index nélkül is elvégezhető lekérdezések használatával a **x-ms-documentdb-enable-vizsgálat** fejléc a REST API-ban vagy a **EnableScanInQuery** beállítás kérése a .NET SDK használatával. Ha bármilyen más szűrők a lekérdezésben, hogy az Azure Cosmos DB az index szűrésére használható ellen, nincs hiba adja vissza.

Ugyanezek a szabályok vonatkoznak a térinformatikai lekérdezéseket. Alapértelmezés szerint hibát ad vissza térbeli lekérdezéseket, ha nincs térbeli index, és nincsenek egyéb szűrők, amelyek az indexből kiszolgálható. Akkor is elvégezhető a vizsgálat használatával **x-ms-documentdb-enable-vizsgálat** vagy **EnableScanInQuery**.

#### <a name="index-precision"></a>Index pontosság
Index pontosság segítségével győződjön meg arról, és hátrányokkal indexelt tárolási terhelés és a lekérdezési teljesítmény. Számok javasolt az alapértelmezett pontosság konfigurációt a-1 (maximum értéket) használja. Mivel számok 8 bájtos JSON-ban, ez az egyenértékű 8 bájtos konfigurációra. 1 – 7, például a pontosság kisebb értéket választja azt jelenti, hogy az egyes tartományokon belül értékek leképezése azonos index bejegyzés. Ezért index tárolóhely csökkentheti, de a lekérdezés végrehajtása lehet feldolgozni további dokumentumok. Ebből következően fogyaszt a kérelemegység a kapacitás növelése érdekében.

Index pontosság konfigurációs van több gyakorlati alkalmazás karakterlánc tartományok. Karakterláncok bármilyen tetszőleges hosszúságú lehet, mert az index pontosság választása hatással lehetnek karakterlánc lekérdezések teljesítményét. Index tárterület szükséges mennyisége is érintheti. Karakterlánc tartomány indexek is konfigurálhatók, 1 és 100-as vagy a -1 (maximum értéket). ORDER BY-lekérdezéseket futtassanak karakterlánc tulajdonságait szeretné, ha meg kell adnia a megfelelő elérési utakra 1 pontossága.

A térbeli indexek mindig használja az alapértelmezett index pontosság (pont, LineString és sokszög) minden alkalmazástípus esetében. Az alapértelmezett index pontosság a térbeli indexek nem lehet felülírni. 

Az alábbi példa bemutatja, hogyan növelheti a pontosság, a tartomány indexek egy gyűjtemény a .NET SDK használatával. 

**Gyűjtemény létrehozása egyéni index pontosságú**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Az Azure Cosmos DB hibát ad vissza, ha a lekérdezés ORDER BY használja, de nem rendelkezik a legnagyobb pontosságú lekérdezett elérési elleni tartomány indexszel. 
> 
> 

Az indexelő hasonlóan elérési utak teljesen kizárhatja. A következő példa bemutatja, hogyan zárhat ki a dokumentumok egy teljes szakasz (egy *részfa*) az indexelő használatával a \* helyettesítő operátor.

    var excluded = new DocumentCollection { Id = "excludedPathCollection" };
    excluded.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    excluded.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Részvétel a, és lemond az indexelő
Kiválaszthatja, hogy kívánja-e a gyűjtemény összes dokumentum automatikusan indexelése. Alapértelmezés szerint minden dokumentum automatikusan indexelt, de a felhasználók bármikor kikapcsolhatják az automatikus indexeléshez. Ha az indexelés ki van kapcsolva, dokumentumok csak keresztül elérhető azok önmagukra mutató hivatkozások, vagy a lekérdezések használatával, hogy a dokumentum azonosítója.

Az Automatikus indexelés ki van kapcsolva, az index csak bizonyos dokumentumokhoz szelektív továbbra is hozzáadhat. Ezzel szemben az automatikus indexelést a hagyhatja, és szelektív ki szeretne zárni bizonyos dokumentumokhoz. Be-és kikapcsolása konfigurációk az indexelés akkor hasznos, ha csak egy részhalmazát, amelyet le kell kérdezni dokumentumok rendelkezik.

A következő minta bemutatja, hogyan tartalmazzák a dokumentum kifejezetten a [SQL API .NET SDK-val](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) és a [RequestOptions.IndexingDirective](https://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) tulajdonság.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Gyűjtemény indexelési házirend módosítása
Az Azure Cosmos DB a működés közbeni gyűjtemény indexelési házirendet módosíthatja. Indexelési szabályzat egy Azure Cosmos DB-gyűjtemények a változás az alakzat az index módosítása vezethet. A módosítás befolyásolja a indexelhetők elérési utakat, a pontosság és az index magát a konzisztenciájú modellt. Indexelési szabályzat lényegében változását végrehajtásához egy új indexbe a régi index. 

**Online index-átalakítások**

![Indexelési útmutató – Azure Cosmos DB online index-átalakítások](./media/indexing-policies/index-transformations.png)

Online index átalakítások menjenek végbe. Ez azt jelenti, hogy a régi szabályzatonként indexelt dokumentumok hatékonyan átalakításából származnak az új házirend szerint *működésének megzavarása nélkül megtesztelheti az írás rendelkezésre állása, vagy a kiosztott átviteli sebesség* a gyűjtemény. Konzisztenciájának olvasási és írási a REST API, SDK-k használatával végrehajtott műveletek vagy belül tárolt eljárások és eseményindítók nincs hatással az index átalakítása során. 

Indexelési házirend módosítása egy aszinkron folyamat, amely a művelet elvégzéséhez szükséges idő attól függ, a dokumentumok méretétől, dokumentumok és fenntartott egységek számát. Amíg újraindexelését folyamatban van, akkor a lekérdezés adhatnak vissza a megfelelő eredményeket, ha a lekérdezés az éppen módosított index és lekérdezések nem ad vissza hibát vagy hibákat. Amíg újraindexelését folyamatban van, a lekérdezések végül konzisztens, függetlenül az indexelési módjának saját konfigurációja (Consistent vagy Lazy). Az index után átalakítása befejeződik, továbbra is megtekintheti a konzisztens eredmények. Ugyanez vonatkozik a lekérdezések bármely interfész: REST API, SDK-k és a tárolt eljárásokkal és eseményindítókkal. Csakúgy, mint Lazy indexelést, az index átalakítási aszinkron módon történik a háttérben a replikákon egy adott replika elérhető késztartalék források segítségével. 

Index átalakításokat is teljesülnek. Az Azure Cosmos DB karbantartása nem két példányban az index és a Váltás a régi index ki az újra. Ez azt jelenti, hogy nincsenek további lemezterület szükséges felhasznált ebben a gyűjteményben, míg az index átalakítások történik.

Indexelési házirend módosításához változások áthelyezése a régi index, az új elsősorban az indexelési mód konfigurációk alapján lépnek érvénybe. Az indexelő mód konfigurációk, mint például/kizárt elérési utak, index típusú és a szükséges egyéb értékek nagyobb szerepet játszanak. 

Ha a régi és új szabályzatok mindkét konzisztens indexelést, az Azure Cosmos DB egy online indexkészítés átalakítást hajt végre. Egy másik indexelési házirend-módosítások, konzisztens az indexelő mód az átalakítás közben nem lehet alkalmazni. Azonban áthelyezheti Lazy vagy nincs folyamatban van egy átalakítás közben az indexelő mód: 

* Lusta helyezi át, az index szabályzatának módosítása esetén hatékony azonnal. Az Azure Cosmos DB elindul, hozza létre újra az index aszinkron módon történik. 
* Ha áthelyezi a nincs, az index azonnal megszakad. Nincs áthelyezése akkor hasznos, ha megszakítja a folyamatban lévő átalakulása, és a kezdés tiszta lappal egy másik indexelési házirendet. 

A következő kódrészletet bemutatja, hogyan módosíthatja egy gyűjtemény indexelési házirend-a konzisztens az indexelő módból lusta indexelési módra. A .NET SDK-t használja, ha akkor is indíthat egy indexelési házirend módosítása az új **ReplaceDocumentCollectionAsync** metódust.

**Lusta Consistent az indexelési házirend módosítása**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Nyomon követheti a index átalakítása**

Nyomon követheti az index átalakítás százalékos előrehaladását konzisztens index használatával a **IndexTransformationProgress** válasz tulajdonságot egy **ReadDocumentCollectionAsync** hívja. Más SDK-k és a REST API-támogatása egyenértékű tulajdonságai és metódusai indexelési szabályzat módosítása. Egy index átalakítást index konzisztens állapotának ellenőrzéséhez hívása **ReadDocumentCollectionAsync**: 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * A **IndexTransformationProgress** tulajdonság alkalmazható, csak egy egységes index átalakításakor. Használja a **ResourceResponse.LazyIndexingProgress** átalakítások követése Lusta index tulajdonsága.
> * A **IndexTransformationProgress** és a **LazyIndexingProgress** tulajdonságok fel van töltve, csak az egy nem particionált gyűjteményt, azaz, ha egy gyűjteményt, amely nem tartozik partíciós kulcs létrehozása.
>

Egy gyűjtemény indexe helyezi át a nincs mód az indexelés vethetők el. Ha megszakítja a folyamatban lévő átalakulása, és azonnal nekiláthasson egy új erre akkor lehet hasznos működési eszköz.

**Dobja el a gyűjtemény indexe**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Ha szeretné módosít indexelési szabályzat az Azure Cosmos DB-gyűjteményekhez? Az alábbiakban a leggyakoribb alkalmazási helyzetei:

* Normál működés során szolgálja ki a konzisztens eredmények, de forráshelyeként lusta indexelési módra tömeges adatok importálás során.
* Az aktuális Azure Cosmos DB-gyűjteményekre vonatkozó új indexelési szolgáltatások használatának megkezdéséhez. Például használja, a térinformatikai lekérdezések, ami megköveteli, hogy a térbeli index típusa vagy ORDER BY / karakterlánc-tartomány lekérdezések, amelyek a tartomány index típusú karakterlánc szükséges.
* Válassza ki az aktuális indexelendő tulajdonságait, és módosítsa őket idővel.
* Finomhangolja az indexelő pontosság lekérdezési teljesítmény vagy a felhasznált tárterület csökkentése érdekében.

> [!NOTE]
> Indexelési házirend módosítása használatával **ReplaceDocumentCollectionAsync**, 1.3.0 vagy a .NET SDK újabb verzióját kell használnia.
> 
> Index átalakítási sikeresen befejeződik győződjön meg arról, hogy nincs elegendő szabad tárterület érhető el a gyűjtemény. Ha a gyűjtemény elérte a tárolási kvótát, az index átalakítás fel van függesztve. Index átalakítási automatikusan folytatja Ha tárterület érhető el, például ha néhány dokumentumot töröl.
> 
> 

## <a name="performance-tuning"></a>Teljesítmény-finomhangolás
Az SQL API-k teljesítmény-mérőszámokat, például a használt index tárterület és átviteli sebesség költsége (a kérelemegység) minden művelet adatainak megadása. Ezen információk használatával különféle indexelési szabályzatok, összehasonlítása és a teljesítmény finomhangolásának.

A tárolási kvótát és a egy gyűjtemény használati ellenőrzéséhez futtassa a **fő** vagy **első** kérelmet a gyűjtemény-erőforráshoz. Ezt követően vizsgálja meg a **x-ms-kérelem-quota** és a **x-ms-kérelem-használat** fejlécek. A .NET SDK-ban a [DocumentSizeQuota](https://msdn.microsoft.com/library/dn850325.aspx) és [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) tulajdonságok [ResourceResponse < T\> ](https://msdn.microsoft.com/library/dn799209.aspx) ezek megfelelő értékeket tartalmaznak.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Minden egyes írási műveletnél az indexelő járó többletterhelést mérésére (létrehozása, frissítése vagy törlése), vizsgálja meg a **x-ms-kérelem-díj** fejléc (vagy az azzal egyenértékű [RequestCharge](https://msdn.microsoft.com/library/dn799099.aspx) tulajdonság [ ResourceResponse < T\> ](https://msdn.microsoft.com/library/dn799209.aspx) a .NET SDK-ban), ezek a műveletek által felhasznált kérelemegységek számát.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Az indexelési házirend-megadás módosításai
A séma az indexelési szabályzat módosítása 2015. július 7,., a REST API verziója 2015-06-03 jelent meg. Az SDK-verziókra megfelelő osztályát felel meg a sémának, új megvalósításokkal rendelkeznek. 

A következő módosításokat a JSON-specifikáció lettek megvalósítva:

* Tartomány indexek indexelési szabályzat támogatja a karakterláncokat.
* Mindegyik elérési út rendelkezhet több index definíciója. Ez lehet egy, az egyes adattípusokhoz.
* Pontosság indexelő támogatja az 1 – 8 számok, 1 és 100-karakterláncok és a -1 (maximális pontosság).
* Elérési út szegmensek egy idézőjel karaktert mindegyik elérési út nem igényelnek. Például hozzáadhat egy elérési utat   **/cím /?** helyett **"title" / /?**.
* A gyökér elérési úthoz, amely jelöli az "összes elérési út" is reprezentovat jako **/ \*** (mellett **/**).

Ha rendelkezik a kódot, hogy rendelkezések gyűjtemények tartalmazó 1.1.0-s verzió a .NET SDK vagy korábbi verziójú írt egyéni indexelési házirendet, SDK-verzió, 1.2.0-s áthelyezése módosítania kell az alkalmazás kódjában, ezek a változások kezelésére. Ha nem készített kódot, amely konfigurálja-e indexelési házirendet, vagy ha azt tervezi, az SDK-t egy korábbi verzióját használja, a módosítások nem szükségesek.

Gyakorlati összehasonlításáért Íme egy példa egy egyéni indexelési szabályzat használatával írt REST API verziója 2015-06-03, az a korábbi REST API verziója 2015-04-08 használatával írt azonos indexelési szabályokat követ.

**Jelenlegi indexelési szabályzat JSON (REST API verziója 2015-06-03)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }


**Korábban a házirend JSON (REST API verziója 2015-04-08) indexelése**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>További lépések
Az index csoportházirend felügyeleti mintáit, és tudhat meg többet az Azure Cosmos DB lekérdezési nyelv tekintse meg a következőket:

* [Az SQL API .NET index felügyeleti Kódminták](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Az SQL API REST-gyűjtemény műveletek](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [SQL-lekérdezés](sql-api-sql-query.md)


---
title: "Azure-házirendek indexelő Cosmos adatbázis |} Microsoft Docs"
description: "Indexelő működésének megismerése az Azure Cosmos-Adatbázisba. Ismerje meg, hogyan lehet konfigurálni, és módosítsa az automatikus indexeléshez és jobb teljesítményt nyújt az indexelési házirendet."
keywords: "Indexelő működése, az automatikus indexeléshez, adatbázis indexelő"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/17/2017
ms.author: arramac
ms.openlocfilehash: 8b990d1887551cbe182fe1c38d2cfd02f3af5e78
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Hogyan működik az Azure Cosmos DB index adatokat?

Alapértelmezés szerint az összes Azure Cosmos DB adatok indexelése. Míg számos ügyfél ahhoz, hogy automatikusan kezelik az indexelő minden szempontját Azure Cosmos DB boldogok, Azure Cosmos DB is támogat egy egyéni megadó **házirend indexelő** gyűjtemények létrehozása során. Az indexelő házirendek az Azure Cosmos Adatbázisba nincsenek rugalmasabb és hatékonyabb, mint más adatbázis platformokhoz kínált másodlagos indexek, mert a használatukkal megtervezését és testreszabása az index az alakzat séma rugalmasságát feláldozása nélkül. Megtudhatja, hogyan indexelési működik az Azure Cosmos Adatbázisba, ismernie kell arról, hogy kezelésével indexelési házirendet, hogy minden részletre kiterjedő mellékhatásokkal indextárolási terheléssel jár, az írási és a lekérdezés átviteli sebesség és a lekérdezés konzisztencia között.  

Ez a cikk azt nézze meg Azure Cosmos DB indexelő házirendek, hogyan szabhatja testre a indexelési házirendet, és a kapcsolódó kompromisszumot. 

A cikk elolvasása után képes lesz a következő kérdések megválaszolásához:

* Hogyan lehet felülbírálni a Tulajdonságok belefoglalása / kizárása az indexelő?
* Hogyan konfigurálható az index a végleges frissítéseket?
* Hogyan lehet beállítani a Order By vagy a tartomány lekérdezések végrehajtásához indexelő?
* Hogyan tehetem módosításokat egy gyűjtemény indexelési házirendet?
* Hogyan összehasonlítása tárterületi és teljesítménybeli különböző indexelési házirendek?

## <a id="CustomizingIndexingPolicy"></a>Az indexelési házirendet gyűjtemény testreszabása
A fejlesztők szabhatja az alapértelmezett indexelési házirendet egy Azure Cosmos DB gyűjteményen felülbírálása, és a következő vonatkozásait tárolás, az írási/lekérdezési teljesítmény és a lekérdezés konzisztencia közötti kompromisszumot.

* **Beleértve/dokumentumok és az index elérési utak kizárása**. A fejlesztők bizonyos dokumentumok kizárt vagy beszúrása vagy cseréje őket a gyűjteményhez időpontjában indexben szereplő választhat. A fejlesztők is választhatja, hogy bevonhat vagy kizárhat más néven bizonyos JSON tulajdonságai útvonalak (többek között a következőket helyettesítő mintákat) indexben szereplő dokumentumok között indexelése.
* **Különböző konfigurálása Index típusok**. Az egyes belefoglalt elérési utak fejlesztők is megadhat igényelnek-e egy gyűjtemény indexe típusú az adatok alapján, és várt, lekérdezés munkaterhelés és a numerikus vagy karakterlánc "pontosság" az egyes elérési utakat.
* **Index frissítés üzemmódjainak**. Azure Cosmos DB három indexelési módot, ami az indexelési házirendet egy Azure Cosmos DB gyűjteményen állíthatók támogatja: konzisztens, Lazy és "nincs". 

A következő .NET kódrészletet bemutatja, hogyan állítsa be az egyéni indexelési házirendet egy gyűjtemény létrehozása közben. Itt azt állítja be a házirendet a karakterláncok és a számok tartományindexszel rendelkező, a maximális pontosság. Ezzel a házirend-karakterláncok Order By lekérdezések végrehajtása teszi lehetővé.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> A JSON-séma az indexelési házirendet REST API-t 2015-06-03 verziója támogatja a tartomány indexek karakterláncok megjelenésével megváltozott. .NET SDK 1.2.0 és a Java, Python, és a Node.js SDK-k 1.1.0-ás támogatja az új házirend-séma. Régebbi SDK-k a REST API-t 2015-04-08 verzióját használja, és támogatja a régebbi séma indexelő házirend.
> 
> Alapértelmezés szerint a a tartományindexszel rendelkező a Azure Cosmos DB indexeli az összes karakterlánc-dokumentumok következetesen egy belül, és a numerikus tulajdonságai.  
> 
> 

### <a name="customizing-the-indexing-policy-using-the-portal"></a>Az indexelési házirendet, a portál használatával testreszabása

Az indexelési házirendet a gyűjtemény az Azure portál használatával módosíthatja. Nyissa meg az Azure Cosmos DB fiók az Azure portálon, válassza ki a gyűjteményt, a kattintson a bal oldali navigációs menü **beállítások**, és kattintson a **indexelő házirend**. Az a **indexelő házirend** panelen módosíthatja az indexelési házirendet, és kattintson a **OK** menti a módosításokat. 

### <a id="indexing-modes"></a>Az indexelő adatbázis-mód
Azure Cosmos DB három indexelési módot támogat az Azure Cosmos DB gyűjteményt – az indexelő házirendjében állítható be Consistent, Lazy és "nincs".

**Egységes**: Ha egy Azure Cosmos DB gyűjtési "konzisztens" van kijelölve, a lekérdezések egy adott Azure Cosmos DB gyűjtemény hajtsa végre a konzisztencia szintjét a pont olvasása megadott (azaz erős, kötött elavulás, munkamenet vagy végleges). Az index frissítése a dokumentum frissítéssel (azaz insert, replace, update és delete egy dokumentum, egy Azure Cosmos DB gyűjteményben) szinkron módon történik.  Egységes indexelő ugyan lehetséges csökkentése érdekében következetes lekérdezéseket támogat, az írási teljesítmény. Ez a függvény az egyedi elérési utak indexelése igénylő és a "konzisztenciaszint" értéke. Egységes indexelő módban készült "write gyorsan, azonnal lekérdezés" munkaterhelések.

**Lusta**: Ebben az esetben az index frissítése aszinkron módon ha egy Azure Cosmos DB gyűjteményt videokártyának, ez azt jelenti, hogy ha a gyűjtemény átviteli sebesség nem teljes kihasználását felhasználói kérelem kiszolgálására. A dokumentum adatfeldolgozást igénylő "betöltési, később lekérdezés" munkaterhelések esetén a "Lusta" indexelő mód alkalmasak lehetnek. Vegye figyelembe, hogy inkonzisztens eredményeket kaphat, mint adat lekérdezi okozhatnak lassan indexelt. Ez azt jelenti, hogy a COUNT lekérdezés vagy a meghatározott lekérdezési eredmények nem feltétlenül konzisztens vagy repeatable egy adott időpontban. Az index általában catch feldolgozott adatokkal mód be van. Tekintetében Lusta indexelő idő Élettartam (TTL) módosítja az index eldobása és ismételt létrehozása megtörtént, így az a szám és a lekérdezési eredmények inkonzisztens egy ideig eredményez. Ezen okok miatt Azure Cosmos DB fiókok többsége használjon konzisztens indexelő.

**Nincs**: egy gyűjtemény indexe mód "None" jelölésű nincs társítva index tartozik. Ez általában akkor használatos, ha egy kulcs-érték tárolóként első Azure Cosmos DB és dokumentumok csak az ID tulajdonság által elért. 

> [!NOTE]
> A meglévő index eldobása mellékhatása konfigurálása az indexelési házirendet a "None" rendelkezik. Akkor használja, ha a hozzáférési minták csak igénylik, "id" és/vagy "önhivatkozást".
> 
> 

Az alábbi táblázat az indexelési üzemmód (Consistent és Lazy) konfigurálva ahhoz a gyűjteményhez, és a lekérdezés kéréshez megadott konzisztenciaszint alapuló lekérdezések konzisztencia. Ez semmilyen illesztőfelület - REST API-t SDK-k használatával végzett lekérdezések vonatkozik, vagy a tárolt eljárások és eseményindítók. 

|Konzisztencia|Indexelő mód: konzisztens|Indexelő mód: Lusta|
|---|---|---|
|Erős|Erős|Végleges|
|Kötött elavulás|Kötött elavulás|Végleges|
|Munkamenet|Munkamenet|Végleges|
|Végleges|Végleges|Végleges|

Azure Cosmos-adatbázis nincs mód indexelő gyűjteményre végrehajtott lekérdezések hibát ad vissza. Lekérdezések továbbra is hajtható végre, mert keresztül explicit vizsgálatok `x-ms-documentdb-enable-scan` fejléc a következő a REST API-t vagy a `EnableScanInQuery` kérése a beállítás a .NET SDK használatával. Néhány lekérdezés szolgáltatások, mint az ORDER BY nem támogatottak a vizsgálatok `EnableScanInQuery`.

Az alábbi táblázatban láthatók az indexelési mód (Consistent Lazy és None) alapuló lekérdezések konzisztencia Ha EnableScanInQuery meg van adva.

|Konzisztencia|Indexelő mód: konzisztens|Indexelő mód: Lusta|Indexelő mód: nincs|
|---|---|---|---|
|Erős|Erős|Végleges|Erős|
|Kötött elavulás|Kötött elavulás|Végleges|Kötött elavulás|
|Munkamenet|Munkamenet|Végleges|Munkamenet|
|Végleges|Végleges|Végleges|Végleges|

A következő kód a minta megjelenítése hogyan létrehozása az Azure Cosmos DB konzisztens indexelő az összes dokumentum Beszúrások a .NET SDK használatával.

     // Default collection creates a hash index for all string fields and a range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Index elérési utak
Azure Cosmos-adatbázis a modellek JSON-dokumentumok és az index fákként, és lehetővé teszi a házirendek a fája módjának hangolására. A dokumentumok kiválaszthatja, mely elérési kell lennie, illetve tiltani szeretné indexelésének. Továbbfejlesztett írási teljesítmény és alacsonyabb index tárolási forgatókönyvek esetén ez kínálhat, a lekérdezési mintáknak előzetesen ismert.

Index elérési utak a legfelső szintű (/) kezdődnie, és általában végén a? helyettesítő karakteres operátor szerinti szűrése, azt jelöli, hogy nincsenek-e az előtag több lehetséges értékei. Például VÁLASSZA kiszolgálására * a Families F WHERE F.familyName = "Andersen", meg kell adni egy index elérési útját /familyName/? a gyűjtemény indexe házirendben.

Index elérési utakat is használhatja a * helyettesítő operátorral adja meg az elérési utak rekurzív módon az előtag szerinti viselkedését. Például/hasznos / * indexelő kizárását minden elemet a hasznos tulajdonság használható.

Az alábbiakban a megadja a index közös minták:

| Útvonal                | Leírás/használati eset                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | A gyűjtemény alapértelmezett elérési utat. Rekurzív és a teljes dokumentum fa vonatkozik.                                                                                                                                                                                                                                   |
| / prop /?             | A következő lekérdezések kiszolgálásához szükséges index elérési útja (kivonatoló vagy tartomány típusokat rendre):<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop > 5<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop                                                                       |
| / prop / *             | A megadott címke az összes elérési utat index elérési útját. Az alábbi lekérdezéseket együttműködik<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop > 5<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop.nextprop = "érték"<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop         |
| [] / tulajdonságai / /?         | Index elérési út szükséges iterációs szolgálnak, és CSATLAKOZZON a skaláris értékeket tartalmazhat, például a ["a", "b", "c"] tömbök lekérdezéseket:<br><br>Válassza ki címke címke IN collection.props a WHERE címke = "érték"<br><br>A gyűjtemény c ILLESZTÉSI címke IN c.props VÁLASSZA címke ahol címke > 5                                                                         |
| [] /subprop/ /props/? | Index elérési iterációs kiszolgálásához szükséges, és az objektumok tömbök ILLESZTÉS küldött lekérdezésekre, például [{subprop: "a"}, {subprop: "b"}]:<br><br>Válassza ki címke címke IN collection.props a WHERE tag.subprop = "érték"<br><br>A gyűjtemény c ILLESZTÉSI címke IN c.props címke kiválasztása WHERE tag.subprop = "érték"                                  |
| / prop/subprop /?     | Lekérdezések kiszolgálásához szükséges index elérési útja (kivonatoló vagy tartomány típusokat rendre):<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Egyéni index elérési utak beállításakor szükségesek az alapértelmezett indexelési szabály a teljes dokumentum fa különleges elérési jelölik meg "/ *". 
> 
> 

A következő példa a tartomány indexelő konkrét elérési utat és 20 bájt egyéni pontosság érték konfigurálja:

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


### <a name="index-data-types-kinds-and-precisions"></a>Index adattípusok, típusú és szükséges
Most, hogy azt már hozott útvonalak megadása egy pillantást, vizsgáljuk meg a beállítások azt az indexelési házirendet egy útvonal konfigurálásához használható. Megadhatja, hogy egy vagy több indexelési definíciót minden az elérési úthoz:

* Adattípus: **karakterlánc**, **szám**, **pont**, **sokszög**, vagy **LineString** (csak egyet tartalmazhat bejegyzés adatok típusonként és példányonként elérési útja)
* Milyen index: **kivonatoló** (egyenlőség lekérdezések), **tartomány** (egyenlőség, tartomány vagy Order By lekérdezések), vagy **Spatial** (térbeli lekérdezéseket) 
* Pontosság: Kivonatoló index Ez az érték 1-karakterláncok, mind az alapértelmezett szám a 8 a 3. A tartományindexszel Ez az érték lehet -1 (maximális pontosság) és 1-100 (maximális pontosság) a karakterlánc- vagy számértékeknek változhat.

#### <a name="index-kind"></a>Index típusa
Azure Cosmos-adatbázis használatát támogatja kivonatoló és a tartomány index minden az elérési úthoz (amelyeket konfigurálni karakterláncok, számokat, vagy mindkettő).

* **Kivonatoló** hatékony egyenlőség és JOIN lekérdezéseket támogat. A legtöbb használatából adódó kivonatindexek nem kell az alapértelmezett érték 3 bájt-nál nagyobb pontosságú. DataType karakterlánc vagy szám lehet.
* **Tartomány** hatékony egyenlőség lekérdezéseket, a lekérdezések támogat (használatával >, <>, =, < =,! =), és Order By lekérdezések. Order By lekérdezések alapértelmezés szerint is szükség lehet index maximális pontosság (-1). DataType karakterlánc vagy szám lehet.

Azure Cosmos-adatbázis a is támogatja. a térbeli index jellegű minden elérési utat, amely a pont, sokszög vagy LineString adattípusok adható meg. Az érték a megadott elérési úton kell lennie mint érvényes GeoJSON töredéket `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Térbeli** támogatja a hatékony térbeli (belül és távolság) lekérdezések. DataType lehet pont, sokszög vagy LineString.

> [!NOTE]
> Azure Cosmos DB támogatja, pontokat, sokszögek és Linestring automatikus indexeléshez.
> 
> 

Az alábbiakban a támogatott index különböző és példákat a lekérdezéseiben, amelyeket el kiszolgálásához használt:

| Index típusa | Leírás/használati eset                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Kivonat       | Kivonatoló keresztül/prop /? (vagy /) segítségével hatékonyan tudja szolgálni az alábbi lekérdezéseket:<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Kivonatoló keresztül/tulajdonságai / [] /? (és / vagy/tulajdonságai /) segítségével hatékonyan tudja szolgálni az alábbi lekérdezéseket:<br><br>A gyűjtemény c ILLESZTÉSI címke IN c.props címke kiválasztása WHERE címke = 5                                                                                                                       |
| tartomány      | Tartomány/prop/keresztül? (vagy /) segítségével hatékonyan tudja szolgálni az alábbi lekérdezéseket:<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop > 5<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop                                                                                                                                                                                                              |
| Térbeli     | Tartomány/prop/keresztül? (vagy /) segítségével hatékonyan tudja szolgálni az alábbi lekérdezéseket:<br><br>Válassza ki a gyűjtemény c<br><br>HOL ST_DISTANCE (c.prop, {"type": "Terjesztésipont-", "koordináták": [0.0, 10.0]}) < 40<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN(c.prop, {"type": "Polygon",...}) – a pont is engedélyezve van az indexelő<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN({"type": "Point",...}, c.prop)--a sokszög engedélyezve van az indexelő              |

Alapértelmezés szerint hibát ad vissza, mint a tartomány operátorok tartalmazó lekérdezések a > =, ha nincs (a bármely pontosság) tartomány index ahhoz, hogy jelezze, hogy a vizsgálatok akkor lehet szükség, a lekérdezés kiszolgálásához. Az x-ms-documentdb-enable-vizsgálat fejlécet a REST API-t vagy a .NET SDK használatával EnableScanInQuery beállítást tartomány index nélkül lekérdezések végezheti el. Ha nincs más szűrők a lekérdezésben Azure Cosmos DB az index használatával szűrést, akkor nem hibaüzenetet küld.

Ugyanazok a szabályok vonatkoznak a térbeli lekérdezéseket. Alapértelmezés szerint hibát ad vissza térbeli lekérdezésekhez, ha nincs térbeli index, és nincsenek egyéb szűrők az indexből szolgáltatható. Keresés x-ms-documentdb-enable-vizsgálat/EnableScanInQuery használatával elvégezhető.

#### <a name="index-precision"></a>Index pontosság
Index pontosság lehetővé teszi a terhelés index tároló és a lekérdezési teljesítmény közötti kompromisszumot. A számok azt javasoljuk, az alapértelmezett pontosság konfigurációt a-1 ("maximális"). Mivel számok 8 bájt a JSON-ban, ez megegyezik egy 8 bájtos konfigurációt. Kiadási kisebb értéket, például 1-7, a pontosság azt jelenti, hogy értékek egyes tartományokon belül az azonos indexbejegyzése hozzárendelését. Ezért az index tárolóhely csökkenti, de a lekérdezés-végrehajtás kell feldolgozni a további dokumentumokat, és ezért felhasználását, azaz a további átviteli egységek kérése

Index pontosság konfigurációs karakterlánc címtartományai több alkalmazás van. Karakterláncok bármilyen tetszőleges hosszúságú lehet, mert az index pontosság is hatással lehet a tartalmazó karakterlánc lekérdezések teljesítményét, és hatással lehet a szükséges index tárhely mennyiségét. Karakterlánc tartomány indexek konfigurálható 1-100 vagy -1 ("maximális"). Ha azt szeretné, karakterlánc tulajdonságai Order By lekérdezések végrehajtásához, majd meg kell adnia egy-1 -nek megfelelő útvonalaira pontosságát.

A térbeli indexek mindig az alapértelmezett index pontosság minden típusú (pontok, Linestring és sokszögek) használ, és nem bírálható felül. 

A következő példa bemutatja, hogyan tartomány indexek .NET SDK használatával egy gyűjtemény pontosság növeléséhez. 

**Hozzon létre egy egyéni index pontosság gyűjteményt**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for Strings to range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB hibát ad vissza, ha a lekérdezés Order By használja, de nem rendelkezik a tartományindexszel a lekérdezett útvonalat, ahol a maximális pontosság ellen. 
> 
> 

Hasonlóképpen elérési utak teljesen kizárhatók indexelő. A következő példa bemutatja, hogyan egy teljes szakasz a dokumentumok (más néven kizárása egy részfájának) indexelési használja a "*" helyettesítő karakter.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opting-in-and-opting-out-of-indexing"></a>Engedélyezés és meggátolható a indexelő
Kiválaszthatja, hogy kívánja-e a gyűjtemény összes dokumentumot indexeléséhez. Alapértelmezés szerint összes dokumentum automatikusan indexeli ugyan, de Ön is kapcsolható ki. Ha indexelés ki van kapcsolva, dokumentumokat csak a is elérhetők az erre az önhivatkozások vagy lekérdezések használatával azonosítóját.

Az automatikus indexeléshez ki van kapcsolva, az index csak bizonyos dokumentumokhoz szelektív továbbra is hozzáadhat. Ezzel ellentétben automatikus az indexelő hagyhatja, és szelektív dönt, hogy csak bizonyos dokumentumokhoz kizárása. Be-és kikapcsolása konfigurációk indexelő akkor hasznos, ha a dokumentumok, amelyeket le kell kérdezni csak egy részhalmazát rendelkezik.

Például a következő példa bemutatja, hogyan dokumentum explicit módon tartalmazza a [SQL API .NET SDK](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet) és a [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) tulajdonság.

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modifying-the-indexing-policy-of-a-collection"></a>Az indexelési házirendet gyűjtemény módosítása
Azure Cosmos DB módosítja az indexelési házirendet parancsprogramok gyűjtemény teszi lehetővé. Indexelési házirendet egy Azure Cosmos DB gyűjteményt a változás az index, többek között az elérési utak indexelhetők alakját, a pontosság, valamint az index, maga a konzisztencia modell változása vezethet. Így indexelési házirendet változása hatékonyan végrehajtásához a régi index be egy újat. 

**Online indexkészítés átalakítások**

![Indexelő működése – Azure Cosmos DB online indexkészítés átalakítások](./media/indexing-policies/index-transformations.png)

Index átalakításához végzett online, ami azt jelenti, hogy a régi házirend egy indexelt dokumentumok hatékonyan átalakítaná e az új házirend **anélkül, hogy befolyásolná a írási rendelkezésre állás vagy a kiosztott átviteli sebesség** a gyűjtemény. A konzisztenciájának olvasási és írási műveletet a REST API-t SDK-k használatával létrehozott vagy belül tárolt eljárások és eseményindítók nem változik index átalakítása során. Ez azt jelenti, hogy nincs teljesítménycsökkenés vagy az alkalmazásokhoz állásidő meg, hogy egy indexelési házirendet, módosítsa.

Azonban az index átalakítása folyamatban a időszak lekérdezések nem idővel konzisztenssé, függetlenül a indexelési üzemmód (azonos vagy Lazy). Ez is lekérdezésekre vonatkozik az összes illesztő – REST API-t SDK-k, és a tárolt eljárások és eseményindítók. Csakúgy, mint az indexelő, Lazy index átalakítása történik aszinkron módon történik a replikákon tartalék erőforrásai replika használja a háttérben. 

Index átalakítások is **forrásgyűjteményben** (), azaz az Azure Cosmos DB nem másolatait a két az index és az újjal el a régi index felcserélése. Ez azt jelenti, hogy nincs további lemezterület szükséges vagy a gyűjtemények felhasznált index átalakítások végrehajtása közben.

Indexelési házirendet módosításakor hogyan a módosításai érvényesek lesznek az új egyik függő elsősorban az indexelési mód konfigurációk, mint a többi érték, például úgy a régi index áthelyezése szereplő/kizárt elérési utak, index különböző és szükséges. Ha a régi és az új házirendek egységes indexelő, Azure Cosmos DB egy online indexkészítés átalakítást hajt végre. Egy másik indexelési házirend változásának konzisztens indexelő módban nem tudja alkalmazni, amíg folyamatban van a transzformáció.

Azonban áthelyezheti Lazy vagy "None" átalakítás során az indexelő mód van folyamatban. 

* Amikor Lazy helyezi át, az index házirend módosításakor hatékony azonnal és Azure Cosmos DB elindítja az index létrehozása aszinkron módon történik. 
* Amikor nincs helyezi át, majd az index a rendszer eldobja hatékony azonnal. Nincs áthelyezése akkor hasznos, ha meg kívánja szakítani az egy folyamatban lévő átalakítása és egy másik indexelési házirendet az alapoktól start. 

Ha a .NET SDK használata esetén is indítsa el az indexelési házirend módosítása az új **ReplaceDocumentCollectionAsync** módszerről és az index átalakítása használatával százalékos állapotának nyomon követése a  **IndexTransformationProgress** válasz tulajdonságot egy **ReadDocumentCollectionAsync** hívható meg. Más SDK és a REST API támogatja egyenértékű tulajdonságai és metódusai indexelési házirend módosítása közben.

Íme egy kódrészletet, amely bemutatja, hogyan lehet módosítani egy gyűjtemény indexelési házirendet-konzisztens indexelési üzemmódról Lusta.

**Módosítsa Lusta való egységes az indexelési házirendet**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


Egy index átalakítást előrehaladását hívásával ReadDocumentCollectionAsync, például ellenőrizheti a lent látható módon.

**Index átalakítása előrehaladását úgy követheti nyomon**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

Egy gyűjtemény indexe a nincs mód indexelő áthelyezésével dobhatja el. Ez akkor lehet hasznos működési eszköz, ha azt szeretné, szakítsa meg a folyamatban lévő átalakulása, és egy új azonnali indítása.

**Az index a gyűjtemény**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Ha szeretné módosítja az indexelő házirend az Azure Cosmos DB gyűjtemények? A leggyakoribb használati esetek a következők:

* Konzisztens eredmények kiszolgálására során a normál működés, de a tömeges adatok importálása során a lusta indexelési alá esik
* Kezdő használatával új funkcióinak a jelenlegi Azure Cosmos DB gyűjtemények indexelő pl., például a földrajzi kérdez le, amely igényel a térbeli index ilyen, vagy Order By / karakterlánc-karakterlánc tartomány index jellegű igénylő lekérdezések
* Az aktuális indexelése és változnak az idők a Tulajdonságok kiválasztása
* Az indexelő pontosság és javíthatja a lekérdezések teljesítményét, vagy csökkentse a felhasznált tárhely hangolása

> [!NOTE]
> Indexelési házirendet ReplaceDocumentCollectionAsync módosításához verziójára van szükség > = 1.3.0 a .NET SDK
> 
> Az index átalakításhoz végrehajtása sikeres legyen gondoskodnia kell arról, hogy nincs elég szabad tárterület érhető el a gyűjteményben. Ha a gyűjtemény eléri a tárolási kvótát, az index átalakítás lehet szüneteltetni. Index átalakítása működése automatikusan folytatódik, ha tárolóhely érhető el, például egyes dokumentumok törlésekor.
> 
> 

## <a name="performance-tuning"></a>Teljesítmény-finomhangolás
Az SQL API-k adjon meg teljesítménymutatók, például a használt index tároló kapcsolatos információkat, és az átviteli sebesség költség (kérelemegység) minden műveletéhez. Ez az információ segítségével összehasonlítása különböző indexelési házirendek és a teljesítményhangolás.

A tárolási kvótát és használati gyűjtemény ellenőrzéséhez futtassa HEAD vagy GET kérelmet a gyűjtemény-erőforráshoz, és vizsgálja meg az x-ms-kérelem-kvóta és az x-ms-kérelem-használati fejlécekkel együtt. A .NET SDK-ban a [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) és [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) tulajdonságok [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) tartalmaznia kell a megfelelő értékeket.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


A terhelést növelni az indexelő minden egyes írási műveletnél a mérésére (létrehozása, frissítése vagy törlése), vizsgálja meg az x-ms-kérelem-kell fizetni fejléc (vagy egyenértékű [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) tulajdonság [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) a .NET SDK-ban) használni ezeket a műveleteket kérelem egységek számának mérésére.

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

## <a name="changes-to-the-indexing-policy-specification"></a>Az indexelési házirendet specifikáció módosításai
2015. július 7. REST API-t 2015-06-03 verzió a sémában az indexelési házirendet egy változást jelent meg. A megfelelő osztályokhoz SDK verziókban új megvalósítások felel meg a sémának a tartozik. 

A JSON-specifikáció bevezetett a következő módosításokat:

* Házirend indexelő támogatja a tartomány indexek karakterláncok
* Mindegyik elérési út lehet egy, az egyes adattípusokhoz több index definíciója
* Pontosság indexelő támogatja az 1-8, számok, 1-100 karakterláncok és -1 (maximális pontosság)
* Elérési utak szegmensek nem szükséges egy idézőjel karaktert az egyes elérési utakat. Például hozzáadhat egy elérési utat/cím /? Ahelyett, hogy / "title" /?
* Az "összes elérési utat" jelző Útvonalgyökér is jelenik meg vagy * (kívül /)

Ha kódot, hogy rendelkezések gyűjtemények 1.1.0-ás a .NET SDK vagy régebbi verziójával készült egyéni indexelési házirendet, akkor módosítsa az alkalmazás kódjában, ezek a változások kezelésének SDK verzió 1.2.0 át. Ha nem konfigurálja az indexelési házirendet kódot, vagy SDK régebbi verzióját használja, akkor nem módosításokra szükség.

Gyakorlati összehasonlítása Íme egy példa egyéni indexelési házirendet a REST API verziója 2015-06-03 használatával írt, valamint az előző verzió 2015-04-08.

**Előző indexelési házirendet JSON**

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

**Aktuális indexelési házirendet JSON**

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

## <a name="next-steps"></a>Következő lépések
Kövesse az alábbi hivatkozásokat követve index házirend felügyeleti mintákat és Azure Cosmos DB lekérdezési nyelv tájékozódhat.

1. [Az SQL API .NET Indexkezelés mintakódok](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2. [Az SQL API REST adatgyűjtési műveletek](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3. [Az SQL lekérdezés](documentdb-sql-query.md)


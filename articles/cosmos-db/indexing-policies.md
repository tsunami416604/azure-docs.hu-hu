---
title: Azure-házirendek indexelő Cosmos adatbázis |} Microsoft Docs
description: Indexelő működésének megismerése az Azure Cosmos-Adatbázisba. Ismerje meg, hogyan lehet konfigurálni, és módosítsa az automatikus indexeléshez és jobb teljesítményt nyújt az indexelési házirendet.
keywords: Indexelő működése, az automatikus indexeléshez, adatbázis indexelő
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: d867079b9a5546dc9555697a9066472e4e470977
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298297"
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Hogyan működik az Azure Cosmos DB index adatokat?

Alapértelmezés szerint az összes Azure Cosmos DB adatok indexelése. Bár sok ügyfél ahhoz, hogy automatikusan kezelik az indexelő minden szempontját Azure Cosmos DB problémamentesen működjön, megadhat egy egyéni *házirend indexelő* gyűjtemények létrehozása az Azure Cosmos-Adatbázisba. Az indexelő házirendek az Azure Cosmos Adatbázisba rugalmasabb és hatékonyabb, mint más adatbázis platformokon felkínált másodlagos indexek. Az Azure Cosmos Adatbázisba tervezését és testre szabhatja az index az alakzat séma rugalmasságát feláldozása nélkül. 

Megtudhatja, hogyan indexelési működik az Azure Cosmos Adatbázisba, fontos megérteni, hogy ha Ön kezeli az indexelési házirendet, hogy részletes kompromisszumot indextárolási terheléssel jár, az írási és a lekérdezés átviteli sebesség és a lekérdezés konzisztencia között.  

Az alábbi videó az Azure Cosmos DB Programvezető Andrew Liu automatikus képességeket és hangolására, és az indexelési házirendet konfigurálása az Azure Cosmos DB tárolóra indexelő Azure Cosmos-DB mutatja be. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

Ez a cikk azt nézze meg Azure Cosmos DB indexelő házirendjeit, hogyan szabhatja testre az indexelési házirendet, és a kapcsolódó kompromisszumot. 

A cikk elolvasása után képes lesz a következő kérdések megválaszolásához:

* Hogyan lehet felülbírálni a Tulajdonságok belefoglalása / kizárása az indexelő?
* Hogyan konfigurálható az index a végleges frissítéseket?
* Hogyan lehet beállítani a ORDER BY vagy a tartomány lekérdezések végrehajtásához indexelő?
* Hogyan tehetem módosításokat egy gyűjtemény indexelési házirendet?
* Hogyan összehasonlítása tárterületi és teljesítménybeli különböző indexelési házirendek?

## Az indexelési házirendet gyűjtemény testreszabása <a id="CustomizingIndexingPolicy"></a>  
Az alapértelmezett házirendet az Azure Cosmos DB gyűjteményt a indexelő felülbírálásával testre tárolás, az írási és a lekérdezési teljesítmény és a lekérdezés konzisztencia közötti kompromisszumot. Konfigurálhatja a következő szempontokat:

* **Bevonhat vagy kizárhat a dokumentumok és az elérési út, és onnan az index**. Zárja ki, vagy bizonyos dokumentumokhoz szerepeljenek az index beszúrása vagy cserélje le a dokumentumokat a gyűjteményben. Akkor is is bevonhat vagy kizárhat JSON tulajdonságokat, más néven *elérési utak*, indexben szereplő dokumentumok között indexelése. Elérési utak közé tartozik a helyettesítő mintákat.
* **Konfigurálja a különféle index**. Az egyes belefoglalt elérési út adja meg az elérési út van szükség, egy gyűjtemény indexe típusú. Megadhatja, hogy az elérési út adatok, a várt lekérdezés munkaterhelés és a numerikus vagy karakterlánc "pontosság." index típusa
* **Index frissítési módok konfigurálása**. Az Azure Cosmos DB három indexelési módot támogat: egységes, lassú, és "nincs". Az indexelő módok keresztül az indexelési házirendet egy Azure Cosmos DB gyűjteményt konfigurálhatók. 

A Microsoft .NET következő kódrészletet bemutatja, hogyan állítható be egy egyéni indexelési házirendet, ha létrehoz egy gyűjteményt. Ebben a példában azt állítja be a házirendet egy karakterláncok és számok tartományindexszel rendelkező, a maximális pontosság. Ezzel a házirend-karakterláncok ORDER BY lekérdezések végrehajtásához használható.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Az indexelési házirendet REST API verziója 2015-06-03 megjelenésével megváltozott a JSON-séma. Hogy a kiadástól kezdve a JSON-séma házirend indexeléshez karakterláncok tartomány indexek támogatja. .NET SDK 1.2.0 és a Java, Python, és a Node.js SDK-k 1.1.0-ás támogatja az új házirend-séma. Az SDK korábbi verziói a REST API-t 2015-04-08 verzióját használja. A korábbi séma támogatja az indexelési házirendet.
> 
> Alapértelmezés szerint Azure Cosmos DB indexeli a dokumentumok kapcsolatikarakterlánc-tulajdonságokat következetesen a kivonat indexszel rendelkező. Az indexek dokumentumok belüli összes numerikus tulajdonság következetesen a tartományindexszel rendelkező.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Az indexelési házirendet a portál testreszabása

Az indexelési házirendet a gyűjtemény az Azure-portálon módosíthatja: 

1. A portálon nyissa meg a Azure Cosmos DB-fiókjába, és válassza ki a gyűjteményt. 
2. Válassza ki a bal oldali navigációs menü **beállítások**, majd válassza ki **indexelő házirend**. 
3. A **indexelő házirend**, módosítsa az indexelési házirendet, és válassza **OK**. 

### Az indexelő adatbázis-mód <a id="indexing-modes"></a>  
Azure Cosmos-adatbázis támogatja az indexelési házirendet egy Azure Cosmos DB gyűjteményen keresztül konfigurálható három indexelési módot: egységes, lassú, és "nincs".

**Egységes**: Ha egy Azure Cosmos DB gyűjtési Consistent, egy adott Azure Cosmos DB gyűjtemény lekérdezései hajtsa végre a konzisztencia szintjét a pont olvasása megadott (erős, kötött elavulás, munkamenet és végleges). Az index frissítése a dokumentum frissítéssel (insert, replace, update és delete egy dokumentumot egy Azure Cosmos DB gyűjteményben) szinkron módon történik.

Egységes indexelő használ egy lehetséges csökkentése érdekében következetes lekérdezéseket támogat, az írási teljesítmény. A csökkentési, a függvény az egyedi elérési utak indexelése igénylő és a "konzisztenciaszint." Egységes indexelő módban készült "write gyorsan, azonnal lekérdezés" munkaterhelések.

**Lusta**: az index frissítése aszinkron módon ha egy Azure Cosmos DB gyűjteményt videokártyának, ez azt jelenti, hogy ha a gyűjtemény átviteli sebesség nem teljes kihasználását felhasználói kérelem kiszolgálására.  Vegye figyelembe, hogy inkonzisztens eredményeket kaphat, mert az adatok okozhatnak és indexelt lassan. Ez azt jelenti, hogy a COUNT lekérdezés vagy meghatározott lekérdezési eredmények lehet, hogy nem konzisztens vagy repeatable, megadott idő. 

Az index általában feldolgozott adatokkal utólagos módban van. Az indexelő Lazy idő Élettartam (TTL) változik éppen eltávolítja, majd újból létrehozza az indexet eredményez. Így az a szám és a lekérdezési eredmények inkonzisztens egy ideig. A legtöbb Azure Cosmos DB fiókok a konzisztens indexelési módot kell használniuk.

**Nincs**: olyan gyűjtemény, amelyikhez nincs index mód nincs társítva index tartozik. Ez általában akkor használható, ha Azure Cosmos DB egy kulcs-érték tárolóként szolgál, és dokumentumok csak az ID tulajdonság által elért. 

> [!NOTE]
> A meglévő index eldobása mellékhatása konfigurálása az indexelési házirendet nincs rendelkezik. Akkor használja, ha a hozzáférési minták csak azonosító szükséges, vagy önálló hivatkozásra.
> 
> 

Az alábbi táblázat az indexelési üzemmód (Consistent és Lazy) konfigurálva ahhoz a gyűjteményhez, és a lekérdezés kéréshez megadott konzisztenciaszint alapuló lekérdezések konzisztencia. Ez vonatkozik semmilyen felhasználói felületének használatával végzett lekérdezések: REST API-SDK-k, vagy a tárolt eljárások és eseményindítók. 

|Konzisztencia|Az indexelő mód: konzisztens|Az indexelő mód: Lusta|
|---|---|---|
|Erős|Erős|Végleges|
|A kötött elavulási|A kötött elavulási|Végleges|
|Munkamenet|Munkamenet|Végleges|
|Végleges|Végleges|Végleges|

Azure Cosmos-adatbázis nincs mód indexelő rendelkező gyűjtemények végzett lekérdezések hibát ad vissza. Lekérdezések továbbra is hajtható végre, mert keresztül explicit vizsgálatok **x-ms-documentdb-enable-vizsgálat** fejléc a következő a REST API-t vagy a **EnableScanInQuery** beállítás kérése a .NET SDK használatával. A vizsgálatok nem támogatottak bizonyos lekérdezési funkciók – például ORDER BY **EnableScanInQuery**.

Az alábbi táblázatban láthatók az indexelési mód (Consistent Lazy és None) alapuló lekérdezések konzisztencia amikor **EnableScanInQuery** van megadva.

|Konzisztencia|Indexelő mód: konzisztens|Indexelő mód: Lusta|Indexelő mód: nincs|
|---|---|---|---|
|Erős|Erős|Végleges|Erős|
|A kötött elavulási|A kötött elavulási|Végleges|A kötött elavulási|
|Munkamenet|Munkamenet|Végleges|Munkamenet|
|Végleges|Végleges|Végleges|Végleges|

A következő kód a minta megjelenítése a .NET SDK használatával következetes indexelő az összes dokumentum Beszúrások hogyan hozzon létre egy Azure Cosmos DB gyűjteményt.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Index elérési utak
Azure Cosmos-adatbázis a JSON-dokumentumokat és az index fákként modellek. A házirendek a fája módjának észlelheti. A dokumentumok kiválaszthatja a belefoglalása / kizárása indexelő elérési útjait. Ez kínálhat javított teljesítménye és alacsonyabb index tárolási forgatókönyvek, ahol a lekérdezési mintáknak előzetesen ismert.

Index elérési utak a legfelső szintű (/) kezdődnie, és általában végén a? helyettesítő karakteres operátor. Ez azt jelzi, hogy nincsenek-e az előtag több lehetséges értékei. Például VÁLASSZA kiszolgálására * a Families F WHERE F.familyName = "Andersen", meg kell adni egy index elérési útját /familyName/? a gyűjtemény indexe házirendben.

Index elérési utakat is használhatja a \* helyettesítő operátorral adja meg az elérési utak rekurzív módon az előtag szerinti viselkedését. Például/hasznos / * indexelő kizárását minden elemet a hasznos tulajdonság használható.

Az alábbiakban a megadja a index közös minták:

| Útvonal                | Leírás/használati eset                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | A gyűjtemény alapértelmezett elérési utat. Rekurzív és a teljes dokumentum fa vonatkozik.                                                                                                                                                                                                                                   |
| / prop /?             | Index elérési út a következő lekérdezések kiszolgálásához szükséges (a kivonatoló vagy tartomány típusával, illetve):<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop > 5<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop                                                                       |
| / prop / *             | A megadott címke az összes elérési utat index elérési útját. Az alábbi lekérdezéseket együttműködik<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop > 5<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop.nextprop = "érték"<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop         |
| [] / tulajdonságai / /?         | Index elérési út szükséges iterációs szolgálnak, és CSATLAKOZZON a skaláris értékeket tartalmazhat, például a ["a", "b", "c"] tömbök lekérdezéseket:<br><br>Válassza ki címke címke IN collection.props a WHERE címke = "érték"<br><br>A gyűjtemény c ILLESZTÉSI címke IN c.props VÁLASSZA címke ahol címke > 5                                                                         |
| [] /subprop/ /props/? | Index elérési iterációs kiszolgálásához szükséges, és az objektumok tömbök ILLESZTÉS küldött lekérdezésekre, például [{subprop: "a"}, {subprop: "b"}]:<br><br>Válassza ki címke címke IN collection.props a WHERE tag.subprop = "érték"<br><br>A gyűjtemény c ILLESZTÉSI címke IN c.props címke kiválasztása WHERE tag.subprop = "érték"                                  |
| / prop/subprop /?     | Index elérési lekérdezések kiszolgálásához szükséges (a kivonatoló vagy tartomány típusával, illetve):<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Ha úgy állítja be az egyéni index elérési utak, a következőket kell végrehajtania adhatja meg az alapértelmezett indexelési szabály a teljes dokumentum fa, különleges elérési megjelölt "/ *". 
> 
> 

A következő példa egy adott helyre tartományindexszel és 20 bájt egyéni pontosság érték konfigurálja:

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
Lehetősége van több útvonal az indexelési házirendet konfigurálásakor. Megadhatja, hogy egy vagy több indexelési definíciót minden az elérési úthoz:

* **Adattípus**: karakterlánc, szám, pont, sokszög vagy LineString (tartalmazhat adatok típusonként és példányonként elérési út csak egy bejegyzés).
* **Milyen index**: kivonatoló (egyenlőség lekérdezések), a tartomány (egyenlőség, tartomány vagy ORDER BY lekérdezések) vagy a Spatial (térbeli lekérdezéseket).
* **Pontosság**: egy kivonatoló az index, ez az érték 1-8 karakterláncok és a számok. Az alapértelmezett érték 3. Tartomány index az érték lehet -1 (maximális pontosság). 1 és 100 (maximális pontosság) karakterlánc- vagy számértékeknek a munkafüzet változhat.

#### <a name="index-kind"></a>Index típusa
Azure Cosmos DB támogatja kivonatoló index és a tartomány index különböző minden útvonalat, amelyet a karakterlánc vagy szám adattípusok konfigurálhatók, vagy mindkettőt.

* **Kivonatoló** hatékony egyenlőség és JOIN lekérdezéseket támogat. Kivonatindexek használata esetek többségében az alapértelmezett érték 3 bájt-nál nagyobb pontosságú nincs szükség. Az adattípus karakterlánc vagy szám lehet.
* **Tartomány** hatékony egyenlőség lekérdezéseket, a lekérdezések támogat (használatával >, <>, =, < =,! =), és ORDER BY lekérdezések. ORDER By lekérdezések alapértelmezés szerint is szükség lehet index maximális pontosság (-1). Az adattípus karakterlánc vagy szám lehet.

Azure Cosmos-adatbázis a is támogatja. a térbeli index jellegű minden elérési utat, amely a pont, sokszög vagy LineString adattípusok adható meg. Az érték a megadott elérési úton kell lennie mint érvényes GeoJSON töredéket `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Térbeli** támogatja a hatékony térbeli (belül és távolság) lekérdezések. Az adattípus lehet pont, sokszög vagy LineString.

> [!NOTE]
> Azure Cosmos DB támogatja a pont, sokszög és LineString adattípusok automatikus indexeléshez.
> 
> 

Az alábbiakban a támogatott index különböző és példákat a lekérdezéseiben, amelyeket el kiszolgálásához használt:

| Index típusa | Leírás/használati eset                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Kivonat       | Kivonatoló keresztül/prop /? (vagy /) segítségével hatékonyan tudja szolgálni az alábbi lekérdezéseket:<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Kivonatoló keresztül/tulajdonságai / [] /? (és / vagy/tulajdonságai /) segítségével hatékonyan tudja szolgálni az alábbi lekérdezéseket:<br><br>A gyűjtemény c ILLESZTÉSI címke IN c.props címke kiválasztása WHERE címke = 5                                                                                                                       |
| Tartomány      | Tartomány/prop/keresztül? (vagy /) segítségével hatékonyan tudja szolgálni az alábbi lekérdezéseket:<br><br>Válassza ki a gyűjtemény-c WHERE c.prop = "érték"<br><br>Válassza ki a gyűjtemény-c WHERE c.prop > 5<br><br>Válassza ki a gyűjtemény c ORDER BY c.prop                                                                                                                                                                                                              |
| Térbeli     | Tartomány/prop/keresztül? (vagy /) segítségével hatékonyan tudja szolgálni az alábbi lekérdezéseket:<br><br>Válassza ki a gyűjtemény c<br><br>HOL ST_DISTANCE (c.prop, {"type": "Terjesztésipont-", "koordináták": [0.0, 10.0]}) < 40<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN(c.prop, {"type": "Polygon",...}) – a pont is engedélyezve van az indexelő<br><br>Válassza ki a gyűjtemény c ahol ST_WITHIN({"type": "Point",...}, c.prop)--a sokszög engedélyezve van az indexelő              |

Alapértelmezés szerint hibát ad vissza, mint a tartomány operátorok tartalmazó lekérdezések a > =, ha nincs (a bármely pontosság) tartomány index jelezze, hogy a vizsgálatok akkor lehet szükség, a lekérdezés kiszolgálásához. A tartományindexszel nélkül is végrehajthatók lekérdezések segítségével a **x-ms-documentdb-enable-vizsgálat** fejléc a következő a REST API-t vagy a **EnableScanInQuery** beállítás kérése a .NET SDK használatával. Ha a lekérdezést, hogy Azure Cosmos DB használatával az index alapján szűrni összes többi szűrőt, nem a hibaüzenet.

Ugyanazok a szabályok vonatkoznak a térbeli lekérdezéseket. Alapértelmezés szerint hibát ad vissza térbeli lekérdezésekhez, ha nincs térbeli index, és nincsenek egyéb szűrők az indexből szolgáltatható. Akkor is lehet elvégezni, mivel a vizsgálat használatával **x-ms-documentdb-enable-vizsgálat** vagy **EnableScanInQuery**.

#### <a name="index-precision"></a>Index pontosság
Index pontosság segítségével ellenőrizze az index tárolási terhelés és a lekérdezési teljesítmény közötti kompromisszumot. A számok azt javasoljuk, -1 (maximális) alapértelmezett pontosság konfigurációját. Mivel számok 8 bájt a JSON-ban, ez megegyezik egy 8 bájtos konfigurációt. Például az 1 – 7, a pontosság alacsonyabb értéket válasszon azt jelenti, hogy az egyes tartományokon belül értékek leképezése azonos index bejegyzés. Ezért index tárolóhely csökkenti, de a lekérdezés-végrehajtás kell feldolgozni a további dokumentumokat. Következésképpen a kérelemegység további átviteli elfoglalja.

Index pontosság konfigurációs karakterlánc címtartományai több alkalmazás van. Karakterláncok bármilyen tetszőleges hosszúságú lehet, mert az index pontosság választása hatással lehetnek karakterlánc lekérdezések teljesítményét. Akkor is előfordulhat, hogy hatással a index tárolóhely szükséges. 1 és 100 vagy -1 (maximális) karakterlánc tartomány indexek konfigurálható. Ha azt szeretné, karakterlánc tulajdonságai ORDER BY lekérdezések végrehajtásához, meg kell adnia egy-1 -nek megfelelő útvonalaira pontosságát.

A térbeli indexek mindig használja az alapértelmezett index pontosság mindenfajta (Point, LineString és sokszög). Az alapértelmezett index pontosság térbeli indexek nem bírálható felül. 

A következő példa bemutatja, hogyan .NET SDK használatával egy gyűjtemény tartomány indexek pontosság növeléséhez. 

**Hozzon létre egy egyéni index pontosság gyűjteményt**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB hibát ad vissza, ha a lekérdezés ORDER BY használja, de nem rendelkezik a lekérdezett útvonalat, ahol a maximális pontosság elleni tartomány indexszel. 
> 
> 

Hasonlóképpen, akkor is teljesen elérési utak kizárása a indexelő. A következő példa bemutatja, hogyan kizárása a dokumentumok egy teljes szakaszáról (egy *részfa*) használatával indexelésének a \* helyettesítő operátor.

    var excluded = new DocumentCollection { Id = "excludedPathCollection" };
    excluded.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    excluded.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Részt vevő és tilthatják le az indexelő
Kiválaszthatja, hogy kívánja-e a gyűjtemény összes dokumentumot indexeléséhez. Alapértelmezés szerint minden dokumentumok automatikusan indexeli ugyan, de bármikor kikapcsolhatják az automatikus indexeléshez. Ha indexelés ki van kapcsolva, dokumentumokat csak a is elérhetők az erre az önhivatkozások vagy a dokumentum segítségével lekérdezések azonosítóját.

Az automatikus indexeléshez ki van kapcsolva, az index csak bizonyos dokumentumokhoz szelektív továbbra is hozzáadhat. Ezzel ellentétben automatikus az indexelő hagyhatja, és szelektív dönt, hogy bizonyos dokumentumokhoz kizárása. Be-és kikapcsolása konfigurációk indexelő akkor hasznos, ha csak egy részhalmazát, amelyet a lekérdezendő dokumentumok rendelkezik.

A következő példa bemutatja, hogyan lehet például egy dokumentum explicit módon használatával a [SQL API .NET SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) és a [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) tulajdonság.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Az indexelési házirendet gyűjtemény módosítása
Az Azure Cosmos Adatbázisba a parancsprogramok gyűjtemény az indexelési házirendet módosításokat végezheti el. Indexelési házirendet egy Azure Cosmos DB gyűjteményt a változás az index az alakzat egy változást vezethet. A módosítás befolyásolja a indexelhetők elérési utak, a pontosság és a konzisztencia-modell magát az index. A házirend hatékonyan indexelő módosítják végrehajtásához egy új indexbe a régi index. 

**Online indexkészítés átalakítások**

![Indexelő működése – Azure Cosmos DB online indexkészítés átalakítások](./media/indexing-policies/index-transformations.png)

Index átalakításokat tesz online. Ez azt jelenti, hogy a régi házirend egy indexelt dokumentumok hatékonyan átalakítaná e az új házirend *anélkül, hogy befolyásolná a írási rendelkezésre állás vagy a kiosztott átviteli sebesség* a gyűjtemény. A konzisztenciájának olvasási és írási műveletet a REST API-t, SDK-k használatával végrehajtott vagy belül tárolt eljárások és eseményindítók nem érinti index átalakítása során. Nincs teljesítménycsökkenés vagy az alkalmazásokhoz állásidő meg, hogy egy indexelési házirendet, módosítsa.

Azonban az index átalakítása folyamatban a időszak lekérdezések nem idővel konzisztenssé, függetlenül a indexelési üzemmód (azonos vagy Lazy). Ugyanez vonatkozik a lekérdezések bármely interfész: REST API-SDK-k, és a tárolt eljárások és eseményindítók. Csakúgy, mint az indexelő, Lazy index átalakítása van aszinkron módon történik a háttérben a replikákon szerint történik tartalék erőforrást, amely egy adott replika érhető el. 

Index átalakítások helyen is. Azure Cosmos-adatbázis nem másolatait a két az index és a lapozófájl-kapacitás el a régi index az újjal. Ez azt jelenti, hogy nincs további lemezterület szükséges vagy felhasznált a gyűjteményekben, míg az index átalakítások történik.

Ha megváltoztatja az indexelési házirendet, a módosításai érvényesek lesznek a régi index áthelyezése az új elsősorban az indexelési mód konfigurációk alapján. Az indexelő mód konfigurációk mint például a/kizárt elérési utak, index különböző és szükséges egyéb értékek nagyobb szerepet játszanak. 

Ha a régi és az új házirendek mindkét konzisztens indexelő, az Azure Cosmos DB egy online indexkészítés átalakítást hajt végre. Egy másik indexelési házirendkezelés, amelynél az egységes indexelési mód, amíg folyamatban van az átalakítás nem alkalmazható. Azonban áthelyezheti Lazy vagy nincs folyamatban van az átalakítás során az indexelő mód: 

* Amikor Lazy helyezi át, az index házirend módosítása azonnal hatékony. Azure Cosmos DB elindul, hozza létre újra az index aszinkron módon történik. 
* Nincs helyezi át, ha az index azonnal megszakad. Nincs áthelyezése akkor hasznos, ha szakítsa meg a folyamatban lévő átalakulása, és indítsa el a különböző indexelési házirendet az alapoktól szeretne. 

A következő kódrészletet bemutatja, hogyan lehet módosítani egy gyűjtemény indexelési házirendet-konzisztens indexelési üzemmódról lusta indexelési módra. Ha a .NET SDK használata esetén is indítsa el az indexelési házirend módosítása az új **ReplaceDocumentCollectionAsync** metódust.

**Módosítsa Lusta Consistent az indexelési házirendet**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Index átalakítása előrehaladását úgy követheti nyomon**

Nyomon követheti a az index átalakítás előrehaladás százalékosan kifejezve konzisztens index használatával a **IndexTransformationProgress** válasz tulajdonságot egy **ReadDocumentCollectionAsync** hívható meg. Más SDK, és a REST API támogatja egyenértékű tulajdonságai és metódusai indexelési házirend módosítása. Ellenőrizheti a konzisztens indexhez index átalakulása előrehaladását meghívásával **ReadDocumentCollectionAsync**: 

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
> * A **IndexTransformationProgress** tulajdonsága alkalmazható csak egy egységes indexhez átalakításakor. Használja a **ResourceResponse.LazyIndexingProgress** nyomon követése átalakítások Lusta indexhez tulajdonság.
> * A **IndexTransformationProgress** és a **LazyIndexingProgress** tulajdonságok fel van töltve, csak a nem particionált gyűjtemény, ez azt jelenti, hogy gyűjteménye, amely nem tartozik partíciós kulcs jön létre.
>

Egy gyűjtemény indexe a nincs mód indexelő áthelyezésével dobhatja el. Ez akkor lehet hasznos működési eszköz, ha azt szeretné, szakítsa meg a folyamatban lévő átalakulása, és azonnal nekiláthasson egy új.

**Egy gyűjtemény index dobható el**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Ha szeretné módosítja az indexelő házirend az Azure Cosmos DB gyűjtemények? A leggyakoribb használati esetek a következők:

* Konzisztens eredmények szolgálnak a normál működés során, ugyanakkor tartalék megoldásként lusta indexelési módra tömeges adatok importálása során.
* Indítsa el a jelenlegi Azure Cosmos DB gyűjteményre új indexelési funkciókat használ. Például használja, földrajzi kérdez le, ami megköveteli, hogy a térbeli index jellegű vagy ORDER BY / lekérdezések, a tartomány index típusú karakterlánc igénylő karakterlánc.
* Válassza ki az aktuális indexelése tulajdonságait, és módosítsa őket adott idő alatt.
* Az indexelő pontosság lekérdezések teljesítményének növelése vagy csökkentése felhasznált tárhely hangolását.

> [!NOTE]
> Indexelési házirendet a módosítandó **ReplaceDocumentCollectionAsync**, 1.3.0 vagy újabb verziója a .NET SDK kell használnia.
> 
> Az index átalakításhoz sikeresen befejeződik győződjön meg arról, hogy nincs elég szabad tárterület érhető el a gyűjtemény. Ha a gyűjtemény eléri a tárolási kvótát, az index átalakítás fel van függesztve. Index átalakítása automatikusan folytatja tárolási hely esetén érhető el, például egyes dokumentumok törlésekor.
> 
> 

## <a name="performance-tuning"></a>Teljesítmény-finomhangolás
Az SQL API-k teljesítménymutatók, például a használt index tárhely és minden művelet átviteli költsége (kérelemegység) kapcsolatos információkkal szolgálnak. Ezen információk használatával összehasonlítása különböző indexelési szabályzatok, és a teljesítményhangolás.

A tárolási kvótát és használati gyűjtemény ellenőrzéséhez futtassa a **HEAD** vagy **beolvasása** kérelmet a gyűjtemény-erőforráshoz. Ezt követően vizsgálja meg a **x-ms-kérelem-kvóta** és a **x-ms-kérelem-használati** fejléceket. A .NET SDK-ban a [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) és [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) tulajdonságok [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) tartalmaznia kell a megfelelő értékeket.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


A terhelést növelni az indexelő minden egyes írási műveletnél a mérésére (létrehozása, frissítése vagy törlése), vizsgálja meg a **x-ms-kérelem-kell fizetni** fejléc (vagy egyenértékű [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) tulajdonság [ ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) a .NET SDK-ban), amelyeket a rendszer használni ezeket a műveleteket kérelem egységek számának mérésére.

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
A sémában az indexelési házirendet változása 2015. július 7, a REST API verziója 2015-06-03 jelent meg. A megfelelő osztályokhoz SDK verziókban új megvalósítások felel meg a sémának a tartozik. 

A JSON-specifikáció bevezetett a következő módosításokat:

* Tartomány indexek házirend indexelő támogatja a karakterláncokat.
* Mindegyik elérési út lehet több index definíciója. Egy, az egyes adattípusokhoz rendelkezhet.
* 1 és 8 számok, 1 és 100 karakterláncok, illetve -1 (maximális pontosság) pontosság indexelő támogatja.
* Elérési út szegmensek nem feltétlenül szükséges egy idézőjel karaktert az egyes elérési utakat. Például hozzáadhat egy elérési utat   **/cím /?** Ahelyett, hogy **/ "title" /?**.
* Az útvonalgyökér "görbékhez" jelölő ábrázolhatók **/ \*** (kívül **/**).

Ha rendelkezik kódot, hogy rendelkezések gyűjtemények a .NET SDK 1.1.0-ás verzióját vagy régebbi verziójú készült egyéni indexelési házirendet, áthelyezése SDK verzió 1.2.0, módosítania kell az alkalmazás kódjában, ezek a változások kezelésére. Ha még nem rendelkezik a kódot, hogy konfigurálja az indexelési házirendet, vagy ha azt tervezi, továbbra is az SDK-t egy korábbi verzióját használja, nem módosításokra szükség.

Gyakorlati összehasonlítása Ez egy példa egy egyéni indexelési házirendet használatával írt REST API verziója 2015-06-03, ugyanazt az indexelési házirendet a korábbi verziót 2015-04-08 REST API használatával írt követ.

**A jelenlegi indexelő házirend JSON (REST API-verzió: 2015-06-03)**

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


**Korábban a házirend JSON (REST API-verzió: 2015-04-08) indexelő**

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
Index házirend felügyeleti mintákat és tudhat meg többet az Azure Cosmos DB lekérdező nyelv tekintse meg a következőket:

* [Az SQL API .NET index felügyeleti mintakódok](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Az SQL API REST adatgyűjtési műveletek](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Az SQL lekérdezés](sql-api-sql-query.md)


---
title: Azure Cosmos DB indexelési házirendek
description: Megtudhatja, hogyan konfigurálhatja és módosíthatja az alapértelmezett indexelési házirendet az automatikus indexeléshez és a Azure Cosmos DB nagyobb teljesítményéhez.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 886d17098259ddbb78698a3c1280f797e370c714
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597162"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Szabályzatok indexelése a Azure Cosmos DBban

Azure Cosmos DB minden tárolóhoz tartozik egy indexelési házirend, amely azt határozza meg, hogy a tároló elemei hogyan legyenek indexelve. Az újonnan létrehozott tárolók alapértelmezett indexelési házirendje minden elem összes tulajdonságát indexeli, és minden karakterlánc vagy szám esetén a tartomány indexeit, valamint a GeoJSON objektum térbeli indexeit is kikényszeríti. Ez lehetővé teszi a nagy lekérdezési teljesítmény megszerzését anélkül, hogy az indexelést és az indexelést előre kellene gondolni.

Bizonyos helyzetekben érdemes lehet felülbírálni ezt az automatikus viselkedést, hogy jobban megfeleljen az igényeinek. Az indexelési *mód*beállításával testre szabhatja a tároló indexelési házirendjét, és belefoglalhatja vagy kizárhatja a *Tulajdonságok elérési útját*.

> [!NOTE]
> A cikkben ismertetett indexelési szabályzatok frissítésének módszere csak a Azure Cosmos DB SQL (Core) API-ra vonatkozik.

## <a name="indexing-mode"></a>Indexelési mód

A Azure Cosmos DB két indexelési módot támogat:

- **Konzisztens**: az indexet a rendszer szinkron módon frissíti az elemek létrehozásakor, frissítésekor vagy törlésekor. Ez azt jelenti, hogy az olvasási lekérdezések konzisztenciája lesz a [fiókhoz konfigurált konzisztencia](consistency-levels.md).
- **Nincs**: az indexelés le van tiltva a tárolón. Ez általában akkor használatos, ha egy tárolót tiszta kulcs-érték tárolóként használ a másodlagos indexek szükségessége nélkül. Emellett a tömeges műveletek teljesítményének javítására is használható. A tömeges műveletek befejezését követően az index mód beállítható Konzisztensre, majd a [IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) a befejezésig figyelhető.

> [!NOTE]
> A Cosmos DB a lusta indexelési módot is támogatja. A lusta indexelés sokkal alacsonyabb prioritási szinten végzi el az index frissítését, ha a motor nem végez semmilyen más munkát. Ez **inkonzisztens vagy hiányos** lekérdezési eredményeket eredményezhet. Emellett a tömeges műveletek esetében a "None" helyett a lusta indexelés használata sem biztosít előnyt, mert az index mód bármilyen módosítása az index eldobását és újbóli létrehozását eredményezi. Ezen okok miatt javasoljuk, hogy az ügyfeleket használja. A tömeges műveletek teljesítményének növeléséhez állítsa az index módot a none értékre, majd térjen vissza konzisztens módba, és figyelje a tároló `IndexTransformationProgress` tulajdonságát a befejezésig.

Alapértelmezés szerint az indexelési házirend `automatic` értékre van állítva. Ez úgy érhető el, hogy az indexelési házirendben az `automatic` tulajdonságot `true`re állítja. Ha ezt a tulajdonságot úgy állítja be, hogy `true`, az Azure CosmosDB automatikusan indexeli a dokumentumokat írásuk szerint.

## <a name="including-and-excluding-property-paths"></a>Tulajdonságok elérési útjának belefoglalása és kizárása

Az egyéni indexelési házirend megadhatja az indexelésből explicit módon befoglalt vagy kizárt tulajdonságokat. Az indexelt elérési utak számának optimalizálásával csökkentheti a tároló által használt tárterület méretét, és javíthatja az írási műveletek késését. Ezek az elérési utak az [indexelés áttekintése szakaszban leírt módszer](index-overview.md#from-trees-to-property-paths) szerint vannak meghatározva, a következő kiegészítésekkel:

- egy skaláris értékhez (sztringhez vagy számhoz) vezető elérési út végződik `/?`
- egy tömb elemeit a `/[]` jelöléssel együtt kell kezelni (nem `/0`, `/1` stb.).
- a `/*` helyettesítő karakterrel a csomópont alatti elemek is megegyeznek

Ugyanezt a példát még egyszer kell megtennie:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- a `headquarters` `employees` elérési útja `/headquarters/employees/?`

- a `locations` "`country` elérési útja `/locations/[]/country/?`

- a `headquarters` alatt található bármilyen elérési út `/headquarters/*`

Például belefoglalhatjuk a `/headquarters/employees/?` elérési utat. Ez az elérési út biztosítaná, hogy indexeljük az alkalmazottak tulajdonságot, de nem indexelik a további beágyazott JSON-t ezen a tulajdonságon belül.

## <a name="includeexclude-strategy"></a>Belefoglalási/kizárási stratégia

Az indexelési házirendnek tartalmaznia kell a gyökér elérési útját `/*` vagy egy belefoglalt vagy kizárt elérési utat.

- Adja meg a gyökér elérési útját az indexelni nem kívánt elérési utak szelektív kizárásához. Ez az ajánlott módszer, mivel lehetővé teszi, hogy Azure Cosmos DB proaktív módon indexelje a modellbe felvehető új tulajdonságokat.
- Zárja ki a gyökér elérési útját, hogy szelektíven szerepeljenek az indexelni kívánt elérési utak.

- A normál karaktereket tartalmazó elérési utak esetében: alfanumerikus karakterek és _ (aláhúzás), nem kell elmenekülnie az elérésiút-karakterláncot idézőjelek közé (például "/Path/?"). Más speciális karaktereket tartalmazó elérési utak esetén el kell kerülnie az elérésiút-karakterláncot az idézőjelek között (például: "/\"path-ABC \"/?"). Ha az elérési úton speciális karaktereket vár, a biztonság érdekében minden elérési utat eljuthat. Funkcionálisan nem tesz különbséget, ha minden elérési utat és csak a speciális karaktereket tartalmazó útvonalat használ.

- A "ETAG" rendszertulajdonság alapértelmezés szerint ki van zárva az indexelésből, kivéve, ha a ETAG hozzá lett adva a befoglalt útvonalhoz az indexeléshez.

Az útvonalak belefoglalása és kizárása esetén a következő attribútumok jelenhetnek meg:

- `kind` lehet `range` vagy `hash`. A Range index funkció biztosítja a kivonatoló index összes funkcióját, ezért javasoljuk, hogy használjon egy tartomány-indexet.

- `precision` a befoglalt elérési utak index szintjén definiált szám. @No__t_0 értéke a maximális pontosságot jelzi. Javasoljuk, hogy mindig állítsa be ezt az értéket `-1`ra.

- `dataType` lehet `String` vagy `Number`. Ez azt jelzi, hogy milyen típusú JSON-tulajdonságok lesznek indexelve.

Ha nincs megadva, ezek a tulajdonságok a következő alapértelmezett értékek lesznek:

| **Tulajdonság neve**     | **Alapértelmezett érték** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` és `Number` |

Tekintse meg [ezt a szakaszt](how-to-manage-indexing-policy.md#indexing-policy-examples) az indexelési házirend példáinak megjelenítéséhez, beleértve az elérési utakat is.

## <a name="spatial-indexes"></a>Térbeli indexek

Ha a térbeli elérési utat definiálja az indexelési házirendben, meg kell határoznia, hogy melyik indexet kell alkalmazni ```type``` az adott elérési útra. A térbeli indexek lehetséges típusai a következők:

* Pont

* Sokszög

* MultiPolygon

* LineString

Alapértelmezés szerint a Azure Cosmos DB nem hoz létre térbeli indexeket. Ha a térbeli SQL beépített funkcióit szeretné használni, hozzon létre egy térbeli indexet a szükséges tulajdonságokkal. A térbeli indexek hozzáadására vonatkozó példákat a következő [szakaszban](geospatial.md) találja: indexelési házirend.

## <a name="composite-indexes"></a>Összetett indexek

A két vagy több tulajdonsággal rendelkező `ORDER BY` záradékkal rendelkező lekérdezések összetett indexet igényelnek. Megadhat egy összetett indexet is, amellyel javítható számos Esélyegyenlőségi és tartományon belüli lekérdezés teljesítménye. Alapértelmezés szerint a rendszer nem definiál összetett indexeket, ezért szükség szerint [összetett indexeket kell hozzáadnia](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) .

Az összetett index meghatározásakor a következőket kell megadnia:

- Két vagy több tulajdonság elérési útja. Az a sorrend, amelyben a tulajdonságok elérési útjai definiálva vannak.

- A sorrend (növekvő vagy csökkenő).

> [!NOTE]
> Összetett index hozzáadásakor a lekérdezés a meglévő tartomány-indexeket fogja használni, amíg az új összetett index hozzáadása be nem fejeződik. Ezért összetett index hozzáadásakor előfordulhat, hogy nem veszi figyelembe azonnal a teljesítménnyel kapcsolatos fejlesztéseket. Az indexek átalakításának előrehaladását az SDK-k [egyikével](how-to-manage-indexing-policy.md)követheti nyomon.

### <a name="order-by-queries-on-multiple-properties"></a>Rendezés több tulajdonság lekérdezésével:

A következő szempontokat kell használni, ha összetett indexeket használ `ORDER BY` záradékkal rendelkező lekérdezésekhez két vagy több tulajdonsággal:

- Ha az összetett index elérési útjai nem egyeznek a `ORDER BY` záradékban szereplő tulajdonságok sorrendjével, akkor az összetett index nem támogatja a lekérdezést.

- Az összetett index elérési útjának (növekvő vagy csökkenő) sorrendjének meg kell egyeznie a `ORDER BY` záradékban szereplő `order`ával is.

- Az összetett index egy `ORDER BY` záradékot is támogat az ellenkező sorrendben az összes elérési úton.

Vegye figyelembe a következő példát, ahol összetett index van definiálva a tulajdonságok neve, kora és _ts:

| **Összetett index**     | **Példa `ORDER BY` lekérdezésre**      | **Támogatja az összetett index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Az indexelési szabályzatot testre szabhatja, hogy kiszolgálja az összes szükséges `ORDER BY` lekérdezést.

### <a name="queries-with-filters-on-multiple-properties"></a>A több tulajdonság alapján szűrt lekérdezések

Ha egy lekérdezés két vagy több tulajdonságon is tartalmaz szűrőket, hasznos lehet összetett indexet létrehozni ezekhez a tulajdonságokhoz.

Vegyük például a következő lekérdezést, amely egy két tulajdonságra vonatkozó esélyegyenlőségi szűrővel rendelkezik:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Ez a lekérdezés hatékonyabb lesz, kevesebb időt vesz igénybe, és kevesebb RU-t használ, ha a (z) (név ASC, Age ASC) összetett indexet képes kihasználni.

A tartományhoz tartozó szűrőkkel rendelkező lekérdezések összetett indexszel is optimalizálható. A lekérdezés azonban csak egyetlen tartományból álló szűrőt tartalmazhat. A tartományhoz tartozó szűrők közé tartoznak a `>`, a `<`, a `<=`, a `>=` és a `!=`. A tartomány szűrőt az összetett indexben kell megadni.

Vegye figyelembe a következő lekérdezést mind az esélyegyenlőségi, mind a tartományos szűrőkkel:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Ez a lekérdezés hatékonyabb lesz egy összetett indexszel (név ASC, Age ASC). A lekérdezés azonban nem használ összetett indexet (kor ASC, név ASC), mert az egyenlőségi szűrőket először az összetett indexben kell meghatározni.

A következő szempontokat kell figyelembe venni összetett indexek létrehozásához több tulajdonság szűrőit használó lekérdezésekhez

- A lekérdezés szűrője tulajdonságainak meg kell egyezniük az összetett indexben szereplő tulajdonságokkal. Ha egy tulajdonság az összetett indexben szerepel, de nem szerepel a lekérdezésben szűrőként, a lekérdezés nem fogja használni az összetett indexet.
- Ha egy lekérdezés olyan további tulajdonságokkal rendelkezik a szűrőben, amelyek nem lettek definiálva egy összetett indexben, akkor a lekérdezés kiértékeléséhez az összetett és a tartomány indexek kombinációját fogja használni a rendszer. Ehhez kevesebb RU-t kell használni, mint a csak a tartomány indexeket.
- Ha egy tulajdonság tartomány-szűrővel rendelkezik (`>`, `<`, `<=`, `>=` vagy `!=`), akkor ezt a tulajdonságot az összetett indexben kell megadni. Ha egy lekérdezés több tartományból álló szűrővel rendelkezik, nem fogja használni az összetett indexet.
- Összetett index létrehozásakor, ha több szűrőkkel optimalizálja a lekérdezéseket, az összetett index `ORDER` nem lesz hatással az eredményekre. Ez a tulajdonság nem kötelező.
- Ha nem definiál összetett indexet egy lekérdezéshez több tulajdonságra vonatkozó szűrőkkel, a lekérdezés továbbra is sikeres lesz. A lekérdezés RU-díja azonban egy összetett indexszel is csökkenthető.

Tekintse át az alábbi példákat, amelyekben összetett index van meghatározva a tulajdonságok neve, kora és időbélyeg:

| **Összetett index**     | **Példa lekérdezésre**      | **Támogatja az összetett index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>A szűrőt és egy ORDER BY záradékot is tartalmazó lekérdezések

Ha egy lekérdezés egy vagy több tulajdonságra vonatkozik, és az ORDER BY záradékban eltérő tulajdonságokkal rendelkezik, hasznos lehet a szűrőben szereplő tulajdonságokat felvenni a `ORDER BY` záradékba.

Ha például a szűrőben lévő tulajdonságokat hozzáadja a ORDER BY záradékhoz, a következő lekérdezés újraírható egy összetett index kihasználása érdekében:

Lekérdezés tartomány-index használatával:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Lekérdezés összetett index használatával:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Ugyanazokat a mintákat és lekérdezési optimalizálásokat lehet általánosítani a több egyenlőségi szűrővel rendelkező lekérdezések esetében:

Lekérdezés tartomány-index használatával:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Lekérdezés összetett index használatával:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Összetett indexek létrehozásakor a következő szempontokat kell használni egy szűrővel és `ORDER BY` záradékkal rendelkező lekérdezés optimalizálásához:

* Ha a lekérdezés a tulajdonságok alapján szűri a szűrőket, előbb szerepelnie kell a `ORDER BY` záradékban.
* Ha nem definiál összetett indexet egy olyan lekérdezéshez, amely egy tulajdonságra vonatkozó szűrővel rendelkezik, és külön `ORDER BY` záradékot használ egy másik tulajdonság használatával, a lekérdezés továbbra is sikeres lesz. A lekérdezés RU-díja azonban egy összetett indexszel is csökkenthető, különösen akkor, ha a `ORDER BY` záradékban található tulajdonság magas fokú.
* A több tulajdonsággal rendelkező `ORDER BY` lekérdezésekhez, valamint a több tulajdonság szűrőit tartalmazó lekérdezésekhez tartozó összetett indexek létrehozásával kapcsolatos megfontolások továbbra is érvényesek.


| **Összetett index**                      | **Példa `ORDER BY` lekérdezésre**                                  | **Támogatja az összetett index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Az indexelési szabályzat módosítása

Egy tároló indexelési házirendjét bármikor frissítheti [a Azure Portal vagy a támogatott SDK-k egyikével](how-to-manage-indexing-policy.md). Az indexelési házirend frissítése elindítja a régi indexről az új verzióra történő átalakítást, amely online és helyben történik (így a művelet során nincs szükség további tárolóhelyre). A régi szabályzat indexét a rendszer hatékonyan átalakítja az új szabályzatra anélkül, hogy az hatással lenne az írási rendelkezésre állásra vagy a tárolón kiosztott átviteli sebességre. Az index transzformáció egy aszinkron művelet, és a befejezéshez szükséges idő a kiépített átviteli sebességtől, az elemek számától és méretétől függ.

> [!NOTE]
> A tartomány vagy a térbeli index hozzáadásakor a lekérdezések nem adják vissza az összes egyező eredményt, így a hibák visszaküldése nélkül is megtörténnek. Ez azt jelenti, hogy előfordulhat, hogy a lekérdezési eredmények nem konzisztensek, amíg az index átalakítása be nem fejeződik. Az indexek átalakításának előrehaladását az SDK-k [egyikével](how-to-manage-indexing-policy.md)követheti nyomon.

Ha az új indexelési házirend mód konzisztens értékre van állítva, akkor a rendszer nem alkalmazhat más indexelési házirendet, amíg az index átalakítás folyamatban van. A futó index-transzformáció megszakítható úgy, hogy az indexelési házirend mód értékét a none értékre állítja (amely azonnal eldobja az indexet).

## <a name="indexing-policies-and-ttl"></a>Indexelési házirendek és TTL

Az élettartam [(TTL) funkció](time-to-live.md) használatához az indexelésnek aktívnak kell lennie a tárolón, amelyen be van kapcsolva. Ez azt jelenti, hogy:

- nem lehet aktiválni az ÉLETTARTAMot olyan tárolón, ahol az indexelési mód nincs értékre van állítva.
- az indexelési mód nem állítható be egyetlen olyan tárolón sem, amelyben az élettartam aktiválva van.

Olyan esetekben, ahol nem szükséges a tulajdonság elérési útjának indexelése, de az élettartam megadása kötelező, az indexelési házirendet a következővel használhatja:

- az indexelési mód konzisztens értékre van beállítva, és
- nincs belefoglalt elérési út, és
- `/*` az egyetlen kizárt elérési út.

## <a name="next-steps"></a>Következő lépések

Az indexeléssel kapcsolatban az alábbi cikkekben olvashat bővebben:

- [Indexelés – áttekintés](index-overview.md)
- [Az indexelési szabályzat kezelése](how-to-manage-indexing-policy.md)

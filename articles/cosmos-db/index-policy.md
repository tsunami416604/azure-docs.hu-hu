---
title: Azure Cosmos DB indexelési házirendek
description: Megtudhatja, hogyan konfigurálhatja és módosíthatja az alapértelmezett indexelési házirendet az automatikus indexeléshez és a Azure Cosmos DB nagyobb teljesítményéhez.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 86ac042bdddce36f00be71cc5109618bec909d90
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914174"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Szabályzatok indexelése a Azure Cosmos DBban

Azure Cosmos DB minden tárolóhoz tartozik egy indexelési házirend, amely azt határozza meg, hogy a tároló elemei hogyan legyenek indexelve. Az újonnan létrehozott tárolók alapértelmezett indexelési házirendje minden elem összes tulajdonságát indexeli, és minden karakterlánc vagy szám esetén a tartomány indexeit, valamint a GeoJSON objektum térbeli indexeit is kikényszeríti. Ez lehetővé teszi a nagy lekérdezési teljesítmény megszerzését anélkül, hogy az indexelést és az indexelést előre kellene gondolni.

Bizonyos helyzetekben érdemes lehet felülbírálni ezt az automatikus viselkedést, hogy jobban megfeleljen az igényeinek. Az indexelési *mód*beállításával testre szabhatja a tároló indexelési házirendjét, és belefoglalhatja vagy kizárhatja a *Tulajdonságok elérési útját*.

> [!NOTE]
> A cikkben ismertetett indexelési szabályzatok frissítésének módszere csak a Azure Cosmos DB SQL (Core) API-ra vonatkozik.

## <a name="indexing-mode"></a>Indexelési mód

A Azure Cosmos DB két indexelési módot támogat:

- **Konzisztens**: Ha egy tároló indexelési házirendje konzisztens értékre van állítva, az indexet a rendszer szinkron módon frissíti az elemek létrehozásakor, frissítésekor vagy törlésekor. Ez azt jelenti, hogy az olvasási lekérdezések konzisztenciája lesz a [fiókhoz konfigurált konzisztencia](consistency-levels.md).
- **Nincs**: Ha egy tároló indexelési házirendje a none értékre van állítva, az indexelés hatékonyan le van tiltva ezen a tárolón. Ez általában akkor használatos, ha egy tárolót tiszta kulcs-érték tárolóként használ a másodlagos indexek szükségessége nélkül. A tömeges beszúrási műveletek felgyorsítását is lehetővé teszi.

Emellett az indexelési házirendben az **automatikus** tulajdonságot **igaz**értékre kell állítani. Ha ez a tulajdonság TRUE (igaz) értékre van állítva, a Azure Cosmos DB automatikusan indexeli a dokumentumokat írásuk szerint.

## <a name="including-and-excluding-property-paths"></a>Tulajdonságok elérési útjának belefoglalása és kizárása

Az egyéni indexelési házirend megadhatja az indexelésből explicit módon befoglalt vagy kizárt tulajdonságokat. Az indexelt elérési utak számának optimalizálásával csökkentheti a tároló által használt tárterület méretét, és javíthatja az írási műveletek késését. Ezek az elérési utak az [indexelés áttekintése szakaszban leírt módszer](index-overview.md#from-trees-to-property-paths) szerint vannak meghatározva, a következő kiegészítésekkel:

- egy skaláris értékhez (sztringhez vagy számhoz) vezető útvonal végződik`/?`
- egy tömb elemeit a rendszer a `/[]` jelöléssel együtt tárgyalja (a `/1` `/0`helyett stb.).
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

- az `headquarters`elérésiútja `employees``/headquarters/employees/?`

- az `locations`elérésiút `country``/locations/[]/country/?`

- az elérési út `headquarters``/headquarters/*`

Például belefoglalhatjuk az `/headquarters/employees/?` elérési utat. Ez az elérési út biztosítaná, hogy indexeljük az alkalmazottak tulajdonságot, de nem indexelik a további beágyazott JSON-t ezen a tulajdonságon belül.

## <a name="includeexclude-strategy"></a>Belefoglalási/kizárási stratégia

Minden indexelési házirendnek tartalmaznia kell a gyökér `/*` elérési útját, vagy egy belefoglalt vagy kizárt elérési utat.

- Adja meg a gyökér elérési útját az indexelni nem kívánt elérési utak szelektív kizárásához. Ez az ajánlott módszer, mivel lehetővé teszi, hogy Azure Cosmos DB proaktív módon indexelje a modellbe felvehető új tulajdonságokat.
- Zárja ki a gyökér elérési útját, hogy szelektíven szerepeljenek az indexelni kívánt elérési utak.

- A normál karaktereket tartalmazó elérési utak esetében: alfanumerikus karakterek és _ (aláhúzás), nem kell elmenekülnie az elérésiút-karakterláncot idézőjelek közé (például "/Path/?"). Más speciális karaktereket tartalmazó elérési utak esetén el kell kerülnie az elérésiút-karakterláncot az idézőjelek között (például\": "/\"Path-ABC/?"). Ha az elérési úton speciális karaktereket vár, a biztonság érdekében minden elérési utat eljuthat. Funkcionálisan nem tesz különbséget, ha minden elérési utat és csak a speciális karaktereket tartalmazó útvonalat használ.

- A "ETAG" rendszertulajdonság alapértelmezés szerint ki van zárva az indexelésből, kivéve, ha a ETAG hozzá lett adva a befoglalt útvonalhoz az indexeléshez.

Az útvonalak belefoglalása és kizárása esetén a következő attribútumok jelenhetnek meg:

- `kind``range` lehet vagy `hash`. A Range index funkció biztosítja a kivonatoló index összes funkcióját, ezért javasoljuk, hogy használjon egy tartomány-indexet.

- `precision`a befoglalt elérési utakhoz tartozó index szintjén definiált szám. A érték a `-1` maximális pontosságot jelzi. Azt javasoljuk, hogy mindig állítsa be `-1`ezt az értéket.

- `dataType``String` lehet vagy `Number`. Ez azt jelzi, hogy milyen típusú JSON-tulajdonságok lesznek indexelve.

Ha nincs megadva, ezek a tulajdonságok a következő alapértelmezett értékek lesznek:

| **Tulajdonság neve**     | **Alapértelmezett érték** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` és `Number` |

Tekintse meg [ezt a szakaszt](how-to-manage-indexing-policy.md#indexing-policy-examples) az indexelési házirend példáinak megjelenítéséhez, beleértve az elérési utakat is.

## <a name="spatial-indexes"></a>Térbeli indexek

Ha a térbeli elérési utat definiálja az indexelési házirendben, meg kell ```type``` határoznia, hogy melyik indexet kell alkalmazni az adott elérési útra. A térbeli indexek lehetséges típusai a következők:

* Pont

* Sokszög

* MultiPolygon

* LineString

Alapértelmezés szerint a Azure Cosmos DB nem hoz létre térbeli indexeket. Ha a térbeli SQL beépített funkcióit szeretné használni, hozzon létre egy térbeli indexet a szükséges tulajdonságokkal. A térbeli indexek hozzáadására vonatkozó példákat a következő [szakaszban](geospatial.md) találja: indexelési házirend.

## <a name="composite-indexes"></a>Összetett indexek

A két vagy több tulajdonsággal rendelkező záradékkalrendelkezőlekérdezésekösszetettindexetigényelnek.`ORDER BY` Megadhat egy összetett indexet is, amellyel javítható számos Esélyegyenlőségi és tartományon belüli lekérdezés teljesítménye. Alapértelmezés szerint a rendszer nem definiál összetett indexeket, ezért szükség szerint [összetett indexeket kell hozzáadnia](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) .

Az összetett index meghatározásakor a következőket kell megadnia:

- Két vagy több tulajdonság elérési útja. Az a sorrend, amelyben a tulajdonságok elérési útjai definiálva vannak.

- A sorrend (növekvő vagy csökkenő).

> [!NOTE]
> Összetett index hozzáadásakor – más típusú indexekhez hasonlóan – a lekérdezések inkonzisztens eredményeket adnak vissza, mivel az index frissítése folyamatban van.

### <a name="order-by-queries-on-multiple-properties"></a>Rendezés több tulajdonság lekérdezésével:

A következő szempontokat kell használni, ha összetett indexeket használ a lekérdezésekhez `ORDER BY` két vagy több tulajdonsággal rendelkező záradékkal:

- Ha az összetett index elérési útjai nem egyeznek a `ORDER BY` záradékban szereplő tulajdonságok sorrendjével, akkor az összetett index nem támogatja a lekérdezést.

- Az összetett index elérési útjának (növekvő vagy csökkenő) sorrendjét is meg kell `order` egyeznie `ORDER BY` a záradékban szereplővel.

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

Az indexelési szabályzatot testre szabhatja, hogy az összes `ORDER BY` szükséges lekérdezést kiszolgálja.

### <a name="queries-with-filters-on-multiple-properties"></a>Lekérdezések több tulajdonsággal rendelkező szűrőkkel

Ha egy lekérdezés két vagy több tulajdonságon is tartalmaz szűrőket, hasznos lehet összetett indexet létrehozni ezekhez a tulajdonságokhoz.

Vegyük például a következő lekérdezést, amely egy két tulajdonságra vonatkozó esélyegyenlőségi szűrővel rendelkezik:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Ez a lekérdezés hatékonyabb lesz, kevesebb időt vesz igénybe, és kevesebb RU-t használ, ha a (z) (név ASC, Age ASC) összetett indexet képes kihasználni.

A tartományhoz tartozó szűrőkkel rendelkező lekérdezések összetett indexszel is optimalizálható. A lekérdezés azonban csak egyetlen tartományból álló szűrőt tartalmazhat. A tartományhoz `>`tartozó `<`szűrők `<=`a `>=`következők: `!=`,,, és. A tartomány szűrőt az összetett indexben kell megadni.

Vegye figyelembe a következő lekérdezést mind az esélyegyenlőségi, mind a tartományos szűrőkkel:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Ez a lekérdezés hatékonyabb lesz egy összetett indexszel (név ASC, Age ASC). A lekérdezés azonban nem használ összetett indexet (kor ASC, név ASC), mert az egyenlőségi szűrőket először az összetett indexben kell meghatározni.

A következő szempontokat kell figyelembe venni összetett indexek létrehozásához több tulajdonság szűrőit használó lekérdezésekhez

- A lekérdezés szűrője tulajdonságainak meg kell egyezniük az összetett indexben szereplő tulajdonságokkal. Ha egy tulajdonság az összetett indexben szerepel, de nem szerepel a lekérdezésben szűrőként, a lekérdezés nem fogja használni az összetett indexet.
- Ha egy lekérdezés olyan további tulajdonságokkal rendelkezik a szűrőben, amelyek nem lettek definiálva egy összetett indexben, akkor a lekérdezés kiértékeléséhez az összetett és a tartomány indexek kombinációját fogja használni a rendszer. Ehhez kevesebb RU-t kell használni, mint a csak a tartomány indexeket.
- Ha egy tulajdonság tartomány-szűrővel (`>` `<=`, `<` `>=`,, vagy `!=`) rendelkezik, akkor ezt a tulajdonságot az összetett indexben kell megadni. Ha egy lekérdezés több tartományból álló szűrővel rendelkezik, nem fogja használni az összetett indexet.
- Összetett index létrehozásakor, ha több szűrőkkel optimalizálja a lekérdezéseket, `ORDER` az összetett index nem lesz hatással az eredményekre. Ez a tulajdonság nem kötelező.
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

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Lekérdezés szűrővel és ORDER BY záradékkal

Ha a lekérdezés egy vagy több tulajdonságra vonatkozik `ORDER BY` , és az ORDER BY záradékban eltérő tulajdonságokkal rendelkezik, hasznos lehet a szűrőben szereplő tulajdonságokat felvenni a záradékba.

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

A következő szempontokat kell használni összetett indexek létrehozásához egy szűrővel és `ORDER BY` záradékkal rendelkező lekérdezés optimalizálásához:

* Ha a lekérdezés a tulajdonságok alapján szűri, ezeket a `ORDER BY` záradékban elsőként kell szerepeltetni.
* Ha nem definiál összetett indexet egy olyan lekérdezéshez, amely egy tulajdonságra vonatkozó szűrővel rendelkezik, `ORDER BY` és egy külön záradékot használ egy másik tulajdonság használatával, a lekérdezés továbbra is sikeres lesz. A lekérdezés ru-díja azonban egy összetett indexszel is csökkenthető, különösen akkor, ha a `ORDER BY` záradékban szereplő tulajdonság magas fokú.
* Az összetett indexek `ORDER BY` több tulajdonsággal rendelkező lekérdezésekhez való létrehozásával, valamint a több tulajdonság szűrőit tartalmazó lekérdezésekkel kapcsolatos megfontolások továbbra is érvényesek.


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
> Míg az újraindexelés folyamatban van, előfordulhat, hogy a lekérdezések nem adják vissza az összes egyező eredményt, és így nem kell hibát visszaadnia. Ez azt jelenti, hogy előfordulhat, hogy a lekérdezési eredmények nem konzisztensek, amíg az index átalakítása be nem fejeződik. Az indexek átalakításának előrehaladását az SDK-k [egyikével](how-to-manage-indexing-policy.md)követheti nyomon.

Ha az új indexelési házirend mód konzisztens értékre van állítva, akkor a rendszer nem alkalmazhat más indexelési házirendet, amíg az index átalakítás folyamatban van. A futó index-transzformáció megszakítható úgy, hogy az indexelési házirend mód értékét a none értékre állítja (amely azonnal eldobja az indexet).

## <a name="indexing-policies-and-ttl"></a>Indexelési házirendek és TTL

Az élettartam [(TTL) funkció](time-to-live.md) használatához az indexelésnek aktívnak kell lennie a tárolón, amelyen be van kapcsolva. Ez azt jelenti, hogy:

- nem lehet aktiválni az ÉLETTARTAMot olyan tárolón, ahol az indexelési mód nincs értékre van állítva.
- az indexelési mód nem állítható be egyetlen olyan tárolón sem, amelyben az élettartam aktiválva van.

Olyan esetekben, ahol nem szükséges a tulajdonság elérési útjának indexelése, de az élettartam megadása kötelező, az indexelési házirendet a következővel használhatja:

- az indexelési mód konzisztens értékre van beállítva, és
- nincs belefoglalt elérési út, és
- `/*`az egyetlen kizárt elérési út.

## <a name="next-steps"></a>További lépések

Az indexeléssel kapcsolatban az alábbi cikkekben olvashat bővebben:

- [Indexelés – áttekintés](index-overview.md)
- [Az indexelési szabályzat kezelése](how-to-manage-indexing-policy.md)

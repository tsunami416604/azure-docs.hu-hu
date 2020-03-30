---
title: Az Azure Cosmos DB indexelési szabályzatai
description: Ismerje meg, hogyan konfigurálhatja és módosíthatja az alapértelmezett indexelési szabályzatot az automatikus indexeléshez és a nagyobb teljesítményhez az Azure Cosmos DB-ben.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292065"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Az Azure Cosmos DB indexelési szabályzatai

Az Azure Cosmos DB-ben minden tároló rendelkezik egy indexelési szabályzattal, amely meghatározza, hogy a tároló elemeit hogyan kell indexelni. Az újonnan létrehozott tárolók alapértelmezett indexelési házirendje minden elem minden tulajdonságát indexeli, bármely karakterlánc vagy szám tartományindexeinek kényszerítése, valamint a Point típusú GeoJSON-objektumok térbeli indexei. Ez lehetővé teszi, hogy a magas lekérdezési teljesítmény, anélkül, hogy gondolni indexelés és indexkezelés előre.

Egyes esetekben előfordulhat, hogy felül szeretné bírálni ezt az automatikus viselkedést, hogy jobban megfeleljen az igényeinek. A tároló indexelési házirendjét az *indexelési mód*beállításával, valamint *a tulajdonságelérési utak*felvételével vagy kizárásával szabhatja testre.

> [!NOTE]
> A jelen cikkben ismertetett indexelési szabályzatok frissítésének módszere csak az Azure Cosmos DB SQL (Core) API-jára vonatkozik.

## <a name="indexing-mode"></a>Indexelési mód

Az Azure Cosmos DB két indexelési módot támogat:

- **Konzisztens**: Az index az elemek létrehozásakor, frissítésekor vagy törlésekor szinkron módon frissül. Ez azt jelenti, hogy az olvasási lekérdezések konzisztenciája [a fiókhoz konfigurált konzisztencia](consistency-levels.md)lesz.
- **Nincs**: Az indexelés le van tiltva a tárolón. Ez gyakran használatos, ha egy tárolót használnak, mint egy tiszta kulcs-érték tároló, anélkül, hogy a másodlagos indexek. Azt is fel lehet használni, hogy javítsa a nagy műveletek teljesítményét. A tömeges műveletek befejezése után az indexmód konzisztens, majd az [IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) használatával figyelhető a befejezésig.

> [!NOTE]
> Az Azure Cosmos DB is támogatja a lusta indexelési mód. A lusta indexelés sokkal alacsonyabb prioritási szinten hajtja végre az index frissítéseit, ha a motor nem végez más munkát. Ez **inkonzisztens vagy hiányos** lekérdezési eredményeket eredményezhet. Ha azt tervezi, hogy lekérdezi a Cosmos-tároló, ne válassza a lusta indexelés.

Alapértelmezés szerint az indexelési házirend beállítása `automatic`. Úgy érhető el, hogy `automatic` az indexelési `true`házirendben a tulajdonságot a értékre állítja. Ha ezt `true` a tulajdonságot úgy állítja be, hogy az Azure CosmosDB automatikusan indexelje a dokumentumokat, ahogy azok írásra vannak írva.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Ingatlanútvonalak kal együtt és annak kizárása

Az egyéni indexelési házirend megadhatja azokat a tulajdonságútvonalakat, amelyek kifejezetten szerepelnek vagy ki vannak zárva az indexelésből. Az indexelt elérési utak számának optimalizálásával csökkentheti a tároló által használt tárterületet, és javíthatja az írási műveletek késését. Ezek az elérési utak [az indexelési áttekintő szakaszban leírt módszer](index-overview.md#from-trees-to-property-paths) szerint vannak definiálva, a következő kiegészítésekkel:

- egy skaláris értékhez (karakterlánchoz vagy számhoz) vezető görbe, amely`/?`
- a tömb elemeit a `/[]` jelölés (a helyett `/0` `/1` stb.) együtt kezelik.
- a `/*` helyettesítő karakter használható a csomópont alatti elemek egyeztetésére

Ugyanazt a példát ismételve:

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

- az `headquarters`"út" `employees``/headquarters/employees/?`

- a `locations` `country` ' elérési út`/locations/[]/country/?`

- az út, `headquarters` hogy bármi alatt van`/headquarters/*`

Például, mi is `/headquarters/employees/?` az utat. Ez az elérési út biztosítaná, hogy indexeljük az alkalmazottak tulajdonságát, de nem indexelünk további beágyazott JSON-t ezen a tulajdonságon belül.

## <a name="includeexclude-strategy"></a>Belefoglalás/kizárás stratégia

Minden indexelési házirendnek `/*` tartalmaznia kell a gyökérelérési utat egy foglalt vagy egy kizárt elérési útként.

- Adja meg a gyökérelérési utat, hogy szelektíven kizárja azokat az elérési utakat, amelyeket nem kell indexelni. Ez az ajánlott megközelítés, mivel lehetővé teszi az Azure Cosmos DB proaktív indexelése a modellhez hozzáadott új tulajdonság.
- Zárja ki a gyökérelérési utat, hogy szelektíven tartalmazza az indexelendő elérési utakat.

- Alfanumerikus karaktereket és _ (aláhúzás) tartalmazó normál karakterekkel rendelkező görbék esetén nem kell kikerülnie a dupla idézőjelek körüli elérési útkarakterláncot (például "/path/?"). Más speciális karakterekkel rendelkező görbék esetén ki kell lépnie a dupla\"idézőjelek\"körüli elérési út karakterláncából (például "/ path-abc /?"). Ha azt várod, speciális karakterek az utat, akkor menekülni minden utat a biztonság érdekében. Funkcionálisan ez nem tesz különbséget, ha menekülni minden utat Vs csak az is, hogy különleges karaktereket.

- A rendszertulajdonság `_etag` alapértelmezés szerint ki van zárva az indexelésből, kivéve, ha az etag hozzáadódik a mellékelt elérési úthoz az indexeléshez.

- Ha az indexelési mód **konzisztensre**van állítva, a rendszer tulajdonságai `id` és `_ts` a rendszer automatikusindexelésre kerül.

Az elérési utak belefoglalásakor és kizárásakor a következő attribútumokkal találkozhat:

- `kind`lehet vagy `range` `hash`. A tartományindex funkció a kivonatindex összes funkcióját biztosítja, ezért tartományindex használatát javasoljuk.

- `precision`A belefoglalt elérési utak indexszintjén meghatározott szám. A maximális `-1` pontosságot jelzi. Javasoljuk, hogy mindig `-1`ezt az értéket adja meg a értékre.

- `dataType`lehet vagy `String` `Number`. Ez az indexelt JSON-tulajdonságok típusait jelzi.

Ha nincs megadva, ezek a tulajdonságok a következő alapértelmezett értékekkel rendelkeznek:

| **Tulajdonság neve**     | **Alapértelmezett érték** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` és `Number` |

[Ebben a szakaszban](how-to-manage-indexing-policy.md#indexing-policy-examples) az elérési utak befoglalására és kizárására vonatkozó irányelvpéldák indexelése.

## <a name="spatial-indexes"></a>Térbeli indexek

Ha az indexelési házirendben térbeli görbét ```type``` határoz meg, meg kell határoznia, hogy melyik indexet kell alkalmazni az adott görbére. A térbeli indexek lehetséges típusai a következők:

* Pont

* Sokszög

* MultiPoligon

* Vonalsor-karakterlánc

Az Azure Cosmos DB alapértelmezés szerint nem hoz létre térbeli indexeket. Ha térbeli SQL beépített függvényeket szeretne használni, hozzon létre egy térbeli indexet a szükséges tulajdonságokhoz. [Ebben a szakaszban](geospatial.md) a térbeli indexek hozzáadására vonatkozó házirend-példákindexeket tartalmaz.

## <a name="composite-indexes"></a>Összetett indexek

A két `ORDER BY` vagy több tulajdonsággal rendelkező záradékkal rendelkező lekérdezésekhez összetett indexre van szükség. Összetett indexet is definiálhat számos egyenlőségi és tartománylekérdezés teljesítményének javítása érdekében. Alapértelmezés szerint nincsenek összetett indexek definiálva, ezért szükség szerint [összetett indexeket](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) kell hozzáadnia.

Összetett index definiálásakor a következőket kell megadnia:

- Két vagy több tulajdonságelérési út. A tulajdonságelérési utak definiálásának sorrendje számít.

- A sorrend (növekvő vagy csökkenő).

> [!NOTE]
> Összetett index hozzáadásakor a lekérdezés a meglévő tartományindexeket fogja használni, amíg az új összetett index hozzáadása be nem fejeződik. Ezért összetett index hozzáadásakor előfordulhat, hogy nem veszi meg azonnal a teljesítménybeli javulást. Az indexátalakítás előrehaladását az [SDK-k egyikének használatával](how-to-manage-indexing-policy.md)lehet nyomon követni.

### <a name="order-by-queries-on-multiple-properties"></a>ORDER BY lekérdezések több tulajdonságra:

A következő szempontok at használja az összetett indexek használata a `ORDER BY` két vagy több tulajdonsággal rendelkező záradékkal rendelkező lekérdezésekhez:

- Ha az összetett index elérési útjai nem egyeznek meg a `ORDER BY` záradékban szereplő tulajdonságok sorrendjével, akkor az összetett index nem támogatja a lekérdezést.

- Az összetett indexelérési utak (növekvő vagy csökkenő) sorrendjének `ORDER BY` is meg kell egyeznie a `order` záradékban lévővel.

- Az összetett index egy `ORDER BY` olyan záradékot is támogat, amelynek sorrendje az összes elérési úton ellentétes.

Vegye figyelembe a következő példát, amikor összetett index van definiálva a tulajdonságok nevében, korán és _ts:

| **Összetett index**     | **Mintalekérdezés `ORDER BY`**      | **Támogatja a Kompozit Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Testre szabhatja az indexelési `ORDER BY` házirendet, hogy az összes szükséges lekérdezést kitudja szolgálni.

### <a name="queries-with-filters-on-multiple-properties"></a>A több tulajdonság alapján szűrt lekérdezések

Ha egy lekérdezés két vagy több tulajdonságra vonatkozó szűrőket tartalmaz, hasznos lehet összetett indexet létrehozni ezekhez a tulajdonságokhoz.

Vegyük például a következő lekérdezést, amelynek két tulajdonsága egyenlőségszűrője van:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Ez a lekérdezés hatékonyabb lesz, kevesebb időt vesz igénybe, és kevesebb RU-t fogyaszt, ha képes összetett indexet kihasználni (ASC név, AGE ASC).

A tartományszűrőkkel rendelkező lekérdezések összetett indexszel is optimalizálhatók. A lekérdezés azonban csak egyetlen tartományszűrővel rendelkezhet. A tartományszűrők `<` `<=`közé `>=`tartozik `!=` `>`a , , , , és . A tartományszűrőt az összetett indexben kell utoljára definiálni.

Vegye figyelembe a következő lekérdezést egyenlőségi és tartományszűrőkkel:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Ez a lekérdezés hatékonyabb lesz egy összetett index (név ASC, age ASC). A lekérdezés azonban nem használ összetett indexet (age ASC, name ASC), mert az egyenlőségi szűrőket először az összetett indexben kell meghatározni.

A következő szempontok at használja összetett indexek lekérdezések szűrők több tulajdonság

- A lekérdezés szűrőjének tulajdonságainak meg kell egyezniük az összetett indexben lévőtulajdonságokkal. Ha egy tulajdonság szerepel az összetett indexben, de szűrőként nem szerepel a lekérdezésben, a lekérdezés nem használja az összetett indexet.
- Ha egy lekérdezés további tulajdonságokkal rendelkezik a szűrőben, amelyek nem voltak definiálva az összetett indexben, akkor a rendszer összetett és tartományindexek kombinációját használja a lekérdezés kiértékeléséhez. Ehhez kevesebb RU-t igényel, mint kizárólag tartományindexek használatával.
- Ha egy tulajdonságtartományszűrővel`>` `<`( `<=` `>=`, `!=`, , , vagy ), akkor ezt a tulajdonságot kell az utolsóként definiálni az összetett indexben. Ha egy lekérdezés egynél több tartományszűrővel rendelkezik, nem használja az összetett indexet.
- Ha összetett indexet hoz létre a `ORDER` lekérdezések több szűrővel történő optimalizálására, az összetett index nem lesz hatással az eredményekre. Ez a tulajdonság nem kötelező.
- Ha nem ad meg összetett indexet egy több tulajdonságra vonatkozó szűrőkkel rendelkező lekérdezéshez, a lekérdezés továbbra is sikeres lesz. A lekérdezés RU-költsége azonban összetett indexszel csökkenthető.

Vegye figyelembe a következő példákat, amikor összetett index van definiálva a tulajdonságok nevében, korán és időbélyegén:

| **Összetett index**     | **Mintalekérdezés**      | **Támogatja a Kompozit Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>A szűrőt és egy ORDER BY záradékot is tartalmazó lekérdezések

Ha egy lekérdezés egy vagy több tulajdonságra szűr, és az ORDER BY záradékban különböző tulajdonságokkal rendelkezik, hasznos lehet a szűrőtulajdonságainak hozzáadása a `ORDER BY` záradékhoz.

Ha például a szűrő ben lévő tulajdonságokat hozzáadja az ORDER BY záradékhoz, a következő lekérdezés átírható egy összetett index kihasználásához:

Lekérdezés tartományindex használatával:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Lekérdezés összetett indexhasználatával:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Ugyanaz a minta- és lekérdezésoptimalizálás általánosítható több egyenlőségi szűrővel rendelkező lekérdezések esetén:

Lekérdezés tartományindex használatával:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Lekérdezés összetett indexhasználatával:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Az összetett indexek létrehozásakor a következő szempontok at használja `ORDER BY` a lekérdezés szűrővel és záradékkal történő optimalizálásához:

* Ha a lekérdezés a tulajdonságokra szűr, `ORDER BY` ezeket először a záradékban kell szerepeltetni.
* Ha nem ad meg összetett indexet egy lekérdezésben, `ORDER BY` amely egy tulajdonságszűrőt és egy másik tulajdonságot használó külön záradékot használ, a lekérdezés továbbra is sikeres lesz. A lekérdezés ru költsége azonban csökkenthető összetett indexszel, különösen akkor, ha a záradékban szereplő `ORDER BY` tulajdonság nagy számossággal rendelkezik.
* A több tulajdonsággal rendelkező `ORDER BY` lekérdezések összetett indexeinek létrehozásához, valamint a több tulajdonságra vonatkozó szűrőkkel rendelkező lekérdezések létrehozásához továbbra is érvényesek.


| **Összetett index**                      | **Mintalekérdezés `ORDER BY`**                                  | **Támogatja a Kompozit Index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Az indexelési házirend módosítása

A tároló indexelési szabályzata bármikor frissíthető [az Azure Portalon vagy a támogatott SDK-k egyikén.](how-to-manage-indexing-policy.md) Az indexelési házirend frissítése elindítja a régi indexről az újra való átalakítást, amely online és a helyén történik (így a művelet során nem használ fel további tárhelyet). A régi szabályzat indexe hatékonyan átalakul az új szabályzatba anélkül, hogy befolyásolná az írási rendelkezésre állást vagy a tárolón kiosztott átviteli szintet. Az indexátalakítása aszinkron művelet, és a befejezéshez szükséges idő a kiosztott átviteli függ, az elemek száma és mérete függ.

> [!NOTE]
> Tartomány vagy térbeli index hozzáadása közben előfordulhat, hogy a lekérdezések nem adják vissza az összes egyező eredményt, és ezt hibák visszaadása nélkül teszik. Ez azt jelenti, hogy a lekérdezés i. Az indexátalakítás előrehaladását az [SDK-k egyikének használatával](how-to-manage-indexing-policy.md)lehet nyomon követni.

Ha az új indexelési házirend mód konzisztens, az index átalakítása közben más indexelési házirend-módosítás nem alkalmazható. A futó indexátalakítás megszakítható az indexelési házirend mód nincs értékre állításával (amely azonnal eldobja az indexet).

## <a name="indexing-policies-and-ttl"></a>Indexelési házirendek és TTL

Az [élő (Time-to-Live) (TTL) funkció](time-to-live.md) megköveteli, hogy az indexelés aktív legyen a be van kapcsolva lévő tárolón. Ez azt jelenti, hogy:

- nem lehet aktiválni a TTL-t olyan tárolóban, ahol az indexelési mód Nincs,
- nem lehet az indexelési módot Nincs beállításra állítani egy olyan tárolón, ahol a TTL aktiválva van.

Olyan esetekben, amikor nincs tulajdonságelérési út indexelésre, de a TTL szükséges, használhatja az indexelési szabályzat:

- egy indexelési mód, amely konzisztens, és
- nincs benne az ösvényen, és
- `/*`mint az egyetlen kizárt útvonal.

## <a name="next-steps"></a>További lépések

Az indexelésről az alábbi cikkekben olvashat bővebben:

- [Indexelés – áttekintés](index-overview.md)
- [Az indexelési házirend kezelése](how-to-manage-indexing-policy.md)

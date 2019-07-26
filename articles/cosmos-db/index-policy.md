---
title: Azure Cosmos DB indexelési házirendek
description: Megtudhatja, hogyan konfigurálhatja és módosíthatja az alapértelmezett indexelési házirendet az automatikus indexeléshez és a Azure Cosmos DB nagyobb teljesítményéhez.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: thweiss
ms.openlocfilehash: 01e3e1f1c9bffee0604de1260e8e466f5b1d229d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467873"
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

## <a name="including-and-excluding-property-paths"></a>Tulajdonságok elérési útjának belefoglalása és kizárása

Az egyéni indexelési házirend megadhatja az indexelésből explicit módon befoglalt vagy kizárt tulajdonságokat. Az indexelt elérési utak számának optimalizálásával csökkentheti a tároló által használt tárterület méretét, és javíthatja az írási műveletek késését. Ezek az elérési utak az [indexelés áttekintése szakaszban leírt módszer](index-overview.md#from-trees-to-property-paths) szerint vannak meghatározva, a következő kiegészítésekkel:

- egy skaláris értékhez (sztringhez vagy számhoz) vezető útvonal végződik`/?`
- egy tömb elemeit a rendszer a `/[]` jelöléssel együtt tárgyalja (a `/1` `/0`helyett stb.).
- a `/*` helyettesítő karakterrel a csomópont alatti elemek is megegyeznek

Ugyanezt a példát még egyszer kell megtennie:

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

- az `headquarters`elérésiútja `employees``/headquarters/employees/?`
- az `locations`elérésiút `country``/locations/[]/country/?`
- az elérési út `headquarters``/headquarters/*`

Ha az indexelési házirendben explicit módon szerepel egy elérési út, azt is meg kell határoznia, hogy mely indexeket kell alkalmazni az adott elérési útra és minden egyes index típusra vonatkozóan, az index adattípusa a következőre vonatkozik:

| Index típusa | Engedélyezett cél adattípusok |
| --- | --- |
| Tartomány | Karakterlánc vagy szám |
| Térbeli | Pont, LineString vagy sokszög |

Például megadhatja az `/headquarters/employees/?` elérési utat, és `Range` azt is meghatározhatja, hogy az adott útvonalon legyen `String` - `Number` e az index a két és az érték esetében is.

### <a name="includeexclude-strategy"></a>Belefoglalási/kizárási stratégia

Minden indexelési házirendnek tartalmaznia kell a gyökér `/*` elérési útját, vagy egy belefoglalt vagy kizárt elérési utat.

- Adja meg a gyökér elérési útját az indexelni nem kívánt elérési utak szelektív kizárásához. Ez az ajánlott módszer, mivel lehetővé teszi, hogy Azure Cosmos DB proaktív módon indexelje a modellbe felvehető új tulajdonságokat.
- Zárja ki a gyökér elérési útját, hogy szelektíven szerepeljenek az indexelni kívánt elérési utak.

- A normál karaktereket tartalmazó elérési utak esetében: alfanumerikus karakterek és _ (aláhúzás), nem kell elmenekülnie az elérésiút-karakterláncot idézőjelek közé (például "/Path/?"). Más speciális karaktereket tartalmazó elérési utak esetén el kell kerülnie az elérésiút-karakterláncot az idézőjelek között (például\": "/\"Path-ABC/?"). Ha az elérési úton speciális karaktereket vár, a biztonság érdekében minden elérési utat eljuthat. Funkcionálisan nem tesz különbséget, ha minden elérési utat és csak a speciális karaktereket tartalmazó útvonalat használ.

- A "ETAG" rendszertulajdonság alapértelmezés szerint ki van zárva az indexelésből, kivéve, ha a ETAG hozzá lett adva a befoglalt útvonalhoz az indexeléshez.

Tekintse meg [ezt a szakaszt](how-to-manage-indexing-policy.md#indexing-policy-examples) az indexelési házirendek példáinak megtekintéséhez.

## <a name="composite-indexes"></a>Összetett indexek

`ORDER BY` A két vagy több tulajdonságot tartalmazó lekérdezések összetett indexet igényelnek. Az összetett indexeket jelenleg csak a többszörös `ORDER BY` lekérdezések használják. Alapértelmezés szerint a rendszer nem definiál összetett indexeket, ezért szükség szerint [összetett indexeket](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) kell hozzáadnia.

Az összetett index meghatározásakor a következőket kell megadnia:

- Két vagy több tulajdonság elérési útja. Az a sorrend, amelyben a tulajdonságok elérési útjai definiálva vannak.
- A sorrend (növekvő vagy csökkenő).

Az összetett indexek használatakor a következő szempontokat kell figyelembe venni:

- Ha az összetett index elérési útjai nem egyeznek meg az ORDER BY záradékban szereplő tulajdonságok sorrendjével, akkor az összetett index nem támogatja a lekérdezést.

- Az összetett index elérési útjának (növekvő vagy csökkenő) sorrendjét is meg kell egyeznie az ORDER BY záradékban szereplő sorrendtel.

- Az összetett index az ORDER BY záradékot is támogatja az ellenkező sorrendben az összes elérési úton.

Vegye figyelembe a következő példát, ahol összetett index van meghatározva az a, b és c tulajdonságban:

| **Összetett index**     | **Példa `ORDER BY` lekérdezésre**      | **Az index támogatja?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

Az indexelési szabályzatot testre szabhatja, hogy az összes `ORDER BY` szükséges lekérdezést kiszolgálja.

## <a name="modifying-the-indexing-policy"></a>Az indexelési szabályzat módosítása

Egy tároló indexelési házirendjét bármikor frissítheti [a Azure Portal vagy a támogatott SDK-](how-to-manage-indexing-policy.md)k egyikével. Az indexelési házirend frissítése elindítja a régi indexről az új verzióra történő átalakítást, amely online és helyben történik (így a művelet során nincs szükség további tárolóhelyre). A régi szabályzat indexét a rendszer hatékonyan átalakítja az új szabályzatra anélkül, hogy az hatással lenne az írási rendelkezésre állásra vagy a tárolón kiosztott átviteli sebességre. Az index transzformáció egy aszinkron művelet, és a befejezéshez szükséges idő a kiépített átviteli sebességtől, az elemek számától és méretétől függ. 

> [!NOTE]
> Míg az újraindexelés folyamatban van, előfordulhat, hogy a lekérdezések nem adják vissza az összes egyező eredményt, és így nem kell hibát visszaadnia. Ez azt jelenti, hogy előfordulhat, hogy a lekérdezési eredmények nem konzisztensek, amíg az index átalakítása be nem fejeződik. Az indexek átalakításának előrehaladását az [SDK](how-to-manage-indexing-policy.md)-k egyikével követheti nyomon.

Ha az új indexelési házirend mód konzisztens értékre van állítva, akkor a rendszer nem alkalmazhat más indexelési házirendet, amíg az index átalakítás folyamatban van. A futó index-transzformáció megszakítható úgy, hogy az indexelési házirend mód értékét a none értékre állítja (amely azonnal eldobja az indexet).

## <a name="indexing-policies-and-ttl"></a>Indexelési házirendek és TTL

Az élettartam [(TTL) funkció](time-to-live.md) használatához az indexelésnek aktívnak kell lennie a tárolón, amelyen be van kapcsolva. Ez azt jelenti, hogy:

- nem lehet aktiválni az ÉLETTARTAMot olyan tárolón, ahol az indexelési mód nincs értékre van állítva.
- az indexelési mód nem állítható be egyetlen olyan tárolón sem, amelyben az élettartam aktiválva van.

Olyan esetekben, ahol nem szükséges a tulajdonság elérési útjának indexelése, de az élettartam megadása kötelező, az indexelési házirendet a következővel használhatja:

- az indexelési mód konzisztens értékre van beállítva, és
- nincs belefoglalt elérési út, és
- `/*`az egyetlen kizárt elérési út.

## <a name="obsolete-attributes"></a>Elavult attribútumok

Az indexelési házirendek használatakor a következő, elavult attribútumok jelenhetnek meg:

- `automatic`a egy indexelési házirend gyökerében definiált logikai érték. Ezt a rendszer figyelmen kívül hagyja `true`, és beállíthatja, ha a használt eszköz megköveteli.
- `precision`a befoglalt elérési utakhoz tartozó index szintjén definiált szám. Ezt a rendszer figyelmen kívül hagyja `-1`, és beállíthatja, ha a használt eszköz megköveteli.
- `hash`egy olyan indexelési típus, amelyet mostantól a tartomány típusa helyettesít.

## <a name="next-steps"></a>További lépések

Az indexeléssel kapcsolatban az alábbi cikkekben olvashat bővebben:

- [Indexelés – áttekintés](index-overview.md)
- [Az indexelési szabályzat kezelése](how-to-manage-indexing-policy.md)

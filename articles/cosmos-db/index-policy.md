---
title: Az Azure Cosmos DB indexelési szabályzatok
description: Ismerje meg, hogyan konfigurálja, és módosítsa az alapértelmezett indexelési szabályzat Automatikus indexelés és a nagyobb teljesítmény az Azure Cosmos DB-ben.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: a089d8bd4f2197c93d43e70742743db29944b910
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872670"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Az Azure Cosmos DB indexelési szabályzatok

Az Azure Cosmos DB minden tárolót egy indexelési házirendet, amely előírja, hogy hogyan lehet indexelni a tároló elemek rendelkezik. Alapértelmezett házirend az indexelési újonnan létrehozott tárolók indexek minden elem, tartomány indexei bármilyen karakterlánc vagy szám kényszerítése minden tulajdonsághoz, és a térbeli indexek, az összes GeoJSON-objektum írja be a pont. Ez lehetővé teszi a lekérdezési teljesítmény eléréséhez gondolja át az indexelés és előre Indexkezelés nélkül.

Bizonyos esetekben érdemes lehet, hogy jobban kockázatiszint-e az automatikus viselkedés felülbírálásához. Egy tároló indexelési házirend beállításával testre szabhatja a *indexelési mód*, és bevonhat vagy kizárhat *tulajdonság elérési utak*.

## <a name="indexing-mode"></a>Az indexelő mód

Az Azure Cosmos DB két indexelési módot támogat:

- **Egységes**: Ha egy tároló indexelési szabályzat Consistent úgy van beállítva, az index frissítése szinkron módon létrehozása, frissítése és törölhet elemeket. Ez azt jelenti, hogy a konzisztencia az olvasási lekérdezések lesz a [konzisztencia, a fiókhoz konfigurált](consistency-levels.md).

- **Nincs**: Egy tároló indexelési házirendet None értékre van állítva, ha az indexelés van hatékonyan le van tiltva az adott tárolóban. Ez gyakran használják, ha a tárolót használja a másodlagos indexekkel kellene tiszta kulcs-érték tárolóként. Felgyorsítva a tömeges beszúrási műveletek is segít.

## <a name="including-and-excluding-property-paths"></a>Belefoglalásához és kizárásához tulajdonság elérési utak

Egyéni indexelési házirendet adhat meg, amelyek kifejezetten foglalt vagy zárva az indexelő tulajdonság elérési utak. Indexelt útvonalak száma optimalizálással, csökkentheti a tároló által felhasznált tárterület mennyisége, és növelheti a késést az írási műveletek. Az elérési utak vannak definiálva a következő [az indexelő-áttekintés szakaszban leírt módszer](index-overview.md#from-trees-to-property-paths) az alábbi kiegészítésekkel:

- egy skaláris érték (karakterlánc vagy szám) vezető elérési út végződik `/?`
- a tömb elemei együtt szolgálhatók keresztül a `/[]` jelölés (helyett `/0`, `/1` stb.)
- a `/*` helyettesítő karakter is használható a megfelelő elemeket a csomópont alatt

Újra véve ugyanebben a példában:

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

- a `headquarters`a `employees` elérési út `/headquarters/employees/?`
- a `locations`" `country` elérési út `/locations/[]/country/?`
- az elérési útját alatt `headquarters` van `/headquarters/*`

Ha egy elérési utat az indexelési házirendet explicit módon szerepel, azt is határozza meg, melyik index típusokat kell alkalmazni az elérési útnak és az egyes index, az adattípus az index vonatkozik:

| Index típusa | Engedélyezett a céloldali adattípusok |
| --- | --- |
| Tartomány | Karakterlánc vagy szám |
| Térbeli | Pont, LineString, vagy sokszög |

Például, hogy tartalmazhatja a `/headquarters/employees/?` elérési útja, és adja meg, amely egy `Range` index kell alkalmazni az adott elérési úttal is `String` és `Number` értékeket.

### <a name="includeexclude-strategy"></a>Stratégia belefoglalása vagy kizárása

A gyökér elérési útját tartalmazza rendelkezik minden olyan indexelési házirend `/*` egy része vagy kizárt elérési utat.

- Elérési utak, amelyeket nem lehet indexelni kell kizárhatja a gyökér elérési útját tartalmazza. Ez a lehetőség az ajánlott módszer lehetővé teszi az Azure Cosmos DB proaktív módon index minden új tulajdonság, amely a modell adhatók hozzá.
- Zárja ki a gyökér elérési úthoz szelektív elérési utakat indexelni kell felvenni.

- Normál karakterek, amelyek tartalmazzák az elérési utakra: alfanumerikus karaktereket és _ (aláhúzás), nem kell az elérési útvonal karakterláncának körüli idézőjeleket (például "/ path /?") karaktert. Az elérési utakat speciális karaktereket, escape-körüli dupla idézőjelek között az elérési útvonal karakterláncának kell (például "/\"elérési út – abc\"/?"). Ha különleges karaktereket az elérési úthoz, akkor is escape-minden biztonsági elérési útját. Funkcionálisan nem létrehozni, akkor eltérést Ha escape-e minden elérési út csak az azokat, amelyeket speciális karakterek Vs.

Lásd: [ebben a szakaszban](how-to-manage-indexing-policy.md#indexing-policy-examples) házirend példák az indexelés.

## <a name="modifying-the-indexing-policy"></a>Az indexelési szabályzat módosítása

Bármikor frissítheti egy tároló indexelési házirendet [az Azure-portálon vagy valamelyik támogatott SDK-k használatával](how-to-manage-indexing-policy.md). Az indexelési házirendet egy frissítést, a másikat, amely történik online és a helyen, (tehát a művelet során felhasznált nincs további tárhely) a régi indexből átalakítás aktiválása. A régi házirend index hatékony működésének megzavarása nélkül megtesztelheti az írás rendelkezésre állása és a tárolóban kiosztott átviteli átalakítva az új szabályzathoz. Index átalakítási aszinkron művelet, és a végrehajtásához szükséges idő attól függ, a kiosztott átviteli sebesség, azon elemek száma és mérete. 

> [!NOTE]
> Újraindexelését folyamatban van, míg a lekérdezések nesmí vracet a megfelelő eredményeit, és akkor teszi ezt, anélkül, hogy bármilyen hibát adna vissza. Ez azt jelenti, hogy lekérdezési eredmények lehetséges, hogy nem konzisztens az index átalakítás befejeződéséig. Index átalakítási folyamat nyomon lehet [az SDK-k egyikével](how-to-manage-indexing-policy.md).

Az új indexelési házirend üzemmód Consistent értékre van állítva, ha nincs indexelési szabályzat módosítása alkalmazhatók, amíg folyamatban van az index átalakítást. Egy futó index átalakítás szakadhatnak meg úgy, hogy nincs (amely azonnal, eldobja az index) az indexelési házirend üzemmód.

## <a name="indexing-policies-and-ttl"></a>Indexelési szabályzatok és az élettartam szerepel

A [idő-az-élettartam (TTL) funkció](time-to-live.md) igényel, az indexelés a tárolón bekapcsolt állapotú lesz aktív. Ez azt jelenti, hogy:

- már nem egy tárolóban, ahol az indexelési mód értéke None, TTL aktiválása
- nem alkalmas az indexelési üzemmód beállítása None egy tárolón, ahol TTL aktiválva van.

Forgatókönyvek, ahol nincs útvonal szükséges indexelése, de az élettartam (TTL) szükséges egy az indexelési házirendet is használhatja:

- az indexelő módban a Consistent, és
- nem található elérési útját, és
- `/*` mert az csak a kizárt elérési út.

## <a name="obsolete-attributes"></a>Elavult attribútumok

Az indexelési szabályzatok használatakor merülhetnek fel a következő attribútumokat, amelyek már elavult:

- `automatic` logikai érték beolvasása egy indexelési házirendet gyökérmappájában van meghatározva. Most már figyelmen kívül hagyja, és a állítható `true`, amikor az eszköz írja elő.
- `precision` egy szám belefoglalt elérési utak az index szintjén van meghatározva. Most már figyelmen kívül hagyja, és a állítható `-1`, amikor az eszköz írja elő.
- `hash` az index olyan altípus, amely váltotta fel a tartomány típusa van.

## <a name="next-steps"></a>További lépések

További információ az indexelő az alábbi cikkeket:

- [Az indexelő áttekintése](index-overview.md)
- [Indexelési házirend kezelése](how-to-manage-indexing-policy.md)

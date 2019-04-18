---
title: Az Azure Cosmos DB indexelése
description: Indexelő működésének megismerése az Azure Cosmos DB-hez.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: ecf53251020ce1b639a5bf8da65f5d31ff699db9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265695"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Az Azure Cosmos DB - indexelő áttekintése

Az Azure Cosmos DB egy sémafüggetlen adatbázis, és lehetővé teszi, hogy az alkalmazás gyorsan iterálása anélkül, hogy sémákkal vagy indexkezeléssel bajlódnia. Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli az összes elem a tárolóban anélkül, hogy a séma vagy másodlagos indexek fejlesztőktől.

## <a name="items-as-trees"></a>Fák elemek

JSON-dokumentumok formájában a tárolóban lévő elemek kivetítést, és őket, fák képviselő, Azure Cosmos DB Normalizálja a struktúra és a példány értékeket is elemek között egységesíti fogalma be egy **dinamikusan kódolású az elérési út szerkezete** . A címkék a JSON-dokumentumok, a tulajdonságnevek és azok értékeit is tartalmaz, amelyek e ábrázolás lesz a fa egy csomópontjára. A fa a levelek tartalmazza a tényleges értékek és a köztes csomópont tartalmazza az adatbázisséma-információk. Az alábbi kép mutatja a két létrehozott fák elemek (1. és 2) az Azure Cosmos-tárolóban:

![Fa jelképeként két különböző elemek egy Azure Cosmos-tárolóban.](./media/index-overview/indexing-as-tree.png)

Adja meg szülőként a tényleges csomópontok, a címkéket a JSON-dokumentum alá tartozó, egy ál gyökércsomópont jön létre. A beágyazott adatstruktúrák meghajtó a hierarchiában a fában. A numerikus értékek (például 0, 1,...) feliratú mesterséges csomópontok köztes enumerálások megjelenítésére alkalmazzák, és a tömb indexek.

## <a name="index-paths"></a>Indexek elérési útjai

Az Azure Cosmos DB-elemek egy Azure Cosmos-tárolóban az pedig JSON-dokumentumok, fák projektek. Az index házirendeket elérési utak a fa belül majd hangolhassa. Választhat, vagy elérési utak kizárása a indexelés. Ez jobb írási teljesítményt, és csökkentheti a indexelt tárolási forgatókönyvekhez, ahol a lekérdezési mintáknak ismert előre. További tudnivalókért lásd: [Index elérési utak](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indexelés: Technikai részletek

Az Azure Cosmos database vonatkozik *Automatikus indexelés* az adatokhoz, ahol minden elérési út egy fa indexelése, ha nem konfigurál az egyes elérési utak kizárása.

Az Azure Cosmos-adatbázis védett fordított index adatszerkezet az egyes elemek információk tárolására és hatékony lekérdezése ábrázolását megkönnyítése érdekében. Az index fa egy dokumentumot, amely az összes a fákat, a tárolóban lévő egyes elemek jelölő az Unió úgy van felépítve. Az index fa igényével együtt növekszik az idő múlásával új elemek hozzáadásakor, vagy a meglévő elemeket frissülnek a tárolóban. Ellentétben a relációs adatbázis-indexelés, az Azure Cosmos DB nem indítsa újra a teljesen új indexelő, új mezők jelennek meg. Új elemek hozzáadásakor a meglévő index struktúra. 

Az index fa minden egyes csomópontja a címke és az Elhelyezés értékek, az úgynevezett tartalmazó indexbejegyzés a *kifejezés*, és az azonosítók nevű elem a *hozzászólásai*. A hozzászólásai a kapcsos zárójelek közé (például {1,2}) a fordított index ábra felelnek meg az elemek például *dokumentum 1* és *Document2* tartalmazó az adott címke értéke. A séma és a példány értékei is egységesen kezelésének egy fontos utalás, hogy minden csomagolt nagy méretű indexszel. Egy példány érték, amely még mindig a levelek nem ismétlődik, lehet különböző szerepkörök között elemek, a másik sémát címkék, de ugyanazt az értéket. Az alábbi képen látható két különböző elemek fordított indexelés:

![Technikai részletek indexelést, fordított Index](./media/index-overview/inverted-index.png)

> [!NOTE]
> A fordított index hasonló, egy keresőmotor, adatok beolvasása a tartományban használt indexelő struktúrákat jelenhet meg. Ezzel a módszerrel az Azure Cosmos DB segítségével kereshet az adatbázis bármely elem, függetlenül a séma struktúráját.

A normalizált elérési útja az index kódol egészen a legfelső szintű előre elérési útját az értéket, és a típus adatait az érték. Az elérési út és az érték kódolt különböző típusú, például tartomány-, térbeli indexelő biztosít. Az érték kódolás célja, hogy adjon meg egyedi érték, vagy meg az elérési utakat áll.

## <a name="querying-with-indexes"></a>Az indexek lekérdezése

A fordított index lehetővé teszi, hogy egy lekérdezést a dokumentumokat, a lekérdezési predikátumban megfelelő gyorsan azonosíthatja. Úgy kezeli a séma- és a példány értéke egyenletesen elérési utak tekintetében, a fordított index is egy fa. Így az index és az eredmények lehetnek egy érvényes JSON-dokumentum szerializált és fában visszaküldés maguk-dokumentumok formájában adja vissza. Ez a módszer lehetővé teszi, hogy további lekérdezés eredményét feletti recursing. Az alábbi képen egy pont lekérdezésben indexelő példáját mutatja be:  

![Pont lekérdezési példa](./media/index-overview/index-point-query.png)

Az egy sortartomány-lekérdezés *GermanTax* van egy [felhasználó által definiált függvény](stored-procedures-triggers-udfs.md#udfs) lekérdezés-feldolgozás részeként végrehajtott. A felhasználó által definiált függvény bármely regisztrált, JavaScript-függvény által biztosított a gazdag programozási logika, a lekérdezés integrálva. Az alábbi képen egy sortartomány-lekérdezés az indexelő példáját mutatja be:

![Tartomány lekérdezési példa](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>További lépések

További információ az indexelő az alábbi cikkeket:

- [Indexelési házirend](index-policy.md)
- [Index típusa](index-types.md)
- [Index elérési utak](index-paths.md)
- [Indexelési házirend kezelése](how-to-manage-indexing-policy.md)

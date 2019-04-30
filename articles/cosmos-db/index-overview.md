---
title: Az Azure Cosmos DB indexelése
description: Indexelő működésének megismerése az Azure Cosmos DB-hez.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 3bb8913725acf04f71aba8b4c4350235f2c44dfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61051870"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexelés az Azure Cosmos DB használatával – áttekintés

Azure Cosmos DB a sémafüggetlen adatbázis, amely lehetővé teszi, hogy az alkalmazás ismételt futtatásával anélkül, hogy sémákkal vagy indexkezeléssel bajlódnia. Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli az összes elemet minden tulajdonsága az [tároló](databases-containers-items.md#azure-cosmos-containers) lehetővé bármilyen vagy másodlagos indexek konfigurálása nélkül.

Ez a cikk az a célja, hogy azt ismertetik, hogyan indexeli az Azure Cosmos DB az adatokat, és hogyan használja az indexeket a lekérdezési teljesítmény javításához. Javasoljuk, hogy meg ebben a szakaszban testreszabása haladóbb [indexelési szabályzataihoz](index-policy.md).

## <a name="from-items-to-trees"></a>A fákat elemek

Minden alkalommal, amikor egy elem tárolása egy tárolóban történik, a tartalom előre jelzett költségről JSON-dokumentumként, akkor egy fa ábrázolás konvertálva. Mi ez azt jelenti, hogy az adott elem minden egyes tulajdonság egy csomópontra a fanézetben lekérdezi ábrázolva. Egy pszeudo gyökércsomópont adja meg szülőként minden az első szintű tulajdonságokat az elem jön létre. Levélszintjén elem végzi a tényleges skaláris értékek tartalmaznak.

Tegyük fel fontolja meg ezt az elemet:

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

A következő fa lenne szerepelhetnek:

![Az előző elemet egy fa ábrázolva](./media/index-overview/item-as-tree.png)

Vegye figyelembe, hogyan tömbök kódolt a fa: minden bejegyzés a tömbben lekérdezi egy köztes csomópont címkézte meg, hogy a bejegyzés a tömbön belüli index (0, 1 stb.).

## <a name="from-trees-to-property-paths"></a>A tulajdonság elérési utakhoz fák

Miért érdemes az Azure Cosmos DB alakítja elemek fák oka az, mivel így által ezen fák belüli elérési tulajdonságai. Az elérési út egy tulajdonság, azt is a fastruktúrán csomópontból az adott tulajdonsághoz, és fűzze össze az egyes bejárt csomópontok a címkék.

Az alábbiakban az egyes tulajdonságot a fenti példa elem elérési útjait:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Egy cikk írásakor az Azure Cosmos DB hatékonyan indexeli minden egyes tulajdonság elérési útja és a megfelelő értéket.

## <a name="index-kinds"></a>Index típusú

Az Azure Cosmos DB jelenleg kétféle indexek használatát támogatja:

A **tartomány** használható index típusa:

- egyenlőség lekérdezéseket: `SELECT * FROM container c WHERE c.property = 'value'`
- tartomány lekérdezések: `SELECT * FROM container c WHERE c.property > 'value'` (működik `>`, `<`, `>=`, `<=`, `!=`)
- `ORDER BY` a lekérdezésekre: `SELECT * FROM container c ORDER BY c.property`
- `JOIN` a lekérdezésekre: `SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'`

Tartomány indexek használható skaláris értékek (karakterlánc vagy szám).

A **térbeli** használható index típusa:

- térinformatikai távolság lekérdezéseket: `SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40`
- a térinformatikai lekérdezések belül: `SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })`

A térbeli indexek esetén használható formátumú [GeoJSON](geospatial.md) objektumokat. Pontok, Linestring, és poligonok jelenleg támogatott.

## <a name="querying-with-indexes"></a>Az indexek lekérdezése

Az elérési utak, ki kell olvasni, amikor az adatok indexelése megkönnyítik az indexben található, a lekérdezés feldolgozása során. Összekapcsolja a `WHERE` indexelt elérési útjaihoz záradék a lekérdezés, lehetőség a cikkek, amelyek nagyon gyorsan megfelelnek a lekérdezési predikátumban azonosítására.

Vegyük példaként a következő lekérdezést: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. A lekérdezési predikátumban (szűrés a cikkek, ahol minden olyan helyen van annak az országnak "Franciaország") megfelel az alábbi vörös színnel elérési útja:

![Egy megadott elérési úthoz a fán egyeztetése](./media/index-overview/matching-path.png)

> [!NOTE]
> Egy `ORDER BY` záradék *mindig* tartományt kell indexelése és sikertelen lesz, ha az elérési út hivatkozik, ezért nem rendelkezik ilyennel.

## <a name="next-steps"></a>További lépések

További információ az indexelő az alábbi cikkeket:

- [Indexelési házirend](index-policy.md)
- [Indexelési házirend kezelése](how-to-manage-indexing-policy.md)

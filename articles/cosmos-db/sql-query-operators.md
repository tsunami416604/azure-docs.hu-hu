---
title: Az Azure Cosmos DB SQL-lekérdezés operátorok
description: További információ az Azure Cosmos DB SQL-operátorok.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342480"
---
# <a name="operators-in-azure-cosmos-db"></a>Az Azure Cosmos DB operátorok

Ez a cikk az Azure Cosmos DB által támogatott különböző operátorok részletesen.

## <a name="equality-and-comparison-operators"></a>Egyenlőség és összehasonlító operátorok

Az alábbi táblázat egyenlőségi összehasonlítás eredménye minden két JSON-típusok között az SQL API-ban.

| **Op** | **Nincs definiálva** | **NULL** | **Logikai érték** | **Szám** | **Karakterlánc** | **Object** | **Pole** |
|---|---|---|---|---|---|---|---|
| **Nincs definiálva** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan |
| **NULL** | Meghatározatlan | **oké** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan |
| **Logikai érték** | Meghatározatlan | Meghatározatlan | **oké** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan |
| **Szám** | Meghatározatlan | Meghatározatlan | Meghatározatlan | **oké** | Meghatározatlan | Meghatározatlan | Meghatározatlan |
| **Karakterlánc** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | **oké** | Meghatározatlan | Meghatározatlan |
| **Object** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | **oké** | Meghatározatlan |
| **Pole** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | **oké** |

Az összehasonlító operátorok például `>`, `>=`, `!=`, `<`, és `<=`, típusában vagy a kettő közötti összehasonlítás objektumok vagy észszerűek Tárolótömböket `Undefined`.  

Ha a skaláris kifejezés eredménye `Undefined`, az elem nem található meg az eredményt, mivel `Undefined` nem egyenlő `true`.

## <a name="logical-and-or-and-not-operators"></a>Logikai (AND, OR és NOT) operátorok

Logikai operátorok a logikai értékek művelethez. Az alábbi táblázatokban logikai hiteles táblák ezen operátorok számára:

**VAGY a kezelő**

| VAGY | True (Igaz) | False (Hamis) | Meghatározatlan |
| --- | --- | --- | --- |
| True (Igaz) |True (Igaz) |True (Igaz) |True (Igaz) |
| False (Hamis) |True (Igaz) |False (Hamis) |Meghatározatlan |
| Meghatározatlan |True (Igaz) |Meghatározatlan |Meghatározatlan |

**ÉS operátor**

| ÉS | True (Igaz) | False (Hamis) | Meghatározatlan |
| --- | --- | --- | --- |
| True (Igaz) |True (Igaz) |False (Hamis) |Meghatározatlan |
| False (Hamis) |False (Hamis) |False (Hamis) |False (Hamis) |
| Meghatározatlan |Meghatározatlan |False (Hamis) |Meghatározatlan |

**NOT operátor**

| NEM |  |
| --- | --- |
| True (Igaz) |False (Hamis) |
| False (Hamis) |True (Igaz) |
| Meghatározatlan |Meghatározatlan |


## <a name="-operator"></a>* operátor

A speciális operátor *-projektek, a teljes elem-jébe. Használatakor a csak tervezett mezőt kell lennie. A lekérdezés, például `SELECT * FROM Families f` érvényes, de `SELECT VALUE * FROM Families f` és `SELECT *, f.id FROM Families f` nem érvényesek.

## <a name="-and--operators"></a>? és?? Operátorok

Használhatja a Ternáris (?), és egyesítse a feltételes kifejezések, mint a programnyelvek például hozhat létre (?) operátorok C# és a JavaScript. 

Használhatja a? operátor menet közben új JSON-tulajdonságokkal létrehozására. Például a következő lekérdezés osztályozza a besorolási szintek be `elementary` vagy `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Hívások is beágyazhatja a? operátor szerinti szűrése, ahogy a következő lekérdezést: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Csakúgy, mint más lekérdezési operátorokkal a? operátor elemek nem tartalmazza, ha a hivatkozott tulajdonságai hiányoznak, vagy az összehasonlított típusok különböző.

Használja a?? operátor hatékonyan ellenőrzéséhez egy tulajdonságot egy elemet a szolgáltatásban tárolt részben strukturált vagy vegyes típusú adatok lekérdezésekor. Ha például az alábbi lekérdezés a értéket ad vissza `lastName` Ha jelen van, vagy `surname` Ha `lastName` nincs jelen.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>További lépések

- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [a kulcsszavak](sql-query-keywords.md)
- [SELECT záradék](sql-query-select.md)

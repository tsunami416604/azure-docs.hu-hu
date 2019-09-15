---
title: A Azure Cosmos DB SQL-lekérdezési operátorai
description: Ismerkedjen meg Azure Cosmos DB SQL-operátorokkal.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 899355ad7331a3df8cd5d647a573dc15e3a0bb14
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003377"
---
# <a name="operators-in-azure-cosmos-db"></a>A Azure Cosmos DB operátorai

Ez a cikk a Azure Cosmos DB által támogatott különböző operátorokat részletezi.

## <a name="equality-and-comparison-operators"></a>Egyenlőségi és összehasonlító operátorok

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

Az összehasonlító operátorok, `>`például `>=` `!=` `Undefined`a,,, `<=`, és, összehasonlítás különböző típusok vagy két objektum vagy tömb között. `<`  

Ha a skaláris kifejezés `Undefined`eredménye, az elem nem szerepel az eredményben, mert `Undefined` nem egyenlő `true`.

## <a name="logical-and-or-and-not-operators"></a>Logikai (AND, OR és NOT) operátorok

Logikai operátorok a logikai értékek művelethez. Az alábbi táblázatok a logikai igazság táblázatait mutatják be a következő operátorok esetében:

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

A speciális operátor * a teljes tételt a következőképpen tervezi. Használatakor a csak tervezett mezőt kell lennie. A hasonló `SELECT * FROM Families f` lekérdezés érvényes, `SELECT *, f.id FROM Families f` de `SELECT VALUE * FROM Families f` nem érvényes.

## <a name="-and--operators"></a>? és? Operátorok

A Ternáris (?) és az egyesítő (??) operátorok feltételes kifejezéseket hozhatnak létre, például a programozási C# nyelvekhez hasonlóan és a javascripthez. 

Használhatja a? operátor az új JSON-tulajdonságok menet közbeni létrehozásához. Például a következő lekérdezés osztályozza a minősítési szinteket `elementary` a `other`vagy a értékre:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

A hívások beágyazására is lehetőség van? operátor, a következő lekérdezéshez hasonlóan: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Más lekérdezési operátorokhoz hasonlóan a? a kezelő kizárja az elemeket, ha a hivatkozott tulajdonságok hiányoznak, vagy az összehasonlítható típusok eltérnek.

Használja a?? az operátor a részben strukturált vagy kevert típusú adattípusok elleni lekérdezés során hatékonyan keres egy adott elemmel. Például a következő lekérdezés visszatér `lastName` , ha van, vagy `surname` ha `lastName` nincs jelen.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>További lépések

- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [a kulcsszavak](sql-query-keywords.md)
- [SELECT záradék](sql-query-select.md)

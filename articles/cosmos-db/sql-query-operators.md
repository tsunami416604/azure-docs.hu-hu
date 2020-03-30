---
title: SQL-lekérdezés-operátorok az Azure Cosmos DB-hez
description: Ismerje meg az SQL-operátorok, például az egyenlőség, az összehasonlítás és az Azure Cosmos DB által támogatott logikai operátorok.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063574"
---
# <a name="operators-in-azure-cosmos-db"></a>Operátorok az Azure Cosmos DB-ben

Ez a cikk részletezi az Azure Cosmos DB által támogatott különböző operátorokat.

## <a name="equality-and-comparison-operators"></a>Egyenlőség és összehasonlítás operátorok

Az alábbi táblázat az SQL API-ban bármely két JSON-típus közötti egyenlőség-összehasonlítások eredményét mutatja be.

| **Op** | **Meghatározatlan** | **Null** | **Logikai** | **Szám** | **Sztring** | **Objektum** | **Tömb** |
|---|---|---|---|---|---|---|---|
| **Meghatározatlan** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan |
| **Null** | Meghatározatlan | **oké** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan |
| **Logikai** | Meghatározatlan | Meghatározatlan | **oké** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan |
| **Szám** | Meghatározatlan | Meghatározatlan | Meghatározatlan | **oké** | Meghatározatlan | Meghatározatlan | Meghatározatlan |
| **Sztring** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | **oké** | Meghatározatlan | Meghatározatlan |
| **Objektum** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | **oké** | Meghatározatlan |
| **Tömb** | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | Meghatározatlan | **oké** |

Az összehasonlítási operátorok `>=` `!=`, `<`, `<=` `Undefined` `>`, , , és , összehasonlítása típusok között, vagy két objektum vagy tömb termel.  

Ha a skaláris kifejezés `Undefined`eredménye, az elem nem szerepel `Undefined` az eredményben, mert nem egyenlő. `true`

## <a name="logical-and-or-and-not-operators"></a>Logikai (ÉS, OR és NEM) operátorok

A logikai operátorok logikai értékeken működnek. Az alábbi táblázatok az operátorok logikai igazságtábláit mutatják be:

**OR operátor**

Akkor `true` adja vissza, `true`ha a feltételek bármelyike .

|  | **True (Igaz)** | **False (Hamis)** | **Meghatározatlan** |
| --- | --- | --- | --- |
| **True (Igaz)** |True (Igaz) |True (Igaz) |True (Igaz) |
| **False (Hamis)** |True (Igaz) |False (Hamis) |Meghatározatlan |
| **Meghatározatlan** |True (Igaz) |Meghatározatlan |Meghatározatlan |

**ÉS operátor**

Akkor `true` ad vissza, ha mindkét kifejezés `true`.

|  | **True (Igaz)** | **False (Hamis)** | **Meghatározatlan** |
| --- | --- | --- | --- |
| **True (Igaz)** |True (Igaz) |False (Hamis) |Meghatározatlan |
| **False (Hamis)** |False (Hamis) |False (Hamis) |False (Hamis) |
| **Meghatározatlan** |Meghatározatlan |False (Hamis) |Meghatározatlan |

**NEM operátor**

Megfordítja bármely logikai kifejezés értékét.

|  | **Nem** |
| --- | --- |
| **True (Igaz)** |False (Hamis) |
| **False (Hamis)** |True (Igaz) |
| **Meghatározatlan** |Meghatározatlan |

**Operátorok sorrendje**

A logikai `OR` `AND`operátorok `NOT` , és az elsőbbségi szint az alábbi:

| **Művelet** | **Prioritás** |
| --- | --- |
| **Nem** |1 |
| **És** |2 |
| **Vagy** |3 |

## <a name="-operator"></a>* kezelő

A speciális operátor * a teljes cikket úgy vetíti ki, ahogy van. Ha használja, akkor kell az egyetlen kivetített mező. A lekérdezés `SELECT * FROM Families f` ily ként `SELECT VALUE * FROM Families f` `SELECT *, f.id FROM Families f` érvényes, de érvénytelen.

## <a name="-and--operators"></a>? És?? operátorok

A Ternary (?) és a Coalesce (??) operátorok segítségével feltételes kifejezéseket hozhat létre, például a C# és a JavaScript programozási nyelvekben.

Használhatja a ? új JSON-tulajdonságok at fly. A következő lekérdezés például osztályzatot osztályoz a következő be sorolja: `elementary` `other`

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Azt is nest hívásokat a ? operátort, mint a következő lekérdezésben: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Mint más lekérdezési operátorok, a ? az operátor kizárja az elemeket, ha a hivatkozott tulajdonságok hiányoznak, vagy az összehasonlított típusok eltérőek.

Használja a ?? operátorsal hatékonyan ellenőrizheti, hogy egy elemben található-e tulajdonság, amikor félig strukturált vagy vegyes típusú adatokat kérdez. A következő lekérdezés például akkor ad vissza, `lastName` ha jelen van, vagy `surname` ha `lastName` nincs jelen.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Kulcsszavak](sql-query-keywords.md)
- [SELECT záradék](sql-query-select.md)

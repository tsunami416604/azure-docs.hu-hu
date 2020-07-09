---
title: A Azure Cosmos DB SQL-lekérdezési operátorai
description: Ismerje meg az olyan SQL-operátorokat, mint az egyenlőség, az összehasonlítás és a Azure Cosmos DB által támogatott logikai operátorok.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80063574"
---
# <a name="operators-in-azure-cosmos-db"></a>A Azure Cosmos DB operátorai

Ez a cikk a Azure Cosmos DB által támogatott különböző operátorokat részletezi.

## <a name="equality-and-comparison-operators"></a>Egyenlőségi és összehasonlító operátorok

Az alábbi táblázat a két JSON-típus közötti egyenlőség összehasonlításának eredményét mutatja az SQL API-ban.

| **Op** | **Nem definiált** | **Null** | **Logikai** | **Szám** | **Sztring** | **Objektum** | **Tömb** |
|---|---|---|---|---|---|---|---|
| **Nem definiált** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált |
| **Null** | Nem definiált | **oké** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált |
| **Logikai** | Nem definiált | Nem definiált | **oké** | Nem definiált | Nem definiált | Nem definiált | Nem definiált |
| **Szám** | Nem definiált | Nem definiált | Nem definiált | **oké** | Nem definiált | Nem definiált | Nem definiált |
| **Sztring** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | **oké** | Nem definiált | Nem definiált |
| **Objektum** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | **oké** | Nem definiált |
| **Tömb** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | **oké** |

Az összehasonlító operátorok, például a,,, `>` `>=` `!=` `<` , és `<=` , összehasonlítás különböző típusok vagy két objektum vagy tömb között `Undefined` .  

Ha a skaláris kifejezés eredménye `Undefined` , az elem nem szerepel az eredményben, mert `Undefined` nem egyenlő `true` .

## <a name="logical-and-or-and-not-operators"></a>Logikai (és, vagy nem) operátorok

Logikai operátorok logikai értékeken működnek. Az alábbi táblázatok a logikai igazság táblázatait mutatják be a következő operátorok esetében:

**VAGY operátor**

`true`A feltételek bármelyikének beolvasása `true` .

|  | **Igaz** | **Hamis** | **Nem definiált** |
| --- | --- | --- | --- |
| **Igaz** |True (Igaz) |True (Igaz) |True (Igaz) |
| **Hamis** |True (Igaz) |False (Hamis) |Nem definiált |
| **Nem definiált** |True (Igaz) |Nem definiált |Nem definiált |

**ÉS operátor**

Azt adja vissza `true` , hogy mindkét kifejezés hol van `true` .

|  | **Igaz** | **Hamis** | **Nem definiált** |
| --- | --- | --- | --- |
| **Igaz** |True (Igaz) |False (Hamis) |Nem definiált |
| **Hamis** |False (Hamis) |False (Hamis) |False (Hamis) |
| **Nem definiált** |Nem definiált |False (Hamis) |Nem definiált |

**NEM operátor**

Bármely logikai kifejezés értékének megfordítása.

|  | **NEM** |
| --- | --- |
| **Igaz** |False (Hamis) |
| **Hamis** |True (Igaz) |
| **Nem definiált** |Nem definiált |

**Operátori prioritás**

A logikai operátorok, `OR` `AND` és `NOT` az alább látható elsőbbségi szinttel rendelkeznek:

| **Operátor** | **Prioritás** |
| --- | --- |
| **NEM** |1 |
| **ÉS** |2 |
| **VAGY** |3 |

## <a name="-operator"></a>* operátor

A speciális operátor * a teljes tételt a következőképpen tervezi. Használata esetén az csak az egyetlen tervezett mező lehet. A hasonló lekérdezés `SELECT * FROM Families f` érvényes, de `SELECT VALUE * FROM Families f` `SELECT *, f.id FROM Families f` nem érvényes.

## <a name="-and--operators"></a>? és? operátorok

A Ternáris (?) és az egyesítő (??) operátorok feltételes kifejezéseket hozhatnak létre, például a C# és a JavaScript programozási nyelveken.

Használhatja a? operátor az új JSON-tulajdonságok menet közbeni létrehozásához. Például a következő lekérdezés osztályozza a minősítési szinteket a vagy a értékre `elementary` `other` :

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

Használja a?? az operátor a részben strukturált vagy kevert típusú adattípusok elleni lekérdezés során hatékonyan keres egy adott elemmel. Például a következő lekérdezés visszatér `lastName` , ha van, vagy `surname` Ha `lastName` nincs jelen.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>További lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Kulcsszavak](sql-query-keywords.md)
- [SELECT záradék](sql-query-select.md)

---
title: A Azure Cosmos DB SQL-lekérdezési operátorai
description: Ismerje meg az olyan SQL-operátorokat, mint az egyenlőség, az összehasonlítás és a Azure Cosmos DB által támogatott logikai operátorok.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870938"
---
# <a name="operators-in-azure-cosmos-db"></a>A Azure Cosmos DB operátorai

Ez a cikk a Azure Cosmos DB által támogatott különböző operátorokat részletezi.

## <a name="equality-and-comparison-operators"></a>Egyenlőségi és összehasonlító operátorok

Az alábbi táblázat a két JSON-típus közötti egyenlőség összehasonlításának eredményét mutatja az SQL API-ban.

| **Op** | **Nem definiált** | **NULL** | **Logikai** | **Száma** | **Sztring** | **Objektum** | **Tömb** |
|---|---|---|---|---|---|---|---|
| **Nem definiált** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált |
| **NULL** | Nem definiált | **oké** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált |
| **Logikai** | Nem definiált | Nem definiált | **oké** | Nem definiált | Nem definiált | Nem definiált | Nem definiált |
| **Száma** | Nem definiált | Nem definiált | Nem definiált | **oké** | Nem definiált | Nem definiált | Nem definiált |
| **Sztring** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | **oké** | Nem definiált | Nem definiált |
| **Objektum** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | **oké** | Nem definiált |
| **Tömb** | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | Nem definiált | **oké** |

Az összehasonlító operátorok, mint például a `>`, a `>=`, a `!=`, a `<`és a `<=`, a különböző típusú vagy két objektum vagy tömb közötti összehasonlítás `Undefined`hoz létre.  

Ha a skaláris kifejezés eredménye `Undefined`, az elem nem szerepel az eredményben, mert `Undefined` nem egyenlő `true`.

## <a name="logical-and-or-and-not-operators"></a>Logikai (és, vagy nem) operátorok

Logikai operátorok logikai értékeken működnek. Az alábbi táblázatok a logikai igazság táblázatait mutatják be a következő operátorok esetében:

**VAGY operátor**

| VAGY | Igaz | Hamis | Nem definiált |
| --- | --- | --- | --- |
| Igaz |Igaz |Igaz |Igaz |
| Hamis |Igaz |Hamis |Nem definiált |
| Nem definiált |Igaz |Nem definiált |Nem definiált |

**ÉS operátor**

| ÉS | Igaz | Hamis | Nem definiált |
| --- | --- | --- | --- |
| Igaz |Igaz |Hamis |Nem definiált |
| Hamis |Hamis |Hamis |Hamis |
| Nem definiált |Nem definiált |Hamis |Nem definiált |

**NEM operátor**

| NOT |  |
| --- | --- |
| Igaz |Hamis |
| Hamis |Igaz |
| Nem definiált |Nem definiált |


## <a name="-operator"></a>* operátor

A speciális operátor * a teljes tételt a következőképpen tervezi. Használata esetén az csak az egyetlen tervezett mező lehet. Egy lekérdezés, például `SELECT * FROM Families f` érvényes, de a `SELECT VALUE * FROM Families f` és a `SELECT *, f.id FROM Families f` érvénytelen.

## <a name="-and--operators"></a>? és? operátorok

A Ternáris (?) és az egyesítő (??) operátorok feltételes kifejezéseket hozhatnak létre, például a programozási C# nyelvekhez hasonlóan és a javascripthez. 

Használhatja a? operátor az új JSON-tulajdonságok menet közbeni létrehozásához. Az alábbi lekérdezés például osztályozza a minőségi szinteket `elementary` vagy `other`ba:

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

Használja a?? az operátor a részben strukturált vagy kevert típusú adattípusok elleni lekérdezés során hatékonyan keres egy adott elemmel. Például a következő lekérdezés visszaadja `lastName` ha van, vagy `surname`, ha `lastName` nincs jelen.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Következő lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Kulcsszavak](sql-query-keywords.md)
- [SELECT záradék](sql-query-select.md)

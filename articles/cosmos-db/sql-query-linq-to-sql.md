---
title: LINQ –SQL-fordítás az Azure Cosmos DB-ben
description: Ismerje meg a támogatott LINQ-operátorokat, és azt, hogy a LINQ-lekérdezések hogyan vannak leképezve az Azure Cosmos DB SQL-lekérdezéseihez.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441250"
---
# <a name="linq-to-sql-translation"></a>LINQ-SQL fordítás

Az Azure Cosmos DB lekérdezési szolgáltató a linq-lekérdezésből a Cosmos DB SQL-lekérdezésbe történő hozzárendelést hajtja végre. A következő leírás a LINQ alapvető ismeretségét feltételezi.

A lekérdezésszolgáltató típusa rendszer csak a JSON primitív típusokat támogatja: numerikus, logikai, karakterlánc és null.

A lekérdezésszolgáltató a következő skaláris kifejezéseket támogatja:

- Állandó értékek, beleértve a primitív adattípusok állandó értékeit a lekérdezés kiértékelési idején.
  
- Egy objektum vagy tömbelem tulajdonságára hivatkozó tulajdonság-/tömbindex-kifejezések. Példa:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetikai kifejezések, beleértve a numerikus és logikai értékek gyakori aritmetikai kifejezéseit. A teljes listát az [Azure Cosmos DB SQL specifikációjában.](https://go.microsoft.com/fwlink/p/?LinkID=510612)
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Karakterlánc-összehasonlító kifejezések, amelyek egy karakterlánc-érték összehasonlítását foglalják magukban valamilyen állandó karakterláncértékkel.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objektum-/tömblétrehozási kifejezések, amelyek összetett vagy névtelen típusú objektumot vagy ilyen objektumok tömbjét adják vissza. Ezeket az értékeket beágyazhatja.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Támogatott LINQ operátorok

Az SQL .NET SDK részét képező LINQ-szolgáltató a következő operátorokat támogatja:

- **Válassza ki**a : A vetítések SQL SELECT-re vannak lefordítva, beleértve az objektumépítést is.
- **Hol**: A szűrők SQL WHERE-ra, és támogatják a fordítást a , `&&` `||`és `!` az SQL operátorok között
- **SelectMany**: Lehetővé teszi a tömbök oldását az SQL JOIN záradékba. Kifejezések láncolására vagy beágyazására használható a tömbelemek szűréséhez.
- **OrderBy** és **OrderByDescending**: Fordítás rendelésre ASC vagy DESC.
- Az összesítéshez a **count,** **sum**, **min**, **max**és az átlagos operátorok száma, száma és **aszinkron** megfelelőik, valamint a **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**és **AverageAsync**operátorok.
- **CompareTo**: Tartományok összehasonlítására fordítva. A karakterláncok gyakran használatosak, mivel a .NET-ben nem hasonlíthatók össze.
- **Ugrás** és **take:** Sql OFFSET és LIMIT fordítása a lekérdezés ből származó eredmények korlátozására és a tördelésre.
- **Matematikai függvények**: Támogatja `Abs`a `Acos` `Asin`.NET `Cos`, `Exp` `Floor`, `Log` `Log10`, `Pow` `Round` `Sign` `Sin` `Sqrt` `Atan` `Ceiling`, , `Tan`, `Truncate` , , , , , , , , , , , , , , és az egyenértékű SQL beépített függvények fordítását.
- **Karakterlánc-függvények**: Támogatja `Concat`a `Contains` `Count`.NET , , `SubString` `ToLower`, `ToUpper` `TrimEnd` `TrimStart` `EndsWith``IndexOf`, `Replace` `Reverse`, `StartsWith`, , , , , , , , és az egyenértékű SQL beépített függvények fordítását.
- **Tömbfüggvények**: Támogatja a `Concat` `Contains`.NET `Count` , és az egyenértékű SQL beépített függvények fordítását.
- **Térinformatikai bővítmény függvények**: Támogatja `Distance`a `IsValid` `IsValidDetailed`helyettes `Within` metódusok , , , és az egyenértékű SQL beépített függvények fordítását.
- **Felhasználó által definiált függvénybővítmény függvény:** Támogatja a `UserDefinedFunctionProvider.Invoke` stub metódusból a megfelelő felhasználó által definiált függvényre történő fordítást.
- **Egyéb**: Támogatja a feltételes operátorok fordítását `Coalesce` és a feltételes operátorokat. A `Contains` környezettől függően karakterlánc- és ARRAY_CONTAINS vagy SQL IN-re fordítható.

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan a szabványos LINQ-lekérdezési operátorok lefordítani a Cosmos DB-lekérdezések.

### <a name="select-operator"></a>Operátor kiválasztása

A szintaxis `input.Select(x => f(x))`a `f` , ahol a skaláris kifejezés.

**Válassza ki az operátort, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Válassza ki az operátort, 2 példa:** 

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Válassza ki az operátort, 3 példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>SelectMany operátor

A szintaxis `input.SelectMany(x => f(x))`a `f` , ahol egy skaláris kifejezés, amely egy tárolótípust ad vissza.

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Amennyiben az üzemeltető

A szintaxis `input.Where(x => f(x))`a `f` , ahol egy skaláris kifejezés, amely logikai értéket ad vissza.

**Amennyiben az üzemeltető, 1.**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Amennyiben az üzemeltető, 2.**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Összetett SQL-lekérdezések

Az előző operátorokat hatékonyabb lekérdezések létrehozásához írhatja. Mivel a Cosmos DB támogatja a beágyazott tárolókat, összefűzheti vagy beágyazhatja a kompozíciót.

### <a name="concatenation"></a>Összefűzés

A szintaxis `input(.|.SelectMany())(.Select()|.Where())*`a . Az összeakadt lekérdezés ekkre egy választható `SelectMany` lekérdezéssel kezdődhet, amelyet több `Select` vagy `Where` operátor követ.

**Összefűzés, 1.**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Összefűzés, 2.**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Összefűzés, 3.**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Összefűzés, 4.**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Fészkelő

A szintaxis `input.SelectMany(x=>x.Q())` `Q` a `Select`, `SelectMany`vagy `Where` operátor.

A beágyazott lekérdezés a belső lekérdezést alkalmazza a külső tároló minden elemére. Az egyik fontos jellemzője, hogy a belső lekérdezés hivatkozhat a külső tároló elemeinek mezőire, például egy önillesztésre.

**Beágyazás, 1.**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Egymás bafekálása, 2.**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Egymás bafekálása, 3.**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentumadatok modellezése](modeling-data.md)

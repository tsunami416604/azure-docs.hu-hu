---
title: LINQ to SQL fordítás Azure Cosmos DB
description: Ismerje meg, hogy a LINQ-operátorok támogatottak-e, és hogy a LINQ-lekérdezések hogyan vannak leképezve a Azure Cosmos DB SQL-
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d8dd6392cf22852a10c1dc2600edcbc647f3c510
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871159"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL fordítás

A Azure Cosmos DB lekérdezési szolgáltató a LINQ-lekérdezésből származó legjobb műveletet hajtja végre egy Cosmos DB SQL-lekérdezésben. A következő leírás feltételezi, hogy a LINQ alapvető ismerete.

A lekérdezés-szolgáltató típusa a rendszeren csak a JSON egyszerű típusokat támogatja: numerikus, logikai, karakterlánc és null.

A lekérdezési szolgáltató a következő skaláris kifejezéseket támogatja:

- Állandó értékek, beleértve a primitív adattípusok állandó értékeit a lekérdezés kiértékelési idején.
  
- Tulajdonság/tömb index kifejezés, amely egy objektum vagy egy tömb elem tulajdonságára hivatkozik. Példa:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetikai kifejezések, beleértve a közös aritmetikai kifejezéseket a numerikus és a logikai értékeken. A teljes listát lásd: [Azure Cosmos db SQL-specifikáció](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Karakterlánc-összehasonlító kifejezések, amelyek között szerepel egy karakterlánc-érték összehasonlítása egy konstans karakterlánc-értékkel.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objektum/tömb létrehozási kifejezése, amely összetett értéktípus vagy névtelen típus vagy ilyen objektumok tömbjét adja vissza. Ezeket az értékeket beágyazhatja.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Támogatott LINQ-operátorok

Az SQL .NET SDK-ban található LINQ Provider a következő operátorokat támogatja:

- **Válassza ki**a kivetítések lefordítását az SQL SELECT (objektum-kialakítás) elemre.
- **Hol**: a szűrők lefordítva az SQL-re, és a `&&`, `||`és `!` közötti fordítás támogatása az SQL-operátorok számára
- **SelectMany**: lehetővé teszi a tömbök felcsévélését az SQL JOIN záradékba. A paranccsal a tömb elemeinek szűréséhez használhatja a kifejezéseket.
- **OrderBy** és **OrderByDescending**: lefordítható az ASC vagy a desc szerinti sorrendbe.
- Az összesítéshez a **Count**, a **Sum**, a **min**, a **Max**és az **átlag** operátor, valamint a **CountAsync**, a **SumAsync**, a **MinAsync**, a **MaxAsync**és a **AverageAsync**.
- **Compareto metódus végrehajtása**: a tartomány-összehasonlításokra fordít. Általában a sztringek esetében használatos, mivel azok nem összehasonlíthatóak a .NET-ben.
- **Igény**: az SQL Top-re fordítja le a lekérdezés eredményeinek korlátozását.
- **Matematikai függvények**: támogatja a .net `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`és `Truncate` megfelelő SQL beépített függvényeknek való fordítását.
- **Karakterlánc-függvények**: támogatja a .net `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`és `TrimStart` megfelelő SQL beépített függvényeknek való fordítását.
- **Array functions**: támogatja a .net `Concat`, `Contains`és `Count` fordítását a megfelelő SQL beépített függvényekbe.
- **Térinformatikai bővítmény függvények**: a helyettes metódusok `Distance`, `IsValid`, `IsValidDetailed`és `Within`, a megfelelő SQL beépített függvények fordítását támogatja.
- **Felhasználó által definiált függvény bővítményi függvény**: a helyettes metódusból `UserDefinedFunctionProvider.Invoke` a megfelelő felhasználó által definiált függvényhez való fordítást.
- **Egyéb**: a `Coalesce` és a feltételes operátorok fordítását támogatja. A a (z) `Contains` karakterláncot a (z), ARRAY_CONTAINS vagy SQL értékre is lefordíthatja a környezettől függően.

## <a name="examples"></a>Példák

A következő példák azt szemléltetik, hogy a standard LINQ lekérdezési operátorok hogyan fordítanak le Cosmos DB lekérdezésekre.

### <a name="select-operator"></a>Operátor kiválasztása

A szintaxis `input.Select(x => f(x))`, ahol a `f` skaláris kifejezés.

**Operátor kiválasztása, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Operátor kiválasztása, 2. példa:** 

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Operátor kiválasztása, 3. példa:**

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

A szintaxis `input.SelectMany(x => f(x))`, ahol a `f` egy skaláris kifejezés, amely egy tároló típusát adja vissza.

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Where operátor

A szintaxis `input.Where(x => f(x))`, ahol a `f` egy skaláris kifejezés, amely logikai értéket ad vissza.

**Where operátor, 1. példa:**

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
  
**Where operátor, 2. példa:**

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

Az előző operátorokat az erősebb lekérdezések létrehozásához is létrehozhatja. Mivel a Cosmos DB támogatja a beágyazott tárolókat, összefűzheti vagy beágyazhatja az összeállítást.

### <a name="concatenation"></a>Összefűzés

A szintaxis `input(.|.SelectMany())(.Select()|.Where())*`. Az összefűzött lekérdezések nem kötelező `SelectMany` lekérdezéssel kezdődhetnek, amelyet több `Select` vagy `Where` operátor követ.

**Összefűzés, 1. példa:**

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

**Összefűzés, 2. példa:**

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

**Összefűzés, 3. példa:**

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

**Összefűzés, 4. példa:**

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

A szintaxis `input.SelectMany(x=>x.Q())`, ahol a `Q` `Select`, `SelectMany`vagy `Where` operátor.

Egy beágyazott lekérdezés a belső lekérdezést alkalmazza a külső tároló minden elemére. Az egyik fontos funkció, hogy a belső lekérdezés hivatkozhat a külső tárolóban található elemek mezőire, például egy önillesztésre.

**Beágyazás, 1. példa:**

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

**Beágyazás, 2. példa:**

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

**Beágyazás, 3. példa:**

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


## <a name="next-steps"></a>Következő lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentum-adattípusok](modeling-data.md)

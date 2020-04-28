---
title: LINQ to SQL fordítás Azure Cosmos DB
description: Ismerje meg, hogy a LINQ-operátorok támogatottak-e, és hogy a LINQ-lekérdezések hogyan vannak leképezve a Azure Cosmos DB SQL-
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75441250"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL fordítás

A Azure Cosmos DB lekérdezési szolgáltató a LINQ-lekérdezésből származó legjobb műveletet hajtja végre egy Cosmos DB SQL-lekérdezésben. A következő leírás feltételezi, hogy a LINQ alapvető ismerete.

A lekérdezés-szolgáltató típusa a rendszeren csak a JSON egyszerű típusokat támogatja: numerikus, logikai, karakterlánc és null.

A lekérdezési szolgáltató a következő skaláris kifejezéseket támogatja:

- Állandó értékek, beleértve a primitív adattípusok állandó értékeit a lekérdezés kiértékelési idején.
  
- Tulajdonság/tömb index kifejezés, amely egy objektum vagy egy tömb elem tulajdonságára hivatkozik. Például:
  
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

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Támogatott LINQ-operátorok

Az SQL .NET SDK-ban található LINQ Provider a következő operátorokat támogatja:

- **Válassza ki**a kivetítések lefordítását az SQL SELECT (objektum-kialakítás) elemre.
- **Hol**: a szűrők lefordítása az SQL-re, `&&`és `||`a, `!` és az SQL-operátorok közötti fordítás támogatása
- **SelectMany**: lehetővé teszi a tömbök felcsévélését az SQL JOIN záradékba. A paranccsal a tömb elemeinek szűréséhez használhatja a kifejezéseket.
- **OrderBy** és **OrderByDescending**: lefordítható az ASC vagy a desc szerinti sorrendbe.
- Az összesítéshez a **Count**, a **Sum**, a **min**, a **Max**és az **átlag** operátor, valamint a **CountAsync**, a **SumAsync**, a **MinAsync**, a **MaxAsync**és a **AverageAsync**.
- **Compareto metódus végrehajtása**: a tartomány-összehasonlításokra fordít. Általában a sztringek esetében használatos, mivel azok nem összehasonlíthatóak a .NET-ben.
- **Kihagyás** és **igény**: az SQL-eltolás és a lekérdezési eredmények korlátozásának korlátozása és a tördelés végrehajtása.
- **Matematikai függvények**: a .net `Abs`, `Acos` `Asin` `Atan` `Ceiling` `Cos` `Truncate` ,,,,,,,,,,,,,,, és az egyenértékű SQL beépített függvényekre való fordítást támogatja. `Exp` `Floor` `Log` `Log10` `Pow` `Round` `Sign` `Sin` `Sqrt` `Tan`
- **Karakterlánc-függvények**: támogatja a .net `Concat`, `Contains` `Count` `EndsWith``IndexOf` `Replace` `Reverse` `StartsWith` `TrimStart` ,,,,,,,,,, és és az egyenértékű SQL beépített függvények fordítását. `SubString` `ToLower` `ToUpper` `TrimEnd`
- **Array functions**: támogatja a .net `Concat`- `Contains`ről való `Count` fordítást, valamint a megfelelő SQL beépített függvényeket.
- **Térinformatikai bővítmény függvények**: a helyettes metódusokból `Distance`, `IsValid`, `IsValidDetailed`és `Within` az egyenértékű SQL beépített függvényekbe való fordítást támogatja.
- **Felhasználó által definiált függvény kiterjesztési funkciója**: a helyettes metódusból `UserDefinedFunctionProvider.Invoke` a megfelelő felhasználó által definiált függvénybe való fordítást támogatja.
- **Egyéb**: támogatja a és `Coalesce` a feltételes operátorok fordítását. A ( `Contains` z) a (z) függvényt a környezettől függően a karakterláncra, ARRAY_CONTAINS vagy SQL értékre fordíthatja.

## <a name="examples"></a>Példák

A következő példák azt szemléltetik, hogy a standard LINQ lekérdezési operátorok hogyan fordítanak le Cosmos DB lekérdezésekre.

### <a name="select-operator"></a>Operátor kiválasztása

A szintaxis az `input.Select(x => f(x))`, ahol `f` a egy skaláris kifejezés.

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

A szintaxis az `input.SelectMany(x => f(x))`, ahol `f` a egy skaláris kifejezés, amely egy tároló típusát adja vissza.

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

A szintaxis az `input.Where(x => f(x))`, ahol `f` a egy skaláris kifejezés, amely egy logikai értéket ad vissza.

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

A szintaxis: `input(.|.SelectMany())(.Select()|.Where())*`. Az összefűzött lekérdezések egy opcionális `SelectMany` lekérdezéssel kezdődhetnek, amelyet több `Select` vagy `Where` operátor is követ.

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

`input.SelectMany(x=>x.Q())` A szintaxis `Q` az a `Select`, `SelectMany`a vagy `Where` az operátor.

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


## <a name="next-steps"></a>További lépések

- [.NET-minták Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dokumentumadatok modellezése](modeling-data.md)

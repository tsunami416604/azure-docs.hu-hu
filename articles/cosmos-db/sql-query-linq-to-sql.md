---
title: LINQ to SQL fordítás Azure Cosmos DB
description: LINQ-lekérdezések leképezése Azure Cosmos DB SQL-lekérdezésekre.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: ce9d96a90a2463d1ab8e1a9774a019e38ca681f4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036033"
---
# <a name="linq-to-sql-translation"></a>"LINQ to SQL fordítási"

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

- **Válassza**a következőket: A vetítések fordítása az SQL SELECT-be, beleértve az objektumok építését is.
- **Hol**: A szűrők fordítása az SQL-re, és `&&`a `||`, és `!` az SQL-operátorok közötti fordítás támogatása
- **SelectMany**: Lehetővé teszi a tömbök leválasztását az SQL JOIN záradékba. A paranccsal a tömb elemeinek szűréséhez használhatja a kifejezéseket.
- **OrderBy** és **OrderByDescending**: A sorrendet az ASC vagy a DESC értékkel rendezheti le.
- **Száma**, **Sum**, **Min**, **maximális**, és **átlagos** összesítő és a aszinkron megfelelőjükre operátorok**CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, és **AverageAsync**.
- **Compareto metódus végrehajtása**: A tartomány-összehasonlításokra fordít. Általában a sztringek esetében használatos, mivel azok nem összehasonlíthatóak a .NET-ben.
- **Igény**: Az SQL TOP-re fordítja le a lekérdezés eredményeinek korlátozását.
- **Matematikai függvények**: Támogatja `Abs`a .net `Acos` ,`Asin` `Atan` `Ceiling` ,,`Cos`,,,,, ,,,`Floor` `Exp` `Log` `Log10` `Pow` `Round` ,,,`Tan`, és és`Truncate` az egyenértékű SQL beépített függvények. `Sqrt` `Sign` `Sin`
- **Karakterlánc-függvények**: Támogatja `Concat`a .net `Contains` ,`Count` `EndsWith``IndexOf` ,,`Replace`,,,,, ,,,`StartsWith` `Reverse` `SubString` `ToLower` `ToUpper` `TrimEnd`, valamint `TrimStart` a megfelelő SQL beépített függvényekhez.
- **Tömb függvények**: Támogatja a .net `Concat`, `Contains`a és `Count` az egyenértékű SQL beépített függvények fordítását.
- **Térinformatikai bővítmény függvények**: A a helyettes metódusok `Distance` `IsValid` `IsValidDetailed`, a, és `Within` az egyenértékű SQL beépített függvények fordítását támogatja.
- **Felhasználó által definiált függvény bővítményi függvény**: Támogatja a stub metódus `UserDefinedFunctionProvider.Invoke` fordítását a megfelelő felhasználó által definiált függvényre.
- **Egyéb**: Támogatja a és `Coalesce` a feltételes operátorok fordítását. `Contains` A a (z) függvénynek a kontextustól függően a (z) ARRAY_CONTAINS vagy SQL-karakterláncot is tartalmazhatja.

## <a name="examples"></a>Példák

A következő példák azt szemléltetik, hogy a standard LINQ lekérdezési operátorok hogyan fordítanak le Cosmos DB lekérdezésekre.

### <a name="select-operator"></a>Művelet kiválasztása

A szintaxis `input.Select(x => f(x))`, ahol `f` egy skaláris kifejezés.

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

A szintaxis `input.SelectMany(x => f(x))`, ahol `f` egy skaláris kifejezés, amely egy tároló-típust ad vissza.

- **LINQ lambda kifejezés**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Ahol operátor

A szintaxis `input.Where(x => f(x))`, ahol `f` van egy skaláris kifejezés, amely egy logikai értéket ad vissza.

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

A szintaxis `input(.|.SelectMany())(.Select()|.Where())*`. Az összefűzött lekérdezések egy opcionális `SelectMany` lekérdezéssel kezdődhetnek, amelyet több `Select` vagy `Where` operátor is követ.

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

### <a name="nesting"></a>A beágyazási

`input.SelectMany(x=>x.Q())` A szintaxis `Q` az a `Select`, a`SelectMany` vagy`Where` az operátor.

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

- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dokumentum-adattípusok](modeling-data.md)

---
title: "\"LINQ to SQL fordítása az Azure Cosmos DB\""
description: LINQ-lekérdezések leképezése az Azure Cosmos DB SQL-lekérdezéseket.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342569"
---
# <a name="linq-to-sql-translation"></a>"LINQ to SQL fordítási"

Az Azure Cosmos DB-lekérdezésszolgáltató hajt végre, egy Cosmos DB SQL-lekérdezést az ajánlott beavatkozást leképezése a LINQ-lekérdezésekre. A következő leírást egy LINQ alapszintű ismeretét feltételezi.

A lekérdezés szolgáltató típusa rendszer támogatja a csak a JSON egyszerű típusok: numerikus, logikai értéket, a karakterlánc, és NULL értékű.

A lekérdezés-szolgáltató támogatja a következő skaláris kifejezések:

- Állandó értékek, beleértve az állandó értékek, a primitív adattípusokat lekérdezés kiértékelése során.
  
- Egy objektum vagy egy tömbelem tulajdonságára index kifejezés tulajdonságtömb /. Példa:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetikai kifejezésekben, beleértve a numerikus és logikai értékek a közös aritmetikai kifejezésekben. A teljes listát lásd: a [Azure Cosmos DB SQL-specifikáció](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Karakterlánc összehasonlítási kifejezésekben, többek között egy karakterláncértéket néhány állandó karakterlánc értéket összehasonlítása.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objektum vagy tömb létrehozása kifejezés, amely összetett érték vagy névtelen típusú objektumot, vagy ilyen objektumok egy tömbjét adja vissza. Ezek az értékek ágyazhatja be.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>Támogatott LINQ-operátorokat

A LINQ szolgáltató az SQL .NET SDK-val tartalmazza az alábbi műveleteket támogatja:

- **Válassza ki**: Leképezések SQL SELECT, beleértve a objektumkonstrukciók fordítani.
- **Ahol**: Szűrők, amelyek, ahol az SQL, és támogatja a fordítási közötti `&&`, `||`, és `!` , az SQL-operátorok
- **SelectMany**: Lehetővé teszi a tömbök, az SQL JOIN záradék visszagörgetésének. Láncolt, vagy a tömb elemeinek szűréséhez kifejezések beágyazása használatával.
- **OrderBy** és **OrderByDescending**: Az ASC vagy DESC rendezési fordítani.
- **Száma**, **Sum**, **Min**, **maximális**, és **átlagos** összesítő és a aszinkron megfelelőjükre operátorok**CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, és **AverageAsync**.
- **Compareto metódus végrehajtása**: A rendszer lefordítja arra tartomány összehasonlítást. Gyakran használt karakterláncok, mivel azok még nem összehasonlítható a .NET-ben.
- **Igénybe**: A rendszer lefordítja arra az SQL felső korlátozza a lekérdezés eredményeit.
- **Matematikai függvények**: Támogatja a .NET használatával fordítási `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, és `Truncate` a megfelelő SQL a beépített funkciók.
- **Karakterlánc-függvények**: Támogatja a .NET használatával fordítási `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, és `TrimStart` a megfelelő SQL a beépített funkciók.
- **A tömb funkciók**: Támogatja a .NET használatával fordítási `Concat`, `Contains`, és `Count` a megfelelő SQL a beépített funkciók.
- **Térinformatikai bővítmény funkciók**: Támogatja a helyettes módszerek fordítási `Distance`, `IsValid`, `IsValidDetailed`, és `Within` a megfelelő SQL a beépített funkciók.
- **Felhasználó által definiált függvény bővítmény függvény**: Támogatja a helyettes metódus fordítási `UserDefinedFunctionProvider.Invoke` a megfelelő felhasználó által definiált függvénynek.
- **Vegyes**: Támogatja a fordítási `Coalesce` és feltételes operátorokat. Lefordítja `Contains` karakterláncot tartalmaz, ARRAY_CONTAINS vagy SQL a környezettől függően.

## <a name="examples"></a>Példák

Az alábbi példák bemutatják, hogyan a standard szintű LINQ lekérdezés operátorok némelyike lefordítja a Cosmos DB-lekérdezésekre.

### <a name="select-operator"></a>Válasszon operátort

A szintaxis `input.Select(x => f(x))`, ahol `f` egy skaláris kifejezés.

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
  
**Válassza ki az operátort, 2. példa:** 

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Válassza ki az operátort, 3. példa:**

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

**Ahol kezelő, 1. példa:**

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
  
**Ahol kezelő, a 2. példa:**

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

Létrehozhatja azt az előző operátorok nagyobb teljesítményű lekérdezések kialakításához. Mivel a Cosmos DB támogatja a beágyazott tárolókat, fűzze össze, vagy beágyazni az összeállításban.

### <a name="concatenation"></a>Összefűzés

A szintaxis `input(.|.SelectMany())(.Select()|.Where())*`. Egy összefűzött lekérdezés kezdhet egy nem kötelező `SelectMany` lekérdezést, majd több `Select` vagy `Where` operátorok.

**Összefűzés, 1. példa:**

- **LINQ lambda kifejezés**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
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

A szintaxis `input.SelectMany(x=>x.Q())` ahol `Q` van egy `Select`, `SelectMany`, vagy `Where` operátor.

Egy beágyazott lekérdezésen egyes elemei a külső tároló a belső lekérdezés vonatkozik. A rendszer egyik fontos szolgáltatása, hogy a belső lekérdezés a külső tárolóban, például a önillesztés elem mezőire hivatkozhat.

**A beágyazási, 1. példa:**

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

**A beágyazási, 2. példa:**

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

**A beágyazási, 3. példa:**

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
- [Dokumentumadatok modellezése](modeling-data.md)

---
title: FROM záradék az Azure Cosmos DB-ben
description: Ismerje meg az SQL szintaxist, és például az Azure Cosmos DB FROM záradékát. Ez a cikk példákat is bemutat a hatókör eredményeire, és alelemeket kap a FROM záradék használatával.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587685"
---
# <a name="from-clause-in-azure-cosmos-db"></a>FROM záradék az Azure Cosmos DB-ben

A FROM`FROM <from_specification>`( ) záradék nem kötelező, kivéve, ha a forrás a lekérdezés későbbi részében szűrve van, vagy kivetítő. A lekérdezés, mint `SELECT * FROM Families` enumerálni a teljes `Families` tárolót. A tároló neve helyett a ROOT speciális azonosítót is használhatja.

A FROM záradék lekérdezésenként a következő szabályokat érvényesíti:

* A tároló lehet alias, `SELECT f.id FROM Families AS f` például `SELECT f.id FROM Families f`vagy egyszerűen . Itt `f` az alias `Families`a . Az AS egy választható kulcsszó az azonosító [aliasához.](sql-query-aliasing.md)  

* Az aliasután az eredeti forrásnév nem köthető össze. Például `SELECT Families.id FROM Families f` szintaktikailag érvénytelen, mert `Families` az azonosító aliasban van, és többé nem oldható fel.  

* Minden hivatkozott tulajdonságnak teljesen minősítettnek kell lennie, hogy szigorú sémabetapadás hiányában ne legyenek kétértelmű kötések. Például `SELECT id FROM Families f` szintaktikailag érvénytelen, mert `id` a tulajdonság nincs kötve.

## <a name="syntax"></a>Szintaxis
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumentumok
  
- `<from_source>`  
  
  Adatforrást ad meg, aliassal vagy anélkül. Ha az alias nincs megadva, a `<container_expression>` következő szabályok alapján következik ki:  
  
  -  Ha a kifejezés container_name, akkor container_name lesz aliasként használva.  
  
  -  Ha a `<container_expression>`kifejezés , majd property_name, akkor property_name lesz aliasként használva. Ha a kifejezés container_name, akkor container_name lesz aliasként használva.  
  
- Másként`input_alias`  
  
  Itt adhatja `input_alias` meg, hogy az az alapul szolgáló tárolókifejezés által visszaadott értékhalmaz.  
 
- `input_alias`IN (A)  
  
  Itt adható `input_alias` meg, hogy az értékhalmazt az alapul szolgáló tárolókifejezés által visszaadott tömbök összes tömbelemének iterálásával kapott értékhalmaznak kell-e képviselnie. Az alapul szolgáló tárolókifejezés által visszaadott értékeket, amelyek nem tömbök, figyelmen kívül hagyják.  
  
- `<container_expression>`  
  
  Megadja a dokumentumok beolvasásához használandó tárolókifejezést.  
  
- `ROOT`  
  
  Itt adható meg, hogy a dokumentumot az alapértelmezett, jelenleg csatlakoztatott tárolóból kell beolvasni.  
  
- `container_name`  
  
  Itt adható meg, hogy a dokumentumot a megadott tárolóból kell beolvasni. A tároló nevének meg kell egyeznie a jelenleg csatlakoztatott tároló nevével.  
  
- `input_alias`  
  
  Itt adható meg, hogy a dokumentumot a megadott alias által meghatározott másik forrásból kell beolvasni.  
  
- `<container_expression> '.' property_name`  
  
  Itt adható meg, hogy a dokumentumot a `property_name` tulajdonság elérésével kell beolvasni.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Itt adható meg, hogy a dokumentumot a `property_name` megadott tárolókifejezéssel beolvasott összes dokumentum tulajdonságának vagy array_index elemének elérésével kell beolvasni.  
  
## <a name="remarks"></a>Megjegyzések
  
Az `<from_source>(`s-ben megadott vagy kikövetkeztetett összes aliasnak egyedinek kell lennie. A szintaxis `<container_expression>.`property_name megegyezik a . `<container_expression>' ['"property_name"']'` Az utóbbi szintaxis azonban akkor használható, ha a tulajdonságnév nem azonosító karaktert tartalmaz.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Hiányzó tulajdonságok, hiányzó tömbelemek és nem definiált értékek kezelése
  
Ha egy tárolókifejezés tulajdonságokhoz vagy tömbelemekhez fér hozzá, és ez az érték nem létezik, a rendszer figyelmen kívül hagyja az értéket, és nem dolgozza fel tovább.  
  
### <a name="container-expression-context-scoping"></a>Tárolókifejezés-környezet hatókörének hatóköre  
  
A tárolókifejezések lehetnek tárolószintű vagy dokumentum-hatókörűek:  
  
-   A kifejezés tárolóhatókörrel van elszórtan, ha a `container_name`tárolókifejezés mögöttes forrása ROOT vagy . Az ilyen kifejezés a tárolóból közvetlenül beolvasott dokumentumok készletét jelöli, és nem függ más tárolókifejezések feldolgozásától.  
  
-   Egy kifejezés dokumentum-hatókörű, ha az alapul szolgáló `input_alias` forrása a tároló kifejezés vezet be korábban a lekérdezésben. Az ilyen kifejezés az aliastárolóhoz társított készlethez tartozó minden egyes dokumentum hatókörében lévő tárolókifejezés kiértékelésével kapott dokumentumok egy készletét jelöli.  Az eredményül kapott halmaz az alapul szolgáló készlet egyes dokumentumainak tárolókifejezésének kiértékelésével kapott készletek egybefűzős egysége lesz. 

## <a name="examples"></a>Példák

### <a name="get-subitems-by-using-the-from-clause"></a>Alelemek beszereznie a FROM záradék használatával

A FROM záradék a forrást kisebb részhalmazra csökkentheti. Ha csak egy részfát szeretne számba venni az egyes elemekben, az algyökér válhat forrássá, ahogy az a következő példában látható:

```sql
    SELECT *
    FROM Families.children
```

Az eredmény a következő:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Az előző lekérdezés egy tömböt használt forrásként, de egy objektumot is használhat forrásként. A lekérdezés figyelembe vesz minden érvényes, meghatározott JSON-értéket a forrásban az eredménybe való felvételhez. A következő példa `Families` kizárná, `address.state` hogy nincs értéke.

```sql
    SELECT *
    FROM Families.address.state
```

Az eredmény a következő:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>További lépések

- [Kezdetekhez](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [WHERE záradék](sql-query-where.md)

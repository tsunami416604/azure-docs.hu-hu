---
title: Az Azure Cosmos DB záradék
description: Ismerje meg az Azure Cosmos DB SQL FROM záradék
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 6bc93569dc9a0405ec3a8dfd719c89ede01df84d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342757"
---
# <a name="from-clause"></a>FROM záradékban

A feladó (`FROM <from_specification>`) záradék nem kötelező, kivéve, ha a forrás van szűrve, vagy előre jelzett költségről később a lekérdezésben. A lekérdezés, például `SELECT * FROM Families` számba veszi át a teljes `Families` tároló. A speciális azonosító legfelső szintű is használható, ha a tároló a tároló nevének használata helyett.

A FROM záradék a következő szabályok lekérdezésenként érvényesíti:

* A tároló lehet aliassal, például `SELECT f.id FROM Families AS f` vagy egyszerűen csak `SELECT f.id FROM Families f`. Itt `f` a hivatkozási `Families`. MIVEL egy nem kötelező kulcsszó használatával [alias](sql-query-aliasing.md) azonosítóját.  

* Egyszer aliassal, az eredeti adatforrás neve nem köthető. Például `SELECT Families.id FROM Families f` szintaktikailag mert azonosító `Families` alias lett, és többé nem lehet feloldani.  

* Lehet, hogy az összes hivatkozott tulajdonság teljesen minősített, szigorú sémát megfelelést hiányában nem egyértelmű kötések elkerülése érdekében. Ha például `SELECT id FROM Families f` szintaktikailag mert tulajdonság `id` nincs kötve.

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
  
  Egy adatforrás, itt adhatja meg, vagy az alias nélkül. Ha nincs megadva alias, azt fogja nelze odvodit z a `<container_expression>` szabályok a következő használatával:  
  
  -  Ha a kifejezés egy container_name, majd container_name használható aliasként.  
  
  -  Ha a kifejezés `<container_expression>`, akkor property_name, majd property_name használja a rendszer aliasként. Ha a kifejezés egy container_name, majd container_name használható aliasként.  
  
- AS `input_alias`  
  
  Megadja, hogy a `input_alias` olyan készlete, a mögöttes tároló-kifejezés által visszaadott értékek.  
 
- `input_alias` IN  
  
  Megadja, hogy a `input_alias` kell kívánt csoportjának megfelelő értékeket a léptetés keresztül minden egyes a mögöttes tároló-kifejezés által visszaadott tömb összes tömbelemek rekordsémáját. Bármely, amely nem tömb mögöttes tároló-kifejezés által visszaadott értéket a rendszer figyelmen kívül hagyja.  
  
- `<container_expression>`  
  
  Meghatározza azt a tárolót, lekérheti a dokumentumokat.  
  
- `ROOT`  
  
  Megadja, hogy a dokumentum az alapértelmezett beállítás, a jelenleg csatlakoztatott tároló lehet beolvasni.  
  
- `container_name`  
  
  Megadja, hogy a dokumentum a megadott tároló lehet beolvasni. A tároló neve meg kell egyeznie a jelenleg csatlakoztatott tároló nevét.  
  
- `input_alias`  
  
  Megadja, hogy a dokumentum határozzák meg a megadott alias a más forrásból kell olvasni.  
  
- `<container_expression> '.' property_`  
  
  Itt adhatja meg, a dokumentum elérésével legyen beolvasva a `property_name` által lekért összes dokumentum vlastnost nebo tömbindex tömbelem megadott tároló kifejezés.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Itt adhatja meg, a dokumentum elérésével legyen beolvasva a `property_name` által lekért összes dokumentum vlastnost nebo tömbindex tömbelem megadott tároló kifejezés.  
  
## <a name="remarks"></a>Megjegyzések
  
Összes alias következtetni vagy megadott a `<from_source>(`s) egyedinek kell lennie. A szintaxist `<container_expression>.`property_name megegyezik a `<container_expression>' ['"property_name"']'`. Az utóbbi szintaxis azonban használható, ha egy tulajdonságnév nem azonosító karaktert tartalmaz.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Hiányzó tulajdonságok, hiányzik a tömb elemei, és nem definiált értékek kezelése
  
Ha egy tároló kifejezés fér hozzá, tulajdonságok vagy a tömb elemeinek és, hogy az érték nem létezik, ezt az értéket figyelmen kívül hagyja, és további nincs feldolgozva.  
  
### <a name="container-expression-context-scoping"></a>Tároló kifejezés környezeti hatókör  
  
Egy tároló-kifejezés lehet tároló hatókörű vagy dokumentum hatókörű:  
  
-   Egy kifejezés, tároló-hatáskörű, ha a tároló kifejezés az alapul szolgáló forrás vagy a legfelső szintű vagy `container_name`. Az ilyen kifejezés dokumentumok, a tároló közvetlenül lekért adatok halmazai, és nem függ más tároló-kifejezések feldolgozása.  
  
-   Egy kifejezés, dokumentum-hatáskörű, ha az alapul szolgáló adatforrás a tároló kifejezés `input_alias` jelent meg a korábban a lekérdezésben. Ilyen egy kifejezés kiértékelése a fióknévnek tároló társított csoporthoz tartozó egyes dokumentumok hatókörében a tároló kifejezést kapott dokumentumokat egy halmazát jelölik.  Eredő beállítása során az egyes dokumentumok az alapul szolgáló készletben a tároló kifejezés által beszerzett egy Uniója fogja. 

## <a name="examples"></a>Példák

### <a name="get-subitems-by-using-the-from-clause"></a>A FROM záradék használatával elem beolvasása

A FROM záradék csökkentheti a forrás egy kisebb részhalmazra. Csak az egyes elemek részfájának enumerálása, a subroot válhat a forrás, az alábbi példában látható módon:

```sql
    SELECT *
    FROM Families.children
```

Az eredmények a következők:

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

Az előző lekérdezés egy tömb használja forrásként, de a forrásként egy objektumot is használhatja. A lekérdezés minden olyan érvényes, megadott JSON érték az eredménye, hogy a forrás figyelembe veszi. Az alábbi példa kizárja a `Families` , amely nem rendelkezik egy `address.state` értéket.

```sql
    SELECT *
    FROM Families.address.state
```

Az eredmények a következők:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>További lépések

- [Bevezetés](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [WHERE záradék](sql-query-where.md)
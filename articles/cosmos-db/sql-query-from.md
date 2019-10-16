---
title: FROM záradék Azure Cosmos DB
description: Az SQL FROM záradék megismerése Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 79bb17277a041f71c095ed724737012f9501f16f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327001"
---
# <a name="from-clause-in-azure-cosmos-db"></a>FROM záradék Azure Cosmos DB

A FROM (`FROM <from_specification>`) záradék nem kötelező, kivéve, ha a forrást a lekérdezésben később szűrik vagy tervezték meg. Egy lekérdezés, például @no__t – 0, a teljes `Families` tárolón lévő enumerálások. A tároló neve helyett a speciális azonosító GYÖKERÉt is használhatja a tárolóhoz.

A FROM záradék a következő szabályokat alkalmazza a lekérdezésekhez:

* A tároló alias lehet, például `SELECT f.id FROM Families AS f` vagy egyszerűen `SELECT f.id FROM Families f`. Itt `f` a `Families` aliasa. MINT egy opcionális kulcsszó az azonosító [aliasához](sql-query-aliasing.md) .  

* Az alias megadása után az eredeti forrás neve nem köthető. A `SELECT Families.id FROM Families f` például szintaktikaien érvénytelen, mert a (`Families` azonosító) alias lett, és már nem oldható fel.  

* Minden hivatkozott tulajdonságnak teljes mértékben minősítettnek kell lennie, hogy elkerülje a nem egyértelmű kötéseket a szigorú sémák betartásának hiányában. A `SELECT id FROM Families f` például szintaktikai módon érvénytelen, mert a (`id`) tulajdonság nincs kötve.

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
  
  Egy adatforrás megadása aliassal vagy anélkül. Ha nincs megadva alias, a rendszer a következő szabályok alapján következtet a `<container_expression>` értékkel:  
  
  -  Ha a kifejezés egy container_name, akkor a rendszer aliasként fogja használni a container_name.  
  
  -  Ha a kifejezés `<container_expression>`, akkor a rendszer a property_name, majd aliasként fogja használni a property_name. Ha a kifejezés egy container_name, akkor a rendszer aliasként fogja használni a container_name.  
  
- MINT @no__t – 0  
  
  Megadja, hogy a `input_alias` a mögöttes tároló kifejezés által visszaadott értékek halmaza.  
 
- @no__t – 0 a  
  
  Megadja, hogy a `input_alias` értéknek az alapul szolgáló tároló kifejezése által visszaadott tömb összes tömb elemének megismétlésével kapott értékek halmazát kell képviselnie. A mögöttes tároló kifejezés által visszaadott összes érték figyelmen kívül lesz hagyva.  
  
- `<container_expression>`  
  
  Meghatározza a dokumentumok beolvasásához használandó tároló kifejezését.  
  
- `ROOT`  
  
  Megadja, hogy a rendszer beolvassa a dokumentumot az alapértelmezett, jelenleg csatlakoztatott tárolóból.  
  
- `container_name`  
  
  Megadja, hogy a dokumentumot a megadott tárolóból kell beolvasni. A tároló nevének meg kell egyeznie a jelenleg csatlakoztatott tároló nevével.  
  
- `input_alias`  
  
  Megadja, hogy a dokumentumot a megadott alias által definiált másik forrásból kell beolvasni.  
  
- `<container_expression> '.' property_`  
  
  Megadja, hogy a dokumentumot a megadott tároló kifejezés által beolvasott összes dokumentum `property_name` tulajdonságának vagy tömbindex Array elemének elérésével kell beolvasni.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Megadja, hogy a dokumentumot a megadott tároló kifejezés által beolvasott összes dokumentum `property_name` tulajdonságának vagy tömbindex Array elemének elérésével kell beolvasni.  
  
## <a name="remarks"></a>Megjegyzések
  
Az `<from_source>(`s) összes megadott vagy következtetett aliasnevének egyedinek kell lennie. A `<container_expression>.`property_name szintaxisa megegyezik a `<container_expression>' ['"property_name"']'` értékkel. Az utóbbi szintaxis azonban akkor használható, ha egy tulajdonság neve nem azonosító karaktert tartalmaz.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Hiányzó tulajdonságok, hiányzó tömb elemek és nem definiált értékek kezelésére
  
Ha egy tároló kifejezése hozzáfér a Tulajdonságok vagy a tömb elemeihez, és ez az érték nem létezik, a rendszer figyelmen kívül hagyja ezt az értéket, és nem dolgozza fel őket.  
  
### <a name="container-expression-context-scoping"></a>Container kifejezés kontextusának hatóköre  
  
A Container kifejezés lehet tároló-hatókörű vagy dokumentum – hatókör:  
  
-   A kifejezés tároló hatókörű, ha a tároló kifejezésének alapjául szolgáló forrása a ROOT vagy a `container_name`. Ez a kifejezés a tárolóból közvetlenül beolvasott dokumentumok készletét jelöli, és nem függ más tároló-kifejezések feldolgozásából.  
  
-   A kifejezés dokumentum-hatókörű, ha a tároló kifejezésének alapjául szolgáló forrása @no__t – 0 a lekérdezés korábbi részében. Ez a kifejezés a tároló kifejezésnek az alias-tárolóhoz társított készlethez tartozó hatókörében való kiértékelésével beszerzett dokumentumok készletét jelöli.  Az eredményül kapott készlet az alapul szolgáló készletben lévő összes dokumentum tároló kifejezésének kiértékelésével létrejött készletek Uniója lesz. 

## <a name="examples"></a>Példák

### <a name="get-subitems-by-using-the-from-clause"></a>Alelemek lekérése a FROM záradék használatával

A FROM záradék kisebb részhalmazra csökkentheti a forrást. Ahhoz, hogy az egyes elemekben csak egy részfa legyen felsorolva, az algyökér forrása lehet, ahogy az az alábbi példában látható:

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

Az előző lekérdezés a tömböt használta forrásként, de egy objektumot is használhat forrásként. A lekérdezés az eredménybe való felvételhez a forrásban érvényes, megadott JSON-értéket veszi figyelembe. A következő példa kizárja a `Families` értéket, amely nem rendelkezik `address.state` értékkel.

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

## <a name="next-steps"></a>Következő lépések

- [Első lépések](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [WHERE záradék](sql-query-where.md)
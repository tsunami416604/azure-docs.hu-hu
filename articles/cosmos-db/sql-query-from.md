---
title: FROM záradék Azure Cosmos DB
description: Ismerje meg az SQL-szintaxist, és példát az Azure Cosmos DB FROM záradékra. Ez a cikk példákat is mutat a hatókör eredményeire, és a FROM záradék használatával beolvassa az alárendelt elemeket.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587685"
---
# <a name="from-clause-in-azure-cosmos-db"></a>FROM záradék Azure Cosmos DB

A FROM (`FROM <from_specification>`) záradék nem kötelező, kivéve, ha a forrást a lekérdezésben később szűrik vagy tervezték. Egy lekérdezés, például `SELECT * FROM Families` enumerálások a teljes `Families` tárolóban. A tároló neve helyett a speciális azonosító GYÖKERÉt is használhatja a tárolóhoz.

A FROM záradék a következő szabályokat alkalmazza a lekérdezésekhez:

* A tároló lehet alias, például `SELECT f.id FROM Families AS f` vagy egyszerűen `SELECT f.id FROM Families f`. Itt `f` a `Families`aliasa. MINT egy opcionális kulcsszó az azonosító [aliasához](sql-query-aliasing.md) .  

* Az alias megadása után az eredeti forrás neve nem köthető. A `SELECT Families.id FROM Families f` például érvénytelen, mert az azonosító `Families` aliasa van, és többé nem oldható fel.  

* Minden hivatkozott tulajdonságnak teljes mértékben minősítettnek kell lennie, hogy elkerülje a nem egyértelmű kötéseket a szigorú sémák betartásának hiányában. A `SELECT id FROM Families f` például érvénytelen, mert a tulajdonság `id` nincs kötve.

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
  
  Egy adatforrás, itt adhatja meg, vagy az alias nélkül. Ha nincs megadva alias, a rendszer a következő szabályok alapján következteti ki a `<container_expression>`:  
  
  -  Ha a kifejezés egy container_name, majd container_name használható aliasként.  
  
  -  Ha a kifejezés `<container_expression>`, akkor property_name, property_name aliasként lesz használva. Ha a kifejezés egy container_name, majd container_name használható aliasként.  
  
- MINT `input_alias`  
  
  Megadja, hogy a `input_alias` az alapul szolgáló tároló kifejezés által visszaadott értékek halmaza.  
 
- `input_alias` a  
  
  Megadja, hogy a `input_alias` a mögöttes tároló kifejezés által visszaadott tömb összes tömb elemének megismétlésével megadott értékek halmazát adja meg. Bármely, amely nem tömb mögöttes tároló-kifejezés által visszaadott értéket a rendszer figyelmen kívül hagyja.  
  
- `<container_expression>`  
  
  Meghatározza azt a tárolót, lekérheti a dokumentumokat.  
  
- `ROOT`  
  
  Megadja, hogy a dokumentum az alapértelmezett beállítás, a jelenleg csatlakoztatott tároló lehet beolvasni.  
  
- `container_name`  
  
  Megadja, hogy a dokumentum a megadott tároló lehet beolvasni. A tároló neve meg kell egyeznie a jelenleg csatlakoztatott tároló nevét.  
  
- `input_alias`  
  
  Megadja, hogy a dokumentum határozzák meg a megadott alias a más forrásból kell olvasni.  
  
- `<container_expression> '.' property_name`  
  
  Megadja, hogy a dokumentumot a `property_name` tulajdonság elérésével kell beolvasni.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Azt adja meg, hogy a dokumentumot a megadott tároló kifejezés által beolvasott összes dokumentum `property_name` tulajdonságával vagy array_index tömb elemével kell beolvasni.  
  
## <a name="remarks"></a>Megjegyzések
  
Minden, a `<from_source>(`s-ben megadott vagy oda kikövetkeztetett aliasnak egyedinek kell lennie. A `<container_expression>.`property_name szintaxisa ugyanaz, mint `<container_expression>' ['"property_name"']'`. Az utóbbi szintaxis azonban használható, ha egy tulajdonságnév nem azonosító karaktert tartalmaz.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Hiányzó tulajdonságok, hiányzik a tömb elemei, és nem definiált értékek kezelése
  
Ha egy tároló kifejezés fér hozzá, tulajdonságok vagy a tömb elemeinek és, hogy az érték nem létezik, ezt az értéket figyelmen kívül hagyja, és további nincs feldolgozva.  
  
### <a name="container-expression-context-scoping"></a>Tároló kifejezés környezeti hatókör  
  
Egy tároló-kifejezés lehet tároló hatókörű vagy dokumentum hatókörű:  
  
-   A kifejezés tároló hatókörű, ha a tároló kifejezésének alapjául szolgáló forrása gyökér vagy `container_name`. Az ilyen kifejezés dokumentumok, a tároló közvetlenül lekért adatok halmazai, és nem függ más tároló-kifejezések feldolgozása.  
  
-   A kifejezés dokumentum-hatókörű, ha a tároló kifejezés alapjául szolgáló forrás `input_alias` a lekérdezés korábbi részében van bevezetve. Ilyen egy kifejezés kiértékelése a fióknévnek tároló társított csoporthoz tartozó egyes dokumentumok hatókörében a tároló kifejezést kapott dokumentumokat egy halmazát jelölik.  Eredő beállítása során az egyes dokumentumok az alapul szolgáló készletben a tároló kifejezés által beszerzett egy Uniója fogja. 

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

Az előző lekérdezés a tömböt használta forrásként, de egy objektumot is használhat forrásként. A lekérdezés az eredménybe való felvételhez a forrásban érvényes, megadott JSON-értéket veszi figyelembe. Az alábbi példa kizárja azokat a `Families`, amelyek nem rendelkeznek `address.state` értékkel.

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

- [Bevezetés](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [WHERE záradék](sql-query-where.md)

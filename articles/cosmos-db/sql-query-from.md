---
title: FROM záradék Azure Cosmos DB
description: Ismerje meg az SQL-szintaxist, és példát az Azure Cosmos DB FROM záradékra. Ez a cikk példákat is mutat a hatókör eredményeire, és a FROM záradék használatával beolvassa az alárendelt elemeket.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 4f6d7580ea7ff0e8968c0c3ce4b3ca6111c86ac8
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873369"
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
  
  Egy adatforrás megadása aliassal vagy anélkül. Ha nincs megadva alias, a rendszer a következő szabályok alapján következteti ki a `<container_expression>`:  
  
  -  Ha a kifejezés container_name, akkor container_name aliasként lesz használva.  
  
  -  Ha a kifejezés `<container_expression>`, akkor property_name, property_name aliasként lesz használva. Ha a kifejezés container_name, akkor container_name aliasként lesz használva.  
  
- MINT `input_alias`  
  
  Megadja, hogy a `input_alias` az alapul szolgáló tároló kifejezés által visszaadott értékek halmaza.  
 
- `input_alias` a  
  
  Megadja, hogy a `input_alias` a mögöttes tároló kifejezés által visszaadott tömb összes tömb elemének megismétlésével megadott értékek halmazát adja meg. A mögöttes tároló kifejezés által visszaadott összes érték figyelmen kívül lesz hagyva.  
  
- `<container_expression>`  
  
  Meghatározza a dokumentumok beolvasásához használandó tároló kifejezését.  
  
- `ROOT`  
  
  Megadja, hogy a rendszer beolvassa a dokumentumot az alapértelmezett, jelenleg csatlakoztatott tárolóból.  
  
- `container_name`  
  
  Megadja, hogy a dokumentumot a megadott tárolóból kell beolvasni. A tároló nevének meg kell egyeznie a jelenleg csatlakoztatott tároló nevével.  
  
- `input_alias`  
  
  Megadja, hogy a dokumentumot a megadott alias által definiált másik forrásból kell beolvasni.  
  
- `<container_expression> '.' property_`  
  
  Azt adja meg, hogy a dokumentumot a megadott tároló kifejezés által beolvasott összes dokumentum `property_name` tulajdonságával vagy array_index tömb elemével kell beolvasni.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Azt adja meg, hogy a dokumentumot a megadott tároló kifejezés által beolvasott összes dokumentum `property_name` tulajdonságával vagy array_index tömb elemével kell beolvasni.  
  
## <a name="remarks"></a>Megjegyzések
  
Minden, a `<from_source>(`s-ben megadott vagy oda kikövetkeztetett aliasnak egyedinek kell lennie. A `<container_expression>.`property_name szintaxisa ugyanaz, mint `<container_expression>' ['"property_name"']'`. Az utóbbi szintaxis azonban akkor használható, ha egy tulajdonság neve nem azonosító karaktert tartalmaz.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Hiányzó tulajdonságok, hiányzó tömb elemek és nem definiált értékek kezelésére
  
Ha egy tároló kifejezése hozzáfér a Tulajdonságok vagy a tömb elemeihez, és ez az érték nem létezik, a rendszer figyelmen kívül hagyja ezt az értéket, és nem dolgozza fel őket.  
  
### <a name="container-expression-context-scoping"></a>Container kifejezés kontextusának hatóköre  
  
A Container kifejezés lehet tároló-hatókörű vagy dokumentum – hatókör:  
  
-   A kifejezés tároló hatókörű, ha a tároló kifejezésének alapjául szolgáló forrása gyökér vagy `container_name`. Ez a kifejezés a tárolóból közvetlenül beolvasott dokumentumok készletét jelöli, és nem függ más tároló-kifejezések feldolgozásából.  
  
-   A kifejezés dokumentum-hatókörű, ha a tároló kifejezés alapjául szolgáló forrás `input_alias` a lekérdezés korábbi részében van bevezetve. Ez a kifejezés a tároló kifejezésnek az alias-tárolóhoz társított készlethez tartozó hatókörében való kiértékelésével beszerzett dokumentumok készletét jelöli.  Az eredményül kapott készlet az alapul szolgáló készletben lévő összes dokumentum tároló kifejezésének kiértékelésével létrejött készletek Uniója lesz. 

## <a name="examples"></a>Példák

### <a name="get-subitems-by-using-the-from-clause"></a>Alelemek lekérése a FROM záradék használatával

A FROM záradék kisebb részhalmazra csökkentheti a forrást. Ahhoz, hogy az egyes elemekben csak egy részfa legyen felsorolva, az algyökér forrása lehet, ahogy az az alábbi példában látható:

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

Az előző lekérdezés a tömböt használta forrásként, de egy objektumot is használhat forrásként. A lekérdezés az eredménybe való felvételhez a forrásban érvényes, megadott JSON-értéket veszi figyelembe. Az alábbi példa kizárja azokat a `Families`, amelyek nem rendelkeznek `address.state` értékkel.

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

## <a name="next-steps"></a>Következő lépések

- [Első lépések](sql-query-getting-started.md)
- [SELECT záradék](sql-query-select.md)
- [WHERE záradék](sql-query-where.md)
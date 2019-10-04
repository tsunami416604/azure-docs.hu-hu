---
title: Skaláris kifejezések a Azure Cosmos DB SQL-lekérdezésekben
description: A skaláris kifejezés SQL-szintaxisának megismerése Azure Cosmos DBhoz.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: c35ad65a584f8ee95142e9bc85a58b5b6cd99744
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003542"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skaláris kifejezések a Azure Cosmos DB SQL-lekérdezésekben

A [Select záradék](sql-query-select.md) támogatja a skaláris kifejezéseket. Egy skaláris kifejezés szimbólumok és egyetlen értéket beszerzése kiértékelése operátorok kombinációja. Skaláris kifejezések például a következők: konstansok, tulajdonság-referenciák, tömb elemek hivatkozásai, alias-hivatkozások vagy függvények hívásai. A skaláris kifejezések összetett kifejezésekkel kombinálhatók a kezelők használatával.

## <a name="syntax"></a>Szintaxis
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumentumok
  
- `<constant>`  
  
   Egy állandó értéket jelöli. Lásd: [állandók](sql-query-constants.md) című szakasz részletezi.  
  
- `input_alias`  
  
   Egy által definiált értéket képvisel a `input_alias` rendszerben bevezetett a `FROM` záradékban.  
  Ez az érték nem lehet garantáltan **nem definiált** –**nem definiált** a rendszer kihagyja a bemeneti értékeket.  
  
- `<scalar_expression>.property_name`  
  
   A tulajdonság az objektum olyan értékét jelöli. Ha a tulajdonság nem létezik, vagy a tulajdonság egy értékre hivatkozik, amely nem objektum, a kifejezés kiértékelése nem **definiált** értékre történik.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Egy tömb indexével `property_name` `array_index` rendelkező Name vagy Array elemet tartalmazó tulajdonság értékét jelöli. A tulajdonság/tömbindex nem létezik, vagy a tulajdonság/tömbindex hivatkozik egy érték alapján ez nem egy objektum vagy tömb, akkor a kifejezés eredménye nem meghatározott értéket eredményezne.  
  
- `unary_operator <scalar_expression>`  
  
   Az operátor alkalmazott egyetlen értéket jelöli. Lásd: [operátorok](sql-query-operators.md) című szakasz részletezi.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Az operátor alkalmazott két értéket jelöli. Lásd: [operátorok](sql-query-operators.md) című szakasz részletezi.  
  
- `<scalar_function_expression>`  
  
   Egy adott hívás eredménye által definiált értéket jelöli.  
  
- `udf_scalar_function`  
  
   A felhasználó által definiált skaláris függvény nevét.  
  
- `builtin_scalar_function`  
  
   A beépített skaláris függvény nevét.  
  
- `<create_object_expression>`  
  
   Egy új objektumot hoz létre a megadott tulajdonságokkal kapott értéket és az értéküket jelöli.  
  
- `<create_array_expression>`  
  
   Hozzon létre egy új tömbben elemként megadott értékekkel beszerzett értéket jelöli.  
  
- `parameter_name`  
  
   A megadott paraméternév egy értékét jelöli. A paraméter nevének rendelkeznie kell egy \@ az első karakter.  
  
## <a name="remarks"></a>Megjegyzések
  
  Beépített vagy felhasználó által definiált skaláris függvény meghívásakor az összes argumentumot meg kell adni. Ha bármelyik argumentum nem definiált, a függvény nem hívható, és az eredmény az lesz meghatározva.  
  
  Objektum létrehozásakor bármely vlastnost, amelyek nem definiált értéket kapja kihagyva, és nem tartalmazza a létrehozott objektum.  
  
  Ha egy tömb, bármely elem érték létrehozása, amely hozzá van rendelve **nem definiált** érték lesz kihagyva és nem tartalmazza a létrehozott objektum. Ennek hatására úgy, hogy a létrehozott tömb nem rendelkezik kihagyja az indexek a sor a következő meghatározott elemhez.  

## <a name="examples"></a>Példák

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Az eredmények a következők:

```json
    [{
      "$1": 1.33333
    }]
```

A következő lekérdezésben a skaláris kifejezés eredménye egy logikai érték:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Az eredmények a következők:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>További lépések

- [Bevezetés a Azure Cosmos DBba](introduction.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Allekérdezéseket](sql-query-subquery.md)
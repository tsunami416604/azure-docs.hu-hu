---
title: Skaláris kifejezések az Azure Cosmos DB SQL-lekérdezésekben
description: Ismerje meg az Azure Cosmos DB SQL szintaxisának skaláris kifejezését. Ez a cikk azt is ismerteti, hogyan egyesítheti a skaláris kifejezéseket operátorok használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: f8c98915ad3b682af00492acc7bc51672ec874a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870734"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skaláris kifejezések az Azure Cosmos DB SQL-lekérdezésekben

A [SELECT záradék](sql-query-select.md) támogatja a skaláris kifejezéseket. A skaláris kifejezés olyan szimbólumok és operátorok kombinációja, amelyek egyetlen érték eléréséhez értékelhetők. A skaláris kifejezések közé tartoznak például: állandók, tulajdonsághivatkozások, tömbelem-hivatkozások, aliashivatkozások vagy függvényhívások. A skaláris kifejezések operátorok használatával összetett kifejezésekké kombinálhatók.

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
  
   Állandó értéket jelöl. A részleteket lásd [az Állandók](sql-query-constants.md) című részben.  
  
- `input_alias`  
  
   A `input_alias` `FROM` záradékban bevezetett érték által meghatározott értéket jelöli.  
  Ez az érték garantáltan nem **lesz meghatározatlan** – a bemeneti adatokban lévő**nem definiált** értékek kimaradnak.  
  
- `<scalar_expression>.property_name`  
  
   Egy objektum tulajdonságának értékét jelöli. Ha a tulajdonság nem létezik, vagy a tulajdonság egy értékre hivatkozik, amely nem objektum, akkor a kifejezés **értéke nem definiált** érték.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   A tulajdonság nevét `property_name` vagy tömbelemét egy `array_index` tömb indexével rendelkező tulajdonság értékét jelöli. Ha a tulajdonság/tömb index nem létezik, vagy a tulajdonság/tömb indexre olyan érték hivatkozik, amely nem objektum/tömb, akkor a kifejezés értéke nem definiált érték.  
  
- `unary_operator <scalar_expression>`  
  
   Egy egyetlen értékre alkalmazott operátort jelöl. A részleteket lásd [az Operátorok](sql-query-operators.md) részben.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Két értékre alkalmazott operátort jelöl. A részleteket lásd [az Operátorok](sql-query-operators.md) részben.  
  
- `<scalar_function_expression>`  
  
   Függvényhívás eredménye által meghatározott értéket jelöl.  
  
- `udf_scalar_function`  
  
   A felhasználó által definiált skalárfüggvény neve.  
  
- `builtin_scalar_function`  
  
   A beépített skalárfüggvény neve.  
  
- `<create_object_expression>`  
  
   A megadott tulajdonságokkal és értékükkel rendelkező új objektum létrehozásával kapott értéket jelöli.  
  
- `<create_array_expression>`  
  
   Egy új tömb létrehozásával kapott értéket jelöl, amelynek elemekben megadott értékei vannak.  
  
- `parameter_name`  
  
   A megadott paraméternév értékét jelöli. A paraméterneveknek \@ az első karakterként egyetlen karakterrel kell rendelkezniük.  
  
## <a name="remarks"></a>Megjegyzések
  
  Beépített vagy felhasználó által definiált skaláris függvény hívásakor minden argumentumot meg kell adni. Ha az argumentumok bármelyike nincs definiálva, a függvény nem lesz meghívva, és az eredmény nem lesz definiálva.  
  
  Objektum létrehozásakor a meg nem határozott értékhez rendelt tulajdonságokat a program kihagyja, és nem tartalmazza a létrehozott objektumban.  
  
  Tömb létrehozásakor a **meg nem határozott** értékhez rendelt elemértékek et a program kihagyja, és nem tartalmazza a létrehozott objektumban. Ez azt eredményezi, hogy a következő definiált elem úgy kerül a helyére, hogy a létrehozott tömb nem hagyja ki az indexeket.  

## <a name="examples"></a>Példák

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Az eredmény a következő:

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

Az eredmény a következő:

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

- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)
- [Az Azure Cosmos DB .NET-mintái](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Segédlekérdezések](sql-query-subquery.md)
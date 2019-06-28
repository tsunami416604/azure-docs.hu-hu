---
title: Az Azure Cosmos DB SQL-lekérdezések skaláris kifejezések
description: További információ az Azure Cosmos DB SQL-szintaxis a skaláris kifejezés.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 4464c39a45c47c680a13f3ebc34841b47ee0d7c6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342858"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Az Azure Cosmos DB SQL-lekérdezések skaláris kifejezések

A [SELECT záradékban](sql-query-select.md) skaláris kifejezések támogatja. Egy skaláris kifejezés szimbólumok és egyetlen értéket beszerzése kiértékelése operátorok kombinációja. Skaláris kifejezések például: állandók, tulajdonság hivatkozik, tömb elem hivatkozásokat, alias hivatkozásokat vagy függvényhívásokat. Skaláris kifejezéseinek operátorok használatával összetett kifejezések egyesíthetők.

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
  
   A tulajdonság az objektum olyan értékét jelöli. A tulajdonság nem létezik vagy tulajdonság értéket, amely nem objektum, a hivatkozott, majd a kifejezés eredménye **nem definiált** értéket.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Egy nevű tulajdonság értékét jelöli `property_name` vagy az index tömbelem `array_index` egy tömb. A tulajdonság/tömbindex nem létezik, vagy a tulajdonság/tömbindex hivatkozik egy érték alapján ez nem egy objektum vagy tömb, akkor a kifejezés eredménye nem meghatározott értéket eredményezne.  
  
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
  
  Beépített vagy felhasználó által definiált skaláris függvény hívásakor az összes argumentum definiálni kell. Ha bármelyik argumentum nem definiált, a függvény nem hívható, és az eredmény az lesz meghatározva.  
  
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

A következő lekérdezést a skaláris kifejezés eredménye egy logikai érték:

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

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Segédlekérdezések](sql-query-subquery.md)
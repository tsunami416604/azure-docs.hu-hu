---
title: FELSŐ Azure Cosmos DB lekérdezési nyelv
description: Ismerkedjen meg az SQL System Function felső Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303970"
---
# <a name="upper-azure-cosmos-db"></a>FELSŐ (Azure Cosmos DB)
 Egy karakterlánc-kifejezést ad vissza a kisbetűs karakterek nagybetűssé alakítását követően.  

A felső rendszerfüggvény nem használja az indexet. Ha gyakori kis-és nagybetűket nem megkülönböztető összehasonlításokat tervez, a felső rendszerfunkció nagy mennyiségű RU-t használhat fel. Ha ez az eset áll fenn, ahelyett, hogy a felső rendszerfüggvényt használja az összehasonlítások minden egyes időpontjában az adatnormalizálás során, a borítást a Beszúrás után normalizálhatja. Ezután egy olyan lekérdezés, mint például a SELECT * FROM c, ahol az UPPER (c. Name) = ' BOB ' egyszerűen KIVÁLASZTJA * a c-ből, ahol a c.name = ' BOB ' lesz.

## <a name="syntax"></a>Szintaxis
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható a `UPPER` lekérdezésekben  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

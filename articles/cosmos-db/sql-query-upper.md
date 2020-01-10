---
title: FELSŐ Azure Cosmos DB lekérdezési nyelv
description: Ismerkedjen meg az SQL System Function felső Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9809a138a5e28eb069d545d39cfda815c915bd78
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728888"
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
  
  Az alábbi példa bemutatja, hogyan használható a `UPPER` a lekérdezésekben  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

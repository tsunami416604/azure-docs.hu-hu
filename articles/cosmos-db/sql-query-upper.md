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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303970"
---
# <a name="upper-azure-cosmos-db"></a>FELSŐ (Azure Cosmos DB)
 Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza.  

A felső rendszerfüggvény nem használja az indexet. Ha gyakori kis-és nagybetűket nem megkülönböztető összehasonlításokat tervez, a felső rendszerfunkció nagy mennyiségű RU-t használhat fel. Ha ez az eset áll fenn, ahelyett, hogy a felső rendszerfüggvényt használja az összehasonlítások minden egyes időpontjában az adatnormalizálás során, a borítást a Beszúrás után normalizálhatja. Ezután egy olyan lekérdezés, mint például a SELECT * FROM c, ahol az UPPER (c. Name) = ' BOB ' egyszerűen KIVÁLASZTJA * a c-ből, ahol a c.name = ' BOB ' lesz.

## <a name="syntax"></a>Szintaxis
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa bemutatja, hogyan használható a `UPPER` a lekérdezésekben  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

---
title: UPPER az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az AZURE Cosmos DB ÁLTALÁNOS SQL-rendszerfunkcióját.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303970"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 A kisbetűs karakteradatok nagybetűssé alakítása után karakterlánc-kifejezést ad eredményül.  

Az UPPER rendszer funkció nem használja az indexet. Ha azt tervezi, hogy nem tesz gyakori kis- és nagybetűket érzéketlen összehasonlításokat, az UPPER rendszerfunkció jelentős mennyiségű VT-t fogyaszt. Ha ez a helyzet, ahelyett, hogy az ÁLTALÁNOS rendszer funkciót használná az adatok normalizálására minden alkalommal az összehasonlításhoz, normalizálhatja a burkolatot a beillesztéskor. Ezután egy lekérdezés, mint a SELECT * FROM c WHERE UPPER(c.name) = 'BOB' egyszerűen kiválasztja * a c-ből, ahol c.name = 'BOB'.

## <a name="syntax"></a>Szintaxis
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan használható `UPPER` egy lekérdezésben  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)

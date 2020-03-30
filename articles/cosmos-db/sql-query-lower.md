---
title: ALSÓSZINT az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az Azure Cosmos DB KIS SQL rendszerfunkcióját, amely a nagybetűs karakteradatok kisbetűssé konvertálása után karakterlánc-kifejezést ad vissza
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302270"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 Karakterlánc-kifejezést ad vissza, miután a nagybetűs karakteradatokat kisbetűssé alakította.  

A LOWER rendszer funkció nem használja az indexet. Ha azt tervezi, hogy nem ér el gyakori kis- és nagybetűket, akkor az ALSÓSZINTŰ rendszerfunkció jelentős mennyiségű VT-t vehet igénybe. Ebben az esetben ahelyett, hogy az ALSÓ rendszer funkciót használná az adatok minden alkalommal történő normalizálására az összehasonlításhoz, a behelyezéskor normalizálhatja a burkolatot. Ezután egy lekérdezés, mint a SELECT * FROM c WHERE LOWER(c.name) = 'bob' egyszerűen SELECT * FROM c WHERE c.name = 'bob'.

## <a name="syntax"></a>Szintaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa bemutatja, hogyan használható `LOWER` egy lekérdezésben.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfunkció nem használja az indexet.

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB karakterlánc-függvényei](sql-query-string-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)

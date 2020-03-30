---
title: PI az Azure Cosmos DB lekérdezési nyelvében
description: Ismerje meg az SQL rendszer funkció PI az Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349648"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 A PI állandó értékét adja eredményül.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Visszatérési típusok
  
  Numerikus kifejezést ad eredményül.  
  
## <a name="examples"></a>Példák
  
  A következő példa a `PI`értékét adja eredményül.  
  
```sql
SELECT PI() AS pi 
```  
  
 Itt van az eredményhalmaz.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Az Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfüggvények Az Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés az Azure Cosmos DB bemutatása](introduction.md)

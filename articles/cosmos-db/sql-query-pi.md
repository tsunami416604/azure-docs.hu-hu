---
title: PI Azure Cosmos DB lekérdezési nyelv
description: Ismerkedjen meg az SQL System Function PI Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349648"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Az állandó a PI értékét adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a `PI` értékét adja vissza.  
  
```sql
SELECT PI() AS pi 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

---
title: ATAN Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function ATAN Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4ee3867f3a4938358e8d61aa8a98f747756ee3e8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348560"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Adja vissza a szög radiánban, amelynek tangense a megadott numerikus kifejezés. Arkusztangens ezt is nevezik.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a megadott érték `ATAN` értékét adja vissza.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

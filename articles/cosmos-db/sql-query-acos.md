---
title: ACO-Azure Cosmos DB lekérdezési nyelv
description: Ismerje meg az SQL System Function ACO-ket Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c9d29fba6b5dc55a98bf90cfafe0940d7bf9674
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348740"
---
# <a name="acos-azure-cosmos-db"></a>ACO-i (Azure Cosmos DB)
 Adja vissza a szög radiánban, amelynek a koszinusza a megadott numerikus kifejezés; egy szám arkusz koszinusza néven is ismert.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a `ACOS` értéket adja vissza.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

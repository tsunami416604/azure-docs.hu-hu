---
title: COS Azure Cosmos DB lekérdezési nyelv
description: További információ a Azure Cosmos DBban található SQL System függvényről.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e64127b7af749af87b90331c3f175957cfa7f09
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351068"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 A megadott kifejezést az radiánban megadott szög, trigonometriai koszinuszát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa kiszámítja a megadott szög `COS` értékét.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

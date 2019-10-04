---
title: GYERMEKÁGY Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function GYERMEKÁGY Azure Cosmos DBával.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d980319730e69fdcb529272ba1b8fb48d2b5b230
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351248"
---
# <a name="cot-azure-cosmos-db"></a>GYERMEKÁGY (Azure Cosmos DB)
 A megadott szög trigonometriai kotangensét adja vissza radiánban, a megadott numerikus kifejezés.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa kiszámítja a megadott szög `COT` értékét.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

---
title: BŰN Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function SIN szolgáltatással Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b4450ea9dd50f8093fa9569d7f82fe124022c3a1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349460"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 A megadott kifejezést az radiánban megadott szög, trigonometriai szinuszát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   A numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  A numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa kiszámítja a megadott szög `SIN` értékét.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

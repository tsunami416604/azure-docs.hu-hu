---
title: BŰN Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function SIN szolgáltatással Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303103"
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
  
  A következő példa a megadott szög `SIN`ét számítja ki.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

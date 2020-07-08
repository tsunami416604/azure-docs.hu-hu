---
title: COS Azure Cosmos DB lekérdezési nyelv
description: Ismerje meg, hogy a Azure Cosmos DB koszinusz (COS) SQL System függvénye hogyan adja vissza a megadott szög (radián) trigonometriai koszinuszát a megadott kifejezésben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304021"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Az adott kifejezésben megadott szög (radián) trigonometriai koszinuszát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a `COS` megadott szöget számítja ki.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

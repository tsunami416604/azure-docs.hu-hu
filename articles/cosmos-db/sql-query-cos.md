---
title: COS Azure Cosmos DB lekérdezési nyelv
description: Ismerje meg, hogy a Azure Cosmos DB koszinusz (COS) SQL System függvénye hogyan adja vissza a megadott szög (radián) trigonometriai koszinuszát a megadott kifejezésben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873403"
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
  
  A következő példa a megadott szög `COS`ét számítja ki.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

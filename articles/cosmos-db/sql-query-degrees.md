---
title: FOK Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg az Azure Cosmos DB fok SQL System függvényét, hogy a megfelelő szöget adja vissza fokban a radiánban megadott szögben.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f432f5ef57c8eccc0cf629f00d2231364785b9a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871329"
---
# <a name="degrees-azure-cosmos-db"></a>FOK (Azure Cosmos DB)
 A megfelelő szöget adja vissza fokban egy radiánban megadott szögben.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa egy PI/2 radiánban megadott szögben lévő fok értéket adja vissza.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

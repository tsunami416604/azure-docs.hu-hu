---
title: ACO-Azure Cosmos DB lekérdezési nyelv
description: Ismerje meg, hogy az ACO-arccosice (Azure Cosmos DB) SQL System függvénye hogyan adja vissza azt a szöget (radiánban), amelynek koszinusza a megadott numerikus kifejezés
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fd5baa3ffefb7039c40706253e633661a51150f2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873522"
---
# <a name="acos-azure-cosmos-db"></a>ACO-i (Azure Cosmos DB)
 Azt a szöget adja vissza radiánban, amelynek koszinusza a megadott numerikus kifejezés; más néven szám arkusz koszinuszát.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a-1 `ACOS` adja vissza.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

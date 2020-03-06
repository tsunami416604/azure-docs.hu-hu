---
title: ATAN Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg, hogy a Azure Cosmos DB arkusz (ATAN) SQL System függvénye hogyan adja vissza azt a szöget (radiánban), amelynek tangense a megadott numerikus kifejezés
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302678"
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
  
  A következő példa a megadott érték `ATAN`ét adja vissza.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

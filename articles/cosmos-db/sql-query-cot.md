---
title: GYERMEKÁGY Azure Cosmos DB lekérdezési nyelven
description: Ismerje meg, hogy a Azure Cosmos DB a (GYERMEKÁGY) SQL System függvénye hogyan adja vissza a megadott szögben (radiánban) lévő, a megadott numerikus kifejezésben szereplő, a megadott szöghez tartozó trigonometriai tangenset.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299487"
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
  
  A következő példa a megadott szög `COT`ét számítja ki.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

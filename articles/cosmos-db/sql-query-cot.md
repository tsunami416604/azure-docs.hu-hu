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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78299487"
---
# <a name="cot-azure-cosmos-db"></a>GYERMEKÁGY (Azure Cosmos DB)
 Visszaadja a megadott szög (radiánban), a megadott numerikus kifejezésben lévő, meghatározott szögből származó trigonometriai tangensét.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a `COT` megadott szöget számítja ki.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>További lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)

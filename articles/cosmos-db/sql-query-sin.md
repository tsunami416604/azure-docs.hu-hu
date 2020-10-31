---
title: BŰN Azure Cosmos DB lekérdezési nyelven
description: Ismerkedjen meg az SQL System Function SIN szolgáltatással Azure Cosmos DBban.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9f4c7933bed9d51e33ada21d03b9ab8447110e32
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082470"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Az adott kifejezésben a megadott szög (radiánban) a megadott szögben lévő trigonometriai szinuszát adja vissza.  
  
## <a name="syntax"></a>Szintaxis
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*numeric_expr*  
   Egy numerikus kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy numerikus kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  A következő példa a `SIN` megadott szöget számítja ki.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Matematikai függvények Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)

---
title: Azure Cosmos DB lekérdezési nyelv alacsonyabb
description: Ismerkedjen meg az Azure Cosmos DB alsó SQL System függvényével, amely egy karakterlánc-kifejezést ad vissza a nagybetűs karakterek kisbetűsre konvertálása után.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302270"
---
# <a name="lower-azure-cosmos-db"></a>ALSÓ (Azure Cosmos DB)
 Egy karakterlánc-kifejezés nagybetűt adatok átalakítása kisbetűvé után adja vissza.  

Az alsó rendszerfüggvény nem használja az indexet. Ha gyakori kis-és nagybetűket nem megkülönböztető összehasonlításokat tervez, az alacsonyabb rendszerfunkció nagy mennyiségű RU-t is felhasználhat. Ha ez az eset áll fenn, ahelyett, hogy az alacsonyabb rendszerfüggvényt használja az összehasonlítások minden egyes időpontjában, a borítást a beszúráskor normalizálhatja. Ezután egy olyan lekérdezés, mint például a SELECT * FROM c, ahol az alacsonyabb (c. Name) = ' Bob ' egyszerűen kiválaszthatja a * c betűt, ahol a c.name = ' Bob '.

## <a name="syntax"></a>Szintaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezés adja vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan használható a `LOWER` egy lekérdezésben.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Íme az eredményhalmaz.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Megjegyzések

Ez a rendszerfüggvény nem fogja használni az indexet.

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

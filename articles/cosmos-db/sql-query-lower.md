---
title: Azure Cosmos DB lekérdezési nyelv alacsonyabb
description: Ismerkedjen meg az Azure Cosmos DB alsó SQL System függvényével, amely egy karakterlánc-kifejezést ad vissza a nagybetűs karakterek kisbetűsre konvertálása után.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 35efbb8d4d97ab52abb20487d15a80985946c499
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732603"
---
# <a name="lower-azure-cosmos-db"></a>ALSÓ (Azure Cosmos DB)
 Egy karakterlánc-kifejezést ad vissza, miután a nagybetűs karaktereket a kisbetűs értékre konvertálta.  

Az alsó rendszerfüggvény nem használja az indexet. Ha gyakori kis-és nagybetűket nem megkülönböztető összehasonlításokat tervez, az alacsonyabb rendszerfunkció nagy mennyiségű RU-t is felhasználhat. Ha ez az eset áll fenn, ahelyett, hogy az alacsonyabb rendszerfüggvényt használja az összehasonlítások minden egyes időpontjában, a borítást a beszúráskor normalizálhatja. Ezután egy olyan lekérdezés, mint például a SELECT * FROM c, ahol az alacsonyabb (c. Name) = ' Bob ' egyszerűen kiválaszthatja a * c betűt, ahol a c.name = ' Bob '.

## <a name="syntax"></a>Szintaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentumok
  
*str_expr*  
   Egy karakterlánc-kifejezés.  
  
## <a name="return-types"></a>Visszatérési típusok
  
  Egy karakterlánc-kifejezést ad vissza.  
  
## <a name="examples"></a>Példák
  
  Az alábbi példa azt szemlélteti, hogyan használható a `LOWER` egy lekérdezésben.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Itt látható az eredményhalmaz.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Következő lépések

- [Karakterlánc-függvények Azure Cosmos DB](sql-query-string-functions.md)
- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Bevezetés a Azure Cosmos DBba](introduction.md)

---
title: Az Azure Cosmos-tárolók létrehozása az Azure portal és a különböző SDK-k használatával nagy partíciós kulccsal.
description: Ismerje meg, hogyan hozzon létre egy tárolót az Azure portal és a különböző SDK-k használatával nagy partíciókulccsal rendelkező Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: ee1568274792621b8ed9f25d211c40440a82208c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160035"
---
# <a name="create-containers-with-large-partition-key"></a>Nagy partíciókulccsal rendelkező tárolók létrehozása

Az Azure Cosmos DB kivonat-alapú particionálási sémát használ a horizontális skálázást az adatok eléréséhez. Május 3 2019 előtt létrehozott összes Azure Cosmos-tárolót használja a kivonatoló függvényt, amely kiszámítja a kivonatot az első 100 bájtra a partíciókulcs alapján. Ha több partíciókulcsok, amelyek az ugyanazon első 100 bájtra, majd ezeket a logikai partíciók minősül az azonos logikai partíciót a szolgáltatás által. Ez a vezethet problémák, például partíció méretkvóta folyamatban az helytelen, és egyedi indexek alkalmazva a partíciós kulcsok között. Nagyméretű partíciókulcsok jelennek meg a probléma megoldásához. Az Azure Cosmos DB mostantól támogatja nagyméretű partíciókulcsok az érték legfeljebb 2 KB. 

A kulcsok legfeljebb 2 KB nagy partíciós kulcsok továbbfejlesztett verziója, a Jelszókivonat-függvény, amely egyedi kivonattal hozhat létre nagy partícióról funkciójának használatával támogatottak. Ez a kivonat-verzió esetében is ajánlott forgatókönyvek magas partíciós kulcs számossága a partíciókulcs méretétől függetlenül. A partíciós kulcs számossága számít, ha például a ~ 30000 logikai partíciót egy tárolóban sorrendjében egyedi logikai partíciók száma. Ez a cikk ismerteti, hogyan hozhat létre egy tárolót az Azure portal és a különböző SDK-k használatával nagy partíciókulcsot. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Hozzon létre egy nagy partíció kulcsot (.Net SDK V2)

Ha a .net SDK használatával hozzon létre egy tárolót nagy partíciókulccsal rendelkező, kell megadnia a `PartitionKeyDefinitionVersion.V2` tulajdonság. Az alábbi példa bemutatja, hogyan adja meg a verziót a PartitionKeyDefinition objektumon belül, és állítsa be PartitionKeyDefinitionVersion.V2:

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>Hozzon létre egy nagy partíció kulcsot (az Azure portal) 

Egy nagy partíció kulcs létrehozásához létrehozásakor egy új tárolót az Azure Portalon ellenőrizze a **a partíciós kulcs mérete nagyobb, mint 100-bájt** lehetőséget. Alapértelmezés szerint az új tárolók példánycsoportot sem léptetett be, a nagyméretű partíciókulcsok használatával. Törölje a jelölőnégyzet jelölését, ha már nincs szüksége nagy méretű partíciókulcsok, illetve ha az SDK-k verziója régebbi, mint 1.18 futó alkalmazások.

![Hozzon létre az Azure Portalon nagyméretű partíciókulcsok](./media/large-partition-keys/large-partition-key-with-portal.png)
 
## <a name="next-steps"></a>További lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB-kérésegységeiről](request-units.md)
* [A tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [Az Azure Cosmos-fiók használata](account-overview.md)



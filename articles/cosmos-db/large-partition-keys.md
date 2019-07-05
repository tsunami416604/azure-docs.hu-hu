---
title: Az Azure Cosmos-tárolók létrehozása az Azure portal és a különböző SDK-k használatával nagy partíciós kulccsal.
description: Ismerje meg, hogyan hozzon létre egy tárolót az Azure portal és a különböző SDK-k használatával nagy partíciókulccsal rendelkező Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: bd1697378e5db0432d181f9f688ccc2468b306e7
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566023"
---
# <a name="create-containers-with-large-partition-key"></a>Nagy partíciókulccsal rendelkező tárolók létrehozása

Az Azure Cosmos DB kivonat-alapú particionálási sémát használ a horizontális skálázást az adatok eléréséhez. Május 3 2019 előtt létrehozott összes Azure Cosmos-tárolót használja a kivonatoló függvényt, amely kiszámítja a kivonatot az első 100 bájtra a partíciókulcs alapján. Ha több partíciókulcsok, amelyek az ugyanazon első 100 bájtra, majd ezeket a logikai partíciók minősül az azonos logikai partíciót a szolgáltatás által. Ez a vezethet problémák, például partíció méretkvóta folyamatban az helytelen, és egyedi indexek alkalmazva a partíciós kulcsok között. Nagyméretű partíciókulcsok jelennek meg a probléma megoldásához. Az Azure Cosmos DB mostantól támogatja nagyméretű partíciókulcsok az érték legfeljebb 2 KB.

A kulcsok legfeljebb 2 KB nagy partíciós kulcsok továbbfejlesztett verziója, a Jelszókivonat-függvény, amely egyedi kivonattal hozhat létre nagy partícióról funkciójának használatával támogatottak. Ez a kivonat-verzió esetében is ajánlott forgatókönyvek magas partíciós kulcs számossága a partíciókulcs méretétől függetlenül. A partíciós kulcs számossága számít, ha például a ~ 30000 logikai partíciót egy tárolóban sorrendjében egyedi logikai partíciók száma. Ez a cikk ismerteti, hogyan hozhat létre egy tárolót az Azure portal és a különböző SDK-k használatával nagy partíciókulcsot. 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>Hozzon létre egy nagy partíció kulcsot (.Net SDK V2)

Hozzon létre egy tárolót a .NET SDK használatával egy nagy partíciókulccsal rendelkező, adja meg a `PartitionKeyDefinitionVersion.V2` tulajdonság. Az alábbi példa bemutatja, hogyan adja meg az PartitionKeyDefinition objektumban a verzió tulajdonság és PartitionKeyDefinitionVersion.V2 azt.

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

## <a name="create-a-large-partition-key-powershell"></a>Hozzon létre egy nagy partíció kulcsot (PowerShell)

Hozzon létre egy tárolót, egy PowerShell-lel nagy partíciókulccsal rendelkező, adjon meg `"version" = 2` számára a `partitionKey` objektum.

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>Támogatott SDK-verziókra

A nagyméretű partíciókulcsok SDK-k az alábbi minimális verziók támogatottak:

|SDK-típus  | Minimális verziója   |
|---------|---------|
|.Net     |    1.18     |
|Java-szinkronizálás     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | verziója magasabb, mint `2017-05-03` használatával a `x-ms-version` kérés fejlécéhez.|

Jelenleg a Power BI és az Azure Logic Apps belül nagy partíciókulccsal rendelkező tárolók nem használhat. Tárolók ezekből az alkalmazásokból egy nagy partíció kulcs nélkül is használhatja.

## <a name="next-steps"></a>További lépések

* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Az Azure Cosmos DB-kérésegységeiről](request-units.md)
* [A tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [Az Azure Cosmos-fiók használata](account-overview.md)



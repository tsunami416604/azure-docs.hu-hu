---
title: Adatfolyam-végpontok kezelése Azure Media Services v3-val
description: Ez a cikk bemutatja, hogyan kezelheti a streaming-végpontokat Azure Media Services v3-val.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 5dd3cc1efd25f7ec09f897c67bebebedb84d9570
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370545"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Adatfolyam-végpontok kezelése Media Services v3-val

A Media Services-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom folyamatos átvitelének megkezdéséhez, valamint a [dinamikus csomagolás](dynamic-packaging-overview.md) és a [dinamikus titkosítás](content-protection-overview.md)kihasználásához a adatfolyam-továbbítási végpontnak **futó** állapotban kell lennie.

Ez a cikk bemutatja, hogyan indíthatja el a folyamatos átviteli végpontot.
 
> [!NOTE]
> Csak akkor számítunk fel díjat, ha a folyamatos átviteli végpont fut állapotban van.
    
## <a name="prerequisites"></a>Előfeltételek

Tekintse át 

* [Media Services fogalmak](concepts-overview.md)
* [Streaming Endpoint koncepciója](streaming-endpoint-concept.md)
* [Dinamikus csomagolás](dynamic-packaging-overview.md)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Navigáljon a Azure Media Services-fiókjához.
1. A bal oldalon válassza a **folyamatos átviteli végpontok**lehetőséget.
1. Válassza ki az elindítani kívánt folyamatos átviteli végpontot, és kattintson az **Indítás**gombra.

## <a name="use-the-java-sdk"></a>A Java SDK használata

```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Tekintse meg a teljes [Java-kód mintát](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

## <a name="use-the-net-sdk"></a>A .NET SDK használata

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Tekintse meg a [.net-kód teljes mintáját](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

## <a name="use-cli"></a>A CLI használata

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

További információ: [az AMS streaming-Endpoint Start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-rest"></a>A REST használata

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

További információkért lásd: 

* A [Start a streamvégpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) Reference dokumentációja.
* Az adatfolyam-végpont indítása aszinkron művelet. 

    A hosszan futó műveletek figyelésével kapcsolatos részletekért lásd: [hosszan futó műveletek](media-services-apis-overview.md)
* Ez a [Poster-gyűjtemény](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) több Rest-műveletre mutató példákat tartalmaz, például az adatfolyam-végpont indításának módját.

## <a name="next-steps"></a>Következő lépések

* [Media Services v3 OpenAPI-specifikáció (hencegés)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Folyamatos átviteli végpont műveletei](https://docs.microsoft.com/rest/api/media/streamingendpoints)

---
title: Streamelési végpontok kezelése az Azure Media Services v3-as oldalával
description: Ez a cikk bemutatja, hogyan kezelheti a streamelési végpontokat az Azure Media Services v3 használatával.
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
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461044"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Streamelési végpontok kezelése a Media Services 3-as oldalával

Amikor a Media Services-fiók jön létre egy **alapértelmezett** [streamelési végpont](streaming-endpoint-concept.md) hozzáadódik a fiók **leállított** állapotban. A tartalom streamelésének megkezdéséhez és a [dinamikus csomagolás](dynamic-packaging-overview.md) és a [dinamikus titkosítás](content-protection-overview.md)előnyeinek kihasználásához a streamelési végpontnak, amelyből tartalmat szeretne streamelni, **futó** állapotban kell lennie.

Ez a cikk bemutatja, hogyan hajthatja végre a [start](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) parancsot a streamelési végponton különböző technológiák használatával. 
 
> [!NOTE]
> A számlázás csak akkor történik meg, ha a streamelési végpont futó állapotban van.
    
## <a name="prerequisites"></a>Előfeltételek

Felülvizsgálat: 

* [A Media Services fogalmai](concepts-overview.md)
* [Végpont streamelési koncepciója](streaming-endpoint-concept.md)
* [Dinamikus csomagolás](dynamic-packaging-overview.md)

## <a name="use-rest"></a>A REST használata

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

További információkért lásd: 

* A [kezdés egy StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) referencia dokumentáció.
* A streamelési végpont indítása aszinkron művelet. 

    A hosszú ideig futó műveletek figyeléséről a Hosszú ideig futó műveletek című [témakörben talál](media-services-apis-overview.md)további információt.
* Ez [a Postman-gyűjtemény](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) több REST-műveletpéldáit is tartalmazza, többek között a streamelési végpont indítását.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata 
 
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Nyissa meg az Azure Media Services-fiókját.
1. A bal oldali ablaktáblában válassza a **Végpontok streamelése**lehetőséget.
1. Jelölje ki az elindítani kívánt streamelési végpontot, majd válassza a **Start**gombot.

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

További információ: [az ams streaming-endpoint start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>SDK-k használata

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Tekintse meg a teljes [Java-kódmintát](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Tekintse meg a teljes [.NET kódmintát](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>További lépések

* [Media Services v3 OpenAPI specifikáció (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Végpont-műveletek streamelése](https://docs.microsoft.com/rest/api/media/streamingendpoints)

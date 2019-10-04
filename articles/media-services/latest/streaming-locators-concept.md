---
title: A streamelési Lokátorok az Azure Media Services |} A Microsoft Docs
description: Ez a cikk lehetővé teszi a Streamelési Lokátorok vannak, és hogyan használják az Azure Media Services ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299168"
---
# <a name="streaming-locators"></a>Streamelési lokátor

Ahhoz, hogy a kimeneti adategységben található videók elérhetők legyenek az ügyfelek számára lejátszásra, létre kell hozni egy [streamelési lokátort](https://docs.microsoft.com/rest/api/media/streaminglocators), majd streamelési URL-címeket. Egy URL-cím összeállítását, kell összefűzni, a folyamatos átviteli végponton gazdagép nevét és a Streamelési lokátor elérési útja. Egy .NET-példáért tekintse meg a [streamelési lokátor beszerzését](stream-files-tutorial-with-api.md#get-a-streaming-locator) bemutató cikket.

A folyamat létrehozásának egy **Streamelési lokátor** közzététel nevezzük. Alapértelmezés szerint a **Streamelési lokátor** érvényes az API-hívások végrehajtása után azonnal, és tart, amíg nem törli, ha nem konfigurál a választható kezdő és befejező időpontok. 

Létrehozásakor egy **Streamelési lokátor**, meg kell adnia egy **eszköz** nevét és a egy **Streamelési házirend** nevét. További információkért tekintse át a következők témaköröket:

* [Eszközök](assets-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)

A Streamelési lokátor, így csak a felhasználó a kezdő és záró idő között (például között 5/1/2019, 5/5/2019) kívül a tartalom lejátszása is megadhat.  

## <a name="considerations"></a>Megfontolandó szempontok

* **A streamelési Lokátorok** amelyek nem frissíthető. 
* Tulajdonságainak **Streamelési Lokátorok** a DateTime típusú állandóan UTC formátumban vannak.
* Korlátozott számú házirendeket tervezzen a Media Services-fiók és újból felhasználja őket a Streamelési Lokátorok, amikor szükség van a beállítások. További információkért lásd: [kvóták és korlátozások](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>A Streamelési Lokátorok létrehozásához  

### <a name="not-encrypted"></a>Nincs titkosítva

Adatfolyam a fájlt az-a-törlése (nem titkosított) szeretné, ha az előre meghatározott egyértelmű streamelési szabályzat beállítása: a "Predefined_ClearStreamingOnly" (a .NET-ben, használhatja a PredefinedStreamingPolicy.ClearStreamingOnly enumerálás).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Titkosított 

A tartalmak a CENC titkosítás van szüksége, ha a "Predefined_MultiDrmCencStreaming" házirendjének beállítása. A Widevine titkosítása Smooth, DASH adatfolyamok és PlayReady lépnek. A kulcs a lejátszás ügyfél a beállított DRM-licencek alapján lesz elküldve.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Ha is szeretné titkosítani a HLS-folyamot CBCS (FairPlay), használja a "Predefined_MultiDrmStreaming".

## <a name="associate-filters-with-streaming-locators"></a>Szűrők társítani a Streamelési Lokátorok

Lásd: [szűrők: társíthat a Streamelési Lokátorok](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Szűrő, a sorrendben, a Streamelési lokátor entitások lap

Lásd: [szűrése, rendezése, a Media Services entitások lapozás](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lista Streamelési Lokátorok adategység neve szerint

A társított Eszközintelligencia neve alapján a Streamelési Lokátorok lekéréséhez használja a következő műveleteket:

|Nyelv|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|parancssori felület|[az ams eszköz list-streaming-keresők](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>Lásd még:

* [Eszközök](assets-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Videók feltöltése, kódolása és streamelése a .NET használatával](stream-files-tutorial-with-api.md)

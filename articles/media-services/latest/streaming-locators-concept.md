---
title: Streamelési lokátorok az Azure Media Services szolgáltatásban | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogy mik a streamelési lokátorok, és hogyan használják az Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 3a9568e1a0307cd1713c511ef42c065424306548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302882"
---
# <a name="streaming-locators"></a>Streamelési lokátor

Ahhoz, hogy a kimeneti adategységben található videók elérhetők legyenek az ügyfelek számára lejátszásra, létre kell hozni egy [streamelési lokátort](https://docs.microsoft.com/rest/api/media/streaminglocators), majd streamelési URL-címeket. URL-cím létrehozásához össze kell fűznie a streamelési végpont gazdagép nevét és a streamelési lokátor elérési útját. Egy .NET-példáért tekintse meg a [streamelési lokátor beszerzését](stream-files-tutorial-with-api.md#get-a-streaming-locator) bemutató cikket.

A **streamelési lokátor** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint a **streamelési lokátor** az API-hívások kezdeményezése után azonnal érvényes, és a törlésig tart, kivéve, ha konfigurálja a választható kezdési és befejezési időpontokat. 

**A streamelési lokátor**létrehozásakor meg kell adnia egy **eszköznevet** és egy **streamelési házirend** nevét. További információkért tekintse át a következők témaköröket:

* [Objektumok](assets-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)

Megadhatja a streamelési lokátor kezdési és befejezési idejét is, amely csak a tartalom lejátszását teszi lehetővé ezen időpontok között (például 2019/05/5/2019 között).  

## <a name="considerations"></a>Megfontolandó szempontok

* **A streamelési lokátorok** nem updatable. 
* A Datetime típusú **streamelési lokátorok** tulajdonságai mindig UTC formátumban vannak.
* A Media Service-fiókhoz korlátozott házirendeket kell terveznie, és újra fel kell használnia őket a streamelési lokátorokhoz, amikor ugyanazokra a beállításokra van szükség. További információt a [Kvóták és korlátozások](limits-quotas-constraints.md)című témakörben talál.

## <a name="create-streaming-locators"></a>Streamelési lokátorok létrehozása  

### <a name="not-encrypted"></a>Nincs titkosítva

Ha a fájlt a tiszta (nem titkosított) adatfolyamként szeretné streamelni, állítsa be az előre definiált törlési streamelési házirendet: "Predefined_ClearStreamingOnly" (a .NET-ben használhatja a PredefinedStreamingPolicy.ClearStreamingOnly enum-ot).

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

Ha a tartalmat CENC titkosítással kell titkosítania, állítsa a házirendet "Predefined_MultiDrmCencStreaming".If you need to crypt your content with the CENC encryption, set your policy to "Predefined_MultiDrmCencStreaming". A Widevine titkosítás dash-adatfolyamra és PlayReady to Smooth-ra lesz alkalmazva. A kulcs a konfigurált DRM-licencek alapján kerül a lejátszási ügyfélhez.

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

Ha a HLS-adatfolyamot CBCS (FairPlay) segítségével is szeretné titkosítani, használja a "Predefined_MultiDrmStreaming".

> [!NOTE]
> A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="associate-filters-with-streaming-locators"></a>Szűrők társítása a streamelési lokátorokhoz

Lásd: [Szűrők: társítás a streamelési lokátorokkal.](filters-concept.md#associating-filters-with-streaming-locator)

## <a name="filter-order-page-streaming-locator-entities"></a>Szűrés, rendelés, lapstreamelési lokátor entitások

Lásd: [Media Services-entitások szűrése, rendelése, lapozása.](entities-overview.md)

## <a name="list-streaming-locators-by-asset-name"></a>Streamelési lokátorok listázása eszköznév szerint

A streamelési lokátorok lefoglalásához a társított eszköznév alapján használja a következő műveleteket:

|Nyelv|API|
|---|---|
|REST|[liststreaminglocatorok](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|parancssori felület|[az ams eszközlista-streaming-lokátorok](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator (EszközstreamingLocator)](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators (StreamingLocators)](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Lásd még

* [Objektumok](assets-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)
* [Oktatóanyag: Videók feltöltése, kódolása és streamelése a .NET használatával](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>További lépések

[Hogyan hozzunk létre egy streamelési lokátort és hozzunk létre URL-eket](create-streaming-locator-build-url.md)

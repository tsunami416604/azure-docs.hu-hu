---
title: Házirendek az Azure Media Services Streaming |} A Microsoft Docs
description: Ez a cikk lehetővé teszi a Streamelési szabályzatok vannak, és hogyan használják az Azure Media Services ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746744"
---
# <a name="streaming-policies"></a>Streamelési szabályok

Az Azure Media Services v3 [szabályzatokat. adatfolyam](https://docs.microsoft.com/rest/api/media/streamingpolicies) lehetővé teszi az adatfolyam-továbbítási protokollok és a titkosítási beállítások megadása a [Streamelési Lokátorok](streaming-locators-concept.md). Használja az előre definiált adatfolyam-szabályzatokra, vagy egy egyéni szabályzatot hozott létre. Jelenleg elérhető előre definiált adatfolyam-szabályzatok a következők: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'.

> [!IMPORTANT]
> * Tulajdonságainak **szabályzatokat. adatfolyam** a DateTime típusú állandóan UTC formátumban vannak.
> * Korlátozott számú házirendeket tervezzen a Media Services-fiók és újból felhasználja őket a Streamelési Lokátorok, amikor szükség van a beállítások. 

## <a name="examples"></a>Példák

### <a name="not-encrypted"></a>Nincs titkosítva

Adatfolyam a fájlt az-a-törlése (nem titkosított) szeretné, ha az előre meghatározott egyértelmű streamelési szabályzat beállítása: a "Predefined_ClearStreamingOnly" (a .NET, a használható PredefinedStreamingPolicy.ClearStreamingOnly).

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

A tartalmak borítékot és cenc titkosítási van szüksége, ha a "Predefined_MultiDrmCencStreaming" házirendjének beállítása. Ez a szabályzat jelzi, hogy két tartalomkulcsot (envelope és CENC) szeretne létrehozni és beállítani a lokátoron. Így az envelope, a PlayReady és a Widevine titkosítások lesznek alkalmazva (a kulcsot a konfigurált DRM-licencek alapján továbbítja a rendszer a lejátszást végző ügyfelének).

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

Ha is szeretné titkosítani a stream-CBCS (FairPlay), használja a "Predefined_MultiDrmStreaming".

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

Lásd: [szűrése, rendezése, a Media Services entitások lapozás](entities-overview.md).

## <a name="next-steps"></a>További lépések

* [Fájl streamelése](stream-files-dotnet-quickstart.md)
* [AES-128, a dinamikus titkosítás és a kulcstovábbítást használata](protect-with-aes128.md)
* [DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata](protect-with-drm.md)

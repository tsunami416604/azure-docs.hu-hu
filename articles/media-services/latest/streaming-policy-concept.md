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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120188"
---
# <a name="streaming-policies"></a>Streamelési szabályok

Az Azure Media Services v3 [szabályzatokat. adatfolyam](https://docs.microsoft.com/rest/api/media/streamingpolicies) lehetővé teszi az adatfolyam-továbbítási protokollok és a titkosítási beállítások megadása a [Streamelési Lokátorok](streaming-locators-concept.md). A Media Services v3 tartalmaz néhány előre definiált adatfolyam-szabályzatok, így közvetlenül a próbaverzióra vagy éles környezetben is használhatja őket. 

A jelenleg elérhető előre definiált adatfolyam-szabályzatokat:<br/>'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'.

Ha nincsenek különleges követelmények (például ha más protokollt adja meg, ezzel kell használnia egy egyéni kulcstovábbítást, vagy szeretné használni, törölje a jelet hangsávra), létrehozhat egy egyéni Streamelési szabályzatot. 

 
> [!IMPORTANT]
> * Tulajdonságainak **szabályzatokat. adatfolyam** a DateTime típusú állandóan UTC formátumban vannak.
> * Korlátozott számú házirendeket tervezzen a Media Services-fiók és újból felhasználja őket a Streamelési Lokátorok, amikor szükség van a beállítások. További információkért lásd: [kvóták és korlátozások](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Döntési fa

Az alábbi döntési fája segítségével válasszon egy előre definiált adatfolyam-szabályzatot a forgatókönyvhöz.

Kattintson a képre a teljes méretű megjelenítéshez.  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>Példák

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

### <a name="encrypted"></a>Titkosítva 

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

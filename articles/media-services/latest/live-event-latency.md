---
title: LiveEvent alacsony késési beállításai a Azure Media Servicesban | Microsoft Docs
description: Ez a témakör áttekintést nyújt az alacsony késésű LiveEvent, és bemutatja, hogyan állítható be a kis késleltetés.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: 24eb5f8691c94c1f319820f3133b66065504ace3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87000891"
---
# <a name="live-event-low-latency-settings"></a>Élő esemény alacsony késési beállításai

Ez a cikk bemutatja, hogyan állíthatja be a kis késleltetést egy [élő esemény](/rest/api/media/liveevents)esetében. Azt is ismerteti, hogy milyen eredményeket láthat a különböző játékosok alacsony késési beállításainak használatakor. Az eredmények a CDN és a hálózati késés alapján változnak.

Az új **LowLatency** funkció használatához állítsa be a **StreamOptionsFlag** a **LowLatency** értékre a **liveevent**. A HLS-lejátszás [LiveOutput](/rest/api/media/liveoutputs) létrehozásakor állítsa a [LiveOutput. HLS. fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) az 1 értékre. Ha a stream működik, használhatja a [Azure Media Player](https://ampdemo.azureedge.net/) (amp bemutató oldalt), és beállíthatja a lejátszási beállításokat az "alacsony késleltetésű heurisztikus profil" használatára.

> [!NOTE]
> A Azure Media Player LowLatency-HeuristicProfile jelenleg az MPEG-DASH protokollban lévő streamek lejátszására lett tervezve, amelyeknek CSF vagy CMAF formátumúnak kell lenniük (például `format=mdp-time-csf` vagy `format=mdp-time-cmaf` ). 

A következő .NET-példa bemutatja, hogyan állíthatja be a **LowLatency** a **liveevent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Tekintse meg a teljes példát: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Élő események késése

Az alábbi táblázatok a késések jellemző eredményeit jelenítik meg (ha a LowLatency jelző engedélyezve van) Media Servicesban, amely attól az időponttól mérve, amikor a hozzájárulási csatorna eléri a szolgáltatást, amikor egy megjelenítő látja a lejátszást a lejátszón. Ha optimálisan szeretné használni az alacsony késést, állítsa be a kódoló beállításait a "Pictures" (GOP) hossz 1 másodpercre. Ha nagyobb GOP-ot használ, minimalizálhatja a sávszélesség-használatot, és az azonos képarányú bitrátát csökkentheti. Ez különösen hasznos a kevesebb mozgást biztosító videóknál.

### <a name="pass-through"></a>Továbbítás 

||2s GOP – alacsony késés engedélyezve|1s GOP – alacsony késés engedélyezve|
|---|---|---|
|**DASH in AMP**|10s|8s|
|**HLS natív iOS-lejátszón**|14s|10s|

### <a name="live-encoding"></a>Live Encoding

||2s GOP – alacsony késés engedélyezve|1s GOP – alacsony késés engedélyezve|
|---|---|---|
|**DASH in AMP**|14s|10s|
|**HLS natív iOS-lejátszón**|18s|13s|

> [!NOTE]
> A végpontok közötti késés a helyi hálózati feltételektől vagy a CDN gyorsítótárazási rétegének bevezetésével függően változhat. Tesztelje a pontos konfigurációkat.

## <a name="next-steps"></a>További lépések

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)

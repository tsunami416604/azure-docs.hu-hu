---
title: LiveEvent alacsony késleltetési beállítások az Azure Media Services - | Microsoft dokumentumok
description: Ez a témakör áttekintést nyújt a LiveEvent alacsony késésű beállításairól, és bemutatja, hogyan állítható be az alacsony késés.
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
ms.openlocfilehash: a82a0644fac099b568ab86ea213b98cd8e7d5c22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199648"
---
# <a name="live-event-low-latency-settings"></a>Élő esemény alacsony késleltetési beállításai

Ez a cikk bemutatja, hogyan állítható be alacsony késleltetés [élő eseményen.](https://docs.microsoft.com/rest/api/media/liveevents) Azt is tárgyalja a tipikus eredményeket, hogy látod, ha az alacsony késleltetési beállítások at különböző lejátszók. Az eredmények a CDN és a hálózati késés függvényeitől függően változnak.

Az új **LowLatency** szolgáltatás használatához a **StreamOptionsFlag** beállítást a LiveEvent eseményen **lowLatency** -re kell **állítania.** A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) for HLS-lejátszás létrehozásakor állítsa a [LiveOutput.Hls.fragmentsPerTsSegment szegmenst](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) 1-re. Miután az adatfolyam elindult, használhatja az [Azure Media Playert](https://ampdemo.azureedge.net/) (AMP bemutatólapot), és beállíthatja a lejátszási beállításokat az "Alacsony késésű heurisztikai profil" használatához.

> [!NOTE]
> Jelenleg az Azure Media Player LowLatency HeuriisticProfile profilját úgy tervezték, hogy az MPEG-DASH protokollban lévő `format=mdp-time-csf` adatfolyamokat CSF vagy CMAF formátumban (például vagy ) `format=mdp-time-cmaf`lejátssza. 

A következő .NET példa bemutatja, hogyan állíthatja be a **LowLatency -t** a **LiveEvent eseményen:**

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

Lásd a teljes példát: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Élő események késése

A következő táblázatok a Media Services késésének tipikus eredményeit mutatják (ha a LowLatency jelző engedélyezve van) attól az időponttól kezdve, amikor a hozzájárulási hírcsatorna eléri a szolgáltatást, és amikor a megtekintő látja a lejátszón a lejátszást. Az alacsony késleltetés optimális használatához a kódoló beállításait 1 másodperces "Képcsoport" (GOP) hosszra kell hangolni. Ha nagyobb GOP-hosszt használ, minimalizálja a sávszélesség-fogyasztást, és ugyanazzal a képkockasebességgel csökkenti a bitrátát. Különösen előnyös a kevésbé mozgású videókban.

### <a name="pass-through"></a>Továbbítás 

||2. GOP alacsony késleltetés engedélyezve|1. GOP alacsony késleltetés engedélyezve|
|---|---|---|
|DASH az AMP-ben|10-es évek|8-as évek|
|HLS natív iOS lejátszón|14s|10-es évek|

### <a name="live-encoding"></a>Live Encoding

||2. GOP alacsony késleltetés engedélyezve|1. GOP alacsony késleltetés engedélyezve|
|---|---|---|
|DASH az AMP-ben|14s|10-es évek|
|HLS natív iOS lejátszón|18s|13s|

> [!NOTE]
> A végpontok késése a helyi hálózati feltételektől függően vagy egy CDN-gyorsítótárazási réteg bevezetésével változhat. Tesztelje a pontos konfigurációkat.

## <a name="next-steps"></a>További lépések

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő közvetítésoktató anyaga](stream-live-tutorial-with-api.md)


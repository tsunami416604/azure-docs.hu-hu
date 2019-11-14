---
title: Azure Media Services élő átirat | Microsoft Docs
description: Ez a cikk ismerteti a Azure Media Services élő átírását.
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 98084b9bb6f19d22c7995d3044bb32ceaa947dc5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040423"
---
# <a name="live-transcription-preview"></a>Élő átírás (előzetes verzió)

Az Azure Media Service videó-, hang-és más protokollokat tartalmazó szövegeket biztosít. Ha az élő streamet az MPEG-DASH vagy a HLS/CMAF használatával teszi közzé, akkor a videó-és hanganyagokkal együtt a szolgáltatás a IMSC 1.1-kompatibilis TTML, amely az MPEG-4 rész 30 (ISO/IEC 14496-30) töredékbe van csomagolva. Ha a kézbesítést HLS/TS-n keresztül használja, a szöveg kidarabolt VTT lesz kézbesítve. 

Ez a cikk azt ismerteti, hogyan engedélyezhető az élő átírás egy élő esemény Azure Media Services v3-vel való továbbításakor. A folytatás előtt győződjön meg arról, hogy ismeri a Media Services v3 REST API-k használatát (részletekért tekintse meg [ezt az oktatóanyagot](stream-files-tutorial-with-rest.md) ). Ismernie kell az [élő adatfolyam-továbbítási](live-streaming-overview.md) koncepciót is. Javasoljuk, hogy a [streamet a Media Services oktatóanyaggal élő közvetítéssel](stream-live-tutorial-with-api.md) fejezze be. 

> [!NOTE]
> Jelenleg az élő átírás csak előzetes verzióként érhető el az USA 2. nyugati régiójában. Támogatja az angol nyelvű szöveg átírását. A szolgáltatás API-referenciája ebben a dokumentumban van – mivel az előzetes verzióban érhető el, a részletek nem érhetők el a REST-dokumentumokban. 

## <a name="creating-the-live-event"></a>Az élő esemény létrehozása 

Az élő esemény létrehozásához a PUT műveletet a 2019-05-01-es verzióra kell elküldeni, például: 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

A művelet a következő törzstel rendelkezik (ahol egy átmenő élő esemény jön létre az RTMP-vel a betöltési protokollként). Vegye figyelembe a transzkripciós tulajdonság hozzáadását. A nyelvhez csak az en-US engedélyezett érték adható meg. 

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Az élő esemény állapota csak akkor kérdezhető le, ha a "Running" állapotba kerül, ami azt jelzi, hogy most már küldhet egy, az RTMP-hírcsatornát is. Mostantól ugyanazok a lépések hajthatók végre, mint az oktatóanyagban, például az előnézeti hírcsatorna ellenőrzése és az élő kimenetek létrehozása. 

## <a name="delivery-and-playback"></a>Kézbesítés és lejátszás 

Tekintse át a [dinamikus csomagolás áttekintése](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) című cikket, amely bemutatja, hogyan használja a szolgáltatás a dinamikus csomagolást a videó-, hang-és egyéb protokollok továbbítására. Ha az élő streamet az MPEG-DASH vagy a HLS/CMAF használatával teszi közzé, akkor a videó-és hanganyagokkal együtt a szolgáltatás a IMSC 1.1-kompatibilis TTML, amely az MPEG-4 rész 30 (ISO/IEC 14496-30) töredékbe van csomagolva. Ha a kézbesítést HLS/TS-n keresztül használja, a szöveg kidarabolt VTT lesz kézbesítve. A stream lejátszásához használhat egy weblejátszót, például a [Azure Media Player](use-azure-media-player.md) .  

> [!NOTE]
>  Azure Media Player használata esetén használja a 2.3.3-es vagy újabb verziót.

## <a name="known-issues"></a>Ismert problémák 

Előzetes verzióban az élő átírással kapcsolatos ismert problémák a következők: 

* A szolgáltatás csak az USA 2. nyugati régiójában érhető el.
* Az alkalmazásoknak az előnézeti API-kat kell használniuk, amelyeket a [Media Services v3 OpenAPI specifikációjának](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) leírásában talál.
* Az egyetlen támogatott nyelv az angol.
* A tartalomvédelem tekintetében csak az AES-borítékok titkosítása támogatott.

## <a name="next-steps"></a>Következő lépések

[Media Services áttekintése](media-services-overview.md)
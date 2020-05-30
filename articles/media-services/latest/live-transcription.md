---
title: Élő átírás
titleSuffix: Azure Media Services
description: Ismerkedjen meg Azure Media Services élő átírással.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: inhenkel
ms.openlocfilehash: 9481b4ee2f225c7f76337d73b27630e4c67cc780
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193605"
---
# <a name="live-transcription-preview"></a>Élő átírás (előzetes verzió)

Az Azure Media Service videó-, hang-és szöveges szolgáltatásokat biztosít különböző protokollokban. Ha az élő streamet az MPEG-DASH vagy a HLS/CMAF használatával teszi közzé, akkor a videó-és hanganyagokkal együtt a szolgáltatás a IMSC 1.1-kompatibilis TTML-ban továbbítja az átmásolt szöveget. A kézbesítés az MPEG-4 rész 30 (ISO/IEC 14496-30) töredékbe van csomagolva. Ha a kézbesítést HLS/TS-n keresztül használja, a szöveg kidarabolt VTT lesz kézbesítve.

Ez a cikk azt ismerteti, hogyan engedélyezhető az élő átírás egy élő esemény Azure Media Services v3-vel való továbbításakor. A folytatás előtt győződjön meg arról, hogy ismeri a Media Services v3 REST API-k használatát (részletekért tekintse meg [ezt az oktatóanyagot](stream-files-tutorial-with-rest.md) ). Ismernie kell az [élő adatfolyam-továbbítási](live-streaming-overview.md) koncepciót is. Azt javasoljuk, hogy a [streamet a Media Services oktatóanyaggal élő közvetítéssel](stream-live-tutorial-with-api.md) fejezze be.

> [!NOTE]
> Jelenleg az élő átírás csak előzetes verzióként érhető el az USA 2. nyugati régiójában. Támogatja az angol nyelvű szöveg átírását. Az ehhez a szolgáltatáshoz tartozó API-hivatkozás az alábbi – mert előzetes verzióban érhető el, a részletek nem érhetők el a REST-dokumentumokban.

## <a name="creating-the-live-event"></a>Az élő esemény létrehozása

Az élő esemény létrehozásához a PUT műveletet a 2019-05-01-Preview verzióra kell elküldeni, például:

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

Lekérdezheti az élő esemény állapotát, amíg a "Running" állapotba nem kerül, ami azt jelzi, hogy most már küldhet egy, az RTMP-hírcsatornát is. Mostantól ugyanazok a lépések láthatók, mint az oktatóanyagban, például az előnézeti hírcsatorna ellenőrzése és az élő kimenetek létrehozása.

## <a name="start-transcription-after-live-event-has-started"></a>Átírás az élő esemény elindítása után

Az élő átírást egy élő esemény megkezdése után indíthatja el. Az élő átírások bekapcsolásához javítja az élő eseményt, hogy tartalmazza az "átiratok" tulajdonságot. Az élő átírások kikapcsolásához az "átiratok" tulajdonság el lesz távolítva az élő esemény objektumból.

> [!NOTE]
> Ha az élő esemény során többször is be-vagy kikapcsolja az átírást, nem támogatott a forgatókönyv.

Ez az élő átírások bekapcsolására szolgáló példa.

JAVÍTÁS```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

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

## <a name="transcription-delivery-and-playback"></a>Transzkripciós kézbesítés és lejátszás

Tekintse át a [dinamikus csomagolás áttekintése című](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) cikket, amely bemutatja, hogyan használja a szolgáltatás a dinamikus csomagolást videó, hang és szöveg továbbítására különböző protokollokban. Ha az élő streamet az MPEG-DASH vagy a HLS/CMAF használatával teszi közzé, akkor a videó-és hanganyagokkal együtt a szolgáltatás a IMSC 1.1-kompatibilis TTML-ban továbbítja az átmásolt szöveget. Ez a kézbesítés az MPEG-4 rész 30 (ISO/IEC 14496-30) töredékbe van csomagolva. Ha a HLS/TS-n keresztül kézbesítést használ, a szöveg feldarabolt VTT lesz kézbesítve. A stream lejátszásához használhat egy weblejátszót, például a [Azure Media Player](use-azure-media-player.md) .  

> [!NOTE]
> Azure Media Player használata esetén használja a 2.3.3-es vagy újabb verziót.

## <a name="known-issues"></a>Ismert problémák

Az előzetes verzióban az alábbi ismert problémák állnak az élő átirattal:

* A szolgáltatás csak az USA 2. nyugati régiójában érhető el.
* Az alkalmazásoknak az előnézeti API-kat kell használniuk, amelyeket a [Media Services v3 OpenAPI-specifikáció](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)ismertet.
* Az egyetlen támogatott nyelv az angol (en-US).
* A tartalomvédelem csak az AES-borítékok titkosítását támogatja.

## <a name="next-steps"></a>További lépések

* [Media Services áttekintése](media-services-overview.md)

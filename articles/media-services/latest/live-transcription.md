---
title: Élő átírás
titleSuffix: Azure Media Services
description: További információ az Azure Media Services élő átírásáról.
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
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499873"
---
# <a name="live-transcription-preview"></a>Élő átírás (előzetes verzió)

Az Azure Media Service különböző protokollokban biztosítja a video-, hang- és szöveganyagokat. Amikor az élő közvetítést MPEG-DASH vagy HLS/CMAF használatával teszi közzé, majd a videóval és hanggal együtt szolgáltatásunk az IMSC1.1-kompatibilis TTML-ben továbbítja az átírt szöveget. A szállítás MPEG-4 Part 30 (ISO/IEC 14496-30) töredékekbe van csomagolva. Ha a szállítás hls/TS-en keresztül, majd a szöveg szállított darabolt VTT.

Ez a cikk bemutatja, hogyan engedélyezheti az élő átírást élő események streamelésekesetén az Azure Media Services v3-as használatával. Mielőtt folytatna, győződjön meg arról, hogy ismeri a Media Services v3 REST API-k használatát (a részleteket az [oktatóanyagban](stream-files-tutorial-with-rest.md) találja). Ismernie kell az [élő streamelés](live-streaming-overview.md) fogalmát is. Javasoljuk, hogy töltse ki a [Stream live with Media Services](stream-live-tutorial-with-api.md) oktatóanyag.

> [!NOTE]
> Jelenleg az élő transzkripció csak az USA nyugati régiójában érhető el előnézeti funkcióként. Támogatja a kimondott szavak angol nyelvű átírását a szöveghez. A szolgáltatás API-hivatkozása az alábbiakban található – a becasuse előzetes verzióban érhető el, a részletek nem érhetők el a REST-dokumentumokkal.

## <a name="creating-the-live-event"></a>Az élő esemény létrehozása

Az élő esemény létrehozásához küldje el a PUT műveletet a 2019-05-01-preview verzióra, például:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

A művelet a következő törzsű (ahol egy áthaladási élő esemény jön létre rtmp, mint a betöltési protokoll). Figyelje meg egy átírástulajdonság hozzáadását. A nyelv egyetlen engedélyezett értéke az en-US.

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

Az élő esemény állapotának lekérdezése mindaddig, amíg "Futás" állapotba nem kerül, ami azt jelzi, hogy most már küldhet hozzájárulási RTMP-hírcsatornát. Most már ugyanazokat a lépéseket, mint ebben az oktatóanyagban, például az előnézeti hírcsatorna ellenőrzése és az Élő kimenetek létrehozása.

## <a name="transcription-delivery-and-playback"></a>Átírás kézbesítése és lejátszása

Tekintse át a [dinamikus csomagolásáttekintésről](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) szóló cikket arról, hogy szolgáltatásunk hogyan használja a dinamikus csomagolást a videók, hanganyagok és szövegek különböző protokollokban történő szállításához. Amikor az élő közvetítést MPEG-DASH vagy HLS/CMAF használatával teszi közzé, majd a videóval és hanggal együtt szolgáltatásunk az IMSC1.1-kompatibilis TTML-ben továbbítja az átírt szöveget. Ez a szállítás MPEG-4 Part 30 (ISO/IEC 14496-30) töredékekbe van csomagolva. Ha a szállítás segítségével HLS /TS, majd a szöveg szállított darabolt VTT. Használhatja a weblejátszó, például az [Azure Media Player](use-azure-media-player.md) az adatfolyam lejátszásához.  

> [!NOTE]
> Az Azure Media Player használata esetén használja a 2.3.3-as vagy újabb verziót.

## <a name="known-issues"></a>Ismert problémák

Az előzetes verzióban az alábbi ismert problémák vannak az élő átírással kapcsolatban:

* A funkció csak az USA nyugati részén 2 érhető el.
* Az alkalmazásoknak a [Media Services v3 OpenAPI specifikációjában](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)ismertetett előzetes API-kat kell használniuk.
* Az egyetlen támogatott nyelv az angol (en-us).
* Tartalomvédelem esetén csak az AES borítéktitkosítás támogatott.

## <a name="next-steps"></a>További lépések

* [A Media Services áttekintése](media-services-overview.md)

---
title: Azure Media Clipper-levágási feladatok elküldése | Microsoft Docs
description: A levágási feladatok Azure Media Clipperből való elküldésének lépései
services: media-services
keywords: klip; alklip; kódolás; média
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 04d0d2bb8939c8036ec6817c58f9ac2fbb3acd72
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684979"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Levágási feladatok elküldése az Azure Media Clipperből 

Az Azure Media Clipper **submitSubclipCallback** módszert igényel a kivágási feladatok elküldéséhez. Ez a függvény a Clipper kimenetének egy webszolgáltatásba való bevezetésére szolgál. Ez a webszolgáltatás a kódolási feladatot küldi el. A Clipper kimenete vagy egy Media Encoder Standard kódolási beállításkészlet a megjelenített feladatokhoz, vagy a dinamikus jegyzékfájl-szűrési hívások REST API hasznos adat. Ez az áteresztő modell azért szükséges, mert a Media Services-fiók hitelesítő adatai nem biztonságosak az ügyfél böngészőjében.

Az alábbi ábra a böngésző-ügyfél, a webszolgáltatás és a Azure Media Services közötti munkafolyamatot mutatja be: ![Azure Media Clipper Sequence diagram](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

Az előző ábrán a négy entitás a következő: a végfelhasználó böngészőjében, a webszolgáltatásban, a Clipper-erőforrásokat üzemeltető CDN-végponton és a Azure Media Services. Amikor a végfelhasználó a weboldalra navigál, a lap beolvassa a lenyíró JavaScript-és CSS-erőforrásokat a fogadó CDN-végpontból. A végfelhasználó konfigurálja a levágási feladatot vagy a dinamikus jegyzékfájl-létrehozási hívást a böngészőből. Amikor a végfelhasználó elküldi a feladatot vagy a szűrő létrehozási hívását, a böngésző a feladathoz tartozó adattartalmat egy olyan webszolgáltatáshoz helyezi, amelyet telepítenie kell. Ez a webszolgáltatás végső soron elküldi a kivágási feladatot vagy szűrő-létrehozási hívást Azure Media Services a Media Services-fiók hitelesítő adataival.

A következő mintakód egy minta **submitSubclipCallback** metódust mutat be. Ebben a módszerben a Media Encoder Standard kódolási beállításkészlet HTTP-BEJEGYZÉSét implementálja. Ha a bejegyzés sikeres volt (**eredmény**), akkor a rendszer megoldotta az **ígéretet** , ellenkező esetben a rendszer elutasítja a hiba részleteit.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
A beküldési művelet kimenete vagy a megjelenített feladathoz tartozó Media Encoder Standard kódolási beállításkészlet, vagy a dinamikus jegyzékfájl-szűrők REST API hasznos adat.

## <a name="submitting-encoding-job-to-create-video"></a>Kódolási feladatok elküldése videó létrehozásához
Egy Media Encoder Standard kódolási feladatot küldhet egy frame-pontos videoklip létrehozásához. A kódolási feladatokhoz megjelenített videók, új töredezett MP4-fájlok hozhatók létre.

A megjelenített nyírási feladatok kimeneti szerződése egy JSON-objektum, amely a következő tulajdonságokkal rendelkezik:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

A kódolási feladatok végrehajtásához küldje el a Media Encoder Standard kódolási feladatot a társított beállításkészlettel. A kódolási feladatok [.net SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) -val vagy [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset)történő elküldésével kapcsolatos részletekért tekintse meg ezt a cikket.

## <a name="quickly-creating-video-clips-without-encoding"></a>Videoklipek gyors létrehozása kódolás nélkül
A kódolási feladatok létrehozásának alternatívája az Azure Media Clipper használatával dinamikus jegyzékfájl-szűrőket hozhat létre. A szűrők nem igénylik a kódolást, és gyorsan létrehozhatók, mivel új eszköz nem jön létre. A szűrők kivágására szolgáló kimeneti szerződés egy JSON-objektum, amely a következő tulajdonságokkal rendelkezik:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

A REST-hívás dinamikus jegyzékfájl-szűrő létrehozásához való elküldéséhez küldje el a társított szűrő adattartalmát a [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest)használatával.

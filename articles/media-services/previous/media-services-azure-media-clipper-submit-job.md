---
title: Azure Media Clipperrel klipekkel kapcsolatos feladatok elküldése |} A Microsoft Docs
description: Az Azure Media Clipperrel klipekkel kapcsolatos feladatok elküldésének lépései
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 02/08/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a6f6033fb52c54cf3f9987743fcd725e3539803
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453796"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Az Azure Media Clipperrel klipekkel kapcsolatos feladatok elküldése 

Az Azure Media Clipperrel van szükség egy **submitSubclipCallback** kapcsolást feladatküldéshez kezeléséhez végrehajtandó módszer. Ez a függvény végrehajtási egy HTTP POST webszolgáltatás Clipperrel kimenetre van. A webszolgáltatás, ahol elküldheti a kódolási feladat. A Clipperrel kimenete vagy a Media Encoder Standard renderelt feladatok készletet, vagy a REST API-val hasznos dinamikusjegyzék-szűrő hívások kódolás. Ez a csatlakoztatott modell szükség, mert a media services-fiók hitelesítő adatai nem biztonságosak, az ügyfél böngészőjében.

A következő feladatütemezési ábra mutatja be a munkafolyamat a böngészőalapú ügyfél, a webszolgáltatás és az Azure Media Services között: ![Az Azure Media Clipperrel Szekvenciadiagram](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

A fenti ábrán a négy entitások vannak: a felhasználó böngészőben, a webszolgáltatást, a CDN-végpontot üzemeltető Clipperrel erőforrásokkal és az Azure Media Services. Amikor a végfelhasználó a weblapot, a lap beolvasása a Clipperrel JavaScript és CSS-erőforrásokat üzemeltetési a CDN-végponthoz. A végfelhasználó a Kivágás feladat vagy a dinamikusjegyzék-szűrő létrehozása hívás a böngésző konfigurálja. Amikor a felhasználó ad meg a feladat vagy szűrő létrehozási hívásnak, a böngészőben a feladat hasznos egy webszolgáltatás, amelyet telepítenie kell a program keretében. A webszolgáltatás végső soron elküldi a Kivágás feladatot, vagy szűrő létrehozási hívásnak az Azure Media Services használata a media services-fiók hitelesítő adatait.

Az alábbi kódmintában látható egy minta **submitSubclipCallback** metódust. Ezzel a módszerrel a HTTP POST, a Media Encoder Standard kódolási előbeállítás valósíthatja meg. Ha sikeres volt-e a bejegyzés (**eredmény**), a **Promise** megoldódott, ellenkező esetben nem fogadta el a hiba részleteit.

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
A feladat elküldése kimenete a Media Encoder Standard kódolási előbeállítás megjelenített feladat, vagy a REST API-val hasznos dinamikusjegyzék-szűrők számára.

## <a name="submitting-encoding-job-to-create-video"></a>Hozhat létre a videó kódolási feladat elküldése
A Media Encoder Standard kódolási feladat létrehozása egy keret pontos videoklipet küldhet. Kódolási feladat a termék jelennek meg a videókat, egy új töredékes MP4-fájlt.

A feladat kimeneti szerződés a megjelenített kapcsolást egy JSON-objektum a következő tulajdonságokkal:

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

Hajtsa végre a kódolási feladat elküldéséhez a Media Encoder Standard hozzárendelt feladathoz kódolási előbeállítást. Lásd a kódolás elküldése a jelen cikkben alább feladatokat a következővel: a [.NET SDK-val](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) vagy [REST API-val](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Gyors létrehozásakor videoklipeket kódolás nélkül
Ahelyett, hogy a kódolási feladat létrehozása, az Azure Media Clipperrel használhatja dinamikusjegyzék-szűrők létrehozásához. Szűrők nem szükséges kódolás, és gyorsan létrehozhatók, egy új objektumot nem jön létre. Egy szűrő kapcsolást kimeneti szerződése egy JSON-objektum a következő tulajdonságokkal:

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

Dinamikusjegyzék-szűrő létrehozása a REST-hívást elküldeni, küldje el a kapcsolódó szűrő hasznos használatával a [REST API-val](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).

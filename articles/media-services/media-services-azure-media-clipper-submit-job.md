---
title: "Azure Media Webmontázskészítő Kivágás feladatok elküldéséhez |} Microsoft Docs"
description: "Azure Media Webmontázskészítő Kivágás-feladatok elküldésekor lépései"
services: media-services
keywords: "Klip; subclip; kódolás; adathordozó"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d29889a4c972638f5d127e9c518aa85fbc19d861
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Az Azure Media Webmontázskészítő Kivágás feladatok elküldéséhez
Az Azure Media Webmontázskészítő szükséges egy **submitSubclipCallback** Kivágás feladat elküldése kezelése végrehajtandó metódus. Ez a függvény megkülönbözteti a Webmontázskészítő kimeneti webszolgáltatásnak HTTP POST megvalósításához. Ez a webszolgáltatás, ahol is elküldhetik a kódolási feladat. A Webmontázskészítő eredménye vagy a Media Encoder Standard megjelenített feladatok készletet, vagy a REST API forgalma dinamikus jegyzék szűrő hívások kódolást. A csatlakoztatott modell szükség, mert a media services-fiók hitelesítő adatait az ügyfél böngészője nem biztonságosak.

A következő feladatütemezési ábra szemlélteti a munkafolyamat a böngészőalapú ügyfél, a webszolgáltatás és az Azure Media Services között: ![Azure Media Webmontázskészítő Szekvenciadiagram](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

A fenti ábrán a négy entitások legyenek: a végfelhasználó böngésző, a webes szolgáltatás, a CDN-végpont a Webmontázskészítő erőforrások és az Azure Media Services üzemeltető. Amikor a felhasználók megnyitják a weblapot, a lap lekérése a Webmontázskészítő JavaScript és a CSS-erőforrások az üzemeltetési CDN-végpontot. A végfelhasználó konfigurálja a Kivágás feladat vagy a dinamikus jegyzék szűrő létrehozási hívás a böngészőjükben. Amikor a végfelhasználó ad meg a feladat vagy szűrő létrehozási híváshoz, a böngésző a feladat forgalma egy webszolgáltatás, amelyet telepíteni kell a helyezi. Ez a webszolgáltatás végső soron elküldi a Kivágás feladatot, vagy szűrő létrehozási híváshoz. az Azure Media Services segítségével a media services-fiók hitelesítő adatait.

Az alábbi példakód mutatja be egy minta **submitSubclipCallback** metódust. Ezzel a módszerrel a HTTP POST, a Media Encoder Standard kódolási beállításkészlet valósíthatja meg. Ha sikeres volt-e a FELADÁS egy vagy több (**eredmény**), a **ígéret** feloldja, ellenkező esetben nem fogadta el a hiba részletes adatait.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, retuns true if the operation was accept in the back-end; otherwise, returns false.
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
A feladat elküldése eredménye a Media Encoder Standard kódolási beállításkészlet megjelenített feladat vagy a REST API hasznos dinamikus jegyzék szűrők.

## <a name="submitting-encoding-job-to-create-video"></a>Videó létrehozásához kódolási feladat elküldése
A keret pontos videó klip létrehozásához Media Encoder Standard kódolási feladat küldése Kódolási feladat által előállított videók megjelenítve, egy új töredékes MP4-fájlokat.

A feladat kimenete szerződés megjelenített levágás egy JSON-objektum a következő tulajdonságokkal:

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
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to sumit the subclip job.
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

A Media Encoder Standard végezhető el a kódolási feladat elküldeni a társított a kódolási feladat az adott néven beállítás. Lásd a jelen cikkben alább kódolás elküldése a feladatok használata a [.NET SDK](https://docs.microsoft.com/en-us/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) vagy [REST API](https://docs.microsoft.com/en-us/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Gyors létrehozása kódolás nélkül videó videóklipeket
Ahelyett, hogy egy kódolási feladat, Azure Media Webmontázskészítő használhatja dinamikus manifest-szűrők létrehozásához. Szűrők kódolás nem igényelnek, és nem létrehoz egy új eszközt, gyorsan hozható létre. A szűrő Kivágás kimeneti szerződés egy JSON-objektum a következő tulajdonságokkal:

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

Küldje el a REST-hívást dinamikus manifest-szűrő létrehozásához, a Küldés a kapcsolódó szűrő hasznos használatával a [REST API](https://docs.microsoft.com/en-us/azure/media-services/media-services-rest-dynamic-manifest).
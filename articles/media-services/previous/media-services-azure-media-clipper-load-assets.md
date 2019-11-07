---
title: Eszközök betöltése az Azure Media Clipperbe | Microsoft Docs
description: Az eszközök Azure Media Clipperbe való betöltésének lépései
services: media-services
keywords: klip; alklip; kódolás; média
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 66b4ca5b2859dd306f6eb1c669a07840189f53d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685042"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Eszközök betöltése az Azure Media Clipperbe  

Az eszközöket kétféleképpen lehet betölteni az Azure Media Clipperbe:
1. Statikusan átadása az eszközök egy könyvtárában
2. Eszközök listájának dinamikusan generálása API-n keresztül

## <a name="statically-load-videos-into-clipper"></a>Videók statikus betöltése a Clipperbe
A videók statikus betöltésével lehetővé teheti, hogy a végfelhasználók klipeket hozzanak létre anélkül, hogy az eszköz kiválasztása panelről kiválasztanak videókat.

Ebben az esetben az eszközök statikus készletét kell átadnia a Clipper-nek. Minden eszköz tartalmaz egy AMS-eszközt/szűrőt, a nevet, a közzétett streaming URL-címet. Ha alkalmazható, a rendszer átadja a tartalomvédelmi hitelesítési jogkivonatot vagy a miniatűr URL-címek tömbjét is. Ha a érték be van töltve, a miniatűrök bekerülnek az illesztőfelületbe. Azokban az esetekben, amelyekben az adattárat statikus és kisméretű, a könyvtárban található minden egyes eszközhöz átadhatja a tárgyi szerződést.

> [!NOTE]
> Ha az adattárolóba statikusan tölt be eszközöket, az eszközök **közvetlenül az idővonalhoz** lesznek hozzáadva, és az **eszköz panel nem jelenik**meg. Az első adategység hozzá lesz adva az idővonalhoz, és a többi objektum az idősor jobb oldalán van halmozva.

A statikus adattár betöltéséhez használja a **Load** metódust az egyes eszközök JSON-ábrázolásának továbbításához. Az alábbi mintakód szemlélteti egy adott eszköz JSON-ábrázolását.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> A Load () metódus hívása a Ready (kezelő) metódussal történik, ahogy az előző példában is látható. Az előző példa garantálja, hogy a widget készen áll az eszközök betöltése előtt.

> [!NOTE]
> Ahhoz, hogy a miniatűr URL-címek a várt módon működjenek a beosztási időkereten belül, a videón (az időtartam alapján) és a tömbön belüli időrendi sorrendben egyenletesen el kell osztani őket. A következő JSON-kódrészletet használhatja mintaként a rendszerképek "Media Encoder Standard" processzorral történő generálásához:

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Videók dinamikus betöltése a Clipper-ben
A videók dinamikusan tölthetők be a Clipper-be, hogy a végfelhasználók az eszköz kiválasztására szolgáló panelen is kiválasszák a videókat.

Azt is megteheti, hogy a visszahívási funkcióval dinamikusan betölti az eszközöket. Olyan helyzetekben, ahol az eszközök dinamikusan jönnek létre, vagy a könyvtár nagy méretű, a visszahíváson keresztül kell betölteni. Az eszköz dinamikus betöltéséhez végre kell hajtania az opcionális onLoadAssets visszahívási függvényt. Ezt a függvényt a rendszer az inicializáláskor továbbítja a Clipper-be. A feloldott eszközöknek ugyanahhoz a szerződéshez kell tartoznia, mint a statikusan betöltött eszközök. A következő mintakód szemlélteti a metódus aláírását, a várt bemenetet és a várt kimenetet.

> [!NOTE]
> Amikor dinamikusan tölti be az eszközöket a Clipper-be, az eszközök az **eszköz kiválasztása panelen**jelennek meg.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, returns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```

---
title: Eszközök betöltése az Azure Media clipperbe |} A Microsoft Docs
description: Eszközök betöltése az Azure Media clipperbe lépései
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 415f58d9c6880bc7ef9ba6b6ba3f575fae382f04
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54806887"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Eszközök betöltése az Azure Media clipperbe
Eszközök által kétféleképpen tölthetők az Azure Media clipperbe:
1. A szalagtár eszközök statikusan átadása
2. Dinamikusan létrehozni az API-n keresztül az eszközök listája

## <a name="statically-load-videos-into-clipper"></a>Videók statikusan betöltheti Clipperrel
Videók statikusan töltse be a végfelhasználók számára, hogy klipeket készíthet anélkül, hogy videókat az eszköz kiválasztás panel engedélyezése Clipperrel.

Ebben az esetben átadhatja eszközök statikus készletét a Clipperrel. Minden eszköz egy AMS eszköz/szűrés azonosító, név, már közzétett egy streamelési URL-cím. magában foglalja. Ha szükséges, egy hitelesítési tokent a content protection vagy a tömb Miniatűr URL-címek lehet átadni. Ha az átadott, a miniatűrök fel van töltve a felületen. Az olyan forgatókönyvekben, ahol az eszközintelligencia-könyvtár kis- és statikus az egyes eszközök a könyvtárban az eszközintelligencia szerződésben adhat át.

> [!NOTE]
> Eszközök statikusan betöltenie a Clipperrel, eszközök bővül **közvetlenül az idővonalon a** és a **eszköz ablaktáblában nem jelenik meg**. Az első eszköz bekerül az idővonalon, és az eszközök a többi adatbázisfiókhoz az idősor jobb szélén).

Az eszközintelligencia statikus kódtár betöltése, használja a **betöltése** metódust minden eszköz JSON-ábrázolását adja át. Az alábbi példakód mutatja be a JSON-reprezentációval egy eszközre.

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
> Javasoljuk, hogy lánc a load() metódust hívja meg a ready(handler) metódus az előző példában látható módon. Az előző példában garantálja, hogy a vezérlő készen áll az eszközök betöltése előtt.

> [!NOTE]
> A Miniatűr URL-címek használatával működnek megfelelően, a Clipperrel idővonalról, egyenletesen között kell elosztania (időtartama alapján) a videó és a tömbön belüli időrendi sorrendben. Használhatja az alábbi előre definiált JSON-kódrészlet minta referenciaként a "Media Encoder Standard" processzor rendszerképek létrehozásához:

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

## <a name="dynamically-load-videos-in-clipper"></a>Dinamikusan a Clipperrel a videók betöltése
Videók dinamikusan töltse be a Clipperrel engedélyezéséhez jelölje be a videókat az eszköz kiválasztása panel grafikus ellen, a végfelhasználók számára.

Másik lehetőségként dinamikusan használatával egy visszahívást eszközök betöltheti. Olyan esetekben, ahol eszközök dinamikusan jönnek létre, vagy a könyvtárban nagy a visszahívás útján kell betölteni. Eszközintelligencia dinamikusan betöltése, meg kell valósítani a választható onLoadAssets visszahívást függvény. Ez a függvény az inicializáláskor Clipperrel átad a. A feloldott adategységek meg kell felelnie a statikusan betöltött objektumként ugyanazt a szerződést. A következő példakód azt szemlélteti, a podpis metody, a várt bemeneti és a várt kimeneti.

> [!NOTE]
> Ha dinamikusan betöltenie a Clipperrel eszközök, eszközök jelennek az **eszköz időkiválasztás panel**.

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
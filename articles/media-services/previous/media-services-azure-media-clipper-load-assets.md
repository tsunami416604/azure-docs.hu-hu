---
title: Eszközök betöltése az Azure Media Webmontázskészítő |} Microsoft Docs
description: Eszközök betöltése az Azure Media Webmontázskészítő lépései
services: media-services
keywords: Klip; subclip; kódolás; adathordozó
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a479218ff8bd5addf4273b23c06380859e0ea08
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788297"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Az Azure Media Webmontázskészítő eszközök betöltése
Eszközök két módszer betölthető az Azure Media Webmontázskészítő:
1. A szalagtár eszközök statikusan továbbításához
2. Dinamikus generálása az API-n keresztül eszközök listája

## <a name="statically-load-videos-into-clipper"></a>Videók statikusan betölthető Webmontázskészítő
Videók statikusan betölthető a Webmontázskészítő engedélyezése a felhasználók számára, hogy videóklipeket build videók az eszköz kiválasztása panel kijelölése nélkül.

Ebben az esetben akkor át eszközök statikus meg a Webmontázskészítő. Minden eszköz egy AMS eszköz-vagy szűrőkezelő azonosító, név, közzétett adatfolyam-továbbítási URL tartalmazza. Ha van ilyen, a tartalomvédelem hitelesítésére szolgáló jogkivonat vagy thumbnail tömbje URL-címeket is átadni. Az átadott, ha a miniatűrök fel van töltve a felületén. A forgatókönyvekben, ahol az eszköz könyvtár statikus és kis továbbíthatja a eszköz szerződésben minden eszköz a könyvtárban.

> [!NOTE]
> Eszközök hozzáadott betöltésekor statikusan eszközök azokat a Webmontázskészítő, **erre az ütemterv** és a **eszköz ablaktábla megjelenítése nem**. Az első eszköz hozzáadódik az ütemterv és az eszközök a többi halmozott az ütemterv jobb oldalán).

A statikus eszköz kódtár betöltése, használja a **betöltése** metódus felelt meg az egyes eszközök JSON-ábrázolását. A következő példakód azt szemlélteti, hogy egy eszköz a JSON-megjelenítés.

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
> Javasoljuk, hogy a load() metódust hívja meg az előző példában látható módon ready(handler) metódus lánc. Az előző példában biztosítja, hogy a widget készen áll az eszközök betöltése előtt.

> [!NOTE]
> A Miniatűr URL-címek működnek az Webmontázskészítő idővonal azok kell legyen egyenlően elosztva a videó (időtartam alapján) és a tömbön belüli időrendben sorolja fel. Az alábbi előre definiált JSON-részlet minta referenciaként használható a "Media Encoder Standard" processzorral rendelkező képek létrehozása:

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

## <a name="dynamically-load-videos-in-clipper"></a>Dinamikusan betölteni Webmontázskészítő videók
Videók dinamikusan betölthető a Webmontázskészítő engedélyezése a felhasználók számára, hogy válassza ki a videók elleni vágandó eszköz kijelölés panelről.

Alternatív megoldásként betöltése eszközökre dinamikusan egy visszahívás. Olyan esetekben, ahol eszközök dinamikusan jönnek létre, vagy a könyvtárban nagy a visszahívás keresztül kell betölteni. A betöltés dinamikusan az eszköz, meg kell valósítani a választható onLoadAssets visszahívási függvényt. Ez a funkció átad a inicializáláskor Webmontázskészítő. A feloldott eszközök ugyanabban az egyezményben statikusan betöltött eszközökként kell felelnie. A következő példakód azt mutatja be, a metódus-aláírás, a várt bemeneti és a várt kimeneti.

> [!NOTE]
> Betöltésekor dinamikusan eszközök a Webmontázskészítő be, az eszközök megjelennek-e a a **eszköz ablaktáblát**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
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
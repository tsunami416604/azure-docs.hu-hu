---
title: Az Azure Media Player rövid útmutatója
description: Ismerje meg az Azure Media Player beállításának alapvető lépéseit.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726465"
---
# <a name="azure-media-player-quickstart"></a>Az Azure Media Player rövid útmutatója
Az Azure Media Player könnyen beállítható. Csak néhány percet vesz igénybe a médiatartalmak Azure Media Services-fiókjából történő alapvető lejátszása. Ez a szakasz nagy vonalakban ismerteti az alapvető lépéseket. Az alábbi szakaszok az Azure Media Player beállításának és konfigurálásának konkrétumait ismertetik.  Egyszerűen adja hozzá a következőket a dokumentum `<head>` eleméhez:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **Ne** használja a `latest` verziót éles környezetben, mivel ez igény szerint változhat. Cserélje `latest` le az Azure Media Player egy verziójára; például `latest` cserélje `1.0.0`ki . Az Azure Media Player verziói [innen](azure-media-player-changelog.md)kérdezhetők le.

## <a name="use-the-video-element"></a>A videoelem használata

Ezután egyszerűen `<video>` használja az elemet, ahogy `data-setup` szokott, de egy további attribútummal, amely minden lehetőséget tartalmaz. Ezek a beállítások bármely Azure Media Services-beállítást tartalmazhatnak egy érvényes JSON-objektumban.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Ha nem szeretné használni az automatikus telepítést, kihagyhatja az `data-setup` attribútumot, és manuálisan inicializálhatja a videoelemet.

```html
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)
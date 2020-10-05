---
title: Azure Media Player rövid útmutató
description: Ismerje meg a Azure Media Player beállításának alapvető lépéseit.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "81726465"
---
# <a name="azure-media-player-quickstart"></a>Az Azure Media Player gyorsútmutatója
A Azure Media Player egyszerűen beállítható. Csak néhány percet vesz igénybe, hogy lekérje a médiatartalom alapszintű lejátszását a Azure Media Services-fiókjából. Ez a szakasz nagy vonalakban ismerteti az alapvető lépéseket. Az alábbi szakaszokban a Azure Media Player beállításával és konfigurálásával kapcsolatos konkrét tudnivalókat talál.  Egyszerűen adja hozzá a következőket a dokumentum `<head>` eleméhez:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> A verziót **ne** használja `latest` éles környezetben, mert az igény szerint változhat. Cserélje le a `latest` (Azure Media Player) verzióját a helyére, például a következővel: `latest` `1.0.0` . Azure Media Player verziók lekérdezése [innen](azure-media-player-changelog.md)lehetséges.

## <a name="use-the-video-element"></a>A videó elem használata

Ezután egyszerűen használja az `<video>` elemet úgy, ahogy a szokásos módon tenné, de egy további, `data-setup` tetszőleges beállításokat tartalmazó attribútummal. Ezek a beállítások bármilyen Azure Media Services lehetőséget tartalmazhatnak egy érvényes JSON-objektumban.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Ha nem szeretné az automatikus telepítést használni, kihagyhatja az `data-setup` attribútumot, és manuálisan is inicializálhatja a videó elemet.

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

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)
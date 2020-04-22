---
title: Az Azure Media Player API-metódusai
description: Az Azure Media Player API lehetővé teszi, hogy a videót JavaScript-en keresztül használja, függetlenül attól, hogy a böngésző HTML5-videóval, Flash-sel, Silverlight-sal vagy bármely más támogatott lejátszási technológiával játssza le a videót.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727267"
---
# <a name="api"></a>API #

Az Azure Media Player API lehetővé teszi, hogy a videót JavaScript-en keresztül használja, függetlenül attól, hogy a böngésző HTML5-videóval, Flash-sel, Silverlight-sal vagy bármely más támogatott lejátszási technológiával játssza le a videót.

## <a name="referencing-the-player"></a>Hivatkozás a lejátszóra ##

Az API-függvények használatához hozzá kell férnünk a lejátszó objektumhoz. Szerencsére könnyű megszerezni. Csak meg kell győződnie arról, hogy a videotag rendelkezik azonosítóval. A példabeágyazási kód `vid1`azonosítója . Ha egy oldalon több videó is található, győződjön meg arról, hogy minden videocímke egyedi azonosítóval rendelkezik.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Ha a játékos még nem inicializálódott az adatbeállítási attribútummal vagy más módszerrel, akkor ez a lejátszót is inicializálja.

## <a name="wait-until-the-player-is-ready"></a>Várjon, amíg a lejátszó készen áll ##

Az Azure Media Player videó és API beállításához szükséges idő a használt lejátszási technológiától függően változik. HTML5 gyakran sokkal gyorsabban betölteni, mint a Flash vagy silverlight. Ezért a játékos "kész" funkcióját kell használni minden olyan kód aktiválásához, amely hez a játékos API-ja szükséges.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

VAGY

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API-módszerek ##

Most, hogy hozzáféregy kész lejátszóhoz, szabályozhatja a videót, értékeket kaphat, vagy válaszolhat a videóeseményekre. Az Azure Media Player API-függvénynevei a [HTML5-média API-t](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)próbálják követni. A fő különbség az, hogy a getter / setter funkciókat használják a videó tulajdonságait.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Regisztráció eseményekre ##
Az eseményeket közvetlenül a lejátszó inicializálása után kell regisztrálni, hogy minden eseményt megfelelően jelentsék az alkalmazásnak, és azokat a kész eseményen kívül kell elvégezni.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>További lépések ##

<!---Some context for the following links goes here--->
- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)
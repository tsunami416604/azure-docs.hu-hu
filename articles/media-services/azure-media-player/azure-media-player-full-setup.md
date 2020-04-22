---
title: Az Azure Media Player teljes beállítása
description: Ismerje meg, hogyan állíthatja be az Azure Media Playert.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727241"
---
# <a name="azure-media-player-full-setup"></a>Az Azure Media Player teljes beállítása #

Az Azure Media Player könnyen beállítható. A médiatartalmak egyszerű lejátszása mindössze néhány percet vesz igénybe közvetlenül az Azure Media Services-fiókból. [A minták](https://github.com/Azure-Samples/azure-media-player-samples) a kiadás mintakönyvtárában is rendelkezésre állnak.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>1. lépés: Tartalmazza a JavaScript és CSS fájlokat a fejét az oldal ##

Az Azure Media Player segítségével elérheti a parancsfájlokat a CDN üzemeltetett verziójából. Gyakran ajánlott most a JavaScriptet a végső `<body>` törzscímke `<head>`elé helyezni a helyett, de az Azure Media Player tartalmaz egy "HTML5 Shiv"-et, amelynek a régebbi IE-verziók fejében kell lennie ahhoz, hogy a videocímkét érvényes elemként tartsa tiszteletben.

> [!NOTE]
> Ha már használ egy HTML5-ös shiv, mint [a Modernizr,](http://modernizr.com/) akkor az Azure Media Player JavaScript bárhol. Azonban győződjön meg róla, a változata Modernizr tartalmazza a shiv videó.

### <a name="cdn-version"></a>CDN verzió ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> **Ne** használja a `latest` verziót éles környezetben, mivel ez igény szerint változhat. Cserélje `latest` le az Azure Media Player egy verziójára. Cserélje ki `latest` például a helyére. `2.1.1` Az Azure Media Player verziói [innen](azure-media-player-changelog.md)kérdezhetők le.

> [!NOTE]
> A `1.2.0` kiadás óta már nem szükséges a helyet felvenni a tartalék technikusok közé (automatikusan felveszi a helyet az azuremediaplayer.min.js fájl relatív elérési útjáról). Módosíthatja a helyét a tartalék techs hozzáadásával a `<head>` következő szkriptet az után a fenti szkriptek.

> [!NOTE]
> A Flash és a Silverlight beépülő modulok jellege miatt az swf- és xap-fájlokat bizalmas adatok és adatok nélkül kell üzemeltetni egy tartományban – ezt automatikusan gondoskodunk az Azure CDN által üzemeltetett verzióról.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>2. lépés: HTML5-videocímke hozzáadása az oldalhoz ##

Az Azure Media Player segítségével HTML5-videocímkét használhat a videók beágyazásához. Az Azure Media Player ezután elolvassa a címkét, és minden böngészőben működik, nem csak a HTML5-videót támogató képpen. Az alapszintű árrésen túl az Azure Media Playernek szüksége van néhány további darabra.

1. Az `<data-setup>` attribútum a `<video>` megmondja az Azure Media Player automatikusan állítsa be a videót, ha az oldal készen áll, és olvassa el az attribútum (JSON formátumban).
1. Az `id` attribútum: Kell használni, és egyedi minden videó ugyanazon az oldalon.
1. Az `class` attribútum két osztályt tartalmaz:
    - `azuremediaplayer`az Azure Media Player felhasználói felületének funkcióihoz szükséges stílusokat alkalmazza
    - `amp-default-skin`az alapértelmezett felületet alkalmazza a HTML5 vezérlőkre
1. Az `<source>` két szükséges attribútumot tartalmaz
    - `src`az attribútum tartalmazhat egy **.ism/manifest* fájlt az Azure Media Services-ből, az Azure Media Player automatikusan hozzáadja a DASH, SMOOTH és HLS URL-címeit a lejátszóhoz
    - `type`attribútum az adatfolyam szükséges MIME-típusa. A *".ism/manifest"* fájlhoz társított MIME-típus *a következő: "application/vnd.ms-sstr+xml"*
1. A *választható* `<data-setup>` attribútum `<source>` az Azure Media Player, ha vannak olyan egyedi kézbesítési szabályzatok az Azure Media Services, beleértve, de nem kizárólagosan, titkosítási típus (AES vagy PlayReady, Widevine, vagy FairPlay) és a jogkivonat.

Attribútumok, beállítások, források és zeneszámok pontosan úgy szerepeltessenek/zárják ki, mint a HTML5-ös videók esetében.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Alapértelmezés szerint a nagy lejátszás gomb a bal felső sarokban található, így nem fedi el a poszter érdekes részeit. Ha a nagy lejátszás gomb középre szeretné középre, hozzáadhat egy további `amp-big-play-centered` `class` elemet az `<video>` elemhez.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternatív beállítás a dinamikusan betöltött HTML-hez ###

Ha a weboldal vagy alkalmazás betölti a videó tag dinamikusan (ajax, appendChild, stb), hogy nem létezik, amikor az oldal betöltődik, akkor szeretné, hogy manuálisan állítsa be a lejátszó helyett támaszkodva az adat-setup attribútum. Ehhez először távolítsa el az adatbeállítási attribútumot a címkéről, hogy ne zavarjon a lejátszó inicializálásakor. Ezután futtassa a következő JavaScript-kódot egy idővel az Azure Media Player JavaScript betöltése után, és miután a videocímke be van töltve a DOM-ba.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
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
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

A `amp` függvény első argumentuma a videocímke azonosítója. Cserélje ki a sajátjával.

A második argumentum egy beállításobjektum. Ez lehetővé teszi, hogy további lehetőségeket, mint akkor az adat-setup attribútum.

A harmadik érv egy "kész" visszahívás. Miután az Azure Media Player inicializálta, meg hívja ezt a függvényt. A kész visszahívásban az "ez" objektum a játékospéldányra utal.

Elemazonosító használata helyett az elemre is hivatkozhat.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)
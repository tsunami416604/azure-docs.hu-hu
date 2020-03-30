---
title: MPEG-DASH adaptív streamelési videó beágyazása HTML5 alkalmazásba DASH.js fájllal | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan ágyazhat be egy MPEG-DASH Adaptív streamelési videót egy HTML5 alkalmazásba a DASH.js fájllal.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6c1df14ba5a9f233f42750d4e6dea68a7d6ddc0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564856"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>MPEG-DASH adaptív streamelt videók beágyazása DASH.js-sel rendelkező HTML5-alkalmazásba  

## <a name="overview"></a>Áttekintés
Az MPEG-DASH a videotartalmak adaptív streamelésének ISO szabványa, amely jelentős előnyökkel jár a kiváló minőségű, adaptív videostreaming-kimenetet nyújtani kívánó fejlesztők számára. Az MPEG-DASH segítségével a videofolyam automatikusan alacsonyabb definícióhoz igazodik, amikor a hálózat túlterheltté válik. Ez csökkenti annak valószínűségét, hogy a megtekintő "szüneteltetett" videót lát, miközben a lejátszó letölti a következő néhány másodpercet a lejátszáshoz (azaz csökkenti a pufferelés valószínűségét). A hálózati torlódás okának csökkenésével a videólejátszó visszatér a jobb minőségű adatfolyamhoz. A szükséges sávszélesség adaptálásának képessége a videó gyorsabb kezdési idejét is eredményezi. Ez azt jelenti, hogy az első néhány másodperc lejátszható egy gyorsan letölthető, gyengébb minőségű szegmensben, majd a megfelelő tartalom pufferelése után magasabb minőségre léphet.

Dash.js egy nyílt forráskódú MPEG-DASH videó lejátszó javascriptben írt. Célja, hogy egy robusztus, cross-platform játékos, amely szabadon újra használható alkalmazások, amelyek megkövetelik a videolejátszást. MPEG-DASH lejátszást biztosít minden olyan böngészőben, amely támogatja a W3C Media Source Extensions (MSE), ma a Chrome, a Microsoft Edge és az IE11 (más böngészők jelezték szándékukat az MSE támogatására). A DASH.js-ről további információt a GitHub dash.js tárházban talál.

## <a name="creating-a-browser-based-streaming-video-player"></a>Böngészőalapú adatfolyam-lejátszó létrehozása
Ahhoz, hogy hozzon létre egy egyszerű weboldalt, amely megjeleníti a videó lejátszó a várt ellenőrzések, mint a játék, szünet, visszatekerés, stb, meg kell:

1. HTML-lap létrehozása
2. A videocímke hozzáadása
3. Add hozzá a dash.js lejátszót
4. A lejátszó inicializálása
5. CsS-stílus hozzáadása
6. Az eredmények megtekintése az MSE-t megvalósító böngészőben

Inicializálása a játékos lehet kitölteni csak egy maroknyi sornyi JavaScript kódot. Segítségével dash.js, ez tényleg olyan egyszerű beágyazni MPEG-DASH videó a böngésző-alapú alkalmazások.

## <a name="creating-the-html-page"></a>A HTML-lap létrehozása
Az első lépés az, hogy hozzon létre egy szabványos HTML oldalt, amely tartalmazza a **videó** elem, mentse ezt a fájlt basicPlayer.html, mint a következő példa mutatja:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>A DASH.js lejátszó hozzáadása
A dash.js hivatkozási implementáció hozzáadásához meg kell ragadnia a dash.all.js fájlt a dash.js projekt legújabb verziójából. Ezt az alkalmazás JavaScript mappájába kell menteni. Ez a fájl egy kényelmi fájl, amely összehúzza az összes szükséges dash.js kódot egyetlen fájlba. Ha körülnéz a dash.js tárházban, megtalálja az egyes fájlokat, a tesztkódot és még sok mást, de ha csak a dash.js fájlt szeretné használni, akkor a dash.all.js fájlra van szüksége.

Ha hozzá szeretné adni a dash.js lejátszót az alkalmazásokhoz, adjon hozzá egy parancsfájlcímkét a basicPlayer.html fejszakaszához:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Ezután hozzon létre egy függvényt a lejátszó inicializálásához, amikor az oldal betöltődik. Adja hozzá a következő parancsfájlt a dash.all.js betöltésének sora után:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Ez a függvény először létrehoz egy DashContext. Ezzel konfigurálhatja az alkalmazást egy adott futásidejű környezetben. Technikai szempontból meghatározza azokat az osztályokat, amelyeket a függőségi injektálási keretrendszernek az alkalmazás összeállításakor használnia kell. A legtöbb esetben a Dash.di.DashContext-t használja.

Ezután példányosítsa meg a dash.js keretrendszer, a MediaPlayer elsődleges osztályát. Ez az osztály tartalmazza a szükséges alapvető módszereket, például a lejátszást és a szüneteltetést, kezeli a kapcsolatot a videoelemmel, és kezeli a médiabemutató-leírás (MPD) fájl értelmezését is, amely leírja a lejátszandó videót.

A MediaPlayer osztály indítási() funkciója annak biztosítására van megnevezve, hogy a lejátszó készen álljon a videó lejátszására. A funkció többek között biztosítja, hogy az összes szükséges osztály (a környezet meghatározása szerint) be legyen töltve. Ha a lejátszó készen áll, az attachView() funkcióval csatolhatja hozzá a videoelemet. Az indítási funkció lehetővé teszi, hogy a MediaPlayer befecskendezze a videostreamet az elembe, és szükség esetén vezérelje a lejátszást.

Adja át az MPD-fájl URL-címét a MediaPlayernek, hogy tudjon a rról a videóról, amelyet várhatóan le fog játszani. A most létrehozott setupVideo() függvényt végre kell hajtani, miután az oldal teljesen betöltődött. Ehhez használja a testelem onload eseményét. Módosítsa `<body>` az elemet a következőre:

```html
    <body onload="setupVideo()">
```

Végül állítsa be a méretét a videó elem css használatával. Adaptív streamelési környezetben ez különösen fontos, mivel a lejátszott videó mérete változhat, ahogy a lejátszás alkalmazkodik a változó hálózati feltételekhez. Ebben az egyszerű demo egyszerűen kényszeríteni a videó elem, hogy 80%-a rendelkezésre álló böngésző ablak hozzáadásával a következő CSS a fej rész az oldal:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Videó lejátszása
Videó lejátszásához mutasson a böngészőre az basicPlayback.html fájlra, és kattintson a lejátszás gombra a megjelenített videolejátszón.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:

[GitHub dash.js tárház](https://github.com/Dash-Industry-Forum/dash.js) 


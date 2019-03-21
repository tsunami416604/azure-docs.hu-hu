---
title: Egy MPEG-DASH adaptív Streamelt videók beágyazása dash.js-sel rendelkező HTML5-alkalmazásba |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan egy MPEG-DASH adaptív Streamelés videók beágyazása dash.js-sel rendelkező HTML5-alkalmazásba.
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
ms.openlocfilehash: ff14afc9e438c2b493219e9fa207b2e8793b3c87
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189206"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Egy MPEG-DASH adaptív Streamelt videók beágyazása dash.js-sel rendelkező HTML5-alkalmazásba  

## <a name="overview"></a>Áttekintés
MPEG-DASH egy ISO szabvány a videó tartalmát, amelyet jelentős előnyöket kínál a fejlesztők számára, aki a kiváló minőségű, az adaptív videó kimeneti adatfolyam továbbítása az adaptív streameléshez. MPEG-dash protokollal a video-adatfolyamot igazodik automatikusan alacsonyabb definícióját, ha a hálózat túlterheltnek válik. Ez csökkenti annak lehetőségét, hogy a megjelenítő "szüneteltetett" videó jelent meg, amíg a Windows Media player letölti a play (más néven pufferelés) tovább néhány másodperc alatt. Csökkenti a hálózati torlódás, mivel a videólejátszó ezután visszaküldi jobb minőségű adatfolyam. A szükséges sávszélesség alkalmazkodni is a videó gyorsabb kezdési időt eredményez. Ez azt jelenti, hogy az első néhány másodperc alatt játszhatók le egy gyors letöltési alacsonyabb minőségi szegmens, és majd eltárolva a lépés a magasabb minőségű egyszer elegendő tartalom legfeljebb pufferben.

Dash.js-sel egy nyílt forráskódú MPEG-DASH videolejátszó JavaScript nyelven. A cél, hogy adja meg a nagy teherbírású, többplatformos lejátszók, amely a videó lejátszás igénylő alkalmazások szabadon felhasználható. Minden olyan böngészőre, amely támogatja a W3C Media forrás Extensions (MSE) még ma, hogy a Chrome, a Microsoft Edge és az IE11 (más böngészők támogatásához MSE szándékát jelezte,) az MPEG-DASH lejátszás biztosít. Js dash.js-sel kapcsolatos további információkért tekintse meg a GitHub-adattár dash.js-sel.

## <a name="creating-a-browser-based-streaming-video-player"></a>Böngésző-alapú streamelési videolejátszó létrehozása
Egy egyszerű várt videolejátszót megjelenítő weblap létrehozása azt szabályozza, ilyen a Play áruházból, szüneteltetése, Visszatekerés stb. kell, hogy:

1. Hozzon létre egy HTML-oldalt
2. A videó címke hozzáadása
3. Adja hozzá a Windows Media player dash.js-sel
4. A Windows Media player inicializálása
5. Adjon hozzá néhány CSS-stílus
6. Az eredmények megtekintése a böngészőben, amely megvalósítja az MSE

A Windows Media player inicializálása elvégezhető a mindössze néhány sornyi JavaScript-kódot. Dash.js-sel használja, ez valójában, amely egyszerű, MPEG-DASH videók beágyazása a böngészőalapú alkalmazások.

## <a name="creating-the-html-page"></a>A HTML-oldal létrehozása
Az első lépés az, hogy hozzon létre egy standard HTML tartalmazó oldalra a **videó** elem, a fájl basicPlayer.html, mint az alábbi példa szemlélteti:

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

## <a name="adding-the-dashjs-player"></a>A dash.js-sel Windows Media Player hozzáadása
Az alkalmazás ad hozzá a dash.js-sel referenciaimplementációt, szüksége dash.js-sel projekt 1.0-ás kiadásba dash.all.js fájl megnyitása. Ez az alkalmazás JavaScript mappájában menteni kell. Ez a fájl egy kényelmes fájlt, amely a együttesen kéri le a szükséges dash.js-kódot egyetlen fájlba. Ha tekintse meg a dash.js-sel tárház körül, akkor keresse meg az egyes fájlokat, tesztelje a kódot, és még sok más, de minden ehhez van használja dash.js-sel, az dash.all.js fájl nem a következőkre lesz szüksége.

Szeretne hozzáadni a dash.js-sel Windows Media player az alkalmazások, a fő szakaszt az basicPlayer.html parancsfájl címke hozzáadása:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Ezután hozzon létre egy függvényt, amely a Windows Media player inicializálni az oldal betöltésekor. Adja hozzá a következő szkriptet a sort, amelyben dash.all.js betöltése után:

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

Ez a függvény először létrehoz egy DashContext. Ez az alkalmazás egy adott futtatókörnyezethez konfigurálására szolgál. Egy technikai szempontból meghatározza a osztályokat, amelyeket a függőségi injektálási keretrendszer használnia kell, ha az alkalmazás létrehozása. A legtöbb esetben Dash.di.DashContext használja.

Következő lépésként hozza létre az elsődleges osztály az dash.js-sel keretrendszer, a Media Player. Ez az osztály tartalmazza az alapvető módszerek, például a szükséges lejátszásához és szüneteltetése, kezeli a videóelemet rendelkező kapcsolatot és is kezeli az adathordozó bemutató leírása (MPD) fájlt, amely lejátszható a videó ismerteti az értelmezésében.

Annak érdekében, hogy a Windows Media player készen áll a videó lejátszása a Media Player osztály startup() függvény nevezzük. Többek között a funkció biztosítja, hogy a szükséges osztályokat (a környezet által meghatározott) lettek betöltve. Ha készen áll a lejátszó, a attachView() függvény használatával csatolhat a videóelemet. Az indítási függvényben lehetővé teszi a Media Player a video-adatfolyamot behelyezése elem, és szükség szerint lejátszási mennyiségét is szabályozhatja.

A Media Player URL-címét a MPD fájl adja meg, hogy azt ismer a videó lejátszásához várható. Az imént létrehozott setupVideo() függvény kell hajtható végre, miután az oldal teljes mértékben be van töltve. Ehhez használja a szervezet elem betöltésre. Módosítsa a <body> elem:

```html
    <body onload="setupVideo()">
```

Végezetül állítsa a CSS segítségével videóelemet méretét. Adaptív adatfolyam-továbbítási környezetben ez különösen fontos, mert a mérete, a videó lejátszását változhatnak lejátszási alkalmazkodik a változó hálózati körülmények. Az egyszerű bemutató egyszerűen kényszerítése videó elem 80 %-a rendelkezésre álló böngészőablakban adja hozzá a következő CSS a lap a fő szakaszra:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Videó lejátszása
Videó lejátszása, a basicPlayback.html fájlban a böngészőt, és megjelenik a videólejátszó a lejátszás gombra.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Videolejátszó alkalmazások fejlesztése](media-services-develop-video-players.md)

[GitHub-adattár dash.js-sel](https://github.com/Dash-Industry-Forum/dash.js) 


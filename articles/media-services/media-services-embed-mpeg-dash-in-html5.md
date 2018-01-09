---
title: "Egy MPEG-DASH adaptív adatfolyam-továbbítási videó beágyazása DASH.js HTML5 alkalmazás |} Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan egy MPEG-DASH adaptív adatfolyam-videó beágyazása DASH.js HTML5 alkalmazás."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.openlocfilehash: be0fc51574950cad0558a85b3f20f8b14eafda13
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Egy MPEG-DASH adaptív adatfolyam-továbbítási videó beágyazása DASH.js HTML5 alkalmazás
## <a name="overview"></a>Áttekintés
MPEG-DASH egy ISO szabvány a videotartalom, azok számára kiváló minőségű, adaptív video-kimeneti adatfolyam továbbítására kívánnak jelentős előnyt kínál, amelyek adaptív streameléshez. A MPEG-DASH a video-adatfolyamot automatikusan eldobja alacsonyabb definition a hálózati sávszélességből válik. Ez csökkenti a "felfüggesztett" videó jelent meg, amíg a Windows Media player számára, hogy (más néven pufferelés) a következő néhány másodpercig letölti megjelenítő valószínűségét. Csökkenti a hálózati torlódás, mert a videólejátszó pedig visszatérhet egy magasabb színvonalú adatfolyam. Így meghatározhatja, hogy a szükséges sávszélesség igazítja videó gyorsabb idejét is eredményezi. Amely azt jelenti, hogy az első néhány másodperc is lehet lejátszott egy gyors-letöltési alacsonyabb minőségi szegmens majd eltárolva a lépés akár egy magasabb minőségi egyszer elegendő tartalom pufferben.

Dash.js egy nyílt forráskódú MPEG-DASH videólejátszó JavaScript nyelven írt. A cél, hogy adjon meg egy robusztus, platformfüggetlen player, amelyek a videó lejátszása igénylő alkalmazások szabadon felhasználható. A böngészők támogat a W3C Media forrás Extensions (MSE), amely Chrome, Microsoft Edge és (a más böngészőkkel MSE támogatásához a leképezés jelezte) IE11 MPEG-DASH lejátszás biztosít. Js DASH.js kapcsolatos további információkért tekintse meg a GitHub-tárházban dash.js.

## <a name="creating-a-browser-based-streaming-video-player"></a>Webböngésző-alapú adatfolyam videólejátszó létrehozása
Egy egyszerű weblap, amely megjeleníti a videólejátszó várt létrehozása szabályozza ilyen a play, szüneteltetése, Visszatekerés stb., újra kell:

1. Hozzon létre egy HTML-weblap
2. A videó címke hozzáadása
3. Adja hozzá a dash.js player
4. A Windows Media player inicializálása
5. Adja hozzá az egyes CSS-stílus
6. Az eredmények megtekintése a böngészőben, amely megvalósítja az MSE

A Windows Media player inicializálása a csupán néhány sornyi JavaScript-kód olyan hajthatók végre. Dash.js használ, akkor valóban, amely egyszerű MPEG-DASH videó beágyazása a webböngésző-alapú alkalmazásokat.

## <a name="creating-the-html-page"></a>A HTML-weblap létrehozása
Az első lépés az, hogy hozzon létre egy szabványos HTML tartalmazó oldalra a **videó** elem, a fájl basicPlayer.html, a következő példa szemlélteti:

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

## <a name="adding-the-dashjs-player"></a>A DASH.js Player hozzáadása
A dash.js hivatkozás végrehajtása hozzáadni az alkalmazáshoz, az 1.0-ás kiadásba dash.js projekt dash.all.js a fájl adása kell. Ez az alkalmazás a JavaScript mappában mentéséhez. Ezt a fájlt egy olyan kényelmi fájl együtt az összes szükséges dash.js kód lekéri egyetlen fájlba. Ha tekintse meg a dash.js tárház körül, akkor az egyes fájlok található, tesztelni kódot, és még sok más, de ha összes szeretné van használja dash.js, az dash.all.js fájl nem találja.

Az alkalmazások a dash.js player hozzáadásához basicPlayer.html központi szakaszába parancsfájl címke hozzáadása:

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Ezután hozzon létre egy működnek, mint a Windows Media player inicializálni az oldal betöltésekor. Vegye fel a következő parancsfájlt a sort, amelyben dash.all.js betöltése után:

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

Ez a funkció először létrehoz egy DashContext. Ez az alkalmazás egy adott futásidejű környezet konfigurálására szolgál. Technikai szempontból meghatározza az osztályokat a függőségi injektálási keretrendszer kell használni, amikor az alkalmazás létrehozása. A legtöbb esetben Dash.di.DashContext használja.

A következő példányosítható dash.js keretrendszer, Media Player elsődleges osztály. Ez az osztály tartalmazza a legfontosabb módszerek, például a szükséges lejátszásához és szüneteltetése, kezeli a videó elem kapcsolattal is kezeli az adathordozó bemutató leírása (MPD) fájlt, amely lejátszható a videó ismerteti a értelmezését.

A Media Player osztály startup() funkciót, hogy a Windows Media player készen áll a videó lejátszása nevezik. Többek között a funkció biztosítja, hogy a szükséges osztályokat (a környezet által meghatározott) lett betöltve. Ha készen áll a Windows Media player, csatolhat a videó elem a attachView() függvény használatával. Ez lehetővé teszi a Media Player a video-adatfolyamot behelyezése elem, és szükség szerint lejátszás is szabályozhatja.

Így az tudni fogja, kapcsolatos a videó lejátszása várható a Media Player átadása a MPD fájl URL-CÍMÉT. Az imént létrehozott setupVideo() függvényt kell hajtható végre, ha az oldal teljes mértékben be van töltve. Ezt úgy teheti meg a törzs elem betöltésre. Módosítsa a <body> elemet:

    <body onload="setupVideo()">

Végezetül be a videó elem stíluslappal méretének. Adaptív adatfolyam-továbbítási környezetben ez különösen fontos, mert a videó lejátszását méretét, a lejátszás alkalmazkodik a változó hálózati körülmények módosíthatja. Ez egyszerű bemutató a egyszerűen kényszerítése videó elemként 80 %-át a rendelkezésre álló böngészőablakban adja hozzá a következő CSS a head részt a lap:

    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

## <a name="playing-a-video"></a>A videó lejátszása
A videó lejátszása, irányítsa a böngészőt a basicPlayback.html fájlt, és a videólejátszó jelenik meg a lejátszás gombra.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Videolejátszó alkalmazások fejlesztése](media-services-develop-video-players.md)

[GitHub-tárházban dash.js](https://github.com/Dash-Industry-Forum/dash.js) 


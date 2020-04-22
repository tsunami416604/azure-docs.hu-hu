---
title: Beépülő modulok írása az Azure Media Player alkalmazáshoz
description: Megtudhatja, hogyan írhat bebővítményt az Azure Media Player javascripttel
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727111"
---
# <a name="writing-plugins-for-azure-media-player"></a>Beépülő modulok írása az Azure Media Player alkalmazáshoz #

A plugin JavaScript írt, hogy kiterjesszék vagy fokozzák a játékos. Beépülő modulokat írhat, amelyek megváltoztatják az Azure Media Player megjelenését, funkcionalitását, vagy akár felülettel is rendelkeznek más szolgáltatásokkal. Ezt két egyszerű lépésben teheti meg:

## <a name="step-1"></a>1. lépés ##

Írja be a JavaScript egy függvény, mint így:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

A kódot közvetlenül a HTML-lapba írhatja címkéken belül `<script>` vagy egy külső JavaScript fájlban. Ha az utóbbit teszi, az AMP-parancsfájl `<head>` *után* mindenképpen szerepeljenek a JavaScript-fájl a HTML-oldalon.

Példa:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>2. lépés ##
Inicializálja a plugin javascript két módon:

1. módszer:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

2. módszer:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Plugin lehetőségek nem szükségesek, köztük csak lehetővé teszi a fejlesztők segítségével a plugin beállítani a viselkedését, anélkül, hogy változtatni a forráskódot.

Inspirációés több példa létrehozása plugin vessen egy pillantást a [galéria](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Plugin kód dinamikusan megváltoztatja elemeket a DOM élettartama alatt a néző játékos tapasztalat, soha nem teszi állandó változások a játékos forráskódját. Ez az, ahol a megértés a böngésző fejlesztői eszközök jól jön. Ha például meg szeretné változtatni egy elem megjelenését a lejátszóban, megtalálhatja annak HTML-elemét az osztály neve alapján, majd onnan hozzáadhat vagy módosíthat attribútumokat. Itt egy nagyszerű forrás a [HTML-attribútumok megváltoztatásáról.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Integrált bővítmények ###

 Jelenleg két plugin sült AMP: az [idő-tip](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) és [gyorsbillentyűk](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Ezek a dugó eredetileg úgy fejlesztették ki, hogy moduláris dugó a játékos, de most már szerepel a játékos forráskódját.

### <a name="plugin-gallery"></a>Plugin Galéria ###

A [plugin galéria](http//:aka.ms/ampplugins) több plugin, hogy a közösség már hozzájárult a funkciók, mint a time-line markerek, zoom, analitika és így tovább. Az oldal hozzáférést biztosít a dugó és utasításokat, hogyan kell beállítani, valamint egy demo, amely megmutatja a plugin akcióban. Ha létrehoz egy jó plugin, hogy úgy gondolja, tartalmaznia kell a galériában, nyugodtan küldje el, így tudjuk ellenőrizni.

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)
---
title: Beépülő modulok írása Azure Media Playerhoz
description: Megtudhatja, hogyan írhat egy beépülő modult Azure Media Player a JavaScript használatával
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727111"
---
# <a name="writing-plugins-for-azure-media-player"></a>Beépülő modulok írása Azure Media Playerhoz #

A beépülő modul JavaScriptet írt a lejátszó kiterjesztéséhez vagy bővítéséhez. Írhat olyan beépülő modulokat, amelyek megváltoztatják Azure Media Player megjelenését, funkcionalitását, vagy akár más szolgáltatásokkal is. Ezt két egyszerű lépésben teheti meg:

## <a name="step-1"></a>1. lépés ##

Írja be a JavaScriptet egy olyan függvénybe, amely a következőképpen néz ki:

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

A kódot közvetlenül a HTML-lapba is megírhatja `<script>` a címkéken belül, vagy egy külső JavaScript-fájlban. Ha ezt teszi, ügyeljen arra, hogy a JavaScript-fájlt a HTML- `<head>` oldalán az amp parancsfájl *után* is tartalmazza.

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
Inicializálja a beépülő modult a JavaScripttel kétféleképpen:

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

A beépülő modul beállításai nem szükségesek, beleértve azokat is, amelyek lehetővé teszik, hogy a beépülő modul a forráskód módosítása nélkül konfigurálja a saját viselkedését.

A beépülő [modulok](azure-media-player-plugin-gallery.md) létrehozásával kapcsolatos további példákért tekintse meg a katalógust

>[!NOTE]
> A beépülő modul programkódja dinamikusan módosítja a DOM elemeit a megtekintő lejátszó élményének élettartama során, soha nem teszi végleges módosításokat a lejátszó forráskódjában. Ebben az esetben a böngésző fejlesztői eszközeinek ismerete hasznos. Ha például módosítani szeretné egy elem megjelenését a lejátszóban, megkeresheti a HTML-elemét az osztály neve alapján, majd ott is hozzáadhat vagy módosíthat attribútumokat. Íme egy nagyszerű erőforrás a [HTML-attribútumok módosításához.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Beépített beépülő modulok ###

 Jelenleg két beépülő modul található a AMP: The [Time-tipp](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) és a [Gyorsbillentyűk](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)között. Ezek a beépülő modulok eredetileg moduláris beépülő modulok lettek kifejlesztve a lejátszóhoz, de most már beletartoznak a lejátszó forráskódba.

### <a name="plugin-gallery"></a>Beépülő modul gyűjteménye ###

A [bővítmények](http//:aka.ms/ampplugins) katalógusában számos beépülő modul található, amelyekkel a Közösség már hozzájárult olyan funkciókhoz, mint az idővonal-jelölők, a nagyítás, az elemzés és egyebek. A lap hozzáférést biztosít a beépülő modulhoz, valamint útmutatást nyújt a beállításához, valamint egy bemutatót, amely bemutatja a beépülő modult működés közben. Ha olyan remek beépülő modult hoz létre, amelyet a katalógusba szeretne foglalni, nyugodtan elküldheti, hogy bejelentkezzen.

## <a name="next-steps"></a>További lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)
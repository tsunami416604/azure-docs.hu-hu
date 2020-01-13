---
title: Rajzolási eszközök modulja | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a rajzolási beállításokat a Microsoft Azure Maps web SDK használatával
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 2f23d4d7962fc4a01ac2f9d20dc834bcd2f08be5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910588"
---
# <a name="use-the-drawing-tools-module"></a>A Rajzeszközök modul használata

A Azure Maps web SDK egy *Rajzeszközök modult*biztosít. Ez a modul megkönnyíti a Térkép alakzatainak megrajzolását és szerkesztését egy bemeneti eszköz, például egy egér érintési képernyő használatával. A modul alaposztálya a [Konfigurációkezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) , és biztosítja a Térkép alakzatainak rajzolásához és szerkesztéséhez szükséges összes képességet. A Rajzfigyelő közvetlenül és az egyéni eszköztár felhasználói felületén is használható, vagy használhatja a beépített [rajzolási eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) osztályt. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>A rajzolási eszközök modul betöltése egy weblapon

1. Hozzon létre egy új HTML-fájlt, és [a szokásos módon implementálja a térképet](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Töltse be a Azure Maps rajzolási eszközök modulját. Kétféleképpen is betöltheti:
    - Használja az Azure Maps Services modul globálisan üzemeltetett, Azure Content Delivery Network verzióját. Adja hozzá a JavaScript és a CSS stíluslap hivatkozását a fájl `<head>` elemében:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.js"></script>
        ```

    - Azt is megteheti, hogy helyileg betölti a Azure Maps web SDK forráskódhoz tartozó Rajzeszközök modult az [Azure-Maps-Draw-Tools](https://www.npmjs.com/package/azure-maps-drawing-tools) NPM csomag használatával, majd az alkalmazással üzemelteti. Ez a csomag írógéppel kapcsolatos definíciókat is tartalmaz. Használja ezt a parancsot:
    
        > **NPM telepítése az Azure-Maps-Draw-Tools**
    
        Ezután adjon hozzá egy hivatkozást a JavaScript és a CSS stíluslaphoz a fájl `<head>` elemében:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>A rajzolási kezelő közvetlen használata

Most, hogy a Rajzeszközök modul be lett töltve az alkalmazásba, a rajzolási és szerkesztési képességek engedélyezéséhez használhatja a [rajz-kezelőt](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) a térképen. Megadhatja, hogy a rendszer hogyan hozza létre a rajzolási beállításokat, vagy az `drawingManager.setOptions()` függvényt használja.

### <a name="set-the-drawing-mode"></a>Rajzolási mód beállítása

A következő kód létrehoz egy példányt a rajzolás-kezelőben, és beállítja a rajzolási **mód** beállítást. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Az alábbi kód egy teljes körű futtatási példát mutat be, amely bemutatja, hogyan állítható be a rajzolási mód a rajzoló kezelőben. Kattintson a térképre a sokszög rajzolásának megkezdéséhez.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög rajzolása" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>sokszög rajzolásával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>A beavatkozás típusának beállítása

A rajzolási kezelő három különböző módszert támogat a térképekkel való interakciók rajzolásához.

* `click` – a koordinátákat az egér vagy érintés gombra kattintva adja hozzá a rendszer.
* `freehand ` – a koordinátákat akkor adja hozzá a rendszer, ha az egér vagy a érintés a térképen van húzva. 
* `hybrid` – a koordinátákat akkor adja hozzá a rendszer, ha az egér vagy a érintés kattint.

A következő kód lehetővé teszi a sokszög rajzolási módját, és beállítja a rajzolási interakció típusát, amelyet a projektmenedzsernek be kell tartania `freehand`. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Az alábbi kódrészlet olyan funkciót valósít meg, amely lehetővé teszi, hogy egy sokszöget szabadon rajzoljon a térképen, miközben lenyomva tartja a bal oldali egérgombot, és a körülötte lévő egeret húzza. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Szabad oldali rajzolás" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollat a <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>szabad lapokon</a> .
</iframe>


### <a name="customizing-drawing-options"></a>Rajzolási beállítások testreszabása

Az előző példák azt mutatták be, hogyan lehet testre szabni a rajzolási beállításokat a rajzobjektum példányának összeállítása során. A `drawingManager.setOptions()` függvény használatával is megadhatja a rajzolási beállításokat. Az alábbi eszköz segítségével kipróbálhatja a rajzolási beállítások testreszabását a setOptions függvénnyel.

<br/>

<iframe height="685" title="A rajzolási kezelő testreszabása" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>a tollat</a> a <a href='https://codepen.io'>CodePen</a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>).
</iframe>


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan használhatja a rajzolási eszközök modul további funkcióit:

> [!div class="nextstepaction"]
> [Rajzolási eszköztár hozzáadása](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Alakzatadatok beolvasása](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagálás a rajzolási eseményekre](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interakció típusa és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Rajzolási kezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzolási eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

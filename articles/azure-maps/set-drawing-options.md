---
title: Rajzeszközök modul | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a rajzolási beállítások adatait a Microsoft Azure Maps Web SDK használatával
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334316"
---
# <a name="use-the-drawing-tools-module"></a>A Rajzeszközök modul használata

Az Azure Maps Web SDK *egy rajzeszköz-modult*biztosít. Ez a modul megkönnyíti az alakzatok rajzolását és szerkesztését a térképen egy beviteli eszközzel, például egérrel vagy érintőképernyővel. Ennek a modulnak az alaposztálya a [rajzkezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). A rajzkezelő biztosítja az alakzatok rajzolásához és szerkesztéséhez szükséges összes lehetőséget a térképen. Közvetlenül használható, és integrálva van egy egyéni eszköztár felhasználói felületével. Használhatja a beépített [rajzeszköztár-osztályt](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) is. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>A rajzeszközök modul betöltése egy weblapon

1. Hozzon létre egy új HTML-fájlt, és [valósítsa meg a térképet a szokásos módon](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Töltse be az Azure Maps rajzeszközök modul. Kétféleképpen töltheti be:
    - Használja az Azure Maps-szolgáltatások modul globálisan üzemeltetett, Azure Content Delivery Network verzióját. Hivatkozás hozzáadása a JavaScript és CSS `<head>` stíluslaphoz a fájl elemében:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Vagy betöltheti a rajzeszközök modult az Azure Maps Web SDK forráskód helyileg az [azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm csomag használatával, majd az alkalmazással üzemeltetheti azt. Ez a csomag TypeScript-definíciókat is tartalmaz. Használja ezt a parancsot:
    
        > **npm telepítés azure-maps-rajz-eszközök**
    
        Ezután adjon hozzá egy hivatkozást a JavaScript `<head>` és CSS stíluslap az elem a fájl:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>A rajzkezelő közvetlen használata

Miután a rajzeszközök modulbe betöltődik az alkalmazásba, a [rajzkezelővel](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)engedélyezheti a rajzolási és szerkesztési lehetőségeket. Megadhatja a rajzkezelő beállításait a példányelkészítés közben, `drawingManager.setOptions()` vagy használhatja a függvényt.

### <a name="set-the-drawing-mode"></a>A rajzolási mód beállítása

A következő kód létrehozza a rajzkezelő egy példányát, és beállítja a **rajzolási mód** beállítást. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Az alábbi kód egy teljes futó példa arra, hogyan kell beállítani a rajzkezelő rajzmódját. Kattintson a térképre a sokszög rajzolásának megkezdéséhez.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög rajzolása" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>rajzolja meg a sokszöget</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Kapcsolatitevékenység típusának beállítása

A rajzkezelő három különböző módot támogat a térképpel való interakcióra az alakzatok rajzolása érdekében.

* `click`- Koordináták kerülnek, ha az egér vagy érintés kattintott.
* `freehand `- Koordináták kerülnek, ha az egér vagy érintés húzta a térképen. 
* `hybrid`- Koordináták kerülnek, ha az egér vagy érintés kattintott, vagy húzta.

A következő kód lehetővé teszi a sokszögrajzolás módot, és beállítja a rajzkezelő által betartandó `freehand`rajzi művelet típusát. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Ez a kódminta a sokszög rajzolásának funkcióját valósítja meg a térképen. Csak tartsa lenyomva a bal egérgombot, és húzza körbe, szabadon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Szabadkézi rajzolás" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>szabadkézi tollrajzát</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>


### <a name="customizing-drawing-options"></a>Rajzbeállítások testreszabása

Az előző példák bemutatták, hogyan szabhatja testre a rajzbeállításokat a Rajzkezelő példányaközben. A Rajzkezelő beállításait a `drawingManager.setOptions()` funkció val is megadhatja. Az alábbiakban egy eszköz, hogy teszteljék testreszabása az összes lehetőséget a rajz kezelő segítségével setOptions funkciót.

<br/>

<iframe height="685" title="Rajzkezelő testreszabása" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>alakzatadatokbe beszerezni</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>További lépések

További információ a rajzeszközök modul további funkcióinak használatáról:

> [!div class="nextstepaction"]
> [Rajzolási eszköztár hozzáadása](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Formázott adatok lekérése](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Rajzolási eseményekre való reakció](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Az interakciók típusai és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Rajzkezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzolás eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

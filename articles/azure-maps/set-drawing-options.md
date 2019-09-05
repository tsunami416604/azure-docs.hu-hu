---
title: Rajzolási beállítások megadása Azure Mapsban | Microsoft Docs
description: A rajzolási beállításokkal kapcsolatos adatértékek beállítása Azure Maps web SDK használatával
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309831"
---
# <a name="set-drawing-options"></a>Rajzolási beállítások megadása

Ez a cikk bemutatja, hogyan változtathatja meg a felhasználói élményt a [projektmenedzser](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) különböző lehetőségei. A **drawingManager. setOptions ()** függvény használatával megadhatja, hogy mely beállítások legyenek elérhetők a rajzolási kezelőhöz.


## <a name="set-drawing-mode"></a>Rajzolási mód beállítása

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
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>sokszög rajzolásával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="set-interaction-type"></a>Interakció típusának beállítása

A következő kód azt a rajzolási interakció típusát állítja be, amelyet a projektmenedzsernek be kell tartania. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Az alábbi kód minta olyan funkciót valósít meg, amely lehetővé teszi, hogy a Térkép szabadon rajzolható legyen, miközben lenyomja a bal egérgombot, és húzza a körülötte lévőre. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Szabad oldali rajzolás" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'></a> a tollat a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) függvényt a <a href='https://codepen.io'>CodePen</a>-on.
</iframe>


## <a name="customizing-drawing-options"></a>Rajzolási beállítások testreszabása

Az előző példák azt mutatták be, hogyan lehet testre szabni a rajzolási beállításokat a rajzobjektum példányának összeállítása során. A **drawingManager. setOptions ()** függvény használatával is megadhatja a rajzolási beállításokat. Az alábbi eszköz segítségével kipróbálhatja a rajzolási beállítások testreszabását a setOptions függvénnyel.

<br/>

<iframe height="685" title="A rajzolási kezelő testreszabása" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>a tollat</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Rajzolási kezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzolási eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

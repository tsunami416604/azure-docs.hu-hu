---
title: Rajzolási eszköztár hozzáadása a Azure Mapshoz | Microsoft Docs
description: Rajzolási eszköztár hozzáadása térképhez Azure Maps web SDK használatával
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ed16d8797e541ee474b4f52f3d4379721922a734
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432873"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Rajzolási eszközök eszköztárának hozzáadása térképhez

Ez a cikk bemutatja, hogyan használhatja a rajzolási eszközök modult, és hogyan jelenítheti meg a rajzolás eszköztárat a térképen. A [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) vezérlő hozzáadja a rajzolás eszköztárat a térképen. Megtudhatja, hogyan hozhat létre térképeket csak egy és minden rajzolási eszközzel, és hogyan szabhatja testre a rajzolási alakzatok megjelenítését a rajzolási kezelőben.

## <a name="add-drawing-toolbar"></a>Rajzolási eszköztár hozzáadása

A következő kód létrehozza a rajzolási kezelő egy példányát, és megjeleníti az eszköztárat a térképen.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Alább látható a fenti funkciók teljes futtatására szolgáló kód mintája:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolási eszköztár hozzáadása" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>Rajzolás eszköztár hozzáadásával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Megjelenő eszköztár beállításainak korlátozása

A következő kód létrehoz egy példányt a rajzolás-kezelőből, és megjeleníti az eszköztárat, amely csak egy sokszög alakú rajzolási eszközzel jelenik meg a térképen. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Alább látható a fenti funkciók teljes futtatására szolgáló kód mintája:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög rajzolási eszköz hozzáadása" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>rajzolási eszköz hozzáadása</a> a <a href='https://codepen.io'>CodePen</a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) című témakört.
</iframe>


## <a name="change-drawing-rendering-style"></a>Rajzolási renderelési stílus módosítása

A következő kód beolvassa a rajzolási rétegeket a projektmenedzsertől, és módosítja a rajzolási stílus módosítására vonatkozó beállításokat. Ebben az esetben a pontok kék jelölő ikonnal jelennek meg, a sorok piros és négy képpont szélesek lesznek, a sokszögek zöld kitöltési színnel és narancssárga körvonalsal lesznek ellátva.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Alább látható a fenti funkciók teljes futtatására szolgáló kód mintája:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolási renderelési stílus módosítása" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>módosítása rajzolási stílusát</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan használhatja a rajzolási eszközök modul további funkcióit:

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
> [Rajzolási eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzolási kezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
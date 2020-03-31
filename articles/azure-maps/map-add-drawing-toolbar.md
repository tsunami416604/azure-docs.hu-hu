---
title: Rajzeszköztár hozzáadása térképhez | Microsoft Azure Maps
description: Rajzeszköztár hozzáadása térképhez az Azure Maps Web SDK használatával
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334501"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Rajzeszközök eszköztárának hozzáadása térképhez

Ez a cikk bemutatja, hogyan használhatja a Rajzeszközök modult, és hogyan jelenítheti meg a rajzeszköztárat a térképen. A [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) vezérlő hozzáadja a rajz eszköztárat a térképhez. Megtudhatja, hogyan hozhat létre térképeket csak egy és az összes rajzeszközzel, és hogyan szabhatja testre a rajzalakzatok renderelését a rajzkezelőben.

## <a name="add-drawing-toolbar"></a>Rajzolási eszköztár hozzáadása

A következő kód létrehozza a rajzkezelő egy példányát, és megjeleníti az eszköztárat a térképen.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolási eszköztár hozzáadása" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>( Toll <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>hozzáadása rajzeszköztárát</a> ( ) a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Megjelenik az eszköztár beállításainak korlátozása

A következő kód létrehozza a rajzkezelő egy példányát, és csak egy sokszögrajzoló eszközzel jeleníti meg az eszköztárat a térképen. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszögrajzoló eszköz hozzáadása" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>hozzáadása sokszögrajzot az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által a <a href='https://codepen.io'>CodePen oldalon.</a>
</iframe>


## <a name="change-drawing-rendering-style"></a>Rajzmegjelenítési stílus módosítása

A rajzolt alakzatok stílusa testreszabható a rajzkezelő mögöttes rétegeinek beolvasásával a `drawingManager.getLayers()` funkció használatával, majd az egyes rétegek beállításainak beállításával. Az alakzatok szerkesztésekor a koordinátákhoz megjelenő húzási fogópontok HTML-jelölők. A fogópontok stílusa testreszabható, ha a HTML-jelölő beállításokat a `dragHandleStyle` rajzkezelő és `secondaryDragHandleStyle` -beállítások között adja át.  

A következő kód lekéri a renderelési rétegeket a rajzkezelőtől, és módosítja a rajzmegjelenítési stílus módosításának beállításait. Ebben az esetben a pontok egy kék jelölő ikonnal jelennek meg. A vonalak pirosak és négy képpont szélesek lesznek. A sokszögek zöld kitöltőszínnel és narancssárga körvonaljal rendelkeznek. Ezután a fogópontok stílusát négyzetalakú ikonokká változtatja. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzmegjelenítési stílus módosítása" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>módosítása rajzmegjelenítési stílust</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>


## <a name="next-steps"></a>További lépések

További információ a rajzeszközök modul további funkcióinak használatáról:

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
> [Rajzolás eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzkezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

---
title: Alakzatok adatainak beolvasása a rajzolási kezelőből Azure Mapsban | Microsoft Docs
description: Alakzatadatok beszerzése Azure Maps web SDK használatával
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e7706fba1efad1bd0ce7110e129dcf113689af9a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309747"
---
# <a name="get-shape-data"></a>Alakzatadatok beolvasása

Ebből a cikkből megtudhatja, hogyan kérhet le a térképen rajzolt alakzatokat a [Draw Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) **drawingManager. getSource ()** függvény használatával. Különböző forgatókönyvek lehetnek, amelyekben érdemes kinyerni egy rajzolt alakzat geojson-mennyiségét, és azt máshol is használni.  


## <a name="get-data-from-drawn-shape"></a>Adatok lekérése rajzolt alakzatból

A következő függvény lekéri a rajzolt alakzat forrásául szolgáló adatokat, és megjeleníti a képernyőn. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Az alábbiakban a teljes futó kód minta látható, ahol rajzolhat egy alakzatot a funkció teszteléséhez:

<br/>

<iframe height="686" title="Alakzatadatok beolvasása" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>a tollat</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Rajzolási kezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzolási eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

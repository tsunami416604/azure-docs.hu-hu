---
title: Adatok beolvasása a térképen lévő alakzatokból | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan szerezheti be az adatalakzatokat térképeken a Microsoft Azure Maps web SDK használatával.
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334415"
---
# <a name="get-shape-data"></a>Formázott adatok lekérése

Ez a cikk bemutatja, hogyan kérheti le a térképre rajzolt alakzatok mennyiségét. A **drawingManager. getSource ()** függvényt használjuk a [rajzolási kezelőn](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--)belül. A rajzolt alakzatok geojson-adatok kinyerése és a máshol való használata különböző forgatókönyveket mutat be.  


## <a name="get-data-from-drawn-shape"></a>Adatok lekérése rajzolt alakzatból

A következő függvény beolvassa a rajzolt alakzat forrásául szolgáló adatokat, és megjeleníti a képernyőre. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Az alábbiakban a teljes futó kód minta látható, ahol rajzolhat egy alakzatot a funkció teszteléséhez:

<br/>

<iframe height="686" title="Formázott adatok lekérése" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>a tollat</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan használhatja a rajzolási eszközök modul további funkcióit:

> [!div class="nextstepaction"]
> [Rajzolási eseményekre való reakció](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Az interakciók típusai és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Rajzolási kezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzolási eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

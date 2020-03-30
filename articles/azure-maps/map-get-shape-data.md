---
title: Adatok beszereznie alakzatokat a térképen | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan kaphat alakzatadatokat a Térképen a Microsoft Azure Maps Web SDK használatával.
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334415"
---
# <a name="get-shape-data"></a>Formázott adatok lekérése

Ez a cikk bemutatja, hogyan szerezheti be a térképen rajzolt alakzatok adatait. A **drawingManager.getSource()** függvényt használjuk a [rajzkezelőn](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--)belül. Vannak különböző forgatókönyvek, ha egy rajzolt alakzat geojson adatait szeretné kinyerni, és máshol használni.  


## <a name="get-data-from-drawn-shape"></a>Adatok beszereznie rajzolt alakzatból

A következő függvény leveszi a rajzolt alakzat forrásadatait, és kiírja azokat a képernyőre. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Az alábbiakban a teljes futókód minta, ahol rajzolhat egy alakzatot, hogy teszteljék a funkciót:

<br/>

<iframe height="686" title="Formázott adatok lekérése" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>alakzatadatokbe beszerezni</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>További lépések

További információ a rajzeszközök modul további funkcióinak használatáról:

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

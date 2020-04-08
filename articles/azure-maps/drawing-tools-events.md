---
title: Rajzeszköztár hozzáadása térképhez | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan vehet fel rajzeszköztárat egy térképhez a Microsoft Azure Maps Web SDK használatával
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d8509af7829910bdda8bba3d63553e83626fe784
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804673"
---
# <a name="drawing-tool-events"></a>Rajzeszköz-események

Amikor rajzeszközöket használ a térképen, hasznos reagálni bizonyos eseményekre, amikor a felhasználó a térképre rajzol. Ez a táblázat az `DrawingManager` osztály által támogatott összes eseményt sorolja fel.

| Esemény | Leírás |
|-------|-------------|
| `drawingchanged` | Akkor aktiválódik, ha egy alakzat bármely koordinátája hozzá lett adva vagy módosult. | 
| `drawingchanging` | Akkor aktiválódik, ha egy alakzat előnézeti koordinátája megjelenik. Az esemény például többször is meggyullad, amikor egy koordinátát húz. | 
| `drawingcomplete` | Akkor aktiválódik, ha egy alakzat kirajzolása vagy szerkesztési módból való kiváltása befejeződött. |
| `drawingmodechanged` | A rajzolási mód váltásakor aktiválódik. Az új rajzolási mód átkerül az eseménykezelőbe. |
| `drawingstarted` | Akkor aktiválódik, amikor a felhasználó elkezd rajzolni egy alakzatot, vagy szerkesztési módba helyez egy alakzatot.  |

A következő kód bemutatja, hogyan működnek a Rajzeszközök modul eseményei. Rajzolj alakzatokat a térképre, és nézd, ahogy az események tűz.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzeszközök eseményei" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>( ) <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>Tollrajz eszközök eseményeit</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

<br/>

## <a name="examples"></a>Példák

Lássunk néhány gyakori forgatókönyvet, amelyek a rajzeszközök eseményeit használják.

### <a name="select-points-in-polygon-area"></a>Pontok kijelölése a sokszögterületen

Ez a kód bemutatja, hogyan figyelhet egy felhasználói rajzalakzatot. Ebben a példában a kód sokszögek, téglalapok és körök alakzatait figyeli. Ezután meghatározza, hogy a térképen mely adatpontok vannak a rajzolt területen belül. Az `drawingcomplete` esemény a kijelölési logika aktiválására szolgál. A kiválasztott logikában a kód végighalad a térképen lévő összes adatponton. Ellenőrzi, hogy van-e metszéspontja a pontnak és a húzott alakzat nak. Ez a példa a nyílt forráskódú [Turf.js könyvtárat](https://turfjs.org/) használja egy térbeli metszéspont számításának elvégzéséhez.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adatok kijelölése a rajzolt sokszögterületen" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Lásd a Toll <a href='https://codepen.io/azuremaps/pen/XWJdeja'>kiválasztása adatokat rajzolt sokszög</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>területen az Azure Maps ( ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rajzolás és keresés sokszögterületen

Ez a kód az alakzat területén belüli érdeklődési pontokat keresi, miután a felhasználó befejezte az alakzat rajzolását. A kód módosításához és végrehajtásához kattintson a keret jobb felső sarkában található "Szerkesztés a kódtollon" gombra. Az `drawingcomplete` esemény a keresési logika aktiválására szolgál. Ha a felhasználó téglalapot vagy sokszöget rajzol, a program a geometrián belül keres. Ha egy kört rajzol, a sugár és a középső pozíció segítségével történik egy érdekes pont keresése. Az `drawingmodechanged` esemény határozza meg, hogy a felhasználó mikor vált át a rajzmódra, és ez az esemény törli a vásznat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolás és keresés sokszögterületen" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Tollrajzolást és a keresést a sokszögterületen</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Mérőeszköz létrehozása

Az alábbi kód bemutatja, hogyan használhatók a rajzesemények egy mérőeszköz létrehozásához. A `drawingchanging` segítségével figyeli az alakzatot, ahogy rajzolják. Ahogy a felhasználó mozgatja az egeret, a program kiszámítja az alakzat méreteit. Az `drawingcomplete` esemény az alakzat végső kiszámítására szolgál a rajzolás után. Az `drawingmodechanged` esemény annak meghatározására szolgál, hogy a felhasználó mikor vált rajzmódba. Az esemény `drawingmodechanged` törli a rajzvásznat, és törli a régi mérési információkat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mérőeszköz" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>( ) <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Tollmérő eszközét</a> ( ) a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

<br/>

## <a name="next-steps"></a>További lépések

További információ a rajzeszközök modul további funkcióinak használatáról:

> [!div class="nextstepaction"]
> [Formázott adatok lekérése](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Az interakciók típusai és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a Szolgáltatások modulról:

> [!div class="nextstepaction"]
> [Szolgáltatások modul](how-to-use-services-module.md)

Nézze meg több kódminták:

> [!div class="nextstepaction"]
> [Kódmintalap](https://aka.ms/AzureMapsSamples)

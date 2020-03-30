---
title: Vezérlők hozzáadása térképhez | Microsoft Azure Maps
description: Nagyításvezérlés, hangmagasság-vezérlés, elforgatás vezérlése és stílusválasztó hozzáadása a térképhez a Microsoft Azure Maps ben.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334563"
---
# <a name="add-controls-to-a-map"></a>Vezérlők hozzáadása térképhez

Ez a cikk bemutatja, hogyan adhat vezérlőket a térképhez. Azt is megtudhatja, hogyan hozhat létre térképet az összes vezérlővel és egy [stílusválasztóval.](https://docs.microsoft.com/azure/azure-maps/choose-map-style)

## <a name="add-zoom-control"></a>Nagyításvezérlő hozzáadása

A nagyításvezérlő gombokat ad hozzá a térkép nagyításához és kicsinyítéséhez. A következő kódminta létrehozza a [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) osztály egy példányát, és hozzáadja a térkép jobb alsó sarkát.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható.

<br/>

<iframe height='500' scrolling='no' title='Nagyításvezérlő hozzáadása' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd: A Toll <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>nagyítási</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>vezérlő hozzáadása az Azure Maps ( ) segítségével a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

## <a name="add-pitch-control"></a>Hangmagasság-vezérlés hozzáadása

A hangmagasság-vezérlő gombokat ad hozzá a hangmagasság megdöntéséhez a horizonthoz viszonyított térképhez. A következő kódminta létrehozza a [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) osztály egy példányát. Hozzáadja a PitchControl-t a térkép jobb felső sarkához.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható.

<br/>

<iframe height='500' scrolling='no' title='Hangmagasság-vezérlő hozzáadása' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd: A Toll hozzáadása a<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>hangmagasság-vezérlő</a> az Azure Maps ( ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Iránytű vezérlő hozzáadása

Az iránytű vezérlő egy gombot ad hozzá a térkép elforgatáshoz. A következő kódminta létrehozza az [Iránytűvezérlő](/javascript/api/azure-maps-control/atlas.control.compasscontrol) osztály egy példányát, és hozzáadja a térkép bal alsó sarkát.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Az alábbiakban a fenti funkciók teljes futókód-mintája látható.

<br/>

<iframe height='500' scrolling='no' title='Forgatásvezérlő hozzáadása' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd: A <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Toll: Forgatásvezérlés</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>hozzáadása az Azure Maps ( ) segítségével a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

## <a name="a-map-with-all-controls"></a>Térkép az összes vezérlővel

Több vezérlő térhet be egy tömbbe, és egyszerre hozzáadható a térképhez, és a fejlesztés egyszerűsítése érdekében a térkép ugyanazon területén helyezkedhet el. A következő hozzáadja a szabványos navigációs vezérlőket a térképhez ezzel a megközelítéssel.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

A következő kódminta hozzáadja a nagyítási, iránytű-, hangmagasság- és stílusválasztó vezérlőket a térkép jobb felső sarkához. Figyelje meg, hogyan automatikusan verem. A vezérlőobjektumok sorrendje határozza meg a vezérlőobjektumok sorrendjét a térképen. A térképen lévő vezérlők sorrendjének módosításához módosíthatja azok sorrendjét a tömbben.

<br/>

<iframe height='500' scrolling='no' title='A térkép az összes vezérlővel' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A térképet</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>az Azure Maps ( ) által a <a href='https://codepen.io'>CodePen</a>webhelyen.
</iframe>

A stílusválasztó vezérlőt a [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) osztály határozza meg. A stílusválasztó vezérlő használatáról további információt a [Térképstílus kiválasztása című](choose-map-style.md)témakörben talál.

## <a name="customize-controls"></a>Vezérlők testreszabása

Itt van egy eszköz, hogy teszteljék a különböző lehetőségek testreszabására a vezérlők.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Navigációs vezérlési beállítások" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>( ) Pen <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>Navigation vezérlőbeállításait</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

Ha testre szabott navigációs vezérlőket szeretne létrehozni, hozzon létre egy osztályt, amely az `atlas.Control` osztályból terjed, vagy hozzon létre egy HTML-elemet, és helyezze azt a térképdiv fölé. Van ez a felhasználói `setCamera` felület vezérlő hívja a térképek funkciót mozgatni a térképet. 

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Iránytű vezérlése](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl (PitchControl)](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

A teljes kódot lásd a következő cikkekben:

> [!div class="nextstepaction"]
> [Gombostű hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Előugró ablak hozzáadása](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Buborékréteg hozzáadása](map-add-bubble-layer.md)


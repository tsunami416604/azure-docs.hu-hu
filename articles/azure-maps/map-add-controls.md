---
title: Vezérlőelemek hozzáadása térképhez | Microsoft Azure térképek
description: Nagyítás-vezérlés, pitch Control, a Control és a Style Picker hozzáadása Microsoft Azure Maps térképekhez.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334563"
---
# <a name="add-controls-to-a-map"></a>Vezérlőelemek hozzáadása térképhez

Ez a cikk bemutatja, hogyan adhat hozzá vezérlőket térképekhez. Azt is megtudhatja, hogyan hozhat létre térképet az összes vezérlővel és a [Style Picker](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Nagyítási vezérlő hozzáadása

A nagyítás-vezérlés gombokkal bővítheti a térképet és a nagyítást. A következő mintakód létrehozza a [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) osztály egy példányát, és hozzáadja a Térkép jobb alsó sarkához.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Nagyítási vezérlő hozzáadása' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>nagyítási vezérlőt</a> a <a href='https://codepen.io'>CodePen</a>-on való hozzáadását ismertető tollat.
</iframe>

## <a name="add-pitch-control"></a>Pitch vezérlőelem hozzáadása

A Pitch vezérlő gombok hozzáadásával megdöntheti, hogy a szurok a horizonthoz viszonyítva legyen-e leképezve. A következő mintakód a [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) osztály egy példányát hozza létre. Hozzáadja a PitchControl a Térkép jobb felső sarkához.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Pitch vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>a tollat a</a> CodePen-on<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( <a href='https://codepen.io'>CodePen</a>) használatával.
</iframe>

## <a name="add-compass-control"></a>Iránytű vezérlőelem hozzáadása

Az iránytű vezérlőelem egy gombot helyez el a Térkép elforgatásához. Az alábbi mintakód az [iránytű vezérlő](/javascript/api/azure-maps-control/atlas.control.compasscontrol) osztály egy példányát hozza létre, és hozzáadja a térkép bal alsó sarkához.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Forgatás vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>elforgatási vezérlő hozzáadásával</a> .
</iframe>

## <a name="a-map-with-all-controls"></a>Térkép az összes vezérlővel

Több vezérlő helyezhető egy tömbbe, és a térképhez mind egyszerre, mind a Térkép ugyanazon területén helyezhető el, hogy egyszerűbbé váljon a fejlesztés. A következő lépés a szabványos navigációs vezérlőket hozzáadja a térképhez ezzel a módszerrel.

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

A következő mintakód hozzáadja a nagyítás, az iránytű, a szurok és a stílus választó vezérlőket a Térkép jobb felső sarkához. Figyelje meg, hogy a rendszer hogyan automatikusan verembe. A parancsfájlban lévő vezérlőelem-objektumok sorrendje azt diktálja, hogy milyen sorrendben jelenjenek meg a térképen. Ha módosítani szeretné a vezérlőelemek sorrendjét a térképen, módosíthatja a sorrendjét a tömbben.

<br/>

<iframe height='500' scrolling='no' title='Térkép az összes vezérlővel' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a térképes tollat az <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>összes vezérlővel</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A Style Picker vezérlőt a [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) osztály határozza meg. A Style Picker vezérlő használatával kapcsolatos további információkért lásd: [Térkép stílusának kiválasztása](choose-map-style.md).

## <a name="customize-controls"></a>Vezérlők testreszabása

Itt található egy eszköz, amellyel kipróbálhatja a vezérlők testreszabásának különböző lehetőségeit.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Navigációs vezérlőelem beállításai" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>felügyelt tollas Navigálás beállításait</a> .
</iframe>

Ha testreszabott navigációs vezérlőket szeretne létrehozni, hozzon létre egy osztályt, amely `atlas.Control` kiterjeszti az osztályból, vagy hozzon létre egy HTML-elemet, és helyezze a Térkép div fölé. Ez a felhasználói felületi vezérlő hívja `setCamera` meg a Maps függvényt a Térkép áthelyezéséhez. 

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Iránytű vezérlő](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

A következő cikkekben talál teljes kódot:

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


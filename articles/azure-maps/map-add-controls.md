---
title: Vezérlőelemek hozzáadása térképhez | Microsoft Azure térképek
description: Nagyítás-vezérlés, pitch Control, a Control és a Style Picker hozzáadása Microsoft Azure Maps térképekhez.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6652ca30d9f38eb6184fce5f1ff5428d29c271a6
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911271"
---
# <a name="add-controls-to-a-map"></a>Vezérlőelemek hozzáadása térképhez

Ez a cikk bemutatja, hogyan adhat hozzá vezérlőket térképekhez. Azt is megtudhatja, hogyan hozhat létre egy térképet az összes vezérlővel és egy [Style választóval](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

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

<iframe height='500' scrolling='no' title='Nagyítási vezérlő hozzáadása' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>-on való <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Nagyítás-vezérlés hozzáadására</a> szolgáló tollat.
</iframe>

## <a name="add-pitch-control"></a>Pitch vezérlőelem hozzáadása

A Pitch vezérlő gombok hozzáadásával megdöntheti, hogy a szurok a horizonthoz viszonyítva legyen-e leképezve. A következő mintakód létrehozza a [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) osztály egy példányát, és hozzáadja a Térkép jobb felső sarkához.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Alább látható a fenti funkciók teljes futási kódjának mintája.

<br/>

<iframe height='500' scrolling='no' title='Pitch vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>hozzáadásával</a> a <a href='https://codepen.io'>CodePen</a>.
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

<iframe height='500' scrolling='no' title='Forgatás vezérlőelem hozzáadása' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>hozzáadásával</a> .
</iframe>

## <a name="a-map-with-all-controls"></a>Térkép az összes vezérlővel

A következő mintakód hozzáadja a Style Picker, a nagyítás, a szurok és az iránytű vezérlőket a Térkép jobb alsó sarkához. Figyelje meg, hogy a rendszer hogyan automatikusan verembe. A parancsfájlban lévő vezérlőelem-objektumok sorrendje azt diktálja, hogy milyen sorrendben jelenjenek meg a térképen. A térképen a vezérlőelemek sorrendjének módosításához megváltoztathatja a parancsfájlban található sorrendet.

<br/>

<iframe height='500' scrolling='no' title='Térkép az összes vezérlővel' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti az <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>összes vezérlőt tartalmazó térképet</a> .
</iframe>

A Style Picker vezérlőt a [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) osztály határozza meg. A Style Picker vezérlő használatával kapcsolatos további információkért lásd: [Térkép stílusának kiválasztása](choose-map-style.md).

## <a name="customize-controls"></a>Vezérlők testreszabása

Itt található egy eszköz, amellyel kipróbálhatja a vezérlők testreszabásának különböző lehetőségeit.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Navigációs vezérlőelem beállításai" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>navigációs vezérlő beállításait</a> .
</iframe>

Ha testreszabott navigációs vezérlőket szeretne létrehozni, hozzon létre egy osztályt, amely kiterjeszthető a `atlas.Control` osztályból, vagy hozzon létre egy HTML-elemet, és helyezze a Térkép div fölé. Ez a felhasználói felületi vezérlő hívja meg a Maps `setCamera` függvényt a Térkép áthelyezéséhez. 

## <a name="next-steps"></a>Következő lépések

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
> [PIN-kód hozzáadása](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Felugró ablak hozzáadása](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Vonal rétegének hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszög réteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [Buborék réteg hozzáadása](map-add-bubble-layer.md)


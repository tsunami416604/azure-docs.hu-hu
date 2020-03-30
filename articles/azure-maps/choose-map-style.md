---
title: A térkép stílusának módosítása az Azure Mapsben | Microsoft Azure Maps
description: Ebben a cikkben a Microsoft Azure Maps webes SDK-ban elérhető stílussal kapcsolatos funkciókról olvashat.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335689"
---
# <a name="change-the-style-of-the-map"></a>A térkép stílusának módosítása

A térkép számos különböző [stílusbeállítást](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) támogat, amelyek a térkép inicializálásakor vagy később a térképek `setStyle` funkció használatával állíthatók be. Ez a cikk bemutatja, hogyan használhatja ezeket a stílusbeállításokat a térképek megjelenésének testreszabásához. Ismerje meg, hogyan állíthat be stílust a térkép betöltésekor, és hogyan állíthat be új térképstílust a stílusválasztó vezérlővel.

## <a name="set-the-style-options"></a>A stílusbeállítások megadása 

A stílusbeállítások akkor adhatók át a térképnek, ha `setStyle` később inicializálják vagy frissítik a térképek funkcióval.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

A következő eszköz bemutatja, hogyan változnak a különböző stílusbeállítások a térkép renderelésének módjában. A 3D épületek megtekintéséhez nagyítson egy nagyobb város közelébe. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Térképstílus beállításai" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>( ) <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Tolltérkép stílusbeállításait</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="choose-a-base-map-style"></a>Alaptérképstílus kiválasztása

Az egyik leggyakoribb térképstílus-beállítás a stílusstílus megváltoztatására szolgál. Az [Azure Maps számos támogatott térképstílusa](supported-map-styles.md) elérhető a webes SDK-ban. 

### <a name="set-base-map-style-on-map-load"></a>Alaptérkép-stílus beállítása a térkép betöltésén


A térképstílus a térkép inicializálásakor a `style` beállítással adható meg. A következő kódban `style` a térkép opciója `grayscale_dark` inicializáláskor van beállítva.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='A stílus beállítása a térkép terhelésén' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Toll <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>beállítása a stílust a térkép terhelés</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Az alaptérkép stílusának frissítése

 A térképstílus a `setStyle` funkció valamerre és a `style` kívánt térképstílus beállításával frissíthető.

```javascript
map.setStyle({ style: 'satellite' });
```

A következő kódban a térképpéldány betöltése után `road` a `satellite` térképstílus a [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) függvény használatával frissül.

<br/>

<iframe height='500' scrolling='no' title='A stílus frissítése' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>frissítése a stílust az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>A stílusválasztó hozzáadása

A stílusválasztó vezérlő egy könnyen használható gombot biztosít úszó panellel, amelyet a végfelhasználó a térképstílus megváltoztatására használhat. A stílusválasztó két különböző elrendezési lehetőséggel rendelkezik. Alapértelmezés szerint a stílusválasztó `icons` az elrendezést használja, és az összes térképstílust vízszintes ikonsorként jeleníti meg. 

<center>

![Stílusválasztó ikonelrendezése](media/choose-map-style/style-picker-icon-layout.png)</center>

A második elrendezési `list` lehetőség neve, és megjeleníti a térképstílusok görgethető listáját.  

<center>

![Stílusválasztó lista elrendezése](media/choose-map-style/style-picker-list-layout.png)</center>


A következő kód bemutatja, hogyan hozhat létre egy példányt a stílusválasztó vezérlőből, és hogyan veheti fel a térkép jobb felső sarkába. A stílusválasztó sötét stílust használ, és a listaréteg segítségével néhány kijelölt térképstílust jelenít meg.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

A következő kód hozzáad egy stílusválasztó vezérlőt az alapértelmezett beállításokkal a térképhez, így a felhasználó könnyedén válthat a különböző térképstílusok között. A térképstílus takarásában a jobb felső sarokban lévő térképstílus-vezérlővel.

<br/>

<iframe height='500' scrolling='no' title='A stílusválasztó hozzáadása' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>hozzáadása a stílusválasztó az</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Alapértelmezés szerint az Azure Maps S0 tarifacsomagjának használatakor a stílusválasztó vezérlő felsorolja az összes rendelkezésre álló stílust. Ha csökkenteni szeretné a listában szereplő stílusok számát, adja át a listában megjeleníteni kívánt `mapStyle` stílusok egy tömbjét a stílusválasztó beállításának. Ha S1-et használ, és az összes rendelkezésre álló stílust meg szeretné jelenjenek, állítsa a `mapStyles` stílusválasztó beállítását a beállításra. `"all"`

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Stílusbeállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Vezérlők hozzáadása a térképekhez:

> [!div class="nextstepaction"]
> [Térképvezérlők hozzáadása](map-add-controls.md)

> [!div class="nextstepaction"]
> [Gombostű hozzáadása](map-add-pin.md)

---
title: A Térkép stílusának módosítása Azure Mapsban | Microsoft Azure térképek
description: Ebben a cikkben megismerheti a Microsoft Azure Maps web SDK-ban elérhető stílussal kapcsolatos funkciókat.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335689"
---
# <a name="change-the-style-of-the-map"></a>A térkép stílusának módosítása

A Térkép több különböző [stílust](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) is támogat, amelyek akkor állíthatók be, ha a Térkép inicializálása vagy későbbi használata a `setStyle` Maps függvénnyel történik. Ez a cikk bemutatja, hogyan használhatja ezeket a stílusokat a térképek megjelenésének testreszabásához. Megtudhatja, hogyan állíthat be egy stílust a Térkép betöltésekor, és megtudhatja, hogyan állíthat be új leképezési stílust a Style Picker vezérlőelem használatával.

## <a name="set-the-style-options"></a>A stílus beállításainak megadása 

A stílusra vonatkozó beállítások átadhatók a térképnek, amikor a rendszer inicializálja vagy később `setStyle` frissíti a Maps függvény használatával.

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

Az alábbi eszköz azt mutatja be, hogy a különböző stílusú beállítások Hogyan változnak a Térkép megjelenítésének módjával. Ha szeretné megtekinteni a 3D-s épületeket, a nagyítást egy nagyobb városhoz közelítheti meg. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Térkép stílusa beállításai" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Térkép stílusának beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Alapszintű Térkép stílusának kiválasztása

Az alapszintű Térkép stílusa az egyik leggyakoribb lehetőség a stílusának módosításához. [Azure Maps számos támogatott térképi stílusa](supported-map-styles.md) elérhető a web SDK-ban. 

### <a name="set-base-map-style-on-map-load"></a>Alapszintű Térkép stílusának beállítása a Térkép betöltéséhez


A Térkép stílusát a beállítás beállításával `style` lehet megadni a Térkép inicializálásakor. A következő kódban a Térkép beállítása `style` az inicializálás beállításra van beállítva `grayscale_dark` .

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='A stílus beállítása a Térkép betöltéséhez' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>-on a <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>stílus betöltésére vonatkozó beállítást</a> .
</iframe>

### <a name="update-the-base-map-style"></a>Az alapszintű Térkép stílusának frissítése

 A Térkép stílusa a `setStyle` függvény használatával frissíthető, és beállítható `style` a kívánt Térkép stílusa.

```javascript
map.setStyle({ style: 'satellite' });
```

A következő kódban a térképi példány betöltését követően a Térkép stílusa a [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) függvény `satellite` használatával `road` frissül.

<br/>

<iframe height='500' scrolling='no' title='A stílus frissítése' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>stílus frissítése</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>A Style Picker hozzáadása

A Style Picker Control egy könnyen használható gomb, amely a felhasználó által a Térkép stílusa módosítására használható. A Style Picker két különböző elrendezési lehetőséggel rendelkezik. Alapértelmezés szerint a Style Picker az `icons` elrendezést használja, és az összes leképezési stílust az ikonok vízszintes soraként jeleníti meg. 

<center>

![Style Picker ikon elrendezése](media/choose-map-style/style-picker-icon-layout.png)</center>

A második elrendezési beállítás neve `list` , és megjeleníti a Térkép stílusainak görgethető listáját.  

<center>

![Style Picker-lista elrendezése](media/choose-map-style/style-picker-list-layout.png)</center>


A következő kód bemutatja, hogyan hozhat létre a Style Picker vezérlőelem egy példányát, és hogyan adhatja hozzá a Térkép jobb felső sarkában. A Style Picker úgy van beállítva, hogy sötét stílussal rendelkezzen, és megjelenítse a kiválasztott, néhány térképi stílust a lista réteg használatával.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

A következő kód egy Style Picker vezérlőelemet helyez el az alapértelmezett beállításokkal a térképhez, így a felhasználó könnyedén válthat a különböző térképi stílusok között. A Térkép stílusa a jobb felső sarokban látható Térkép stílusa vezérlőelem használatával válthat.

<br/>

<iframe height='500' scrolling='no' title='A Style Picker hozzáadása' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Style Picker hozzáadásával</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Alapértelmezés szerint a Azure Maps S0 díjszabási szintje használatakor a Style Picker vezérlő felsorolja az összes elérhető stílust. Ha csökkenteni szeretné a listában szereplő stílusok számát, adja át a listában megjeleníteni kívánt stílusok tömbjét a Style Picker (stílus kiválasztása) `mapStyle` lehetőséggel. Ha S1-et használ, és az összes elérhető stílust meg szeretné jeleníteni, állítsa `mapStyles` a Style Picker kapcsolót a következőre: `"all"`.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Vezérlőelemek hozzáadása a térképekhez:

> [!div class="nextstepaction"]
> [Térképvezérlők hozzáadása](map-add-controls.md)

> [!div class="nextstepaction"]
> [Gombostű hozzáadása](map-add-pin.md)

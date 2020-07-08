---
title: Térkép létrehozása Azure Mapssal | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a térképet egy weblapon a Microsoft Azure Maps web SDK használatával.
author: Philmea
ms.author: philmea
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 97eb1ebb61e5ff78ed918fded8107f5775b533c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83124023"
---
# <a name="create-a-map"></a>Térkép létrehozása

Ebből a cikkből megtudhatja, hogyan hozhat létre térképet és animálhat egy térképet.  

## <a name="loading-a-map"></a>Térkép betöltése

A Térkép betöltéséhez hozzon létre egy új, a [map osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)egy példányát. A Térkép inicializálásakor adjon át egy DIV-elem AZONOSÍTÓját a térkép megjelenítéséhez, és adja át a Térkép betöltéséhez használni kívánt beállításokat. Ha a névtérben nincs megadva az alapértelmezett hitelesítési információ `atlas` , ezeket az adatokat a térképi beállításokban kell megadni a Térkép betöltésekor. A Térkép a teljesítmény érdekében aszinkron módon több erőforrást is betölt. A Térkép példányának létrehozása után csatoljon egy vagy egy `ready` `load` eseményt a térképhez, majd adja hozzá a térképhez kapcsolódó további kódokat az eseménykezelőhöz. Az `ready` esemény akkor következik be, amikor a térképnek elegendő erőforrása van betöltve a programozott módon való interakcióhoz. Az `load` esemény akkor következik be, amikor a Térkép kezdeti nézete befejeződött. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alapszintű Térkép terhelése" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>alapszintű leképezésének terhelését</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ugyanazon a lapon több térképet is betölthet. Ugyanazon a lapon több Térkép is használhatja ugyanazt a vagy eltérő hitelesítési és nyelvi beállításokat.

## <a name="show-a-single-copy-of-the-world"></a>A világ egyetlen példányának megjelenítése

Ha a Térkép széles képernyőre van nagyítva, a világ több példánya is vízszintesen jelenik meg. Ez a lehetőség bizonyos forgatókönyvek esetében kiváló megoldás, de más alkalmazások esetében érdemes megtekinteni a világ egyetlen példányát. Ez a viselkedés úgy valósítható meg, hogy a Maps `renderWorldCopies` beállítást állítja be `false` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a CodePen-on található <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> () értéket a következőben: Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>).
</iframe>


## <a name="map-options"></a>Térképi beállítások

Ha létrehoz egy térképet, több különböző típusú lehetőség is átadható, amelyekkel testre szabhatja a Térkép funkcióit az alábbi listában.

- A [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) és a [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) a Térkép által megjelenített terület megadására szolgálnak.
- A [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) annak megadására szolgálnak, hogy a Térkép hogyan működjön együtt a térképet használó szolgáltatásokkal.
- A [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) a Térkép megadására szolgálnak.
- A [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) annak megadására szolgálnak, hogy a Térkép hogyan legyen elérhető, amikor a felhasználó kommunikál a térképpel. 

Ezek a beállítások a Térkép betöltése után is frissíthetők a `setCamera` ,, `setServiceOptions` `setStyle` és `setUserInteraction` függvények használatával. 

## <a name="controlling-the-map-camera"></a>A Térkép kamera vezérlése

A Térkép megjelenített területét kétféleképpen állíthatja be a Térkép kamerájának használatával. A kamera beállításait a Térkép betöltésekor is megadhatja. Vagy a Térkép `setCamera` betöltését követően bármikor meghívhatja a lehetőséget, hogy programozott módon frissítse a Térkép nézetét.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>A kamera beállítása

A térképes kamera azt szabályozza, hogy mi jelenjen meg a Térkép vászon nézőpontjában. A kamera beállításai az inicializáláskor vagy a Maps függvénybe való továbbításkor adhatók át a térképi beállításoknak `setCamera` .

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

A következő kódban létrejön egy [leképezési objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) , és a középpont és a nagyítási beállítások vannak megadva. A Térkép tulajdonságai, például a középpont és a nagyítási szint a [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)részei.

<br/>

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraOptions használatával' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/qxKBMN/'> `CameraOptions` </a>a tollat a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>-on keresztül Azure Location based Services () használatával.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>A kamera határainak beállítása

A térképes kamera frissítéséhez egy határoló mező használható. Ha a határolókeret a pont adatokból lett kiszámítva, gyakran hasznos a képpont kitöltési értékének megadására a kamera beállításai között az ikon méretének megfelelően. Ezzel biztosíthatja, hogy a pontok ne érje el a Térkép nézőpontjának szegélyét.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

A következő kódban a [Térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) a-on keresztül épül fel `new atlas.Map()` . Térképi tulajdonságok, például `CameraBoundsOptions` a Térkép osztály [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) függvényében definiálhatók. A határértékek és a kitöltés tulajdonságai a használatával állíthatók be `setCamera` .

<br/>

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraBoundsOptions használatával' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'> `CameraBoundsOptions` </a>a tollat a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>-on keresztül Azure Maps () használatával.
</iframe>

### <a name="animate-map-view"></a>Animálási Térkép nézet

A Térkép kamera-beállításainak beállításakor az [animációs beállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) is megadhatók. Ezekkel a beállításokkal adhatja meg, hogy milyen típusú animációt és időtartamot kell figyelembe venni a kamera áthelyezéséhez.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

A következő kódban az első kód blokk létrehoz egy térképet, és beállítja az Enter és a zoom Térkép stílusát. A második blokkban az animálás gombra kattintva létrejön egy Click eseménykezelő. Ha erre a gombra kattint, a `setCamera` függvény hívása a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) és a [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)véletlenszerű értékekkel történik.

<br/>

<iframe height='500' scrolling='no' title='Animálási Térkép nézet' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a toll <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animálása Térkép nézetet</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>A kód kipróbálása

Tekintse meg a kód mintáit. A JavaScript-kódot a **js lapon** szerkesztheti, és az **eredmény lapon**megtekintheti a Térkép nézet módosításait. Kattintson a Szerkesztés elemre a **CodePen**, a jobb felső sarokban, és módosítsa a kódot a CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Az alkalmazás funkcióinak hozzáadásához tekintse meg a kód példáit:

> [!div class="nextstepaction"]
> [Térkép stílusának módosítása](choose-map-style.md)

> [!div class="nextstepaction"]
> [Vezérlőelemek hozzáadása a térképhez](map-add-controls.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)

---
title: Térkép létrehozása az Azure Maps segítségével | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan jeleníthet meg térképet egy weblapon a Microsoft Azure Maps Web SDK használatával.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c85d6078fce7fc8e5a5b5d8485517a8b262044a9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802331"
---
# <a name="create-a-map"></a>Térkép létrehozása

Ez a cikk bemutatja, hogyan hozhat létre térképet, és animálhatja a térképet.  

## <a name="loading-a-map"></a>Térkép betöltése

Térkép betöltéséhez hozzon létre egy új példányt a [Térkép osztályból.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) A térkép inicializálásakor adja át a DIV elem azonosítóját a térkép megjelenítéséhez, és adja át a térkép betöltésekor használandó beállításokat. Ha a névtérben nincs megadva alapértelmezett hitelesítési információ, akkor ezt az `atlas` információt a térkép betöltésekor meg kell adni a térkép beállításaiban. A térkép több erőforrást tölt be aszinkron teljesítmény érdekében. Mint ilyen, a térképpéldány létrehozása `ready` `load` után csatoljon egy vagy eseményt a térképhez, majd adjon hozzá további kódot, amely kölcsönhatásba lép a térképpel az eseménykezelőhöz. Az `ready` esemény akkor következik be, amikor a térkép elegendő erőforrást tölt be ahhoz, hogy programozott módon kommunikálhassa. Az `load` esemény akkor következik be, ha a kezdeti térképnézet teljesen befejeződött. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alapszintű térképterhelés" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen Basic<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>térképterhelést</a> az Azure Maps ( ) szerint a <a href='https://codepen.io'>CodePen-en.</a>
</iframe>

> [!TIP]
> Ugyanazon az oldalon több térképet is betölthet. Ugyanazon az oldalon több térkép is használhatja ugyanazt vagy eltérő hitelesítési és nyelvi beállításokat.

## <a name="show-a-single-copy-of-the-world"></a>A világ egyetlen példányának megjelenítése

Amikor a térképet széles vásznon kicsinyíti, a világ több példánya vízszintesen jelenik meg. Ez a lehetőség nagyszerű bizonyos esetekben, de más alkalmazások esetében kívánatos, hogy egyetlen példányt a világ. Ez a viselkedés úgy `renderWorldCopies` valósítja `false`meg, hogy a térképek beállítást a-ra állítja.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = hamis" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Lásd a Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies =</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>hamis az Azure Maps ( ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Térképbeállítások

Amikor létrehoz egy térképet ott, több különböző típusú lehetőségeket lehet átadni, hogy testre szabhatja, hogy a térkép működik az alábbiak szerint.

- [A CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) és a [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) segítségével adhatja meg, hogy a térkép milyen területet jelenítsen meg.
- [A ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) segítségével megadhatja, hogy a térkép hogyan működjön együtt a térképet működtető szolgáltatásokkal.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) használják, hogy adja meg a térképet kell stilizált és renderelt.
- [A UserInteractionOptions beállítással](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) megadhatja, hogy a térkép hogyan érjen el, amikor a felhasználó interakcióba lép a térképpel. 

Ezek a beállítások a `setCamera`, `setServiceOptions`, , `setStyle`és `setUserInteraction` a függvények használatával történő betöltésután is frissíthetők. 

## <a name="controlling-the-map-camera"></a>A térképkamera vezérlése

A térkép kamerájával kétféleképpen állíthatja be a térkép megjelenített területének beállítását. A kamera beállításait a térkép betöltésekor állíthatja be. Vagy bármikor felhívhatja `setCamera` a lehetőséget, miután a térkép betöltődött a térképnézet programozott frissítéséhez.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>A kamera beállítása

A térképkamera azt szabályozza, hogy mi jelenjen meg a térképvászon nézetablakában. Kamera lehetőségeket lehet átadni a térkép en lehetőségek, `setCamera` amikor inicializált, vagy át a térképek funkciót.

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

A következő kódban [egy Térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) jön létre, és a középső és nagyítási beállítások vannak megszabva. A térkép tulajdonságok, például a középső és a nagyítási szint a [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)részét képezik.

<br/>

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraOptions segítségével' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Toll <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>térkép `CameraOptions` létrehozása az </a>Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>helyalapú szolgáltatásokon keresztül ( ) című tollat a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>A kamerahatárok beállítása

A térképkamera frissítéséhez határolókeret használható. Ha a határolókeretet pontadatokból számították ki, gyakran hasznos, ha a kamerabeállításokban is meg kell adni egy képpontkitöltési értéket, hogy figyelembe vegye az ikon méretét. Ez segít biztosítani, hogy a pontok ne essenek le a térkép nézetablakának széléről.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

A következő kódban egy [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) objektum `new atlas.Map()`épül fel a . Leképezési `CameraBoundsOptions` tulajdonságok, például a [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) funkció a Map osztály on-mail ben definiálható. A határok és a kitöltési tulajdonságok a használatával `setCamera`vannak beállítva.

<br/>

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraBoundsOptions segítségével' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Toll <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>térkép `CameraBoundsOptions` létrehozása </a>az<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) segítségével a <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Térképnézet animálása

A térkép kamerabeállításainak megadásakor [animációs beállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) is beállíthatók. Ezek a beállítások határozzák meg az animáció típusát és időtartamát, amelyet a kamera mozgatásához kell tartania.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

A következő kódban az első kódblokk létrehoz egy térképet, és beállítja a be- és nagyítási térképstílusokat. A második kódblokkban egy kattintási eseménykezelő jön létre az animálás gombhoz. Ha erre a gombra `setCamera` kattint, a funkció neve a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) és az AnimationOptions néhány véletlenszerű értékével [történik.](/javascript/api/azure-maps-control/atlas.animationoptions)

<br/>

<iframe height='500' scrolling='no' title='Térképnézet animálása' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Map View by Azure Maps ( ) című témakört a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="try-out-the-code"></a>Próbálja ki a kódot

Nézd meg a kódmintákat. A JavaScript-kódot a **JS lapon** szerkesztheti, és megtekintheti a térképnézet változásait az **Eredmény lapon.** A **CodePen szerkesztése**elemre is kattinthat a jobb felső sarokban, és módosíthatja a kódot a CodePen alkalmazásban.

<a id="relatedReference"></a>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Kameraopciók](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Tekintse meg a kódpéldákat, hogy funkciókat adjon az alkalmazáshoz:

> [!div class="nextstepaction"]
> [Térkép stílusának módosítása](choose-map-style.md)

> [!div class="nextstepaction"]
> [Vezérlőelemek hozzáadása a térképhez](map-add-controls.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)

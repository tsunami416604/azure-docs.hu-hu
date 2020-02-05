---
title: Térkép létrehozása Azure Mapssal | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg a térképet egy weblapon a Microsoft Azure Maps web SDK használatával.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988583"
---
# <a name="create-a-map"></a>Térkép létrehozása

Ebből a cikkből megtudhatja, hogyan hozhat létre térképet és animálhat egy térképet.  

## <a name="loading-a-map"></a>Térkép betöltése

A Térkép betöltéséhez hozzon létre egy új, a [map osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)egy példányát. A Térkép inicializálásakor adjon át egy DIV-elem AZONOSÍTÓját a térkép megjelenítéséhez, és adja át a Térkép betöltéséhez használni kívánt beállításokat. Ha nincs megadva az alapértelmezett hitelesítési információ a `atlas` névtérben, ezeket az információkat meg kell adni a térképi beállításokban a Térkép betöltésekor. A Térkép a teljesítmény érdekében aszinkron módon több erőforrást is betölt. Így a Map-példány létrehozása után csatoljon egy `ready` vagy `load` eseményt a térképhez, majd adja hozzá a térképhez kapcsolódó további kódokat az eseménykezelőhöz. A `ready` esemény azonnal következik be, amint a térképnek elegendő erőforrása van betöltve a programozott módon való interakcióhoz. Az `load` esemény akkor következik be, amikor a kezdeti leképezési nézet teljes betöltést végzett. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alapszintű Térkép terhelése" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>alapszintű leképezésének terhelését</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ugyanazon a lapon több térképet is betölthet. Ugyanazon a lapon több Térkép is használhatja ugyanazt a vagy eltérő hitelesítési és nyelvi beállításokat.

## <a name="show-a-single-copy-of-the-world"></a>A világ egyetlen példányának megjelenítése

Ha a Térkép széles képernyőre van nagyítva, a világ több példánya is vízszintesen jelenik meg. Ez a lehetőség bizonyos forgatókönyvek esetében kiváló megoldás, de más alkalmazások esetében érdemes megtekinteni a világ egyetlen példányát. Ez a viselkedés úgy valósítható meg, ha a Maps `renderWorldCopies` lehetőséget `false`re állítja.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false Azure Maps értéket</a> .
</iframe>

## <a name="controlling-the-map-camera"></a>A Térkép kamera vezérlése

A Térkép megjelenített területét kétféleképpen állíthatja be a Térkép kamerájának használatával. A kamera beállításait a Térkép betöltésekor is megadhatja. Másik lehetőségként meghívhatja a `setCamera` lehetőséget, amikor a Térkép betöltődött, és programozott módon frissíti a Térkép nézetet.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>A kamera beállítása

A következő kódban létrejön egy [leképezési objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) , és a középpont és a nagyítási beállítások vannak megadva. A Térkép tulajdonságai, például a középpont és a nagyítási szint a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)részei.

<br/>

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraOptions használatával' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti, hogy a toll <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>létrehoz egy térképet a `CameraOptions`on keresztül </a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>A kamera határainak beállítása

A következő kódban a [Térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) a `new atlas.Map()`on keresztül lett felépítve. A leképezési tulajdonságok, például a `CameraBoundsOptions` a Map osztály [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) funkciójával definiálhatók. A korlátok és a kitöltés tulajdonságai a `setCamera`használatával állíthatók be.

<br/>

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraBoundsOptions használatával' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti, hogy a toll <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>létrehoz egy térképet a `CameraBoundsOptions`on keresztül </a>.
</iframe>

### <a name="animate-map-view"></a>Animálási Térkép nézet

A következő kódban az első kód blokk létrehoz egy térképet, és beállítja az Enter és a zoom Térkép stílusát. A második blokkban az animálás gombra kattintva létrejön egy Click eseménykezelő. Ha erre a gombra kattint, a rendszer meghívja a `setCamera` függvényt a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) és a [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)véletlenszerű értékeivel.

<br/>

<iframe height='500' scrolling='no' title='Animálási Térkép nézet' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animálása Térkép nézetet</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>A kód kipróbálása

Tekintse meg a kód mintáit. A JavaScript-kódot a **js lapon** szerkesztheti, és az **eredmény lapon**megtekintheti a Térkép nézet módosításait. Kattintson a Szerkesztés elemre a **CodePen**, a jobb felső sarokban, és módosítsa a kódot a CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Az alkalmazás funkcióinak hozzáadásához tekintse meg a kód példáit:

> [!div class="nextstepaction"]
> [A Térkép stílusának módosítása](choose-map-style.md)

> [!div class="nextstepaction"]
> [Vezérlőelemek hozzáadása a térképhez](map-add-controls.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)

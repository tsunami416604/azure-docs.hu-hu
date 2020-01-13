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
ms.openlocfilehash: 49c86f3e6c654ecbfcd07809f42a1b038ca3f8ab
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911110"
---
# <a name="create-a-map"></a>Térkép létrehozása

Ebből a cikkből megtudhatja, hogyan hozhat létre térképet és animálhat egy térképet.  

## <a name="loading-a-map"></a>Térkép betöltése

A Térkép betöltéséhez hozzon létre egy új, a [map osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)egy példányát. A Térkép inicializálásakor egy DIV-elem AZONOSÍTÓját, amely megjeleníti a térképet és a leképezés betöltéséhez használni kívánt beállításokat. Ha nincs megadva az alapértelmezett hitelesítési információ a `atlas` névtérben, ezeket az információkat meg kell adni a térképi beállításokban a Térkép betöltésekor. A Térkép a teljesítmény érdekében aszinkron módon több erőforrást is betölt. Ennek megfelelően a Map-példány létrehozása után csatoljon egy `ready` vagy `load` eseményt a térképhez, majd adja hozzá az eseménykezelő térképével kommunikáló további kódokat. A `ready` esemény azonnal következik be, amint a térképnek elegendő erőforrása van betöltve a programozott módon való interakcióhoz. Az `load` esemény akkor következik be, amikor a kezdeti leképezési nézet teljes betöltést végzett. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alapszintű Térkép terhelése" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>alapszintű leképezésének terhelését</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Több Térkép is betölthető ugyanarra az oldalra, és mindegyik használhatja ugyanazt a vagy a különböző hitelesítési és nyelvi beállításokat.

## <a name="show-a-single-copy-of-the-world"></a>A világ egyetlen példányának megjelenítése

Ha a Térkép széles képernyőre van nagyítva, a világ több példánya is vízszintesen jelenik meg. Ez nagy a legtöbb forgatókönyvhöz, de néhány alkalmazás esetében érdemes lehet csak egyetlen példányt látni a világból. Ezt úgy teheti meg, hogy a Maps `renderWorldCopies` lehetőséget `false`ra állítja be.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false Azure Maps értéket</a> .
</iframe>

## <a name="controlling-the-map-camera"></a>A Térkép kamera vezérlése

A Térkép megjelenített területét kétféleképpen állíthatja be a kamera használatával. Megadhatja a kamera beállításait, például a középpontot és a nagyítást, amikor betölti a térképet, vagy a Térkép betöltését követően meghívja a `setCamera` lehetőséget, hogy programozott módon frissítse a Térkép nézetét.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>A kamera beállítása

A következő kódban létrejön egy [leképezési objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) , és a középpont és a nagyítási beállítások vannak megadva. A Térkép tulajdonságai, például a központ és a nagyítási szint a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)részei.

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

A következő kódban az első kód blokk létrehoz egy térképet, és beállítja a Térkép stílusát, a középpontot és a nagyítási értékeket. A második blokkban az animálás gombra kattintva létrejön egy Click eseménykezelő. Ha erre a gombra kattint, a rendszer a setCamera függvényt a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)véletlenszerű értékekkel hívja meg.

<br/>

<iframe height='500' scrolling='no' title='Animálási Térkép nézet' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animálása Térkép nézetet</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>A kód kipróbálása

Vessen egy pillantást a fenti mintakód. A JavaScript-kódot a bal oldalon található **js lapon** szerkesztheti, és a jobb oldalon látható **eredmények lapon** láthatja a Térkép nézet módosításait. Kattintson a **Szerkesztés CodePen** gombra, és szerkessze a kódot a CodePen.

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
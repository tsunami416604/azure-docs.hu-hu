---
title: Térkép létrehozása Azure Mapssal | Microsoft Docs
description: Térkép létrehozása a Azure Maps web SDK-val.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 932c244ff41e757413a05cde019ee7ee1a82232d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976126"
---
# <a name="create-a-map"></a>Térkép létrehozása

Ebből a cikkből megtudhatja, hogyan hozhat létre térképet és animálhat egy térképet.  

## <a name="loading-a-map"></a>Térkép betöltése

A Térkép betöltéséhez hozzon létre egy új, a [map osztály](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)egy példányát. A Térkép inicializálásakor egy DIV-elem AZONOSÍTÓját, amely megjeleníti a térképet és a leképezés betöltéséhez használni kívánt beállításokat. Ha a `atlas` névtérben nincs megadva az alapértelmezett hitelesítési információ, ezeket az adatokat a térképi beállításokban kell megadni a Térkép betöltésekor. A Térkép a teljesítmény érdekében aszinkron módon több erőforrást is betölt. Ilyenkor a Map-példány létrehozása után csatoljon egy `ready` vagy `load` egy eseményt a térképhez, majd adja hozzá az eseménykezelő térképéhez kapcsolódó további kódokat. Az `ready` esemény akkor következik be, amikor a térképnek elegendő erőforrása van betöltve a programozott módon való interakcióhoz. Az `load` esemény akkor következik be, amikor a Térkép kezdeti nézete befejeződött. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alapszintű Térkép terhelése" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg <a href='https://codepen.io/azuremaps/pen/rXdBXx/'></a> a toll alapszintű leképezésének terhelését Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Több Térkép is betölthető ugyanarra az oldalra, és mindegyik használhatja ugyanazt a vagy a különböző hitelesítési és nyelvi beállításokat.

## <a name="show-a-single-copy-of-the-world"></a>A világ egyetlen példányának megjelenítése

Ha a Térkép széles képernyőre van nagyítva, a világ több példánya is vízszintesen jelenik meg. Ez nagy a legtöbb forgatókönyvhöz, de néhány alkalmazás esetében érdemes lehet csak egyetlen példányt látni a világból. Ezt úgy teheti meg, hogy `renderWorldCopies` `false`a Maps beállítást a értékre állítja.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io'>CodePen</a>-on található <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> () értéket a következőben: Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>).
</iframe>

## <a name="controlling-the-map-camera"></a>A Térkép kamera vezérlése

A Térkép megjelenített területét kétféleképpen állíthatja be a kamera használatával. Megadhatja a kamera beállításait, például a középpontot és a nagyítást, amikor betölti a `setCamera` térképet, vagy a Térkép betöltését követően bármikor meghívja a lehetőséget, hogy programozott módon frissítse a Térkép nézetét.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>A kamera beállítása

A következő kódban létrejön egy [leképezési objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) , és a középpont és a nagyítási beállítások vannak megadva. A Térkép tulajdonságai, például a központ és a nagyítási szint a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)részei.

<br/>

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraOptions használatával' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>keresztül `CameraOptions` </a>Azure Location based Services () használatával.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>A kamera határainak beállítása

A következő kódban a [Térkép objektum](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) a-on keresztül `new atlas.Map()`épül fel. Térképi tulajdonságok, `CameraBoundsOptions` például a Térkép osztály [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) függvényében definiálhatók. A határértékek és a kitöltés tulajdonságai a használatával `setCamera`állíthatók be.

<br/>

<iframe height='500' scrolling='no' title='Térkép létrehozása a CameraBoundsOptions használatával' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io'>CodePen</a>-on<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>keresztül `CameraBoundsOptions` </a>Azure Maps () használatával.
</iframe>

### <a name="animate-map-view"></a>Animálási Térkép nézet

A következő kódban az első kód blokk létrehoz egy térképet, és beállítja a Térkép stílusát, a középpontot és a nagyítási értékeket. A második blokkban az animálás gombra kattintva létrejön egy Click eseménykezelő. Ha erre a gombra kattint, a rendszer a setCamera függvényt a [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)véletlenszerű értékekkel hívja meg.

<br/>

<iframe height='500' scrolling='no' title='Animálási Térkép nézet' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a toll <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animálása Térkép nézetet</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>A kód kipróbálása

Vessen egy pillantást a fenti mintakód. A JavaScript-kódot a bal oldalon található **js lapon** szerkesztheti, és a jobb oldalon látható **eredmények lapon** láthatja a Térkép nézet módosításait. Kattintson a **Szerkesztés CodePen** gombra, és szerkessze a kódot a CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>További lépések

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
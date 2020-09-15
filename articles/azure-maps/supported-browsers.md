---
title: A web SDK által támogatott böngészők | Microsoft Azure térképek
description: Ismerje meg, hogyan ellenőrizhető, hogy a Azure Maps web SDK támogatja-e a böngészőt. A támogatott böngészők listájának megtekintése. Ismerje meg, hogyan használhatók a térképes szolgáltatások a régi böngészőkkel.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 73d875e32f4fcf22551cf6b0946e49c2f826e523
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085025"
---
# <a name="web-sdk-supported-browsers"></a>A webes SDK által támogatott böngészők

A Azure Maps web SDK egy [Atlas. isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas#issupported-boolean-)nevű segítő függvényt biztosít. Ez a függvény észleli, hogy a webböngészőnek van-e legalább WebGL-funkciója, amely a Térkép vezérlőelem betöltésének és megjelenítésének támogatásához szükséges. Íme egy példa arra, hogyan használhatja a függvényt:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Asztal

A Azure Maps web SDK a következő asztali böngészőket támogatja:

- Microsoft Edge (jelenlegi és korábbi verzió)
- Google Chrome (aktuális és korábbi verzió)
- Mozilla Firefox (aktuális és korábbi verzió)
- Apple Safari (Mac OS X) (aktuális és korábbi verzió)

Lásd még: [korábbi böngészők megcélzása](#Target-Legacy-Browsers) a cikk későbbi részében.

## <a name="mobile"></a>Mobil

A Azure Maps web SDK a következő mobil böngészőket támogatja:

- Android
  - A Chrome jelenlegi verziója Android 6,0 és újabb rendszereken
  - Chrome Webview az Android 6,0-es és újabb verzióiban
- iOS
  - Mobile Safari az iOS jelenlegi és korábbi verziójában
  - UIWebView és WKWebView az iOS jelenlegi és korábbi verziójában
  - Az iOS-hez készült Chrome jelenlegi verziója

> [!TIP]
> Ha egy webnézet vezérlőelem használatával ágyaz be egy térképet egy mobileszközön belül, érdemes lehet a [Azure Maps web SDK NPM-csomagját](https://www.npmjs.com/package/azure-maps-control) használni az Azure-Content Delivery Network üzemeltetett SDK verziójára való hivatkozás helyett. Ez a megközelítés csökkenti a betöltési időt, mert az SDK már szerepel a felhasználó eszközén, és nem kell a futtatáskor letölteni.

## <a name="nodejs"></a>Node.js

A következő web SDK-modulok is támogatottak a Node.jsban:

- Szolgáltatások modul ([dokumentációs](how-to-use-services-module.md)  |  [NPM modul](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Cél örökölt böngészők

Előfordulhat, hogy olyan régebbi böngészőket szeretne megcélozni, amelyek nem támogatják a WebGL, vagy csak korlátozott támogatással rendelkeznek. Ilyen esetekben javasoljuk, hogy Azure Maps-szolgáltatásokat, valamint egy nyílt forráskódú Térkép vezérlőelemet, például a [betegtájékoztatót](https://leafletjs.com/)használja. Bemutatunk egy példát:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + szórólap" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + prospektust</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps web SDK-ról:

> [!div class="nextstepaction"]
> [Térképvezérlő](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Szolgáltatások modul](how-to-use-services-module.md)

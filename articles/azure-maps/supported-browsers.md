---
title: A web SDK által támogatott böngészők | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatók a Microsoft Azure Maps web SDK által támogatott böngészők, és hogyan ellenőrizhető, hogy egy böngésző támogatott böngésző-e.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988787"
---
# <a name="web-sdk-supported-browsers"></a>A webes SDK által támogatott böngészők

A Azure Maps web SDK egy [Atlas. isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)nevű segítő függvényt biztosít. Ez a függvény észleli, hogy a webböngészőnek van-e legalább WebGL-funkciója, amely a Térkép vezérlőelem betöltésének és megjelenítésének támogatásához szükséges. Íme egy példa arra, hogyan használhatja a függvényt:

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

A Node. js-ben a következő web SDK-modulok is támogatottak:

- Szolgáltatások modul ([dokumentáció](how-to-use-services-module.md) | [NPM modul](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Cél örökölt böngészők

Előfordulhat, hogy olyan régebbi böngészőket szeretne megcélozni, amelyek nem támogatják a WebGL, vagy csak korlátozott támogatással rendelkeznek. Ilyen esetekben javasoljuk, hogy Azure Maps-szolgáltatásokat, valamint egy nyílt forráskódú Térkép vezérlőelemet, például a [betegtájékoztatót](https://leafletjs.com/)használja. Például:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + szórólap" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + szórólap</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Következő lépések

További információ a Azure Maps web SDK-ról:

> [!div class="nextstepaction"]
> [Térképvezérlő](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Szolgáltatások modul](how-to-use-services-module.md)

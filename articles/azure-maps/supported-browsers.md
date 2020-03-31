---
title: Web SDK által támogatott böngészők | Microsoft Azure Maps
description: Ebből a cikkből megismerheti a Microsoft Azure Maps Web SDK támogatott böngészőit, és azt, hogy miként ellenőrizheti, hogy a böngésző támogatott böngésző-e.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988787"
---
# <a name="web-sdk-supported-browsers"></a>A webes SDK által támogatott böngészők

Az Azure Maps Web SDK egy [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)nevű segítő funkciót biztosít. Ez a funkció azt észleli, hogy a webböngésző rendelkezik-e a térképvezérlő betöltésének és megjelenítésének támogatásához szükséges minimális WebGL-szolgáltatásokkal. Íme egy példa a függvény használatára:

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

Az Azure Maps Web SDK a következő asztali böngészőket támogatja:

- Microsoft Edge (jelenlegi és korábbi verzió)
- Google Chrome (jelenlegi és korábbi verzió)
- Mozilla Firefox (jelenlegi és korábbi verzió)
- Apple Safari (Mac OS X) (jelenlegi és korábbi verzió)

Lásd [még: A korábbi böngészők célzása](#Target-Legacy-Browsers) a cikk későbbi részében.

## <a name="mobile"></a>Mobiltelefon

Az Azure Maps Web SDK a következő mobilböngészőket támogatja:

- Android
  - A Chrome jelenlegi verziója Android 6.0-s és újabb verziókon
  - Chrome WebView Android 6.0-s és újabb verziói
- iOS
  - Mobile Safari az iOS jelenlegi és korábbi főverzióján
  - UIWebView és WKWebView az iOS jelenlegi és korábbi főverzióján
  - Az iOS Chrome jelenlegi verziója

> [!TIP]
> Ha egy webview-vezérlő használatával csatlakoztat be egy térképet egy mobilalkalmazásba, előfordulhat, hogy [az Azure Maps Web SDK npm csomagját](https://www.npmjs.com/package/azure-maps-control) szeretné használni ahelyett, hogy az Azure Content Delivery Networkben üzemeltetett SDK-verziójára hivatkozna. Ez a megközelítés csökkenti a betöltési időt, mert az SDK már a felhasználó eszközén van, és nem kell letölteni futásidőben.

## <a name="nodejs"></a>Node.js

A Node.js a következő Web SDK-modulokat is támogatja:

- Szolgáltatási modul ([dokumentáció](how-to-use-services-module.md) | [npm modul](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Örökölt böngészők célzása

Előfordulhat, hogy olyan régebbi böngészőket szeretne megcélozni, amelyek nem támogatják a WebGL-t, vagy amelyek csak korlátozott mértékben támogatják azt. Ilyen esetekben azt javasoljuk, hogy az Azure Maps-szolgáltatásokat egy nyílt forráskódú térképvezérlővel, például [a Leaflet-el](https://leafletjs.com/)együtt használja. Például:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Szórólap" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>+ <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Leaflet</a> by Azure Maps ( ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>További lépések

További információ az Azure Maps Web SDK-ról:

> [!div class="nextstepaction"]
> [Térképvezérlő](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Szolgáltatások modul](how-to-use-services-module.md)

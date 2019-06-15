---
title: A webes SDK támogatott böngészők – az Azure Maps |} A Microsoft Docs
description: További információk a támogatott böngészők az Azure Maps Web SDK-val
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686649"
---
# <a name="web-sdk-supported-browsers"></a>A webes SDK által támogatott böngészők

Az Azure Maps Web SDK biztosít egy nevű segítőfüggvény [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Ez a függvény észleli, hogy egy webes böngésző rendelkezik a minimális vagy a Web GL funkciók be- és a térkép vezérlőelem megjelenítése támogatásához szükséges. A következő példa bemutatja, hogyan használja a függvényt:

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Asztali

Az Azure Maps Web SDK a következő asztali böngészők használatát támogatja:

- A Microsoft Edge (jelenlegi és korábbi verzió)
- Google Chrome (jelenlegi és korábbi verzió)
- Mozilla Firefox (jelenlegi és korábbi verzió)
- Apple Safari (Mac OS X) (jelenlegi és korábbi verzió)

Lásd még: [régebbi böngészők cél](#Target-Legacy-Browsers) a cikk későbbi részében.

## <a name="mobile"></a>mobil

Az Azure Maps Web SDK a következő mobilböngészők használatát támogatja:

- Android
  - Chrome-ban, az Android 6.0 vagy újabb verziója
  - Chrome WebView-t az Android 6.0-s és újabb verziók
- iOS
  - A jelenlegi és korábbi főverziója iOS Mobile Safari
  - UIWebView és a jelenlegi és korábbi főverziója iOS WKWebView
  - Chrome-ban iOS-es verziója

> [!TIP]
> Ha a mobilalkalmazás belül térkép beágyazott WebView vezérlőelem használatával, előfordulhat, hogy szeretné használni a [npm-csomag az Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) helyett az SDK-t, amely az Azure Content Delivery verziója hivatkozik Hálózat. Ez a megközelítés csökkenti a betöltési idő, mert az SDK már csak a felhasználó eszközén, és le kell tölteni futási időben nem szükséges.

## <a name="nodejs"></a>Node.js

Az alábbi Web SDK-modulokat is támogatottak a node.js-ben:

- Szolgáltatások modul ([dokumentáció](how-to-use-services-module.md) | [npm modult](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Örökölt támogatott böngészők

Előfordulhat, hogy kívánt cél régebbi böngészők vagy a Web GL, amelyek nem támogatják, vagy csak korlátozott támogatást biztosítunk. Ezekben az esetekben javasoljuk, hogy az Azure Maps szolgáltatás együtt egy nyílt forráskódú térkép vezérlőelem, például a használt [utasítással](https://leafletjs.com/). Például:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Az Azure Maps + tájékoztatón" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>az Azure Maps + tájékoztatón</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>További lépések

További információ az Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Térképvezérlő](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Szolgáltatások modul](how-to-use-services-module.md)

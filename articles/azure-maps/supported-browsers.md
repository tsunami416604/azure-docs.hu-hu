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
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577339"
---
# <a name="web-sdk-supported-browsers"></a>SDK által támogatott webböngészők

Az Azure Maps Web SDK biztosít egy segédfüggvény [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) észleli, hogy egy webes böngésző rendelkezik-e a minimális vagy a Web GL funkciók betöltése és megjelenítése a térkép vezérlőelem támogatásához szükséges. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Asztal

Az Azure Maps Web SDK támogatja a következő asztali böngészők.

- A jelenlegi és korábbi verzióját a Microsoft Edge 
- A Chrome jelenlegi és korábbi verzióját 
- A jelenlegi és korábbi verzióját a Firefox 
- A jelenlegi és korábbi verziót a Safari (Mac OS X) 

Lásd még: [régebbi böngészők cél](#Target-Legacy-Browsers).

## <a name="mobile"></a>Mobiltelefon

Az Azure Maps Web SDK támogatja a következő mobilböngészők.

-  Android
    * Az Android 6.0 Chrome jelenlegi verziója
    * Az Android 6.0 Chrome WebView-t
- iOS
    * A jelenlegi és korábbi főverziója iOS Mobile Safari
    * UIWebView és a jelenlegi és korábbi főverziója iOS WKWebView
    * Chrome-ban iOS-es verziója

> [!TIP]
> Ha egy térkép belül egy mobilalkalmazás WebView-vezérlés használatával végez beágyazást, előfordulhat, hogy inkább használja a [npm-csomag az Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) hivatkozik a CDN üzemeltetett verze sady SDK helyett. Ez csökkenti betöltési idő, az SDK-t fog már a felhasználó eszközén, és nem futásidőben le kell tölteni.

## <a name="nodejs"></a>Node.js

Az alábbi Web SDK-modulokat is támogatottak a node.js-ben:

- Szolgáltatások modul ([dokumentáció](how-to-use-services-module.md) | [npm modult](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Örökölt támogatott böngészők

Ha szeretne megcélozni a régebbi böngészőknél előfordulhat, hogy nem támogatja a, vagy csak korlátozottan támogatják a vagy a Web GL, javasoljuk, hogy az Azure Maps szolgáltatás használata egy nyílt forráskódú térkép vezérlőelem együtt például [utasítással](https://leafletjs.com/). 


<iframe height="500" style="width: 100%;" scrolling="no" title="Az Azure Maps + tájékoztatón" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>az Azure Maps + tájékoztatón</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>További lépések

További információ az Azure Maps Web SDK-t.

> [!div class="nextstepaction"]
> [Térképvezérlő](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Szolgáltatások modul](how-to-use-services-module.md)

---
title: Támogatott webböngészők – az Azure Maps |} A Microsoft Docs
description: További információk a támogatott webböngészők az Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 2678fa7c9290c7ec0a27288e7739fde4bb0870fd
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501271"
---
# <a name="supported-web-browsers"></a>Támogatott webböngészők

Az Azure Maps Web SDK biztosít egy segédfüggvény [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) észleli, hogy egy webes böngésző rendelkezik-e a minimális vagy a Web GL funkciók betöltése és megjelenítése a térkép vezérlőelem támogatásához szükséges. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Add your map code here.
}
```

Az Azure Maps Web SDK az alábbi webböngészőkkel támogatja.

## <a name="desktop"></a>Asztal

- A jelenlegi és korábbi verzióját a Microsoft Edge 
- A Chrome jelenlegi és korábbi verzióját 
- A jelenlegi és korábbi verzióját a Firefox 
- A jelenlegi és korábbi verziót a Safari (Mac OS X) 

Lásd még: [régebbi böngészők cél](#Target-Legacy-Browsers).

## <a name="mobile"></a>Mobiltelefon

-  Android
    * Az Android 6.0 Chrome jelenlegi verziója
    * Az Android 6.0 Chrome WebView-t
- iOS
    * A jelenlegi és korábbi főverziója iOS Mobile Safari
    * UIWebView és a jelenlegi és korábbi főverziója iOS WKWebView
    * Chrome-ban iOS-es verziója

> [!TIP]
> Ha mobilalkalmazás nézet WebView vezérlőelem belül térkép ágyaz be, előfordulhat, használja a [npm-csomag az Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) előnyösebb hivatkozik az SDK-t a CDN üzemeltetett verzióját. Ez csökkenti betöltési idő, az SDK-t fog már a felhasználó eszközén, és nem futásidőben le kell tölteni.

## <a name="nodejs"></a>Node.js

Az alábbi Web SDK-modulokat is támogatottak a node.js-ben:

- Szolgáltatások modul ([dokumentáció](how-to-use-services-module.md) | [npm modult](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Örökölt támogatott böngészők

Ha szeretne megcélozni, amely nem támogatja a, vagy csak korlátozottan támogatják a vagy a Web GL régebbi webböngészők, javasoljuk, hogy az Azure Maps szolgáltatás használata egy nyílt forráskódú térkép vezérlőelem együtt például [utasítással](https://leafletjs.com/). 

<iframe height="500" style="width: 100%;" scrolling="no" title="Az Azure Maps + tájékoztatón" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>az Azure Maps + tájékoztatón</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>
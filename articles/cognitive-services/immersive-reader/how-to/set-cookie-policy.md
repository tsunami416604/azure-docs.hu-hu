---
title: Állítsa be a magával ragadó olvasó cookie-szabályzatát
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan állíthatja be a cookie-kra vonatkozó szabályzatot a Magával ragadó olvasóhoz.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946110"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Hogyan kell beállítani a cookie-politika a magával ragadó reader

A Magával ragadó olvasó alapértelmezés szerint letiltja a cookie-k használatát. Ha engedélyezi a cookie-használatot, akkor a Magával ragadó olvasó cookie-kat használhat a felhasználói beállítások fenntartására és a funkciók használatának nyomon követésére. Ha engedélyezi a cookie-k használatát a Immersive Reader alkalmazásban, kérjük, vegye figyelembe az EU cookie-megfelelőségi irányelveinek követelményeit. A fogadó alkalmazás felelőssége, hogy az EU cookie-megfelelőségi szabályzatának megfelelően megszerezze a szükséges felhasználói hozzájárulást.

A cookie-kra vonatkozó szabályzat a Immersive Reader [opciókkal](../reference.md#options)állítható be. További információt a [CookiePolicy enum](../reference.md#cookiepolicy-enum) című témakörben talál.

## <a name="enable-cookie-usage"></a>Cookie-használat engedélyezése

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Cookie-használat letiltása

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Node.js rövid útmutatót,](../quickstart-nodejs.md) hogy mi mást tehet a Magával ragadó Reader SDK segítségével Node.js
* Tekintse meg a [Python oktatóanyag,](../tutorial-python.md) hogy mi mást tehet ünk a magával ragadó Reader SDK python használatával
* Tekintse meg a [Swift bemutató,](../tutorial-ios-picture-immersive-reader.md) hogy mi mást tehetünk a magával ragadó Reader SDK swift
* Fedezze fel a [magával ragadó Reader SDK-t](https://github.com/microsoft/immersive-reader-sdk) és a [magával ragadó Reader SDK-referenciát](../reference.md)
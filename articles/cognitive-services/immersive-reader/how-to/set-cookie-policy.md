---
title: A megölelő olvasó cookie-szabályzatának beállítása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan állíthatja be a cookie-szabályzatot a teljes olvasóhoz.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 876379a211038933cf665bd1a4a4daae9c9b2787
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267069"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>A cookie-szabályzat beállítása a magától megjelenő olvasóhoz

A lebilincselő olvasó alapértelmezés szerint letiltja a cookie-használatot. Ha engedélyezi a cookie-használatot, a felhasználóbarát olvasó cookie-kat használhat a felhasználói beállítások fenntartásához és a funkciók használatának nyomon követéséhez. Ha engedélyezi a cookie-használatot a megölelő olvasóban, vegye figyelembe az EU cookie-megfelelőségi szabályzatának követelményeit. A gazda alkalmazás feladata a szükséges felhasználói jóváhagyások beszerzése az EU cookie-megfelelőségi szabályzatának megfelelően.

A cookie-szabályzat a magával ragadó olvasó [beállításain](../reference.md#options)keresztül állítható be. További információ: [CookiePolicy Enum](../reference.md#cookiepolicy-enum) .

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

* Tekintse meg a [Node. js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) rövid útmutatóját, amelyből megtudhatja, hogy mit tehet a magával az olvasó SDK-val a Node. js használatával
* Tekintse meg a [Python-oktatóanyagot](../tutorial-python.md) , amelyből megtudhatja, hogy mit tehet a részletes olvasó SDK-val a Python használatával
* Tekintse meg a [Swift-oktatóanyagot](../tutorial-ios-picture-immersive-reader.md) , amelyből megtudhatja, mit tehet a gyors
* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](../reference.md)
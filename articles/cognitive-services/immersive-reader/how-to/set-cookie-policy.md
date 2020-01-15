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
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946110"
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

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [Node. js](../quickstart-nodejs.md) rövid útmutatóját, amelyből megtudhatja, hogy mit tehet a magával az olvasó SDK-val a Node. js használatával
* Tekintse meg a [Python-oktatóanyagot](../tutorial-python.md) , amelyből megtudhatja, hogy mit tehet a részletes olvasó SDK-val a Python használatával
* Tekintse meg a [Swift-oktatóanyagot](../tutorial-ios-picture-immersive-reader.md) , amelyből megtudhatja, mit tehet a gyors
* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](../reference.md)
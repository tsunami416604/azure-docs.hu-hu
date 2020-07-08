---
title: Ismert Safari böngészővel kapcsolatos problémák (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft hitelesítési függvénytár JavaScripthez (MSAL.js) a Safari böngészővel való használatakor felmerülő problémákat.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76696112"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Ismert problémák a Safari böngészőben MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Csendes jogkivonat megújítása a Safari 12 és a ITP 2,0 esetében

Az Apple iOS 12 és MacOS 10,14 operációs rendszerek tartalmazzák a [Safari 12 böngésző](https://developer.apple.com/safari/whats-new/)kiadását. A biztonság és az adatvédelem érdekében a Safari 12 a 2,0-es [intelligens nyomkövetést](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)is tartalmazza. Ez lényegében azt eredményezi, hogy a böngésző el szeretné dobni a harmadik féltől származó cookie-k készletét. A ITP 2,0 az identitás-szolgáltatók által a harmadik féltől származó cookie-k által beállított cookie-kat is kezeli.

### <a name="impact-on-msaljs"></a>A MSAL.js gyakorolt hatás

A MSAL.js rejtett iframe használatával hajtja végre a csendes tokenek beszerzését és megújítását a hívások részeként `acquireTokenSilent` . A csendes jogkivonat-kérelmek arra támaszkodnak az IFRAME-re, hogy hozzáférjenek az Azure AD által beállított cookie-k által jelölt hitelesített felhasználói munkamenethez. Ha ITP 2,0 megakadályozza a cookie-k elérését, MSAL.js a tokenek csendes beszerzését és megújítását, és ez `acquireTokenSilent` hibát eredményez.

Ezen a ponton nincs megoldás erre a problémára, és a rendszer kiértékeli a közösségi szabványokkal kapcsolatos lehetőségeket.

### <a name="work-around"></a>Megkerülő megoldás

Alapértelmezés szerint a ITP beállítás engedélyezve van a Safari böngészőben. Ezt a beállítást letilthatja, ha a **Beállítások**  ->  **Adatvédelem** lehetőségre navigál, és törli a **helyek közötti nyomkövetés tiltása** lehetőséget.

![Safari-beállítás](./media/msal-js-known-issue-safari-browser/safari.png)

A `acquireTokenSilent` hibákat egy interaktív beszerzési jogkivonat hívásával kell kezelnie, amely felszólítja a felhasználót, hogy jelentkezzen be.
A ismételt bejelentkezések elkerülése érdekében a megvalósítható megközelítéssel kezelheti a hibát, `acquireTokenSilent` és megadhatja a felhasználónak, hogy letiltsa a itp-beállítást a Safariban az interaktív hívás folytatása előtt. Ha a beállítás le van tiltva, a következő csendes jogkivonat megújításának sikeresnek kell lennie.

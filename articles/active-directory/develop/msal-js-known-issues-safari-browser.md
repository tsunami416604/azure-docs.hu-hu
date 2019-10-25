---
title: Böngészőkkel kapcsolatos ismert problémák (Microsoft Authentication Library JavaScripthez)
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft hitelesítési függvénytár JavaScripthez (MSAL. js) való használatakor felmerülő problémákat a Safari böngészőben.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51d800ea2fbbc733a6213d7bc4f61f955612aba0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803065"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>A Safari böngészőben a MSAL. js-vel kapcsolatos ismert problémák 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Csendes jogkivonat megújítása a Safari 12 és a ITP 2,0 esetében

Az Apple iOS 12 és MacOS 10,14 operációs rendszerek tartalmazzák a [Safari 12 böngésző](https://developer.apple.com/safari/whats-new/)kiadását. A biztonság és az adatvédelem érdekében a Safari 12 a 2,0-es [intelligens nyomkövetést](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)is tartalmazza. Ez lényegében azt eredményezi, hogy a böngésző el szeretné dobni a harmadik féltől származó cookie-k készletét. A ITP 2,0 az identitás-szolgáltatók által a harmadik féltől származó cookie-k által beállított cookie-kat is kezeli.

### <a name="impact-on-msaljs"></a>A MSAL. js-re gyakorolt hatás

A MSAL. js egy rejtett iframe-t használ a `acquireTokenSilent`-hívások részeként a csendes jogkivonat beszerzésének és megújításának végrehajtásához. A csendes jogkivonat-kérelmek arra támaszkodnak az IFRAME-re, hogy hozzáférjenek az Azure AD által beállított cookie-k által jelölt hitelesített felhasználói munkamenethez. Ha ITP 2,0 megakadályozza a cookie-k elérését, a MSAL. js nem tudja lekérni és megújítani a tokeneket, és ez `acquireTokenSilent` hibákat eredményez.

Ezen a ponton nincs megoldás erre a problémára, és a rendszer kiértékeli a közösségi szabványokkal kapcsolatos lehetőségeket.

### <a name="work-around"></a>Megkerülő megoldás

Alapértelmezés szerint a ITP beállítás engedélyezve van a Safari böngészőben. Ezt a beállítást letilthatja, ha a **beállítások** -> az **Adatvédelem** lehetőségre navigál, és törli a **helyek közötti nyomkövetés tiltása** lehetőséget.

![Safari-beállítás](./media/msal-js-known-issue-safari-browser/safari.png)

Egy interaktív beszerzési jogkivonat-hívással kell kezelnie a `acquireTokenSilent` hibákat, amely felszólítja a felhasználót, hogy jelentkezzen be.
A ismételt bejelentkezések elkerülése érdekében a megvalósítható megközelítéssel kezelheti a `acquireTokenSilent` meghibásodását, és megadhatja a felhasználónak, hogy letiltsa a ITP-beállítást a Safariban az interaktív hívás folytatása előtt. Ha a beállítás le van tiltva, a következő csendes jogkivonat megújításának sikeresnek kell lennie.

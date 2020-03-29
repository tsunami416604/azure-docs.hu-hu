---
title: Ismert Safari böngésző problémák (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy milyen problémák merülnek fel a Microsoft JavaScript-hitelesítési könyvtárának (MSAL.js) Safari böngészővel való használata során.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696112"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Ismert problémák a Safari böngészőmsal 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Csendes tokenmegújítása safari 12 és ITP 2.0

Az Apple iOS 12 és macOS 10.14 operációs rendszerek a [Safari 12 böngésző](https://developer.apple.com/safari/whats-new/)kiadását tartalmazták. Biztonsági és adatvédelmi célból a Safari 12 tartalmazza az [Intelligens követésmegelőzés 2.0-s indexet.](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) Ez lényegében azt eredményezi, hogy a böngésző eldobja a harmadik féltől származó cookie-kat. Az ITP 2.0 az identitásszolgáltatók által beállított cookie-kat is harmadik féltől származó cookie-kként kezeli.

### <a name="impact-on-msaljs"></a>Az MSAL.js-re gyakorolt hatás

Az MSAL.js egy rejtett Iframe-et használ a `acquireTokenSilent` csendes tokenek beszerzéséhez és megújításához a hívások részeként. A csendes jogkivonat-kérelmek támaszkodnak az Iframe hozzáféréssel rendelkezik a hitelesített felhasználói munkamenet által képviselt Azure AD által beállított cookie-kat. Mivel az ITP 2.0 megakadályozza a cookie-khoz való hozzáférést, az MSAL.js nem szerzi meg és újítja meg a tokeneket, és ez hibákat eredményez. `acquireTokenSilent`

Jelenleg nincs megoldás erre a kérdésre, és a szabványok közösségével értékeljük a lehetőségeket.

### <a name="work-around"></a>Megkerülés

Alapértelmezés szerint az ITP-beállítás engedélyezve van a Safari böngészőben. Ezt a beállítást letilthatja, ha a **Beállítások adatvédelem** -> **Privacy** elemre navigál, és törölje a jelölőnégyzet bejelölését a Helyek közötti **követés megakadályozása** jelölőnégyzetből.

![szafari beállítás](./media/msal-js-known-issue-safari-browser/safari.png)

A `acquireTokenSilent` hibákat egy interaktív jogkivonat-hívással kell kezelnie, amely a felhasználó bejelentkezését kéri.
Az ismételt bejelentkezések elkerülése érdekében a hiba kezelése `acquireTokenSilent` és a felhasználó számára lehetővé teheti az ITP-beállítás letiltását a Safariban, mielőtt folytatná az interaktív hívást. Ha a beállítás le van tiltva, a későbbi csendes jogkivonat-megújítások sikeresnek kell lenniük.

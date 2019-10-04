---
title: Ismert problémák a böngészőkben (Microsoft-hitelesítési tár JavaScript-) |} Az Azure
description: Ismert problémák a Microsoft-hitelesítési tár JavaScript (MSAL.js) használatakor a ismerteti a Safari böngészőben.
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
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873885"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>A Safari böngésző az MSAL.js ismert problémák 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>A Safari 12 és ITP 2.0 csendes token megújítása

12\. az Apple iOS és MacOS 10.14 operációs rendszerek része egy kiadása a [12 a Safari böngésző](https://developer.apple.com/safari/whats-new/). Biztonsági és adatvédelmi okokból a Safari 12 tartalmazza a [intelligens követési megelőzési 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Ez lényegében hatására a böngésző dobja el a külső cookie-k beállítása alapján történik. ITP 2.0 is kezeli az identitás-szolgáltatóktól, külső cookie-k által beállított cookie-kat.

### <a name="impact-on-msaljs"></a>Impact on MSAL.js

MSAL.js rejtett Iframe használatával beavatkozás nélküli token beszerzése és megújítása során végrehajtott a `acquireTokenSilent` hívásokat. A beavatkozás nélküli jogkivonat-kérelmeket az IFRAME-keret férhető hozzá a hitelesített felhasználói munkamenetet a cookie-kat, állítsa be az Azure AD által képviselt támaszkodnak. ITP 2.0 letiltja a hozzáférést a cookie-kat, MSAL.js csendes beszerezni, és a jogkivonatok megújítása sikertelen lesz, és ennek eredményeként `acquireTokenSilent` hibák.

Nincs a probléma megoldása ezen a ponton, és hogy kipróbálja a beállítások a szabványok Közösséggel.

### <a name="work-around"></a>Megkerüléséhez

A ITP beállítás alapértelmezés szerint engedélyezve van a Safari böngészőben. Ezzel a beállítással letilthatja az **beállítások** -> **adatvédelmi** és az eszközhitelesítést az **többhelyes követési megakadályozása** lehetőséget.

![Safari-beállítás](./media/msal-js-known-issue-safari-browser/safari.png)

Kezelni kell a `acquireTokenSilent` rendelkező egy interaktív hibák beszerezni token hívás, amely felszólítja a felhasználót, hogy jelentkezzen be.
Ismétlődő bejelentkezések elkerülése érdekében az megközelítés valósítható meg, hogy kezelni a `acquireTokenSilent` hiba, és adja meg a felhasználó lehet kikapcsolni a Safari ITP beállítását az interaktív hívás végrehajtása előtt. Ha a beállítás le van tiltva, ezt követő csendes token megújításának sikeres legyen.

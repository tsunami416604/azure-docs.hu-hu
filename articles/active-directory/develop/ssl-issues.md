---
title: Az Objective-C-problémák MSAL hibaelhárítása | Microsoft Identity platform
description: Ismerje meg, hogy mi a teendő a MSAL SSL-tanúsítványokat használó különböző problémákról. Objective-C függvénytár.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76892686beec8ea18d56166519353fb5a2495124
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268907"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-ssl-issues"></a>Útmutató: IOS-és macOS-alapú SSL-problémák MSAL hibaelhárítása

Ez a cikk az [iOS és a MacOS rendszerhez készült Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) használata során esetlegesen előforduló problémák megoldásához nyújt segítséget.

## <a name="network-issues"></a>Hálózati problémák

**Hiba – 1200**: "SSL-hiba történt, és nem hozható létre biztonságos kapcsolat a kiszolgálóval."

Ez a hiba azt jelenti, hogy a kapcsolatok nem biztonságosak. Akkor következik be, amikor egy tanúsítvány érvénytelen. Ha további információra van szó, beleértve az SSL-ellenőrzési hibát okozó kiszolgálót `NSURLErrorFailingURLErrorKey` is, `userInfo` tekintse meg a következő témakört:.

Ez a hiba az Apple hálózatkezelési könyvtárából származik. A NSURL-hibakódok teljes listája a macOS és iOS SDK-ban található NSURLError. h nyelven érhető el. A hibával kapcsolatos további információkért tekintse meg a [rendszerhiba-kódok betöltését](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)ismertető témakört.

## <a name="certificate-issues"></a>Tanúsítványokkal kapcsolatos problémák

Ha egy érvénytelen tanúsítványt biztosító URL-cím csatlakozik a hitelesítési folyamat részeként használni kívánt kiszolgálóhoz, a probléma diagnosztizálásához érdemes megkezdeni az URL-cím tesztelését egy SSL-ellenőrző szolgáltatással, például a [QUALYS SSL Labs Analyzer](https://www.ssllabs.com/ssltest/analyze.html)használatával. A kiszolgálót a forgatókönyvek és böngészők széles körére teszteli, és számos ismert sebezhetőséget keres.

Alapértelmezés szerint az Apple új [app Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) funkciója szigorúbb biztonsági házirendeket alkalmaz az SSL-tanúsítványokat használó alkalmazásokra. Egyes operációs rendszerek és webböngészők alapértelmezés szerint megkezdték a házirendek érvényesítését. Biztonsági okokból javasoljuk, hogy ne tiltsa le az ATS-t.

Az SHA-1 kivonatokat használó tanúsítványok ismert biztonsági réseket tartalmaz. A legtöbb modern böngésző nem engedélyezi az SHA-1 kivonatokkal rendelkező tanúsítványokat.

## <a name="captive-portals"></a>Fogságban lévő portálok

A céges portál egy weblapot jelenít meg a felhasználónak, amikor először fér hozzá egy Wi-Fi-hálózathoz, és még nem kapott hozzáférést ehhez a hálózathoz. Elfogja az internetes forgalmat, amíg a felhasználó nem teljesíti a portál követelményeit. Hálózati hibák, mert a felhasználó nem tud csatlakozni a hálózati erőforrásokhoz, amíg a felhasználó nem csatlakozik a portálon.

## <a name="next-steps"></a>További lépések

Ismerje meg a [fogságban lévő portálokat](https://en.wikipedia.org/wiki/Captive_portal) és az Apple új [app TRANSPORT Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) szolgáltatását.

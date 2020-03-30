---
title: TLS/SSL problémák elhárítása (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: A cikk ismerteti, mi a teendő a TLS/SSL-tanúsítványok MSAL-mal való használatával kapcsolatos különböző problémákkal kapcsolatban. Objective-C könyvtár.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 42bd016a9d0882cc97eaa800235226b2de9569a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369403"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Útmutató: Az MSAL hibaelhárítása iOS és macOS TLS/SSL problémák esetén

Ez a cikk az iOS és a [macOS rendszerhez a Microsoft Hitelesítési könyvtár (MSAL)](reference-v2-libraries.md) használata során felmerülő problémák elhárításához nyújt segítséget

## <a name="network-issues"></a>Hálózati problémák

**-1200 hiba:**"SSL-hiba történt, és nem lehet biztonságos kapcsolatot létesíteni a kiszolgálóval."

Ez a hiba azt jelenti, hogy a kapcsolat nem biztonságos. Ez akkor fordul elő, ha egy tanúsítvány érvénytelen. További információt, beleértve azt is, hogy melyik kiszolgáló `NSURLErrorFailingURLErrorKey` nem `userInfo` felel meg a TLS-ellenőrzésen, olvassa el a hibaobjektum szótárában.

Ez a hiba az Apple hálózati könyvtárából származik. Az NSURL hibakódok teljes listája az NSURLError.h-ban található a macOS és az iOS SDK-kban. A hibával kapcsolatos további részleteket az [URL-címek betöltése rendszerhibakódok ban talál.](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)

## <a name="certificate-issues"></a>Tanúsítványokkal kapcsolatos problémák

Ha az érvénytelen tanúsítványt biztosító URL-cím csatlakozik a hitelesítési folyamat részeként használni kívánt kiszolgálóhoz, a probléma diagnosztizálása jó kezdet, ha az URL-címet egy SSL-érvényesítési szolgáltatással, például [ssl-kiszolgálóteszttel](https://www.ssllabs.com/ssltest/analyze.html)teszteli. A kiszolgálót a forgatókönyvek és böngészők széles skálája szerint teszteli, és számos ismert biztonsági rést keres.

Alapértelmezés szerint az Apple új [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) funkciója szigorúbb biztonsági szabályzatokat alkalmaz a TLS/SSL tanúsítványokat használó alkalmazásokra. Egyes operációs rendszerek és webböngészők alapértelmezés szerint megkezdték ezen házirendek némelyikének érvényesítését. Biztonsági okokból azt javasoljuk, hogy ne tiltsa le az ATS-t.

Az SHA-1 kivonatokat használó tanúsítványok ismert biztonsági résekkel rendelkeznek. A legtöbb modern webböngésző nem engedélyezi az SHA-1 kivonatokkal rendelkező tanúsítványokat.

## <a name="captive-portals"></a>Fogságban tartott portálok

A kötött portál egy weboldalt jelenít meg a felhasználónak, amikor először fér hozzá egy Wi-Fi hálózathoz, és még nem kapott hozzáférést a hálózathoz. Elfogja az internetes forgalmat, amíg a felhasználó nem felel meg a portál követelményeinek. Hálózati hibák, mert a felhasználó nem tud csatlakozni a hálózati erőforrásokhoz, amíg a felhasználó nem csatlakozik a portálon keresztül.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [fogságban tartott portálokról](https://en.wikipedia.org/wiki/Captive_portal) és az Apple új [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) funkciójáról.

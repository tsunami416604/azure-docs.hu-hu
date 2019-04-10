---
title: Frissítés az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Válassza ki, melyik proxy megoldás a legjobb, ha frissít, a Microsoft Forefront vagy egyesített Access-átjárón.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa143aac52fe0024620047eb67f24cc79e55c9b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279312"
---
# <a name="compare-remote-access-solutions"></a>Távelérési megoldások összehasonlítása

Az Azure Active Directory Application Proxy egyike a két távelérési megoldás, amely a Microsoft kínál. A másik pedig a webalkalmazás-Proxy, a helyileg telepített verzióját. Ezek a megoldások két cserélje le a korábbi, amely a Microsoft által kínált termékek: Microsoft Forefront Threat Management Gateway (TMG), és egységes Access-átjárón (UAG). Ez a cikk segítségével azonosíthatja a ezeket a megoldásokat hogyan hasonlítsa össze egymással. Azoknak, továbbra is használja az elavult TMG vagy a UAG megoldásokat Ez a cikk segítségével tervezze meg a migrációt a megfelelőt a Proxy segítségével. 


## <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

Ez a táblázat segítségével megismerheti, hogyan Threat Management Gateway (TMG), a Unified Access-átjáró (UAG), a webalkalmazás-proxykiszolgálóként (WAP) és az Azure AD Application Proxy (AP) hasonlítsa össze egymással.

| Szolgáltatás | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Tanúsítványalapú hitelesítés | Igen | Igen | - | - |
| Alkalmazások szelektív közzététele | Igen | Igen | Igen | Igen |
| Az előhitelesítést, és egyszeri bejelentkezés | Igen | Igen | Igen | Igen | 
| Tűzfal 2/3. réteg | Igen | Igen | - | - |
| Továbbítói proxy képességek | Igen | - | - | - |
| VPN-képességek | Igen | Igen | - | - |
| Gazdag protokoll támogatása | - | Igen | Igen, ha HTTP-n keresztül futtat | Igen, ha HTTP protokollon keresztül vagy a távoli asztali átjárón keresztül |
| ADFS-proxykiszolgálóra szolgál | - | Igen | Igen | - |
| Alkalmazás-hozzáférési egyetlen portálon | - | Igen | - | Igen |
| Válasz törzsében hivatkozás fordítása | Igen | Igen | - | Igen | 
| Hitelesítés a fejlécek | - | Igen | - | Igen, a pingaccess segítségével | 
| A felhőméretű biztonsági | - | - | - | Igen | 
| Feltételes hozzáférés | - | Igen | - | Igen |
| Nincsenek összetevők a demilitarizált zónában (DMZ) | - | - | - | Igen |
| Egy kimenő kapcsolatot sem | - | - | - | Igen |

A legtöbb esetben javasoljuk, hogy az Azure AD-alkalmazásproxy, a modern megoldása. A webalkalmazás-Proxy csak az előnyben részesített olyan esetekben proxykiszolgáló az AD FS-hez, és nem használhat egyéni tartományok az Azure Active Directoryban. 

Azure AD-alkalmazásproxy hasonló termékek, köztük a képest egyedi előnyöket kínálja:

- Az Azure AD a helyszíni erőforrásokhoz való kiterjesztése
   - A felhőméretű biztonság és védelem
   - Szolgáltatások, mint például a feltételes hozzáférés és a multi-factor Authentication hitelesítés is egyszerűen engedélyezése
- A demilitarizált zónában nincsenek összetevők
- Szükséges egy kimenő kapcsolatot sem
- Egy hozzáférési panelen, hogy a felhasználók is nyissa meg az összes alkalmazásokban, beleértve az Office 365, az Azure AD integrált SaaS-alkalmazások, és a helyszíni webalkalmazásokhoz. 


## <a name="next-steps"></a>További lépések

- [A helyszíni alkalmazások biztonságos távoli elérést biztosíthat az Azure AD-alkalmazás használatával](application-proxy.md)
- [Átállás a Forefront TMG és érdekében, hogy az alkalmazásproxy](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

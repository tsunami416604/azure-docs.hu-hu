---
title: Frissítés az Azure AD alkalmazásproxyra | Microsoft dokumentumok
description: Válassza ki, hogy melyik proxymegoldás a legjobb, ha a Microsoft Forefront vagy az Unified Access Gateway rendszerről frissít.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4790dc7ebeeee3407e89bcf38d7e3f25699ed328
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108412"
---
# <a name="compare-remote-access-solutions"></a>Távelérési megoldások összehasonlítása

Az Azure Active Directory alkalmazásproxy a Microsoft által elérhető két távelérési megoldás egyike. A másik a webalkalmazás-proxy, a helyszíni verzió. Ez a két megoldás a Microsoft által kínált korábbi termékekhelyébe lép: a Microsoft Forefront Threat Management Gateway (TMG) és az Unified Access Gateway (UAG). Ebből a cikkből megtudhatja, hogy ez a négy megoldás hogyan viszonyul egymáshoz. Azok számára, akik még mindig az elavult TMG vagy UAG megoldásokat használják, használja ezt a cikket az alkalmazásproxy egyikére való áttelepítés megtervezéséhez. 


## <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

Ebben a táblázatban megtudhatja, hogy a Veszélyforrások kezelése átjáró (TMG), az egyesített hozzáférésű átjáró (UAG), a webalkalmazás-proxy (WAP) és az Azure AD alkalmazásproxy (AP) hogyan viszonyul egymáshoz.

| Szolgáltatás | Tmg | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Tanúsítványhitelesítés | Igen | Igen | - | - |
| Böngészőalkalmazások szelektív közzététele | Igen | Igen | Igen | Igen |
| Előhitelesítés és egyszeri bejelentkezés | Igen | Igen | Igen | Igen | 
| 2/3. | Igen | Igen | - | - |
| Továbbítási proxyképességek | Igen | - | - | - |
| VPN-képességek | Igen | Igen | - | - |
| Gazdag protokolltámogatás | - | Igen | Igen, ha http-n fut | Igen, http-n vagy távoli asztali átjárón keresztül történő futtatás esetén |
| ADFS-proxykiszolgálóként szolgál | - | Igen | Igen | - |
| Egy portál az alkalmazás-hozzáféréshez | - | Igen | - | Igen |
| Választörzs hivatkozásfordítása | Igen | Igen | - | Igen | 
| Hitelesítés fejlécekkel | - | Igen | - | Igen, a PingAccess segítségével | 
| Felhőszintű biztonság | - | - | - | Igen | 
| Feltételes hozzáférés | - | Igen | - | Igen |
| Nincsenek alkatrészek a demilitarizált zónában (DMZ) | - | - | - | Igen |
| Nincs bejövő kapcsolat | - | - | - | Igen |

A legtöbb esetben azt javasoljuk, hogy az Azure AD alkalmazásproxy a modern megoldás. Webalkalmazás-proxy csak olyan esetekben, amelyek proxykiszolgálót igényelnek az AD FS,és nem használhat egyéni tartományokat az Azure Active Directoryban. 

Az Azure AD alkalmazásproxy egyedi előnyöket kínál a hasonló termékekhez képest, többek között a következőket:

- Az Azure AD kiterjesztése a helyszíni erőforrásokra
   - Felhőszintű biztonság és védelem
   - Az olyan funkciók, mint a feltételes hozzáférés és a többtényezős hitelesítés, könnyen engedélyezhetők
- Nincsenek alkatrészek a demilitarizált zónában
- Nincs szükség bejövő kapcsolatokra
- Egy hozzáférési panel, amelyet a felhasználók az összes alkalmazásukhoz használhatnak, beleértve az O365-öt, az Azure AD-vel integrált SaaS-alkalmazásokat és a helyszíni webalkalmazásokat. 


## <a name="next-steps"></a>További lépések

- [Az Azure AD alkalmazás használata a helyszíni alkalmazások biztonságos távoli eléréséhez](application-proxy.md)

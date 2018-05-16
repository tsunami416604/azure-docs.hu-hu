---
title: Frissítés az Azure AD-alkalmazásproxy |} Microsoft Docs
description: Válassza ki, melyik proxy megoldás esetén ajánlott, ha frissít, a Microsoft Forefront vagy egységes Access-átjárón.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cf1c39d0270dcb7de3ae8ff487b21874b23db74a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="compare-remote-access-solutions"></a>A távelérési megoldások összehasonlítása

Az Azure Active Directory Alkalmazásproxyjával egyike a két távelérési megoldás, amely a Microsoft biztosít. A másik pedig a webalkalmazás-Proxy, a helyszíni verzióira. Ezek a megoldások két cserélje le a korábbi termékek, amelyet a Microsoft kínál: Microsoft Forefront Threat Management Gateway (TMG) és az egységes Access-átjáró (UAG). Ez a cikk használatával megérthetik, hogy hogyan ezek a megoldások összehasonlítása egymáshoz. Azok az Ön továbbra is elavult TMG vagy UAG megoldások segítségével ez a cikk tervezze az áttelepítést az alkalmazásproxy egyikére. 


## <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

Ez a táblázat segítségével azonosíthatja, hogyan hasonlítsa össze a Threat Management Gateway (TMG), a Unified Access-átjáró (UAG), a webalkalmazás-proxykiszolgálóként (WAP) és az Azure AD Application Proxy (AP) egymással.

| Szolgáltatás | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Tanúsítványhitelesítés | Igen | Igen | - | - |
| Szelektív közzététel böngészőben megjelenő alkalmazásokba | Igen | Igen | Igen | Igen |
| Előhitelesítés és egyszeri bejelentkezés | Igen | Igen | Igen | Igen | 
| 2/3 tűzfal réteg | Igen | Igen | - | - |
| Előre proxyképességekkel | Igen | - | - | - |
| VPN-képességek | Igen | Igen | - | - |
| Gazdag protokoll támogatása | - | Igen | Igen, ha HTTP Protokollon keresztül fut | Igen, ha HTTP Protokollon keresztül vagy a távoli asztali átjárón keresztül futtatja |
| Az AD FS-proxy kiszolgálóként működik | - | Igen | Igen | - |
| Egy alkalmazás-hozzáférés-portál | - | Igen | - | Igen |
| Válasz törzsében hivatkozás fordítás | Igen | Igen | - | Igen | 
| A fejlécek hitelesítés | - | Igen | - | Igen, de PingAccess | 
| A felhőméretű biztonsági | - | - | - | Igen | 
| Feltételes hozzáférés | - | Igen | - | Igen |
| A demilitarizált zónában (DMZ) nincsenek összetevők | - | - | - | Igen |
| Bejövő kapcsolatok nem | - | - | - | Igen |

A legtöbb esetben ajánlott a modern megoldás az Azure AD-alkalmazást. Webalkalmazás-Proxy proxykiszolgáló szükséges az AD FS Szolgáltatásokhoz csak az előnyben részesített forgatókönyveket, és nem használhat egyéni tartományok az Azure Active Directoryban. 

Az Azure AD-alkalmazásproxy előnyökkel egyedi hasonló termékek, köztük a képest:

- Azure AD-be a helyszíni erőforrások kiterjesztése
   - A felhőméretű biztonság és védelem
   - Például a feltételes hozzáférés és a multi-factor Authentication szolgáltatások is könnyen engedélyezése
- Nincs componenet a demilitarizált zónában
- Bejövő kapcsolatok nem szükséges
- Egy hozzáférési panel, hogy a felhasználók az összes alkalmazásuk kezelésére, beleértve az Office 365 folytathatja, az Azure AD integrált Szolgáltatottszoftver-alkalmazásoknál, és a helyszíni webalkalmazások. 


## <a name="next-steps"></a>További lépések

- [A helyszíni alkalmazások biztonságos távoli hozzáférést biztosítanak az Azure AD-alkalmazás segítségével](application-proxy.md)
- [Átállás Forefront TMG és alkalmazásproxy UAG](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

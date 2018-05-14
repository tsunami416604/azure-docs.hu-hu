---
title: Egyszeri bejelentkezés alkalmazásproxy alkalmazáshoz való konfigurálása |} Microsoft Docs
description: Hogyan konfigurálhat egyszeri bejelentkezést az application proxy alkalmazáshoz gyorsan
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 09d17f9f70ef352fe8d44a16c0f78323153a8a1a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Egyszeri bejelentkezés alkalmazásproxy alkalmazáshoz való konfigurálása

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók csatlakozni egy alkalmazáshoz több alkalommal hitelesítése nélkül. Lehetővé teszi, hogy az egyetlen-hitelesítés létrejöjjön, Azure Active Directoryban, a felhőben, és lehetővé teszi, hogy a szolgáltatás vagy az összekötő megszemélyesíthet-e a felhasználót, hogy végezze el a további hitelesítési kihívás az alkalmazásból.

## <a name="how-to-configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása
Egyszeri bejelentkezés beállításához először győződjön meg arról, hogy az alkalmazás Azure Active Directoryn keresztül előhitelesítéshez van konfigurálva. A konfiguráció végrehajtását, lépjen **Azure Active Directory**  - &gt; **vállalati alkalmazások**  - &gt; **minden alkalmazás**   - &gt; Az alkalmazás  **- &gt; alkalmazásproxy**. Ezen a lapon lásd a "Előtti hitelesítés" mezőt, és győződjön meg arról, hogy az beállítása "az Azure Active Directory. 

Előtti hitelesítési módszerekkel kapcsolatos további információkért lásd a 4. lépését a [alkalmazás közzétételi dokumentum](manage-apps/application-proxy-publish-azure-portal.md).

   ![Azure-portálon előhitelesítési módszer](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Egyszeri bejelentkezési módok konfigurálása az alkalmazás Proxy alkalmazások
Az adott típusú egyszeri bejelentkezés konfigurálása. A bejelentkezés módszerek besorolt alapján milyen típusú hitelesítés a háttéralkalmazás használja. Alkalmazás Proxy támogatják a különböző alkalmazások háromféle bejelentkezés:

-   **Jelszó alapú bejelentkezés**: jelszó alapú bejelentkezés alkalmas bármely alkalmazás által használt felhasználónév és jelszó megadására be kellene jelentkeznie. Konfigurációs lépéseit a [jelszó-SSO konfigurációs dokumentáció](active-directory-enterprise-apps-whats-new-azure-portal.md#bring-your-own-password-sso-applications).

-   **Integrált Windows-hitelesítés**: az integrált Windows-hitelesítéssel (IWA) alkalmazások esetén az egyszeri bejelentkezés engedélyezve van a Kerberos által korlátozott delegálás (KCD). Ez a módszer az Active Directory megszemélyesíthet felhasználókat, és a küldési és fogadási jogkivonatokat a nevében Application Proxy összekötők engedélyezi. Kerberos által korlátozott Delegálás konfigurálása a részletek megtalálhatók a [egyszeri bejelentkezéshez a Kerberos által korlátozott Delegálás dokumentáció](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

-   **Fejléc-alapú bejelentkezés**: fejléc-alapú bejelentkezés az partnerség keresztül engedélyezhető, és néhány további beállításokra van szükség. További információkért a partneri kapcsolat áll fenn, és az egyszeri bejelentkezést használ fejlécek alkalmazáshoz való konfigurálásával kapcsolatos lépésenkénti útmutatót lásd: a [PingAccess az Azure AD-dokumentáció](application-proxy-ping-access.md).

Ezen lehetőségek találhatók az alkalmazás a "Vállalati alkalmazások" lesz, és megnyitása a **egyszeri bejelentkezés** oldalon, a bal oldali menüben. Vegye figyelembe, hogy ha az alkalmazás létrehozása a régi portál, előfordulhat, hogy nem látja ezeket a beállításokat.

Ez a lap is megjelenik egy további bejelentkezési beállítás: csatolt Sign-On. Alkalmazásproxy is támogatja ezt a lehetőséget. Azonban ez a beállítás nem adja hozzá az egyszeri bejelentkezés az alkalmazást. Az alkalmazás előfordulhat, hogy már rendelkezik egyszeri bejelentkezéshez egy másik szolgáltatás, például az Active Directory összevonási szolgáltatások használatával megvalósított említett. 

Ezzel a beállítással egy rendszergazda mutató hivatkozás létrehozásához az alkalmazás a felhasználók első föld meg az alkalmazáshoz való hozzáféréskor. Például ha egy alkalmazás, amely hitelesíti a felhasználókat az Active Directory összevonási szolgáltatások 2.0 eszköz használatával van konfigurálva, a rendszergazda segítségével a "csatolt bejelentkezési-" beállítás hozható létre hivatkozás azt a hozzáférési panelen.

## <a name="next-steps"></a>További lépések
[Adja meg az egyszeri bejelentkezés az alkalmazásokba a Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

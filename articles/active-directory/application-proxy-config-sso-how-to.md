---
title: "Egyszeri bejelentkezés alkalmazásproxy alkalmazáshoz való konfigurálása |} Microsoft Docs"
description: "Hogyan konfigurálhat egyszeri bejelentkezést az application proxy alkalmazáshoz gyorsan"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3fddcb9387017bfaf48199bdacd839bfc357a820
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Egyszeri bejelentkezés alkalmazásproxy alkalmazáshoz való konfigurálása

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók csatlakozni egy alkalmazáshoz több alkalommal hitelesítése nélkül. Lehetővé teszi, hogy az egyetlen-hitelesítés létrejöjjön, Azure Active Directoryban, a felhőben, és lehetővé teszi, hogy a szolgáltatás vagy az összekötő megszemélyesíthet-e a felhasználót, hogy végezze el a további hitelesítési kihívás az alkalmazásból.

## <a name="how-to-configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása
Egyszeri bejelentkezés beállításához először győződjön meg arról, hogy az alkalmazás Azure Active Directoryn keresztül előhitelesítéshez van konfigurálva. Ehhez nyissa meg a **Azure Active Directory**  - &gt; **vállalati alkalmazások**  - &gt; **összes alkalmazás**  - &gt; az alkalmazás  **- &gt; alkalmazásproxy**. Ezen a lapon lásd a "Előtti hitelesítés" mezőt, és győződjön meg arról, hogy az beállítása "az Azure Active Directory. 

Előtti hitelesítési módszerekkel kapcsolatos további információkért lásd: a negyedik lépése a [alkalmazás közzétételi dokumentum](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

   ![Előhitelesítési módszer az Azure portálon](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Egyszeri bejelentkezési módok konfigurálása az alkalmazás Proxy alkalmazások
Ezután konfiguráljuk az adott típusú egyszeri bejelentkezést. A bejelentkezés módszerek besorolt alapján milyen típusú hitelesítés a háttéralkalmazás használja. Alkalmazás Proxy alkalmazások bejelentkezés három típusú támogatja:

-   **Jelszó alapú bejelentkezés**: jelszó alapú bejelentkezés alkalmas bármely alkalmazás által használt felhasználónév és jelszó megadására való bejelentkezéshez. Konfigurációs lépések megtalálhatók a [jelszó-SSO konfigurációs dokumentáció](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#bring-your-own-password-sso-applications).

-   **Integrált Windows-hitelesítés**: az integrált Windows-hitelesítéssel (IWA) alkalmazások esetén az egyszeri bejelentkezés engedélyezve van a Kerberos által korlátozott delegálás (KCD). Így az alkalmazás Proxy összekötők engedély megszemélyesíthet felhasználókat, és a küldési és fogadási jogkivonatok helyettük végzi az Active Directoryban. Kerberos által korlátozott Delegálás konfigurálása a részletek megtalálhatók a [egyszeri bejelentkezéshez a Kerberos által korlátozott Delegálás dokumentáció](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd).

-   **Fejléc-alapú bejelentkezés**: fejléc-alapú bejelentkezés az partnerség keresztül engedélyezhető, és néhány további beállításokra van szükség. További információkért a partneri kapcsolat áll fenn, és az egyszeri bejelentkezést használ fejlécek alkalmazáshoz való konfigurálásával kapcsolatos lépésenkénti útmutatót lásd: a [PingAccess az Azure AD-dokumentáció](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access).

Ezen lehetőségek találhatók az alkalmazás a "Vállalati alkalmazások" lesz, és megnyitása a **egyszeri bejelentkezés** oldalon, a bal oldali menüben. Vegye figyelembe, hogy ha az alkalmazás létrehozása a régi portál, előfordulhat, hogy nem látja ezeket a beállításokat.

Ez a lap is megjelenik egy további bejelentkezési beállítás: csatolt Sign-On. Ez is támogatott alkalmazásproxy. Vegye figyelembe azonban, hogy ez a beállítás nem adja hozzá az egyszeri bejelentkezés az alkalmazást. Az alkalmazás előfordulhat, hogy már rendelkezik egyszeri bejelentkezéshez egy másik szolgáltatás, például az Active Directory összevonási szolgáltatások használatával megvalósított említett. 

Ezzel a beállítással egy rendszergazda mutató hivatkozás létrehozásához az alkalmazás a felhasználók első föld meg az alkalmazáshoz való hozzáféréskor. Például ha egy alkalmazás, amely hitelesíti a felhasználókat az Active Directory összevonási szolgáltatások 2.0 eszköz használatával van konfigurálva, a rendszergazda segítségével a "csatolt bejelentkezési-" beállítás hozható létre hivatkozás azt a hozzáférési panelen.

## <a name="next-steps"></a>Következő lépések
[Adja meg az egyszeri bejelentkezés az alkalmazásokba a Proxy](active-directory-application-proxy-sso-using-kcd.md)

---
title: Az alkalmazásproxy-alkalmazásba való egyszeri bejelentkezés konfigurálása
description: Az alkalmazásproxy-alkalmazás egyszeri bejelentkezése gyors konfigurálása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mimart
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76712013"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Az alkalmazásproxy-alkalmazásegyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók számára, hogy egy alkalmazás hitelesítése nélkül többször i. Lehetővé teszi, hogy az egyetlen hitelesítés a felhőben, az Azure Active Directory ellen, és lehetővé teszi, hogy a szolgáltatás vagy az összekötő megszemélyesíteni a felhasználót, hogy az alkalmazás további hitelesítési kihívások teljesítéséhez.

## <a name="how-to-configure-single-sign-on"></a>Egyszeri bejelentkezés beállítása
Az egyszeri szolgáltatás konfigurálásához először győződjön meg arról, hogy az alkalmazás az Azure Active Directoryn keresztüli előhitelesítéshez van konfigurálva. A konfiguráció megoldásához nyissa meg az **Azure Active Directory**  - &gt; **vállalati alkalmazások**  - &gt; **minden alkalmazás**  - &gt; az ** - &gt; alkalmazásalkalmazás-proxyját.** Ezen a lapon megjelenik az "Előhitelesítés" mező, és győződjön meg arról, hogy "Azure Active Directory" van beállítva. 

Az előhitelesítési módszerekről az [alkalmazásközzétételi dokumentum](application-proxy-add-on-premises-application.md)4.

   ![Előhitelesítési módszer az Azure Portalon](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Egyszeri bejelentkezési módok konfigurálása alkalmazásproxy-alkalmazásokhoz
Konfigurálja az egyszeri bejelentkezés adott típusát. A bejelentkezési módszerek besorolása a háttéralkalmazás által használt hitelesítés típusa alapján történik. Az alkalmazásproxy-alkalmazások háromféle bejelentkezést támogatnak:

-   **Jelszóalapú bejelentkezés:** Jelszóalapú bejelentkezés bármely olyan alkalmazáshoz használható, amely felhasználónév és jelszó mezőket használ a bejelentkezéshez. A konfigurációs lépések a [Jelszó konfigurálása egyszeri bejelentkezés egy Azure AD-gyűjteményalkalmazáshoz.](configure-password-single-sign-on-non-gallery-applications.md)

-   **Integrált Windows-hitelesítés**: Az integrált Windows-hitelesítést (IWA) használó alkalmazások esetében az egyszeri bejelentkezés a Kerberos korlátozott delegálásán (KCD) keresztül engedélyezve van. Ez a módszer engedélyt ad az Alkalmazásproxy-összekötőknek az Active Directoryban a felhasználók megszemélyesítésére, valamint jogkivonatok küldésére és fogadására a nevükben. A KCD konfigurálásával kapcsolatos részletek a [KCD dokumentációval történő egyszeri bejelentkezésben](application-proxy-configure-single-sign-on-with-kcd.md)találhatók.

-   **Fejlécalapú bejelentkezés: A**fejlécalapú bejelentkezés partnerkapcsolaton keresztül engedélyezve van, és további konfigurációt igényel. A partnerségrészleteiről és a proxyk konfigurálásáról a hitelesítéshez fejléceket használó alkalmazásba való egyszeri bejelentkezés részletes en című témakörben [olvashat.](application-proxy-configure-single-sign-on-with-ping-access.md)

-   **SAML egyszeri bejelentkezés:** Az SAML egyszeri bejelentkezéssel az Azure AD hitelesíti magát az alkalmazás a felhasználó Azure AD-fiókjával. Az Azure AD egy kapcsolatprotokollon keresztül közli a bejelentkezési adatokat az alkalmazással. Az SAML-alapú egyszeri bejelentkezéssel a felhasználókat az SAML-jogcímekben megadott szabályok alapján adott alkalmazásszerepkörökhöz képezheti. Az SAML egyszeri bejelentkezés beállításáról az SAML az [alkalmazásproxyval történő egyszeri bejelentkezésről](application-proxy-configure-single-sign-on-on-premises-apps.md)az SAML című témakörben talál.

Ezek a lehetőségek megtalálhatók az alkalmazás "Vállalati alkalmazások" menüben történő megnyitásával, majd a bal oldali **egyszeri bejelentkezés** i oldal megnyitásával. Vegye figyelembe, hogy ha az alkalmazás a régi portálon jött létre, előfordulhat, hogy nem látja ezeket a beállításokat.

Ezen az oldalon egy további bejelentkezési lehetőség is látható: Csatolt bejelentkezés. Ezt a beállítást az alkalmazásproxy is támogatja. Ez a beállítás azonban nem ad hozzá egyszeri bejelentkezést az alkalmazáshoz. Ez azt mondta, hogy az alkalmazás már rendelkezik egyszeri bejelentkezési implementálással egy másik szolgáltatás, például az Active Directory összevonási szolgáltatások használatával. 

Ez a beállítás lehetővé teszi, hogy egy rendszergazda hozzon létre egy hivatkozást egy alkalmazás, amely a felhasználók először földet az alkalmazás elérésekor. Ha például van egy olyan alkalmazás, amely úgy van beállítva, hogy hitelesítse a felhasználókat az Active Directory összevonási szolgáltatások 2.0 használatával, a rendszergazda a "Csatolt bejelentkezés" beállítással hozhat létre hivatkozást a hozzáférési panelen.

## <a name="next-steps"></a>További lépések
- [Jelszótároló az alkalmazásproxyval való egyszeri bejelentkezéshez](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos korlátozott delegálása az alkalmazásproxyval történő egyszeri bejelentkezéshez](application-proxy-configure-single-sign-on-with-kcd.md)
- [Fejlécalapú hitelesítés egyszeri bejelentkezéshez alkalmazásproxyval](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML az alkalmazásproxyval történő egyszeri bejelentkezéshez.](application-proxy-configure-single-sign-on-on-premises-apps.md)

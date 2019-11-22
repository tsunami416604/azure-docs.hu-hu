---
title: Egyszeri bejelentkezés konfigurálása alkalmazásproxy-alkalmazáshoz
description: Hogyan konfigurálhat egyszeri bejelentkezést az alkalmazásproxy-alkalmazáshoz való gyors
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
ms.openlocfilehash: 8b5eca08e0b736937af42e58d81148636e3269df
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275635"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Egyszeri bejelentkezés egy alkalmazásproxy-alkalmazás konfigurálása

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók hozzáférni egy alkalmazáshoz több alkalommal hitelesítése nélkül. Ez lehetővé teszi, hogy az egyetlen-hitelesítés a felhőben, Azure Active Directory, és lehetővé teszi, hogy a szolgáltatás vagy az összekötő megszemélyesíteni a felhasználót, hogy végezze el az alkalmazás minden olyan további hitelesítési kihívásokat.

## <a name="how-to-configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása
Egyszeri bejelentkezés konfigurálásához először győződjön meg arról, hogy az alkalmazás konfigurálása Azure Active Directoryn keresztül az előhitelesítési. A konfiguráció végrehajtását, lépjen a **Azure Active Directory**  - &gt; **vállalati alkalmazások**  - &gt; **minden alkalmazás**   - &gt; Az alkalmazás  **- &gt; alkalmazásproxy**. Ezen a lapon lásd a "Előtti hitelesítés" mezőt, és győződjön meg arról, hogy állítsa be az "Azure Active Directory. 

Üzem előtti hitelesítési módszerekkel kapcsolatos további információkért lásd: 4. lépését a [közzétételi Alkalmazásdokumentum](application-proxy-add-on-premises-application.md).

   ![Az előhitelesítési módszer az Azure Portalon](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Egyszeri bejelentkezési módok konfigurálása az Application Proxy alkalmazásai
Az adott típusú egyszeri bejelentkezés konfigurálása. A bejelentkezési módszerek besorolt alapján a hitelesítés típusát a háttéralkalmazás használja. Alkalmazásproxy-alkalmazások bejelentkezési három típusát támogatja:

-   **Jelszóalapú bejelentkezés**: jelszóalapú bejelentkezés használható felhasználónév és jelszó mezők bejelentkezhet a minden alkalmazás esetén. Az szükséges konfigurációs lépéseket [jelszó egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazásba konfigurálása](configure-password-single-sign-on-gallery-applications.md).

-   **Integrált Windows-hitelesítés**: integrált Windows-hitelesítés (IWA) használó alkalmazásokhoz, az egyszeri bejelentkezés engedélyezve van – a Kerberos által korlátozott delegálás (KCD). Ez a módszer alkalmazásproxy-összekötők engedélyt ad az Active Directory megszemélyesíthet felhasználókat, és a küldési és fogadási jogkivonatokat a felhasználók nevében. KCD konfigurálása a részletek megtalálhatók a [egyszeri bejelentkezés KCD dokumentációban](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Fejlécalapú bejelentkezés**: fejlécalapú bejelentkezés kialakított partnerség révén engedélyezve van, és a további konfigurálást igényel. További információ a partneri kapcsolat és a részletes utasításokat a egyszeri bejelentkezést egy alkalmazáshoz, amely a fejlécek használja a hitelesítéshez, tekintse meg a [dokumentáció az Azure AD-alapú PingAccess](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **SAML egyszeri bejelentkezés**: az SAML egyszeri bejelentkezés használatával az Azure ad hitelesíti az alkalmazást a felhasználó Azure ad-fiókjával. Azure ad-ben a bejelentkezési adatait az alkalmazás egy kapcsolat protokollon keresztül kommunikál. Az SAML-alapú egyszeri bejelentkezéssel az SAML-jogcímek által meghatározott szabályok alapján leképezheti a felhasználókat bizonyos alkalmazási szerepkörökre. Az SAML egyszeri bejelentkezés beállításával kapcsolatos információkért lásd: [az egyszeri bejelentkezés SAML-szolgáltatása az alkalmazásproxy](application-proxy-configure-single-sign-on-on-premises-apps.md)használatával.

A felsorolt lehetőségek találhatók fogja az alkalmazás a "Nagyvállalati alkalmazások", és nyissa meg a **egyszeri bejelentkezés** lapra a bal oldali menüben. Vegye figyelembe, hogy ha az alkalmazás a régi portálon lett létrehozva, akkor előfordulhat, hogy nem látja ezeket a lehetőségeket.

Ezen a lapon is megjelenik egy további bejelentkezési lehetőség: csatolt bejelentkezés. Ez a beállítás az alkalmazásproxy is támogatott. Azonban ez a beállítás nem adható hozzá egyszeri bejelentkezést az alkalmazást. Ugyanakkor az alkalmazás lehetséges, hogy már rendelkezik egyszeri bejelentkezés egy másik szolgáltatás, például az Active Directory összevonási szolgáltatások használatával implementált. 

Ez a beállítás lehetővé teszi, hogy egy rendszergazda hozhat létre egy alkalmazást a felhasználók első föld a elérésekor az alkalmazás. Például ha egy alkalmazás, amely hitelesíti a felhasználót az Active Directory összevonási szolgáltatások 2.0 van beállítva, a rendszergazda segítségével a "csatolt bejelentkezés" lehetőséget a hozzáférési panelen, egy hivatkozás létrehozásához.

## <a name="next-steps"></a>Következő lépések
- [Jelszó-tároló egyszeri bejelentkezéshez alkalmazásproxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos által korlátozott delegálás az Application proxyval való egyszeri bejelentkezéshez](application-proxy-configure-single-sign-on-with-kcd.md)
- [Fejléc-alapú hitelesítés egyszeri bejelentkezéshez alkalmazásproxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML egyszeri bejelentkezéshez alkalmazásproxy](application-proxy-configure-single-sign-on-on-premises-apps.md)használatával.

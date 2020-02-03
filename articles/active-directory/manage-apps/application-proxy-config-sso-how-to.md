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
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712013"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Egyszeri bejelentkezés egy alkalmazásproxy-alkalmazás konfigurálása

Egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók hozzáférni egy alkalmazáshoz több alkalommal hitelesítése nélkül. Ez lehetővé teszi, hogy az egyetlen-hitelesítés a felhőben, Azure Active Directory, és lehetővé teszi, hogy a szolgáltatás vagy az összekötő megszemélyesíteni a felhasználót, hogy végezze el az alkalmazás minden olyan további hitelesítési kihívásokat.

## <a name="how-to-configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása
Egyszeri bejelentkezés konfigurálásához először győződjön meg arról, hogy az alkalmazás konfigurálása Azure Active Directoryn keresztül az előhitelesítési. Ehhez a konfigurációhoz nyissa meg a **Azure Active Directory** -&gt; **vállalati alkalmazások** -&gt; **minden alkalmazás** -&gt; az alkalmazás- **&gt; alkalmazásproxy**. Ezen a lapon lásd a "Előtti hitelesítés" mezőt, és győződjön meg arról, hogy állítsa be az "Azure Active Directory. 

Az előhitelesítési módszerekkel kapcsolatos további információkért tekintse meg az [alkalmazás közzétételi dokumentumának](application-proxy-add-on-premises-application.md)4. lépését.

   ![Az előhitelesítési módszer az Azure Portalon](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Egyszeri bejelentkezési módok konfigurálása az Application Proxy alkalmazásai
Az adott típusú egyszeri bejelentkezés konfigurálása. A bejelentkezési módszerek besorolt alapján a hitelesítés típusát a háttéralkalmazás használja. Alkalmazásproxy-alkalmazások bejelentkezési három típusát támogatja:

-   **Jelszó alapú bejelentkezés**: a jelszó-alapú bejelentkezés bármely olyan alkalmazáshoz használható, amely Felhasználónév és jelszó mezőket használ a bejelentkezéshez. A konfigurációs lépések a [jelszó egyszeri bejelentkezés konfigurálása Azure ad Gallery-alkalmazásokhoz](configure-password-single-sign-on-non-gallery-applications.md)című témakörben találhatók.

-   **Integrált Windows-hitelesítés**: integrált Windows-HITELESÍTÉST (IWA) használó alkalmazások esetén az egyszeri bejelentkezés a Kerberos által korlátozott delegáláson (KCD) keresztül engedélyezett. Ez a módszer alkalmazásproxy-összekötők engedélyt ad az Active Directory megszemélyesíthet felhasználókat, és a küldési és fogadási jogkivonatokat a felhasználók nevében. A KCD konfigurálásával kapcsolatos részletek a [KCD dokumentációjában található egyszeri bejelentkezésben](application-proxy-configure-single-sign-on-with-kcd.md)találhatók.

-   **Fejléc-alapú bejelentkezés**: a fejléc-alapú bejelentkezés egy partneren keresztül érhető el, és további konfigurálásra van szükség. Az egyszeri bejelentkezés olyan alkalmazásra való konfigurálásával kapcsolatos részletekért, amely fejléceket használ a hitelesítéshez, tekintse meg a [PingAccess for Azure ad dokumentációját](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **SAML egyszeri bejelentkezés**: az SAML egyszeri bejelentkezés használatával az Azure ad hitelesíti az alkalmazást a felhasználó Azure ad-fiókjával. Azure ad-ben a bejelentkezési adatait az alkalmazás egy kapcsolat protokollon keresztül kommunikál. Az SAML-alapú egyszeri bejelentkezéssel az SAML-jogcímek által meghatározott szabályok alapján leképezheti a felhasználókat bizonyos alkalmazási szerepkörökre. Az SAML egyszeri bejelentkezés beállításával kapcsolatos információkért lásd: [az egyszeri bejelentkezés SAML-szolgáltatása az alkalmazásproxy](application-proxy-configure-single-sign-on-on-premises-apps.md)használatával.

Az egyes lehetőségek a "vállalati alkalmazások" alkalmazásban, a bal oldali menüben pedig az **egyszeri bejelentkezési** oldal megnyitásával érhetők el. Vegye figyelembe, hogy ha az alkalmazás a régi portálon lett létrehozva, akkor előfordulhat, hogy nem látja ezeket a lehetőségeket.

Ezen a lapon is megjelenik egy további bejelentkezési lehetőség: csatolt bejelentkezés. Ez a beállítás az alkalmazásproxy is támogatott. Azonban ez a beállítás nem adható hozzá egyszeri bejelentkezést az alkalmazást. Ugyanakkor az alkalmazás lehetséges, hogy már rendelkezik egyszeri bejelentkezés egy másik szolgáltatás, például az Active Directory összevonási szolgáltatások használatával implementált. 

Ez a beállítás lehetővé teszi, hogy egy rendszergazda hozhat létre egy alkalmazást a felhasználók első föld a elérésekor az alkalmazás. Például ha egy alkalmazás, amely hitelesíti a felhasználót az Active Directory összevonási szolgáltatások 2.0 van beállítva, a rendszergazda segítségével a "csatolt bejelentkezés" lehetőséget a hozzáférési panelen, egy hivatkozás létrehozásához.

## <a name="next-steps"></a>Következő lépések
- [Jelszó-tároló egyszeri bejelentkezéshez alkalmazásproxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos által korlátozott delegálás az Application proxyval való egyszeri bejelentkezéshez](application-proxy-configure-single-sign-on-with-kcd.md)
- [Fejléc-alapú hitelesítés egyszeri bejelentkezéshez alkalmazásproxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML egyszeri bejelentkezéshez alkalmazásproxy](application-proxy-configure-single-sign-on-on-premises-apps.md)használatával.

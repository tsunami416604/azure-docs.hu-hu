---
title: Egyszeri bejelentkezés konfigurálása alkalmazásproxy-alkalmazáshoz
description: Hogyan konfigurálhatja az egyszeri bejelentkezést az alkalmazásproxy-alkalmazásba gyorsan
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/12/2019
ms.author: kenwith
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb3d1d921be858531372130264e777f8c04a9328
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764944"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Egyszeri bejelentkezés konfigurálása alkalmazásproxy-alkalmazáshoz

Az egyszeri bejelentkezés (SSO) lehetővé teszi a felhasználók számára, hogy többszöri hitelesítés nélkül férhessenek hozzá az alkalmazásokhoz. Lehetővé teszi, hogy az egyszeri hitelesítés a felhőben, a Azure Active Directoryon történjen, és lehetővé teszi, hogy a szolgáltatás vagy-összekötő megszemélyesítse a felhasználót az alkalmazás további hitelesítési kihívásainak teljesítéséhez.

## <a name="how-to-configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása
Az egyszeri bejelentkezés konfigurálásához először ellenőrizze, hogy az alkalmazás konfigurálva van-e az előhitelesítéshez Azure Active Directoryon keresztül. A konfiguráció elvégzéséhez nyissa meg **Azure Active Directory**  - &gt; **vállalati alkalmazások**  - &gt; **minden** alkalmazás  - &gt; az Application ** - &gt; Application proxyt**. Ezen az oldalon látható az "előzetes hitelesítés" mező, és győződjön meg arról, hogy a "Azure Active Directory. 

Az előhitelesítési módszerekkel kapcsolatos további információkért tekintse meg az [alkalmazás közzétételi dokumentumának](application-proxy-add-on-premises-application.md)4. lépését.

   ![Előhitelesítési módszer a Azure Portalban](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Az egyszeri bejelentkezés üzemmódjának konfigurálása alkalmazásproxy-alkalmazásokhoz
Konfigurálja az adott típusú egyszeri bejelentkezést. A bejelentkezési módszerek besorolása a háttérbeli alkalmazás által használt hitelesítési típus alapján történik. Az App proxy-alkalmazások háromféle bejelentkezési típust támogatnak:

-   **Jelszó alapú bejelentkezés**: a jelszó-alapú bejelentkezés bármely olyan alkalmazáshoz használható, amely Felhasználónév és jelszó mezőket használ a bejelentkezéshez. A konfigurációs lépések a [jelszó egyszeri bejelentkezés konfigurálása Azure ad Gallery-alkalmazásokhoz](configure-password-single-sign-on-non-gallery-applications.md)című témakörben találhatók.

-   **Integrált Windows-hitelesítés**: integrált Windows-HITELESÍTÉST (IWA) használó alkalmazások esetén az egyszeri bejelentkezés a Kerberos által korlátozott delegáláson (KCD) keresztül engedélyezett. Ez a módszer lehetővé teszi az alkalmazásproxy-összekötők számára az Active Directory a felhasználók megszemélyesítését, valamint a jogkivonatok küldését és fogadását a nevükben. A KCD konfigurálásával kapcsolatos részletek a [KCD dokumentációjában található egyszeri bejelentkezésben](application-proxy-configure-single-sign-on-with-kcd.md)találhatók.

-   **Fejléc-alapú bejelentkezés**: a fejléc-alapú bejelentkezés egy partneren keresztül érhető el, és további konfigurálásra van szükség. Az egyszeri bejelentkezés olyan alkalmazásra való konfigurálásával kapcsolatos részletekért, amely fejléceket használ a hitelesítéshez, tekintse meg a [PingAccess for Azure ad dokumentációját](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **SAML egyszeri bejelentkezés**: az SAML egyszeri bejelentkezés használatával az Azure ad hitelesíti az alkalmazást a felhasználó Azure ad-fiókjával. Az Azure AD a bejelentkezési adatokat kapcsolati protokollon keresztül továbbítja az alkalmazásnak. Az SAML-alapú egyszeri bejelentkezéssel az SAML-jogcímek által meghatározott szabályok alapján leképezheti a felhasználókat bizonyos alkalmazási szerepkörökre. Az SAML egyszeri bejelentkezés beállításával kapcsolatos információkért lásd: [az egyszeri bejelentkezés SAML-szolgáltatása az alkalmazásproxy](application-proxy-configure-single-sign-on-on-premises-apps.md)használatával.

Az egyes lehetőségek a "vállalati alkalmazások" alkalmazásban, a bal oldali menüben pedig az **egyszeri bejelentkezési** oldal megnyitásával érhetők el. Vegye figyelembe, hogy ha az alkalmazás a régi portálon lett létrehozva, akkor előfordulhat, hogy nem látja ezeket a lehetőségeket.

Ezen az oldalon egy további bejelentkezési lehetőség is látható: a csatolt bejelentkezés. Ezt a beállítást a alkalmazásproxy is támogatja. Ez a beállítás azonban nem ad hozzá egyszeri bejelentkezést az alkalmazáshoz. Az is előfordulhat, hogy az alkalmazás már rendelkezik egy másik szolgáltatás, például a Active Directory összevonási szolgáltatások (AD FS) használatával történő egyszeri bejelentkezéssel. 

Ez a beállítás lehetővé teszi, hogy a rendszergazda olyan alkalmazásra mutató hivatkozást hozzon létre, amelyet a felhasználók először az alkalmazáshoz való hozzáféréskor érnek el. Ha például van egy olyan alkalmazás, amely a felhasználók hitelesítésére van konfigurálva a Active Directory összevonási szolgáltatások (AD FS) 2,0 használatával, a rendszergazda a "csatolt bejelentkezés" lehetőség használatával létrehozhat egy hivatkozást a hozzáférési panelen.

## <a name="next-steps"></a>További lépések
- [Jelszó-tároló egyszeri bejelentkezéshez alkalmazásproxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos által korlátozott delegálás az Application proxyval való egyszeri bejelentkezéshez](application-proxy-configure-single-sign-on-with-kcd.md)
- [Fejléc-alapú hitelesítés egyszeri bejelentkezéshez alkalmazásproxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML egyszeri bejelentkezéshez alkalmazásproxy](application-proxy-configure-single-sign-on-on-premises-apps.md)használatával.

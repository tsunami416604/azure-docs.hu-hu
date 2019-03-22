---
title: SAML egyszeri bejelentkezést a helyszíni alkalmazások az Azure Active Directory Application Proxy (előzetes verzió) |} A Microsoft Docs
description: Ismerje meg, hogyan egyszeri bejelentkezést biztosít a helyszíni alkalmazásokat, amelyeket az SAML-hitelesítés alkalmazásproxyn keresztül közzétett.
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
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 415b33dce42945c40aedd996d4dcfa5c6b987b44
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336218"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML egyszeri bejelentkezést az alkalmazásproxy (előzetes verzió) használatával a helyszíni alkalmazások

Az SAML-hitelesítés biztonságosak, és ezeket az alkalmazásokat alkalmazásproxyn keresztül távoli elérést biztosíthat a helyszíni alkalmazásokhoz biztosíthat egyszeri bejelentkezés (SSO). SAML egyszeri bejelentkezéshez, az Azure Active Directory (Azure AD) hitelesíti magát az alkalmazást a felhasználó Azure AD-fiók használatával. Azure ad-ben a bejelentkezési adatait az alkalmazás egy kapcsolat protokollon keresztül kommunikál. Felhasználók is leképezheti az SAML-jogcímek az meghatározott szabályok alapján adott alkalmazás-szerepkörökhöz. Alkalmazásproxy mellett SAML egyszeri bejelentkezés engedélyezésével a felhasználók hozzáférhetnek külső az alkalmazás és a egy zökkenőmentes egyszeri Bejelentkezéses felhasználói élmény.

Az alkalmazások által kiállított SAML-jogkivonatokat használják képesnek kell lennie **Azure Active Directory**. Ez a konfiguráció egy helyszíni identitásszolgáltatót használó alkalmazások nem vonatkozik. Ebben az esetben ajánlott áttekinteni [erőforrások áttelepítése az Azure AD-alkalmazások](migration-resources.md).

SAML egyszeri Bejelentkezést az alkalmazásproxy használatával a SAML-jogkivonat titkosítása funkció is működik. További információ: [konfigurálása az Azure AD SAML-jogkivonat titkosítása](howto-saml-token-encryption.md).

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Az alkalmazásproxy használatával a helyszíni alkalmazás közzététele

Egyszeri bejelentkezés a helyszíni alkalmazások megadhat, előtt győződjön meg arról, az alkalmazásproxy engedélyezve van, és a egy összekötő telepítve van. Lásd: [távelérés alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása az Azure ad-ben](application-proxy-add-on-premises-application.md) megtudhatja, hogyan.

Fog az oktatóanyag során vegye figyelembe a következőket:

* Tegye közzé alkalmazását az utasításoknak megfelelően a jelen oktatóanyagban. Ügyeljen arra, hogy válassza ki, hogy **Azure Active Directory** , a **előhitelesítés** metódus az alkalmazás (4. lépés: [helyszíni alkalmazás hozzáadása az Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)).
* Másolás a **külső URL-cím** az alkalmazáshoz.
* Ajánlott eljárásként használja az egyéni tartományok, amikor csak lehetséges optimalizált felhasználói élményt biztosít. Tudjon meg többet [egyéni tartományok használata az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md).
* Adjon hozzá legalább egy felhasználót az alkalmazáshoz, és ellenőrizze, hogy a teszt a helyszíni alkalmazás hozzáféréssel rendelkezik.

## <a name="set-up-saml-sso"></a>Az SAML egyszeri bejelentkezés beállítása

1. Az Azure Portalon válassza ki a **Azure Active Directory > Vállalati alkalmazások** válassza ki az alkalmazást a listából.
1. Az alkalmazás **áttekintése** lapon jelölje be **egyszeri bejelentkezési**.
1. Válassza ki **SAML** egyszeri bejelentkezési módszert.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon, a Szerkesztés a **alapszintű SAML-konfigurációja** adatokat, és kövesse a [Enter alapszintű SAML-konfigurációja](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on) SAML-alapú konfigurálása az alkalmazás hitelesítést.

   * Győződjön meg arról, hogy a **válasz URL-cím** legfelső szintű megegyezik, vagy az elérési út alapján a **külső URL-cím** a helyszíni alkalmazás, amely az Azure AD-alkalmazásproxyn keresztül távoli hozzáféréshez hozzáadott.

     ![Alapszintű SAML-konfigurációs adatok megadása](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > Ha a háttéralkalmazás vár a **válasz URL-cím** a saját alkalmazások biztonságos bejelentkezési bővítménye telepítéséhez a felhasználók eszközeire kell lennie a belső URL-cím,. Ezzel a bővítménnyel automatikusan átirányítja a megfelelő Application Proxy szolgáltatáshoz. A bővítmény telepítése: [saját alkalmazások biztonságos bejelentkezési bővítménye](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

## <a name="test-your-app"></a>Az alkalmazás tesztelése

Ha végrehajtotta ezeket a lépéseket, az alkalmazás működik és kell lennie. Az alkalmazás teszteléséhez:

1. Nyisson meg egy böngészőt, és keresse meg a külső URL-cím, az alkalmazás közzétételekor létrehozott. 
1. Jelentkezzen be a teszt-fiókkal, amelyet az alkalmazáshoz rendelt.

## <a name="next-steps"></a>További lépések

- [Hogyan nyújt az Azure AD-alkalmazásproxy egyszeri bejelentkezést?](application-proxy-single-sign-on.md)
- [Az alkalmazásproxy hibaelhárítása](application-proxy-troubleshoot.md)

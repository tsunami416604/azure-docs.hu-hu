---
title: SAML egyszeri bejelentkezés helyszíni alkalmazásokhoz az Azure AD App Proxy használatával
description: Ismerje meg, hogyan biztosíthat egyszeri bejelentkezést az SAML-hitelesítéssel védett helyszíni alkalmazásokhoz. Távoli hozzáférést biztosíthat a helyszíni alkalmazásokhoz alkalmazásproxyval.
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
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3d2117e913f292e92f37f31d2e123587c70a189
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803297"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>SAML egyszeri bejelentkezés helyszíni alkalmazásokhoz alkalmazásproxyval

Egyszeri bejelentkezés (SSO) biztosíthatja a helyszíni alkalmazások, amelyek SAML-hitelesítéssel védett, és távoli hozzáférést biztosít ezekhez az alkalmazásokhoz alkalmazásproxyn keresztül. Saml egyszeri bejelentkezéssel az Azure Active Directory (Azure AD) hitelesíti magát az alkalmazás a felhasználó Azure AD-fiókjával. Az Azure AD egy kapcsolatprotokollon keresztül közli a bejelentkezési adatokat az alkalmazással. A felhasználókat az SAML-jogcímekben megadott szabályok alapján is hozzárendelje adott alkalmazásszerepkörökhöz. Az alkalmazásproxy engedélyezése mellett SAML Egyszeri bejelentkezés, a felhasználók külső hozzáférést biztosít az alkalmazáshoz, és a zökkenőmentes Egyszeri bejelentkezés élmény.

Az alkalmazásoknak képesnek kell lenniük az Azure Active Directory által kibocsátott SAML-tokenek **felhasználására.** Ez a konfiguráció nem vonatkozik a helyszíni identitásszolgáltatót használó alkalmazásokra. Ezekben a forgatókönyvekben azt javasoljuk, hogy tekintse át [az erőforrásokat az alkalmazások Azure AD-be való áttelepítéséhez.](migration-resources.md)

SAML SSO alkalmazásproxyval is működik az SAML token titkosítási szolgáltatás. További információ: [Az Azure AD SAML tokentitkosítás konfigurálása.](howto-saml-token-encryption.md)

Az alábbi protokolldiagramok a szolgáltató által kezdeményezett (SP által kezdeményezett) folyamat és az identitásszolgáltató által kezdeményezett (IdP által kezdeményezett) folyamat egyszeri bejelentkezési sorrendjét írják le. Az alkalmazásproxy az SAML Egyszeri kezelővel együttműködve gyorsítótárazza az SAML-kérelmet, valamint a helyszíni alkalmazáshoz és a helyszíni alkalmazásból érkező válasznak a gyorsítótárazását.

  ![SAML SP-folyamat](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP-folyamat](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Alkalmazás létrehozása és SAML SSO beállítása

1. Az Azure Portalon válassza az **Azure Active Directory > Enterprise alkalmazásokat,** majd az Új **alkalmazás**lehetőséget.

2. Adja meg az új alkalmazás megjelenítendő nevét, válassza **a Gyűjteményben nem található bármely más alkalmazás integrálása**lehetőséget, majd válassza a **Létrehozás lehetőséget.**

3. Az alkalmazás **Áttekintés lapján** válassza az **Egyszeri bejelentkezés**lehetőséget.

4. Válassza az **SAML-t** egyszeri bejelentkezési módszerként.

5. Először állítsa be az SAML SSO-t, hogy működjön a vállalati hálózaton. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon lépjen az **Egyszerű SAML konfigurációfejlécre,** és válassza ki a **Szerkesztés** ikont (egy ceruzát). Kövesse az [Alapvető SAML-konfiguráció megadása](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) az alkalmazás SAML-alapú hitelesítésének konfigurálásához kövesse az Enter basic SAML konfiguráció című lépéseit.

6. Adjon hozzá legalább egy felhasználót az alkalmazáshoz, és győződjön meg arról, hogy a tesztfiók hozzáfér az alkalmazáshoz. A vállalati hálózathoz való csatlakozás közben a tesztfiók segítségével ellenőrizze, hogy rendelkezik-e egyszeri bejelentkezéssel az alkalmazásba. 

   > [!NOTE]
   > Az alkalmazásproxy beállítása után visszajön, és frissíti az SAML **válasz URL-címét.**

## <a name="publish-the-on-premises-application-with-application-proxy"></a>A helyszíni alkalmazás közzététele alkalmazásproxyval

Mielőtt egyszeri használatú alkalmazások at biztosíthatna, engedélyeznie kell az alkalmazásproxyt, és telepítenie kell egy összekötőt. Tekintse meg az oktatóanyag [hozzáadása egy helyszíni alkalmazás távoli hozzáférés az Azure AD-ben,](application-proxy-add-on-premises-application.md) hogy megtanulják, hogyan készítheti el a helyszíni környezetben, telepítése és regisztrálása egy összekötőt, és tesztelje az összekötőt. Ezután hajtsa végre az alábbi lépéseket az új alkalmazás alkalmazásproxyval történő közzétételéhez. Az alább nem említett egyéb beállításokról tekintse meg a Helyszíni alkalmazás hozzáadása az [Azure AD-hez](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) című szakaszt az oktatóanyagban.

1. Ha az alkalmazás még mindig meg van nyitva az Azure Portalon, válassza **az Alkalmazásproxy**lehetőséget. Adja meg az alkalmazás **belső URL-címét.** Ha egyéni tartományt használ, az alkalmazásHoz fel kell töltenie a TLS/SSL tanúsítványt is. 
   > [!NOTE]
   > Ajánlott eljárásként amikor csak lehetséges, használjon egyéni tartományokat az optimalizált felhasználói élmény érdekében. További információ az [egyéni tartományok használata az Azure AD alkalmazásproxyban.](application-proxy-configure-custom-domain.md)

2. Válassza ki az **Azure Active Directoryt** az alkalmazás **előhitelesítési** módszereként.

3. Másolja az alkalmazás **külső URL-címét.** Az SAML-konfiguráció befejezéséhez szüksége lesz erre az URL-címre.

4. A tesztfiók használatával próbálja meg megnyitni az alkalmazást a **külső URL-címmel,** hogy ellenőrizze, hogy az alkalmazásproxy megfelelően van-e beállítva. Ha problémák merülnek fel, olvassa el az [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása című témakört.](application-proxy-troubleshoot.md)

## <a name="update-the-saml-configuration"></a>Az SAML-konfiguráció frissítése

1. Ha az alkalmazás még mindig nyitva van az Azure Portalon, válassza **az Egyszeri bejelentkezés**lehetőséget. 

2. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon lépjen az **Egyszerű SAML konfigurációfejlécre,** és válassza ki a **Szerkesztés** ikont (egy ceruzát). Győződjön meg arról, hogy az alkalmazásproxyban konfigurált **külső URL-cím** az **Azonosító**, **a Válasz URL**és a **Kijelentkezés URL-címe** mezőkben van feltöltve. Ezek az URL-címek szükségesek az alkalmazásproxy megfelelő működéséhez. 

3. A korábban beállított **Válasz URL-címet** úgy szerkeszti, hogy a tartomány elérhető legyen az interneten alkalmazásproxyn keresztül. Ha például **a külső URL-cím** `https://contosotravel-f128.msappproxy.net` és `https://contosotravel.com/acs`az eredeti Válasz **URL-címe** volt, frissítenie kell az eredeti Válasz **URL-címet** `https://contosotravel-f128.msappproxy.net/acs`erre.

    ![Alapvető SAML konfigurációs adatok megadása](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Jelölje be a frissített **Válasz URL-cím** melletti jelölőnégyzetet, ha alapértelmezettként szeretné megjelölni.

   * Miután a szükséges **válasz URL-címet** jelölte meg alapértelmezettként, törölheti a korábban konfigurált **Válasz URL-címet** is, amely a belső URL-címet használta.

   * Sp által kezdeményezett folyamat esetén győződjön meg arról, hogy a háttéralkalmazás megadja a megfelelő **válasz URL-címet** vagy helyességi feltétel fogyasztói szolgáltatás URL-címét a hitelesítési jogkivonat fogadásához.

    > [!NOTE]
    > Ha a háttéralkalmazás azt várja, hogy a **Válasz URL-címe** a belső URL-cím legyen, [egyéni tartományokat](application-proxy-configure-custom-domain.md) kell használnia a megfelelő belső és külső URL-címek hez, vagy telepítenie kell a Saját alkalmazások biztonságos bejelentkezési bővítményt a felhasználók eszközeire. Ez a bővítmény automatikusan átirányítja a megfelelő alkalmazásproxy-szolgáltatásra. A bővítmény telepítéséhez olvassa el a [Saját alkalmazások biztonságos bejelentkezési bővítmény című témakört.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)
    
## <a name="test-your-app"></a>Az alkalmazás tesztelése

Miután elvégezte ezeket a lépéseket, az alkalmazásnak futnia kell. Az alkalmazás tesztelése:

1. Nyisson meg egy böngészőt, és keresse meg az alkalmazás közzétételekor létrehozott **külső URL-címet.** 
1. Jelentkezzen be az alkalmazáshoz rendelt tesztfiókkal. Képesnek kell lennie az alkalmazás betöltésére, és sso az alkalmazásba.

## <a name="next-steps"></a>További lépések

- [Hogyan biztosít az Azure AD alkalmazásproxy egyszeri bejelentkezést?](application-proxy-single-sign-on.md)
- [Alkalmazásproxyval kapcsolatos hibaelhárítás](application-proxy-troubleshoot.md)

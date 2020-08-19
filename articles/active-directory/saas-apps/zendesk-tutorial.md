---
title: 'Oktatóanyag: Azure Active Directory integráció a zendesk-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és zendesk között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: jeedes
ms.openlocfilehash: c7d452803d15bab77df8e85a861de914a5ed08d5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546063"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a zendesk

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a zendesk a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az zendesk-t az Azure AD-vel, a következőket teheti:

* A zendesk-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a zendesk az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Zendesk egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A zendesk támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A zendesk támogatja az [ **automatikus** felhasználó-kiépítés használatát](zendesk-provisioning-tutorial.md)
* A zendesk konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-zendesk-from-the-gallery"></a>Zendesk hozzáadása a gyűjteményből

A zendesk Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a zendesk a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **zendesk** kifejezést a keresőmezőbe.
1. Válassza ki a **zendesk** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a zendesk

Konfigurálja és tesztelje az Azure AD SSO-t a zendesk a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a zendesk-ben.

Az Azure AD SSO és a zendesk konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[ZENDESK SSO konfigurálása](#configure-zendesk-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre zendesk-teszt felhasználót](#create-zendesk-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-zendesk rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **zendesk** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.zendesk.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.zendesk.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a zendesk ügyfélszolgálati [csapatához](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A zendesk alkalmazás megadott formátumban várja az SAML-kijelentéseket. Nincsenek kötelező SAML-attribútumok, de opcionálisan kezelhetők a **felhasználói attribútumok** szakasz az alkalmazás-integráció lapon. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > A bővítmény attribútumaival olyan attribútumokat adhat hozzá, amelyek alapértelmezés szerint nem az Azure AD-ben vannak. A **zendesk** által elfogadott SAML-attribútumok teljes listájának lekéréséhez kattintson az [SAML-ban beállítható felhasználói attribútumok](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) elemre.

1. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomat értékét** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

1. A **zendesk beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a zendesk.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **zendesk**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-zendesk-sso"></a>Zendesk SSO konfigurálása

1. A **zendesk**belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![image](./media/target-process-tutorial/install_extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés zendesk** gombra a zendesk alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a zendesk való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a zendesk, nyisson meg egy új böngészőablakot, és jelentkezzen be a zendesk vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Kattintson a **rendszergazda**elemre.

1. A bal oldali navigációs ablaktáblán kattintson a **Beállítások**, majd a **Biztonság**elemre.

1. A **Biztonság** lapon hajtsa végre a következő lépéseket:

    ![Biztonság](./media/zendesk-tutorial/ic773089.png "Biztonság")

    ![Egyszeri bejelentkezés](./media/zendesk-tutorial/ic773090.png "Egyszeri bejelentkezés")

    a. Kattintson a **rendszergazda & ügynökök** fülre.

    b. Válassza az **egyszeri bejelentkezés (SSO) és az SAML**lehetőséget, majd válassza az **SAML**lehetőséget.

    c. Az **SAML SSO URL** szövegmezőben illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    d. A **távoli kijelentkezési URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portal másolt.

    e. A **Tanúsítvány ujjlenyomata** szövegmezőbe illessze be a tanúsítvány **ujjlenyomatának** értékét, amelyet a Azure Portal másolt.

    f. Kattintson a **Mentés** gombra.

### <a name="create-zendesk-test-user"></a>Zendesk-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a zendesk-ben. A zendesk támogatja az automatikus felhasználó-kiépítés használatát, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [meg az automatikus](Zendesk-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

> [!NOTE]
> A rendszer automatikusan kiépíti a **végfelhasználói** fiókokat a bejelentkezéskor. Az **ügynök** -és **rendszergazdai** fiókokat manuálisan kell kiépíteni a **zendesk** a bejelentkezés előtt.

1. Jelentkezzen be a **zendesk** -bérlőbe.

2. Válassza ki az **Ügyfél lista** lapot.

3. Válassza a **felhasználó** fület, majd kattintson a **Hozzáadás**gombra.

    ![Felhasználói csoportok](./media/zendesk-tutorial/ic773632.png "Felhasználó hozzáadása")
4. Írja be a kiépíteni kívánt meglévő Azure AD-fiók **nevét** és **e-mail-címét** , majd kattintson a **Mentés**gombra.

    ![Új felhasználó](./media/zendesk-tutorial/ic773633.png "Új felhasználó")

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a zendesk által biztosított zendesk felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a zendesk csempére kattint, automatikusan be kell jelentkeznie arra a zendesk, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A zendesk kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A zendesk és a speciális láthatóság és vezérlők elleni védelem](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A felhasználók üzembe helyezésének konfigurálása](zendesk-provisioning-tutorial.md)
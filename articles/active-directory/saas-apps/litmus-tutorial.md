---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Lakmusz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Lakmusz között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2020
ms.author: jeedes
ms.openlocfilehash: 494a698ac4b6f8262851878cfee4bb91126d9f02
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823026"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Lakmusz

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Lakmusz a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Lakmusz-t az Azure AD-vel, a következőket teheti:

* A Lakmusz-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Lakmusz az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Lakmusz egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Lakmusz támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A Lakmusz konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-litmus-from-the-gallery"></a>Lakmusz hozzáadása a gyűjteményből

A Lakmusz Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Lakmusz a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Lakmusz** kifejezést a keresőmezőbe.
1. Válassza ki a **Lakmusz** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-litmus"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Lakmusz

Konfigurálja és tesztelje az Azure AD SSO-t a Lakmusz a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Lakmusz-ben.

Az Azure AD SSO és a Lakmusz konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[LAKMUSZ SSO konfigurálása](#configure-litmus-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre lakmusz-teszt felhasználót](#create-litmus-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Lakmusz rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Lakmusz** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://litmus.com/sessions/new`

1. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (RAW)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. A **Lakmusz beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Lakmusz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Lakmusz**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-litmus-sso"></a>Lakmusz SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Lakmusz alkalmazásba.

1. A bal oldali navigációs panelen kattintson a **Biztonság** elemre.

    ![Lakmusz-konfiguráció](./media/litmus-tutorial/security-img.png)

1. Az **SAML-hitelesítés konfigurálása** szakaszban hajtsa végre a következő lépéseket:

    ![Lakmusz-konfiguráció](./media/litmus-tutorial/configure1.png)

    a. Váltson át az **SAML engedélyezése** váltógomb bekapcsolására.

    b. Válassza az **általános** lehetőséget a szolgáltató számára.

    c. Adja meg az **identitás-szolgáltató nevét**. pl.: `Azure AD`

1. Hajtsa végre a következő lépéseket:

    ![Lakmusz-konfiguráció](./media/litmus-tutorial/configure3.png)

    a. Az **SAML 2,0-végpont (http)** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    b. Nyissa meg a letöltött **tanúsítványfájl** Azure Portal a Jegyzettömbben, és illessze be a tartalmat **X. 509 tanúsítvány** szövegmezőbe.

    c. Kattintson az **SAML-beállítások mentése**gombra.

### <a name="create-litmus-test-user"></a>Lakmusz-tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Lakmusz alkalmazásba.

1. A bal oldali navigációs panelen kattintson a **fiókok** elemre.

    ![Lakmusz-konfiguráció](./media/litmus-tutorial/accounts-img.png)

1. Kattintson az **új felhasználó hozzáadása** fülre.

    ![Lakmusz-konfiguráció](./media/litmus-tutorial/add-new-user.png)

1. A **felhasználó hozzáadása** szakaszban hajtsa végre a következő lépéseket:

    ![Lakmusz-konfiguráció](./media/litmus-tutorial/user-profile.png)

    a. Az **e-mail** szövegmezőbe írja be a felhasználó e-mail-címét, például: **B. Simon \@ contoso.com**

    b. Az **Utónév** szövegmezőbe írja be a (z) " **B**" nevű felhasználó utónevét.

    c. A **vezetékneve** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**nevet.

    d. Kattintson a **felhasználó létrehozása**gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Lakmusz csempére kattint, automatikusan be kell jelentkeznie arra a Lakmusz, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Lakmusz kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Lakmusz és a speciális láthatóság és vezérlők elleni védelem](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Tableau Serverkiszolgálóval | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Tableau Server között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d19a7cc8d81f9e6e913f147b24c5cce03ff82027
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76986733"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Tableau Serverkiszolgálóval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Tableau Servert az Azure Active Directoryval (Azure AD). Ha integrálja a Tableau Servert az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Tableau Server.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve legyenek a Tableau Serverbe az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Tableau Server egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Tableau Server támogatja az **SP** által kezdeményezett sso-t
* A Tableau Server konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Tableau Server hozzáadása a gyűjteményből

A Tableau Server azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Tableau Servert a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Tablókiszolgáló** kifejezést a keresőmezőbe.
1. Válassza a **Tableau Server** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Tableau Server alkalmazáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a Tableau Server kiszolgálóval egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a tableau-kiszolgáló kapcsolódó felhasználója között.

Az Azure AD SSO beállítása és tesztelése a Tableau Server kiszolgálóval, hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Állítsa be a Tableau Server Egyszeri bejelentkezést](#configure-tableau-server-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
    1. **[Hozzon létre Tableau Server teszt felhasználó](#create-tableau-server-test-user)** - a B.Simon megfelelője a Tableau Server, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Tableau Server** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://azure.<domain name>.link`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://azure.<domain name>.link`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Az előző értékek nem valós értékek. Frissítse az értékeket a tényleges URL-cím és azonosító a Tableau Server konfigurációs lap, amely később az oktatóanyag ismerteti.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Tableau Server beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a Tableau Server.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Tableau Server**elemet.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-tableau-server-sso"></a>Tableau Server SSO konfigurálása

1. Az alkalmazáshoz konfigurált SSO konfigurálásához rendszergazdaként kell bejelentkeznie a Tableau Server bérlőjéhez.

2. A **KONFIGURÁCIÓ** lapon válassza a **Felhasználói identitás & Access**lehetőséget, majd a **Hitelesítési** módszer lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. A **KONFIGURÁCIÓ** lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. A **hitelesítési módszer hez**válassza az SAML lehetőséget.

    b. Jelölje be az **SAML-hitelesítés engedélyezése a kiszolgálón jelölőnégyzetet.**

    c. Tableau Server url-címet ad vissza – Az az <http://tableau_server>URL- és url-cím, amelyet a Tableau Server felhasználói el fognak érni, például . Használata `http://localhost` nem ajánlott. A záró perjellel (például `http://tableau_server/`) rendelkező URL-cím használata nem támogatott. A **Tableau-kiszolgáló url-címének** másolása és beillesztése a **Bejelentkezési URL-cím** beszövegébe az Azure Portal **Alapszintű SAML-konfiguráció szakaszában**

    d. SAML-entitásazonosító – Az entitásazonosító egyedileg azonosítja a Tableau Server telepítését az IdP számára. Itt újra megadhatja a Tableau Server URL-címét, ha úgy tetszik, de nem kell a Tableau Server URL-címét. **SAML-entitásazonosító** másolása és **beillesztése** az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában az Azonosító szövegmezőbe

    e. Kattintson az **XML-metaadatfájl letöltése** elemre, és nyissa meg a szövegszerkesztő alkalmazásban. Keresse meg a helyességifeltétel-fogyasztói szolgáltatás URL-címét a Http Post és index 0 segítségével, és másolja az URL-t. Most illessze be a **Válasz URL-szövegdobozába** az Azure Portal **alapszintű SAML-konfigurációszakaszában**

    f. Keresse meg az Azure Portalról letöltött összevonási metaadatfájlt, majd töltse fel az **SAML Idp metaadatfájlba.**

    g. Adja meg azoknak az attribútumoknak a nevét, amelyeket az IdP használ a felhasználónevek, a megjelenítendő nevek és az e-mail címek tárolására.

    h. Kattintson a **Mentés gombra**

    > [!NOTE]
    > Az ügyfélnek fel kell töltenie a Tableau Server SAML SSO-konfigurációjában lévő tanúsítványokat, és figyelmen kívül hagyja az SSO-folyamatot. Ha segítségre van szüksége az SAML konfigurálásához a Tableau Server kiszolgálón, olvassa el ezt a cikket [Az SAML konfigurálása](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm)című témakörben.

### <a name="create-tableau-server-test-user"></a>Tableau Server tesztfelhasználó létrehozása

Ennek a szakasznak az a célja, hogy hozzon létre egy B.Simon nevű felhasználót a Tableau Server ben. A Tableau-kiszolgáló összes felhasználóját ki kell építenie.

A felhasználó felhasználónevének meg kell egyeznie a felhasználónév Azure AD egyéni attribútumában konfigurált **értékkel.** A megfelelő leképezés az integráció működnie kell konfigurálása Azure AD egyszeri bejelentkezés.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie a szervezet Tableau Server rendszergazdájával.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Tableau Server csempére kattint, automatikusan be kell jelentkeznie arra a Tableau-kiszolgálóra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Tableau Server alkalmazást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
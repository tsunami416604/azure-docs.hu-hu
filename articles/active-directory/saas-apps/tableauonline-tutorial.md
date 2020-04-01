---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Tableau Online-nal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Tableau Online között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 187600edb599f5a5775e1b847ed1cb3a49f3b827
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985577"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Tableau Online-nal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Tableau Online-t az Azure Active Directoryval (Azure AD). Ha integrálja a Tableau Online-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Tableau Online-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve tableau online az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Tableau Online egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Tableau Online támogatja az **SP** által kezdeményezett SSO-t
* A Tableau Online konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Tableau Online hozzáadása a gyűjteményből

A Tableau Online azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Tableau Online-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény Hozzáadás szakaszábaírja a** **Tabló online** kifejezést a keresőmezőbe.
1. Válassza a **Tableau Online** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Tableau Online-nal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Tableau Online-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez a Tableau Online-nal hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Állítsa be a Tableau Online Egyszeri bejelentkezést](#configure-tableau-online-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Tableau Online teszt felhasználó](#create-tableau-online-test-user)** - egy megfelelője B.Simon a Tableau Online, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Tableau Online** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Tableau Online tartomány és URL-címek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A **Bejelentkezés az URL-cím** mezőbe írja be az URL-címet:`https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Az **Azonosító (entitásazonosító)** mezőbe írja be az URL-címet:`https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Az értéket `<entityid>` az oktatóanyag **Tabló online beállítása** szakaszában kapja meg. Az entitásazonosító értéke az **Azure AD-azonosító** értéke lesz a **Tableau Online beállítása** szakaszban.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Tableau Online beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például BrittaSimon\@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a Tableau Online.

1. Az Azure portalon válassza az Enterprise Applications ( **Nagyvállalati alkalmazások**, mind **az összes alkalmazás**) lehetőséget, majd a **Tableau Online**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Tableau Online**lehetőséget.

    ![A Tableau Online hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-tableau-online-sso"></a>Tableau Online SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Tableau Online alkalmazásba. Nyissa meg a **Beállítások,** majd **a Hitelesítés lehetőséget.**

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Az SAML engedélyezéséhez a **Hitelesítési típusok** csoportban szakasz. Jelölje be **A további hitelesítési módszer engedélyezése** jelölőnégyzetet, majd jelölje be az **SAML** jelölőnégyzetet.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Görgessen lefelé **a Metaadatfájl importálása a Tableau Online szakaszba.**  Kattintson a Tallózás gombra, és importálja az Azure AD-ből letöltött metaadatfájlt. Ezután kattintson **az Alkalmaz gombra.**

   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Az **Egyezés állítások** szakaszban szúrja be az **e-mail cím,** **utónév**és **vezetéknév**megfelelő identitásszolgáltatói helyességi nevét. Az azure AD-től az adatok beszerezése: 
  
    a. Az Azure Portalon nyissa meg a **Tableau Online** alkalmazásintegrációs lapot.

    b. A **Felhasználói attribútumok & Jogcímek** csoportban kattintson a szerkesztés ikonra.

   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/attributesection.png)

    c. Másolja a névtér értékét a következő attribútumokhoz: keresztnév, e-mail és vezetéknév a következő lépések végrehajtásával:

   ![Az Azure AD egyszeri bejelentkezése](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Kattintson **a user.givenname** értékre

    e. Másolja az értéket a **Névtér** mezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/attributesection2.png)

    f. Az e-mail és a vezetéknév névtérértékeinek másolásához ismételje meg a fenti lépéseket.

    g. Váltson át a Tableau Online alkalmazásra, majd állítsa be a **Felhasználói attribútumok & jogcímek** szakaszt az alábbiak szerint:

    * **E-mail: e-mail** vagy **userprincipalname**

    * Utónév: **keresztnév**

    * Vezetéknév: **vezetéknév**

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Tableau Online tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Tableau Online-ban.

1. A **Tableau Online -on**kattintson a **Beállítások,** majd **a Hitelesítés** szakaszra. Görgessen le a **Felhasználók kezelése** szakaszhoz. Kattintson **a Felhasználók hozzáadása,** majd **az E-mail címek megadása gombra.**
  
    ![Azure AD-tesztfelhasználó létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Válassza **a Felhasználók hozzáadása (SAML) hitelesítéshez**lehetőséget. Az **Enter e-mail címek** szövegdoboz\@add britta.simon contoso.com
  
    ![Azure AD-tesztfelhasználó létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Kattintson **a Felhasználók hozzáadása gombra.**

### <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Tableau Online csempére kattint, automatikusan be kell jelentkeznie arra a Tableau Online-ba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
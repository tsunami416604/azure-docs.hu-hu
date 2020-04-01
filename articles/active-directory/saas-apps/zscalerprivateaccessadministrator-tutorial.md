---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Zscaler private access-rendszergazdával | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Zscaler Private Access Administrator között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7f0bc04bb8e7358f5bdb2e4dfc3db198e24303f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085644"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Oktatóanyag: Az Azure Active Directory integrációja a Zscaler private access-rendszergazdával

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Zscaler privát hozzáférési rendszergazdáját az Azure Active Directoryval (Azure AD).
A Zscaler privát hozzáférési rendszergazdájának az Azure AD-vel való integrálása a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Zscaler privát hozzáférési rendszergazdájához.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Zscaler Private Access Administrator (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Zscaler private access-rendszergazda, a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Zscaler Private Access Administrator egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Zscaler Private Access Administrator támogatja az **SP** és **az IDP** által kezdeményezett sso-t

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Zscaler private access administrator hozzáadása a galériából

A Zscaler Private Access Administrator azure-beli AD-be való integrációjának konfigurálásához hozzá kell adnia a Zscaler Private Access Administrator-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Zscaler private access-rendszergazdát a katalógusból szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler Private Access Administrator ( Zscaler Private Access Administrator**) kifejezést, válassza a **Zscaler Private Access Administrator** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Zscaler private access rendszergazda az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Zscaler Private Access Administrator szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó Zscaler private access administrator létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Zscaler Private Access Administrator alkalmazásával a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Zscaler Private Access Administrator Single Sign-On --t](#configure-zscaler-private-access-administrator-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Zscaler Private Access Administrator teszt felhasználó](#create-zscaler-private-access-administrator-test-user)** – egy megfelelője Britta Simon zscaler privát hozzáférési rendszergazda, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Zscaler private access-rendszergazda, hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Zscaler Private Access Administrator** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Zscaler private access administrator tartomány és URL-címek egyszeri bejelentkezési információk](common/idp-relay.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.private.zscaler.com/auth/metadata`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.private.zscaler.com/auth/sso`

    c. Kattintson **a További URL-címek beállítása gombra.**

    d. A **Továbbítási állapot** mezőbe írjon be egy URL-címet:`idpadminsso`

5.  Ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépést:

    ![Zscaler private access administrator tartomány és URL-címek egyszeri bejelentkezési információk](common/both-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [a Zscaler Private Access Administrator Ügyfél támogatási csapatával,](https://help.zscaler.com/zpa-submit-ticket) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Zscaler privát hozzáférés rendszergazdájának beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>A Zscaler privát hozzáférési rendszergazdájának konfigurálása egyszeri bejelentkezés

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Zscaler Private Access Administrator alkalmazásba.

2. A lap tetején kattintson a **Felügyelet** elemre, és keresse meg a **HITELESÍTÉS szakaszt,** és kattintson az **IdP-konfiguráció**elemre.

    ![Zscaler privát hozzáférés rendszergazdája](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. A jobb felső sarokban kattintson az **IdP-konfiguráció hozzáadása gombra.** 

    ![Zscaler privát hozzáférési rendszergazda addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Az **IdP-konfiguráció hozzáadása** lapon hajtsa végre a következő lépéseket:
 
    ![Zscaler private access rendszergazda idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kattintson **a Fájl kiválasztása** gombra a letöltött metaadat-fájl feltöltéséhez az Azure AD-ből az **IdP metaadat-fájl feltöltése** mezőben.

    b. Beolvassa az **IdP-metaadatokat** az Azure AD-ből, és feltölti az összes mezőadatait az alábbiak szerint.

    ![Zscaler privát hozzáférési rendszergazda idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Válassza **az Egyszeri bejelentkezés** **rendszergazdaként lehetőséget.**

    d. Válassza ki a tartományt a **Domains** mezőből.
    
    e. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező `brittasimon@yourcompanydomain.extension`típusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon az Azure egyszeri bejelentkezést a Zscaler privát hozzáférési rendszergazdájának biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Zscaler Private Access Administrator lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zscaler Private Access Administrator lehetőséget.**

    ![A Zscaler privát hozzáférés-rendszergazda hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Zscaler Private Access Administrator tesztfelhasználójának létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Zscaler private access administratorba, ki kell építeni őket a Zscaler private access administrator-ba. A Zscaler private access-rendszergazda esetén a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Zscaler Private Access Administrator vállalati webhelyére.

2. A lap tetején kattintson a **Felügyelet** elemre, és keresse meg a **HITELESÍTÉS szakaszt,** és kattintson az **IdP-konfiguráció**elemre.

    ![Zscaler privát hozzáférés rendszergazdája](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Kattintson a menü bal oldalán a **Rendszergazdák** elemre.

    ![Zscaler privát hozzáférés rendszergazdája](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. A jobb felső sarokban kattintson a **Rendszergazda hozzáadása gombra:**

    ![Zscaler private access rendszergazda hozzáadása rendszergazda](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. A **Rendszergazda hozzáadása** lapon hajtsa végre az alábbi lépéseket:

    ![Zscaler Private Access rendszergazda](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. A **Felhasználónév** mezőbe írja be a BrittaSimon@contoso.comfelhasználó e-mail címét, például .

    b. A **Jelszó** mezőbe írja be a Jelszó jelölőnégyzetet.

    c. A **Jelszó megerősítése** mezőbe írja be a Jelszó jelölőnégyzetet.

    d. Válassza a **Szerepkör t** **Zscaler Private Access Administrator (Szerepkör: Zscaler Private Access Administrator) lehetőséget.**

    e. Az **E-mail** mezőbe írja be BrittaSimon@contoso.coma felhasználó e-mail címét, például .

    f. A **Telefon** mezőbe írja be a Telefonszám mezőt.

    g. Az **Időzóna** szövegmezőben jelölje ki az Időzónát.

    h. Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Zscaler private access administrator csempére kattint, automatikusan be kell jelentkeznie a Zscaler privát hozzáférési rendszergazdájához, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


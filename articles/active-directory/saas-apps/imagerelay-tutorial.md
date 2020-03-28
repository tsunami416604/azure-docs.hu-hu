---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a képtovábbítóval | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a lemezkép-továbbító között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd0637a632b277eae019ac4aebfbc7cdb87e8e2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158974"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Oktatóanyag: Az Azure Active Directory integrációja a képtovábbítóval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a képtovábbító az Azure Active Directory (Azure AD) használatával.
A képtovábbító integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a képtovábbítóhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a képtovábbító (single sign-on) rendszerbe az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a képtovábbítóval a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Képtovábbító egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A képrelé támogatja az **SP** által kezdeményezett sso-t

## <a name="adding-image-relay-from-the-gallery"></a>Képtovábbítás hozzáadása a galériából

A képtovábbító azure-ad-ba való integrálásának konfigurálásához hozzá kell adnia a képtovábbító a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha képtovábbítót szeretne hozzáadni a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Képtovábbítás**kifejezést , válassza a **Képtovábbítás** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Képtovábbítás az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést a Képtovábbítóval egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a képtovábbítóban létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be a képtovábbító egyszeri bejelentkezést](#configure-image-relay-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Képtovábbító tesztfelhasználó létrehozása](#create-image-relay-test-user)** – britta Simon megfelelője a képtovábbítóban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének lemezképtovábbítóval való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Rendszertovábbítás** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Képtovábbító tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.imagerelay.com/`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a képtovábbító ügyfél támogatási csapatával,](http://support.imagerelay.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Képtovábbítás beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-image-relay-single-sign-on"></a>Képtovábbító beállítása egyszeri bejelentkezéskor

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a képtovábbító vállalati webhelyre.

2. A felső eszköztáron kattintson a **Felhasználók & engedélyek** munkaterhelésre.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Kattintson **az Új engedély létrehozása gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. Az **Egyszeri bejelentkezési beállítások munkaterhelésben** jelölje be a Ez a **csoport csak egyszeri bejelentkezéssel,** majd kattintson a **Mentés gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Nyissa meg a **Fiókbeállítások lehetőséget.**

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Nyissa meg az **Egyszeri bejelentkezési beállítások munkaterhelést.**

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. Az **SAML-beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. A **Bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    b. A **Kijelentkezés URL-cím** szövegmezőjébe illessze be a **Kijelentkezés URL-címének** az Azure Portalról másolt értékét.

    c. **Névazonosító formátumként válassza** **az urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress lehetőséget.**

    d. A **szolgáltatótól érkező kérelmek kötési beállításaiként (Image Relay)** válassza a **POST Binding (KÜLDÉs)** lehetőséget.

    e. Az **x.509 tanúsítvány csoportban**kattintson **a Tanúsítvány frissítése gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Nyissa meg a letöltött tanúsítványt a jegyzettömbben, másolja a tartalmat, majd illessze be az **x.509 Tanúsítvány** szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. A **Just-In-Time felhasználói kiépítés** csoportban válassza a **Just-In-Time felhasználói kiépítés engedélyezése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Válassza ki azt az engedélycsoportot (például **Egyszeri bejelentkezés alapszintű),** amely csak egyszeri bejelentkezésen keresztül jelentkezhet be.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Kattintson a **Mentés** gombra.

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
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a képtovábbító.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Képtovábbító**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Képtovábbító**lehetőséget.

    ![A Képtovábbító hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-image-relay-test-user"></a>Képtovábbító tesztfelhasználó létrehozása

A szakasz célja, hogy hozzon létre egy felhasználó nevű Britta Simon a képtovábbító.

**Britta Simon nevű felhasználó létrehozásához a Képtovábbítóban hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a képtovábbító vállalati webhelyére rendszergazdaként.

2. Nyissa meg **a Felhasználók & engedélyeket,** és válassza **az SSO-felhasználó létrehozása**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Adja meg a kiépíteni kívánt felhasználó **e-mail,** **utónév**, **vezetéknév**és **vállalat** a nevét, és válassza ki az engedélycsoportot (például Az Egyszerű sSO-t), amely az a csoport, amely csak egyszeri bejelentkezésen keresztül tud bejelentkezni.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Kattintson **a Létrehozás gombra.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a Képtovábbító csempére kattint, automatikusan be kell jelentkeznie arra a képtovábbítóba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
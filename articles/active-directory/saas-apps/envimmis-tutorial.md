---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Envi MMIS-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Envi MMIS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1689517042713e9a3ce75c6ada822cb3d6ff138
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158269"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Oktatóanyag: Az Azure Active Directory integrációja az Envi MMIS-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Envi MMIS-t az Azure Active Directoryval (Azure AD).
Az Envi MMIS integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az Envi MMIS-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az Envi MMIS-be (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az Envi MMIS-szel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Envi MMIS egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az Envi MMIS támogatja az **SP** és **az IDP** által kezdeményezett sso-t

## <a name="adding-envi-mmis-from-the-gallery"></a>Envi MMIS hozzáadása a galériából

Az Envi MMIS Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Envi MMIS-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha envi MMIS-t szeretne hozzáadni a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Envi MMIS ( Envi MMIS**) kifejezést, válassza az **Envi MMIS** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Envi MMIS az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Envi MMIS-szel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó az Envi MMIS-ben létre kell hozni a kapcsolatot.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Envi MMIS-szel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be az Envi MMIS single sign-on-t](#configure-envi-mmis-single-sign-on)** - az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Envi MMIS tesztfelhasználót](#create-envi-mmis-test-user)** – ha az Envi MMIS-ben britta Simon megfelelője szeretne lenni, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az Envi MMIS-szel hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **Envi MMIS** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![Envi MMIS tartomány és URL-címek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.<CUSTOMER DOMAIN>.com/Account`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Envi MMIS tartomány és URL-címek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az Envi MMIS ügyféltámogatási csapatával](mailto:support@ioscorp.com) az értékek lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra az **összevonási metaadat-XML** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. Az **Envi MMIS beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-envi-mmis-single-sign-on"></a>Envi MMIS egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Envi MMIS-webhelyre rendszergazdaként.

2. Kattintson a **Saját tartomány** fülre.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/envimmis-tutorial/configure1.png)

3. Kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/envimmis-tutorial/configure2.png)

4. Jelölje be **a Távhitelesítés használata** jelölőnégyzetet, majd a **Hitelesítés típusa** legördülő menüben válassza a **HTTP-átirányítás** lehetőséget.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/envimmis-tutorial/configure3.png)

5. Válassza az **Erőforrások** lapot, majd kattintson **a Metaadatok feltöltése gombra.**

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/envimmis-tutorial/configure4.png)

6. A **Metaadatok feltöltése** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/envimmis-tutorial/configure5.png)

    a. Válassza a **Fájl** lehetőséget a **Feltöltés máshonnan** legördülő menüből.

    b. Töltse fel a letöltött metaadatfájlt az Azure Portalról a **fájl kiválasztása ikon kiválasztásával.**

    c. Kattintson az **OK** gombra.

7. A letöltött metaadatfájl feltöltése után a mezők automatikusan kitöltődnek. Kattintson a **Frissítés gombra**

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés az Envi MMIS-hez való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **Envi MMIS**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Envi MMIS**elemet.

    ![Az Envi MMIS hivatkozás a Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-envi-mmis-test-user"></a>Envi MMIS tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az Envi MMIS-be, ki kell építeni őket az Envi MMIS-be. Envi MMIS esetén a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be az Envi MMIS vállalati webhelyére rendszergazdaként.

2. Kattintson a **Felhasználói lista** fülre.

    ![Alkalmazott hozzáadása](./media/envimmis-tutorial/user1.png)

3. Kattintson **a Felhasználó hozzáadása** gombra.

    ![Alkalmazott hozzáadása](./media/envimmis-tutorial/user2.png)

4. A **Felhasználó hozzáadása** csoportban hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/envimmis-tutorial/user3.png)

    a. A **Felhasználónév** mezőbe írja be a Britta Simon fiók felhasználónevét, például **brittasimon\@contoso.com**.
    
    b. Az **Utónév** mezőbe írja be a BrittaSimon keresztnevét, például **Britta.**

    c. A **Vezetéknév** mezőbe írja be a BrittaSimon vezetéknevét, **például Simon**.

    d. Írja be a felhasználó címét a szövegdoboz **címében.**
    
    e. Az **E-mail cím** mezőbe írja be britta Simon-fiók e-mail címét, például **brittasimon\@contoso.com**.

    f. Az **SSO felhasználónév** mezőbe írja be a Britta Simon fiók felhasználónevét, például **brittasimon\@contoso.com**.

    g. Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az Envi MMIS csempére kattint, automatikusan be kell jelentkeznie az Envi MMIS-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


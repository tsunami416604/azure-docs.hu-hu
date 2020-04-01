---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Ziflow-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Ziflow között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: d9745bdb1cb6de86a96946564865958433d49732
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086195"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Oktatóanyag: Az Azure Active Directory integrációja a Ziflow-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Ziflow-t az Azure Active Directoryval (Azure AD).
A Ziflow integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Ziflow-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve ziflow (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Ziflow konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Ziflow egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Ziflow támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-ziflow-from-the-gallery"></a>Ziflow hozzáadása a galériából

A Ziflow azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Ziflow-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Ziflow-t a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Ziflow**kifejezést, válassza a **Ziflow** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Ziflow az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Ziflow-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Ziflow-ban létre kell hozni a kapcsolatot.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Ziflow-val a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Ziflow Single Sign-On --t](#configure-ziflow-single-sign-on)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Ziflow tesztfelhasználót](#create-ziflow-test-user)** – ha a Ziflow-ban britta Simon megfelelője van, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Ziflow-alkalmazásintegrációs** lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Ziflow tartomány és URL egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Az előző értékek nem valósak. Frissíteni fogja az egyedi azonosító értékét az azonosító és a Bejelentkezési URL-címet a tényleges értékkel, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Ziflow beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-ziflow-single-sign-on"></a>A Ziflow egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Ziflow-ba biztonsági rendszergazdaként.

2. Kattintson az Avatar a jobb felső sarokban, majd kattintson **a Fiók kezelése gombra**.

    ![Ziflow konfigurációkezelése](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. A bal felső sarokban kattintson az **Egyszeri bejelentkezés gombra.**

    ![Ziflow konfigurációs jele](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Az **Egyszeri bejelentkezés** lapon hajtsa végre az alábbi lépéseket:

    ![Ziflow konfiguráció egyetlen](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Válassza a **Típus** **SAML2.0-ként**lehetőséget.

    b. A **Bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    c. Töltse fel az Azure Portalról letöltött 64-es alapú kódolású tanúsítványt az **X509-es aláíró tanúsítványba.**

    d. A **Kijelentkezés url-cím** mezőbe illessze be a **Kijelentkezés URL-cím**értékét, amelyet az Azure Portalról másolt.

    e. Az **azonosítószolgáltató konfigurációs beállításai** szakaszban másolja a kiemelt egyedi azonosító értéket, és fűzze hozzá az azonosító és bejelentkezési URL-címhez az Azure Portal **alapszintű SAML-konfigurációjában.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező brittasimon@yourcompanydomain.extensiontípusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon az Azure egyszeri bejelentkezést a Ziflow-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd a **Ziflow**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Ziflow**lehetőséget.

    ![A Ziflow hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-ziflow-test-user"></a>Ziflow-tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Ziflow-ba, ki kell építeni őket a Ziflow-ba. A Ziflow-ban a kiépítés manuális feladat.

Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a Ziflow-ba biztonsági rendszergazdaként.

2. Keresse meg a **Személyek** a tetején.

    ![Ziflow konfigurációs személyek](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Kattintson a **Hozzáadás,** majd a **Felhasználó hozzáadása**gombra.

    ![Ziflow-konfiguráció felhasználó hozzáadása](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. A **Felhasználó hozzáadása** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Ziflow-konfiguráció felhasználó hozzáadása](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Az **E-mail** szöveg mezőbe írja brittasimon@contoso.combe a felhasználó e-mail címét, például .

    b. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például Britta.

    c. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például Simon.

    d. Válassza ki ziflow szerepkörét.

    e. Kattintson **az 1 felhasználó hozzáadása gombra.**

    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követ, hogy erősítse meg a fiók, mielőtt aktívvá válik.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelziás csempéjére kattint, automatikusan be kell jelentkeznie arra a Ziflow-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a StatusPage programmal | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a StatusPage között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: d947f610e6a753ce2ed349917640b07a55bbb735
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089880"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Oktatóanyag: Az Azure Active Directory integrációja a StatusPage programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a StatusPage programot az Azure Active Directoryval (Azure AD).
A StatusPage integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a StatusPage-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve statuspage (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a StatusPage alkalmazással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* StatusPage egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A StatusPage támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-statuspage-from-the-gallery"></a>Állapotlap hozzáadása a gyűjteményből

A StatusPage azure AD-be való integrációjának konfigurálásához hozzá kell adnia a StatusPage-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni az Állapotlapot a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **StatusPage**( Állapotlap ) **parancsot** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![StatusPage az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a StatusPage szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a StatusPage közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a StatusPage alkalmazással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[A StatusPage Single Sign-On konfigurálása](#configure-statuspage-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[StatusPage tesztfelhasználó létrehozása](#create-statuspage-test-user)** – a StatusPage-ben britta Simon megfelelője, amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **StatusPage** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![StatusPage tartomány és URL-címek egyszeri bejelentkezési információi](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Lépjen kapcsolatba a StatusPage támogatási csapatával [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)az egyszeri bejelentkezés konfigurálásához szükséges metaadatok igényléséhez. 
    >
    > a. A metaadatokból másolja a Kiállító értékét, majd illessze be az **Azonosító** mezőbe.
    >
    > b. A metaadatokból másolja a Válasz URL-címét, majd illessze be a **Válasz URL-címének** szövegmezőjébe.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **StatusPage beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-statuspage-single-sign-on"></a>Állapotlap egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a StatusPage vállalati webhelyére rendszergazdaként.

1. A fő eszköztáron kattintson a **Fiók kezelése gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kattintson az **Egyszeri bejelentkezés** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Az SSO beállítása lapon hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Az **SSO cél URL-cím** mezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    b. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, másolja a tartalmat, majd illessze be a **Tanúsítvány** szövegmezőbe.

    c. Kattintson **a Konfiguráció mentése gombra.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban`brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a StatusPage hozzáférést biztosít.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **StatusPage**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **StatusPage**lehetőséget.

    ![A StatusPage hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-statuspage-test-user"></a>StatusPage tesztfelhasználó létrehozása

Ennek a szakasznak az a célja, hogy hozzon létre egy Britta Simon nevű felhasználót a StatusPage-ben.

A StatusPage támogatja a just-in-time kiépítést. Már engedélyezte az [Azure AD egyszeri bejelentkezés konfigurálása szolgáltatásban.](#configure-azure-ad-single-sign-on)

**Britta Simon nevű felhasználó létrehozásához a StatusPage alkalmazásban hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a StatusPage vállalati webhelyére rendszergazdaként.

1. A felső menüben kattintson a **Fiók kezelése parancsra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kattintson a **Csapattagok** fülre.
  
    ![Azure AD-tesztfelhasználó létrehozása](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Kattintson **a Csapattag hozzáadása gombra.**
  
    ![Azure AD-tesztfelhasználó létrehozása](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Írja be egy olyan érvényes felhasználó **e-mail címét,** **utónevét**és **vezetéknevét,** akit a kapcsolódó szövegdobozokba szeretne beépíteni. 

    ![Azure AD-tesztfelhasználó létrehozása](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. **Szerepkörként**válassza **az Ügyfélrendszergazda lehetőséget.**

1. Kattintson **a Fiók létrehozása gombra.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a StatusPage csempére kattint, automatikusan be kell jelentkeznie arra a StatusPage-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

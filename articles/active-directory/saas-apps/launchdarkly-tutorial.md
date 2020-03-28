---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a LaunchDarkly programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a LaunchDarkly között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3f0671bc-f93f-496e-b465-b9ce8c6633fa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e32f8c3ea300960893163264e99bd6c51138c7c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159653"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Oktatóanyag: Az Azure Active Directory integrációja a LaunchDarkly szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a LaunchDarkly szolgáltatást az Azure Active Directoryval (Azure AD).
A LaunchDarkly integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a LaunchDarkly szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve LaunchDarkly (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a LaunchDarkly szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* LaunchDarkly egyszeri bejelentkezésre szóló előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A LaunchDarkly támogatja az **SP és az IDP** által kezdeményezett SSO-t
* A LaunchDarkly támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-launchdarkly-from-the-gallery"></a>LaunchDarkly hozzáadása a galériából

A LaunchDarkly Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a LaunchDarkly-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a LaunchDarkly-t a katalógusból szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **LaunchDarkly**parancsot, válassza az **Eredménypanel LaunchDarkly** elemét, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![LaunchDarkly az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést az [Alkalmazás név] szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó [Alkalmazás neve] létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az [Alkalmazás neve] beállításához a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja launchdarkly single sign-on](#configure-launchdarkly-single-sign-on)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[LaunchDarkly tesztfelhasználó létrehozása](#create-launchdarkly-test-user)** – a LaunchDarkly-ban britta Simon megfelelője, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az [Alkalmazás név] segítségével hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **LaunchDarkly** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![LaunchDarkly Domain és URL-címek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet:`app.launchdarkly.com`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > A Válasz URL-értéke nem valós. Frissíteni fogja az értéket a tényleges Válasz URL-cím, amely később az oktatóanyag ban ismertetjük. Ha az alkalmazást **IDP** módban kívánja használni, üresen kell hagynia a **Bejelentkezés url-címmezőt,** ellenkező esetben nem tudja elindítani a bejelentkezést az **IDP-ből.** Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://app.launchdarkly.com`

    ![LaunchDarkly Domain és URL-címek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **LaunchDarkly beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-launchdarkly-single-sign-on"></a>LaunchDarkly single sign-on konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a LaunchDarkly vállalati webhelyére rendszergazdaként.

2. Válassza a Bal oldali navigációs panel **Fiókbeállítások parancsát.**

    ![LaunchDarkly konfiguráció](./media/launchdarkly-tutorial/configure1.png)

3. Kattintson **a Biztonság** fülre.

    ![LaunchDarkly konfiguráció](./media/launchdarkly-tutorial/configure2.png)

4. Kattintson **az SSO engedélyezése** **gombra, majd az SAML KONFIGURÁCIÓ SZERKESZTÉSE parancsra.**

    ![LaunchDarkly konfiguráció](./media/launchdarkly-tutorial/configure3.png)

5. Az **SAML konfigurációjának szerkesztése** szakaszban hajtsa végre az alábbi lépéseket:

    ![LaunchDarkly konfiguráció](./media/launchdarkly-tutorial/configure4.png)

    a. Másolja a **példány SAML-fogyasztói szolgáltatásÁNAK URL-címét,** és illessze be a Válasz URL-cím beszövegébe az Azure Portal **LaunchDarkly tartomány és URL-címei** szakaszában.

    b. A **Bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    c. Nyissa meg a letöltött tanúsítványt az Azure Portalról a Jegyzettömbbe, másolja a tartalmat, majd illessze be az **X.509 tanúsítványmezőbe,** vagy közvetlenül feltöltheti a tanúsítványt a **feltöltési elemre**kattintva.

    d. Kattintson a **Mentés gombra**

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva launchdarkly.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **a LaunchDarkly**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **LaunchDarkly**lehetőséget.

    ![A LaunchDarkly hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-launchdarkly-test-user"></a>LaunchDarkly tesztfelhasználó létrehozása

A szakasz célja, hogy hozzon létre egy felhasználó nevű Britta Simon launchdarkly. LaunchDarkly támogatja a just-in-time kiépítése, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Új felhasználó jön létre a LaunchDarkly elérésére tett kísérlet során, ha még nem létezik.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon [a LaunchDarkly ügyféltámogatási csapatához.](mailto:support@launchdarkly.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a LaunchDarkly csempére kattint, automatikusan be kell jelentkeznie arra a LaunchDarkly-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

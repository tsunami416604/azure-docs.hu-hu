---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Fronttal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Front között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 33ed2f98ba66704072e8139654dfbc4cde8ab3be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73155644"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Oktatóanyag: Az Azure Active Directory integrációja a Fronttal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Frontot az Azure Active Directoryval (Azure AD).
A Front integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Fronthoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az első (single sign-on) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció beállítása a Front, szüksége van a következő elemeket:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Első egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az előlap támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-front-from-the-gallery"></a>Front hozzáadása a galériából

A Front azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Frontot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a tárból szeretné hozzáadni az Első t, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Első**, válassza **az Elülső lehetőséget** az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Elöl az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést a Fronttal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó között a Front kapcsolatát kell létrehozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Frontszolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be az elülső egyszeri bejelentkezést](#configure-front-single-sign-on)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre első teszt felhasználó](#create-front-test-user)** - egy megfelelője Britta Simon elöl, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **Első alkalmazás** integrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![Előtér-tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.frontapp.com`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.frontapp.com/sso/saml/callback`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [a Front Client támogatási csapatával,](mailto:support@frontapp.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **Elülső beállítás** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-front-single-sign-on"></a>Front Single Sign-on konfigurálása

1. Jelentkezzen be az első bérlőhöz rendszergazdaként.

2. Nyissa meg a **Beállítások (fogaskerék ikon a bal oldalsáv alján) > Beállítások .**
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/front-tutorial/tutorial_front_000.png)

3. Kattintson **az Egyszeri bejelentkezés** hivatkozásra.
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/front-tutorial/tutorial_front_001.png)

4. Válassza az **SAML** elemet az Egyszeri bejelentkezés legördülő **listájában.**
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/front-tutorial/tutorial_front_002.png)

5. A **belépési pont** szövegmezőben tegye a **bejelentkezési URL-cím** értékét az Azure AD alkalmazáskonfigurációs varázsló.
    
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/front-tutorial/tutorial_front_003.png)

6. Nyissa meg a letöltött **tanúsítványfájlt(Base64)** a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **Aláíró tanúsítvány** szövegmezőbe.
    
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/front-tutorial/tutorial_front_004.png)

7. A **Szolgáltató beállításai** csoportban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/front-tutorial/tutorial_front_005.png)

    a. Másolja az **entitásazonosító** értékét, és illessze be az Azure **Portal Első tartomány és URL-címek** **azonosító** szövegmezőjébe.

    b. Másolja az **ACS URL-címének** értékét, és illessze be a **Válasz URL-cím** szövegmezőjébe az **Azure Portal Első tartomány és URL-címek** szakaszába.
    
8. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Front hozzáférést biztosítva.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Front**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Front**lehetőséget.

    ![Az Alkalmazások listában az Elülső hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-front-test-user"></a>Első tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy felhasználó nevű Britta Simon elöl. Együttműködve [Front Client támogatási csapat](mailto:support@frontapp.com) a felhasználók hozzáadása a Front platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panel előlapjára kattint, automatikusan be kell jelentkeznie arra az előlapra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


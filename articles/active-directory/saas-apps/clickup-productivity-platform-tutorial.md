---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a ClickUp termelékenységi platformmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a ClickUp productivity platform között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: f497955b698d7ca390b40686e94d553a7cd5c948
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048751"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Oktatóanyag: Az Azure Active Directory integrációja a ClickUp hatékonyságnövelő platformmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a ClickUp productivity platformot az Azure Active Directoryval (Azure AD).
A ClickUp productivity platform integrálása az Azure AD-vel a következő előnyökkel jár:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a ClickUp hatékonyságnövelő platformhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve ClickUp productivity platform (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció beállítása a ClickUp termelékenységi platformmal a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* ClickUp Productivity Platform egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A ClickUp Productivity Platform támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>ClickUp produktivitási platform hozzáadása a galériából

A ClickUp productivity platform Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ClickUp productivity platformot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a ClickUp hatékonyságnövelő platformot a katalógusból szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **ClickUp Productivity Platform (Kattintás: Hatékonyságnövelő** **platform)** kifejezést az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Kattintson a Termelékenységi platform elemre az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a ClickUp productivity platformmal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó között a ClickUp termelékenységi platform on ClickUp termelékenységi platform létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a ClickUp productivity platformmal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a ClickUp termelékenységi platform egyszeri bejelentkezését](#configure-clickup-productivity-platform-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[ClickUp productivity platform tesztfelhasználó](#create-clickup-productivity-platform-test-user)** létrehozása – a ClickUp hatékonyságnövelő platform britta simon-i megfelelőjének létrehozásához, amely az Azure AD felhasználói ábrázolásához kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a ClickUp productivity platformmal hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **ClickUp productivity platform** alkalmazásintegrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Kattintson a Termelékenységi platform tartományának elemre és az egyszeri bejelentkezési adatok URL-címeire](common/sp-identifier.png)

    a. A **Bejelentkezés az URL-cím** mezőbe írjon be egy URL-címet:`https://app.clickup.com/login/sso`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Az azonosító értéke nem valós. Frissítse ezt az értéket a tényleges azonosítóval, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>ClickUp termelékenységi platform egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a ClickUp productivity platform bérlője rendszergazdaként.

2. Kattintson a **Felhasználói profilra**, majd a **Beállítások gombra.**

    ![Kattintsona A termelékenység konfigurálása elemre](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Kattintsona A termelékenység konfigurálása elemre](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Válassza a **Microsoft**lehetőséget az Egyszeri bejelentkezés (SSO) szolgáltató csoportban.

    ![Kattintsona A termelékenység konfigurálása elemre](./media/clickup-productivity-platform-tutorial/configure2.png)

4. A **Microsoft egyszeri bejelentkezés konfigurálása** lapon hajtsa végre az alábbi lépéseket:

    ![Kattintsona A termelékenység konfigurálása elemre](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Kattintson a **Másolás** gombra az entitásazonosító értékének másolásához és beillesztéséhez az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában található **azonosító (entitásazonosító)** szövegmezőbe.
    
    b. Az **Azure Federation metaadat-URL-címmezőjébe** illessze be az Alkalmazásösszevonás metaadat-url-címét, amelyet az Azure Portalról másolt, majd kattintson a **Mentés gombra.**

5. A telepítés befejezéséhez kattintson a **Hitelesítés a Microsofttal** gombra a telepítés befejezéséhez és a Microsoft-fiókkal való hitelesítéshez.

    ![Kattintsona A termelékenység konfigurálása elemre](./media/clickup-productivity-platform-tutorial/configure4.png)

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

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést a ClickUp hatékonyságnövelő platformhoz való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd kattintson **a Kattintási hatékonysági platform elemre.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **ClickUp Productivity Platform (Kattintás: Hatékonyságnövelés platform) lehetőséget.**

    ![A ClickUp termelékenységi platform hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-clickup-productivity-platform-test-user"></a>ClickUp termelékenységi platform tesztfelhasználójának létrehozása

1. Egy másik böngészőablakban jelentkezzen be a ClickUp productivity platform bérlője rendszergazdaként.

2. Kattintson a **Felhasználói profilra**, majd válassza a **Személyek**lehetőséget.
   
    ![Kattintsona A termelékenység konfigurálása elemre](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Kattintsona A termelékenység konfigurálása elemre](./media/clickup-productivity-platform-tutorial/user1.png)

3. Írja be a felhasználó e-mail címét a szövegmezőbe, és kattintson a **Meghívás gombra.**

    ![Kattintsona A termelékenység konfigurálása elemre](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > A felhasználó megkapja az értesítést, és el kell fogadnia a meghívást a fiók aktiválására.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a ClickUp productivity platform csempére kattint, automatikusan be kell jelentkeznie a ClickUp termelékenységi platformra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


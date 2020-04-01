---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Showpaddal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Showpad között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 48b6bee0-dbc5-4863-964d-75b25e517741
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6c9235efe75c5ed16de01d950dcbec9746fbfa10
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090799"
---
# <a name="tutorial-azure-active-directory-integration-with-showpad"></a>Oktatóanyag: Az Azure Active Directory integrációja a Showpaddal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Showpadot az Azure Active Directoryval (Azure AD).
A Showpad integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Showpadhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Showpad (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Showpad szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Showpad egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Showpad támogatja az **SP** által kezdeményezett SSO-t
* A Showpad támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-showpad-from-the-gallery"></a>Bemutatópárna hozzáadása a galériából

A Showpad azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Showpadot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Bemutatópadot a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Megjelenítési lehetőséget,** válassza az eredménypanel **Megjelenítés parancsát,** majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Showpad az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Showpaddal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a Showpad létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Showpad segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be a Showpad Single Sign-On --hoz](#configure-showpad-single-sign-on)** configure the Single Sign-On settings on application side.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Showpad tesztfelhasználó létrehozása](#create-showpad-test-user)** – a Showpad ben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik, britta Simon megfelelőjével rendelkezik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Showpad** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![A Bemutatópad tartománya és URL-címei egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<comapany-name>.showpad.biz/login`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<company-name>.showpad.biz`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Showpad ügyféltámogatási csapatával,](https://help.showpad.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Showpad beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-showpad-single-sign-on"></a>A Showpad egyszeri bejelentkezéskonfigurálása

1. Jelentkezzen be a Showpad bérlője rendszergazdaként.

1. A felső menüben kattintson a **Beállítások gombra**.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/showpad-tutorial/tutorial_showpad_001.png)

1. Nyissa meg az **Egyszeri bejelentkezés** gombbal az Engedélyezés gombra, és kattintson **az Engedélyezés gombra.**

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/showpad-tutorial/tutorial_showpad_002.png)

1. Az **SAML 2.0 szolgáltatás hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/showpad-tutorial/tutorial_showpad_003.png) 

    a. A **Név** mezőbe írja be az azonosítószolgáltató nevét (például: a vállalat neve).

    b. **Metaadat-forrásként**válassza az **XML**lehetőséget.

    c. Másolja a metaadat-XML-fájl tartalmát, amelyet az Azure Portalról töltött le, majd illessze be a **metaadat-XML** szövegmezőbe.

    d. Válassza **az automatikus kiépítési fiókok lehetőséget az új felhasználók számára a bejelentkezéskor.**

    e. Kattintson a **Küldés gombra.**

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Showpad hozzáférést biztosítva.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd a **Bemutatópult**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Showpad**lehetőséget.

    ![A Showpad hivatkozás a Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-showpad-test-user"></a>Showpad-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Showpad alkalmazásban. A Showpad támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Showpad, egy új jön létre a hitelesítés után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelShowpad csempéjére kattint, automatikusan be kell jelentkeznie arra a Bemutatópadra, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


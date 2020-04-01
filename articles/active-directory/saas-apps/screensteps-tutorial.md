---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a ScreenSteps szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a ScreenSteps között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 864a9243a9f737506fd4d8cbc3940d7a86711f20
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091666"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Oktatóanyag: Az Azure Active Directory integrációja a ScreenSteps szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a ScreenSteps-et az Azure Active Directoryval (Azure AD).
A ScreenSteps integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a ScreenSteps-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve ScreenSteps (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a ScreenSteps segítségével a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* ScreenSteps egyszeri bejelentkezéssel engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* ScreenSteps támogatja **SP** kezdeményezett SSO

## <a name="adding-screensteps-from-the-gallery"></a>ScreenSteps hozzáadása a galériából

A ScreenSteps azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ScreenSteps-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A ScreenSteps hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **ScreenSteps**( Képernyőlépések ) parancsot, válassza az eredménypanel **Képernyőlépése** parancsát, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![ScreenSteps az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a ScreenSteps segítségével egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó ScreenSteps létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a ScreenSteps segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja ScreenSteps Single Sign-On](#configure-screensteps-single-sign-on)** -, hogy konfigurálja az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[ScreenSteps tesztfelhasználó létrehozása](#create-screensteps-test-user)** – britta Simon megfelelője a ScreenSteps-ben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a ScreenSteps segítségével hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **ScreenSteps** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![ScreenSteps tartomány és URL egyszeri bejelentkezési információk](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **ScreenSteps beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-screensteps-single-sign-on"></a>ScreenSteps egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a ScreenSteps vállalati webhelyére rendszergazdaként.

1. Kattintson **a Fiókbeállítások gombra.**

    ![Fiókkezelés](./media/screensteps-tutorial/ic778523.png "Fiókkezelés")

1. Kattintson **az Egyszeri bejelentkezés gombra.**

    ![Távoli hitelesítés](./media/screensteps-tutorial/ic778524.png "Távoli hitelesítés")

1. Kattintson **az Egyszeri bejelentkezés létrehozása a végponton gombra.**

    ![Távoli hitelesítés](./media/screensteps-tutorial/ic778525.png "Távoli hitelesítés")

1. Az **Egyszeri bejelentkezés létrehozása a végponton** szakaszban hajtsa végre az alábbi lépéseket:

    ![Hitelesítési végpont létrehozása](./media/screensteps-tutorial/ic778526.png "Hitelesítési végpont létrehozása")

    a. A **Cím** mezőbe írjon be egy címet.

    b. A **Mód** listában válassza az **SAML lehetőséget.**

    c. Kattintson **a Létrehozás gombra.**

1. **Az** új végpont szerkesztése.

    ![Végpont szerkesztése](./media/screensteps-tutorial/ic778528.png "Végpont szerkesztése")

1. Az **Egyszeri bejelentkezés szerkesztése a végponton** szakaszban hajtsa végre az alábbi lépéseket:

    ![Távoli hitelesítési végpont](./media/screensteps-tutorial/ic778527.png "Távoli hitelesítési végpont")

    a. Kattintson **az Új SAML tanúsítványfájl feltöltése**elemre, majd töltse fel az Azure Portalról letöltött tanúsítványt.

    b. **Illessze be a bejelentkezési URL-címet,** amelyet az Azure Portalról másolt a **távoli bejelentkezési URL-cím** mezőbe.

    c. Illessze **be a kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt a **Kijelentkezés URL-cím** szövegmezőjébe.

    d. Válassza ki azt **a csoportot,** amelyhez a felhasználókat kiépítésükkor hozzá kívánja rendelni.

    e. Kattintson a **Frissítés** parancsra.

    f. Másolja az **SAML-fogyasztó URL-címét** a vágólapra, és illessze be a **bejelentkezési URL-cím** beírt szövegdobozába az Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    g. Térjen vissza az **Egyszeri bejelentkezés szerkesztése végpontra**.

    h. Kattintson a **Fiók alapértelmezettbeállítása** gombra a végpont használatához minden olyan felhasználó számára, aki bejelentkezik a ScreenSteps alkalmazásba. Másik lehetőségként a Hozzáadás a **webhelyhez** gombra kattintva használhatja ezt a végpontot a **ScreenSteps**területen található egyes webhelyekhez.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban**brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a ScreenSteps hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **ScreenSteps**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **ScreenSteps lehetőséget.**

    ![A ScreenSteps hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-screensteps-test-user"></a>ScreenSteps tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a ScreenSteps alkalmazásban. Együttműködve [ScreenSteps ügyfél támogatási csapat](https://www.screensteps.com/contact) a felhasználók hozzáadása a ScreenSteps platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panel Képernyőlépések csempéjére kattint, automatikusan be kell jelentkeznie azokra a Képernyőlépésekbe, amelyekhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Periscope Data-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Periscope Data között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f378edb-9ac9-494d-a84a-03357b923ee1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: ffa0176c4dde6334e70b9a56b066a677d1610b48
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160194"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Oktatóanyag: Az Azure Active Directory integrációja a Periscope-adatokkal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Periscope-adatokat az Azure Active Directoryval (Azure AD).
A Periscope-adatok integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Periscope-adatokhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Periscope Data (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció periscope-adatokkal való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Periszkóp Adatok egyszeri bejelentkezéssel rendelkező előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Periscope Data támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-periscope-data-from-the-gallery"></a>Periszkóp-adatok hozzáadása a gyűjteményből

A Periscope-adatok Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a periszkóp-adatokat a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha periszkópadatokat szeretne hozzáadni a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Periscope Data (Periscope Data** **)** parancsot az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Periszkóp adatok az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Periscope Data-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat periscope-adatok létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Periscope Data segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Periscope-adatok egyszeri bejelentkezésének konfigurálása](#configure-periscope-data-single-sign-on)** – az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Periscope-adatteszt-felhasználót](#create-periscope-data-test-user)** – ha a Periscope-adatokban britta Simon megfelelője kapcsolódik a felhasználó Azure AD-ábrázolásához.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének periscope-adatokkal való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Periscope Data alkalmazásintegrációs** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Periszkóp adattartomány és URL-címek egyszeri bejelentkezési információi](common/sp-identifier.png)

    a. A **Bejelentkezés az URL-cím** mezőbe írja be bármelyik URL-címét:
    
    | |
    |--|
    | `https://app.periscopedata.com/` |
    | `https://app.periscopedata.com/app/<SITENAME>` |

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > A Bejelentkezés url-cím értéke nem valós. Frissítse az értékeket a tényleges Bejelentkezési URL-címmel. Lépjen kapcsolatba a [Periscope data client támogatási csapatával,](mailto:support@periscopedata.com) hogy megkapja ezt az értéket és az azonosító értéket, amelyet a **Periscope-adatok egyszeri bejelentkezéskonfigurálása** szakaszból kap, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Periszkóp-adatok egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Periscope-adatokba rendszergazdaként.

2. Nyissa meg a fogaskerék menüt a bal alsó sarokban, és nyissa meg a **Számlázási** > biztonság menüt, és hajtsa végre a következő lépéseket.**Security** Ezekhez a beállításokhoz csak a rendszergazdák férhetnek hozzá.

    ![Periszkóp-adatok – Adatok konfigurálása](./media/periscope-data-tutorial/configure01.png)

    a. Másolja az **App Federation metaadat-URL-címét** az **SAML aláíró tanúsítvány** #5 lépésből, és nyissa meg egy böngészőben. Ezzel megnyit egy XML-dokumentumot.

    b. Az **egyszeri bejelentkezés** szövegmezőben válassza az **Azure Active Directory**lehetőséget.

    c. Keresse meg a **SingleSignOnService** címkét, és illessze be a **Hely** értéket az **Egyszeri bejelentkezés URL-címmezőjébe.**

    d. Keresse meg a **SingleLogoutService** címkét, és illessze be a **Hely** értéket az **SLO URL-cím** szövegmezőjébe.

    e. Másolja a példány **azonosító** értékét, és illessze be az Azure Portal **alapszintű SAML-konfigurációszakasz** **azonosító (entitásazonosító)** szövegmezőjébe.

    f. Keresse meg az XML-fájl első címkéjét, másolja az **entitásazonosító** értékét, és illessze be a **Kiállító** mezőbe.

    g. Keresse meg az **IDPSSODescriptor** címkét SAML protokollal. Az adott szakaszban keresse meg a **KeyDescriptor** címkét **a use=signing**segítségével. másolja az **X509Certificate** értékét, és illessze be a **Tanúsítvány** mezőbe.

    h. A több szóközzel rendelkező helyek az alapértelmezett helyet választhatják az **Alapértelmezett terület** legördülő menüből. Ez lesz az a hely, amelyhez az új felhasználók először jelentkeznek be a Periscope-adatokba, és az Active Directory egyszeri bejelentkezésén keresztül kerülnek kiépítésre.

    i. Végül kattintson a **Mentés gombra,** és **erősítse meg, hogy** az SSO-beállítások megváltoznak a **Kijelentkezés**beírásával.

    ![Periszkóp-adatok – Adatok konfigurálása](./media/periscope-data-tutorial/configure02.png)

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a Periscope-adatok hoz való hozzáférést.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Periscope-adatok lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Periscope Data (Periscope-adatok) lehetőséget.**

    ![A Periscope-adatok hivatkozás a Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-periscope-data-test-user"></a>Periszkóp-adatteszt-felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Periscope-adatokba, ki kell építeni őket a Periscope-adatokba. A Periscope-adatok ban a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Periscope-adatokba rendszergazdaként.

2. Kattintson a menü bal alsó részén található **Beállítások** ikonra, és keresse meg az **Engedélyek lehetőséget.**

    ![Periszkóp-adatok – Adatok konfigurálása](./media/periscope-data-tutorial/configure03.png)

3. Kattintson az **ADD USER gombra,** és hajtsa végre a következő lépéseket:

      ![Periszkóp-adatok – Adatok konfigurálása](./media/periscope-data-tutorial/configure04.png)

    a. A **Keresztnév** mezőbe írja be a felhasználó keresztnevét, például **Britta.**

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    c. Az **E-mail** szöveg mezőbe írja be a felhasználó e-mail címét, mint **a\@brittasimon contoso.com**.

    d. Kattintson **az ADD gombra.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Periscope-adatok csempére kattint, automatikusan be kell jelentkeznie a Periscope-adatokba, amelyekhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


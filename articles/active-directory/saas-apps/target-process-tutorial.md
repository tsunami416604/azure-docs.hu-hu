---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a TargetProcess programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a TargetProcess között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 60cd043c34ba2aee365c59feb38cc9dd02d45746
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089044"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Oktatóanyag: Az Azure Active Directory integrációja a TargetProcess programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a TargetProcess-t az Azure Active Directoryval (Azure AD).
A TargetProcess integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a TargetProcess-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a TargetProcess (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció beállítása a TargetProcess szolgáltatással a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* TargetProcess egyszeri bejelentkezéssel engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A TargetProcess támogatja az **SP** által kezdeményezett sso-t
* A TargetProcess támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-targetprocess-from-the-gallery"></a>TargetProcess hozzáadása a gyűjteményből

A TargetProcess azure AD-be való integrációjának konfigurálásához hozzá kell adnia a TargetProcess-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A TargetProcess gyűjteményből való hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **TargetProcess**( **Célfolyamat** ) parancsot az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![TargetProcess az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a TargetProcess szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a TargetProcess közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a TargetProcess alkalmazással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a TargetProcess Single Sign-On --t](#configure-targetprocess-single-sign-on)** az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Create TargetProcess teszt felhasználó](#create-targetprocess-test-user)** - egy megfelelője Britta Simon a TargetProcess, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **TargetProcess** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![A TargetProcess tartomány és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.tpondemand.com/`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.tpondemand.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a TargetProcess ügyfél támogatási csapatával](mailto:support@targetprocess.com) az értékek lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **TargetProcess beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-targetprocess-single-sign-on"></a>Célprocess egyszeri bejelentkezés konfigurálása

1. A **TargetProcess**konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![image](./media/target-process-tutorial/install_extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **beállítás TargetProcess** irányítja, hogy a TargetProcess alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a TargetProcess-be való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-7.

    **Ha manuálisan szeretné konfigurálni az alkalmazást, hajtsa végre az alábbi lépéseket:**

3. Jelentkezzen be a TargetProcess alkalmazásba rendszergazdaként.

4. A felső menüben kattintson a **Telepítés gombra.**

    ![Telepítés](./media/target-process-tutorial/tutorial_target_process_05.png)

5. Kattintson a **Beállítások** fülre.

    ![Beállítások](./media/target-process-tutorial/tutorial_target_process_06.png)

6. Kattintson **az Egyszeri bejelentkezés** fülre.

    ![kattintson az Egyszeri bejelentkezés gombra](./media/target-process-tutorial/tutorial_target_process_07.png)

7. Az Egyszeri bejelentkezés beállításai párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/target-process-tutorial/tutorial_target_process_08.png)

    a. Kattintson **az Egyszeri bejelentkezés engedélyezése gombra.**

    b. A **Bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    c. Nyissa meg a letöltött tanúsítványt a jegyzettömbben, másolja a tartalmat, majd illessze be a **Tanúsítvány** szövegmezőbe.

    d. kattintson **a JIT-kiépítés engedélyezése gombra.**

    e. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a TargetProcess hozzáférést biztosítva.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **TargetProcess**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **TargetProcess**lehetőséget.

    ![A TargetProcess hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-targetprocess-test-user"></a>TargetProcess-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a TargetProcess alkalmazásban. A TargetProcess támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a TargetProcess, egy új jön létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [TargetProcess támogatási csapatához.](mailto:support@targetprocess.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a TargetProcess csempére kattint, automatikusan be kell jelentkeznie arra a célfolyamatra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
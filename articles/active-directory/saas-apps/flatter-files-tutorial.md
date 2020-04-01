---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a laposabb fájlokkal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Laposabb fájlok között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 13012474e34af80c84b034703a3b34f0208036bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73156269"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Oktatóanyag: Az Azure Active Directory integrációja laposabb fájlokkal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a laposabb fájlokat az Azure Active Directoryval (Azure AD).
A Laposabb fájlok integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a laposabb fájlokhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Laposabb fájlok (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Laposabb fájlokkal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Laposabb fájlok egyszeri bejelentkezéssel rendelkező előfizetéssel

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A laposabb fájlok támogatják az **IDP** által kezdeményezett SSO-t

## <a name="adding-flatter-files-from-the-gallery"></a>Laposabb fájlok hozzáadása a galériából

A Laposabb fájlok Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a laposabb fájlokat a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha laposabb fájlokat szeretne hozzáadni a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Laposabb fájlok**kifejezést, válassza a **Fízető fájlok** lehetőséget az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Laposabb fájlok az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Laposabb fájlokkal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Laposabb fájlok közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Febb Files szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Laposabb fájlok egyszeri bejelentkezését](#configure-flatter-files-single-sign-on)** - az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre laposabb fájlok teszt felhasználó](#create-flatter-files-test-user)** -, hogy egy megfelelője Britta Simon a Laposabb fájlok, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Laposabb fájlok** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania semmilyen lépést, mivel az alkalmazás már előre integrálva van az Azure-ral.

    ![Laposabb fájlok domain és URL-ek egyszeri bejelentkezési információk](common/preintegrated.png)

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Laposabb fájlok beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-flatter-files-single-sign-on"></a>Laposabb fájlok egyszeri bejelentkezéskonfigurálása

1. Jelentkezzen be a Laposabb fájlok alkalmazásba rendszergazdaként.

2. Kattintson **az IRÁNYÍTÓPULT gombra.** 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Kattintson a **Beállítások**gombra, majd hajtsa végre a **Vállalat** lapon a következő lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Válassza **az SAML 2.0 használata a hitelesítéshez lehetőséget.**
    
    b. Kattintson **az SAML konfigurálása gombra.**

4. Az **SAML konfigurációpárbeszédpanelen** hajtsa végre az alábbi lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. A **Domain (Tartomány)** mezőbe írja be a regisztrált tartományt.
   
   > [!NOTE]
   > Ha még nem rendelkezik regisztrált tartománnyal, lépjen kapcsolatba [support@flatterfiles.com](mailto:support@flatterfiles.com)a Laposabb fájlok támogatási csapatával a . 
    
    b. Az **Identitásszolgáltató URL-címmezőjébe** illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portal űrlapon másolt.
   
    c.  Nyissa meg az alap-64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **identitásszolgáltató tanúsítványának** szövegmezőjébe.

    d. Kattintson a **Frissítés** parancsra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t engedelgetni a Laposabb fájlok hoz való hozzáférést.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Laposabb fájlok lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Laposabb fájlok lehetőséget.**

    ![A Laposabb fájlok hivatkozás a Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-flatter-files-test-user"></a>Laposabb fájlok létrehozása tesztfelhasználó

A cél ebben a szakaszban, hogy hozzon létre egy felhasználó nevű Britta Simon a Laposabb fájlok.

**Britta Simon nevű felhasználó létrehozásához a Laposabb fájlokban hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Fízelgésfájlok** vállalati webhelyére rendszergazdaként.

2. A bal oldali navigációs ablakban kattintson a **Beállítások**gombra, majd a **Felhasználók** fülre.
   
    ![Laposabb fájlok felhasználójának létrehozása](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Kattintson az **Add User** (Felhasználó hozzáadása) elemre. 

4. A **Felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Laposabb fájlok felhasználójának létrehozása](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Az **Utónév** mezőbe írja be a **Britta**nevet.
   
    b. A **Vezetéknév** mezőbe írja be a **Simon**nevet. 
   
    c. Az **E-mail cím** mezőbe írja be Britta e-mail címét az Azure Portalon.
   
    d. Kattintson a **Küldés gombra.**   


### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Laposabb fájlok csempére kattint, automatikusan be kell jelentkeznie a Laposabb fájlokba, amelyekhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


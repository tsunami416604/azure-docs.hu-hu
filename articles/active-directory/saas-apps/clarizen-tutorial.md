---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Clarizenrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Clarizen között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b34e6087f3a6e7f3cc0f46c53d4fe903838d210
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158596"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Oktatóanyag: Az Azure Active Directory integrációja a Clarizenrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Clarizent az Azure Active Directoryval (Azure AD).
A Clarizen integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Clarizenhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezzenek a Clarizenbe (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció clarizen-integrációkonfigurálásához a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Clarizen egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Clarizen támogatja az **IDP** által kezdeményezett SSO-t

## <a name="adding-clarizen-from-the-gallery"></a>Clarizen hozzáadása a galériából

A Clarizen azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Clarizent a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Clarizen-t a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Clarizen**kifejezést, válassza a **Clarizen** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Clarizen az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Clarizennel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a clarizen-i kapcsolódó felhasználó közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Clarizen-nel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Clarizen Single Sign-On](#configure-clarizen-single-sign-on)** --hoz konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Clarizen tesztfelhasználó](#create-clarizen-test-user)** - egy megfelelője Britta Simon a Clarizen, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Clarizen-alkalmazásintegrációs** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![Clarizen Domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy értéket:`Clarizen`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Ezek nem a valós értékek. A tényleges azonosítót és a válasz URL-címét kell használnia. Itt azt javasoljuk, hogy egy karakterlánc egyedi értékét használja azonosítóként. A tényleges értékek értéséhez lépjen kapcsolatba a [Clarizen támogatási csapatával.](https://success.clarizen.com/hc/en-us/requests/new)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Clarizen beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-clarizen-single-sign-on"></a>A Clarizen egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Clarizen vállalati webhelyére rendszergazdaként.

1. Kattintson a felhasználónevére, majd a **Beállítások gombra.**

    ![A felhasználónév alatt a "Beállítások" gombra kattintva](./media/clarizen-tutorial/tutorial_clarizen_001.png "Beállítások")

1. Kattintson a **Globális beállítások** fülre. Ezután az **Összevont hitelesítés**mezőben kattintson a **szerkesztés gombra.**

    !["Globális beállítások" lap](./media/clarizen-tutorial/tutorial_clarizen_002.png "Globális beállítások")

1. Az **Összevont hitelesítés** párbeszédpanelen hajtsa végre a következő lépéseket:

    !["Összevont hitelesítés" párbeszédpanel](./media/clarizen-tutorial/tutorial_clarizen_003.png "Összevont hitelesítés")

    a. Válassza **az Összevont hitelesítés engedélyezése**lehetőséget.

    b. A letöltött tanúsítvány feltöltéséhez kattintson a **Feltöltés** gombra.

    c. A **Bejelentkezési URL-cím** mezőbe írja be a **bejelentkezési URL-cím** értékét az Azure AD-alkalmazás konfigurációs ablakában.

    d. A **Kijelentkezési URL-cím** mezőbe írja be a **kijelentkezési URL-cím** értékét az Azure AD-alkalmazás konfigurációs ablakából.

    e. Válassza **a Post használata**lehetőséget.

    f. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Clarizen hozzáférés ének biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Clarizen**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Clarizen**lehetőséget.

    ![A Clarizen hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-clarizen-test-user"></a>Clarizen tesztfelhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a Clarizenben.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre az alábbi lépéseket:**

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Clarizenbe, felhasználói fiókokat kell kiépítenie. Clarizen esetén kiépítése manuális feladat.

1. Jelentkezzen be a Clarizen vállalati webhelyére rendszergazdaként.

2. Kattintson **a Személyek gombra.**

    ![Az "Emberek" gombra kattintva](./media/clarizen-tutorial/create_aaduser_001.png "People")

3. Kattintson **a Felhasználó meghívása gombra.**

    !["Felhasználó meghívása" gomb](./media/clarizen-tutorial/create_aaduser_002.png "Felhasználók meghívása")

1. A **Személyek meghívása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    !["Személyek meghívása" párbeszédpanel](./media/clarizen-tutorial/create_aaduser_003.png "Személyek meghívása")

    a. Az **E-mail** mezőbe írja be a Britta Simon-fiók e-mail címét.

    b. Kattintson **a Meghívás gombra.**

    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és kövesse a hivatkozást, hogy erősítse meg a fiók, mielőtt aktívvá válik.


### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Clarizen csempére kattint, automatikusan be kell jelentkeznie arra a Clarizenbe, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

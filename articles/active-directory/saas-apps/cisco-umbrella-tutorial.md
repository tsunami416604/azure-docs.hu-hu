---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Cisco Umbrella programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Cisco Umbrella között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee91ca53e32bfdc387dc20054493d02d506a75da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158643"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Oktatóanyag: Az Azure Active Directory integrációja a Cisco Umbrella programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Cisco Umbrella szolgáltatást az Azure Active Directoryval (Azure AD).
A Cisco Umbrella integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Cisco Umbrella szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Cisco Umbrella (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Cisco Umbrella konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Cisco Umbrella egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Cisco Umbrella támogatja az **SP és az IDP** által kezdeményezett SSO-t

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Cisco Umbrella hozzáadása a galériából

A Cisco Umbrella Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cisco Umbrella-t a galériából a felügyelt SaaS-alkalmazások listájához.

**A Cisco Umbrella hozzáadásához hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Cisco Umbrella**kifejezést, válassza a **Cisco Umbrella** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Cisco Umbrella az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést az [Alkalmazás név] szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó [Alkalmazás neve] létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az [Alkalmazás neve] beállításához a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Cisco Umbrella Single Sign-On -,](#configure-cisco-umbrella-single-sign-on)** hogy konfigurálja az egyszeri bejelentkezés i beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Cisco Umbrella teszt felhasználó](#create-cisco-umbrella-test-user)** -, hogy egy megfelelője Britta Simon a Cisco Umbrella, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az [Alkalmazás név] segítségével hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Cisco Umbrella** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania semmilyen lépést, mivel az alkalmazás már előre integrálva van az Azure-ral.

    ![Cisco Umbrella Domain és URL-ek egyszeri bejelentkezési információk](common/both-preintegrated-signon.png)

    a. Ha az alkalmazást **SP** intiated módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    b. Kattintson **a További URL-címek beállítása gombra.**

    c. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://login.umbrella.com/sso`

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra a **metaadat-XML** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Cisco Umbrella beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-cisco-umbrella-single-sign-on"></a>A Cisco Umbrella egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Cisco Umbrella vállalat webhelyére.

2. A menü bal oldalán kattintson a **Rendszergazda** elemre, és keresse meg a **Hitelesítés** menüt, majd kattintson az **SAML gombra.**

    ![Az admin](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Válassza az **Egyéb** lehetőséget, és kattintson a **NEXT gombra.**

    ![A másik](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. A **Cisco Umbrella metaadatai**lapon kattintson a **TOVÁBB**gombra.

    ![A metaadatok](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. A **Metaadatok feltöltése** lapon, ha előre konfigurálta az SAML-t, válassza a Kattintson ide lehetőséget **a módosításhoz,** és kövesse az alábbi lépéseket.

    ![A következő](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. Az **A lehetőség: XML-fájl feltöltése**, töltse fel az **Összevonási metaadat-XML-fájlt,** amelyet az Azure Portalról töltött le, és a metaadatok feltöltése után az alábbi értékek automatikusan automatikusan kitöltődnek, majd kattintson a **TOVÁBB**gombra.

    ![A choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. Az **SAML-konfiguráció ellenőrzése** csoportban kattintson **a SAML-KONFIGURÁCIÓ TESZTELÉSE gombra.**

    ![A teszt](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Kattintson **a MENTÉS gombra.**

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Cisco Umbrella hozzáférést biztosít.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a Cisco **Umbrella**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza a **Cisco Umbrella**lehetőséget.

    ![A Cisco Umbrella link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-cisco-umbrella-test-user"></a>Cisco Umbrella tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Cisco Umbrella szolgáltatásba, ki kell építeni őket a Cisco Umbrella rendszerbe.  
A Cisco Umbrella esetében a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Cisco Umbrella vállalat webhelyére.

2. A menü bal oldalán kattintson a **Rendszergazda** elemre, és keresse meg a **Fiókok lapot.**

    ![A számla](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. A **Fiókok** lapon kattintson a lap jobb felső részén található **Hozzáadás** elemre, és hajtsa végre a következő lépéseket.

    ![A felhasználó](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. Az **Utónév** mezőbe írja be a keresztnevet, például **Britta**.

    b. A **Vezetéknév** mezőbe írja be a vezetéknevet, **például Simon**.

    c. A **Meghatalmazott rendszergazdai szerepkör kiválasztása területen**válassza ki a szerepkörét.
  
    d. Az **E-mail cím** mezőbe írja be a felhasználó e-mail címét, például **brittasimon\@contoso.com**.

    e. A **Jelszó** mezőbe írja be a jelszót.

    f. A **Jelszó megerősítése** mezőben adja meg újra a jelszót.

    g. Kattintson **a CREATE gombra.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Cisco Umbrella csempére kattint, automatikusan be kell jelentkeznie a Cisco Umbrella programba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

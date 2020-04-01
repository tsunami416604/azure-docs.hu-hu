---
title: 'Bemutató: Az Azure Active Directory integrációja az Origamival | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Origami között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fd347f4eb5f77dacc3c9fd61d0e885e9b3ee7959
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095639"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Oktatóanyag: Az Azure Active Directory integrációja az Origamival

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Origami-t az Azure Active Directoryval (Azure AD).
Integrálása Origami az Azure AD biztosítja a következő előnyöket:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az Origamihoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve origami (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció origamival való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Origami egyszeri bejelentkezés engedélyezve előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Origami támogatja **SP** kezdeményezett SSO

## <a name="adding-origami-from-the-gallery"></a>Origami hozzáadása a galériából

Az Origami azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Origami-t a galériából a felügyelt SaaS-alkalmazások listájához.

**Ahhoz, origami a galériából, végezze el a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja **be origami**, válassza **Origami** eredmény panel, majd kattintson **a Hozzáadás** gombra, hogy adjunk az alkalmazás.

     ![Origami az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Origamival egy **Britta Simon**nevű tesztfelhasználó alapján.
Az egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó origami közötti kapcsolat létre kell hozni.

Konfigurálása és tesztelése Azure AD egyszeri bejelentkezés origami, ki kell töltenie a következő építőelemek:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja Origami Single Sign-On](#configure-origami-single-sign-on)** -, hogy konfigurálja a Single Sign-On beállítások alkalmazási oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Origami teszt felhasználó](#create-origami-test-user)** -, hogy egy megfelelője Britta Simon origami, amely kapcsolódik az Azure AD képviselete a felhasználó.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének az Origamival való konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure portalon](https://portal.azure.com/)az **Origami** alkalmazás integrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Origami Domain és URL-ek egyszeri bejelentkezési információk](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Kapcsolat [Origami Ügyfél támogatási csapat,](https://wordpress.org/support/theme/origami) hogy az érték. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Set up Origami** részben másolja a megfelelő URL-t (ek) mint egy a követelmény.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-origami-single-sign-on"></a>Origami egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be az Origami fiók admin jogokkal.

2. A felső menüben kattintson a **Rendszergazda gombra.**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/origami-tutorial/tutorial_origami_51.png)

3. Az Egyszeri bejelentkezés beállítása párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/origami-tutorial/tutorial_origami_531.png)

    a. Válassza **az Egyszeri bejelentkezés engedélyezése**lehetőséget.

    b. Az **identitásszolgáltató bejelentkezési lapjának URL-címmezőjébe** illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    c. Az **identitásszolgáltató kijelentkezési lap URL-címmezőjébe** illessze be a **kijelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    d. A **Tallózás** gombra kattintva feltöltheti az Azure Portalról letöltött tanúsítványt.

    e. Kattintson a **Save Changes** (Módosítások mentése) gombra.

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

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést az Origami hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications (Nagyvállalati alkalmazások**, válassza az Összes **alkalmazás**lehetőséget , majd **az Origami**lehetőséget) lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza **az Origami**lehetőséget .

    ![Az Origami link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-origami-test-user"></a>Hozzon létre Origami teszt felhasználó

Ebben a részben, akkor hozzon létre egy felhasználó nevű Britta Simon origami. 

1. Jelentkezzen be az Origami fiók admin jogokkal.

2. A felső menüben kattintson a **Rendszergazda gombra.**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/origami-tutorial/tutorial_origami_51.png)

3. A **Felhasználók és biztonság** párbeszédpanelen kattintson a **Felhasználók gombra.**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/origami-tutorial/tutorial_origami_54.png)

4. Kattintson **az Új felhasználó hozzáadása gombra.**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/origami-tutorial/tutorial_origami_55.png)

5. Az Új felhasználó hozzáadása párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/origami-tutorial/tutorial_origami_56.png)

    a. A **Felhasználónév** mezőbe írja be a felhasználó e-mail címét, például **brittasimon\@contoso.com**.

    b. A **Jelszó** mezőbe írjon be egy jelszót.

    c. A **Jelszó megerősítése** mezőbe írja be újra a jelszót.

    d. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **Britta.**

    e. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    f. Kattintson a **Mentés** gombra.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/origami-tutorial/tutorial_origami_57.png)

6. **Felhasználói szerepkörök** és **ügyfélhozzáférés hozzárendelése** a felhasználóhoz. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha rákattint az Origami csempe a Hozzáférési panel, akkor automatikusan be kell jelentkeznie az Origami, amelyre beállította SSO. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


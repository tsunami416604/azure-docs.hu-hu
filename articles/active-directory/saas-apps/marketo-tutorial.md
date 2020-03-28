---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Marketóval | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Marketo között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 0488fd1e9bc10d61d6660745acfc8c39becf3a89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159474"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Oktatóanyag: Az Azure Active Directory integrációja a Marketóval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Marketo-t az Azure Active Directoryval (Azure AD).
A Marketo integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Marketo-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Marketo (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Marketo konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Marketo egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Marketo támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-marketo-from-the-gallery"></a>Marketo hozzáadása a galériából

A Marketo Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Marketo-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Marketo-t a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Marketo**kifejezést, válassza a **Marketo** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Marketo az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Marketo-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a Marketo-ban létre kell hozni egy kapcsolatkapcsolatot.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Marketo-val a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Marketo Single Sign-On](#configure-marketo-single-sign-on)** --t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Marketo teszt felhasználó](#create-marketo-test-user)** - egy megfelelője Britta Simon a Marketo, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Marketo** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![Marketo Domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://saml.marketo.com/sp`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [a Marketo ügyféltámogatási csapatával,](https://investors.marketo.com/contactus.cfm) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Marketo beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-marketo-single-sign-on"></a>Marketo single sign-on konfigurálása

1. Ahhoz, hogy Munchkin Id az alkalmazás, jelentkezzen be marketo segítségével admin hitelesítő adatokat, és hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be a Marketo alkalmazásba rendszergazdai hitelesítő adatokkal.
   
    b. Kattintson a felső navigációs ablak **Rendszergazda** gombjára.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Nyissa meg az Integráció menüt, és kattintson a **Munchkin hivatkozásra**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Másolja a képernyőn megjelenő Munchkin-azonosítót, és fejezze be a válasz URL-címét az Azure AD konfigurációs varázslójában.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Az sso konfigurálásához kövesse az alábbi lépéseket:
   
    a. Jelentkezzen be a Marketo alkalmazásba rendszergazdai hitelesítő adatokkal.
   
    b. Kattintson a felső navigációs ablak **Rendszergazda** gombjára.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Nyissa meg az Integráció menüt, és kattintson **az Egyszeri bejelentkezés gombra.**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Az SAML-beállítások engedélyezéséhez kattintson a **Szerkesztés** gombra.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Engedélyezve** Egyszeri bejelentkezési beállítások.
   
    f. Illessze be az **Azure AD-azonosítót**a **Kiállítóazonosító** szövegmezőbe.
   
    g. Az **Entitásazonosító** mezőbe írja be az `http://saml.marketo.com/sp`URL-címet a következőként: .
   
    h. Válassza ki a Felhasználói azonosító helyét **névazonosító elemként**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Ha a felhasználói azonosító nem UPN érték, módosítsa az Attribútum lapon az értéket.
   
    i. Töltse fel a tanúsítványt, amely et letöltött az Azure AD konfigurációs varázsló. **Mentse** a beállításokat.
   
    j. Az Oldalak átirányítása beállításainak szerkesztése.
   
    k. Illessze be a **bejelentkezési URL-címet** a **Bejelentkezési URL szövegmezőbe.**
   
    l. Illessze be a **kijelentkezési URL-címet** a **Kijelentkezés URL-címének** szövegébe.
   
    m. A **Hiba URL-címben**másolja a **Marketo-példány URL-címét,** és a Beállítások mentéséhez kattintson a **Mentés** gombra.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Az sso felhasználók számára való engedélyezéséhez hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be a Marketo alkalmazásba rendszergazdai hitelesítő adatokkal.
   
    b. Kattintson a felső navigációs ablak **Rendszergazda** gombjára.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Nyissa meg a **Biztonság** menüt, és kattintson **a Bejelentkezési beállítások parancsra.**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Jelölje be az **SSO megkövetelése** beállítást, és **mentse** a beállításokat.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_14.png)

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Marketo hozzáférést biztosítva.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Marketo**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Marketo**lehetőséget.

    ![A Marketo link az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-marketo-test-user"></a>Marketo tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Marketo-ban. kövesse ezeket a lépéseket, hogy hozzon létre egy felhasználó marketo platformon.

1. Jelentkezzen be a Marketo alkalmazásba rendszergazdai hitelesítő adatokkal.

2. Kattintson a felső navigációs ablak **Rendszergazda** gombjára.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Keresse meg a **Biztonság** menüt, és kattintson **a Felhasználók & szerepkörök parancsára.**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kattintson az **Új felhasználó meghívása** hivatkozásra a Felhasználók lapon
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Az Új felhasználó meghívása varázslóban töltse ki az alábbi információkat
   
    a. Írja be a felhasználói **e-mail** címet a szövegmezőbe
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Írja be az **Utónevet** a szövegmezőbe
   
    c. Írja be a **vezetéknevet** a szövegmezőbe
   
    d. Kattintson a **Tovább gombra**

6. Az **Engedélyek** lapon jelölje ki a **userRoles elemet,** és kattintson a **Tovább** gombra.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_17.png)
7. A **küldés** gombra kattintva elküldeni a felhasználói meghívót
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_18.png)

8. A felhasználó megkapja az e-mail értesítést, és kattintson a linkre, és változtassa meg a jelszót a fiók aktiválásához. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Marketo csempére kattint, automatikusan be kell jelentkeznie arra a Marketo-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


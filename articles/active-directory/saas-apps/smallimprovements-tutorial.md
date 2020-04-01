---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a kis fejlesztésekkel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a kis fejlesztések között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: d2d0bbc7a6e1c680434041d1b9d55e39a96b6f44
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090364"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Oktatóanyag: Az Azure Active Directory integrációja kisebb fejlesztésekkel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a kis fejlesztéseket az Azure Active Directoryval (Azure AD).
A kis fejlesztések integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a kis fejlesztésekhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a kis fejlesztések (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció kis fejlesztésekkel való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Kis fejlesztések egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A kis fejlesztések támogatják az **SP** által kezdeményezett sso-t

## <a name="adding-small-improvements-from-the-gallery"></a>Kisebb fejlesztések hozzáadása a galériából

A kis fejlesztések azure AD-be való integrálásának konfigurálásához hozzá kell adnia a katalógus kis fejlesztéseit a felügyelt SaaS-alkalmazások listájához.

**Ha kisebb fejlesztéseket szeretne hozzáadni a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Kis javítások**kifejezést, válassza a Kis **fejlesztések lehetőséget** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Kisebb fejlesztések az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést a Kis fejlesztések szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kis fejlesztések közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez kis fejlesztésekkel kell végrehajtania a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Állítsa be a kis fejlesztések egyszeri bejelentkezés](#configure-small-improvements-single-sign-on)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre kis fejlesztések teszt felhasználó](#create-small-improvements-test-user)** - egy megfelelője Britta Simon kis fejlesztések, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének kis fejlesztésekkel való konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Kis fejlesztések** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Kis fejlesztések tartomány és URL-ek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.small-improvements.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Small Improvements ügyféltámogatási csapatával,](mailto:support@small-improvements.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Kis javítások beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-small-improvements-single-sign-on"></a>Kisebb fejlesztések konfigurálása egyszeri bejelentkezés

1. Egy másik böngészőablakban jelentkezzen be a Kis fejlesztések vállalati webhelyére rendszergazdaként.

1. A fő irányítópult lapon kattintson a bal oldalon a **Felügyelet** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Kattintson az **INTEGRÁCIÓk** szakasz **SAML SSO** gombjára.

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Az SSO beállítása lapon hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. A **HTTP-végpont** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    b. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, másolja a tartalmat, majd illessze be az **x509 Tanúsítvány** szövegmezőbe. 

    c. Ha azt szeretné, hogy az SSO és a Bejelentkezési űrlap hitelesítési lehetőség elérhető legyen a felhasználók számára, akkor ellenőrizze a **Hozzáférés engedélyezése bejelentkezéssel / jelszóval is** opciót.  

    d. Adja meg a megfelelő értéket az SSO bejelentkezésgomb elnevezéséhez az **SAML kérdés** szövegmezőjében.  

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
  
    b. A **Felhasználónév** mezőtípusban**brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít a kis fejlesztések.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a Kis **fejlesztések**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Kis fejlesztések lehetőséget.**

    ![Az Alkalmazások lista kis méretű fejlesztései hivatkozása](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-small-improvements-test-user"></a>Kis fejlesztések létrehozása tesztfelhasználó

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a kis fejlesztésekbe, ki kell építeni őket a kis fejlesztésekbe. Kis fejlesztések esetén kiépítése manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a kis fejlesztések vállalati webhelyére rendszergazdaként.

1. A kezdőlapon lépjen a bal oldali menüre, és kattintson az **Adminisztráció parancsra.**

1. Kattintson a **Felhasználói címtár** gombra a Felhasználókezelés szakaszban.

    ![Azure AD-tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Kattintson **a Felhasználók hozzáadása gombra.**

    ![Azure AD-tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. A **Felhasználók hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket: 

    ![Azure AD-tesztfelhasználó létrehozása](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Adja meg a felhasználó **keresztnevét,** például **Britta.**

    b. Adja meg a felhasználó **vezetéknevét,** **például Simon**.

    c. Adja meg a **brittasimon@contoso.com** **felhasználó e-mail címét,** mint a .

    d. Megadhatja a személyes üzenetet is az **Értesítés küldése e-mail** mezőbe. Ha nem kívánja elküldeni az értesítést, törölje a jelet a jelölőnégyzetből.

    e. Kattintson **a Felhasználók létrehozása gombra.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panel kis javítások csempéjére kattint, automatikusan be kell jelentkeznie a kis fejlesztésekbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
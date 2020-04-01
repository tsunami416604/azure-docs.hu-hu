---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a jelenlét-kezelési szolgáltatásokkal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a jelenlét-kezelési szolgáltatások között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7143d0afce7a3644286703a9eba0da1ee45305f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67106545"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Oktatóanyag: Az Azure Active Directory integrációja a jelenlét-kezelési szolgáltatásokkal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a jelenlét-kezelési szolgáltatásokat az Azure Active Directoryval (Azure AD).
A jelenlét-kezelési szolgáltatások integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a jelenlét-kezelési szolgáltatásokhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelenlét-kezelési szolgáltatások (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a jelenlét-kezelési szolgáltatásokkal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Jelenlét-kezelési szolgáltatások egyszeri bejelentkezéssel rendelkező előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A jelenlét-kezelési szolgáltatások támogatják az **SP** által kezdeményezett SSO-t

## <a name="adding-attendance-management-services-from-the-gallery"></a>Jelenlét-kezelési szolgáltatások hozzáadása a galériából

A jelenlét-kezelési szolgáltatások Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a katalógusból a jelenlét-kezelési szolgáltatásokat a felügyelt SaaS-alkalmazások listájához.

**Ha jelenlét-kezelési szolgáltatásokat szeretne hozzáadni a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Jelenlét-kezelési szolgáltatások**kifejezést, válassza **a Jelenlét-kezelési szolgáltatások** lehetőséget az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Jelenlét-kezelési szolgáltatások az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Jelenlét-kezelési szolgáltatásokkal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Jelenlét-kezelési szolgáltatások létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Jelenlét-kezelési szolgáltatásokkal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a jelenlét-kezelési szolgáltatások egyszeri bejelentkezési -](#configure-attendance-management-services-single-sign-on)** konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre jelenlét-kezelési szolgáltatások teszt felhasználó](#create-attendance-management-services-test-user)** - egy megfelelője Britta Simon a jelenlét-kezelési szolgáltatások, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Jelenlét-kezelési szolgáltatások** alkalmazásintegrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Jelenlét-kezelési szolgáltatások tartományés URL egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://id.obc.jp/<tenant information >/`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a jelenlét-kezelési szolgáltatások ügyféltámogatási csapatával,](https://www.obcnet.jp/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Jelenlét-kezelési szolgáltatások beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-attendance-management-services-single-sign-on"></a>A jelenlét-kezelési szolgáltatások egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Jelenlétkezelési szolgáltatások vállalati webhelyére rendszergazdaként.

1. Kattintson az **SAML-hitelesítésre** a **Biztonságkezelés szakaszban.**

    ![Jelenlét-kezelési szolgáltatások konfigurációja](./media/attendancemanagementservices-tutorial/user1.png)

1. Hajtsa végre a következő lépéseket:

    ![Jelenlét-kezelési szolgáltatások konfigurációja](./media/attendancemanagementservices-tutorial/user2.png)

    a. Válassza **az SAML-hitelesítés használata**lehetőséget.

    b. Az **Azonosító** szövegmezőbe illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. A **hitelesítési végpont URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-érték** értékét, amelyet az Azure Portalról másolt.

    d. Kattintson **a Fájl kiválasztása** az Azure AD-ből letöltött tanúsítvány feltöltéséhez.

    e. Válassza **a Jelszó-hitelesítés letiltása**lehetőséget.

    f. Kattintson **a Regisztráció gombra**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező `brittasimon@yourcompanydomain.extension`típusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a jelenlétkezelési szolgáltatásokhoz.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd a **Jelenlét-kezelési szolgáltatások lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Jelenlét-kezelési szolgáltatások lehetőséget.**

    ![A Jelenlét-kezelési szolgáltatások hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-attendance-management-services-test-user"></a>Jelenlétkezelési szolgáltatások tesztfelhasználójának létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a jelenlét-kezelési szolgáltatásokba, ki kell építeni őket a Jelenlét-kezelési szolgáltatásokba. A jelenlét-kezelési szolgáltatások esetében a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Jelenlétkezelési Szolgáltatások vállalati webhelyére rendszergazdaként.

1. Kattintson a **Felhasználókezelés** elemre a **Biztonságkezelés szakaszban.**

    ![Alkalmazott hozzáadása](./media/attendancemanagementservices-tutorial/user5.png)

1. Kattintson **az Új szabályok bejelentkezés lehetőségre.**

    ![Alkalmazott hozzáadása](./media/attendancemanagementservices-tutorial/user3.png)

1. Az **OBCiD információs szakaszban** hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/attendancemanagementservices-tutorial/user4.png)

    a. Az **OBCiD** mezőbe írja be a `BrittaSimon\@contoso.com`felhasználó e-mail címét, például .

    b. A **Jelszó** mezőbe írja be a felhasználó jelszavát.

    c. Kattintson **a Regisztráció gombra**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panel jelenlétkezelési szolgáltatásai csempére kattint, automatikusan be kell jelentkeznie a jelenlét-kezelési szolgáltatásokba, amelyekhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Freedcampdel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Freedcamp között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de1ae135df4cd070fe9c6ee322e304caa1e17c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67101909"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Oktatóanyag: A Freedcamp integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Freedcampet az Azure Active Directoryval (Azure AD). Ha integrálja a Freedcamp-et az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Freedcamp.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve Freedcamp az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Freedcamp egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A Freedcamp támogatja az **SP és az IDP** által kezdeményezett SSO-t.

## <a name="adding-freedcamp-from-the-gallery"></a>Freedcamp hozzáadása a galériából

A Freedcamp Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Freedcamp-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a galéria szakaszban** írja be a **Freedcamp** kifejezést a keresőmezőbe.
1. Válassza a **Freedcamp** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Freedcamp segítségével egy **Britta Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a freedcamp kapcsolódó felhasználója között.

Az Azure AD SSO freedcamp-es konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[A Freedcamp konfigurálásával](#configure-freedcamp)** konfigurálja az SSO-beállításokat az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
5. **[Hozzon létre Freedcamp tesztfelhasználót,](#create-freedcamp-test-user)** hogy a Freedcamp-ben lévő Britta Simon megfelelője, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Freedcamp** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    1. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A felhasználók saját ügyféltartományukhoz viszonyítva is megadhatják az URL-értékeket, és nem feltétlenül a minta, `freedcamp.com`megadhatják az ügyféldomainre vonatkozó bármely értéket, amely az alkalmazáspéldányukra jellemző. Is felveheti a kapcsolatot [Freedcamp Ügyfél támogatási csapat](mailto:devops@freedcamp.com) további információkért url mintákat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Freedcamp beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Freedcamp konfigurálása

1. A Freedcamp konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Setup Freedcamp** irányítja, hogy a Freedcamp alkalmazás. Innen adja meg az admin hitelesítő adatokat a Freedcamp-be való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-5.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Freedcamp-et, nyisson meg egy új böngészőablakot, és jelentkezzen be a Freedcamp vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A lap jobb felső sarkában kattintson a **profilra,** majd keresse meg a **Saját fiók (Saját fiók) lapot.**

    ![Freedcamp konfiguráció](./media/freedcamp-tutorial/config01.png)

5. A menüsor bal oldalán kattintson az **SSO-ra,** és az **SSO kapcsolatok** lapon hajtsa végre a következő lépéseket:

    ![Freedcamp konfiguráció](./media/freedcamp-tutorial/config02.png)

    a. A **Cím** mezőbe írja be a címet.

    b. Az **Entitásazonosító** szövegmezőben illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. A **Bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    d. Nyissa meg a Base64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát, és illessze be a **Tanúsítvány** mezőbe.

    e. Kattintson a **Küldés gombra.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon Britta Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Freedcamp-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Freedcamp**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-freedcamp-test-user"></a>Freedcamp tesztfelhasználó létrehozása

Az Azure AD-felhasználók engedélyezéséhez jelentkezzen be a Freedcamp be, ki kell építeni a Freedcamp. A Freedcamp, kiépítése a manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Egy másik böngészőablakban jelentkezzen be a Freedcamp be biztonsági rendszergazdaként.

2. A lap felső és jobb oldali sarkában kattintson a **profilra,** majd keresse meg a **Rendszer kezelése lapot.**

    ![Freedcamp konfiguráció](./media/freedcamp-tutorial/config03.png)

3. A Rendszer kezelése lap jobb oldalán hajtsa végre az alábbi lépéseket:

    ![Freedcamp konfiguráció](./media/freedcamp-tutorial/config04.png)

    a. Kattintson a **Felhasználók hozzáadása vagy meghívása elemre.**

    b. Az **E-mail** mezőbe írja be `Brittasimon@contoso.com`a felhasználó e-mail címét, például .

    c. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a Freedcamp csempét, automatikusan be kell jelentkeznie arra a Freedcampbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
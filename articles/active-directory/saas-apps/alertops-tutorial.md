---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az AlertOps-szal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az AlertOps között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b8cb06712e370972e0b8fec518c37d078262e0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107058"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Oktatóanyag: AlertOps integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az AlertOps-ot az Azure Active Directoryval (Azure AD). Ha integrálja az AlertOps-ot az Azure AD-vel, a következőket teheti:

* Az Azure AD- ban, aki rendelkezik hozzáféréssel alertops.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve AlertOps az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* AlertOps egyszeri bejelentkezés (SSO) engedélyezve lévő előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. AlertOps támogatja az **SP és IDP** kezdeményezett SSO.

## <a name="adding-alertops-from-the-gallery"></a>AlertOps hozzáadása a gyűjteményből

Az AlertOps azure AD-be való integrálásának konfigurálásához hozzá kell adnia az AlertOps-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből** szakaszban írja **alertops** a keresőmezőbe.
1. Válassza az **AlertOps** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az AlertOps segítségével egy **Britta Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó AlertOps között.

Az Azure AD SSO beállítása és tesztelése az AlertOps segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[Az AlertOps konfigurálásával](#configure-alertops)** konfigurálja az SSO-beállításokat az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
5. **[Hozzon létre AlertOps tesztfelhasználó,](#create-alertops-test-user)** hogy egy megfelelője Britta Simon alertops, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **AlertOps** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    1. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.alertops.com`

    1. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az AlertOps ügyféltámogatási csapatával,](mailto:support@alertops.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Set up AlertOps (AlertOps beállítása)** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>AlertOps konfigurálása

1. Az AlertOps konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzábővítményt a böngészőhöz, kattintson a **Setup AlertOps** irányítja, hogy a AlertOps alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat az AlertOps-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-5.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani az AlertOps-ot, nyisson meg egy új webböngészőablakot, és jelentkezzen be rendszergazdaként az AlertOps vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Kattintson a bal oldali navigációs panel **Fiókbeállításaira.**

    ![AlertOps-konfiguráció](./media/alertops-tutorial/configure1.png)

5. Az **Előfizetésbeállításai** lapon válassza az **SSO** lehetőséget, és hajtsa végre a következő lépéseket:

    ![AlertOps-konfiguráció](./media/alertops-tutorial/configure2.png)

    a. Jelölje be **az Egyszeri bejelentkezés(Egyszeri bejelentkezés)** jelölőnégyzetet.

    b. Válassza ki az **Azure Active Directoryt** **sso-szolgáltatóként** a legördülő menüből.

    c. A **Kiállító URL-címmezőjében** használja az azonosító értékét, amelyet az Azure Portal **alapszintű SAML-konfiguráció** szakaszában használt.

    d. Az **SAML-végpont URL-címszövegbe** illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    e. Az **SLO végpont URL-címszövegbe** illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    f. Válassza ki **az SHA256-ot** **SAML aláírási algoritmusként** a legördülő menüből.

    g. Nyissa meg a letöltött tanúsítványfájlt(Base64) a Jegyzettömbben. Másolja a tartalmát a vágólapra, majd illessze be az X.509 Tanúsítvány szövegmezőbe.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával alertops hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **AlertOps**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-alertops-test-user"></a>AlertOps-tesztfelhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be az AlertOps vállalati webhelyére rendszergazdaként.

2. Kattintson a bal oldali navigációs panel **felhasználók** elemére.

    ![AlertOps-konfiguráció](./media/alertops-tutorial/user1.png)

3. Válassza **a Felhasználó hozzáadása**lehetőséget.

    ![AlertOps-konfiguráció](./media/alertops-tutorial/user2.png)

4. A **Felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![AlertOps-konfiguráció](./media/alertops-tutorial/user3.png)

    a. A **Bejelentkezési felhasználónév** mezőbe írja be a felhasználó felhasználónevét, például **Brittasimon.**

    b. A **Hivatalos e-mail** szövegmezőbe írja be a felhasználó e-mail címét, mint **brittasimon\@contoso.com**.

    c. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **Britta.**

    d. A **Vezetéknév** mezőbe írja be a felhasználó keresztnevét, például **Simon**.

    e. Válassza ki a **Típus** értéket a legördülő menüből a szervezet szerint.

    f. Válassza ki a felhasználó **szerepkörét** a legördülő menüből a szervezet szerint.

    g. Válassza a **Hozzáadás** lehetőséget.

### <a name="test-sso"></a>SSO tesztelése

Amikor kiválasztja az AlertOps csempét a hozzáférési panelen, automatikusan be kell jelentkeznie az AlertOps-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
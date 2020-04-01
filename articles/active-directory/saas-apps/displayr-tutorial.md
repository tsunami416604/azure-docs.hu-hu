---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Displayr-rel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Displayr között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cd1785595cf2f6b2401837780106f52fdc97e36
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103992"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Oktatóanyag: A Displayr integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Displayr-t az Azure Active Directoryval (Azure AD). Ha integrálja a Displayrt az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Displayr-hez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Displayr az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Displayr egyszeri bejelentkezés (SSO) kompatibilis előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A Displayr támogatja az **SP** által kezdeményezett SSO-t.

## <a name="adding-displayr-from-the-gallery"></a>Megjelenítő hozzáadása a galériából

A Displayr Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Displayrt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Gyűjtemény hozzáadás szakaszába** írja be a **Displayr** kifejezést a keresőmezőbe.
1. Válassza **a Megjelenítő** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Displayr segítségével egy **Britta Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Displayr.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in Displayr.

Az Azure AD SSO Display display-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[A Displayr konfigurálása](#configure-displayr)** az SSO-beállítások alkalmazásoldali beállításához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
5. **[Hozzon létre Displayr teszt felhasználó,](#create-displayr-test-user)** hogy egy megfelelője Britta Simon a Displayr, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg a **Displayr-alkalmazásintegrációs** lapot a **Kezelés** szakaszban, és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A beállítások szerkesztéséhez kattintson az **egységes bejelentkezés saml-lel** lapon az **egyszerű SAML-konfiguráció** szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre a következő lépést:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YOURDOMAIN>.displayr.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba a [DisplayR ügyfél támogatási csapatával,](mailto:support@displayr.com) hogy megkapja ezeket az értékeket. Az Azure Portal alapszintű SAML-konfigurációs szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a Tanúsítvány **(Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A Displayr-alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a Felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Displayr alkalmazás arra számít, hogy még néhány attribútum ot ad vissza az SAML válaszban. A **Felhasználói attribútumok & Jogcímek** párbeszédpanel **jogcímek (előzetes verzió)** szakaszában hajtsa végre az alábbi lépéseket:

    a. Kattintson a **jogcímben visszaadott csoportok**melletti **tollra.**

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Válassza a minden **csoport lehetőséget** a rádiólistából.

    c. Válassza a **Csoportazonosító** **forrásattribútumát.**

    d. Jelölje **be a Csoportjogcím nevének testreszabása**jelölőnégyzetet.

    e. Jelölje **be a Emit csoportok szerepkörjogcímekként jelölőnégyzetet.**

    f. Kattintson a **Mentés** gombra.

1. A **Beállítási megjelenítő** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>A Displayr konfigurálása

1. A Displayr konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzábővítményt a böngésző, kattintson a **Setup Displayr** irányítja, hogy a Displayr alkalmazás. Innen adja meg az admin hitelesítő adatokat a Displayr-be való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Displayr alkalmazást, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Displayr vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Kattintson a **Beállítások** gombra, majd keresse meg a **Fiók menüt.**

    ![Konfiguráció](./media/displayr-tutorial/config01.png)

5. Váltson a felső menü **Beállítások pontjára,** és görgessen le a lapon az **Egyszeri bejelentkezés (SAML) konfigurálása )** gombra kattintva.

    ![Konfiguráció](./media/displayr-tutorial/config02.png)

6. Az **Egyszeri bejelentkezés (SAML)** lapon hajtsa végre az alábbi lépéseket:

    ![Konfiguráció](./media/displayr-tutorial/config03.png)

    a. Jelölje be **az Egyszeri bejelentkezés engedélyezése (SAML)** jelölőnégyzetet.

    b. Másolja a tényleges **azonosító** értékét az Azure AD **Alapszintű SAML konfigurációs** szakaszába, és illessze be a **Kiállító** szövegmezőbe.

    c. A **Bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    d. A **Kijelentkezés URL-címe** mezőbe illessze be a **Kijelentkezés URL-címének**értékét, amelyet az Azure Portalról másolt.

    e. Nyissa meg a Tanúsítvány (Base64) programot a Jegyzettömbben, másolja a tartalmát, és illessze be a **Tanúsítvány** szövegmezőbe.

    f. **A csoportleképezések** megadása nem kötelező.

    g. Kattintson a **Mentés** gombra.  

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Displayr-hez.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Displayr**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-displayr-test-user"></a>Displayr tesztfelhasználó létrehozása

Az Azure AD-felhasználók engedélyezéséhez jelentkezzen be a Displayr-be, ki kell építeniőket a Displayr-be. A Displayr-ben a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Displayr berendszergazdaként.

2. Kattintson a **Beállítások** gombra, majd keresse meg a **Fiók menüt.**

    ![Kijelző konfigurációja](./media/displayr-tutorial/config01.png)

3. Váltson a **beállításokra** a felső menüből, és görgessen lefelé az oldalon, amíg a **Felhasználók** szakaszra nem kattint, majd kattintson az **Új felhasználó gombra.**

    ![Kijelző konfigurációja](./media/displayr-tutorial/config07.png)

4. Az **Új felhasználó** lapon hajtsa végre az alábbi lépéseket:

    ![Kijelző konfigurációja](./media/displayr-tutorial/config06.png)

    a. A **Név** mezőbe írja be a felhasználó nevét, például **Brittasimon**.

    b. Az **E-mail** szöveg mezőbe írja `Brittasimon@contoso.com`be a felhasználó e-mail címét, például .

    c. Válassza ki a megfelelő **csoporttagságot.**

    d. Kattintson a **Mentés** gombra.

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a Displayr csempét, automatikusan be kell jelentkeznie arra a megjelenítőbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

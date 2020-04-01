---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Teamphoria-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Teamphoria között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2631b34f5658c9d4f76ca26d378bc63fe59ad156
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373266"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Teamphoria-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Teamphoria-t az Azure Active Directoryval (Azure AD). Ha integrálja a Teamphoria-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Teamphoria.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Teamphoria az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Teamphoria egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Teamphoria támogatja **SP** kezdeményezett SSO

## <a name="adding-teamphoria-from-the-gallery"></a>Teamphoria hozzáadása a galériából

A Teamphoria Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Teamphoria-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Teamphoria** kifejezést a keresőmezőbe.
1. Válassza a **Teamphoria** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Teamphoria számára

Konfigurálja és tesztelje az Azure AD-sSO-t a Teamphoria segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a teamphoria kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Teamphoria segítségével hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Teamphoria SSO-t](#configure-teamphoria-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Teamphoria teszt felhasználó](#create-teamphoria-test-user)** - egy megfelelője B.Simon a Teamphoria, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Teamphoria** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a Teamphoria ügyféltámogatási csapatával](https://www.teamphoria.com/) az érték leállításához. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Teamphoria beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Teamphoria hozzáférésének biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Teamphoria**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-teamphoria-sso"></a>Teamphoria SSO konfigurálása

1. A Teamphoria konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Beállítása Teamphoria** irányítja, hogy a Teamphoria alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a Teamphoria-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Teamphoria alkalmazást, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Teamphoria vállalati webhelyére, és hajtsa végre az alábbi lépéseket:

4. Lépjen a bal oldali eszköztár **ADMIN SETTINGS** beállítására, és a Beállítás lap csoportban kattintson a **SINGLE SIGN-ON gombra** az Egyszeri bejelentkezés konfigurációs ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Kattintson **az ÚJ IDENTITÁSSZOLGÁLTATÓ HOZZÁADÁSA** lehetőségre a jobb felső sarokban az SSO beállításainak hozzáadásához szolgáló űrlap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Adja meg a részleteket az alábbi mezőkben.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **DISPLAY NAME**: Adja meg a megjelenítendő nevét a plugin az admin oldalon.

    b. **GOMB NEVE**: Annak a lapnak a neve, amely az SSO-n keresztüli bejelentkezéshez a bejelentkezési oldalon jelenik meg.

    c. **TANÚSÍTVÁNY:** Nyissa meg a korábban letöltött tanúsítványt az Azure Portalról a jegyzettömbben, másolja az azonos tartalmát, és illessze be ide a mezőbe.

    d. **BELÉPÉSI PONT**: Illessze be a korábban az Azure Portalról másolt **bejelentkezési URL-címet.**

    e. Váltson a lehetőség **ről BE gombra,** és kattintson a **MENTÉS gombra.**

### <a name="create-teamphoria-test-user"></a>Teamphoria tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Teamphoria szolgáltatásba, ki kell építeni őket a Teamphoria-ba. Teamphoria esetén kiépítése manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Teamphoria vállalati webhelyére rendszergazdaként.

1. Kattintson a **rendszergazdai** beállításokra a bal oldali eszköztáron és a **MANAGE** lapon Kattintson a **FELHASZNÁLÓK** elemre a felhasználók számára a rendszergazdai oldal megnyitásához.

    ![Alkalmazott hozzáadása](./media/teamphoria-tutorial/admin_manage_users.png)

1. Kattintson a **KÉZI MEGHÍVÁS** opcióra.

    ![Személyek meghívása](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Ezen a lapon hajtsa végre a következő műveleteket.

    ![Személyek meghívása](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Az **E-MAIL CÍM** mezőbe írja be a felhasználó **e-mail címét,** például B.Simon.

    b. A **FIRST NAME** mezőbe írja be a felhasználó keresztnevét , például **B**.

    c. A **VEZETÉKNÉV** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    d. Kattintson **az 1 felhasználó meghívása gombra.** A felhasználónak el kell fogadnia a meghívást, hogy létrejön a rendszerben.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Teamphoria csempére kattint, automatikusan be kell jelentkeznie arra a Teamphoriába, amelyhez beállította az sso-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Teamphoria szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)


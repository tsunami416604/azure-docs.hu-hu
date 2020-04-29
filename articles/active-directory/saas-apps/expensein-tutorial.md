---
title: 'Oktatóanyag: Azure Active Directory integráció a ExpenseIn-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ExpenseIn között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09542013dff3a18965d1070216a938c26a144e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102839"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Oktatóanyag: a ExpenseIn és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ExpenseIn a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az ExpenseIn-t az Azure AD-vel, a következőket teheti:

* A ExpenseIn-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a ExpenseIn az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* ExpenseIn egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A ExpenseIn **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="adding-expensein-from-the-gallery"></a>ExpenseIn hozzáadása a gyűjteményből

A ExpenseIn Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a ExpenseIn a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ExpenseIn** kifejezést a keresőmezőbe.
1. Válassza ki a **ExpenseIn** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a ExpenseIn a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a ExpenseIn-ben.

Az Azure AD SSO és a ExpenseIn konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. **[Konfigurálja a ExpenseIn](#configure-expensein)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre ExpenseIn-tesztelési felhasználót](#create-expensein-test-user)** , hogy a ExpenseIn B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **ExpenseIn** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-cím egyikét:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:`https://app.expensein.com/saml`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához, majd kattintson a **Letöltés** gombra a **tanúsítvány** letöltéséhez, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. A **ExpenseIn beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>ExpenseIn konfigurálása

1. A ExpenseIn belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés ExpenseIn** gombra a ExpenseIn alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a ExpenseIn való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a ExpenseIn, nyisson meg egy új böngészőablakot, és jelentkezzen be a ExpenseIn vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Kattintson a **rendszergazda** elemre az oldal tetején, majd navigáljon az **egyszeri bejelentkezéshez** , és kattintson a **szolgáltató hozzáadása**parancsra.

     ![ExpenseIn-konfiguráció](./media/expenseIn-tutorial/config01.png)

5. Az **új identitás-szolgáltató** előugró ablakában hajtsa végre a következő lépéseket:

    ![ExpenseIn-konfiguráció](./media/expenseIn-tutorial/config02.png)

    a. A **szolgáltató neve** szövegmezőbe írja be a következő nevet: pl.: Azure.

    b. Válassza az **Igen** lehetőséget, ha **engedélyezi a szolgáltató Intitated való bejelentkezést**.

    c. A **cél URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    d. A **kiállító** szövegmezőbe illessze be az **Azure ad-azonosító**értékét, amelyet a Azure Portalból másolt.

    e. Nyissa meg a tanúsítványt (Base64) a Jegyzettömbben, másolja ki a tartalmát, és illessze be a **tanúsítvány** szövegmezőbe.

    f. Kattintson a **Létrehozás**gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ExpenseIn.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **ExpenseIn**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-expensein-test-user"></a>ExpenseIn-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a ExpenseIn, a ExpenseIn kell kiépíteni őket. A ExpenseIn-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a ExpenseIn rendszergazdaként.

2. A lap tetején kattintson a **rendszergazda** elemre, majd keresse meg a **felhasználók** lapot, és kattintson az **új felhasználó**lehetőségre.

     ![ExpenseIn-konfiguráció](./media/expenseIn-tutorial/config03.png)

3. A **részletek** előugró ablakában hajtsa végre a következő lépéseket:

    ![ExpenseIn-konfiguráció](./media/expenseIn-tutorial/config04.png)

    a. Az **Utónév** szövegmezőbe írja be a (z) " **B**" felhasználó utónevét.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**nevet.

    c. Az **e-mail** szövege szövegmezőbe írja be a felhasználóhoz hasonló `B.Simon@contoso.com`e-mail címet.

    d. Kattintson a **Létrehozás**gombra.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a ExpenseIn csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a ExpenseIn, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

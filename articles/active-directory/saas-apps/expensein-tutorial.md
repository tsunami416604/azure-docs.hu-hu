---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a ExpenseIn programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a ExpenseIn között.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102839"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Oktatóanyag: A expensein integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a ExpenseIn szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a ExpenseIn-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a ExpenseIn.Control in Azure AD who has access to ExpenseIn.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve expensein az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* ExpenseIn egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A ExpenseIn támogatja az **SP-t és az IDP** által kezdeményezett sso-t.

## <a name="adding-expensein-from-the-gallery"></a>ExpenseIn hozzáadása a galériából

A ExpenseIn azure AD-be való integrálásának konfigurálásához hozzá kell adnia a expensein-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Költséga** keresőmezőbe jelölőnégyzetet.
1. Válassza **a ExpenseIn** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD-sSO-t a ExpenseIn segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a ExpenseInben.

Az Azure AD SSO költséggel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[A ExpenseIn konfigurálásával](#configure-expensein)** konfigurálja az SSO-beállításokat az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
5. **[Hozzon létre expensein tesztfelhasználó,](#create-expensein-test-user)** hogy egy megfelelője B.Simon a ExpenseIn, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **ExpenseIn** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    A **Válasz URL-cím** mezőjébe írja be az URL-cím egyikét:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://app.expensein.com/saml`

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** csoportban kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához, majd a **Letöltés** gombra a **tanúsítvány letöltéséhez (Base64)** és a számítógépre mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. A **Költség beállítása csoportban** másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Költség konfigurálása

1. A ExpenseIn konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Setup ExpenseIn** irányítja, hogy a ExpenseIn alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a ExpenseIn-be való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-5.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a ExpenseIn programot, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a ExpenseIn vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Kattintson a **rendszergazda** a lap tetején, majd keresse meg **az Egyszeri bejelentkezés,** és kattintson **a Szolgáltató hozzáadása**gombra .

     ![KöltségA konfigurációban](./media/expenseIn-tutorial/config01.png)

5. Az **Új identitásszolgáltató** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![KöltségA konfigurációban](./media/expenseIn-tutorial/config02.png)

    a. A **Szolgáltató neve** mezőbe írja be a nevet, például ex:Azure.

    b. Válassza az **Igen** **lehetőséget a Szolgáltató intitated bejelentkezése ként**lehetőséget.

    c. A **Cél URL-cím** mezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    d. A **Kiállító** szövegmezőbe illessze be az **Azure AD-azonosító**értékét, amelyet az Azure Portalról másolt.

    e. Nyissa meg a Tanúsítvány (Base64) programot a Jegyzettömbben, másolja a tartalmát, és illessze be a **Tanúsítvány** mezőbe.

    f. Kattintson **a Létrehozás gombra.**

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a ExpenseIn hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **ExpenseIn**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-expensein-test-user"></a>Költségin tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a ExpenseIn-be, ki kell építeni őket a ExpenseIn-be. A ExpenseIn alkalmazásban a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a ExpenseIn-be rendszergazdaként.

2. Kattintson a **rendszergazda** a lap tetején, majd keresse meg a **felhasználók** és kattintson **az Új felhasználó gombra**.

     ![KöltségA konfigurációban](./media/expenseIn-tutorial/config03.png)

3. A **Részletek** előugró ablakban hajtsa végre a következő lépéseket:

    ![KöltségA konfigurációban](./media/expenseIn-tutorial/config04.png)

    a. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **B.**

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    c. Az **E-mail** szöveg mezőbe írja `B.Simon@contoso.com`be a felhasználó e-mail címét, például .

    d. Kattintson **a Létrehozás gombra.**

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a ExpenseIn csempét, a rendszer automatikusan bejelentkezik arra a Költségcsoportba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

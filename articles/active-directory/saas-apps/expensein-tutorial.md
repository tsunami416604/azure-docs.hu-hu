---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ExpenseIn |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és ExpenseIn között.
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
ms.date: 05/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b849e63b758dca777df2be682b28bc4e10e58330
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481130"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Oktatóanyag: ExpenseIn integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a ExpenseIn integrálása az Azure Active Directory (Azure AD) lesz. ExpenseIn integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá ExpenseIn Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve ExpenseIn az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* ExpenseIn egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a ExpenseIn **SP és IDP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-expensein-from-the-gallery"></a>ExpenseIn hozzáadása a katalógusból

Az Azure AD integrálása a ExpenseIn konfigurálásához hozzá kell ExpenseIn a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **ExpenseIn** kifejezést a keresőmezőbe.
1. Válassza ki **ExpenseIn** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata ExpenseIn **b Simon**. Az SSO működjön kell ExpenseIn az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO ExpenseIn tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a ExpenseIn](#configure-expensein)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés a b Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  b Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre ExpenseIn tesztfelhasználót](#create-expensein-test-user)**  van egy megfelelője a b Simon ExpenseIn, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **ExpenseIn** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre a következő lépést:

    Az a **válasz URL-cím** szöveg írja be az URL-címe közül bármelyik:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe:  `https://app.expensein.com/saml`

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** kattintson**Letöltése** letöltéséhez a **tanúsítvány (Base64)** , és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozás](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Az a **ExpenseIn beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>ExpenseIn konfigurálása

1. Egy másik böngészőablakban jelentkezzen be ExpenseIn rendszergazdaként.

2. Kattintson a **rendszergazdai** majd lépjen az oldal felső részén **egyszeri bejelentkezés** kattintson **Hozzáadás szolgáltató**.

     ![ExpenseIn konfiguráció](./media/expenseIn-tutorial/config01.png)

3. Az a **új identitásszolgáltató** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![ExpenseIn konfiguráció](./media/expenseIn-tutorial/config02.png)

    a. Az a **szolgáltatónevet** szöveg írja be a nevét, mint például az ex: Azure.

    b. Az a **cél URL-cím** szöveg mezőbe illessze be az értékét **bejelentkezési URL-cím**, az Azure Portalról másolt.

    c. Az a **kibocsátó** szöveg mezőbe illessze be az értékét **az Azure AD-azonosító**, az Azure Portalról másolt.

    d. A Jegyzettömb alkalmazásban nyissa meg a tanúsítvány (Base64), másolja a tartalmat, és illessze be a **tanúsítvány** szövegmezőben.

    e. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon b Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban b Simon által biztosított hozzáférés ExpenseIn Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **ExpenseIn**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **b Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-expensein-test-user"></a>ExpenseIn tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ExpenseIn bejelentkezni, akkor ki kell építeni ExpenseIn be. ExpenseIn a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként ExpenseIn.

2. Kattintson a **rendszergazdai** majd lépjen az oldal felső részén **felhasználók** kattintson **új felhasználó**.

     ![ExpenseIn konfiguráció](./media/expenseIn-tutorial/config03.png)

3. Az a **részletek** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![ExpenseIn konfiguráció](./media/expenseIn-tutorial/config04.png)

    a. A **Utónév** szöveget adja meg például a felhasználó utónevét **B**.

    b. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **Simon**.

    c. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó `B. Simon@contoso.com`.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A ExpenseIn csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a ExpenseIn, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

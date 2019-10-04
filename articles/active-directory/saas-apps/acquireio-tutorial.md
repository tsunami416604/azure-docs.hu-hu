---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező AcquireIO |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és AcquireIO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75a92e78e7293316cad6e567ae49c4998299415c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544269"
---
# <a name="tutorial-integrate-acquireio-with-azure-active-directory"></a>Oktatóanyag: AcquireIO integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a AcquireIO integrálása az Azure Active Directory (Azure AD) lesz. AcquireIO integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá AcquireIO Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve AcquireIO az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* AcquireIO egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a AcquireIO **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-acquireio-from-the-gallery"></a>AcquireIO hozzáadása a katalógusból

Az Azure AD integrálása a AcquireIO konfigurálásához hozzá kell AcquireIO a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **AcquireIO** kifejezést a keresőmezőbe.
1. Válassza ki **AcquireIO** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata AcquireIO **B.Simon**. Az SSO működjön kell AcquireIO az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO AcquireIO tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a AcquireIO](#configure-acquireio)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre AcquireIO tesztfelhasználót](#create-acquireio-test-user)**  van egy megfelelője a B.Simon AcquireIO, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **AcquireIO** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre a következő lépést:

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Az érték nem valódi. A tényleges válasz URL-cím a később ismertetett fog kapni a **konfigurálása AcquireIO** az oktatóanyag szakaszában. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **AcquireIO beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-acquireio"></a>AcquireIO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be AcquireIO rendszergazdaként.

2. A menüt a bal oldali menüjében kattintson a **App Store**.

     ![AcquireIO konfiguráció](./media/acquireio-tutorial/config01.png)

3. Görgessen lefelé legfeljebb **Active Directory** , majd kattintson a **telepítése**.

    ![AcquireIO konfiguráció](./media/acquireio-tutorial/config02.png)

4. Az Active Directory előugró ablakban hajtsa végre az alábbi lépéseket:

    ![AcquireIO konfiguráció](./media/acquireio-tutorial/config03.png)

    a. Kattintson a **másolási** a válasz URL-cím a példány másolja és illessze be azt a **válasz URL-cím** szövegmezőjébe **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    b. Az a **bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    c. Nyissa meg a Base64-kódolású tanúsítványt a Jegyzettömbben, másolja a tartalmat, és illessze be a **X.509-tanúsítvány** szövegmezőben.

    d. Kattintson a **azonnali kapcsolódás**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban B.Simon által biztosított hozzáférés AcquireIO Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **AcquireIO**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-acquireio-test-user"></a>AcquireIO tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók AcquireIO bejelentkezni, akkor ki kell építeni AcquireIO be. AcquireIO a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Egy másik böngészőablakban jelentkezzen be AcquireIO rendszergazdaként.

2. A menüt a bal oldali menüjében kattintson az **profilok** , és keresse meg **profil hozzáadása**.

     ![AcquireIO konfiguráció](./media/acquireio-tutorial/config04.png)

3. Az a **Hozzáadás ügyfél** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![AcquireIO konfiguráció](./media/acquireio-tutorial/config05.png)

    a. A **neve** szöveget adja meg például a felhasználó nevét **B.simon**.

    b. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó **B.simon@contoso.com** .

    c. Kattintson a **Submit** (Küldés) gombra.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A AcquireIO csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a AcquireIO, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
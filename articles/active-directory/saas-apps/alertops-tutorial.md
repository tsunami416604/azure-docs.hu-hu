---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező AlertOps |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és AlertOps között.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107058"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Oktatóanyag: AlertOps integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a AlertOps integrálása az Azure Active Directory (Azure AD) lesz. AlertOps integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá AlertOps Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve AlertOps az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* AlertOps egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a AlertOps **SP és IDP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-alertops-from-the-gallery"></a>AlertOps hozzáadása a katalógusból

Az Azure AD integrálása a AlertOps konfigurálásához hozzá kell AlertOps a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **AlertOps** kifejezést a keresőmezőbe.
1. Válassza ki **AlertOps** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata AlertOps **Britta Simon**. Az SSO működjön kell AlertOps az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO AlertOps tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a AlertOps](#configure-alertops)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre AlertOps tesztfelhasználót](#create-alertops-test-user)**  van egy megfelelője a Britta Simon AlertOps, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **AlertOps** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    1. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.alertops.com`

    1. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [AlertOps ügyfél-támogatási csapatának](mailto:support@alertops.com) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **AlertOps beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>AlertOps konfigurálása

1. Automatizálhatja a AlertOps konfigurációra, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő AlertOps** fog irányítja át a AlertOps alkalmazás. Itt adja meg a rendszergazdai hitelesítő adataival bejelentkezni AlertOps. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3 – 5. lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Ha azt szeretné, AlertOps manuálisan állíthatja be az, nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati AlertOps webhelyét, és hajtsa végre az alábbi lépéseket:

4. Kattintson a **Fiókbeállítások** a bal oldali navigációs panelen.

    ![AlertOps konfiguráció](./media/alertops-tutorial/configure1.png)

5. Az a **előfizetési beállítások** lapon jelölje be **SSO** , és hajtsa végre az alábbi lépéseket:

    ![AlertOps konfiguráció](./media/alertops-tutorial/configure2.png)

    a. Válassza ki **használja egyetlen Sign-On(SSO)** jelölőnégyzetet.

    b. Válassza ki **Azure Active Directory** , egy **SSO szolgáltató** a legördülő listából.

    c. Az a **kiállítójának URL-címe** szövegmezőbe, használja az azonosító értékét, amely már használtak a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    d. Az a **SAML-végpont URL-címe** szövegmezőjébe illessze be a **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta.

    e. Az a **SLO-végpont URL-címe** szövegmezőjébe illessze be a **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta.

    f. Válassza ki **SHA256** , egy **SAML aláírási algoritmus** a legördülő listából.

    g. Nyissa meg a letöltött Certificate(Base64) fájlt a Jegyzettömbben. Másolja a vágólapra a tartalmát, és beillesztheti az X.509-tanúsítvány szövegmezőben.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon által biztosított hozzáférés AlertOps Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **AlertOps**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-alertops-test-user"></a>AlertOps tesztfelhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be a AlertOps vállalati hely rendszergazdaként.

2. Kattintson a **felhasználók** a bal oldali navigációs panelen.

    ![AlertOps konfiguráció](./media/alertops-tutorial/user1.png)

3. Válassza ki **felhasználó hozzáadása**.

    ![AlertOps konfiguráció](./media/alertops-tutorial/user2.png)

4. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![AlertOps konfiguráció](./media/alertops-tutorial/user3.png)

    a. Az a **bejelentkezési név** szövegmezőbe írja be a felhasználónevet, a felhasználó például **Brittasimon**.

    b. Az a **hivatalos E-mail** szövegmezőbe írja be például a felhasználó e-mail-címe **Brittasimon\@contoso.com**.

    c. Az a **Utónév** szövegmezőbe írja be például a felhasználó utónevét **Britta**.

    d. Az a **Vezetéknév** szövegmezőbe írja be például a felhasználó utónevét **Simon**.

    e. Válassza ki a **típus** érték beállításával a szervezet a legördülő listából.

    f. Válassza ki a **szerepkör** a felhasználó a szervezet megfelelően a legördülő listából.

    g. Válassza a **Hozzáadás** lehetőséget.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A AlertOps csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a AlertOps, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
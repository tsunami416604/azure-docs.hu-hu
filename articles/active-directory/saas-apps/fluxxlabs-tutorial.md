---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az Fluxx Labs |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Fluxx Labs között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c48e41318ff5ba189e4cc8b8529bb3b81911052
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204389"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Oktatóanyag: Az Azure Active Directory-integráció az Fluxx Labs

Ebben az oktatóanyagban elsajátíthatja, hogyan Fluxx laborok integrálása az Azure Active Directory (Azure AD).

Fluxx laborok integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Fluxx Labs Azure AD-ben.
- A felhasználók automatikusan első bejelentkezett Fluxx Labs (egyszeri bejelentkezés), engedélyezheti az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Fluxx Labs, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Fluxx Labs egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Fluxx Labs hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-fluxx-labs-from-the-gallery"></a>Fluxx Labs hozzáadása a katalógusból
Fluxx laborok integrálása az Azure AD beállítása, hozzá kell Fluxx Labs a galériából a felügyelt SaaS-alkalmazások listájára.

**Fluxx Labs hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Fluxx Labs**válassza **Fluxx Labs** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Fluxx Labs](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Fluxx Laborgyakorlatok egy úgynevezett "Britta Simon" tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Fluxx Fejlesztőlaborokban lévő mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Fluxx Fejlesztőlaborokban lévő hivatkozás kapcsolata kell létrehozni.

Fluxx Labs, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Fluxx Labs tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Fluxx Labs tesztfelhasználót](#create-a-fluxx-labs-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Fluxx Labs-környezetben, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Fluxx Labs alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Fluxx tanulhat, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Fluxx Labs** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

1. Az a **Fluxx Labs tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Fluxx Labs tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:

    | Környezet | Az URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:

    | Környezet | Az URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [Fluxx Labs támogatási csoportjának](mailto:travis@fluxxlabs.com) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/fluxxlabs-tutorial/tutorial_general_400.png)

1. Az a **Fluxx Labs konfigurációs** területén kattintson **konfigurálása Fluxx Labs** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a Fluxx Labs vállalati hely rendszergazdaként.

1. Válassza ki **rendszergazdai** alább a **beállítások** szakaszban.

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config1.png)

1. A rendszergazda panelen válassza ki a **modulok** > **Integrációk** majd **SAML SSO-(Disabled)**

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config2.png)

1. Az attribútum a szakaszban a következő lépésekkel:

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config3.png)

    a. Válassza ki a **SAML SSO** jelölőnégyzetet.

    b. Az a **kérelem elérési útját** szövegmezőbe írja be **saml/auth/**.

    c. Az a **visszahívási elérési** szövegmezőbe írja be **/auth/saml/callback**.

    d. Az a **helyességi feltétel fogyasztói szolgáltatás Url(Single Sign-On URL)** szövegmezőbe írja be a **válasz URL-cím** érték, amely az Azure Portalon megadott.

    e. Az a **célközönség (SP entitás azonosítója)** szövegmezőbe írja be a **azonosító** érték, amely az Azure Portalon megadott.

    f. Az a **Identity Provider SSO cél URL-cím** szövegmezőjébe illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról másolta.

    g. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **szolgáltató Identitástanúsítványt** szövegmezőbe.

    h. A **névazonosító formátuma** szövegmezőbe írja be az értéket `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Miután menti a tartalmat, a mező üres, a biztonság megjelenik, de az érték a konfiguráció mentése.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/fluxxlabs-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/fluxxlabs-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/fluxxlabs-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/fluxxlabs-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-fluxx-labs-test-user"></a>Fluxx Labs tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Fluxx Labs jelentkezzen be, akkor ki kell építeni Fluxx Labs be. Fluxx Labs, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Fluxx Labs vállalati webhely.

1. Kattintson a az alábbiakban látható **ikon**.

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config6.png)

1. Az irányítópulton kattintson a az alább megjelenő ikonra kattintva nyissa meg a **új személyek** kártya.

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config4.png)

1. Az a **új személyek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Fluxx Labs Configuration](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs egyedi azonosítóként e-mailek használata Egyszeri bejelentkezéshez. Töltse fel a **SSO UID** mezőt a felhasználó e-mail címét, az e-mail-címét, amelyek használják az Egyszeri bejelentkezés megfelelő a.

    b. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Fluxx Labs Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel Fluxx Labs, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Fluxx Labs**.

    ![Az alkalmazások listáját a Fluxx Labs hivatkozás](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Fluxx Labs csempére kattint, meg kell lekérése automatikusan bejelentkezett az Fluxx Labs alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/fluxxlabs-tutorial/tutorial_general_203.png

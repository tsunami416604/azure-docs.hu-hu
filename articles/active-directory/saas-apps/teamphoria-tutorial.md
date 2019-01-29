---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Teamphoria |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Teamphoria között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: d7a0523a1e436a086be90896da4d16fab55ce9ab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174448"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Teamphoria

Ebben az oktatóanyagban elsajátíthatja, hogyan Teamphoria integrálása az Azure Active Directory (Azure AD).

Teamphoria integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Teamphoria Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Teamphoria (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Teamphoria az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Teamphoria egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Teamphoria hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-teamphoria-from-the-gallery"></a>Teamphoria hozzáadása a katalógusból
Az Azure AD integrálása a Teamphoria konfigurálásához hozzá kell Teamphoria a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Teamphoria hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure Portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Teamphoria**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

1. Az eredmények panelen válassza ki a **Teamphoria**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Teamphoria a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Teamphoria mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Teamphoria hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Teamphoria tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Teamphoria tesztfelhasználó létrehozása](#creating-a-teamphoria-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure ad-ben ábrázolása őt Teamphoria.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Teamphoria alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Teamphoria, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Teamphoria** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

1. Az a **Teamphoria tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím a következő minta használatával: `https://<sub-domain>.teamphoria.com/login`   

    > [!NOTE] 
    > A bejelentkezési URL-érték nem valódi. Frissíteni ezt az értéket a tényleges bejelentkezési URL-címmel rendelkezik. Kapcsolattartó [Teamphoria ügyfél-támogatási csapatának](https://www.teamphoria.com/) a bejelentkezési URL-Címének lekéréséhez.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , majd mentse a tanúsítványt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_general_400.png)

1. Az a **Teamphoria konfigurációs** területén kattintson **konfigurálása Teamphoria** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

1. Az egyszeri bejelentkezés konfigurálása **Teamphoria** oldalán, jelentkezzen be rendszergazdaként Teamphoria alkalmazását.

1. Lépjen a **ADMINISZTRÁTORI beállítások** lehetőséget a bal oldali eszköztáron és a konfigurálása lapon kattintson a **EGYSZERI bejelentkezés** az egyszeri bejelentkezés konfigurálása ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Kattintson a **hozzáadása új IDENTITÁSSZOLGÁLTATÓ** lehetőséget a jobb felső sarokban az egyszeri bejelentkezéshez a beállítások hozzáadásához a képernyő megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Adja meg adatait a mezőket, amint az alábbi-

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **MEGJELENÍTETT NÉV**: A rendszergazda oldalon adja meg a beépülő modul a megjelenítendő nevét.

    b. **GOMB NEVE**: Az egyszeri bejelentkezés használatával jelentkezik be a bejelentkezési oldalon jelenik meg a lap neve.

    c. **TANÚSÍTVÁNY**: Nyissa meg a Jegyzettömbben az Azure Portalról korábban letöltött tanúsítványt, másolja a tartalmat az azonos, és illessze be ide a mezőbe.

    d. **BELÉPÉSI PONT**: Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról korábban vágólapra másolt.

    e. Váltás lehetőségét **ON** , majd kattintson a **mentése**.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamphoria-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamphoria-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamphoria-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-teamphoria-test-user"></a>Teamphoria tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Teamphoria szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Teamphoria be. Teamphoria, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Teamphoria vállalati webhely.

1. Kattintson a **rendszergazdai** a bal oldali eszköztáron, majd a beállítások a **kezelés** lapon kattintson a **felhasználók** nyissa meg a rendszergazda felhasználók számára.

    ![Alkalmazott hozzáadása](./media/teamphoria-tutorial/admin_manage_users.png)

1. Kattintson a **manuális MEGHÍVÁSA** lehetőséget.

    ![Személyek meghívása](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Ezen a lapon a következő művelet végrehajtása.
    
    ![Személyek meghívása](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Az a **E-mail cím** szövegmezőbe a **e-mail-cím** BrittaSimon az.

    b. Az a **UTÓNÉV** szövegmezőbe írja be **Britta**.

    c. Az a **Vezetéknév** szövegmezőbe írja be **Simon**.

    d. Kattintson a **MEGHÍVÓ 1 felhasználó**. Fogadja el a meghívást, a rendszer létrehozza a felhasználónak kell.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Teamphoria Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200]

**Britta Simon rendel Teamphoria, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

1. Az alkalmazások listájában jelölje ki a **Teamphoria**.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha szeretné tesztelni az egyszeri bejelentkezés beállításai, nyissa meg a hozzáférési panelen. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png

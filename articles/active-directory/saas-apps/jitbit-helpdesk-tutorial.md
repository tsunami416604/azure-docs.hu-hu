---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Jitbit segélyszolgálat |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Jitbit segélyszolgálat között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 71b7cfb92b4caded75e0723564a09fae9f10858c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359773"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Oktatóanyag: Jitbit ügyfélszolgálat az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Jitbit segélyszolgálat integrálása az Azure Active Directory (Azure AD).
Jitbit segélyszolgálat integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Az Azure ad-ben Jitbit segélyszolgálati hozzáféréssel rendelkező szabályozhatja.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve Jitbit segélyszolgálat (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Jitbit segélyszolgálat konfigurálni az Azure AD-integráció, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Jitbit segélyszolgálat egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a segélyszolgálat Jitbit **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Jitbit segélyszolgálat hozzáadása a katalógusból

Az Azure AD integrálása a segélyszolgálat Jitbit konfigurálásához hozzá kell Jitbit segélyszolgálat a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Jitbit segélyszolgálat hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Jitbit segélyszolgálat**, jelölje be **Jitbit segélyszolgálat** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Jitbit segélyszolgálat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálhatja, és tesztelés az Azure AD egyszeri bejelentkezés Jitbit segélyszolgálat alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználót és a kapcsolódó felhasználó Jitbit ügyfélszolgálati hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés az Jitbit segélyszolgálat tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Segélyszolgálat Jitbit egyszeri bejelentkezés konfigurálása](#configure-jitbit-helpdesk-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Jitbit segélyszolgálat tesztfelhasználót](#create-jitbit-helpdesk-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik a felhasználó Azure ad-ben reprezentációja segélyszolgálat Jitbit.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Jitbit segélyszolgálat, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Jitbit segélyszolgálat** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Jitbit segélyszolgálat tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Jitbit segélyszolgálat ügyfél-támogatási csapatának](https://www.jitbit.com/support/) lekérni ezt az értéket.

    b. Az a **azonosító (entityid)** szövegmezőbe írja be egy URL-cím a következőképpen: `https://www.jitbit.com/web-helpdesk/`

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Jitbit segélyszolgálat beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Jitbit segélyszolgálat egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a segélyszolgálat Jitbit vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson **felügyeleti**.

    ![Felügyeleti](./media/jitbit-helpdesk-tutorial/ic777681.png "felügyelete")

1. Kattintson a **általános beállítások**.

    ![Felhasználók, a vállalatok és az engedélyek](./media/jitbit-helpdesk-tutorial/ic777680.png "felhasználókat, a vállalatok és engedélyek")

1. Az a **hitelesítési beállítások** konfigurációs szakaszban, hajtsa végre az alábbi lépéseket:

    ![Hitelesítési beállítások](./media/jitbit-helpdesk-tutorial/ic777683.png "hitelesítési beállítások")

    a. Válassza ki **engedélyezése SAML 2.0-s egyszeri bejelentkezés**, jelentkezzen be az egyszeri bejelentkezés (SSO), **OneLogin**.

    b. Az a **végponti URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    c. Nyissa meg a **base-64** kódolású Jegyzettömbben-tanúsítványt, a tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** szövegmező

    d. Kattintson a **módosítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Jitbit segélyszolgálat Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Jitbit segélyszolgálat**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Jitbit segélyszolgálat**.

    ![Az alkalmazások listáját a Jitbit ügyfélszolgálati hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-jitbit-helpdesk-test-user"></a>Jitbit segélyszolgálat tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a segélyszolgálat Jitbit, akkor ki kell építeni Jitbit segélyszolgálat be. Jitbit segélyszolgálat, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Jitbit segélyszolgálat** bérlő.

1. A felső menüben kattintson **felügyeleti**.

    ![Felügyeleti](./media/jitbit-helpdesk-tutorial/ic777681.png "felügyelete")

1. Kattintson a **felhasználók, a vállalatok és az engedélyek**.

    ![Felhasználók, a vállalatok és az engedélyek](./media/jitbit-helpdesk-tutorial/ic777682.png "felhasználókat, a vállalatok és engedélyek")

1. Kattintson a **Felhasználó hozzáadása** parancsra.

    ![Felhasználó hozzáadása](./media/jitbit-helpdesk-tutorial/ic777685.png "felhasználó hozzáadása")

1. A létrehozás területen írja be az adatokat az Azure AD-fiók kíván létrehozni a következő:

    ![Hozzon létre](./media/jitbit-helpdesk-tutorial/ic777686.png "létrehozása")

   a. Az a **felhasználónév** szövegmezőbe írja be a felhasználónevet a felhasználó például **BrittaSimon**.

   b. Az a **E-mail** szövegmezőbe írja be e-mail a felhasználó például **BrittaSimon@contoso.com**.

   c. Az a **Utónév** szövegmező, mint például a felhasználó utóneve típus **Britta**.

   d. Az a **Vezetéknév** szövegmezőbe írja be például a felhasználó vezetékneve **Simon**.

   e. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
> Jitbit segélyszolgálat felhasználói fiók létrehozása eszközöket és Jitbit segélyszolgálat által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a segélyszolgálat Jitbit csempére kattint, akkor kell automatikusan megtörténik a a Jitbit segélyszolgálathoz, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

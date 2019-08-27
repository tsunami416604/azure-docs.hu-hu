---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Workspot vezérlővel |} A Microsoft Docs'
description: Ismerje meg az egyszeri bejelentkezés az Azure Active Directory és Workspot vezérlő konfigurálása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086681"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Oktatóanyag: Az Azure Active Directory-integráció Workspot vezérlővel

Ebben az oktatóanyagban elsajátíthatja, hogyan Workspot vezérlés integrálása az Azure Active Directory (Azure AD). Workspot vezérlés integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozhatja, ki férhet hozzá Workspot vezérlés az Azure AD használata.
* Engedélyezze a felhasználók automatikusan jelentkezhetnek be Workspot vezérlő (egyszeri bejelentkezés [egyszeri bejelentkezés]) az Azure AD-fiókjaik használatával.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [egyszeri bejelentkezés az Azure AD-alkalmazások](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Workspot vezérlővel, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/free/).

* Egy Workspot vezérlőelem egy előfizetés sign-a-kompatibilis.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

> [!Note]
> Workspot vezérlő az SP által kezdeményezett, és az Identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést támogatja.


## <a name="adding-workspot-control-from-the-gallery"></a>Workspot vezérlőelem hozzáadása a katalógusból

Az Azure AD integrálása a Workspot vezérlő konfigurálásához hozzá kell adnia Workspot vezérlőelem a katalógusból a felügyelt SaaS-alkalmazások listájában.

**Workspot vezérlőelem felvétele a katalógusból, kövesse az alábbi lépéseket:**

1. A bal oldali panelen, a [az Azure portal](https://portal.azure.com)válassza **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** válassza **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Válassza ki **új alkalmazás** az ablak tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Workspot vezérlő**, jelölje be **Workspot vezérlő** az eredmények panelen, és válassza ki a **Hozzáadás**.

     !["Hozzáadása a katalógusból" ablak](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés Workspot vezérlővel megadása a tesztfelhasználónál Britta Simon.
Az egyszeri bejelentkezés működjön a Workspot vezérlőelem kell létesítenie az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat.

Az Azure AD egyszeri bejelentkezés Workspot vezérlővel tesztelése és konfigurálása, hogy a következő feladatokat kell elvégeznie:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók a funkció használatához.
2. [Workspot vezérlő egyszeri bejelentkezés konfigurálása](#configure-workspot-control-single-sign-on) az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
3. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés a Britta Simon teszteléséhez.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Hozzon létre egy Workspot vezérlő tesztfelhasználót](#create-a-workspot-control-test-user) létesíteni egy megfelelője a Britta Simon Workspot vezérlőben, amely a felhasználó Azure ad-ben reprezentációja van csatolva.
6. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Workspot vezérlővel, kövesse az alábbi lépéseket:

1. Az a **Workspot vezérlő** alkalmazás integráció lapján a [az Azure portal](https://portal.azure.com/), jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** ablakban válassza ki **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Válassza ki az egyszeri bejelentkezés válassza módszer ablak](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** () ceruzaikonra kattintva eléréséhez **alapszintű SAML-konfigurációja**.

    ![Az "Alapszintű SAML-konfiguráció" kiemelt ikonjának szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha szeretné konfigurálni az alkalmazás Identitásszolgáltató által kezdeményezett módban, kövesse az alábbi lépéseket:

    ![Workspot vezérlő tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    1. Az a **azonosító** szöveget adja meg egy URL-címet a következő mintában:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. Az a **válasz URL-cím** szöveget adja meg egy URL-címet a következő mintában:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Ha az alkalmazás konfigurálása a Szolgáltató által kezdeményezett módban szeretne, válassza ki a **további URL-címet beállítani**.

    ![Workspot vezérlő tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szöveget adja meg egy URL-címet a következő mintában:<br/>
    ***https://<<i></i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezeket az értékeket cserélje le tényleges azonosítóját, válasz URL-cím és a sign-on URL-CÍMÉT. Forduljon a [Workspot vezérlő ügyfél-támogatási csapatának](mailto:support@workspot.com) beolvasni ezeket az értékeket. Emellett olvassa el a mintákat, vagy a **alapszintű SAML-konfigurációja** szakaszában az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez **tanúsítvány (Base64)** igény szerint az elérhető lehetőségek közül. Mentse a számítógépre.

    ![A tanúsítvány (Base64) letöltési hivatkozás](common/certificatebase64.png)

7. Az a **Workspot felügyeletét** területén másolja ki az igényeinek megfelelő URL-címek:

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    - **Bejelentkezési URL-címe**

    - **Azure AD Identifier**

    - **Kijelentkezési URL-címe**

### <a name="configure-workspot-control-single-sign-on"></a>Workspot vezérlő egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be Workspot ellenőrző a biztonsági-rendszergazdájaként.

2. A lap tetején az eszköztáron válassza **telepítő** , majd **SAML**.

    ![Beállítási lehetőségek](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Az a **Security Assertion Markup nyelvi beállításait** ablakban hajtsa végre az alábbi lépéseket:
 
    ![Security Assertion Markup nyelvi beállításait ablak](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Az a **Entitásazonosító** mezőbe illessze be a **Azure Ad-azonosító** , az Azure Portalról másolt.

    1. Az a **URL-CÍMÉT nehézség** mezőbe illessze be a **bejelentkezési URL-cím** , az Azure Portalról másolt.

    1. Az a **kijelentkezési szolgáltatás URL-címe** mezőbe illessze be a **kijelentkezési URL-címe** , az Azure Portalról másolt.

    1. Válassza ki **frissítésfájl** az X.509-tanúsítvány feltöltése a base-64 kódolású tanúsítványt, amelyet az Azure Portalról letöltött.

    1. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban hozzon létre egy tesztfelhasználót az Azure Portalon.

1. Az Azure Portal bal oldali panelen válassza ki a **Azure Active Directory**, **felhasználók**, majd **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** az ablak tetején.

    ![Az "új felhasználó" gomb](common/new-user.png)

3. A felhasználó tulajdonságai kövesse az alábbi lépéseket:

    ![A felhasználói tulajdonságok ablak](common/user-properties.png)

    1. Az a **neve** írja be a következőt **BrittaSimon**.
  
    1. Az a **felhasználónév** írja be a következőt **brittasimon@* yourcompanydomain.extension***. Adja meg például  **BrittaSimon@contoso.<i> </i>com**.

    1. Válassza ki a **Show jelszó** jelölőnégyzetet. Jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést adhat a Britta Simon Workspot vezérlő számára az Azure egyszeri bejelentkezéshez használandó engedélyezéséhez.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, **minden alkalmazás**, majd **Workspot vezérlő**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájából válassza **Workspot vezérlő**.

    ![Az alkalmazások listáját a Workspot vezérlő hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Válassza ki a **felhasználó hozzáadása** gombra. Válassza ki **felhasználók és csoportok** a a **-hozzárendelés hozzáadása** ablak.

    ![A "Hozzárendelés hozzáadása" ablak](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** ablakban válassza **Britta Simon** származó a **felhasználók** listája. Ezután kattintson a **Kiválasztás** elemre.

6. Ha a SAML-előfeltétel szerepkör értéket várt, válassza ki a megfelelő szerepkört a felhasználó a listájából a **Szerepkörválasztás** ablak. Kattintson a **kiválasztása** alján.

7. Az a **hozzárendelés hozzáadása** ablakban válassza **hozzárendelése**.

### <a name="create-a-workspot-control-test-user"></a>Workspot vezérlő tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a Workspot vezérlés az Azure AD-felhasználók, akkor ki kell építeni Workspot vezérlőelemben. Manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, kövesse az alábbi lépéseket:**

1. Jelentkezzen be a Workspot vezérlőelem egy biztonsági-rendszergazdaként.

2. A lap tetején az eszköztáron válassza **felhasználók** , majd **felhasználó hozzáadása**.

    !["Felhasználók" beállításai](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Az a **új felhasználó hozzáadása** ablakban hajtsa végre az alábbi lépéseket:

    !["Az új felhasználó hozzáadása" ablak](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. A **Utónév** mezőbe írja be például a felhasználó utónevét **Britta**.

    1. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **Simon**.

    1. A **E-mail** mezőbe írja be például a felhasználó e-mail-címe  **Brittasimon@contoso.<i> </i>com**.

    1. Válassza ki a megfelelő felhasználói szerepkört a **szerepkör** legördülő listából.

    1. Válassza ki a megfelelő felhasználói csoportot a **csoport** legördülő listából.

    1. Válassza ki **felhasználó hozzáadása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításainak keresztül teszteljük *hozzáférési Panel*.

Amikor rákattint a **Workspot vezérlő** csempére a hozzáférési panelen, meg kell hogy automatikusan jelentkezzenek be a Workspot vezérlőelemet, amelynek beállítása egyszeri bejelentkezés. További információkért lásd: [Bevezetés a Hozzáférési panel használatába](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező InsideView |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja fog konfigurálása egyszeri bejelentkezéshez InsideView és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 2149b8410104b39652b176895a31b42e094265f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100091"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező InsideView

Ebben az oktatóanyagban elsajátíthatja a InsideView integrálása az Azure Active Directory (Azure AD) lesz.
Ez az integráció ezeket az előnyöket biztosítja:

* Az Azure AD, hogy ki férhet hozzá InsideView is használhatja.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be InsideView (egyszeri bejelentkezés), az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása InsideView, szüksége lesz:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egyszeri bejelentkezés engedélyezve tartalmazó InsideView előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban fogja konfigurálni, és egy tesztelési környezetben az Azure AD egyszeri bejelentkezés tesztelése.

* InsideView identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-insideview-from-the-gallery"></a>InsideView hozzáadása a katalógusból

InsideView integrálása az Azure AD beállításához, hozzá kell InsideView a galériából a felügyelt SaaS-alkalmazások listájára.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**:

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

    ![Válassza ki az új alkalmazás](common/add-new-app.png)

4. A Keresés mezőbe írja be a **InsideView**. Válassza ki **InsideView** a keresési eredmények, és válassza ki a **Hozzáadás**.

    ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban fog konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés az InsideView Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez szüksége InsideView az Azure AD-felhasználót és a megfelelő felhasználó közötti kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezés az InsideView tesztelése és konfigurálása, szüksége a lépések elvégzéséhez:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  a felhasználók számára a funkció engedélyezéséhez.
2. **[InsideView egyszeri bejelentkezés konfigurálása](#configure-insideview-single-sign-on)**  az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  ahhoz, hogy az Azure AD egyszeri bejelentkezés a felhasználó számára.
5. **[Hozzon létre egy InsideView tesztfelhasználót](#create-an-insideview-test-user)**  , amely kapcsolódik a felhasználó Azure ad-ben ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés InsideView, ezeket a lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az alkalmazás integrációs InsideView oldalán válassza **egyszeri bejelentkezési**:

    ![Válassza ki az egyszeri bejelentkezés](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése:

    ![Egyszeri bejelentkezés módszer kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanel mezőbe az alábbi lépéseket.

    ![Alapszintű SAML-konfigurációja párbeszédpanel](common/idp-reply.png)

    Az a **válasz URL-cím** mezőbe írjon be egy URL-címe ebben a mintában:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Ez az érték pedig. Szeretné használni a tényleges válasz URL-cím. Forduljon a [InsideView támogatási csapatának](mailto:support@insideview.com) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** párbeszédpanel az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **letöltése** mellett kapcsolni **(Raw)tanúsítvány**, a igényeknek, és mentse el a tanúsítványt a számítógépen:

    ![Tanúsítvány letöltésére szolgáló hivatkozásra.](common/certificateraw.png)

6. Az a **InsideView beállítása** területén másolja a megfelelő URL-címeket, a követelmények alapján:

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Az Azure AD-azonosító**.

    1. **Kijelentkezési URL-címe**.

### <a name="configure-insideview-single-sign-on"></a>InsideView egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be a InsideView vállalati hely rendszergazdaként.

1. Az ablak tetején válassza **rendszergazdai**, **egyszeri bejelentkezés beállításainak**, majd **hozzáadása SAML**.
   
   ![SAML egyszeri bejelentkezési beállításainak](./media/insideview-tutorial/ic794135.png "SAML egyszeri bejelentkezés beállításai")

1. Az a **adjon hozzá egy új SAML** területén az alábbi lépéseket.

    ![Adjon hozzá egy új SAML szakasz](./media/insideview-tutorial/ic794136.png "új SAML szakasz hozzáadása")

    1. Az a **STS neve** mezőben adjon meg egy nevet a konfigurációnak.

    1. Az a **SamlP/WS-Fed kéretlen végpont** mezőbe illessze be a **bejelentkezési URL-cím** , az Azure Portalról másolt érték.

    1. Nyissa meg az Azure Portalról letöltött nyers tanúsítványt. A tanúsítvány tartalmának másolása a vágólapra, és illessze be a tartalmát a **STS tanúsítvány** mezőbe.

    1. Az a **Crm azonosító Felhasználóleképezés** mezőbe írja be **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. Az a **Crm E-mail leképezési** mezőbe írja be **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. Az a **Crm Utónév leképezési** mezőbe írja be **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** .

    1. Az a **Crm lastName leképezési** mezőbe írja be **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** .  

    1. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portalon válassza ki a **Azure Active Directory** a bal oldali panelen válassza ki a **felhasználók**, majd válassza ki **minden felhasználó**:

    ![Válassza ki az összes felhasználó](common/users.png)

2. Válassza ki **új felhasználó** az ablak tetején:

    ![Válassza ki az új felhasználó](common/new-user.png)

3. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket.

    ![Felhasználói párbeszédpanel](common/user-properties.png)

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **BrittaSimon @\<vállalati_tartomány >.\< bővítmény >** . (Például BrittaSimon@contoso.com.)

    1. Válassza ki **jelszó megjelenítése**, és jegyezze fel az értéket, amely szerepel a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon számára a hozzáférés biztosításával a InsideView Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **InsideView**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **InsideView**.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában, és kattintson a **válassza** gombra az ablak alján.

6. Ha a SAML-előfeltétel szerepkör értéket a várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra az ablak alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-an-insideview-test-user"></a>Hozzon létre egy InsideView tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók InsideView bejelentkezni, azokat hozzá InsideView kell. Adja hozzá manuálisan kell.

Felhasználók vagy kapcsolattartók létrehozása InsideView, lépjen kapcsolatba a [InsideView támogatási csapatának](mailto:support@insideview.com).

> [!NOTE]
> Bármely felhasználói fiók létrehozása az eszközzel, vagy az Azure AD-felhasználói fiókok kiépítése InsideView által biztosított API-t.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most szüksége az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési Panel használatával.

A InsideView csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a a InsideView példányhoz, amelyhez beállítani az egyszeri bejelentkezés Konfigurálásához. A hozzáférési panelen kapcsolatos további információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

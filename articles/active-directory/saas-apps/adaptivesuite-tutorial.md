---
title: 'Oktatóanyag: Az adaptív Insights az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az adaptív Insights között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2c7ca8699274b96f7f382dfe1958bf5babbbe99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720170"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Oktatóanyag: Az adaptív Insights az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan adaptív Insights integrálása az Azure Active Directory (Azure AD).
Az adaptív Insights integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az adaptív Insights az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve adaptív Insights (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az adaptív Insights, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Az adaptív Insights egy bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Az adaptív Insights támogatja **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-adaptive-insights-from-the-gallery"></a>Az adaptív Insights hozzáadása a katalógusból

Az adaptív elemzéseket kaphat az Azure AD-integráció konfigurálása, meg kell adaptív Insights hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Az adaptív Insights hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **adaptív Insights**válassza **adaptív Insights** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában adaptív Insights](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapuló adaptív Insights **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó, az adaptív Insights hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az adaptív Insights tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az adaptív Insights egyszeri bejelentkezés konfigurálása](#configure-adaptive-insights-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Az adaptív Insights tesztfelhasználó létrehozása](#create-adaptive-insights-test-user)**  – egy megfelelője a Britta Simon van adaptív Insights, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása az adaptív Insights, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **adaptív Insights** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon, a következő lépésekkel:

    ![Az adaptív Insights tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Az adaptív Insights kaphat azonosító (entityid) és a válasz URL-cím értékek **SAML egyszeri bejelentkezési beállításainak** lapot.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **adaptív Insights beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-adaptive-insights-single-sign-on"></a>Az adaptív Insights egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az adaptív Insights vállalati hely rendszergazdaként.

2. Lépjen a **rendszergazdai**.

    ![Rendszergazdai](./media/adaptivesuite-tutorial/ic805644.png "rendszergazda")

3. Az a **felhasználók és szerepkörök** területén kattintson **SAML egyszeri bejelentkezési beállításainak kezelése**.

    ![SAML egyszeri bejelentkezési beállításainak kezelése](./media/adaptivesuite-tutorial/ic805645.png "SAML egyszeri bejelentkezési beállításainak kezelése")

4. Az a **SAML egyszeri bejelentkezési beállításainak** lapon, a következő lépésekkel:

    ![SAML egyszeri bejelentkezési beállításainak](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO-beállítások")

    a. Az a **identitásszolgáltató neve** szövegmezőbe írja be a konfiguráció nevét.

    b. Illessze be a **Azure Ad-azonosító** az Azure Portalról másolt érték a **identitásszolgáltató Entitásazonosító** szövegmezőbe.

    c. Illessze be a **bejelentkezési URL-cím** az Azure Portalról másolt érték a **identitásszolgáltató egyszeri bejelentkezési URL-cím** szövegmezőbe.

    d. Illessze be a **kijelentkezési URL-címe** az Azure Portalról másolt érték a **egyéni kijelentkezési URL-címe** szövegmezőbe.

    e. A letöltött tanúsítvány feltöltéséhez kattintson **fájl kiválasztása**.

    f. Válassza ki a következőket:

     * **SAML-felhasználói azonosító**válassza **felhasználó adaptív Insights nevét**.

     * **SAML-felhasználói azonosító hely**válassza **NameID-tulajdonos felhasználói azonosító**.

     * **Az SAML NameID formátum**válassza **E-mail-cím**.

     * **SAML engedélyezése**válassza **SAML SSO engedélyezése és a közvetlen adaptív Insights bejelentkezési**.

    g. Másolás **adaptív Insights egyszeri bejelentkezési URL-címe** , és illessze be a **azonosító (entityid)** és **válasz URL-cím** az eszköztippek a **adaptív Insights tartomány és URL-címek** szakaszban az Azure Portalon.

    h. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be "brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com.

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított adaptív Insights hozzáférés Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **adaptív Insights**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **adaptív Insights**.

    ![Az adaptív Insights hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-adaptive-insights-test-user"></a>Az adaptív Insights tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be az adaptív Insights az Azure AD-felhasználók, akkor ki kell építeni az adaptív elemzésekké. Adaptív Insights esetén kiépítése a manuális feladat.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **adaptív Insights** rendszergazdaként a vállalati webhely.

2. Lépjen a **rendszergazdai**.

   ![Rendszergazdai](./media/adaptivesuite-tutorial/IC805644.png "rendszergazda")

3. Az a **felhasználók és szerepkörök** területén kattintson **felhasználó hozzáadása**.

   ![Felhasználó hozzáadása](./media/adaptivesuite-tutorial/IC805648.png "felhasználó hozzáadása")

4. Az a **új felhasználó** szakaszban, hajtsa végre az alábbi lépéseket:

   ![Küldje el](./media/adaptivesuite-tutorial/IC805649.png "beküldése")

   a. Írja be a **neve**, **bejelentkezési**, **E-mail**, **jelszó** egy érvényes Azure Active Directory-felhasználó létrehozásához a kapcsolódó kívánt a szöveges mezőkben.

   b. Válassza ki a **szerepkör**.

   c. Kattintson a **Submit** (Küldés) gombra.

> [!NOTE]
> Bármely más adaptív Insights felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését adaptív Insights által biztosított API-k.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a adaptív Insights csempére kattint, meg kell automatikusan megtörténik a az adaptív információkká, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: dc1d7032f80babf8686397ae4dcf0043f456acbd
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565171"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Oktatóanyag: FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben és az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja FirmPlay – az Azure Active Directory (Azure AD) a személyzeti osztályon dolgozók Ügyfélképviseletben.
FirmPlay – az Azure AD-vel a személyzeti osztályon dolgozók Ügyfélképviseletben integrálása nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon, az Azure AD-integráció konfigurálása a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon egyszeri bejelentkezés engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Hozzáadás FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon a katalógusból

FirmPlay – az Azure ad-ben, a személyzeti osztályon dolgozók Ügyfélképviseletben integráció konfigurálásához hozzá kell FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben**, jelölje be **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben** eredmény panelen kattintson a **Hozzáadás** gombra kattintva Vegye fel az alkalmazást.

     ![FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés a munkahelyi Azure AD-felhasználót és a kapcsolódó felhasználó FirmPlay - hivatkozás kapcsolata a személyzeti osztályon dolgozók Ügyfélképviseletben kell létrehozni.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés FirmPlay – a személyzeti osztályon, az alkalmazott Ügyfélképviseletben kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[FirmPlay - alkalmazott Ügyfélképviseletben a felvételi egyszeri bejelentkezés konfigurálása](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre FirmPlay - alkalmazott Ügyfélképviseletben személyzeti osztályon tesztfelhasználó számára](#create-firmplay---employee-advocacy-for-recruiting-test-user)**  – Ha rendelkezik egy megfelelője a Britta Simon FirmPlay - alkalmazott Ügyfélképviseletben felvételi, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

FirmPlay - alkalmazott Ügyfélképviseletben a személyzeti osztályon, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![FirmPlay - alkalmazott Ügyfélképviseletben felvételi tartomány és URL-címeket egyetlen bejelentkezési információk](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [FirmPlay - felvételi ügyfél-támogatási csapatával a alkalmazott Ügyfélképviseletben](mailto:engineering@firmplay.com) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>FirmPlay - alkalmazott Ügyfélképviseletben felvételi egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben** oldalon kell küldenie a letöltött **tanúsítvány (Base64)** és a megfelelő másolt URL-címek az Azure Portalról [ FirmPlay - személyzeti osztályon támogatási csapat alkalmazott Ügyfélképviseletben](mailto:engineering@firmplay.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben**.

    ![A FirmPlay – az alkalmazások listáját a személyzeti osztályon kapcsolathoz alkalmazott Ügyfélképviseletben](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Hozzon létre FirmPlay - alkalmazott Ügyfélképviseletben személyzeti osztályon tesztfelhasználó számára

Ebben a szakaszban egy felhasználói Britta Simon nevű FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben hoz létre. Együttműködve [FirmPlay - személyzeti osztályon támogatási csapat alkalmazott Ügyfélképviseletben](mailto:engineering@firmplay.com) felhasználót is hozzáadhat a FirmPlay - alkalmazott Ügyfélképviseletben személyzeti osztályon platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

FirmPlay – a hozzáférési panelen, a személyzeti osztályon csempe alkalmazott Ügyfélképviseletben kattintva meg kell automatikusan megtörténik a FirmPlay – a személyzeti osztályon dolgozók Ügyfélképviseletben, amely egyszeri bejelentkezés beállítása számára. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


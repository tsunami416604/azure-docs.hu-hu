---
title: 'Oktatóanyag: Az mártás Labs - mobil- és webes tesztelése az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés mártás Labs - mobil- és webes tesztelése és Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 41b35324ccca8cf40edbc53ed25a2d8615a9294e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729346"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Oktatóanyag: Az Azure Active Directory-integráció az mártás Labs - mobil- és webalkalmazás tesztelése

Ebben az oktatóanyagban megismerheti, hogyan integrálható a mártás Labs - mobil- és webes tesztelése az Azure Active Directoryval (Azure AD).
Mártás Labs - mobil- és webes tesztelése az Azure AD integrálása nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá mártás Labs - mobil- és webes tesztelése az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve mártás Labs - mobil- és webes tesztelésével (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az mártás Labs - mobil- és webes tesztelése, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Mártás Labs - mobil- és webes tesztelése egy bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Mártás Labs – mobil és webes tesztelés támogatja **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés
* Mártás Labs – mobil és webes tesztelés támogatja **igény szerinti** felhasználók átadása

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Hozzáadás mártás Labs - mobil- és webes tesztelése a katalógusból

Konfigurálja az integrációt, mártás Labs -, mobil- és webes tesztelése az Azure AD-be való hozzá kell mártás Labs - mobil- és webes tesztet a galériából a felügyelt SaaS-alkalmazások listájában.

**Adja hozzá a mártás Labs - mobil- és webes tesztelése a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **mártás Labs - mobil- és webes tesztelés**, jelölje be **mártás Labs - mobil- és webes tesztelése** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Mártás Labs - mobil- és webes tesztelése a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az mártás Labs – mobil- és webes tesztelés alapján nevű tesztfelhasználó **Britta Simon**.
Egyszeri bejelentkezés munkahelyi, az Azure AD-felhasználót és a kapcsolódó felhasználó mártás Labs - hivatkozás kapcsolata a mobil- és webes tesztelés kell hozható létre.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az mártás Labs - mobil- és webes tesztelés, szüksége a következő építőelemeit végrehajtásához:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Mártás Labs - mobil- és webes tesztelés egyszeri bejelentkezés konfigurálása](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Mártás labort létrehozni,-mobil- és tesztelése a webes teszt felhasználói](#create-sauce-labs---mobile-and-web-testing-test-user)**  – egy megfelelője a Britta Simon mártás Labs - mobil- és webes tesztelése, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az mártás Labs - mobil- és webes tesztelés, Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **mártás Labs - mobil- és webes tesztelés** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a felhasználónak nincs bármely lépése végrehajtani, mert az alkalmazás már előre integrálva van az Azure-ral.

    ![Mártás Labs - mobil és webes tesztelés tartomány és URL-címeket egyetlen bejelentkezési adatait](common/preintegrated.png)

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **mártás laborok – mobil- és webes tesztelés** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Mártás Labs - mobilalkalmazás konfigurálása és a webalkalmazás-tesztelési egyszeri bejelentkezés

1. Egy másik böngészőablakban jelentkezzen be a mártás Labs - mobil- és webalkalmazások rendszergazdaként a vállalati webhely tesztelése.

2. Kattintson a a **felhasználói ikon** válassza **csapat felügyeleti** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Adja meg a **tartománynév** be a szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Kattintson a **konfigurálása** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Az a **konfigurálása egyszeri bejelentkezéshez** szakaszban, a következő lépésekkel.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Kattintson a **Tallózás** és az Azure AD-ből a letöltött metaadatfájl feltöltése.

    b. Válassza ki a **engedélyezték-KIÉPÍTÉS engedélyezése** jelölőnégyzetet.

    c. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés mártás Labs - mobil- és webes tesztelés Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **mártás Labs - mobil- és webes tesztelése**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **mártás Labs - mobil- és webes tesztelés**.

    ![Az alkalmazások listáját hivatkozásra a mártás Labs - mobil- és webalkalmazás tesztelése](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Mártás labort létrehozni,-mobil- és tesztelése a webes teszt felhasználói

Ebben a szakaszban egy Britta Simon nevű felhasználó mártás Labs - mobil- és webes tesztelés jön létre. Mártás Labs - mobil és webes tesztelés támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a mártás Labs - mobil- és webes tesztelést, a hitelesítés után egy új jön létre.

> [!Note]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [mártás Labs - mobil- és webes tesztelés támogatási csoportjának](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor rákattint a mártás Labs – mobil- és webes tesztelés csempére a hozzáférési panelen, meg kell automatikusan megtörténik a mártás Labs - mobil- és webes tesztelése, amelynek beállítása egyszeri Bejelentkezéssel való. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


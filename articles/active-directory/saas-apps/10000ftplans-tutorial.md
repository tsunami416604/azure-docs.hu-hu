---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a 10 000 szerint tervek |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a tervek szerint 10 000 között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b60c955e-8fa3-4872-a897-c4e81fd7beac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ef3c9d1fdbf030a820f2a3a1724bd18a4b0cedb
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501161"
---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Oktatóanyag: A 10 000 szerint tervek az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan 10 000 szerint tervek integrálása az Azure Active Directory (Azure AD).
10 000 szerint tervek integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a 10 000 szerint tervek az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett 10 000 szerint tervekhez (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a tervek szerint 10 000, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* 10 000 szerint tervek egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* 10 000 szerint tervek támogatási **SP** által kezdeményezett egyszeri bejelentkezés
* 10 000 szerint tervek támogatási **igény szerinti** felhasználók átadása

## <a name="adding-10000ft-plans-from-the-gallery"></a>10 000 szerint hozzáadása a katalógusból csomagok

Adja meg az Azure AD integrálása a tervek szerint 10 000, szüksége 10 000 szerint csomagok hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**10 000 szerint csomagok hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **10 000 szerint tervek**, jelölje be **10 000 szerint tervek** az eredmény panelen, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![10 000 szerint tervek a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az 10 000 szerint nevű tesztfelhasználó alapján tervek **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó 10 000 szerint a hivatkozás kapcsolata tervek kell hozható létre.

Az Azure AD egyszeri bejelentkezés a tervek szerint 10 000 tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a 10000 szerint csomagok egyszeri bejelentkezés](#configure-10000ft-plans-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre 10000 szerint tervek tesztfelhasználót](#create-10000ft-plans-test-user)**  – szeretné, hogy egy felhasználó Azure ad-ben reprezentációja kapcsolódó 10 000 tervek szerint a Britta Simon megfelelője.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához 10 000 szerint csomagokkal, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **10 000 szerint tervek** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![10 000 szerint csomagok tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be az URL-cím: `https://app.10000ft.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be az URL-cím: `https://app.10000ft.com/saml/metadata`

    > [!NOTE]
    > Az érték **azonosító** eltérő, ha rendelkezik egyéni tartománnyal. Kapcsolattartó [10 000 szerint csomagok ügyfél-támogatási csapatával](https://www.10000ft.com/plans/support) lekérni ezt az értéket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **(Raw)tanúsítvány** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificateraw.png)

6. Az a **10 000 szerint tervek beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-10000ft-plans-single-sign-on"></a>10000 szerint tervek egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **10 000 szerint tervek** oldalon kell küldenie a letöltött **tanúsítvány (Raw)** és az Azure Portalról másolt URL-címek megfelelő [10 000 szerint tervek támogatási csoport ](https://www.10000ft.com/plans/support). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával 10 000 szerint tervekhez Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **10 000 szerint tervek**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **10 000 szerint tervek**.

    ![Az alkalmazások listáját a 10 000 szerint tervek hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-10000ft-plans-test-user"></a>10000 szerint tervek tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó a tervek szerint 10 000 jön létre. 10 000 szerint csomagokat támogatja-igény a felhasználók átadása, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a tervek szerint 10 000, a hitelesítés után egy új jön létre.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [10 000 szerint csomagok ügyfél-támogatási csapatával](https://www.10000ft.com/plans/support).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen, a 10 000 szerint tervek csempére kattint, meg kell automatikusan megtörténik a 10 000 szerint tervekkel, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

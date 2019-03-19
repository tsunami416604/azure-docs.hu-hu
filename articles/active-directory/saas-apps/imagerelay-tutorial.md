---
title: 'Oktatóanyag: A kép Relay Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a lemezkép továbbítási között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63fbab5fffbcc30d0242d223fd8a6b5796c2aeae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901899"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Oktatóanyag: A kép Relay Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan kép Relay integrálása az Azure Active Directory (Azure AD).
Kép Relay integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a lemezkép Relay Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve kép Relay (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a lemezkép Relay, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Kép Relay egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Kép a Relay által támogatott **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-image-relay-from-the-gallery"></a>Kép Relay hozzáadása a katalógusból

Kép Relay integrálása az Azure AD beállítása, hozzá kell kép Relay a galériából a felügyelt SaaS-alkalmazások listájára.

**Kép Relay hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **kép Relay**, jelölje be **kép Relay** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Lemezkép-továbbító a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a lemezkép Relay nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználói lemezkép Relay a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a lemezkép Relay tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Kép Relay egyszeri bejelentkezés konfigurálása](#configure-image-relay-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre kép Relay tesztfelhasználót](#create-image-relay-test-user)**  – a felhasználó Azure ad-ben reprezentációja csatolt kép Relay-megfelelője a Britta Simon rendelkezik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a lemezkép Relay, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **kép Relay** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Kép Relay tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.imagerelay.com/`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [kép Relay ügyfél-támogatási csapatának](http://support.imagerelay.com/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **állítsa be a rendszerkép Relay** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-image-relay-single-sign-on"></a>Kép Relay egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a rendszerkép Relay vállalati hely rendszergazdaként.

2. A felső eszköztáron kattintson a **felhasználók és engedélyek** számítási feladatot.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Kattintson a **hozzon létre új engedély**.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. Az a **egyszeri bejelentkezést a beállítások** feladatnál válassza a **csak jelentkezzen be az egyszeri bejelentkezést keresztül lehet ennek a csoportnak a** jelölőnégyzetet, majd kattintson a **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Lépjen a **Fiókbeállítások**.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Nyissa meg a **egyszeri bejelentkezést a beállítások** számítási feladatot.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. Az a **SAML-beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. A **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím** Azure Portalról másolt.

    b. A **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe** Azure Portalról másolt.

    c. Mint **Névazonosító formátuma**válassza **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: emailAddress**.

    d. Mint **beállítások kötelező a szolgáltató (kép továbbítóként) érkező kéréseket**, jelölje be **POST kötés**.

    e. A **x.509 tanúsítvány**, kattintson a **tanúsítvány frissítése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **x.509 tanúsítvány** szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. A **engedélyezték Felhasználókiépítés** szakaszban jelölje be a **engedélyezték Felhasználókiépítés engedélyezése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Válassza ki az engedélycsoporthoz tartozik (például **SSO alapszintű**) ez engedélyezett, csak a keresztül egyszeri bejelentkezéshez.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon hozzáférésének engedélyezésére lemezkép Relay által használandó Azure egyszeri bejelentkezés engedélyezése.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **kép Relay**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **kép Relay**.

    ![A kép Relay hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-image-relay-test-user"></a>Kép Relay tesztfelhasználó létrehozása

Ez a szakasz célja egy kép Relay Britta Simon nevű felhasználó létrehozásához.

**Kép Relay Britta Simon nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a lemezkép Relay vállalati webhelyre rendszergazdaként.

2. Lépjen a **felhasználók és engedélyek** válassza **SSO-felhasználó létrehozása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Adja meg a **E-mail**, **Utónév**, **Vezetéknév**, és **vállalati** kiépítése, és válassza ki az engedélycsoporthoz tartozik (a felhasználó egyszeri bejelentkezés alapszintű példa) Ez a csoport, amely keresztül bejelentkezni képes csak egyszeri bejelentkezést.

    ![Egyszeri bejelentkezés konfigurálása](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Kattintson a **Create** (Létrehozás) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a lemezkép Relay csempére kattint, meg kell automatikusan megtörténik a lemezkép Relay, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az üzemeltetett grafit |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés grafit üzemeltetett és Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49e70eab0b9824742a67e4446c5ff6f0a9a366ec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894665"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Oktatóanyag: Az Azure Active Directory integrációja az üzemeltetett grafit

Ebben az oktatóanyagban elsajátíthatja, hogyan üzemeltetett grafit integrálása az Azure Active Directory (Azure AD).
Üzemeltetett grafit integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Az Azure ad-ben üzemeltetett grafit hozzáféréssel rendelkező szabályozhatja.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve üzemeltetett grafit (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az üzemeltetett grafit, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Üzemeltetett grafit egyszeri bejelentkezés engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Üzemeltetett grafit támogatja **SP és IDP** által kezdeményezett egyszeri bejelentkezés
* Üzemeltetett grafit támogatja **igény szerinti** felhasználók átadása

## <a name="adding-hosted-graphite-from-the-gallery"></a>Üzemeltetett grafit hozzáadása a katalógusból

Azure ad-ben üzemeltetett grafit integráció konfigurálásához, hozzá kell üzemeltetett grafit a galériából a felügyelt SaaS-alkalmazások listájára.

**Üzemeltetett grafit hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **üzemeltetett grafit**, jelölje be **üzemeltetett grafit** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában lévő üzemeltetett grafit](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az üzemeltetett grafit nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó lévő üzemeltetett grafit hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az üzemeltetett grafit tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Üzemeltetett grafit egyszeri bejelentkezés konfigurálása](#configure-hosted-graphite-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre üzemeltetett grafit tesztfelhasználót](#create-hosted-graphite-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon lévő üzemeltetett grafit, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés grafit üzemeltetett, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **üzemeltetett grafit** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Az üzemeltetett grafit tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.hostedgraphite.com/metadata/<user id>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Az üzemeltetett grafit tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Vegye figyelembe, hogy ezek nem állnak a valós értékeket. A tényleges azonosítója, válasz URL-cím és bejelentkezési URL-ezeket az értékeket frissítenie kell. Ezek az értékek lekéréséhez hozzáférés megnyithatja az alkalmazás oldalán, vagy forduljon a SAML beállítása -> [üzemeltetett grafit támogatási csapatával](mailto:help@hostedgraphite.com).

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **üzemeltetett grafit beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-hosted-graphite-single-sign-on"></a>Üzemeltetett grafit egyszeri bejelentkezés konfigurálása

1. Bejelentkezés az üzemeltetett grafit bérlői rendszergazdaként.

2. Nyissa meg a **SAML beállítása lapon** az oldalsávon (**hozzáférés SAML beállítása ->**).

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Erősítse meg az alábbi URL-címek a kész a konfigurációnak megfelelően a **alapszintű SAML-konfigurációja** szakaszában az Azure Portalon.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. A **entitást vagy a kiállító azonosító** és **Egyszeri bejelentkezési URL-cím** szövegmezőből, illessze be az értékét **Azure Ad-azonosító** és **bejelentkezési URL-cím** másolta, amely az Azure Portalról.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Válassza ki **csak olvasható** , **felhasználói szerepkör alapértelmezett**.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. A base-64 kódolású tanúsítvány megnyitása a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** szövegmezőbe.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Kattintson a **mentése** gombra.

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

Ebben a szakaszban engedélyezze Britta Simon által üzemeltetett grafit való hozzáférés biztosítása az Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **üzemeltetett grafit**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **üzemeltetett grafit**.

    ![Az alkalmazások listáját lévő üzemeltetett grafit hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-hosted-graphite-test-user"></a>Üzemeltetett grafit tesztfelhasználó létrehozása

Ebben a szakaszban lévő üzemeltetett grafit egy Britta Simon nevű felhasználó jön létre. Üzemeltetett grafit támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az üzemeltetett grafit, egy új jön létre a hitelesítés után.

> [!NOTE]
> Ha szeretné manuálisan hozzon létre egy felhasználót, szeretné-e a üzemeltetett grafit támogatási csapat <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a üzemeltetett grafit csempére kattint, meg kell lehet automatikusan bejelentkezett, amelynek beállítása egyszeri bejelentkezés üzemeltetett grafit. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


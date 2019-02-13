---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a Dropbox Business |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Dropbox Business és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 948121ad6b3a0f666fcc8fa64096bab91927b474
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186148"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Oktatóanyag: A Dropbox Business az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Dropbox Business az Azure Active Directory (Azure AD).
Dropbox Business integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Dropbox Business az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Dropbox Business (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Dropbox Business az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Az üzleti egyszeri bejelentkezés Dropbox előfizetés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Üzleti támogatja a Dropbox **SP** által kezdeményezett egyszeri bejelentkezés

* Üzleti támogatja a Dropbox **igény szerinti** felhasználók átadása

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dropbox Business hozzáadása a katalógusból

A Dropbox, az Azure AD-be a vállalati integráció konfigurálásához, hozzá kell Dropbox Business a galériából a felügyelt SaaS-alkalmazások listájára.

**Dropbox Business hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Dropbox Business**válassza **Dropbox Business** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Dropbox Business az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Dropbox Business nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Dropbox Business hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés Dropbox Business az tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az üzleti egyszeri bejelentkezés Dropbox konfigurálása](#configure-dropbox-for-business-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Dropbox Business tesztfelhasználó](#create-dropbox-for-business-test-user)**  – egy megfelelője a Britta Simon Dropbox Business, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Dropbox Business, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Dropbox Business** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az üzleti tartomány és URL-címek egyetlen bejelentkezési adatait a Dropboxban](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.dropbox.com/sso/<id>`

    b. Az a **azonosító (entityid)** szövegmezőbe írjon be egy értéket: `Dropbox`

    > [!NOTE]
    > A fenti bejelentkezési URL-érték nem valódi értéket. A tényleges bejelentkezési URL-címet, az oktatóanyag későbbi részében ismertetett frissíteni az értéket.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **állítsa be a Dropbox Business** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-dropbox-for-business-single-sign-on"></a>Dropbox Business egyszeri bejelentkezés konfigurálása

1. Az egyszeri bejelentkezés konfigurálása **Dropbox Business** oldalán, ugorjon a Dropbox, az üzleti bérlő, és jelentkezzen be a Dropbox business bérlő számára.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/ic769509.png "egyszeri bejelentkezés konfigurálása")

2. Kattintson a a **felhasználói ikon** válassza **beállítások** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure1.png "egyszeri bejelentkezés konfigurálása")

3. A bal oldali navigációs panelén kattintson **felügyeleti konzol**.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure2.png "egyszeri bejelentkezés konfigurálása")

4. Az a **felügyeleti konzol**, kattintson a **beállítások** a bal oldali navigációs ablaktáblán.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure3.png "egyszeri bejelentkezés konfigurálása")

5. Válassza ki **egyszeri bejelentkezési** lehetőség a **hitelesítési** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure4.png "egyszeri bejelentkezés konfigurálása")

6. Az a **egyszeri bejelentkezési** szakaszban, hajtsa végre az alábbi lépéseket:  

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure5.png "egyszeri bejelentkezés konfigurálása")

    a. Válassza ki **szükséges** mint lehetőséget a legördülő menüből a **egyszeri bejelentkezési**.

    b. Kattintson a **bejelentkezési URL-hozzáadása** és a a **identitásszolgáltató bejelentkezési URL-címe szolgáltató** szövegmezőjébe illessze be a **bejelentkezési URL-cím** érték, amely az Azure Portalon, és ezután válassza másolta **Kész**.

    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/configure6.png "egyszeri bejelentkezés konfigurálása")

    c. Kattintson a **tanúsítvány feltöltése**, és keresse meg a **Base64-kódolású tanúsítványfájl** , amely az Azure Portalról letöltött.

    d. Kattintson a **hivatkozás másolása** , és illessze be a másolt érték a **bejelentkezési URL-** a szövegmező **Dropbox Business tartomány és URL-címek** szakaszban az Azure Portalon.

    e. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Dropbox Business Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Dropbox Business**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **Dropbox Business**.

    ![A Dropbox, az alkalmazások listáját az üzleti hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-dropbox-for-business-test-user"></a>Hozzon létre Dropbox Business tesztfelhasználó számára

Ebben a szakaszban egy Britta Simon nevű felhasználó Dropbox Business jön létre. Dropbox Business támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Dropboxban vállalati, a hitelesítés után egy új jön létre.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót, lépjen kapcsolatba kell [üzleti ügyfél támogatási csapata Dropbox](https://www.dropbox.com/business/contact)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a Dropbox Business csempe a hozzáférési panelen, meg kell automatikusan megtörténik a, a Dropbox Business, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


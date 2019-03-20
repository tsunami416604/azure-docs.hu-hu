---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Cisco összevonó |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Cisco összevonó között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b0763e33607367939476ca155040295de864c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837983"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Oktatóanyag: Cisco összevonó az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Cisco összevonó integrálása az Azure Active Directory (Azure AD).
Cisco összevonó integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Cisco összevonó Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve Cisco összevonó (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Cisco rendszerére, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Cisco összevonó egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Cisco összevonó támogatja **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Cisco összevonó hozzáadása a katalógusból

Cisco összevonó integrálása az Azure AD beállítása, hozzá kell Cisco összevonó a galériából a felügyelt SaaS-alkalmazások listájára.

**Cisco összevonó hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Cisco összevonó**, jelölje be **Cisco összevonó** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Cisco rendszerére a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az [alkalmazásnév] nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó [alkalmazásnév] a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Cisco összevonó egyszeri bejelentkezés konfigurálása](#configure-cisco-umbrella-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a Cisco összevonó tesztfelhasználót](#create-cisco-umbrella-test-user)**  – egy megfelelője a Britta Simon Cisco rendszerére, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Cisco összevonó** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a felhasználónak nincs bármely lépése végrehajtani, mert az alkalmazás már előre integrálva van az Azure-ral.

    ![Cisco összevonó tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-preintegrated-signon.png)

    a. Ha az alkalmazás a konfigurálni kívánt **SP** intiated mód, hajtsa végre az alábbi lépéseket:

    b. Kattintson a **további URL-címet beállítani**.

    c. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe: `https://login.umbrella.com/sso`

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **metaadatainak XML**a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **beállításához a Cisco összevonó** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-cisco-umbrella-single-sign-on"></a>Configure Cisco Umbrella Single Sign-On

1. Egy másik böngészőablakban bejelentkezést a Cisco összevonó vállalati webhelyre rendszergazdaként.

2. A menüt a bal oldali menüjében kattintson az **rendszergazdai** , és keresse meg **hitelesítési** majd kattintson a **SAML**.

    ![A rendszergazda](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Válasszon **más** , majd kattintson a **tovább**.

    ![A másik](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Az a **Cisco összevonó metaadatok**, lapon, kattintson a **tovább**.

    ![A metaadatok](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Az a **metaadatok feltöltése** lapon, ha előre konfigurálta az SAML, válassza ki **Ide kattintva módosíthatja őket** lehetőséget, majd hajtsa végre az az alábbi lépéseket.

    ![A Tovább gombra](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. Az a **v: lehetőséget XML-fájl feltöltése**, töltse fel a **összevonási metaadatainak XML** , amely az Azure Portalról, és a metaadatok feltöltése után a letöltött fájl az alábbi értékek automatikusan kitölti a rendszer automatikusan lekérése, majd kattintson a **következő**.

    ![A choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. A **SAML-konfiguráció ellenőrzése** területén kattintson **teszt a SAML-KONFIGURÁCIÓJA**.

    ![A teszt](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Kattintson a **SAVE** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Cisco összevonó Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Cisco összevonó**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **Cisco összevonó**.

    ![Az alkalmazások listáját a Cisco összevonó hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-cisco-umbrella-test-user"></a>Create Cisco Umbrella test user

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Cisco rendszerére, azok ki kell építeni, Cisco Szolgáltatáscsaládnak.  
Cisco rendszerére, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Egy másik böngészőablakban bejelentkezést a Cisco összevonó vállalati webhelyre rendszergazdaként.

2. A menüt a bal oldali menüjében kattintson az **rendszergazdai** , és keresse meg **fiókok**.

    ![A fiók](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Az a **fiókok** lapon **Hozzáadás** az oldal jobb felső, és hajtsa végre az alábbi lépéseket.

    ![A felhasználó](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. Az a **Utónév** mezőbe írja be például a Keresztnév **Britta**.

    b. Az a **Vezetéknév** mezőbe írja be például a lastname **simon**.

    c. Az a **meghatalmazott rendszergazdai szerepkör kiválasztása**, válassza ki a szerepkört.
  
    d. Az a **E-mail cím** mezőbe írja be például a felhasználó a emailaddress **brittasimon\@contoso.com**.

    e. Az a **jelszó** mezőben adja meg a jelszót.

    f. Az a **jelszó megerősítése** mezőbe írja be újra a jelszót.

    g. Kattintson a **létrehozás**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Cisco összevonó csempére kattint, meg kell lehet automatikusan bejelentkezett a Cisco rendszerére, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

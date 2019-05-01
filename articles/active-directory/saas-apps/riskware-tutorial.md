---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Riskware |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Riskware között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 08db463c92e4ee34819f446c3827ed8dfddc02d7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685313"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Riskware

Ebben az oktatóanyagban elsajátíthatja, hogyan Riskware integrálása az Azure Active Directory (Azure AD).
Riskware integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Riskware Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Riskware (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Riskware az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Riskware egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Riskware **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-riskware-from-the-gallery"></a>Riskware hozzáadása a katalógusból

Az Azure AD integrálása a Riskware konfigurálásához hozzá kell Riskware a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Riskware hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Riskware**válassza **Riskware** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Riskware](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Riskware nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Riskware hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Riskware tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Riskware egyszeri bejelentkezés konfigurálása](#configure-riskware-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Riskware tesztfelhasználót](#create-riskware-test-user)**  – egy megfelelője a Britta Simon Riskware, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Riskware, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Riskware** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Riskware tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:
    
    | Környezet| Az URL-minta|
    |--|--|
    | Felhasználói tesztelés|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | TERMÉKKATALÓGUS| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Az a **azonosító (entityid)** szövegmezőbe írja be az URL-cím:
    
    | Környezet| Az URL-minta|
    |--|--|
    | Felhasználói tesztelés| `https://riskcloud.net/uat` |
    | TERMÉKKATALÓGUS| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > A bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Riskware ügyfél-támogatási csapatának](mailto:support@pansoftware.com.au) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **Riskware beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-riskware-single-sign-on"></a>Riskware egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Riskware vállalati hely rendszergazdaként.

1. Kattintson a jobb felső sarokban, **karbantartási** nyissa meg a karbantartást.

    ![Riskware konfigurációk kezelése](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. A karbantartás oldalon kattintson **hitelesítési**.

    ![Riskware konfigurációs authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. A **hitelesítési konfiguráció** lapon, a következő lépésekkel:

    ![Riskware konfigurációs authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Válassza ki **típus** , **SAML** a hitelesítéshez.

    b. Az a **kód** szövegmezőbe írja be a kódot, például AZURE_UAT.

    c. Az a **leírás** szövegmezőbe írja be a leírást. például az AZURE konfigurálása egyszeri bejelentkezéshez.

    d. A **egyszeri bejelentkezési oldalon** szövegmezőjébe illessze be a **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta.

    e. A **lap Kijelentkezés** szövegmezőjébe illessze be a **kijelentkezési URL-címe** érték, amely az Azure Portalról másolta.

    f. Az a **Post űrlapmező** szövegmező, található bejegyzés választ, amely tartalmazza a SAML SAMLResponse például a mező neve

    g. Az a **XML-címkenév identitás** szövegmezőbe típus attribútuma, amely tartalmazza a NameID például a SAML-válasz egyedi azonosítója.

    h. Nyissa meg a letöltött **metaadatainak Xml** a Jegyzettömbben az Azure portálról másolja a tanúsítványt a metaadat-fájlból, és illessze be azt a **tanúsítvány** szövegmező

    i. A **ügyfél URL-címe** szövegmezőbe, illessze be az értéket, **válasz URL-cím**, amely a támogatási csapat kap.

    j. A **kibocsátó** szövegmezőbe, illessze be az értéket a **azonosító**, amely kap a támogatási csoporthoz.

    > [!Note]
    > Kapcsolattartó [Riskware ügyfél-támogatási csapatának](mailto:support@pansoftware.com.au) beolvasni ezeket az értékeket

    k. Válassza ki **használata utáni** jelölőnégyzetet.

    l. Válassza ki **használható SAML-kérelmet** jelölőnégyzetet.

    m. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Riskware Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Riskware**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Riskware**.

    ![Az alkalmazások listáját a Riskware hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-riskware-test-user"></a>Riskware tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Riskware bejelentkezni, akkor ki kell építeni Riskware be. Riskware a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be egy biztonsági-rendszergazdaként Riskware.

1. Kattintson a jobb felső sarokban, **karbantartási** nyissa meg a karbantartást. 

    ![Riskware konfigurációs kezeli](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. A karbantartás oldalon kattintson **személyek**.

    ![Riskware konfigurációs személyek](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Válassza ki **részletek** lapra, és hajtsa végre az alábbi lépéseket:

    ![Riskware konfiguráció részletei](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Válassza ki **személy típusa** , például alkalmazottak.

    b. A **Utónév** szövegmezőbe írja be például a felhasználó utónevét **Britta**.

    c. A **Vezetéknév** szövegmezőbe írja be például a felhasználó vezetékneve **Simon**.

1. Az a **biztonsági** fülre, hajtsa végre az alábbi lépéseket:

    ![Riskware konfigurációs biztonság](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. A **hitelesítési** szakaszban jelölje be a **hitelesítési** üzemmódot, amely rendelkezik beállítása AZURE-konfiguráció, például egyszeri bejelentkezéshez.

    b. Alatt **bejelentkezési adatait** ebben a szakaszban a **felhasználói azonosító** szövegmezőben adja meg az e-mail címét, például a felhasználó `brittasimon@contoso.com`.

    c. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

1. Az a **szervezet** fülre, hajtsa végre az alábbi lépéseket:

    ![Szervezeti Riskware konfiguráció](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Válassza ki a beállítás értéke **Level1** szervezet.

    b. Alatt **személy elsődleges munkahelyi** ebben a szakaszban a **helye** szövegmezőbe írja be a hely.

    c. A **alkalmazott** szakaszban jelölje be **alkalmazott állapota** magasabb, mint.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Riskware csempére kattint, meg kell lehet automatikusan bejelentkezett a Riskware, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

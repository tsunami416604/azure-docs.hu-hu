---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az LMS elháríthatók |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az LMS elháríthatók között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 51ae45b313cbe6b3cb61777a1571a75047632ca9
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818455"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Oktatóanyag: Az Azure Active Directory integrációja az LMS számára

Ebben az oktatóanyagban elsajátíthatja, hogyan elháríthatók LMS integrálása az Azure Active Directory (Azure AD).
Számára a LMS integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az LMS számára a Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve LMS számára (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

LMS elháríthatók az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Számára a segítségével egyetlen bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Számára a LMS támogatja **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-absorb-lms-from-the-gallery"></a>LMS számára a Hozzáadás a katalógusból

Konfigurálása az Azure AD integrálása a számára a LMS, hozzá kell LMS számára a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá az LMS számára a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **elháríthatók LMS**, jelölje be **LMS számára a** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában LMS számára](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó számára a LMS- **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó számára a segítségével a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az LMS elháríthatók tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Számára a LMS egyszeri bejelentkezés konfigurálása](#configure-absorb-lms-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre LMS számára a tesztfelhasználót](#create-absorb-lms-test-user)**  – egy megfelelője a Britta Simon van számára a segítségével, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés segítségével elháríthatók, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **elháríthatók LMS** alkalmazás integráció lapon válassza ki **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Elháríthatók az LMS-tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    Ha használ **számára 5 – felhasználói felület** használja az alábbi konfigurációt:

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://company.myabsorb.com/account/saml`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://company.myabsorb.com/account/saml`

    Ha használ **számára 5 – új Learner élmény** használja az alábbi konfigurációt:

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [LMS-ügyfél számára a támogatási csapat](https://support.absorblms.com/hc/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **metaadatainak XML**a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **elháríthatók LMS beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-absorb-lms-single-sign-on"></a>Konfigurálása számára a LMS egyszeri bejelentkezéshez

1. Egy új böngészőablakban jelentkezzen be az LMS számára a vállalati webhely rendszergazdaként.

2. Válassza ki a **fiók** gomb jobb felső sarokban.

    ![A fiók gomb](./media/absorblms-tutorial/1.png)

3. A fiók panelen válassza ki **portál beállításait**.

    ![A portál beállításait hivatkozás](./media/absorblms-tutorial/2.png)

4. Válassza ki a **egyszeri bejelentkezési beállításainak kezelése** fülre.

    ![A felhasználók lap](./media/absorblms-tutorial/managesso.png)

5. Az a **egyszeri bejelentkezési beállításainak kezelése** lapon, tegye a következőket:

    ![Az egyszeri bejelentkezés konfigurálása lap](./media/absorblms-tutorial/settings.png)

    a. Az a **neve** szövegmezőbe írja be a nevét, például az Azure AD Marketplace-en egyszeri Bejelentkezést.

    b. Válassza ki **SAML** , egy **metódus**.

    c. A Jegyzettömbben nyissa meg a tanúsítványt, amely az Azure Portalról letöltött. Távolítsa el a **---BEGIN CERTIFICATE---** és **---END CERTIFICATE---** címkék. Ezt követően a a **kulcs** mezőbe illessze be a maradék tartalmat.

    d. Az a **mód** jelölje ki **identitás szolgáltató által kezdeményezett**.

    e. Az a **Id tulajdonsága** jelölje ki az Azure AD-ben konfigurált, a felhasználói azonosító attribútum. Például ha *userPrincipalName* van kiválasztva, az Azure AD-ben válassza **felhasználónév**.

    f. Válassza ki **Sha256** , egy **aláírás típusát**.

    g. Az a **bejelentkezési URL-cím** mezőbe illessze be a **felhasználói hozzáférési URL-címe** az alkalmazás **tulajdonságok** az Azure portál oldalán.

    h. Az a **kijelentkezési URL-címe**, illessze be a **kijelentkezéses URL-cím** fájlból másolt érték a **bejelentkezés konfigurálása** ablakot, az Azure Portalon.

    i. Váltógomb **automatikusan átirányíthatók** való **a**.

6. Válassza ki **mentéséhez.**

    ![Az Egyszeri bejelentkezés csak engedélyezése be-vagy kikapcsolása](./media/absorblms-tutorial/save.png)

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés LMS számára a Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **LMS számára a**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **elháríthatók LMS**.

    ![Az alkalmazások listáját az LMS számára a hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-absorb-lms-test-user"></a>Hozzon létre LMS számára a tesztfelhasználó számára

Az Azure AD-felhasználók számára a LMS bejelentkezni azok kell beállítani az LMS számára. Esetén az LMS számára a kiépítés a manuális feladat.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként az LMS számára a vállalati webhely.

2. Az a **felhasználók** ablaktáblán válassza előbb **felhasználók**.

    ![A felhasználók hivatkozás](./media/absorblms-tutorial/absorblms_userssub.png)

3. Válassza ki **felhasználói** fülre.

    ![Az új hozzáadása legördülő lista](./media/absorblms-tutorial/absorblms_createuser.png)

4. Az a **felhasználó hozzáadása** lapon, tegye a következőket:

    ![A felhasználó hozzáadása oldalon](./media/absorblms-tutorial/user.png)

    a. Az a **Utónév** mezőbe írja be például a Keresztnév **Britta**.

    b. Az a **Vezetéknév** mezőbe írja be például a Vezetéknév **Simon**.

    c. Az a **felhasználónév** mezőbe írjon be egy teljes nevet, például **Britta Simon**.

    d. Az a **jelszó** mezőjébe írja be a felhasználói jelszót.

    e. Az a **jelszó megerősítése** mezőbe írja be ismét a jelszót.

    f. Állítsa be a **aktív** kapcsolót **aktív**.

5. Válassza ki **mentéséhez.**

    ![Az Egyszeri bejelentkezés csak engedélyezése be-vagy kikapcsolása](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Alapértelmezés szerint felhasználók átadása nincs engedélyezve az egyszeri Bejelentkezést. Az ügyfél szeretné, engedélyezze ezt a funkciót, ha azok kell azt beállítania említetteknek megfelelően akár [ez](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentációját. Is vegye figyelembe, hogy felhasználói Provisioing csak érhető el a **számára 5 – új Learner élmény** az ACS URL-cím -`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a LMS számára a csempére kattint, meg kell automatikusan megtörténik a az egyszeri bejelentkezést beállítása, amelynek számára a segítségével. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
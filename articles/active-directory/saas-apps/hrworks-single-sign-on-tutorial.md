---
title: 'Oktatóanyag: A HRworks egyszeri bejelentkezés az Azure Active Directory-integráció |} A Microsoft Docs'
description: Ismerje meg, hogy az egyszeri bejelentkezés Azure Active Directory és a HRworks egyszeri bejelentkezés konfigurálása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: df3799111a26afe33cee5f7b6ee1bc3fc6989758
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439714"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező HRworks egyszeri bejelentkezés

Ebben az oktatóanyagban megismerheti, hogyan integrálható HRworks egyszeri bejelentkezés az Azure Active Directory (Azure AD).
HRworks egyszeri bejelentkezés az Azure AD integrálása nyújt a következő előnyökkel jár:

* Szabályozhatja, hogy HRworks egyszeri bejelentkezés hozzáféréssel rendelkező Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a HRworks egyszeri bejelentkezés (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az HRworks egyszeri bejelentkezést, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* HRworks egyszeri bejelentkezést az egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* HRworks egyszeri bejelentkezést támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>A katalógusból HRworks egyszeri bejelentkezés hozzáadása

Az integráció az Azure AD-be, HRworks egyszeri bejelentkezés konfigurálásához hozzá kell HRworks egyszeri bejelentkezést a galériából a felügyelt SaaS-alkalmazások listájára.

**Adhat hozzá HRworks egyszeri bejelentkezést a katalógusban, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **HRworks egyszeri bejelentkezés**, jelölje be **HRworks egyszeri bejelentkezés** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![HRworks egyszeri bejelentkezést a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az HRworks egyszeri bejelentkezés alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó a HRworks egyszeri bejelentkezés hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az HRworks egyszeri bejelentkezés tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[HRworks egyszeri bejelentkezéses egyszeri bejelentkezés konfigurálása](#configure-hrworks-single-sign-on-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre HRworks egyszeri bejelentkezés tesztfelhasználót](#create-hrworks-single-sign-on-test-user)**  - a-megfelelője a Britta Simon rendelkezik a HRworks egyszeri bejelentkezéshez, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása az HRworks egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **HRworks egyszeri bejelentkezés** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![HRworks egyszeri bejelentkezéses tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [HRworks egyszeri bejelentkezéses ügyfél-támogatási csapatának](mailto:nadja.sommerfeld@hrworks.de) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **mentése HRworks egyszeri bejelentkezés beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>HRworks egyszeri bejelentkezéses egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a HRworks egyszeri bejelentkezés rendszergazdaként.

2. Kattintson a **rendszergazda** > **alapjai** > **biztonsági** > **egyszeri bejelentkezési** a a bal oldali menü sávot, és hajtsa végre az alábbi lépéseket:

       ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Ellenőrizze a **használata egyszeri bejelentkezéshez** mezőbe.

    b. Válassza ki **XML-metaadatok** , **metaadatok bemeneti metódusához**.

    c. Válassza ki **egyes NameID azonosító** , **NameID értéke**.

    d. A Jegyzettömbben, nyissa meg a metaadatok XML-t az Azure Portalról letöltött, másolja a tartalmat, és illessze be azt a **metaadatok** szövegmezőbe.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a felhasználónevet, például BrittaSimon@contoso.com.

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezést a hozzáférés biztosításával az HRworks egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **HRworks egyszeri bejelentkezés**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **HRworks egyszeri bejelentkezés**.

    ![Az alkalmazások listáját a HRworks egyszeri bejelentkezés hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-hrworks-single-sign-on-test-user"></a>Tesztfelhasználó HRworks egyszeri bejelentkezés létrehozása

Engedélyezi az Azure AD-felhasználók, jelentkezzen be a való HRworks egyszeri bejelentkezést, azok ki kell építeni az HRworks egyszeri bejelentkezést. A HRworks egyszeri, manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a HRworks Single Sign On rendszergazdaként.

2. Kattintson a **rendszergazda** > **személyek** > **személyek** > **új személy** származó a a menüsávon bal oldalán.

     ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. Az előugró ablakban kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. Az a **hozzon létre új személyt az ország, a jogi feltételek** előugró ablakban adja meg a megfelelő részleteket, például **Utónév**, **Vezetéknév** kattintson **létrehozása**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen HRworks egyszeri bejelentkezés csempére kattint, akkor kell automatikusan megtörténik a, a HRworks egyszeri bejelentkezést, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


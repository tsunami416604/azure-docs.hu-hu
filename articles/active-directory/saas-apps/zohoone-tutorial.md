---
title: 'Oktatóanyag: Zoho egy az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a egy Zoho között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086231"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Oktatóanyag: Zoho egy az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja Zoho egy Azure Active Directoryval (Azure AD).
Az Azure AD integrálása Zoho egy nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Zoho egy Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett egy Zoho (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Zoho egy az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Zoho egy egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Zoho egy támogatja **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-zoho-one-from-the-gallery"></a>A katalógusból Zoho egy hozzáadása

Az Azure AD integrálása a Zoho egy konfigurálásához hozzá kell Zoho egy a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Zoho egy a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Zoho egy**válassza **Zoho egy** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Zoho egy, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Zoho egy úgynevezett tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Zoho egy hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés az Zoho egy tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Zoho egy egyszeri bejelentkezés konfigurálása](#configure-zoho-one-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre egy Zoho tesztfelhasználót](#create-zoho-one-test-user)**  – egy megfelelője a Britta Simon Zoho egy felhasználó Azure ad-ben reprezentációja van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés egy Zoho, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Zoho egy** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Zoho tartománya és URL-címek egyszeri bejelentkezési adatait](common/idp-relay.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe: `one.zoho.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Az előző **válasz URL-cím** értéke nem valódi. Megjelenik a `<saml-identifier>` #step4 az értéket **konfigurálása Zoho egy egyszeri bejelentkezés** szakaszban, az oktatóanyag későbbi részében ismertetett.

    c. Kattintson a **további URL-címet beállítani**.

    d. Az a **továbbítási állapot** szövegmezőbe írja be egy URL-címe: `https://one.zoho.com`

5. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, hajtsa végre a következő lépést:


    ![Zoho tartománya és URL-címek egyszeri bejelentkezési adatait](common/both-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Az előző **bejelentkezési URL-** értéke nem valódi. A tényleges bejelentkezési URL-címet a rendszer frissítse az értéket a **konfigurálása Zoho egy egyszeri bejelentkezés** szakaszban, az oktatóanyag későbbi részében ismertetett. 

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **Zoho egy beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-zoho-one-single-sign-on"></a>Zoho egy egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Zoho egy vállalati hely rendszergazdaként.

2. Az a **szervezet** lapra, kattintson **telepítő** alatt **SAML-hitelesítés**.

    ![Zoho egy szervezeti](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Az előugró oldalon hajtsa végre az alábbi lépéseket:

    ![Zoho One sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Az a **bejelentkezési URL** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    b. Az a **kijelentkezéses URL-cím** szövegmező, illessze be az értéket a **kijelentkezési URL-címe**, az Azure Portalról másolt.

    c. Kattintson a **Tallózás** feltölteni a **tanúsítvány (Base64)** , amely az Azure Portalról letöltött.

    d. Kattintson a **Save** (Mentés) gombra.

4. A SAML-hitelesítés beállítása a mentés után másolja a **SAML-azonosító** értékét, és fűzze hozzá a az **válasz URL-cím** helyén `<saml-identifier>`, például `https://accounts.zoho.com/samlresponse/one.zoho.com` , és illessze be a generált értéket a **Válasz URL-cím** szövegmező alatt **alapszintű SAML-konfigurációja** szakaszban.

    ![Zoho egy saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Nyissa meg a **tartományok** fülre, majd **hozzáadni Domain**.

    ![Zoho One domain](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Az a **hozzáadni Domain** lapon, a következő lépésekkel:

    ![Zoho One add domain](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Az a **tartománynév** szövegmező, például a contoso.com típus tartomány.

    b. Kattintson a **Hozzáadás**lehetőségre.

    >[!Note]
    >Miután hozzáadta a tartomány kövesse [ezek](https://www.zoho.com/one/help/admin-guide/domain-verification.html) a tartomány ellenőrzésének lépéseit. Miután a tartomány már ellenőrizve van, a saját tartománynevét a **bejelentkezési URL-** a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zoho egy Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Zoho egy**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Zoho egy**.

    ![Az alkalmazások listáját a Zoho egy hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-zoho-one-test-user"></a>Zoho egy tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a Zoho egy Azure AD-felhasználók, akkor ki kell építeni az Zoho egy. Zoho egy, a kiépítés a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be egy, a biztonsági rendszergazda Zoho.

2. Az a **felhasználók** lapra, kattintson a **felhasználói embléma**.

    ![Zoho One user](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Az a **felhasználó hozzáadása** lapon, a következő lépésekkel:

    ![Zoho több felhasználó hozzáadása](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. A **neve** szöveget adja meg például a felhasználó nevét **Britta simon**.
    
    b. A **E-mail cím** szöveget adja meg az e-mailt, például a felhasználó brittasimon@contoso.com.

    >[!Note]
    >Válasszon ellenőrzött tartományt a tartomány listából.

    c. Kattintson a **Hozzáadás**lehetőségre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zoho egy csempére kattint, akkor kell automatikusan megtörténik a a Zoho egy, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


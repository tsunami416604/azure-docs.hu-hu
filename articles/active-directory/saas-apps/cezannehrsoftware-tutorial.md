---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Cezanne HR szoftverrel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Cezanne HR szoftverek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 949b5b1aaab60e5b1d160963ac3f024dbbfbe552
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834958"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Oktatóanyag: Az Azure Active Directory-integráció Cezanne HR szoftverrel

Ebben az oktatóanyagban elsajátíthatja, hogyan Cezanne HR szoftver integrálása az Azure Active Directory (Azure AD).
Cezanne HR szoftver integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Cezanne HR szoftverek az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett Cezanne HR-szoftverek (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Cezanne HR-szoftverekkel rendelkező, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Cezanne HR szoftver egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Cezanne HR szoftver támogatja-e **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Cezanne HR szoftver hozzáadása a katalógusból

Az Azure AD-be a HR-szoftver Cezanne integráció konfigurálásához, kell Cezanne HR szoftver hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Cezanne HR szoftver hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Cezanne HR szoftver**, jelölje be **Cezanne HR szoftver** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Cezanne HR szoftver](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó Cezanne HR szoftverrel **Britta Simon**.
Az egyszeri bejelentkezés működjön az Azure AD-felhasználót és a kapcsolódó felhasználó Cezanne HR szoftveres hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés Cezanne HR szoftverrel tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Cezanne HR szoftver egyszeri bejelentkezés konfigurálása](#configure-cezanne-hr-software-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Cezanne HR szoftver tesztfelhasználót](#create-cezanne-hr-software-test-user)**  – egy megfelelője a Britta Simon Cezanne HR szoftver, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Cezanne HR szoftverrel, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Cezanne HR szoftver** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Cezanne HR szoftver tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be az URL-cím: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és válasz URL-cím. Kapcsolattartó [Cezanne HR Szoftverügyfél támogatási csapatának](https://cezannehr.com/services/support/) beolvasni ezeket az értékeket.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Cezanne HR szoftver beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Cezanne HR szoftver egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, a bejelentkezés a Cezanne HR szoftver bérlői rendszergazdaként.

2. A bal oldali navigációs panelén kattintson **rendszerbeállítás**. Lépjen a **biztonsági beállítások**. Majd keresse meg a **egyszeri bejelentkezési konfigurációjának**.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. Az a **engedélyezése a felhasználók számára, hogy jelentkezzen be a következő egyszeri bejelentkezéses (SSO) szolgáltatás** panel, ellenőrizze a **SAML 2.0** mezőbe, majd válassza ki a **speciális konfiguráció** lehetőséget.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Kattintson a **új hozzáadása** gombra.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Hajtsa végre a következő lépéseket **SAML 2.0 IDENTITÁSSZOLGÁLTATÓ** szakaszban.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Adja meg a nevét, az identitásszolgáltató a **megjelenítendő név**.

    b. Az a **entitásának azonosítója** szövegmezőjébe illessze be az értéket, **Azure Ad-azonosító** az Azure Portalról másolt.

    c. Módosítsa a **SAML kötés** a "POST".

    d. A a **biztonsági jogkivonat szolgáltatás végpontját** szövegmezőjébe illessze be az értéket, **bejelentkezési URL-cím** az Azure Portalról másolt.

    e. Adja meg a felhasználói azonosító attribútum neve szövegmező `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Kattintson a **feltöltése** ikonra, töltse fel az Azure Portalról letöltött tanúsítványt.

    g. Kattintson az **OK** gombra.

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Cezanne HR szoftver Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Cezanne HR szoftver**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Cezanne HR szoftver**.

    ![A Cezanne HR szoftverhivatkozás alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-cezanne-hr-software-test-user"></a>Tesztfelhasználó Cezanne HR szoftver létrehozása

Ahhoz, hogy az Azure AD-felhasználók Cezanne HR szoftver-ba való bejelentkezéshez, akkor ki kell építeni Cezanne HR szoftver. Cezanne HR szoftverek esetén a manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Cezanne HR szoftver vállalati hely rendszergazdaként.

2. A bal oldali navigációs panelén kattintson **rendszerbeállítás**. Lépjen a **felhasználók kezelése**. Majd keresse meg a **új felhasználó hozzáadása**.

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "új felhasználó")

3. A **személy adatai** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "új felhasználó")

    a. Állítsa be **belső felhasználói** , ki.

    b. Az a **Utónév** szövegmező, például a felhasználó utónevét típusa **Britta**.  

    c. Az a **Vezetéknév** szövegmezőbe, például a felhasználó vezetéknevét típusa **Simon**.

    d. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

4. A **fiókadatok** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "új felhasználó")

    a. Az a **felhasználónév** szövegmezőbe írja be az e-mailt, felhasználó, például Brittasimon@contoso.com.

    b. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    c. Válassza ki **HR Professional** , **biztonsági szerepkör**.

    d. Kattintson az **OK** gombra.

5. Navigáljon a **egyszeri bejelentkezés** lapra, és válassza **új hozzáadása** a a **SAML 2.0-s azonosítók** területen.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "felhasználó")

6. Válassza ki az identitásszolgáltatót a **identitásszolgáltató** és a szövegmezőbe, **felhasználói azonosító**, adja meg a Britta Simon fiók e-mail-címe.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "felhasználó")

7. Kattintson a **mentése** gombra.

    ![Felhasználói](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "felhasználó")

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Cezanne HR szoftver csempére kattint, akkor kell automatikusan megtörténik a a Cezanne HR szoftver, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

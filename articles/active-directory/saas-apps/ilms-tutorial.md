---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iLMS |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és iLMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d528195161b987dda783c4b22721e950f702f94
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984458"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iLMS

Ebben az oktatóanyagban elsajátíthatja, hogyan iLMS integrálása az Azure Active Directory (Azure AD).
ILMS integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá iLMS Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve iLMS (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

ILMS az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* iLMS egyszeri bejelentkezéses engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* támogatja a iLMS **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-ilms-from-the-gallery"></a>ILMS hozzáadása a katalógusból

Az Azure AD integrálása a iLMS konfigurálásához hozzá kell iLMS a katalógusból a felügyelt SaaS-alkalmazások listájára.

**ILMS hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **iLMS**válassza **iLMS** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![az eredmények listájában iLMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az iLMS nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó iLMS hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az iLMS tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Egyszeri bejelentkezés iLMS konfigurálása](#configure-ilms-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre iLMS tesztfelhasználót](#create-ilms-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó iLMS rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés iLMS, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **iLMS** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Tartomány és URL-címek iLMS egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegbeviteli mezőben illessze be a **azonosító** másolt érték **szolgáltató** SAML beállításai iLMS felügyeleti portálon.

    b. Az a **válasz URL-cím** szövegbeviteli mezőben illessze be a **végpontja (URL)** másolt érték **szolgáltató** SAML-beállítások a következő mintának kellene iLMS felügyeleti portál szakaszában `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Tartomány és URL-címek iLMS egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegbeviteli mezőben illessze be a **végpontja (URL)** másolt érték **szolgáltató** iLMS felügyeleti portálon, az SAML-beállításai `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

6. Igény szerinti kiépítés engedélyezéséhez iLMS alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a felhasználói attribútumok párbeszédpanel.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Engedélyeznie kell a **Un-recognized felhasználói fiók létrehozása** a iLMS ezek az attribútumok leképezésére. Kövesse az utasításokat [Itt](http://support.inspiredelearning.com/customer/portal/articles/2204526) az attribútumok konfigurációjában képet kapjon.

7. Emellett a fentiekben iLMS alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Adatforrás-attribútum|
    | --------|------------- |
    | körzet | user.department |
    | régió | User.state |
    | Szervezeti egység | user.jobtitle |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

9. Az a **iLMS beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-ilms-single-sign-on"></a>ILMS egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **iLMS felügyeleti portál** rendszergazdaként.

2. Kattintson a **SSO:SAML** alatt **beállítások** lapon nyissa meg az SAML-beállítások, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/1.png)

3. Bontsa ki a **szolgáltató** szakaszt, és másolja a **azonosító** és **végpontja (URL)** értéket.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/2.png) 

4. A **identitásszolgáltató** területén kattintson **metaadatok importálása**.

5. Válassza ki a **összevonási metaadatok** az Azure-portálról letöltött fájl **SAML-aláíró tanúsítvány** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Ha szeretné engedélyezni, igény szerinti kiépítés iLMS fiókok létrehozása az ENSZ-ismeri fel a felhasználók, kövesse az alábbi lépéseket:

    a. Ellenőrizze **nem felismert felhasználói fiók létrehozása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Az attribútumok leképezésére iLMS attribútumokat az Azure AD-ben. Az attribútum oszlopának adja meg az attribútum neve vagy az alapértelmezett értéket.

    c. Lépjen a **üzleti szabályok** lapra, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/5.png)

    d. Ellenőrizze **Un-recognized régióban hozzon létre, részlegek és szervezeti egységek** hozhat létre a régióban, részlegek és szervezeti egységek, amelyek még nem léteznek az egyszeri bejelentkezés időpontjában.

    e. Ellenőrizze **Update felhasználói profil során bejelentkezési** , adja meg, hogy a felhasználói profil frissül minden egyszeri bejelentkezés.

    f. Ha a **frissítés üres értékek a felhasználói profil nem kötelező mezők** beállítás be van jelölve, a profil nem kötelező mezők, amelyek üres lesz bejelentkezés után is a felhasználó iLMS profilt tartalmazhatnak üres értékeket az adott mezők.

    g. Ellenőrizze **hiba az értesítési E-mail küldése** , és adja meg az e-mail a felhasználó, ahol a hiba értesítő e-mailt kapni szeretné.

7. Kattintson a **mentése** gombra kattintva mentse a beállításokat.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/save.png)

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

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés iLMS való hozzáférést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **iLMS**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **iLMS**.

    ![Az alkalmazások listáját a iLMS hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-ilms-test-user"></a>ILMS tesztfelhasználó létrehozása

Alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére automatikusan létrehozza az alkalmazásban. Igény szerinti fog működni, ha akkor kattintott a **Un-recognized felhasználói fiók létrehozása** jelölőnégyzet iLMS felügyeleti portálon található SAML konfigurációs beállítása során.

Ha létrehoz egy felhasználót manuálisan kell, majd kövesse az alábbi lépéseket:

1. Jelentkezzen be rendszergazdaként a iLMS vállalati webhely.

2. Kattintson a **felhasználó regisztrálása** alatt **felhasználók** elemére kattintva nyissa meg **felhasználó regisztrálása** lapot.

   ![Alkalmazott hozzáadása](./media/ilms-tutorial/3.png)

3. Az a **regisztrálása felhasználó** lapon, a következő lépésekkel.

    ![Alkalmazott hozzáadása](./media/ilms-tutorial/create_testuser_add.png)

    a. Az a **Utónév** szövegmezőbe írja be az első neve például Britta.

    b. Az a **Vezetéknév** szövegmezőbe írja be például a Simon utolsó nevét.

    c. Az a **E-mail-azonosító** szövegmezőbe írja be az e-mail-cím a felhasználó például BrittaSimon@contoso.com.

    d. Az a **régió** legördülő menüben válassza ki a régiót értékét.

    e. Az a **osztás** legördülő menüben válassza ki a részleg értékét.

    f. Az a **részleg** legördülő menüben válassza ki a részleg értékét.

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Felhasználói regisztráció e-mail a kiválasztásával küldhet **regisztrációs üzenet küldése** jelölőnégyzetet.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a iLMS csempére kattint, meg kell lehet automatikusan bejelentkezett a iLMS, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
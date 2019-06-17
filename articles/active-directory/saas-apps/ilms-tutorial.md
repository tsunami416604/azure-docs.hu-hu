---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iLMS |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és iLMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b2e012a4ce8ac4a9a5afb895d545beb0a0b8946
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100620"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Oktatóanyag: ILMS integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a iLMS integrálása az Azure Active Directory (Azure AD) lesz. ILMS integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá iLMS Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve az Azure AD-fiókjukat iLMS.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* iLMS egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. támogatja a iLMS **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-ilms-from-the-gallery"></a>ILMS hozzáadása a katalógusból

Az Azure AD integrálása a iLMS konfigurálásához hozzá kell iLMS a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **iLMS** kifejezést a keresőmezőbe.
1. Válassza ki **iLMS** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata iLMS **Britta Simon**. Az SSO működjön kell iLMS az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Konfigurálás, és tesztelés az Azure AD SSO iLMS, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Egyszeri bejelentkezés iLMS konfigurálása](#configure-ilms-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre iLMS tesztfelhasználót](#create-ilms-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó iLMS rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **iLMS** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lapon, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, adja meg az értékeket a következő mezőket:

    a. Az a **azonosító** szövegbeviteli mezőben illessze be a **azonosító** másolt érték **szolgáltató** SAML beállításai iLMS felügyeleti portálon.

    b. Az a **válasz URL-cím** szövegbeviteli mezőben illessze be a **végpontja (URL)** másolt érték **szolgáltató** SAML-beállítások a következő mintának kellene iLMS felügyeleti portál szakaszában `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegbeviteli mezőben illessze be a **végpontja (URL)** másolt érték **szolgáltató** iLMS felügyeleti portálon, az SAML-beállításai `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Igény szerinti kiépítés engedélyezéséhez iLMS alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a felhasználói attribútumok párbeszédpanel.

    > [!NOTE]
    > Engedélyeznie kell a **Un-recognized felhasználói fiók létrehozása** a iLMS ezek az attribútumok leképezésére. Kövesse az utasításokat [Itt](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) az attribútumok konfigurációjában képet kapjon.

1. Emellett a fentiekben iLMS alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Adatforrás-attribútum|
    | --------|------------- |
    | körzet | user.department |
    | régió | User.state |
    | Szervezeti egység | user.jobtitle |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az a **iLMS beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-ilms-sso"></a>ILMS egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **iLMS felügyeleti portál** rendszergazdaként.

2. Kattintson a **SSO:SAML** alatt **beállítások** lapon nyissa meg az SAML-beállítások, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/1.png)

3. Bontsa ki a **szolgáltató** szakaszt, és másolja a **azonosító** és **végpontja (URL)** értéket.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/2.png) 

4. A **identitásszolgáltató** területén kattintson **metaadatok importálása**.

5. Válassza ki a **összevonási metaadatok** fájlt letölteni az Azure Portalon a **SAML-aláíró tanúsítvány** szakaszban.

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

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon használja az Azure egyszeri bejelentkezés iLMS való hozzáférést kell engedélyeznie.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **iLMS**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

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

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A iLMS csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a iLMS, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Oktatóanyag: HubSpot az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és HubSpot között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d15f34ad1a2391ef52d86fcc3f11d99a334eabcb
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680145"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Oktatóanyag: HubSpot az Azure Active Directory-integráció

Ebben az oktatóanyagban megismerheti, hogyan történő HubSpot integrálása az Azure Active Directory (Azure AD).
HubSpot integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Az Azure ad-ben történő HubSpot hozzáféréssel rendelkező szabályozhatja.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett történő HubSpot (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Történő HubSpot az Azure AD-integráció konfigurálása, szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* HubSpot egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* HubSpot támogatja **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-hubspot-from-the-gallery"></a>HubSpot hozzáadása a katalógusból

Történő HubSpot integrálása az Azure AD beállítása, hozzá kell HubSpot a galériából a felügyelt SaaS-alkalmazások listájára.

**Történő HubSpot hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **HubSpot**válassza **HubSpot** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![HubSpot a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapján HubSpot **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó HubSpot hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az HubSpot tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[HubSpot egyszeri bejelentkezés konfigurálása](#configure-hubspot-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[HubSpot tesztfelhasználó létrehozása](#create-hubspot-test-user)**  – egy megfelelője a Britta Simon HubSpot, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés HubSpot, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **HubSpot** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![HubSpot tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját és a válasz URL-cím, az oktatóanyag későbbi részében ismertetett ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![HubSpot tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím:  `https://app.hubspot.com/login`

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **HubSpot beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-hubspot-single-sign-on"></a>HubSpot egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be történő HubSpot rendszergazdai fiókjával.

2. Kattintson a **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config1.png)

3. Kattintson a **alapértelmezett fiók**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config2.png)

4. Görgessen le a **biztonsági** szakaszt, és kattintson a **beállítása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config3.png)

5. Az a **egyszeri bejelentkezés beállítása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config4.png)

    a. Kattintson a **másolási** gombra, hogy a **célközönség URl(Service Provider Entity ID)** értékét, és illessze be azt a **azonosító** szövegmezőjébe a **alapszintű SAML Konfigurációs** szakaszban az Azure Portalon.

    b. Kattintson a **másolási** gombra, hogy a **jelentkezzen be az URL-címe, ACS, címzett vagy átirányítási** értékét, és illessze be azt a **válasz URL-cím** szövegmezőjébe a **alapszintű SAML Konfigurációs** szakaszban az Azure Portalon.

    c. Az a **identitás azonosítója vagy a kiállító URL-címe** szövegmezőjébe illessze be a **az Azure AD-azonosító** az Azure Portalról másolt érték.

    d. Az a **identitás szolgáltató egyszeri bejelentkezési URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-cím** az Azure Portalról másolt érték.

    e. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben. A tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** mezőbe.

    f. Kattintson az **Ellenőrzés** lehetőségre.

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

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával történő HubSpot Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **HubSpot**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **HubSpot**.

    ![Az alkalmazások listáját a HubSpot hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-hubspot-test-user"></a>HubSpot tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók történő HubSpot jelentkezzen be, akkor ki kell építeni történő HubSpot. HubSpot, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **HubSpot** rendszergazdaként a vállalati webhely.

2. Kattintson a **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/config1.png)

3. Kattintson a **felhasználók és csoportok**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user1.png)

4. Kattintson a **felhasználó létrehozása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user2.png)

5. Adja meg például a felhasználó e-mail-címe `brittasimon\@contoso.com` a a **hozzáadása e-mail addess(es)** szövegmezőbe, és kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user3.png)

6. Az a **felhasználók létrehozása** szakaszban, haladjon végig a minden egyes fülre, és válassza ki a megfelelő beállításokat és a felhasználót, majd kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user4.png)

7. Kattintson a **küldése** küldése a felhasználónak a meghívót.

    ![Egyszeri bejelentkezés konfigurálása](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Felhasználó fogja aktiválni a meghívás elfogadása után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a HubSpot csempére kattint, meg kell lehet automatikusan bejelentkezett a HubSpot, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a Salesforce-szal |} A Microsoft Docs'
description: Ismerje meg, hogyan egyszeri bejelentkezés konfigurálása Azure Active Directory és a Salesforce között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cec93af806710a6e32ddca8673722de11b00df4f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761225"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Oktatóanyag: Az Azure Active Directory-integráció, a Salesforce-szal

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Salesforce-hoz az Azure Active Directory (Azure AD).
Az Azure AD integrálása a Salesforce nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Salesforce-hoz az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a Salesforce-hoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Salesforce-szal, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Salesforce-ban az egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Salesforce **SP** által kezdeményezett egyszeri bejelentkezés

* Támogatja a Salesforce **igény szerinti** felhasználók átadása

* Támogatja a Salesforce [ **automatikus** felhasználók átadása](salesforce-provisioning-tutorial.md)

## <a name="adding-salesforce-from-the-gallery"></a>A Salesforce hozzáadása a katalógusból

Konfigurálhatja az Azure AD integrálása a Salesforce, hozzá kell Salesforce a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Salesforce-hoz a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Salesforce**, jelölje be **Salesforce** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában a Salesforce-ban](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Salesforce-szal nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy hivatkozás kapcsolatot egy Azure AD-felhasználót és a kapcsolódó felhasználó között a Salesforce-ban kell hozható létre.

Az Azure AD egyszeri bejelentkezés a Salesforce-szal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A Salesforce egyszeri bejelentkezés konfigurálása](#configure-salesforce-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a Salesforce tesztfelhasználót](#create-salesforce-test-user)**  - a-megfelelője a Britta Simon rendelkezik a Salesforce-ban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés, a Salesforce-szal, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Salesforce** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Salesforce-tartománnyal és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be az értéket a következő minta használatával:

    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Az a **azonosító** szövegmezőbe írja be az értéket a következő minta használatával:

    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és azonosító. Kapcsolattartó [a Salesforce-ügyfél-támogatási csapatának](https://help.salesforce.com/support) beolvasni ezeket az értékeket.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **Salesforce beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-salesforce-single-sign-on"></a>A Salesforce egyszeri bejelentkezés konfigurálása

1. Nyissa meg egy új lapot a böngészőben, és a Salesforce-rendszergazdai fiókjával való bejelentkezéshez.

2. Kattintson a a **telepítő** alatt **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/configure1.png)

3. Görgessen le a **beállítások** navigációs ablaktábláján kattintson **identitás** a kapcsolódó szakasz kibontásához. Kattintson a **egyszeri bejelentkezési beállításainak**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso.png)

4. Az a **egyszeri bejelentkezési beállításainak** lap, kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Ha Ön nem lehet engedélyezni az egyszeri bejelentkezés beállításainak Salesforce-fiókjához, szükség lehet kapcsolódni a [a Salesforce-ügyfél-támogatási csapatának](https://help.salesforce.com/support).

5. Válassza ki **SAML engedélyezett**, és kattintson a **mentése**.

      ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-enable-saml.png)

6. Az SAML egyszeri bejelentkezési beállításainak konfigurálásához kattintson **új metaadatait tartalmazó fájl a**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Kattintson a **fájl kiválasztása** az metaadatainak XML-fájl, amelyre letöltötte az Azure Portalon, majd kattintson a feltöltendő **létrehozás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/xmlchoose.png)

8. Az a **SAML egyszeri bejelentkezési beállításainak** lapon mezők automatikusan feltölti és kattintson a Mentés gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/salesforcexml.png)

9. Kattintson a bal oldali navigációs ablaktáblán, a Salesforce-ban, **vállalati beállítások** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-my-domain.png)

10. Görgessen le a **hitelesítési konfiguráció** szakaszt, és kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. Az a **hitelesítési konfiguráció** szakaszt, ellenőrizze a **AzureSSO** , **hitelesítési szolgáltatás** a SAML SSO-konfigurációt, és kattintson a  **Mentés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Ha egynél több hitelesítési szolgáltatás ki van jelölve, felhasználók mely hitelesítési szolgáltatás, például a bejelentkezéshez egyszeri bejelentkezéshez, a Salesforce-környezet inicializálása során való megadását kéri. Ha nem szeretné, hogy fordulhat elő, akkor meg kell **ne jelölje be minden más hitelesítési szolgáltatás**.

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

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezést a hozzáférés biztosításával a Salesforce-hoz.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Salesforce**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Salesforce**.

    ![A Salesforce-hivatkozás alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-salesforce-test-user"></a>A Salesforce tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Salesforce-ban. A Salesforce támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik, a Salesforce-ban, egy új létrejön, amikor megpróbálja elérni a Salesforce. Salesforce-hoz is támogatja a felhasználók automatikus átadása, további részleteket talál [Itt](salesforce-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panel a Salesforce csempére kattint, akkor kell automatikusan megtörténik a a a Salesforce-hoz, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Felhasználók átadásának konfigurálása](salesforce-provisioning-tutorial.md)

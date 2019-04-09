---
title: 'Oktatóanyag: Úgy tesszük az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a úgy tesszük között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f176ce43e4d7162c664115a6fd3ce9369fe79a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270064"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Oktatóanyag: Úgy tesszük az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a úgy tesszük az Azure Active Directory (Azure AD).
Úgy tesszük integrálása az Azure ad-vel nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá, így még a nem Azure AD-ben.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be, így még a nem (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Úgy tesszük az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Úgy, hogy egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Így még a nem támogatja a **SP** által kezdeményezett egyszeri bejelentkezés

* Így még a nem támogatja a **igény szerinti** felhasználók átadása

## <a name="adding-way-we-do-from-the-gallery"></a>Úgy tesszük hozzáadása a katalógusból

Az Azure AD-be, így még a nem az integráció konfigurálásához hozzá kell úgy tesszük a galériából a felügyelt SaaS-alkalmazások listájára.

**Úgy tesszük hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **úgy tesszük**válassza **úgy tesszük** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Így még a nem a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az úgy tesszük nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó úgy tesszük a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés módja végzünk az tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja úgy, hogy egyszeri bejelentkezés](#configure-way-we-do-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre felhasználói teszteljük módon](#create-way-we-do-test-user)**  – így még a nem kapcsolódó felhasználói reprezentációja az Azure AD-megfelelője a Britta Simon van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálni úgy tesszük, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **úgy tesszük** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Így azt tegye tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [módon azt tegye ügyfél-támogatási csapatának](mailto:support@waywedo.com) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **(Raw)tanúsítvány** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificateraw.png)

6. Az a **állítsa be úgy tesszük** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-way-we-do-single-sign-on"></a>Úgy, hogy egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be így még a nem a biztonsági-rendszergazdájaként.

2. Kattintson a **személy ikon** az bármely módon végzünk az oldal jobb felső sarkában kattintson a **fiók** a legördülő menüből.

    ![Úgy tesszük fiók](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

3. Kattintson a **menüikonra** nyissa meg a leküldéses navigációs menüben, majd kattintson a **az egyszeri bejelentkezést**.

    ![Úgy tesszük egyetlen](./media/waywedo-tutorial/tutorial_waywedo_single.png)

4. Az a **egyszeri bejelentkezés beállítása** lapon, a következő lépésekkel:

    ![Mentés végzünk módja](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Kattintson a **kapcsolja be az egyszeri bejelentkezés** kapcsolót **Igen** egyszeri bejelentkezés engedélyezéséhez.

    b. Az a **egyetlen bejelentkezési nevet** szövegmezőbe írja be a nevét.

    c. A a **Entitásazonosító** szövegmezőjébe illessze be az értéket, **az Azure AD-azonosító**, az Azure Portalról másolt.

    d. Az a **SAML egyszeri bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    e. Gombra kattintva töltse fel a tanúsítványt a **kiválasztása** megjelenítő gombra **tanúsítvány**.

    f. **Választható beállítások** -
    
    * Engedélyezi a jelszavak – Ha ez a beállítás le van tiltva, a rendszeres jelszó függvények a úgy tesszük, hogy a felhasználók csak használhatják az egyszeri bejelentkezés.

    * Engedélyezze az Automatikus kiépítés – Ha ez engedélyezve van, a bejelentkezéshez használt e-mail-cím lesz automatikusan összehasonlítva úgy tesszük a felhasználók listája. Ha az e-mail-cím nem egyezik meg az aktív felhasználók az úgy tesszük, automatikusan hozzáadja egy új felhasználói fiók jelentkezik be, a hiányzó információit kérő személy.

      > [!NOTE]
      > Egyszeri bejelentkezés során hozzáadott felhasználók általános felhasználói is hozzáadja, és nincsenek hozzárendelve egy szerepkörhöz a rendszerben. Rendszergazdaként nyissa meg, és módosíthatja az ő biztonsági szerepkörük-szerkesztőben vagy rendszergazdaként, és szerepkörök is hozzárendelhet egy vagy több szervezeti diagram. 

    g. Kattintson a **mentése** megőrizni a beállításokat.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés úgy tesszük az Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **úgy tesszük**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **úgy tesszük**.

    ![Az alkalmazások listáját a úgy tesszük hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-way-we-do-test-user"></a>Úgy tesszük tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó a úgy tesszük jön létre. Így még a nem támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a úgy tesszük, egy új jön létre a hitelesítés után.

> [!Note]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [módon azt tegye ügyfél-támogatási csapatának](mailto:support@waywedo.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen úgy tesszük csempére kattint, meg kell lehet automatikusan bejelentkezett a úgy tesszük, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


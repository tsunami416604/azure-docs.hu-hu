---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a végtelen Campus |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a végtelen Campus között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 75cc92e420b08307377ab85a2d8d121303429ce5
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500039"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Oktatóanyag: Végtelen Campus az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan végtelen Campus integrálása az Azure Active Directory (Azure AD).
Végtelen Campus integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a végtelen Campus Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve végtelen Campus (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Végtelen Campus konfigurálni az Azure AD-integráció, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Végtelen Campus egyszeri bejelentkezés engedélyezve van az előfizetés
* Legalább szüksége lehet az Azure Active Directory-rendszergazda, és telephelyi termék biztonsági szerepkör a "tanuló információk System (SIS)" konfigurálásának befejezéséhez.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja-e végtelen Campus **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-infinite-campus-from-the-gallery"></a>Végtelen Campus hozzáadása a katalógusból

Az Azure AD integrálása a végtelen Campus konfigurálásához hozzá kell végtelen Campus a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Végtelen Campus hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **végtelen Campus**, jelölje be **végtelen Campus** az eredmény panelen, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában végtelen Campus](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapján végtelen Campus **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó a végtelen Campus hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a végtelen Campus tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Végtelen Campus egyszeri bejelentkezés konfigurálása](#configure-infinite-campus-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre végtelen telephelyi tesztfelhasználót](#create-infinite-campus-test-user)**  – egy megfelelője a Britta Simon végtelen Campus, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés végtelen Campus, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **végtelen Campus** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű SAML-konfigurációja szakasz hajtsa végre a következő lépéseket (vegye figyelembe, hogy a tartomány üzemeltetési modell, függ, de a **teljesen minősített-TARTOMÁNYI** értéket meg kell egyeznie a végtelen Campus telepítése):

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Végtelen Campus tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier-reply.png)

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Végtelen Campus egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be végtelen Campus egy biztonsági-rendszergazdaként.

2. Kattintson a bal oldali menü, **rendszerfelügyelet**.

    ![A rendszergazda](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Navigáljon a **felhasználói biztonság** > **SAML felügyeleti** > **SSO szolgáltató konfigurációja**.

    ![Az saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Az a **SSO szolgáltató konfigurációja** lapon, a következő lépésekkel:

    ![Az egyszeri bejelentkezés](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Válassza ki **engedélyezése SAML egyszeri bejelentkezési**.

    b. Szerkessze a **nem kötelező attribútum neve** tartalmaznia **neve**

    c. Az a **válassza ki a Identity Provider (IDP) kiszolgáló adatainak beolvasása** szakaszban válassza ki **metaadatok URL-címe**, illessze be a **alkalmazás összevonási metaadatainak URL-címe** érték, amely rendelkezik az Azure Portalon, a box-tárhelyéből, és kattintson a **szinkronizálási**.

    d. Kattintás után **szinkronizálási** az értékek első automatikusan kitölti a **SSO szolgáltató konfigurációja** lapot. Ezeket az értékeket a 4. lépésben látható értékek megfelelő ellenőrizhető.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com.

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

> [!NOTE]
> Ha azt szeretné, hogy az összes, az Azure-felhasználóknak egyszeri bejelentkezéssel rendelkezik végtelen Campus hozzáférést, és a hozzáférésének végtelen Campus belső engedélyek rendszeren alapulnak, állíthatja a **felhasználó-hozzárendelés szükséges** tulajdonság nem az alkalmazás és hagyja ki a következő lépéseket.

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés végtelen Campus Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **végtelen Campus**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **végtelen Campus**.

    ![Az alkalmazások listáját a végtelen Campus hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-infinite-campus-test-user"></a>Hozzon létre végtelen telephelyi tesztfelhasználót.

Végtelen Campus rendelkezik egy ingyenes demográfiai architektúra. Vegye fel a kapcsolatot [végtelen Campus támogatási csapatának](mailto:sales@infinitecampus.com) a felhasználók hozzáadása az végtelen Campus platformon.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a végtelen Campus csempére kattint, meg kell lehet automatikusan bejelentkezett a végtelen Campus, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

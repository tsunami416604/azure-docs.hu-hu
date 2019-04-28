---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező PageDNA |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és PageDNA között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11cb309e6c49ed36247398909e5e1b7ad9f7bc42
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62736426"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező PageDNA

Ebben az oktatóanyagban elsajátíthatja, hogyan PageDNA integrálása az Azure Active Directory (Azure AD).

PageDNA integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Az Azure AD-ben szabályozhatja, ki férhet hozzá PageDNA.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be PageDNA (egyszeri bejelentkezés), az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

További információk az Azure AD-szoftverként (saas biztosított) alkalmazás integrációja: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

PageDNA az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Az egyszeri bejelentkezés engedélyezve PageDNA előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálni és a egy tesztkörnyezetben az Azure AD egyszeri bejelentkezés tesztelése és PageDNA integrálása az Azure ad-ben.

PageDNA támogatja a következő funkciókat:

* SP által kezdeményezett egyszeri bejelentkezés (SSO).

* Just-in-time-felhasználók létrehozásának.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Az Azure Marketplace-ről PageDNA hozzáadása

Az Azure AD integrálása a PageDNA konfigurálásához hozzá kell PageDNA az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listájára:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com?azure-portal=true).
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directoryval opciót.](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza **+ új alkalmazás** a panel tetején.

    ![Az új alkalmazás-beállítás](common/add-new-app.png)

1. A Keresés mezőbe írja be a **PageDNA**. A keresési eredmények között, válassza ki a **PageDNA**, majd válassza ki **Hozzáadás** , vegye fel az alkalmazást.

    ![Az eredmények listájában PageDNA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapján PageDNA **Britta Simon**. Az egyszeri bejelentkezés működjön a PageDNA a kell létesítenie az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat.

Az Azure AD egyszeri bejelentkezés az PageDNA tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[PageDNA egyszeri bejelentkezés konfigurálása](#configure-pagedna-single-sign-on)**  az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Hozzon létre egy PageDNA tesztfelhasználót](#create-a-pagedna-test-user)**  úgy, hogy a felhasználó a PageDNA elnevezett olyan Britta Simon kapcsolódik az Azure AD-felhasználó, aki nevű Britta Simon.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés PageDNA, tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **PageDNA** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán válassza **szerkesztése** (a ceruza ikon) nyissa meg a **alapszintű SAML-konfigurációja** ablaktáblán.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** ablaktáblán tegye a következőket:

    ![PageDNA domain and URLs single sign-on information](common/sp-identifier.png)

    1. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minták egyikét használva:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. Az a **azonosító (entityid)** mezőbe írjon be egy URL-címet a következő minták egyikét használva:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Ezek az értékek nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és azonosító. Ezek az értékek beszerzéséhez forduljon a [PageDNA támogatási csapatának](mailto:success@pagedna.com). Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** panel az Azure Portalon.

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán, a a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez **(Raw)tanúsítvány** a megadott lehetőségek közül, és mentse el a számítógépen.

    ![A tanúsítvány (nyers) letöltési lehetőséget](common/certificateraw.png)

1. Az a **PageDNA beállítása** területén másolja az URL-címe vagy URL-címeket, amelyekre szüksége:

   * **Bejelentkezési URL-címe**
   * **Azure AD Identifier**
   * **Kijelentkezési URL-címe**

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>PageDNA egyszeri bejelentkezés konfigurálása

PageDNA oldalán konfigurálása egyszeri bejelentkezéshez, küldjön a letöltött tanúsítvány (Raw) és a megfelelő másolt URL-címeket, az Azure Portalról a [PageDNA támogatási csapatának](mailto:success@pagedna.com). A PageDNA csapat fogja győződjön meg arról, a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**   > **felhasználók** > **minden felhasználó**.

    ![A felhasználók és a "Minden felhasználó" lehetőség](common/users.png)

1. Válassza ki a képernyő tetején lévő **+ új felhasználó**.

    ![Új felhasználói beállítás](common/new-user.png)

1. Az a **felhasználói** ablaktáblán tegye a következőket:

    ![A felhasználói panelen](common/user-properties.png)

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **BrittaSimon\@\<vállalati_tartomány >.\< bővítmény >**. Ha például **BrittaSimon\@contoso.com**.

    1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés PageDNA saját hozzáférésének engedélyezésére használja.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **PageDNA**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Az alkalmazások listájában jelölje ki a **PageDNA**.

    ![Az alkalmazások listáját a PageDNA](common/all-applications.png)

1. A bal oldali panelen alatt **kezelés**válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" lehetőséget](common/users-groups-blade.png)

1. Válassza ki **+ Hozzáadás felhasználó**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** ablaktáblán.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** panelen válassza **Britta Simon** a a **felhasználók** listában, és válassza a **kiválasztása** a panel alján.

1. Ha valakitől egy szerepkör értéket a SAML helyességi feltétel, majd a a **Szerepkörválasztás** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz a listából. A panel alján válassza **kiválasztása**.

1. Az a **hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-a-pagedna-test-user"></a>PageDNA tesztfelhasználó létrehozása

Britta Simon nevű felhasználót a PageDNA megtörtént. Nem kell tennie semmit, a felhasználó létrehozásához. PageDNA támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Britta Simon nevű felhasználó már nem létezik az PageDNA, ha egy új jön létre a hitelesítés után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban a az Azure AD egyszeri bejelentkezés beállításai a saját alkalmazások portál segítségével tesztelnie.

Ha bejelöli **PageDNA** a saját alkalmazások portál meg kell hogy automatikusan jelentkezzenek be, amelynek beállítása egyszeri bejelentkezéshez PageDNA az előfizetéshez. A saját alkalmazások portál kapcsolatos további információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TurboRater |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és TurboRater között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 1e67f3c7f9413382d169837eaba50d6af18552e6
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956672"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TurboRater

Ebben az oktatóanyagban elsajátíthatja, hogyan TurboRater integrálása az Azure Active Directory (Azure AD).

TurboRater integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá TurboRater Azure AD-ben.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be TurboRater (egyszeri bejelentkezés), az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

További információk az Azure AD-szoftverként (saas biztosított) alkalmazás integrációja: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

TurboRater az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Az egyszeri bejelentkezés engedélyezve TurboRater előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

TurboRater támogat IDP által kezdeményezett egyszeri bejelentkezés (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Az Azure Marketplace-ről TurboRater hozzáadása

Az Azure AD integrálása a TurboRater konfigurálásához hozzá kell TurboRater az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listájára:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com?azure-portal=true).
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directoryval opciót.](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások csomag](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza **+ új alkalmazás** a panel tetején.

    ![Az új alkalmazás-beállítás](common/add-new-app.png)

1. A Keresés mezőbe írja be a **TurboRater**. A keresési eredmények között, válassza ki a **TurboRater**, majd válassza ki **Hozzáadás** , vegye fel az alkalmazást.

    ![Az eredmények listájában TurboRater](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapján TurboRater **B Simon**. Az egyszeri bejelentkezés működjön a TurboRater a kell létesítenie az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat.

Az Azure AD egyszeri bejelentkezés az TurboRater tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[TurboRater egyszeri bejelentkezés konfigurálása](#configure-turborater-single-sign-on)**  az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés a b Simon teszteléséhez.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  b Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Hozzon létre egy TurboRater tesztfelhasználót](#create-a-turborater-test-user)**  úgy, hogy a felhasználó a TurboRater elnevezett olyan b Simon kapcsolódik az Azure AD-felhasználó, aki nevű b Simon.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés TurboRater, tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **TurboRater** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon jelölje be **szerkesztése** (a ceruza ikon) nyissa meg a **alapszintű SAML-konfigurációja** ablaktáblán.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** ablaktáblán tegye a következőket:

    ![TurboRater tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    1. Az a **azonosító (entityid)** mezőbe írjon be egy URL-címe:

       `https://www.itcdataservices.com`

    1. Az a **válasz URL-cím (helyességi feltétel fogyasztói szolgáltatás URL-címe)** mezőbe írjon be egy URL-címet a következő minta használatával:

       | Környezet | URL |
       | ---------------| --------------- |
       | Tesztelés  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Élő  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Ezek az értékek nem valódi. Az értékeket módosítsa a tényleges azonosítóval, és a válasz URL-címe. Ezek az értékek beszerzéséhez forduljon a [TurboRater támogatási csapatának](https://www.getitc.com/support). Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** panel az Azure Portalon.

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán, a a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül, és mentse el a számítógépen.

    ![Az összevonási metaadatok XML a letöltési lehetőséget](common/metadataxml.png)

1. Az a **TurboRater beállítása** területén másolja az URL-címe vagy URL-címeket, amelyekre szüksége:

   * **Bejelentkezési URL-címe**
   * **Azure AD Identifier**
   * **Kijelentkezési URL-címe**

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>TurboRater egyszeri bejelentkezés konfigurálása

A TurboRater oldalon konfigurálása egyszeri bejelentkezéshez, kell küldenie a letöltött összevonási metaadatainak XML és a megfelelő másolt URL-címek az Azure Portalon a [TurboRater támogatási csapatának](https://www.getitc.com/support). A TurboRater csapat fogja győződjön meg arról, a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**   > **felhasználók** > **minden felhasználó**.

    ![A felhasználók és a "Minden felhasználó" lehetőség](common/users.png)

1. Válassza ki a képernyő tetején lévő **+ új felhasználó**.

    ![Új felhasználói beállítás](common/new-user.png)

1. Az a **felhasználói** ablaktáblán tegye a következőket:

    ![A felhasználói panelen](common/user-properties.png)

    1. Az a **neve** mezőbe írja be **BSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **BSimon\@\<vállalati_tartomány >.\< bővítmény >**. Ha például **BSimon\@contoso.com**.

    1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze b Simon által biztosított a hozzáférés TurboRater Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **TurboRater**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Az alkalmazások listájában jelölje ki a **TurboRater**.

    ![Az alkalmazások listáját a TurboRater](common/all-applications.png)

1. A bal oldali panelen alatt **kezelés**válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" lehetőséget](common/users-groups-blade.png)

1. Válassza ki **+ Hozzáadás felhasználó**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** ablaktáblán.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** panelen válassza **b Simon** a a **felhasználók** listában, és válassza a **kiválasztása** a panel alján.

1. Ha valakitől egy szerepkör értéket a SAML helyességi feltétel, majd a a **Szerepkörválasztás** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz a listából. A panel alján válassza **kiválasztása**.

1. Az a **hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-a-turborater-test-user"></a>TurboRater tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói b Simon nevű TurboRater hoz létre. Együttműködik a [TurboRater támogatási csapatának](https://www.getitc.com/support) b Simon TurboRater felhasználóként hozzáadni. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban a az Azure AD egyszeri bejelentkezés beállításai a saját alkalmazások portál segítségével tesztelnie.

Ha bejelöli **TurboRater** a saját alkalmazások portál meg kell hogy automatikusan jelentkezzenek be, amelynek beállítása egyszeri bejelentkezéshez TurboRater az előfizetéshez. A saját alkalmazások portál kapcsolatos további információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Uberflip |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Uberflip között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 69e86e486a9cdb058b972bda5176c14e15f4630a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682727"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Uberflip

Ebben az oktatóanyagban elsajátíthatja, hogyan Uberflip integrálása az Azure Active Directory (Azure AD).

Uberflip integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Uberflip Azure AD-ben.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be Uberflip (egyszeri bejelentkezés), az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

További információk az Azure AD-szoftverként (saas biztosított) alkalmazás integrációja: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Uberflip az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Az egyszeri bejelentkezés engedélyezve Uberflip előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

Uberflip támogatja a következő funkciókat:

* SP által kezdeményezett, és az Identitásszolgáltató által kezdeményezett egyszeri bejelentkezés (SSO).
* Just-in-time-felhasználók létrehozásának.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Az Azure Marketplace-ről Uberflip hozzáadása

Az Azure AD integrálása a Uberflip konfigurálásához hozzá kell Uberflip az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listájára:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

   ![Az Azure Active Directoryval opciót.](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

   ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza **+ új alkalmazás** a panel tetején.

   ![Az új alkalmazás-beállítás](common/add-new-app.png)

1. A Keresés mezőbe írja be a **Uberflip**. A keresési eredmények között, válassza ki a **Uberflip**, majd válassza ki **Hozzáadás** , vegye fel az alkalmazást.

   ![Az eredmények listájában Uberflip](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapján Uberflip **Britta Simon**. Az egyszeri bejelentkezés működéséhez Azure AD-felhasználót és a egy kapcsolódó felhasználó közötti kapcsolatot létesít a Uberflip kell.

Az Azure AD egyszeri bejelentkezés az Uberflip tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Uberflip egyszeri bejelentkezés konfigurálása](#configure-uberflip-single-sign-on)**  az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Hozzon létre egy Uberflip tesztfelhasználót](#create-an-uberflip-test-user)**  úgy, hogy a felhasználó a Uberflip elnevezett olyan Britta Simon kapcsolódik az Azure AD-felhasználó, aki nevű Britta Simon.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Uberflip, tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Uberflip** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán válassza **szerkesztése** (a ceruza ikon) nyissa meg a **alapszintű SAML-konfigurációja** ablaktáblán.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** ablaktáblán, tegye a következőket, attól függően, melyik egyszeri bejelentkezési mód a konfigurálni kívánt egyikét:

   * Az Identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést a módban az alkalmazás konfigurálása a **válasz URL-cím (helyességi feltétel fogyasztói szolgáltatás URL-címe)** mezőbe írjon be egy URL-címet a következő minta használatával:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-replyurl.png)

     > [!NOTE]
     > Ez az érték nem valódi. Ez az érték frissítse a tényleges válasz URL-cím. A tényleges érték beolvasása, lépjen kapcsolatba a [Uberflip támogatási csapatának](mailto:support@uberflip.com). Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** panel az Azure Portalon.

   * Az alkalmazás konfigurálása a Szolgáltató által kezdeményezett egyszeri bejelentkezési módot, válassza ki a **további URL-címet beállítani**, majd a a **bejelentkezési URL-** mezőbe írja be az URL-cím:

     `https://app.uberflip.com/users/login`

     ![Uberflip tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-signonurl.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán, a a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül, és mentse el a számítógépen.

   ![Az összevonási metaadatok XML a letöltési lehetőséget](common/metadataxml.png)

1. Az a **Uberflip beállítása** panelen másolja az URL-címe vagy URL-CÍMEKET, amelyekre szüksége:

   * **Bejelentkezési URL-címe**
   * **Azure AD Identifier**
   * **Kijelentkezési URL-címe**

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Uberflip egyszeri bejelentkezés konfigurálása

A Uberflip oldalon konfigurálása egyszeri bejelentkezéshez, kell küldenie a letöltött összevonási metaadatainak XML és a megfelelő másolt URL-címek az Azure Portalon a [Uberflip támogatási csapatának](mailto:support@uberflip.com). A Uberflip csapat fogja győződjön meg arról, a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

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

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés Uberflip saját hozzáférésének engedélyezésére használja.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **Uberflip**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Az alkalmazások listájában jelölje ki a **Uberflip**.

    ![Az alkalmazások listáját a Uberflip](common/all-applications.png)

1. A bal oldali panelen alatt **kezelés**válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" lehetőséget](common/users-groups-blade.png)

1. Válassza ki **+ Hozzáadás felhasználó**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** ablaktáblán.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** panelen válassza **Britta Simon** a a **felhasználók** listában, és válassza a **kiválasztása** a panel alján.

1. Ha valakitől egy szerepkör értéket a SAML helyességi feltétel, majd a a **Szerepkörválasztás** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz a listából. A panel alján válassza **kiválasztása**.

1. Az a **hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-an-uberflip-test-user"></a>Hozzon létre egy Uberflip tesztfelhasználó számára

Britta Simon nevű felhasználót a Uberflip megtörtént. Nem kell tennie semmit, a felhasználó létrehozásához. Uberflip támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Britta Simon nevű felhasználó már nem létezik az Uberflip, ha egy új jön létre a hitelesítés után.

> [!NOTE]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, lépjen kapcsolatba a [Uberflip támogatási csapatának](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban a az Azure AD egyszeri bejelentkezés beállításai a saját alkalmazások portál segítségével tesztelnie.

Ha bejelöli **Uberflip** a saját alkalmazások portál meg kell hogy automatikusan jelentkezzenek be, amelynek beállítása egyszeri bejelentkezéshez Uberflip az előfizetéshez. A saját alkalmazások portál kapcsolatos további információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
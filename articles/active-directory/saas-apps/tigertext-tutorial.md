---
title: 'Oktatóanyag: Az Azure Active Directory-integráció TigerText biztonságos Messenger |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés TigerText biztonságos Messenger és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 840b1fe55556cfd853e0928164891d6b21b17cc2
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956862"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Oktatóanyag: Az Azure Active Directory-integráció TigerText biztonságos Messenger

Ebben az oktatóanyagban elsajátíthatja, hogyan TigerText biztonságos Messenger integrálása az Azure Active Directory (Azure AD).

TigerText biztonságos Messenger integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá TigerText biztonságos Messenger Azure AD-ben.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be TigerText biztonságos Messenger (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

További információk az Azure AD-szoftverként (saas biztosított) alkalmazás integrációja: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása TigerText biztonságos Messenger, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Az egyszeri bejelentkezés engedélyezve TigerText biztonságos Messenger-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálni és a egy tesztkörnyezetben az Azure AD egyszeri bejelentkezés tesztelése és TigerText biztonságos Messenger integrálása az Azure ad-ben.

Biztonságos Messenger TigerText támogatja az SP által kezdeményezett egyszeri bejelentkezés (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Az Azure Marketplace-ről TigerText biztonságos Messenger hozzáadása

Az Azure AD-be TigerText biztonságos Messenger-integráció konfigurálásához, hozzá kell TigerText biztonságos Messenger az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com?azure-portal=true).
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directoryval opciót.](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza **+ új alkalmazás** a panel tetején.

    ![Az új alkalmazás-beállítás](common/add-new-app.png)

1. A Keresés mezőbe írja be a **TigerText biztonságos Messenger**. A keresési eredmények között, válassza ki a **TigerText biztonságos Messenger**, majd válassza ki **Hozzáadás** , vegye fel az alkalmazást.

    ![TigerText biztonságos Messenger, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó TigerText biztonságos Messenger **Britta Simon**. Az egyszeri bejelentkezés működjön a TigerText biztonságos Messenger kell létesítenie az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat.

Az Azure AD egyszeri bejelentkezés TigerText biztonságos Messenger tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Biztonságos Messenger TigerText egyszeri bejelentkezés konfigurálása](#configure-tigertext-secure-messenger-single-sign-on)**  az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Hozzon létre egy biztonságos Messenger TigerText tesztfelhasználót](#create-a-tigertext-secure-messenger-test-user)**  , hogy egy felhasználó TigerText biztonságos Messenger elnevezett olyan Britta Simon kapcsolódik az Azure AD-felhasználó, aki nevű Britta Simon.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés TigerText biztonságos Messenger, a következő lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **TigerText biztonságos Messenger** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán válassza **szerkesztése** (a ceruza ikon) nyissa meg a **alapszintű SAML-konfigurációja** ablaktáblán.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** ablaktáblán tegye a következőket:

    ![Biztonságos Messenger TigerText tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    1. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címe:

       `https://home.tigertext.com`

    1. Az a **azonosító (entityid)** mezőbe írja be egy URL-címet a következő minta használatával:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > A **azonosító (entityid)** érték nem valódi. Ez az érték frissítse a tényleges azonosítója. Az érték beszerzéséhez forduljon a [TigerText biztonságos Messenger támogatási csapatának](mailto:prosupport@tigertext.com). Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** panel az Azure Portalon.

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán, a a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül, és mentse el a számítógépen.

    ![Az összevonási metaadatok XML a letöltési lehetőséget](common/metadataxml.png)

1. Az a **TigerText biztonságos Messenger beállítása** területén másolja az URL-címe vagy URL-címeket, amelyekre szüksége:

   * **Bejelentkezési URL-címe**
   * **Azure AD Identifier**
   * **Kijelentkezési URL-címe**

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Biztonságos Messenger TigerText egyszeri bejelentkezés konfigurálása

TigerText biztonságos Messenger oldalán konfigurálása egyszeri bejelentkezéshez, kell küldenie a letöltött összevonási metaadatainak XML és a megfelelő másolt URL-címek az Azure Portalon a [TigerText biztonságos Messenger támogatási csapatának](mailto:prosupport@tigertext.com). A biztonságos Messenger TigerText csapat fogja győződjön meg arról, a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

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

Ebben a szakaszban engedélyezze Britta Simon TigerText biztonságos Messenger, a hozzáférés biztosításával őket Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **TigerText biztonságos Messenger**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Az alkalmazások listájában jelölje ki a **TigerText biztonságos Messenger**.

    ![Biztonságos Messenger TigerText az alkalmazások listáját](common/all-applications.png)

1. A bal oldali panelen alatt **kezelés**válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" lehetőséget](common/users-groups-blade.png)

1. Válassza ki **+ Hozzáadás felhasználó**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** ablaktáblán.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** panelen válassza **Britta Simon** a a **felhasználók** listában, és válassza a **kiválasztása** a panel alján.

1. Ha valakitől egy szerepkör értéket a SAML helyességi feltétel, majd a a **Szerepkörválasztás** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz a listából. A panel alján válassza **kiválasztása**.

1. Az a **hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Biztonságos Messenger TigerText tesztfelhasználó létrehozása

Ebben a szakaszban egy TigerText biztonságos Messenger Britta Simon nevű felhasználó hoz létre. Együttműködik a [TigerText biztonságos Messenger támogatási csapatának](mailto:prosupport@tigertext.com) Britta Simon TigerText biztonságos Messenger felhasználóként hozzáadni. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban a az Azure AD egyszeri bejelentkezés beállításai a saját alkalmazások portál segítségével tesztelnie.

Ha bejelöli **TigerText biztonságos Messenger** a saját alkalmazások portál meg kell hogy automatikusan jelentkezzenek be, amelynek beállítása egyszeri bejelentkezéshez TigerText biztonságos Messenger az előfizetéshez. A saját alkalmazások portál kapcsolatos további információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directory SaaS-alkalmazások integrálását ismertető oktatóanyagok listáját](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

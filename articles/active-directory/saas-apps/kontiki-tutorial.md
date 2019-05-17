---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Kontiki |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Kontiki között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee7454942b9214eeb1253339446df370e20fe01
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785839"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Kontiki

Ebben az oktatóanyagban elsajátíthatja, hogyan Kontiki integrálása az Azure Active Directory (Azure AD).

Kontiki integrálása az Azure AD kínál fel a következő előnyökkel jár:

* Az Azure AD, hogy ki férhet hozzá Kontiki is használhatja.
* Felhasználók is automatikusan megtörténik a Kontiki az Azure ad-ben fiókjukkal (egyszeri bejelentkezés).
* A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

Az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Kontiki az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik Azure AD előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Az egyszeri bejelentkezés engedélyezve Kontiki előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálni és a egy tesztkörnyezetben az Azure AD egyszeri bejelentkezés tesztelése és Kontiki integrálása az Azure ad-ben.

Kontiki támogatja a következő funkciókat:

* **SP által kezdeményezett egyszeri bejelentkezés**
* **Felhasználókiépítés igény**

## <a name="add-kontiki-in-the-azure-portal"></a>Az Azure Portalon Kontiki hozzáadása

Kontiki integrálható az Azure ad-vel, hozzá kell adnia Kontiki a felügyelt SaaS-alkalmazások listájában.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directoryval opciót.](common/select-azuread.png)

1. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Egy alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az új alkalmazás-beállítás](common/add-new-app.png)

1. A Keresés mezőbe írja be a **Kontiki**. A keresési eredmények között, válassza ki a **Kontiki**, majd válassza ki **Hozzáadás**.

    ![Az eredmények listájában Kontiki](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapján Kontiki **Britta Simon**. Az egyszeri bejelentkezés működéséhez ki kell alakítani a társított kapcsolatot egy Azure AD-felhasználót és a kapcsolódó felhasználó között Kontiki.

Az Azure AD egyszeri bejelentkezés az Kontiki tesztelése és konfigurálása, hogy a következő építőelemeit kell elvégeznie:

| Feladat | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók a funkció használatához. |
| **[Kontiki egyszeri bejelentkezés konfigurálása](#configure-kontiki-single-sign-on)** | Az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazásban. |
| **[Hozzon létre egy Azure ad-ben tesztfelhasználó számára](#create-an-azure-ad-test-user)** | Tesztek az Azure AD egyszeri bejelentkezés egy felhasználó nevű Britta Simon. |
| **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assign-the-azure-ad-test-user)** | Britta Simon az Azure AD egyszeri bejelentkezés használata lehetővé teszi. |
| **[Kontiki tesztfelhasználó létrehozása](#create-a-kontiki-test-user)** | Létrehoz egy megfelelője a Britta Simon Kontiki, amely a felhasználó Azure ad-ben reprezentációja van csatolva. |
| **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy működik-e a konfiguráció. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban konfigurálnia az Azure AD egyszeri bejelentkezés Kontiki az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Kontiki** application integration ablaktáblában válassza **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML** vagy **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán válassza **szerkesztése** (a ceruza ikon) nyissa meg a **alapszintű SAML-konfigurációja** ablaktáblán.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** ablaktáblán, a a **bejelentkezési URL-cím** szöveg mezőbe írjon be egy URL-címet, amely rendelkezik a következő mintának: `https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-signonurl.png)

    > [!NOTE]
    > Forduljon a [Kontiki ügyfél-támogatási csapatának](https://customersupport.kontiki.com/enterprise/contactsupport.html) lekérni, használja a megfelelő értéket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán, a a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** melletti **összevonási metaadatainak XML**. Válassza ki a letöltési lehetőséget igényei alapján. Mentse a tanúsítványt a számítógépen.

    ![Az összevonási metaadatok XML tanúsítványt a letöltési lehetőséget](common/metadataxml.png)

1. Az a **Kontiki beállítása** területén másolja a következő URL-címek igényei alapján:

    * Bejelentkezési URL
    * Azure AD-azonosító
    * Kijelentkezési URL

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Kontiki egyszeri bejelentkezés konfigurálása

A Kontiki oldalon konfigurálása egyszeri bejelentkezéshez, a letöltött összevonási metaadatainak XML-fájlt, és a megfelelő URL-címeket, az Azure Portalról másolt küldése a [Kontiki támogatási csapatának](https://customersupport.kontiki.com/enterprise/contactsupport.html). A Kontiki csapatával küldheti el nekik, győződjön meg arról, hogy a SAML egyszeri bejelentkezési kapcsolatot helyesen van beállítva mindkét oldalon az információkat használja.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

    ![A felhasználók és az összes felhasználói beállítások](common/users.png)

1. Válassza ki **új felhasználó**.

    ![Az új felhasználói beállítás](common/new-user.png)

1. Az a **felhasználói** panelen a következő lépéseket:

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **brittasimon\@\<a vállalati tartomány >.\< bővítmény >**. Ha például **brittasimon\@contoso.com**.

    1. Válassza ki a **Show jelszó** jelölőnégyzetet. Írja le az értéket, a megjelenő a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

    ![A felhasználói panelen](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést adhat a Britta Simon Kontiki így ő Azure egyszeri bejelentkezéshez használható.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **Kontiki**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Az alkalmazások listájában jelölje ki a **Kontiki**.

    ![Az alkalmazások listáját a Kontiki](common/all-applications.png)

1. Válassza a menüben **felhasználók és csoportok**.

    ![A felhasználók és csoportok lehetőség](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**. Ezt követően a a **-hozzárendelés hozzáadása** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A Hozzáadás hozzárendelési ablaktáblán](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** ablaktáblán válassza **Britta Simon** felhasználók listájában. Válassza a **Kiválasztás** lehetőséget

1. Ha az a SAML-előfeltétel szerepkör értéket várt a **szerepkör kiválasztása** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz a listából. Válassza a **Kiválasztás** lehetőséget

1. Az a **hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-a-kontiki-test-user"></a>Kontiki tesztfelhasználó létrehozása

Nincs művelet elem Kontiki a felhasználókiépítés konfigurálása. Ha egy hozzárendelt felhasználó próbál Kontiki jelentkezzen be a saját alkalmazások portál használatával, Kontiki ellenőrzi, hogy létezik-e a felhasználó. Ha nem talált a felhasználói fiókot, Kontiki automatikusan létrehozza a felhasználói fiókot.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban a az Azure AD egyszeri bejelentkezés beállításai a saját alkalmazások portál segítségével tesztelnie.

Miután beállította egyszeri bejelentkezést, amikor kiválaszt **Kontiki** a saját alkalmazások portál automatikusan bejelentkezett a Kontiki. A saját alkalmazások portál kapcsolatos további információkért lásd: [használatának és elérésének alkalmazásokat a saját alkalmazások portál](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [Az Azure Active Directory SaaS-alkalmazások integrálását ismertető oktatóanyagok listáját](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

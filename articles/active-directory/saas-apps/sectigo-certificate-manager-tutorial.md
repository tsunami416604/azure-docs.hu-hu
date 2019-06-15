---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Sectigo tanúsítványkezelővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Tanúsítványkezelő Sectigo között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 15-04-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d7c7cf4972b1ee0a5add3b4611dc4c8655da875
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091535"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Oktatóanyag: Az Azure Active Directory-integráció Sectigo tanúsítványkezelővel

Ebben az oktatóanyagban elsajátíthatja, hogyan Sectigo Certificate Manager integrálása az Azure Active Directory (Azure AD).

Sectigo Certificate Manager integrálása az Azure AD kínál fel a következő előnyökkel jár:

* Használhatja az Azure AD-szabályozza, ki férhet Sectigo Certificate Manager.
* Felhasználók is automatikusan megtörténik a Sectigo Certificate Manager a saját Azure AD-fiókok (egyszeri bejelentkezés).
* A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

Az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Sectigo tanúsítványkezelővel, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik Azure AD előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Egyszeri bejelentkezés engedélyezve van az előfizetés Sectigo Certificate Manager.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálni és a egy tesztkörnyezetben az Azure AD egyszeri bejelentkezés tesztelése és Sectigo Certificate Manager integrálása az Azure ad-ben.

Sectigo Certificate Manager támogatja a következő funkciókat:

* **SP által kezdeményezett egyszeri bejelentkezés**
* **Identitásszolgáltató által kezdeményezett egyszeri bejelentkezés**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>A Tanúsítványkezelő Sectigo hozzáadása az Azure Portalon

Sectigo Certificate Manager integrálása az Azure ad-ben, hozzá kell adnia a Tanúsítványkezelő Sectigo a felügyelt SaaS-alkalmazások listájában.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directoryval opciót.](common/select-azuread.png)

1. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Egy alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az új alkalmazás-beállítás](common/add-new-app.png)

1. A Keresés mezőbe írja be a **Sectigo Tanúsítványkezelő**. A keresési eredmények között, válassza ki a **Sectigo Tanúsítványkezelő**, majd válassza ki **Hozzáadás**.

    ![Az eredmények listájában Sectigo Tanúsítványkezelő](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés Sectigo tanúsítványkezelővel nevű tesztfelhasználó alapján **Britta Simon**. Az egyszeri bejelentkezés működéséhez, kapcsolatot kell létesítenie egy társított Azure AD-felhasználót és a kapcsolódó felhasználó közötti Sectigo Certificate Manager.

Az Azure AD egyszeri bejelentkezés Sectigo tanúsítványkezelővel tesztelése és konfigurálása, hogy a következő építőelemeit kell elvégeznie:

| Tevékenység | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók a funkció használatához. |
| **[Tanúsítványkezelő Sectigo egyszeri bejelentkezés konfigurálása](#configure-sectigo-certificate-manager-single-sign-on)** | Az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazásban. |
| **[Hozzon létre egy Azure ad-ben tesztfelhasználó számára](#create-an-azure-ad-test-user)** | Tesztek az Azure AD egyszeri bejelentkezés egy felhasználó nevű Britta Simon. |
| **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assign-the-azure-ad-test-user)** | Britta Simon az Azure AD egyszeri bejelentkezés használata lehetővé teszi. |
| **[A Tanúsítványkezelő Sectigo tesztfelhasználó létrehozása](#create-a-sectigo-certificate-manager-test-user)** | Létrehoz egy megfelelője a Britta Simon Sectigo tanúsítvány Managerben, amely kapcsolódik a felhasználó Azure ad-ben ábrázolása. |
| **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy működik-e a konfiguráció. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban konfigurálja az Azure AD egyszeri bejelentkezés Sectigo tanúsítványkezelővel az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Sectigo Tanúsítványkezelő** application integration ablaktáblában válassza **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML** vagy **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán válassza **szerkesztése** (a ceruza ikon) nyissa meg a **alapszintű SAML-konfigurációja** ablaktáblán.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** ablaktáblán konfigurálása *Identitásszolgáltató által kezdeményezett mód*, kövesse az alábbi lépéseket:

    1. Az a **azonosító** mezőbe írja be az alábbi URL-címek valamelyikét:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Az a **válasz URL-cím** mezőbe írja be az alábbi URL-címek valamelyikét:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Válassza ki **további URL-címet beállítani**.

    1. Az a **továbbítási állapot** mezőbe írja be az alábbi URL-címek valamelyikét:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![A Tanúsítványkezelő Sectigo tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-relay.png)

1.  Az alkalmazás konfigurálása *SP által kezdeményezett mód*, kövesse az alábbi lépéseket:

    * Az a **bejelentkezési URL-cím** mezőbe írja be az alábbi URL-címek valamelyikét:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![A Tanúsítványkezelő Sectigo tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-signonurl.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán, a a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** melletti **tanúsítvány (Base64)** . Válassza ki a letöltési lehetőséget igényei alapján. Mentse a tanúsítványt a számítógépen.

    ![A tanúsítvány (Base64) a letöltési lehetőséget](common/certificatebase64.png)

1. Az a **állítsa be a Sectigo tanúsítvány Managert** területén másolja a következő URL-címek igényei alapján:

    * Bejelentkezési URL
    * Azure AD-azonosító
    * Kijelentkezési URL

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Tanúsítványkezelő Sectigo egyszeri bejelentkezés konfigurálása

A Tanúsítványkezelő Sectigo oldalon konfigurálása egyszeri bejelentkezéshez, a letöltött tanúsítvány (Base64) fájlt, és a megfelelő URL-címeket, az Azure Portalról másolt küldése a [Sectigo tanúsítvány Manager támogatási munkacsoportjának](https://sectigo.com/support). A Tanúsítványkezelő Sectigo csapatával küldheti el nekik, győződjön meg arról, hogy a SAML egyszeri bejelentkezési kapcsolatot helyesen van beállítva mindkét oldalon az információkat használja.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

    ![A felhasználók és az összes felhasználói beállítások](common/users.png)

1. Válassza ki **új felhasználó**.

    ![Az új felhasználói beállítás](common/new-user.png)

1. Az a **felhasználói** panelen a következő lépéseket:

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **brittasimon\@\<a vállalati tartomány >.\< bővítmény\>** . Ha például **brittasimon\@contoso.com**.

    1. Válassza ki a **Show jelszó** jelölőnégyzetet. Írja le az értéket, a megjelenő a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

    ![A felhasználói panelen](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést Britta Simon Sectigo Tanúsítványkezelő, így ő Azure egyszeri bejelentkezéshez használható.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **Sectigo Tanúsítványkezelő**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Az alkalmazások listájában jelölje ki a **Sectigo Tanúsítványkezelő**.

    ![Tanúsítványkezelő Sectigo alkalmazásainak listájában](common/all-applications.png)

1. Válassza a menüben **felhasználók és csoportok**.

    ![A felhasználók és csoportok lehetőség](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**. Ezt követően a a **-hozzárendelés hozzáadása** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A Hozzáadás hozzárendelési ablaktáblán](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** ablaktáblán válassza **Britta Simon** felhasználók listájában. Válassza a **Kiválasztás** lehetőséget

1. Ha az a SAML-előfeltétel szerepkör értéket várt a **szerepkör kiválasztása** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz a listából. Válassza a **Kiválasztás** lehetőséget

1. Az a **hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>A Tanúsítványkezelő Sectigo tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon Sectigo Certificate Manager nevű felhasználót hoz létre. Együttműködik a [Sectigo tanúsítvány Manager támogatási munkacsoportjának](https://sectigo.com/support) a felhasználó hozzáadása a Tanúsítványkezelő Sectigo platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban a az Azure AD egyszeri bejelentkezés beállításai a saját alkalmazások portál segítségével tesztelnie.

Miután beállította egyszeri bejelentkezést, amikor kiválaszt **Sectigo Tanúsítványkezelő** a saját alkalmazások portál automatikusan bejelentkezett Sectigo Certificate Manager. A saját alkalmazások portál kapcsolatos további információkért lásd: [használatának és elérésének alkalmazásokat a saját alkalmazások portál](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [Az Azure Active Directory SaaS-alkalmazások integrálását ismertető oktatóanyagok listáját](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)



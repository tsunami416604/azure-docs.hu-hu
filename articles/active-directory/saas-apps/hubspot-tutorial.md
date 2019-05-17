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
ms.openlocfilehash: 3d74cc1665867568032bb1343e4f2c26c50fe15a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65770189"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Oktatóanyag: HubSpot az Azure Active Directory-integráció

Ebben az oktatóanyagban megismerheti, hogyan történő HubSpot integrálása az Azure Active Directory (Azure AD).

HubSpot integrálása az Azure AD kínál fel a következő előnyökkel jár:

* Az Azure AD-szabályozza, ki férhet hozzá HubSpot is használhatja.
* Felhasználók is automatikusan megtörténik a történő HubSpot (egyszeri bejelentkezés) az Azure AD fiókjukkal.
* A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

Az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Történő HubSpot az Azure AD-integráció konfigurálása, szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik Azure AD előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* HubSpot előfizetés az egyszeri bejelentkezés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálni és a egy tesztkörnyezetben az Azure AD egyszeri bejelentkezés tesztelése és HubSpot integrálása az Azure ad-ben.

HubSpot támogatja a következő funkciókat:

* **SP által kezdeményezett egyszeri bejelentkezés**
* **Identitásszolgáltató által kezdeményezett egyszeri bejelentkezés**

## <a name="add-hubspot-in-the-azure-portal"></a>HubSpot hozzáadása az Azure Portalon

Történő HubSpot integrálása az Azure ad-ben, hozzá kell adnia HubSpot a felügyelt SaaS-alkalmazások listájában.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directoryval opciót.](common/select-azuread.png)

1. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Egy alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az új alkalmazás-beállítás](common/add-new-app.png)

1. A Keresés mezőbe írja be a **HubSpot**. A keresési eredmények között, válassza ki a **HubSpot**, majd válassza ki **Hozzáadás**.

    ![HubSpot a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapján HubSpot **Britta Simon**. Az egyszeri bejelentkezés működéséhez ki kell alakítani a társított kapcsolatot egy Azure AD-felhasználót és a kapcsolódó felhasználó között HubSpot.

Az Azure AD egyszeri bejelentkezés az HubSpot tesztelése és konfigurálása, hogy a következő építőelemeit kell elvégeznie:

| Feladat | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók a funkció használatához. |
| **[HubSpot egyszeri bejelentkezés konfigurálása](#configure-hubspot-single-sign-on)** | Az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazásban. |
| **[Hozzon létre egy Azure ad-ben tesztfelhasználó számára](#create-an-azure-ad-test-user)** | Tesztek az Azure AD egyszeri bejelentkezés egy felhasználó nevű Britta Simon. |
| **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assign-the-azure-ad-test-user)** | Britta Simon az Azure AD egyszeri bejelentkezés használata lehetővé teszi. |
| **[HubSpot tesztfelhasználó létrehozása](#create-a-hubspot-test-user)** | Létrehoz egy megfelelője a Britta Simon HubSpot, amely a felhasználó Azure ad-ben reprezentációja van csatolva. |
| **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy működik-e a konfiguráció. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban konfigurálnia az Azure AD egyszeri bejelentkezés HubSpot az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **HubSpot** application integration ablaktáblában válassza **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML** vagy **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán válassza **szerkesztése** (a ceruza ikon) nyissa meg a **alapszintű SAML-konfigurációja** ablaktáblán.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** ablaktáblán konfigurálása *Identitásszolgáltató által kezdeményezett mód*, kövesse az alábbi lépéseket:

    1. Az a **azonosító** mezőbe írjon be egy URL-címet, amely rendelkezik a következő mintának: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<ügyfél-azonosító\>.

    1. Az a **válasz URL-cím** mezőbe írjon be egy URL-címet, amely rendelkezik a következő mintának: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<ügyfél-azonosító\>.

    ![HubSpot tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    > [!NOTE]
    > Az URL-címeket formázásához is tekintse meg a bemutatott minták a **alapszintű SAML-konfigurációja** panel az Azure Portalon.

1. Az alkalmazás konfigurálása *SP által kezdeményezett* mód:

    1. Válassza ki **további URL-címet beállítani**.

    1. Az a **bejelentkezési URL-cím** mezőbe írja be **https:\//app.hubspot.com/login**.

    ![További URL-címek Set kapcsoló](common/metadata-upload-additional-signon.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán, a a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** melletti **tanúsítvány (Base64)**. Válassza ki a letöltési lehetőséget igényei alapján. Mentse a tanúsítványt a számítógépen.

    ![A tanúsítvány (Base64) a letöltési lehetőséget](common/certificatebase64.png)

1. Az a **HubSpot beállítása** területén másolja a következő URL-címek igényei alapján:

    * Bejelentkezési URL
    * Azure AD-azonosító
    * Kijelentkezési URL

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>HubSpot egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be történő HubSpot rendszergazdai fiókjával.

1. Válassza ki a **beállítások** ikonra az oldal jobb felső sarkában.

    ![HubSpot a beállítások ikonra](./media/hubspot-tutorial/config1.png)

1. Válassza ki **alapértelmezett fiók**.

    ![A fiók alapértelmezés szerint lehetőség HubSpot](./media/hubspot-tutorial/config2.png)

1. Görgessen le a **biztonsági** szakaszt, és válassza ki **beállítása**.

    ![HubSpot lehetőség beállítása](./media/hubspot-tutorial/config3.png)

1. Az a **egyszeri bejelentkezés beállítása** területén kövesse az alábbi lépéseket:

    1. Az a **célközönség URL-címe (a Service Provider Entitásazonosító)** jelölje ki **másolási** az érték másolásához. Az Azure Portalon a a **alapszintű SAML-konfigurációja** panelen illessze be az értéket a **azonosító** mezőbe.

    1. Az a **jelentkezzen be az URL-címe, ACS, címzett vagy átirányítási** jelölje ki **másolási** az érték másolásához. Az Azure Portalon a a **alapszintű SAML-konfigurációja** panelen illessze be az értéket a **válasz URL-cím** mezőbe.

    1. HubSpot, az a a **identitás azonosítója vagy a kiállító URL-címe** mezőbe illessze be az értéket a **az Azure AD-azonosító** másolt az Azure Portalon.

    1. HubSpot, az a a **Identity Provider egyszeri bejelentkezési URL-** mezőbe illessze be az értéket a **bejelentkezési URL-cím** másolt az Azure Portalon.

    1. Windows a Jegyzettömb nyissa meg a letöltött Certificate(Base64) fájlt. Válassza ki, és másolja a fájl tartalmát. Ezt követően a HubSpot, illessze be a **X.509-tanúsítvány** mezőbe.

    1. Válassza ki **ellenőrzése**.

        ![Egyszeri bejelentkezés szakaszában HubSpot beállítása](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

    ![A felhasználók és az összes felhasználói beállítások](common/users.png)

1. Válassza ki **új felhasználó**.

    ![Az új felhasználói beállítás](common/new-user.png)

1. Az a **felhasználói** panelen a következő lépéseket:

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **brittasimon\@\<a vállalati tartomány >.\< bővítmény\>**. Ha például **brittasimon\@contoso.com**.

    1. Válassza ki a **Show jelszó** jelölőnégyzetet. Írja le az értéket, a megjelenő a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

    ![A felhasználói panelen](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ez a szakasz hozzáférést biztosít Britta Simon történő HubSpot így ő Azure egyszeri bejelentkezéshez használható.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **HubSpot**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Az alkalmazások listájában jelölje ki a **HubSpot**.

    ![HubSpot alkalmazásainak listájában](common/all-applications.png)

1. Válassza a menüben **felhasználók és csoportok**.

    ![A felhasználók és csoportok lehetőség](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**. Ezt követően a a **-hozzárendelés hozzáadása** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A Hozzáadás hozzárendelési ablaktáblán](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** ablaktáblán válassza **Britta Simon** felhasználók listájában. Válassza a **Kiválasztás** lehetőséget

1. Ha az a SAML-előfeltétel szerepkör értéket várt a **szerepkör kiválasztása** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz a listából. Válassza a **Kiválasztás** lehetőséget

1. Az a **hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-a-hubspot-test-user"></a>HubSpot tesztfelhasználó létrehozása

Ahhoz, hogy az Azure ad-ben történő HubSpot, a felhasználó bejelentkezni egy felhasználó HubSpot ki kell építeni. HubSpot a kiépítés manuális feladat.

Történő HubSpot lévő felhasználói fiók kiépítése:

1. Jelentkezzen be rendszergazdaként a HubSpot vállalati webhely.

1. Válassza ki a **beállítások** ikonra az oldal jobb felső sarkában.

    ![HubSpot a beállítások ikonra](./media/hubspot-tutorial/config1.png)

1. Válassza ki **felhasználók és csoportok**.

    ![A felhasználók és csoportok HubSpot lehetőség](./media/hubspot-tutorial/user1.png)

1. Válassza ki **felhasználó létrehozása**.

    ![HubSpot a felhasználó lehetőség](./media/hubspot-tutorial/user2.png)

1. Az a **hozzáadása e-mail addess(es)** mezőbe írja be a felhasználó e-mail-címét a formátum brittasimon\@contoso.com, és válassza ki **tovább**.

    ![HubSpot létrehozása felhasználók szakaszban mezőben megadott e-mail-címek hozzáadása](./media/hubspot-tutorial/user3.png)

1. Az a **felhasználók létrehozása** területen válassza ki az egyes lapokon. Minden egyes lapon állítsa be a kapcsolódó lehetőségeket és a felhasználó engedélyeit. Ezután válassza a **Tovább** lehetőséget.

    ![Lapok létrehozása című szakaszban a felhasználók a HubSpot](./media/hubspot-tutorial/user4.png)

1. Szeretne küldeni a felhasználónak a meghívót, válassza ki a **küldése**.

    ![A Küldés HubSpot lehetőség](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > A felhasználó aktiválása után a felhasználó elfogadja a meghívást.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban a az Azure AD egyszeri bejelentkezés beállításai a saját alkalmazások portál segítségével tesztelnie.

Miután beállította egyszeri bejelentkezést, amikor kiválaszt **HubSpot** a saját alkalmazások portál automatikusan bejelentkezett történő HubSpot. A saját alkalmazások portál kapcsolatos további információkért lásd: [használatának és elérésének alkalmazásokat a saját alkalmazások portál](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [Az Azure Active Directory SaaS-alkalmazások integrálását ismertető oktatóanyagok listáját](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

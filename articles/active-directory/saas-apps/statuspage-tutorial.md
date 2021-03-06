---
title: 'Oktatóanyag: Azure Active Directory integráció a StatusPage-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és StatusPage között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: b138ab5a67de747480806c19f0d58014c41a1a77
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522053"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Oktatóanyag: Azure Active Directory integráció a StatusPage

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a StatusPage a Azure Active Directory (Azure AD) szolgáltatással.
A StatusPage és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a StatusPage.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a StatusPage (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció StatusPage való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* StatusPage egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A StatusPage támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-statuspage-from-the-gallery"></a>StatusPage hozzáadása a gyűjteményből

A StatusPage Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a StatusPage a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha StatusPage szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **StatusPage**kifejezést, válassza a **StatusPage** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![StatusPage az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az StatusPage-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a StatusPage kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés StatusPage való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[StatusPage egyszeri bejelentkezés konfigurálása](#configure-statuspage-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre StatusPage-teszt felhasználót](#create-statuspage-test-user)** – hogy a StatusPage Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés StatusPage való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **StatusPage** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri Sign-On beállítása az SAML-vel** lapon hajtsa végre a következő lépéseket:

    ![StatusPage tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    - `https://<subdomain>.statuspagestaging.com/`
    - `https://<subdomain>.statuspage.io/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    - `https://<subdomain>.statuspagestaging.com/sso/saml/consume`
    - `https://<subdomain>.statuspage.io/sso/saml/consume`

    > [!NOTE]
    > Az [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) egyszeri bejelentkezés konfigurálásához szükséges metaadatok igényléséhez lépjen kapcsolatba a StatusPage támogatási csoportjával. 
    >
    > a. A metaadatokból másolja ki a kiállító értékét, majd illessze be az **azonosító** szövegmezőbe.
    >
    > b. A metaadatok között másolja a válasz URL-címét, majd illessze be a **Válasz URL-** szövegmezőbe.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **StatusPage beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-statuspage-single-sign-on"></a>StatusPage egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a StatusPage vállalati webhelyre rendszergazdaként.

1. A fő eszköztáron kattintson a **fiók kezelése**lehetőségre.

    ![Képernyőfelvétel: a StatusPage vállalati webhelyről kiválasztott fiók kezelése.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kattintson az **egyszeri bejelentkezés** fülre.

    ![A képernyőképen az egyszeri bejelentkezés lap látható.](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Az egyszeri bejelentkezés beállítása lapon hajtsa végre a következő lépéseket:

    ![A képernyőképen az S S O Setup (S S O Setup) oldal jelenik meg, ahol megadhatja a leírt értékeket.](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Képernyőfelvétel: a konfiguráció mentése gomb.](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Az **SSO cél URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    b. Nyissa meg a letöltött tanúsítványt a Jegyzettömbben, másolja a tartalmat, majd illessze be a **tanúsítvány** szövegmezőbe.

    c. Kattintson a **konfiguráció mentése**gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a StatusPage hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **StatusPage**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **StatusPage**lehetőséget.

    ![Az StatusPage hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-statuspage-test-user"></a>StatusPage-tesztelési felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a StatusPage-ben.

A StatusPage az igény szerinti üzembe helyezést is támogatja. Már engedélyezte az [Azure ad egyszeri bejelentkezés konfigurálásához](#configure-azure-ad-single-sign-on).

**A következő lépések végrehajtásával hozhat létre egy Britta Simon nevű felhasználót a StatusPage-ben:**

1. Jelentkezzen be a StatusPage vállalati webhelyre rendszergazdaként.

1. A felső menüben kattintson a **fiók kezelése**lehetőségre.

    ![Képernyőfelvétel: a StatusPage vállalati webhelyről kiválasztott fiók kezelése.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kattintson a **csapattagok** fülre.
  
    ![Képernyőfelvétel: a csapattagok lapot jeleníti meg.](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Kattintson a **csapattag hozzáadása**elemre.
  
    ![Képernyőfelvétel: a csapattag hozzáadása gomb.](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Írja be annak az érvényes felhasználónak az **E-mail címét**, **utónevét**és **vezetéknevét** , amelyet szeretne a kapcsolódó szövegmezőbe beírni. 

    ![Képernyőfelvétel: a felhasználó hozzáadása párbeszédpanel, amelyen megadhatja a leírt értékeket.](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. **Szerepkörként**válassza az **ügyfél rendszergazdája**elemet.

1. Kattintson a **fiók létrehozása**lehetőségre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a StatusPage csempére kattint, automatikusan be kell jelentkeznie arra a StatusPage, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
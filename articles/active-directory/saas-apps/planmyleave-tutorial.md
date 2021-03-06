---
title: 'Oktatóanyag: Azure Active Directory integráció a PlanMyLeave-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és PlanMyLeave között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: c143a5af40097039c70c58adba4cb21f687d3fa6
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893161"
---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Oktatóanyag: Azure Active Directory integráció a PlanMyLeave

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a PlanMyLeave a Azure Active Directory (Azure AD) szolgáltatással.
A PlanMyLeave és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a PlanMyLeave.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a PlanMyLeave (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció PlanMyLeave való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* PlanMyLeave egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A PlanMyLeave támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A PlanMyLeave **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-planmyleave-from-the-gallery"></a>PlanMyLeave hozzáadása a gyűjteményből

A PlanMyLeave Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PlanMyLeave a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha PlanMyLeave szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **PlanMyLeave** kifejezést, válassza a **PlanMyLeave** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![PlanMyLeave az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az PlanMyLeave-mel konfigurálja és teszteli a **Britta Simon** nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a PlanMyLeave kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés PlanMyLeave való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[PlanMyLeave egyszeri bejelentkezés konfigurálása](#configure-planmyleave-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre PlanMyLeave-teszt felhasználót](#create-planmyleave-test-user)** – hogy a PlanMyLeave Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés PlanMyLeave való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **PlanMyLeave** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![PlanMyLeave tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company-name>.planmyleave.com/Login.aspx`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<company-name>.planmyleave.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a PlanMyLeave ügyfélszolgálati [csapatához](mailto:support@planmyleave.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **PlanMyLeave beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-planmyleave-single-sign-on"></a>PlanMyLeave egyetlen Sign-On konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a PlanMyLeave-bérlőbe rendszergazdaként.

2. Nyissa meg a **rendszerbeállítást** . Ezután a **biztonság kezelése** szakaszban kattintson a **vállalati SAML-beállítások** elemre.

    ![Képernyőkép, amely a "Rendszerbeállítás" lapot jeleníti meg a "biztonság kezelése" szakaszra, és a "vállalati S L beállítások" művelet be van jelölve.](./media/planmyleave-tutorial/tutorial_planmyleave_002.png) 

3. Az **SAML-beállítások** szakaszban kattintson a szerkesztő ikonra.

    ![Képernyőkép, amely a szakasz jobb felső részén kiválasztott "szerkesztő" ikont jeleníti meg.](./media/planmyleave-tutorial/tutorial_planmyleave_003.png)

4. Az **SAML-beállítások frissítése** szakaszban hajtsa végre a következő lépéseket:

    ![Egyetlen Sign-On konfigurálása az alkalmazás oldalán](./media/planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  A **bejelentkezési URL** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .

    b.  Nyissa meg a letöltött metaadatokat, másolja a  **x509** értéket, majd illessze be a **tanúsítvány** szövegmezőbe.

    c. Állítsa **az "engedélyezve** " értéket az " **Igen** " értékre.

    d. Kattintson a **Mentés** gombra. 

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók** , majd a **minden felhasználó** lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a PlanMyLeave hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **PlanMyLeave** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **PlanMyLeave** lehetőséget.

    ![Az PlanMyLeave hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok** lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-planmyleave-test-user"></a>PlanMyLeave-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a PlanMyLeave-ben. A PlanMyLeave támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a PlanMyLeave-ben, a rendszer egy újat hoz létre a hitelesítés után.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [PlanMyLeave támogatási csapatával](mailto:support@planmyleave.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a PlanMyLeave csempére kattint, automatikusan be kell jelentkeznie arra a PlanMyLeave, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
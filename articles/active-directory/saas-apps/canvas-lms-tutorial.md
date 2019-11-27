---
title: 'Oktatóanyag: Azure Active Directory-integráció a vásznon | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és vászon között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ce5cff0de2939c25400d1d63138b23bc6c9822
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232038"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Oktatóanyag: Azure Active Directory-integráció a vásznon

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a vásznon Azure Active Directory (Azure AD-val).
A vászon az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a vászonhoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a vászonra (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció vászonhoz való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Vászon egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A vászon támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-canvas-from-the-gallery"></a>Vászon hozzáadása a katalógusból

A vászonnak az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a gyűjteményt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha vászon felvételét szeretné hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **vászon**kifejezést, válassza a **vászon** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Vászon az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a vászonra konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a vásznon lévő kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés vászon használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[vászon egyszeri bejelentkezésének konfigurálása](#configure-canvas-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Vászon-tesztelési felhasználó létrehozása](#create-canvas-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó Britta Simon (vászon) partnere van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Vászonval való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **vászon** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Vászon tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.instructure.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek beszerzéséhez vegye fel a kapcsolatot a [Canvas ügyfél-támogatási csapatával](https://community.canvaslms.com/community/help) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

6. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomatot** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

7. A **vászon beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL

### <a name="configure-canvas-single-sign-on"></a>Vászon egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a vászon vállalati webhelyre rendszergazdaként.

2. Lépjen a **tanfolyamok \> felügyelt fiókok \> a Microsoft webhelyre**.

    ![Vászon](./media/canvas-lms-tutorial/ic775990.png "Vászon")

3. A bal oldali navigációs ablaktáblán válassza a **hitelesítés**lehetőséget, majd kattintson az **Új SAML-konfiguráció hozzáadása**lehetőségre.

    ![Hitelesítés](./media/canvas-lms-tutorial/ic775991.png "Hitelesítés")

4. Az aktuális integráció oldalon hajtsa végre a következő lépéseket:

    ![Aktuális integráció](./media/canvas-lms-tutorial/ic775992.png "Aktuális integráció")

    a. A **identitásszolgáltató-entitás azonosítója** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    b. A bejelentkezési **URL-cím** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-cím** értékét.

    c. A **kijelentkezési URL** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

    d. A **jelszó módosítása hivatkozás** szövegmezőbe illessze be a **jelszó módosítása URL-cím** értékét, amelyet a Azure Portal másolt.

    e. A **Tanúsítvány ujjlenyomata** szövegmezőbe illessze be a tanúsítvány **ujjlenyomatának** értékét, amelyet a Azure Portal másolt.

    f. A **bejelentkezési attribútum** listából válassza a **NameID**lehetőséget.

    g. Az **azonosító formátuma** listából válassza az **emailAddress**lehetőséget.

    h. Kattintson a **hitelesítési beállítások mentése**lehetőségre.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon\@yourcompanydomain. Extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a vászonhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **vászon**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **vászon**lehetőséget.

    ![A vászon hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-canvas-test-user"></a>Vászon tesztelési felhasználó létrehozása

Ha engedélyezni szeretné az Azure AD-felhasználók számára, hogy bejelentkezzenek a vászonra, a vásznon kell kiépíteni őket. Vászon esetén a felhasználó kiosztása kézi feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **vászon** bérlőbe.

2. Lépjen a **tanfolyamok \> felügyelt fiókok \> a Microsoft webhelyre**.

   ![Vászon](./media/canvas-lms-tutorial/ic775990.png "Vászon")

3. Kattintson a **felhasználók**elemre.

   ![Felhasználók](./media/canvas-lms-tutorial/ic775995.png "Felhasználók")

4. Kattintson az **új felhasználó hozzáadása**lehetőségre.

   ![Felhasználók](./media/canvas-lms-tutorial/ic775996.png "Felhasználók")

5. Az új felhasználó hozzáadása párbeszédpanelen hajtsa végre a következő lépéseket:

   ![Felhasználó hozzáadása](./media/canvas-lms-tutorial/ic775997.png "Felhasználó hozzáadása")

   a. A **teljes név** szövegmezőbe írja be a felhasználó nevét (például **BrittaSimon**).

   b. Az **e-mail** szövegmezőbe írja be a felhasználó, például a **brittasimon\@contoso.com**-e-mail-címét.

   c. A **Bejelentkezés** szövegmezőbe írja be a felhasználó Azure ad-beli e-mail-címét, például **brittasimon\@contoso.com**.

   d. Válassza ki **a fiók létrehozásával kapcsolatos e-mailt a felhasználónak**.

   e. Kattintson a **felhasználó hozzáadása**elemre.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez a vászon által biztosított egyéb felhasználói fiókok létrehozására szolgáló eszközöket vagy API-kat is használhatja.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen a vászon csempére kattint, automatikusan be kell jelentkeznie arra a vászonra, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


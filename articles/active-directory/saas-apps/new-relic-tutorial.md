---
title: 'Oktatóanyag: Azure Active Directory integráció az új ereklye szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az új ereklye között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: fd75cc392bb30d15b665190eef8fbea39d692ee3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160343"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Oktatóanyag: Azure Active Directory integráció az új Műemlékgel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az új ereklye Azure Active Directory (Azure AD) használatával.
Az új ereklye az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Megadhatja az Azure AD-t, amely hozzáfér az új ereklye szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezzenek az új Ereklyebe (egyszeri Bejelentkezésbe) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció új ereklye használatával történő konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Új ereklye egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az új ereklye támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-new-relic-from-the-gallery"></a>Új ereklye hozzáadása a katalógusból

Az új ereklye Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az új ereklye elemet a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Új ereklye a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **új ereklye**kifejezést, válassza az eredmények panel **új ereklye** elemét, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Új ereklye az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az új ereklye segítségével konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az új ereklye kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés új ereklye használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Új ereklye egyszeri bejelentkezés konfigurálása](#configure-new-relic-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre új ereklye tesztelési felhasználót](#create-new-relic-test-user)** – hogy az új ereklye, amely a felhasználó Azure ad-Britta van társítva, Simon-t tartalmaz.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés új ereklye használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)az **új ereklye** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Új ereklye tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő mintával: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login`-ügyeljen arra, hogy helyettesítse be a saját új ereklye FIÓKjának azonosítóját.

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be a következő URL-címet: `rpm.newrelic.com`

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **új ereklye beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-new-relic-single-sign-on"></a>Új ereklye egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az **új ereklye** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **Fiókbeállítások**lehetőségre.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797036.png "Fiókbeállítások")

3. Kattintson a **Biztonság és hitelesítés** lapra, majd kattintson az **egyszeri bejelentkezés** fülre.
   
    ![Egyszeri bejelentkezés](./media/new-relic-tutorial/ic797037.png "Egyszeri bejelentkezés")

4. Az SAML párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. A letöltött Azure Active Directory-tanúsítvány feltöltéséhez kattintson a **fájl kiválasztása** lehetőségre.

    b. A **távoli bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.
   
    c. A **kijelentkezési URL-cím** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-címet**, amelyet a Azure Portalból másolt.

    d. Kattintson **a módosítások mentése**gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon\@yourcompanydomain. Extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezi az Azure egyszeri bejelentkezést az új ereklye hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **új ereklye**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **új ereklye**lehetőséget.

    ![Az új ereklye hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-new-relic-test-user"></a>Új ereklye tesztelési felhasználó létrehozása

Ahhoz, hogy Azure Active Directory felhasználók bejelentkezzenek az új ereklye szolgáltatásba, az új műemlékbe kell azokat kiépíteni. Új ereklye esetén a kiépítés manuális feladat.

**Ha a felhasználói fiókot új ereklye számára szeretné kiépíteni, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az **új ereklye** céges webhelyre rendszergazdaként.

2. A felső menüben kattintson a **Fiókbeállítások**lehetőségre.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797040.png "Fiókbeállítások")

3. A bal oldali **fiók** ablaktáblán kattintson az **Összefoglalás**, majd a **felhasználó hozzáadása**elemre.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797041.png "Fiókbeállítások")

4. Az **aktív felhasználók** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Aktív felhasználók](./media/new-relic-tutorial/ic797042.png "Aktív felhasználók")
   
    a. Az **e-mail** szövegmezőbe írja be a kiépíteni kívánt érvényes Azure Active Directory-felhasználó e-mail-címét.

    b. A **szerepkört** válassza a **felhasználó**lehetőségre.

    c. Kattintson **a felhasználó hozzáadása**elemre.

>[!NOTE]
>A HRE felhasználói fiókjainak kiépítéséhez az új ereklye által biztosított új ereklye felhasználóifiók-létrehozási eszközöket vagy API-kat is használhatja.
> 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az új ereklye csempére kattint, automatikusan be kell jelentkeznie arra az új emlékre, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


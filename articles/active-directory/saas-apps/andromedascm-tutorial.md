---
title: 'Oktatóanyag: Azure Active Directory integráció az Andromeda szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Andromeda között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 31fc1877dd0edd2fd670f0f9466692ac7bc8ad15
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531101"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Oktatóanyag: Azure Active Directory integráció az Andromeda-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Andromeda-t Azure Active Directory (Azure AD) használatával.
Az Andromeda az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá az Andromeda-hez.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Andromeda (egyszeri bejelentkezés) alkalmazásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az Andromeda-sel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Andromeda egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az Andromeda támogatja **az SP-t és a identitásszolgáltató** KEZDEMÉNYEZett SSO
* Az Andromeda **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-andromeda-from-the-gallery"></a>Az Andromeda hozzáadása a gyűjteményből

Az Andromeda Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Androméda-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az Androméda a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Andromeda**kifejezést, válassza az **Androméda** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Andromeda az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Androméda-vel konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az Andromeda-beli kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az Androméda-vel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[Androméda egyszeri bejelentkezés konfigurálása](#configure-andromeda-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. Az **[Andromeda-teszt felhasználójának létrehozása](#create-andromeda-test-user)** – a Britta Simon in Andromeda partnere, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az Andromeda-vel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/)az **Andromeda** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Az Androméda-tartomány és az URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantURL>.ngcxpress.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Az Androméda-tartomány és az URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Az értéket a tényleges azonosító, a válasz URL-cím és a bejelentkezési URL-cím alapján kell frissíteni, amelyet az oktatóanyag későbbi részében ismertetünk.

6. Az Androméda-alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

    > [!Important]
    > A névtér-definíciók törlése a beállítás beállításakor.

7. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában szerkessze a jogcímeket a **Szerkesztés ikon** használatával, vagy adja hozzá a jogcímeket az **új jogcím hozzáadása** paranccsal az SAML-token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket: 

    | Name | Forrás attribútum|
    | ------ | -----------|
    | szerepkör        | Alkalmazás-specifikus szerepkör |
    | típus        | Alkalmazás típusa |
    | cég       | CompanyName |

    > [!NOTE]
    > Nincsenek valós értékek. Ezek az értékek csak a bemutató célra szolgálnak, a szervezeti szerepköröket használják.

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    : Kattintson a **Mentés** gombra.

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

9. Az **Andromeda beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-andromeda-single-sign-on"></a>Az Andromeda egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be az Andromeda vállalati webhelyre rendszergazdaként.

2. A menüsáv tetején kattintson a **rendszergazda** lehetőségre, és navigáljon a **felügyelet**elemre.

    ![Androméda-rendszergazda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Kattintson a kezelőfelület bal oldalán az **illesztőfelületek** szakaszban az SAML- **konfiguráció**elemre.

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Az **SAML-konfiguráció** szakasz lapon hajtsa végre a következő lépéseket:

    ![Andromeda-konfiguráció](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Jelölje be **az egyszeri bejelentkezés engedélyezése az SAML-vel**lehetőséget.

    b. Az **Andromeda-információ** szakasz alatt másolja **az SP Identity** értéket, és illessze be az **alapszintű SAML-konfigurációs** szakasz **azonosító** szövegmezőbe.

    c. Másolja a **fogyasztói URL-címet** , és illessze be az **alapszintű SAML konfigurációs** szakasz **Válasz URL-** szövegmezőbe.

    d. Másolja a **bejelentkezési URL-címet** , és illessze be az **alapszintű SAML konfigurációs** szakasz **bejelentkezési URL** szövegmezőbe.

    e. Az **SAML-identitás szolgáltatója** szakaszban adja meg a identitásszolgáltató nevét.

    f. Az **egyszeri bejelentkezés végpontjának** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    : Nyissa meg a letöltött **Base64-kódolású tanúsítványt** Azure Portal a Jegyzettömbben, illessze be az **X 509 tanúsítvány** szövegmezőbe.
    
    h. Rendelje hozzá a következő attribútumokat a megfelelő értékkel, hogy elősegítse az egyszeri bejelentkezést az Azure AD-ben. A bejelentkezéshez a **User ID** attribútum szükséges. A kiépítés, az **e-mailek**, a **vállalat**, a **UserType**és a **szerepkör** megadása kötelező. Ebben a szakaszban az attribútumok leképezését (név és értékek) határozzák meg, amelyek korrelálnak a Azure Portal

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az Andromeda elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **Androméda**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Andromeda**elemet.

    ![Az alkalmazások listában található Andromeda-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-andromeda-test-user"></a>Andromeda-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre az Andromeda-ben. Az Andromeda az igény szerinti felhasználói üzembe helyezést támogatja, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik az Andromeda-ben, a rendszer egy újat hoz létre a hitelesítés után. Ha manuálisan kell létrehoznia egy felhasználót, forduljon az [Andromeda ügyfél-támogatási csapathoz](https://www.ngcsoftware.com/support/).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az Andromeda csempére kattint, automatikusan be kell jelentkeznie arra az Andromeda-ba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
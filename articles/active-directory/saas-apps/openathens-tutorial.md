---
title: 'Oktatóanyag: Azure Active Directory integráció a OpenAthens-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és OpenAthens között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81e37e78246a8c021cfdea015a9c4fe7695f5e44
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933988"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Oktatóanyag: Azure Active Directory integráció a OpenAthens

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a OpenAthens a Azure Active Directory (Azure AD) szolgáltatással.
A OpenAthens és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a OpenAthens.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a OpenAthens (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció OpenAthens való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* OpenAthens egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A OpenAthens támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* A OpenAthens **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-openathens-from-the-gallery"></a>OpenAthens hozzáadása a gyűjteményből

A OpenAthens Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a OpenAthens a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha OpenAthens szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **OpenAthens**kifejezést, válassza a **OpenAthens** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![OpenAthens az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az OpenAthens-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a OpenAthens kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés OpenAthens való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[OpenAthens egyszeri bejelentkezés konfigurálása](#configure-openathens-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre OpenAthens-teszt felhasználót](#create-openathens-test-user)** – hogy a OpenAthens Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés OpenAthens való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **OpenAthens** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

5. Az **alapszintű SAML-konfiguráció** szakaszban töltse fel a **szolgáltatói metaadatokat tartalmazó fájlt**, amely az oktatóanyag későbbi részében említett lépéseket ismerteti.

    a. Kattintson a **metaadat-fájl feltöltése**elemre.

    ![openathens feltöltése metaadatok](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés**elemre.

    ![Openathens tallózási metaadatok feltöltése](common/browse-upload-metadata.png)

    c. A metaadat-fájl feltöltése után az **azonosító** érték automatikusan kitöltődik az **SAML alapszintű konfigurációs** szakasz szövegmezőben:

    ![OpenAthens tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-identifier.png)

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>OpenAthens egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a OpenAthens vállalati webhelyre rendszergazdaként.

2. Válassza a **kapcsolatok** lehetőséget a listából a **kezelés** lapon. 

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Válassza ki az **SAML 1.1/2.0**elemet, majd kattintson a **Konfigurálás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. A konfiguráció hozzáadásához kattintson a **Tallózás** gombra a Azure Portal letöltött metadata. xml fájl feltöltéséhez, majd válassza a **Hozzáadás**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Hajtsa végre a következő lépéseket a **részletek** lapon.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. A **megjelenítendő név leképezése**lapon válassza az **attribútum használata**elemet.

    b. A **megjelenítendő név attribútum** szövegmezőbe írja be a `http://schemas.microsoft.com/identity/claims/displayname`értéket.
    
    c. Az **egyedi felhasználók leképezése**területen válassza az **attribútum használata**lehetőséget.

    d. Az **egyedi felhasználói attribútum** szövegmezőbe írja be a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`értéket.

    e. Az **állapot**területen jelölje be mind a három jelölőnégyzetet.

    f. A **helyi fiókok létrehozása**területen válassza az **automatikus**lehetőséget.

    g. Válassza a **módosítások mentése**lehetőséget.

    h. A **</> függő entitás** lapon másolja a **metaadatok URL-címét** , és nyissa meg ezt a böngészőben az **SP-metaadatok XML-** fájljának letöltéséhez. Töltse fel ezt az SP metaadat-fájlt az Azure AD **alapszintű SAML-konfiguráció** szakaszában.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application5.png)

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a OpenAthens hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **OpenAthens**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **OpenAthens**lehetőséget.

    ![Az OpenAthens hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-openathens-test-user"></a>OpenAthens-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a OpenAthens-ben. A OpenAthens támogatja az **igény szerinti felhasználói üzembe**helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a OpenAthens-ben, a rendszer egy újat hoz létre a hitelesítés után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a OpenAthens csempére kattint, automatikusan be kell jelentkeznie arra a OpenAthens, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Oktatóanyag: Azure Active Directory integráció az SAML SSO-vel a Bamboo by Solution GmbH használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure Active Directory és az SAML egyszeri bejelentkezés a Bamboo-hoz a Solution GmbH használatával.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044a2f9a9ecd0ccceb99ce7999af7e2c8578950d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67106540"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory integráció az SAML SSO-val a Bamboo by Solution GmbH használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAML SSO-t a Bamboo-hoz a Azure Active Directory (Azure AD) megoldással.
Az Azure AD-val az alábbi előnyökkel jár:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Bamboo egyszeri bejelentkezéshez a Solution GmbH használatával.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAML SSO-ba a Bamboo-ba az Azure AD-fiókkal, az egyszeri bejelentkezéssel.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Bamboo által használt SAML SSO-val szeretné konfigurálni, a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* SAML egyszeri bejelentkezés a Bamboo-hoz az egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Bamboo by Solution GmbH SAML SSO-je támogatja az **SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* A Bamboo által a Solution GmbH SAML SSO-je a felhasználó üzembe helyezésének **időpontját** támogatja

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>A Bamboo-beli SAML SSO hozzáadása a katalógusból a megoldási GmbH használatával

Ahhoz, hogy az Azure AD-be az SAML SSO-t a Bamboo-hoz az Azure AD-be, hozzá kell adnia a Bamboo-hoz készült SAML SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A következő lépésekkel adhatja hozzá a Bamboo-hez készült SAML egyszeri bejelentkezést a (z)**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **SAML SSO a Bamboo by Solution GmbH**, válassza a **következőt: SAML SSO for Bamboo by Solution GmbH** az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SAML egyszeri bejelentkezés a Bamboo számára az eredmények listájában található feloldási GmbH használatával](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Bamboo által használt SAML SSO-vel a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolatot kell létrehozni a Bamboo-hoz készült SAML egyszeri bejelentkezéssel.

Ha az Azure AD egyszeri bejelentkezést az SAML SSO-val a Bamboo by Solution GmbH használatával szeretné konfigurálni és tesztelni, el kell végeznie a következő építőelemeket:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAML egyszeri bejelentkezést a Bamboo megoldáshoz az egyszeri bejelentkezéssel](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[SAML SSO létrehozása a Bamboo számára a Solution GmbH test User](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** -to have a Britta Simon a következőhöz: SAML SSO for Bamboo by Solution GmbH, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést az SAML SSO-val a Bamboo by Solution GmbH használatával szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portalban](https://portal.azure.com/)a **Bamboo SAML SSO-on a Resolution GmbH** Application Integration lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![SAML egyszeri bejelentkezés a Bamboo-hoz a Solution GmbH tartomány és az URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![SAML egyszeri bejelentkezés a Bamboo-hoz a Solution GmbH tartomány és az URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Vegye fel a kapcsolatot az [SAML SSO-val a Bamboo by Solution GmbH ügyfél-támogatási csapatával](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **set up SAML SSO for Bamboo by Solution GmbH** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>SAML SSO konfigurálása a Bamboo számára az egyszeri bejelentkezéssel

1. Jelentkezzen be a Bamboo-hoz készült SAML SSO-ra a megoldási GmbH vállalati webhely rendszergazdaként.

1. A fő eszköztár jobb oldalán kattintson a **Beállítások** > **bővítmények**elemre.

    ![A beállítások](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Lépjen a biztonság szakaszra, kattintson a menüsávban található **SAML-SingleSignon** .

    ![A Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Az **SAML SIngleSignOn beépülő modul konfigurálása lapon**kattintson a **identitásszolgáltató hozzáadása**elemre.

    ![A identitásszolgáltató hozzáadása](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Az **SAML-azonosító kiválasztása** lapon hajtsa végre a következő lépéseket:

    ![Az identitás szolgáltatója](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Válassza a **identitásszolgáltató típust** **Azure ad**-ként.

    b. A **név** szövegmezőbe írja be a nevet.

    c. A **Leírás** szövegmezőbe írja be a leírást.

    d. Kattintson a **Tovább** gombra.

1. Az **identitás-szolgáltató konfigurációja** lapon kattintson a **tovább**gombra.

    ![Az identitás konfigurációja](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Az **SAML identitásszolgáltató-Metaadatok importálása** lapon kattintson a **fájl betöltése** gombra, hogy feltöltse a **metaadatokat tartalmazó XML-** fájlt, amelyet a Azure Portal letöltött.

    ![A idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Kattintson a **Tovább** gombra.

1. Kattintson a **Beállítások mentése** lehetőségre.

    ![A Mentés](./media/bamboo-tutorial/tutorial_bamboo_save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`a nevet. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban az Azure egyszeri bejelentkezést az SAML egyszeri bejelentkezéshez a Britta-hez való hozzáférés engedélyezésével engedélyezheti.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **következőt: SAML SSO a Bamboo by Solution GmbH**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **következőt: SAML SSO a Bamboo by Solution GmbH**.

    ![A Bamboo-beli SAML egyszeri bejelentkezés az alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>SAML SSO létrehozása a Bamboo számára a Solution GmbH test User

Ennek a szakasznak a célja, hogy egy Britta Simon nevű felhasználót hozzon létre a Bamboo által használt SAML SSO-hoz. A Bamboo by Solution GmbH SAML SSO-je támogatja az igény szerinti üzembe helyezést, és a felhasználók manuálisan is létrehozhatók, [Ha a megoldás alapján az ügyfél-támogatási csapat](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) felveszi a kapcsolatot az SAML SSO-val a Bamboo-hoz.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a Bamboo SSO for bambusz by Solution GmbH csempére kattint, automatikusan be kell jelentkeznie a Bamboo által használt SAML egyszeri bejelentkezésre. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

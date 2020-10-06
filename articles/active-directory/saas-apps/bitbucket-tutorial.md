---
title: 'Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a bitbucket by Solution GmbH használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SAML SSO között a bitbucket by Solution GmbH használatával.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 4aec6f266d7559656bca848801df54c83949e925
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91741242"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a bitbucket by Solution GmbH által

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAML SSO-t a bitbucket for Solution GmbH Azure Active Directory (Azure AD) használatával.
Az SAML SSO az Azure AD-vel való bitbucket általi integrációja a következő előnyöket biztosítja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá az SAML SSO-hoz a bitbucket by Solution GmbH használatával.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAML SSO-ba a bitbucket (egyszeri bejelentkezés) és az Azure AD-fiókjuk használatával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt az SAML SSO-nal szeretné konfigurálni a bitbucket by Solution GmbH esetében, a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SAML egyszeri bejelentkezés a bitbucket-hez az egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A bitbucket által használt SAML SSO támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t
* Az SAML SSO bitbucket általi feloldása a felhasználói üzembe helyezést **csak időben** támogatja


## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>SAML SSO hozzáadása a bitbucket by Solution GmbH által a katalógusból

Ha az SAML SSO-t az Azure AD-be való bitbucket szeretné konfigurálni, az SAML SSO-t hozzá kell adnia az bitbucket-hez készült Solution GmbH által a felügyelt SaaS-alkalmazások listájához.

**A következő lépésekkel adhatja hozzá az SAML SSO-t a bitbucket by Solution GmbH által a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **SAML SSO a bitbucket by Solution GmbH**esetében, válassza az **SAML SSO lehetőséget a bitbucket by Solution GmbH** az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SAML SSO az bitbucket az eredmények listájában a feloldási GmbH által](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az SAML SSO-nal konfigurálja és teszteli a bitbucket által a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó SAML SSO-kapcsolaton keresztüli bitbucket kell létrehozni.

Az Azure AD egyszeri bejelentkezés SAML SSO-vel történő konfigurálásához és teszteléséhez a bitbucket by Solution GmbH esetében el kell végeznie a következő építőelemeket:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAML SSO-t a bitbucket által a feloldási GmbH egyszeri bejelentkezéssel](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on)** , hogy az egyszeri bejelentkezési beállításokat konfigurálja az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[SAML SSO létrehozása a bitbucket által a megoldáshoz használt felhasználó](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** számára – a felhasználó Azure ad-beli Britta kapcsolódó, az SAML SSO-ban található, a bitbucket-hez készült "
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés SAML SSO-vel való konfigurálásához a bitbucket by Solution GmbH esetében hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **bitbucket SAML SSO esetén a megoldási GmbH** Application Integration lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni:

    ![A képernyőfelvételen az alapszintű SAML-konfiguráció látható, ahol megadható az azonosító, a válasz U R L, majd a Mentés elemre.](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Képernyőfelvétel: további U R ls beállítása, ahol megadhatja a bejelentkezést az U R L-ben.](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Vegye fel a kapcsolatot az [SAML SSO-vel a bitbucket by Solution GmbH ügyfélszolgálati csapatával](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on"></a>SAML SSO konfigurálása a bitbucket megoldáshoz az egyszeri bejelentkezéssel

1. Jelentkezzen be az SAML SSO-ra a bitbucket által a megoldási GmbH céges webhelyre rendszergazdaként.

2. A fő eszköztár jobb oldalán kattintson a **Beállítások**elemre.

3. Nyissa meg a fiókok szakaszt, és kattintson a menüsor **SAML-SingleSignon** .

    ![A Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Az **SAML SIngleSignOn beépülő modul konfigurálása lapon**kattintson a **identitásszolgáltató hozzáadása**elemre. 

    ![A identitásszolgáltató hozzáadása](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Az **SAML-azonosító kiválasztása** lapon hajtsa végre a következő lépéseket:

    ![Az identitás szolgáltatója](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Válassza a **identitásszolgáltató típust** **Azure ad**-ként.

    b. A **név** szövegmezőbe írja be a nevet.

    c. A **Leírás** szövegmezőbe írja be a leírást.

    d. Kattintson a **Tovább** gombra.

6. Az **identitás-szolgáltató konfigurációja lapon**kattintson a **tovább**gombra.

    ![Az identitás konfigurációja](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Az **SAML identitásszolgáltató-Metaadatok importálása** lapon kattintson a **fájl betöltése** gombra, hogy feltöltse a **metaadatokat tartalmazó XML-** fájlt, amelyet a Azure Portal letöltött.

    ![A idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
8. Kattintson a **Tovább** gombra.

9. Kattintson a **Beállítások mentése** lehetőségre.

    ![A Mentés](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson az SAML SSO-hoz a bitbucket by Solution GmbH.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **SAML SSO lehetőséget a bitbucket by Solution GmbH esetében**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza ki az **SAML SSO elemet a bitbucket by Solution GmbH számára**.

    ![Az alkalmazások listájában a bitbucket által a megoldási GmbH-hez csatolt, SAML SSO](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>SAML SSO létrehozása a bitbucket által a Resolution GmbH test User használatával

Ennek a szakasznak a célja, hogy létrehozzon egy Britta Simon nevű felhasználót az SAML SSO-ban a bitbucket by Solution GmbH használatával. A bitbucket által használt SAML SSO az igény szerinti üzembe helyezést támogatja, és a felhasználókat manuálisan is létrehozhatja. az [SAML SSO-t a bitbucket a Solution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) ügyfélszolgálati csapata adja meg.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a bitbucket által használt SAML SSO-ra kattint, automatikusan be kell jelentkeznie az SAML SSO-ba a bitbucket by Solution GmbH számára, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


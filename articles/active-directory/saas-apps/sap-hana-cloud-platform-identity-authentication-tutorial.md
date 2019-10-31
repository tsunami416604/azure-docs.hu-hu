---
title: 'Oktatóanyag: Azure Active Directory integráció az SAP Cloud platform Identity Authentication szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP Cloud platform Identity Authentication között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 196de1cacb977305c778e16182b21c8dcfafe13e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161171"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Oktatóanyag: Azure Active Directory integráció az SAP Cloud platform Identity Authentication szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Cloud platform identitás-hitelesítését Azure Active Directory (Azure AD) használatával.
Az SAP Cloud platform Identity Authentication és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá az SAP Cloud platform identitás-hitelesítéséhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAP Cloud platform Identity Authentication (egyszeri bejelentkezés) szolgáltatásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció SAP Cloud platform identitás-hitelesítéssel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SAP Cloud platform Identity Authentication egyszeri bejelentkezés engedélyezve előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az SAP Cloud platform Identity Authentication támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

Mielőtt belemerül a technikai részletekbe, fontos megérteni a megtekinteni kívánt fogalmakat. Az SAP Cloud platform Identity Authentication és Active Directory összevonási szolgáltatások (AD FS) lehetővé teszi az egyszeri bejelentkezést az Azure AD (identitásszolgáltató) által védett alkalmazásokban vagy szolgáltatásokban, az SAP-felhő által védett SAP-alkalmazásokkal és-szolgáltatásokkal. Platform identitásának hitelesítése.

Jelenleg az SAP Cloud platform Identity Authentication proxy-identitás-szolgáltatóként működik az SAP-alkalmazásokban. A Azure Active Directory ebben a beállításban a vezető identitás-szolgáltatóként működik. 

A következő ábra ezt a kapcsolatot szemlélteti:

![Azure AD-tesztkörnyezet létrehozása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Ezzel a beállítással az SAP Cloud platform Identity Authentication bérlője megbízható alkalmazásként van konfigurálva Azure Active Directoryban.

Az ezen a módon védelemmel ellátni kívánt SAP-alkalmazásokat és-szolgáltatásokat az SAP Cloud platform Identity Authentication felügyeleti konzolján kell konfigurálni.

Ezért az SAP-alkalmazásokhoz és-szolgáltatásokhoz való hozzáférés engedélyezésének engedélyezése szükséges az SAP Cloud platform Identity Authentication (nem pedig a Azure Active Directory) számára.

Ha az SAP Cloud platform Identity Authentication alkalmazást a Azure Active Directory piactéren konfigurálja, nem kell külön jogcímeket vagy SAML-jogcímeket konfigurálnia.

> [!NOTE]
> A két fél jelenleg csak a webes egyszeri bejelentkezést tesztelte. Az alkalmazás-API vagy API-to-API kommunikációhoz szükséges folyamatoknak működniük kell, de még nem tesztelték őket. A következő tevékenységek során lesznek tesztelve.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Cloud platform identitás-hitelesítés hozzáadása a katalógusból

Az SAP Cloud platform identitás-hitelesítés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Cloud platform Identity Authentication hitelesítést a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az SAP Cloud platform Identity Authentication a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **SAP Cloud platform Identity Authentication**, válassza az **SAP Cloud platform Identity Authentication** az eredmények paneljén, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SAP Cloud platform Identity Authentication az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli az [alkalmazásnév] névvel a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az [alkalmazásnév] kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[SAP Cloud platform Identity Authentication – egyszeri bejelentkezés konfigurálása](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[SAP Cloud platform Identity Authentication test User](#create-sap-cloud-platform-identity-authentication-test-user)** -to have a Britta Simon a SAP Cloud platform Identity Authentication, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)az **SAP Cloud platform Identity Authentication** Application Integration oldalon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Ha az **alapszintű SAML-konfiguráció** szakaszát szeretné beállítani **identitásszolgáltató** intiated módban, hajtsa végre a következő lépéseket:

    ![SAP Cloud platform Identity Authentication tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `<IAS-tenant-id>.accounts.ondemand.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek beszerzéséhez lépjen kapcsolatba az [SAP Cloud platform Identity Authentication ügyfél-támogatási csapatával](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) . Ha nem érti az azonosító értékét, olvassa el az SAP Cloud platform Identity Authentication dokumentációját a [bérlői SAML 2,0-konfigurációról](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![SAP Cloud platform Identity Authentication tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Használja az adott üzleti alkalmazás bejelentkezési URL-címét. Ha kétségei vannak, lépjen kapcsolatba az [SAP Cloud platform Identity Authentication ügyfél-támogatási csapatával](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) .

6. Az SAP Cloud platform Identity Authentication alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

7. Ha az SAP-alkalmazás olyan attribútumot vár, mint például a **FirstName**, adja hozzá a **FirstName** attribútumot a felhasználó **attribútumai** párbeszédpanel felhasználói **JOGcímek** szakaszában, konfigurálja az SAML-token attribútumot a fenti képen látható módon, és hajtsa végre a következőt: következő lépések:

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be a firstName attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listából válassza ki a User. givenName attribútum értékét.

    f. Kattintson **az OK** gombra

    g. Kattintson a **Save** (Mentés) gombra.

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **metaadatok XML** -fájljának a megadott beállítások alapján történő letöltéséhez, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

9. Az **SAP Cloud platform Identity Authentication beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Az SAP Cloud platform Identity Authentication szolgáltatás egyszeri bejelentkezésének konfigurálása

1. Az alkalmazáshoz tartozó egyszeri bejelentkezés beszerzéséhez nyissa meg az SAP Cloud platform Identity Authentication felügyeleti konzolt. Az URL-cím a következő mintával rendelkezik: `https://<tenant-id>.accounts.ondemand.com/admin`. Ezután olvassa el az SAP Cloud platform Identity Authentication dokumentációját a [Microsoft Azure ad integrációjában](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. A Azure Portal válassza a **Mentés** gombot.

3. Folytassa a következőket csak abban az esetben, ha az SSO-t egy másik SAP-alkalmazáshoz szeretné hozzáadni és engedélyezni. Ismételje meg a következő szakaszt: **SAP Cloud platform Identity Authentication hozzáadása**a katalógusból.

4. A Azure Portal az **SAP Cloud platform Identity Authentication** Application Integration lapon válassza a **csatolt bejelentkezés**lehetőséget.

    ![Csatolt bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Mentse a konfigurációt.

> [!NOTE]
> Az új alkalmazás kihasználja az előző SAP-alkalmazás egyszeri bejelentkezési konfigurációját. Győződjön meg arról, hogy ugyanazt a vállalati identitás-szolgáltatót használja az SAP Cloud platform Identity Authentication felügyeleti konzolján.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az SAP Cloud platform Identity Authentication elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **SAP Cloud platform Identity Authentication**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **SAP Cloud platform Identity Authentication**lehetőséget.

    ![Az SAP Cloud platform Identity Authentication hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>SAP Cloud platform identitás-hitelesítési teszt felhasználó létrehozása

Nem kell felhasználót létrehoznia az SAP Cloud platform Identity Authentication szolgáltatásban. Az Azure AD felhasználói tárolójában lévő felhasználók használhatják az egyszeri bejelentkezés funkciót.

Az SAP Cloud platform Identity Authentication támogatja az identitás-összevonási lehetőséget. Ez a beállítás lehetővé teszi, hogy az alkalmazás meggyőződjön arról, hogy a vállalati identitás-szolgáltató által hitelesített felhasználók léteznek-e az SAP Cloud platform Identity Authentication felhasználói tárolójában.

Az identitás-összevonás beállítás alapértelmezés szerint le van tiltva. Ha az identitás-összevonás engedélyezve van, csak az SAP Cloud platform Identity Authentication szolgáltatásban importált felhasználók férhetnek hozzá az alkalmazáshoz.

További információ arról, hogyan engedélyezheti vagy tilthatja le az identitás-összevonást az SAP Cloud platform Identity Authentication használatával. az identitás-összevonás [konfigurálása a felhasználói tárolóval című témakör "identitás-összevonás engedélyezése az SAP Cloud platform identitás-hitelesítéssel" című szakaszában talál SAP Cloud platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SAP Cloud platform Identity Authentication csempére kattint, automatikusan be kell jelentkeznie az SAP Cloud platform Identity Authentication szolgáltatásba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

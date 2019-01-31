---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az SAP Felhőplatform Identitáshitelesítésével |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az SAP Felhőplatform Identitáshitelesítésével között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: 6b5b664581a1f3da367f74318cfb6bf5564e5b39
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472881"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP Felhőplatform Identitáshitelesítésével

Ebben az oktatóanyagban elsajátíthatja, hogyan SAP Felhőplatform Identitáshitelesítésével integrálása az Azure Active Directory (Azure AD).
SAP Felhőplatform Identitáshitelesítésével integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az SAP Felhőplatform Identitáshitelesítésével Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve SAP Felhőplatform Identitáshitelesítésével (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az SAP Felhőplatform Identitáshitelesítésével konfigurálásához lesz szüksége a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* SAP Felhőplatform Identitáshitelesítésével egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja az SAP Felhőplatform Identitáshitelesítésével **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

Mielőtt alaposabban elmerülne a technikai részleteket, létfontosságú a szereplőknek tekintse meg fog a fogalmak megértéséhez. Az SAP Felhőplatform Identitáshitelesítésével és az Active Directory összevonási szolgáltatások lehetővé teszi az egyszeri bejelentkezés megvalósítása a különböző alkalmazások vagy szolgáltatások, az SAP-alkalmazások (mint az IdP) az Azure AD által védett és védett SAP Cloud services Felhőplatform Identitáshitelesítésével.

SAP Felhőplatform Identitáshitelesítésével jelenleg egy SAP-alkalmazások Proxy identitásszolgáltató funkcionál. Az Azure Active Directory ezután ezt a beállítást a vezető identitásszolgáltató funkcionál. 

A következő ábra szemlélteti ezt a kapcsolatot:

![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Ezzel a beállítással az SAP Felhőplatform Identitáshitelesítésével bérlő az Azure Active Directory megbízható alkalmazás van konfigurálva.

Minden SAP-alkalmazások és szolgáltatások, amelyek így védeni kívánt ezt követően konfigurálni a SAP Felhőplatform Identitáshitelesítésével felügyeleti konzolon.

Ezért az engedélyezési el az SAP-alkalmazások és szolgáltatások kerüljön sor az SAP Felhőplatform Identitáshitelesítésével (szemben az Azure Active Directory) szükséges.

Konfigurálja az SAP Felhőplatform Identitáshitelesítésével alkalmazást az Azure Active Directory Marketplace-en keresztül, nem kell egyéni igények vagy SAML helyességi feltételek konfigurálása.

> [!NOTE]
> Mindkét fél jelenleg csak a webes egyszeri bejelentkezés tesztelve lett. A flow alkalmazás API- vagy API-API-kommunikáció szükséges működik, de nem tesztelte. Egymást követő tevékenységek során teszthellyel.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Felhőplatform Identitáshitelesítésével hozzáadása a katalógusból

Az Azure AD-be SAP Felhőplatform Identitáshitelesítésével integrációjának konfigurálása, hozzá kell SAP Felhőplatform Identitáshitelesítésével a galériából a felügyelt SaaS-alkalmazások listájára.

**SAP Felhőplatform Identitáshitelesítésével hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAP Felhőplatform Identitáshitelesítésével**, jelölje be **SAP Felhőplatform Identitáshitelesítésével** eredmény panelen kattintson a **Hozzáadás** gombra kattintva Vegye fel az alkalmazást.

     ![SAP Felhőplatform Identitáshitelesítésével a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az [alkalmazásnév] nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó [alkalmazásnév] a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az SAP Cloud Platform identitás hitelesítés egyszeri bejelentkezés konfigurálása](#configure-sap-cloud-platform-identity-authentication-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre az SAP Felhőplatform Identitáshitelesítésével tesztfelhasználót](#create-sap-cloud-platform-identity-authentication-test-user)**  – egy megfelelője a Britta Simon SAP Felhőplatform Identitáshitelesítésével, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés az [alkalmazásnév] konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **SAP Felhőplatform Identitáshitelesítésével** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha a konfigurálni kívánt **Identitásszolgáltató** intiated mód hajtsa végre a következő lépéseket:

    ![Az SAP Cloud platformon identitás hitelesítési tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Forduljon a [SAP Cloud platformon identitás hitelesítési ügyfél-támogatási csapatának](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) beolvasni ezeket az értékeket. Ha nem ismeri a azonosító értékét, olvassa el az SAP Felhőplatform Identitáshitelesítésével dokumentáció [bérlői SAML 2.0 konfigurációs](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Az SAP Cloud platformon identitás hitelesítési tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Az adott üzleti alkalmazás bejelentkezési URL-CÍMÉT használja. Forduljon a [SAP Cloud platformon identitás hitelesítési ügyfél-támogatási csapatának](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) Ha kétségei vannak.

6. SAP-Felhőplatform Identitáshitelesítésével alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Ha az SAP-alkalmazás vár egy attribútum, például **firstName**, adja hozzá a **firstName** az attribútum a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok**  párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be az attribútum neve firstName.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** listájához, válassza ki az attribútum értéke user.givenname.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **metaadatainak XML**a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

9. Az a **SAP Felhőplatform Identitáshitelesítésével beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Az SAP Cloud Platform identitás hitelesítés egyszeri bejelentkezés konfigurálása

1. Egyszeri bejelentkezés az alkalmazáshoz konfigurált keresse fel az SAP Felhőplatform Identitáshitelesítésével felügyeleti konzoljára. Az URL-címnek a következő mintának: `https://<tenant-id>.accounts.ondemand.com/admin`. Ezután olvassa el a dokumentációjában az SAP Felhőplatform Identitáshitelesítésével, [a Microsoft Azure AD-integráció](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. Az Azure Portalon válassza ki a **mentése** gombra.

3. Folytassa a következő csak akkor, ha azt szeretné, adja hozzá, és engedélyezze az egyszeri Bejelentkezést az SAP egy másik alkalmazás. Ismételje meg a szakasz alatt **hozzáadása SAP Felhőplatform Identitáshitelesítésével a katalógusból**.

4. Az Azure Portalon az a **SAP Felhőplatform Identitáshitelesítésével** alkalmazás integráció lapon jelölje be **csatolt bejelentkezés**.

    ![Csatolt bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Mentse a konfigurációt.

> [!NOTE]
> Az új alkalmazás használja az előző SAP alkalmazás egyszeri bejelentkezési konfiguráció. Ellenőrizze, hogy használja az ugyanazon vállalati identitás-szolgáltatóktól az SAP Felhőplatform Identitáshitelesítésével felügyeleti konzolon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAP Felhőplatform Identitáshitelesítésével Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SAP Felhőplatform Identitáshitelesítésével**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **SAP Felhőplatform Identitáshitelesítésével**.

    ![Az alkalmazások listáját az SAP Felhőplatform Identitáshitelesítésével hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>SAP Felhőplatform Identitáshitelesítésével tesztfelhasználó létrehozása

Hozzon létre egy felhasználót az SAP Felhőplatform Identitáshitelesítésével nincs szükségünk. Az Azure ad-ben felhasználókhoz tartozó tárolóban lévő felhasználók számára az egyszeri bejelentkezési funkciókat használhat.

SAP Felhőplatform Identitáshitelesítésével támogatja az identitás-összevonási lehetőséget. Ez a beállítás lehetővé teszi, hogy az alkalmazást, ellenőrizze, hogy a vállalati identitás szolgáltató által hitelesített felhasználók létezik-e az a felhasználó store, az SAP Felhőplatform Identitáshitelesítésével.

Az identitás-összevonási beállítás alapértelmezés szerint le van tiltva. Ha az identitás-összevonási engedélyezve van, csak az SAP Felhőplatform Identitáshitelesítésével az importált felhasználók is elérhetik az alkalmazást.

Engedélyezheti vagy tilthatja le az identitás-összevonás az SAP Felhőplatform Identitáshitelesítésével kapcsolatos további információkért lásd a "Engedélyezése identitás összevonási az SAP Felhőplatform Identitáshitelesítésével" [identitás-összevonás konfigurálása az a Felhasználói Store az SAP-Felhőplatform Identitáshitelesítésével](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SAP Felhőplatform Identitáshitelesítésével csempére kattint, meg kell automatikusan megtörténik a, a SAP Felhőplatform Identitáshitelesítésével, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
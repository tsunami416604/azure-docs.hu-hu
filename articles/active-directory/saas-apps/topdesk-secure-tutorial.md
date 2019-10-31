---
title: 'Oktatóanyag: Azure Active Directory integráció a TOPdesk-Secure szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és TOPdesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3125f5b9075a69289bd6a44d62c3d06d5e6d910d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161125"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Oktatóanyag: Azure Active Directory integráció a TOPdesk – biztonságos

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a TOPdesk-Securet Azure Active Directory (Azure AD) használatával.
A TOPdesk-Secure az Azure AD-vel való integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a TOPdesk-Secure szolgáltatáshoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a TOPdesk (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a TOPdesk-Secure szolgáltatással való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* TOPdesk – biztonságos egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* TOPdesk – az **SP** által kezdeményezett egyszeri bejelentkezés támogatása

## <a name="adding-topdesk---secure-from-the-gallery"></a>TOPdesk hozzáadása – biztonságos a katalógusból

A TOPdesk-Secure Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a TOPdesk-Secure szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A TOPdesk a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **TOPdesk-Secure**kifejezést, válassza a **TOPdesk-Secure** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![TOPdesk – biztonságos az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a TOPdesk-Secure szolgáltatással konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat TOPdesk kell létrehozni.

Az Azure AD egyszeri bejelentkezés TOPdesk-Secure használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[TOPdesk – biztonságos egyszeri bejelentkezés konfigurálása](#configure-topdesk---secure-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre TOPdesk – biztonságos teszt felhasználót](#create-topdesk---secure-test-user)** –, hogy a Britta Simon a TOPdesk-Secure, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés TOPdesk-Secure használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **TOPdesk – biztonságos alkalmazás-** integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![TOPdesk – biztonságos tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.topdesk.net`

    b. Az **azonosító URL-címe** mezőben adja meg a TOPdesk metaadat URL-címét, amelyet a TOPdesk-konfigurációból kérhet le. A következő mintát kell használnia: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel a kapcsolatot a [TOPdesk – biztonságos ügyfél-támogatási csapattal](https://www.topdesk.com/us/support/) , hogy megszerezze ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **TOPdesk-Secure beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-topdesk---secure-single-sign-on"></a>TOPdesk-alapú biztonságos egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be a **TOPdesk-Secure** céges webhelyre rendszergazdaként.

2. A **TOPdesk** menüben kattintson a **Beállítások**elemre.

    ![Beállítások](./media/topdesk-secure-tutorial/ic790598.png "Beállítások")

3. Kattintson a **bejelentkezési beállítások**elemre.

    ![Bejelentkezési beállítások](./media/topdesk-secure-tutorial/ic790599.png "Bejelentkezési beállítások")

4. Bontsa ki a **bejelentkezési beállítások** menüt, majd kattintson az **általános**elemre.

    ![Általános](./media/topdesk-secure-tutorial/ic790600.png "Általános")

5. Az **SAML bejelentkezési** konfiguráció szakaszának **biztonságos** részében hajtsa végre a következő lépéseket:

    ![Technikai beállítások](./media/topdesk-secure-tutorial/ic790855.png "Technikai beállítások")

    a. Kattintson a **Letöltés** gombra a nyilvános metaadat-fájl letöltéséhez, majd mentse helyileg a számítógépén.

    b. Nyissa meg a metaadat-fájlt, és keresse meg a **AssertionConsumerService** csomópontot.

    ![Kijelentési fogyasztói szolgáltatás](./media/topdesk-secure-tutorial/ic790856.png "Kijelentési fogyasztói szolgáltatás")

    c. Másolja a **AssertionConsumerService** értéket, illessze be ezt az értéket a válasz URL-címe szövegmezőbe a **TOPdesk – biztonságos tartomány és URL-címek** szakaszban.

6. Tanúsítványfájl létrehozásához hajtsa végre a következő lépéseket:

    ![Tanúsítvány](./media/topdesk-secure-tutorial/ic790606.png "Tanúsítvány")

    a. Nyissa meg a letöltött metaadat-fájlt a Azure Portal.

    b. Bontsa ki a **securitytokenservicetype** csomópontot, amely egy **xsi: Type** of **Fed: ApplicationServiceType**.

    c. Másolja a **x509** csomópont értékét.

    d. Mentse a másolt **x509** -értéket helyileg a számítógépen egy fájlba.

7. A **nyilvános** szakaszban kattintson a **Hozzáadás**gombra.

    ![Hozzáadása](./media/topdesk-secure-tutorial/ic790607.png "Hozzáadás")

8. A **SAML konfigurációs segéd** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![SAML-konfigurációs segéd](./media/topdesk-secure-tutorial/ic790608.png "SAML-konfigurációs segéd")

    a. A letöltött metaadat-fájl Azure Portalból való feltöltéséhez az **összevonási metaadatok**területen kattintson a **Tallózás**gombra.

    b. A tanúsítványfájl feltöltéséhez a **tanúsítvány (RSA)** alatt kattintson a **Tallózás**gombra.

    c. A **titkos kulcs (RSA, PKCS8, der)** esetében feltöltheti saját titkos kulcsát, vagy felveheti a kapcsolatot a [TOPdesk-Secure ügyfélszolgálati csapatával](https://www.topdesk.com/us/support) a titkos kulcs lekéréséhez.

    d. Ha fel szeretné tölteni a TOPdesk támogatási csapatának emblémáját, kattintson a **logo ikon**alatt található **Tallózás**gombra.

    e. A **Felhasználónév attribútum** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. A **megjelenítendő név** szövegmezőbe írja be a konfiguráció nevét.

    g. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a TOPdesk-Secure szolgáltatáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **TOPdesk – biztonságos**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **TOPdesk-Secure**elemet.

    ![TOPdesk – biztonságos hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-topdesk---secure-test-user"></a>TOPdesk létrehozása – biztonságos teszt felhasználó

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a TOPdesk-biztonságba, a TOPdesk-Secure-ben kell kiépíteni őket.  
TOPdesk-Secure esetén a kiépítés manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **TOPdesk-Secure** céges webhelyre rendszergazdaként.

2. A felső menüben kattintson a **TOPdesk \> új \> támogatási fájlok \> operátor**elemre.

    ![Üzemeltető](./media/topdesk-secure-tutorial/ic790610.png "Művelet")

3. Az **új operátor** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Új operátor](./media/topdesk-secure-tutorial/ic790611.png "Új operátor")

    a. Kattintson az **általános** fülre.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    c. Válasszon egy **helyet a fiók** számára a **hely** szakaszban.

    d. A **TOPdesk bejelentkezési** szakasz **bejelentkezési név** szövegmezőbe írja be a felhasználó bejelentkezési nevét.

    e. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> A TOPdesk által biztosított bármely más TOPdesk-Secure felhasználóifiók-létrehozási eszközt vagy API-t használhat a HRE felhasználói fiókjainak kiépítéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a TOPdesk-Secure csempére kattint, automatikusan be kell jelentkeznie a TOPdesk-Secure szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


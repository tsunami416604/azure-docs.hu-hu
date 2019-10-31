---
title: 'Oktatóanyag: Azure Active Directory-integráció a Palo Alto Networks szolgáltatással – rendszergazdai felhasználói felület | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Palo Alto Networks – rendszergazdai felhasználói felület között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c77678c20cb9eed01e2cfa86014963f453a4839
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160184"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Oktatóanyag: Azure Active Directory a Palo Alto Networks-integrációval – rendszergazdai felhasználói felület

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Palo Alto Networks-admin felhasználói felületet Azure Active Directory (Azure AD) használatával.
A Palo Alto Networks integrálása – a rendszergazdai KEZELŐFELÜLET az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja a Palo Alto Networks-admin felhasználói felületét.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Palo Alto Networks-rendszergazdai felhasználói felületre (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Palo Alto Networks-rendszergazdai felhasználói felülettel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Palo Alto Networks – rendszergazdai felhasználói felület egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Palo Alto Networks – a rendszergazdai KEZELŐFELÜLET támogatja az **SP** által kezdeményezett SSO-t
* Palo Alto Networks – a rendszergazda felhasználói FELÜLETe **a** felhasználók üzembe helyezését támogatja

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Palo Alto Networks – rendszergazdai felhasználói felület hozzáadása a katalógusból

A Palo Alto Networks-admin felhasználói felület az Azure AD-be való integrálásának konfigurálásához a gyűjteményből a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a Palo Alto Networks-admin felhasználói felületet.

**A Palo Alto Networks-admin felhasználói felület a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Palo Alto Networks-admin felhasználói felület**, válassza a **Palo Alto Networks-admin felhasználói felület** lehetőséget az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Palo Alto Networks – rendszergazdai KEZELŐFELÜLET az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Palo Alto Networks-admin felhasználói felületen konfigurálhatja és tesztelheti a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a hozzá tartozó, a Palo Alto hálózatokban található felhasználó közötti kapcsolati kapcsolat szükséges.

Az Azure AD egyszeri bejelentkezés a Palo Alto Networks-rendszergazdai KEZELŐFELÜLETtel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Palo Alto Networks – rendszergazdai felhasználói felület egyszeri bejelentkezésének konfigurálása](#configure-palo-alto-networks---admin-ui-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Palo Alto-hálózatok létrehozása – rendszergazda felhasználói felület tesztelése](#create-palo-alto-networks---admin-ui-test-user)** – a Britta Simon-nek a Palo Alto Networks-ben található, a felhasználó Azure ad-képviseletéhez kapcsolódó rendszergazdai felhasználói felületén.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a Palo Alto Networks-rendszergazdai KEZELŐFELÜLETtel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Palo Alto Networks-admin UI** Application Integration lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Palo Alto Networks – rendszergazdai KEZELŐFELÜLETi tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<Customer Firewall FQDN>/php/login.php`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. A **Válasz URL-címe** szövegmezőbe írja be a következő formátumban az előállítási szolgáltatás (ACS) URL-címét: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel a kapcsolatot a [Palo Alto Networks-admin felhasználói felület ügyfélszolgálatával](https://support.paloaltonetworks.com/support) , hogy lekérje ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Palo Alto Networks – a rendszergazdai KEZELŐFELÜLETi alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Mivel az attribútumok értékei csak példaként szolgálnak, a *Felhasználónév* és a *adminrole*megfelelő értékeit képezi le. Van egy másik opcionális attribútum is, a *accessdomain*, amely a tűzfal adott virtuális rendszereihez való rendszergazdai hozzáférés korlátozására szolgál.
   >

6. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Név |  Forrás attribútum|
    | --- | --- |
    | felhasználónév | User. userPrincipalName |
    | adminrole | customadmin |
    | | |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Az attribútumokkal kapcsolatos további információkért tekintse meg a következő cikkeket:
    > * [Rendszergazdai szerepkörű rendszergazda felhasználói felület (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Eszköz-hozzáférési tartomány felügyeleti felhasználói felülethez (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

8. A **Palo Alto Networks-admin felhasználói felület beállítása** szakaszban másolja ki a megfelelő URL-címet (ka) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>A Palo Alto Networks konfigurálása – rendszergazdai felhasználói felület egyszeri bejelentkezés

1. Nyissa meg a Palo Alto Networks Firewall rendszergazdai felhasználói felületét rendszergazdaként egy új ablakban.

2. Válassza az **eszköz** fület.

    ![Az eszköz lap](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. A bal oldali ablaktáblán válassza ki az **SAML-identitás szolgáltatója**elemet, majd az **Importálás** elemre kattintva importálja a metaadat-fájlt.

    ![A Metaadatok importálása gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Az **SAML azonosító szolgáltatói kiszolgáló profiljának importálása** ablakban tegye a következőket:

    ![A "SAML-azonosító a szolgáltatói kiszolgáló profiljának importálása" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. A **profil neve** mezőben adjon meg egy nevet (például **AzureAD felügyeleti felhasználói felület**).
    
    b. Az **identitás-szolgáltató metaadatai**területen válassza a **Tallózás**lehetőséget, majd válassza ki a Azure Portal korábban letöltött metaadat. xml fájlt.
    
    c. Törölje az **identitás-szolgáltatói tanúsítvány** ellenőrzése jelölőnégyzet jelölését.
    
    d. Kattintson az **OK** gombra.
    
    e. Ha a tűzfalon szeretné véglegesíteni a konfigurációkat **, válassza a**végrehajtás lehetőséget.

5. A bal oldali ablaktáblán válassza ki az **SAML-identitás szolgáltatója**elemet, majd válassza ki az SAML identitás-szolgáltatói profilt (például **AzureAD felügyeleti felhasználói felület**), amelyet az előző lépésben hozott létre.

    ![Az SAML-identitás szolgáltatójának profilja](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. A **SAML Identity Provider kiszolgáló profilja** ablakban tegye a következőket:

    ![A "SAML identitás-szolgáltatói kiszolgáló profilja" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Az **Identity Provider slo URL-címe** mezőben cserélje le a korábban importált slo URL-címet a következő URL-címre: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Kattintson az **OK** gombra.

7. A Palo Alto Networks tűzfal rendszergazdai felhasználói felületén válassza az **eszköz**lehetőséget, majd válassza a **rendszergazdai szerepkörök**lehetőséget.

8. Kattintson a **Hozzáadás** gombra.

9. A rendszergazda **szerepkör profilja** ablakban a **név** mezőbe írja be a rendszergazdai szerepkör nevét (például **fwadmin**). A rendszergazdai szerepkör nevének meg kell egyeznie az identitás-szolgáltató által elindított SAML-rendszergazdai szerepkör attribútum nevével. A rendszergazdai szerepkör neve és értéke a Azure Portal **felhasználói attribútumok** szakaszában lett létrehozva.

    ![A Palo Alto Networks-rendszergazda szerepkör konfigurálása](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. A tűzfal rendszergazdai felhasználói felületén válassza az **eszköz**lehetőséget, majd válassza a **hitelesítési profil**lehetőséget.

11. Kattintson a **Hozzáadás** gombra.

12. A **hitelesítési profil** ablakban tegye a következőket: 

    ![A "hitelesítési profil" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. A **név** mezőben adjon meg egy nevet (például **AzureSAML_Admin_AuthProfile**).

    b. A **típus** legördülő listában válassza az **SAML**elemet. 

    c. A **identitásszolgáltató-kiszolgáló profilja** legördülő listában válassza ki a megfelelő SAML Identity Provider-kiszolgáló profilt (például **AzureAD felügyeleti felhasználói felület**).

    c. Jelölje be az **egyszeri kijelentkezés engedélyezése** jelölőnégyzetet.

    d. Az **adminisztrátori szerepkör attribútuma** mezőben adja meg az attribútum nevét (például **adminrole**).

    e. Válassza a **speciális** fület, majd az **engedélyezési lista**területen válassza a **Hozzáadás**lehetőséget.

    ![A Hozzáadás gomb a Speciális lapon](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Jelölje be az **összes** jelölőnégyzetet, vagy válassza ki azokat a felhasználókat és csoportokat, akikkel hitelesíteni lehet a profilt.  
    Amikor egy felhasználó hitelesíti magát, a tűzfal megfelel a társított felhasználónévnek vagy csoportnak a listában szereplő bejegyzéseknek. Ha nem ad hozzá bejegyzéseket, akkor egyetlen felhasználó sem tud hitelesíteni.

    g. Kattintson az **OK** gombra.

13. Ha engedélyezni szeretné a rendszergazdák számára az SAML SSO használatát az Azure használatával, válassza az **eszköz** > a **telepítő**lehetőséget. A **telepítés** ablaktáblán válassza a **felügyelet** fület, majd a **hitelesítési beállítások**területen válassza a **Beállítások** ("fogaskerék") gombot.

    ![A beállítások gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Válassza ki a hitelesítési profil ablakban létrehozott SAML-hitelesítési profilt (például **AzureSAML_Admin_AuthProfile**).

    ![A hitelesítési profil mező](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Kattintson az **OK** gombra.

16. A konfiguráció elvégzéséhez válassza a **commit (véglegesítés**) lehetőséget.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Palo Alto Networks-admin felhasználói felületéhez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Palo Alto Networks-admin felhasználói felület**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Palo Alto Networks-admin felhasználói felület**lehetőséget.

    ![A Palo Alto Networks-admin UI hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Palo Alto-hálózatok létrehozása – rendszergazda felhasználói felület tesztelése

Palo Alto hálózatok – a rendszergazda KEZELŐFELÜLETe támogatja az igény szerinti felhasználói üzembe helyezést. Ha a felhasználó még nem létezik, a rendszer automatikusan létrehozza a sikeres hitelesítés után. A felhasználó létrehozásához nincs szükség beavatkozásra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Palo Alto Networks-admin felhasználói felület csempére kattint, automatikusan be kell jelentkeznie a Palo Alto Networks-admin felhasználói felületére, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Oktatóanyag: A Palo Alto Networks - rendszergazdai felhasználói felület az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Palo Alto Networks - felügyeleti felhasználói Felületéhez között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82560d0767b6865dded3e14e661fe89b7132ab95
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869853"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Oktatóanyag: Az Azure Active Directory-integráció a Palo Alto Networks - felügyeleti felhasználói Felületéhez

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Palo Alto Networks - rendszergazdai felhasználói felület az Azure Active Directoryval (Azure AD).
Futtató Palo Alto Networks - rendszergazdai felhasználói felület az Azure AD integrálása nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Palo Alto Networks - rendszergazdai felhasználói felület az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a Palo Alto Networks - rendszergazdai felhasználói felület (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Palo Alto Networks - rendszergazdai felhasználói felület, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Futtató Palo Alto Networks - felügyeleti felhasználói Felületéhez egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Palo Alto Networks - felügyeleti felhasználói Felületéhez **SP** által kezdeményezett egyszeri bejelentkezés
* Támogatja a Palo Alto Networks - felügyeleti felhasználói Felületéhez **igény szerinti** felhasználók átadása

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Palo Alto Networks - hozzáadása a katalógusból felügyeleti felhasználói Felületéhez

A Palo Alto Networks - rendszergazdai felhasználói felület az Azure AD-be-integráció konfigurálása kell hozzáadnia a Palo Alto Networks - felügyeleti felhasználói Felületéhez a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Palo Alto Networks - felügyeleti felhasználói Felületéhez a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Palo Alto Networks - felügyeleti felhasználói Felületéhez**, jelölje be **Palo Alto Networks - felügyeleti felhasználói Felületéhez** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Futtató Palo Alto Networks - felügyeleti felhasználói Felületéhez a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Palo Alto Networks - felügyeleti felhasználói Felületéhez alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés munkáját, egy Azure AD-felhasználót és a kapcsolódó felhasználó a Palo Alto Networks - hivatkozás kapcsolatának felügyeleti felhasználói Felületéhez kell létrehozni.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a Palo Alto Networks - felügyeleti felhasználói Felületéhez, szüksége a következő építőelemeit végrehajtásához:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a Palo Alto Networks - rendszergazdai felhasználói felület egyszeri bejelentkezés](#configure-palo-alto-networks---admin-ui-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre futtató Palo Alto Networks - felügyeleti felhasználói Felületéhez tesztfelhasználó](#create-palo-alto-networks---admin-ui-test-user)**  – van egy Britta Simon megfelelője a Palo Alto Networks - rendszergazdai felhasználói felület, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

A Palo Alto Networks - felügyeleti felhasználói Felületéhez, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Palo Alto Networks - felügyeleti felhasználói Felületéhez** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Palo Alto Networks - rendszergazdai felhasználói felület tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<Customer Firewall FQDN>/php/login.php`

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a helyességi feltétel fogyasztói Service (ACS) URL-cím a következő formátumban: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [Palo Alto Networks - rendszergazdai felhasználói felület ügyfél-támogatási csapatának](https://support.paloaltonetworks.com/support) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Palo Alto Networks - rendszergazdai felhasználói felület alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumnak. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Az attribútum értékei csak példaként szolgálnak, mivel képezze le a megfelelő értékeket *felhasználónév* és *adminrole*. Nem kötelező attribútum egy másik, *accessdomain*, amely az adott virtuális rendszereken a tűzfalon való rendszergazdai hozzáférés korlátozása szolgál.
   >

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) |  Adatforrás-attribútum|
    | --- | --- |
    | felhasználónév | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Az attribútumokkal kapcsolatos további információkért tekintse meg a következő cikkeket:
    > * [Rendszergazdai felhasználói felület (adminrole) rendszergazdai szerepkör profilja](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Eszköz hozzáférési tartományi rendszergazdai felhasználói felület (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

8. Az a **állítsa be a Palo Alto Networks - felügyeleti felhasználói Felületéhez** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Palo Alto Networks - felügyeleti felhasználói Felületéhez egyszeri bejelentkezés konfigurálása

1. Egy új ablakban rendszergazdaként nyissa meg a Palo Alto hálózatok tűzfal felügyeleti felhasználói Felületéhez.

2. Válassza ki a **eszköz** fülre.

    ![Az eszközök lapon](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. A bal oldali panelen válassza ki a **identitásszolgáltató SAML**, majd válassza ki **importálása** a metaadatait tartalmazó fájl importálása.

    ![Az importálás metaadatok szolgáló gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Az a **SAML azonosítása szolgáltató kiszolgáló profilok importálása** ablakban tegye a következőket:

    ![Az "SAML azonosítása szolgáltató kiszolgáló profil importálása" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Az a **profilnév** mezőben adjon meg egy nevet (például **Azure ad felügyeleti felhasználói Felületéhez**).
    
    b. Alatt **Identity Provider metaadatok**, jelölje be **Tallózás**, és válassza ki a korábban az Azure Portalról letöltött metadata.xml fájlt.
    
    c. Törölje a **identitás társzolgáltató tanúsítványának ellenőrzése** jelölőnégyzetet.
    
    d. Kattintson az **OK** gombra.
    
    e. A konfigurációk a tűzfalon véglegesítéséhez kattintson **véglegesítési**.

5. A bal oldali panelen válassza ki a **identitásszolgáltató SAML**, majd válassza ki a SAML-alapú identitás-szolgáltató profilban (például **Azure ad felügyeleti felhasználói Felületéhez**), amely az előző lépésben létrehozott.

    ![The SAML Identity Provider Profile](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Az a **SAML-alapú identitás szolgáltató kiszolgáló profilban** ablakban tegye a következőket:

    ![The "SAML Identity Provider Server Profile" window](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Az a **Identity Provider SLO URL-cím** mezőben cserélje le a korábban importált SLO URL-cím a következő URL-címe: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Kattintson az **OK** gombra.

7. Válassza ki a Palo Alto hálózatok tűzfal felügyeleti felhasználói Felületéhez, **eszköz**, majd válassza ki **rendszergazdai szerepkörök**.

8. Válassza ki a **Hozzáadás** gombra.

9. Az a **rendszergazdai szerepkör profil** ablakban, a a **neve** adja meg a rendszergazda szerepkör nevét (például **fwadmin**). A rendszergazda szerepkör nevének egyeznie kell az SAML-rendszergazdai szerepkör attribútum nevét, amely az identitásszolgáltató által küldött. A rendszergazda szerepkör nevét és értékét létrejöttek **felhasználói attribútumok** szakaszban az Azure Portalon.

    ![Rendszert futtató Palo Alto hálózatok rendszergazdai szerepkör konfigurálása](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Válassza ki a tűzfalat felügyeleti felhasználói Felületéhez, **eszköz**, majd válassza ki **hitelesítési profilt**.

11. Válassza ki a **Hozzáadás** gombra.

12. Az a **hitelesítési profilt** ablakban tegye a következőket: 

    ![A "Hitelesítés profil" ablak](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Az a **neve** mezőben adjon meg egy nevet (például **AzureSAML_Admin_AuthProfile**).

    b. Az a **típus** legördülő listában válassza **SAML**. 

    c. Az a **IdP-Server-profil** legördülő listára, válassza ki a megfelelő identitás-szolgáltató kiszolgáló SAML-profilt (például **Azure ad felügyeleti felhasználói Felületéhez**).

    c. Válassza ki a **egyszeri kijelentkezési engedélyezése** jelölőnégyzetet.

    d. Az a **rendszergazdai szerepkör attribútum** mezőben megadhatja azon attribútum nevét (például **adminrole**).

    e. Válassza ki a **speciális** fülre, majd a **engedélyezési lista**, jelölje be **Hozzáadás**.

    ![A Hozzáadás gombra a Speciális lapon](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Válassza ki a **összes** jelölje be a jelölőnégyzetet, vagy válassza ki a felhasználókat és csoportokat, amelyek ezzel a profillal hitelesítheti.  
    Amikor egy felhasználó hitelesíti magát, a tűzfal megegyezik a társított felhasználónév vagy a csoport ebben a listában lévő bejegyzéseket ellen. Ha nem ad hozzá bejegyzéseket, nem a felhasználók hitelesíthetők.

    g. Kattintson az **OK** gombra.

13. Válassza ki ahhoz, hogy a rendszergazdák számára, hogy az Azure SAML SSO, **eszköz** > **telepítő**. Az a **telepítő** panelen válassza a **felügyeleti** lapon majd **hitelesítési beállítások**, jelölje be a **beállítások** ("fogaskerék") gomb .

    ![A beállítások gomb](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Válassza ki a SAML-hitelesítés profilt, amelyet a hitelesítési profilt ablak (például **AzureSAML_Admin_AuthProfile**).

    ![A hitelesítési profilt mező](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Kattintson az **OK** gombra.

16. A konfiguráció véglegesítéséhez kattintson **véglegesítési**.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Palo Alto Networks - rendszergazdai felhasználói felület Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Palo Alto Networks - felügyeleti felhasználói Felületéhez**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Palo Alto Networks - felügyeleti felhasználói Felületéhez**.

    ![A futtató Palo Alto Networks - felügyeleti felhasználói Felületéhez hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Futtató Palo Alto Networks - felügyeleti felhasználói Felületéhez tesztfelhasználó létrehozása

Palo Alto Networks - rendszergazdai felhasználói felület támogatja a just-in-time-felhasználók létrehozásának. Ha a felhasználó még nem létezik, automatikusan létrejön a rendszer a sikeres hitelesítés után. Semmit nem kell az, hogy a felhasználó létrehozásához.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A Palo Alto Networks - a hozzáférési panelen felügyeleti felhasználói Felületéhez csempére kattintva, kell lennie automatikusan bejelentkezett a Palo Alto Networks - felügyeleti felhasználói Felületéhez, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

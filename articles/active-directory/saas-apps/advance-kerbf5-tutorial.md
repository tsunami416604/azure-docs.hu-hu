---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az F5-mel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és F5 között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e8e6a9eb50d15e6e4eb788fefe13d7e5983a834
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166057"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az F5-szel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az F5-et Azure Active Directory (Azure AD) használatával. Az F5 és az Azure AD integrálásával a következőket teheti:

* Az F5-hez hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek az F5-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* F5 egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* F5 **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés támogatása
* Az F5 SSO három különböző módon konfigurálható.

- [Az F5 egyszeri bejelentkezés konfigurálása a speciális Kerberos-alkalmazáshoz](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Az F5 egyszeri bejelentkezés konfigurálása a fejléc-alapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezés konfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Az F5 hozzáadása a gyűjteményből

Az F5 Azure AD-integrációjának konfigurálásához az F5 billentyűt kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **F5** kifejezést.
1. Válassza az **F5** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése F5 rendszerhez

Konfigurálja és tesztelje az Azure AD SSO-t az F5 használatával egy **B. Simon**nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az F5 billentyűvel.

Az Azure AD SSO az F5 használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[F5-SSO konfigurálása](#configure-f5-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre F5 test User](#create-f5-test-user)** – ha a felhasználó Azure ad-képviseletéhez társított B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **F5** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az alapszintű **SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<YourCustomFQDN>.f5.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<YourCustomFQDN>.f5.com/`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A következő értékek megszerzéséhez lépjen kapcsolatba az [F5 ügyfél-támogatási csapattal](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML** -fájlját, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az **F5 beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az F5 elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **F5**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-f5-sso"></a>Az F5 SSO konfigurálása

- [Az F5 egyszeri bejelentkezés konfigurálása a fejléc-alapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezés konfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Az F5 egyszeri bejelentkezés konfigurálása a speciális Kerberos-alkalmazáshoz

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az F5 (Advanced Kerberos) vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Importálnia kell a metaadat-tanúsítványt az F5 (Advanced Kerberos) rendszerbe, amelyet később a telepítési folyamat során fog használni. Nyissa meg a **System > Certificate management > forgalom-tanúsítványkezelő > > SSL-tanúsítványok listáját**. Kattintson a jobb oldali sarokban található **Importálás** elemre.

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Az SAML-IDENTITÁSSZOLGÁLTATÓ beállításához lépjen a **hozzáférés > összevonási > SAML-szolgáltató > > létrehozása metaadatokból elemre**.

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure05.png)
 
1. A 3. feladatból feltöltött tanúsítvány meghatározása

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure07.png)

 1. Az SAML SP beállításához nyissa meg a **hozzáférés > összevonási > SAML-szolgáltatás Összevonása > helyi SP-szolgáltatások > létrehozás**lehetőséget.

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Kattintson az **OK** gombra.

1. Válassza ki az SP-konfigurációt, és kattintson a **bind/szüntesse identitásszolgáltató-összekötők**elemre.

     ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Kattintson az **új sor hozzáadása** elemre, és válassza ki az előző lépésben létrehozott **külső identitásszolgáltató** -összekötőt.

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure10.png)
 
1. A Kerberos SSO konfigurálásához **hozzáférés > egyszeri bejelentkezés > Kerberos**

    >[!Note]
    >Ehhez létre kell hoznia és meg kell adni a Kerberos-delegálási fiókot. Tekintse át a KCD szakaszt (lásd: függelék a változó hivatkozásokhoz)

    • Felhasználónév forrása`session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Felhasználói tartomány forrása`session.logon.last.domain`

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure11.png)

1. Hozzáférési profil konfigurálásához nyissa meg **> profil/szabályzatok > hozzáférési profil (munkamenetenkénti házirendek) című szakaszt**.

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure16.png)
 
    * Session. Logon. Last. usernameUPN kifejezés {[mcget {Session. SAML. Last. Identity}]}

    * Session. ad. lastactualdomain TEXT-bemutató. élő

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName =% {Session. Logon. Last. usernameUPN})

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure19.png)

    * Session. Logon. Last. username kifejezés {"[mcget {Session. ad. Last. attr. sAMAccountName}]"}

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {Session. Logon. Last. username}
    * mcget {Session. Logon. Last. password}

1. Új csomópont hozzáadásához lépjen a **helyi forgalom > csomópontok > csomópontok listája > +** .

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Új készlet létrehozásához nyissa meg a **helyi forgalom > készletek > készlet lista > létrehozás**lehetőséget.

     ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure22.png)

 1. Új virtuális kiszolgáló létrehozásához nyissa meg a **helyi forgalom > Virtual servers > virtuális kiszolgálók listáját > +** .

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure23.png)

1. Itt adhatja meg az előző lépésben létrehozott hozzáférési profilt.

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>A Kerberos-delegálás beállítása 

>[!Note]
>További részletekért tekintse meg a következőt: [](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **1. lépés: Delegálási fiók létrehozása**

    * Példa a tartománynévre: főverzió. élő Sam-fiók neve: Big-ipuser

    * New-ADUser-Name "APM delegálási fiók"- host/big-ipuser.superdemo.live@superdemo.live userPrincipalName-sAMAccountName "Big-ipuser"-PasswordNeverExpires $True-enabled $True-AccountPassword (read-Host-AsSecureString "password! 1234")

* **2. lépés: SPN beállítása (az APM delegálási fiókján)**

    *  Példa Setspn – A Host/Big-ipuser. redemo. Live Big-ipuser

* **3. lépés: SPN-delegálás (a App Service fiókhoz)**

    * Állítsa be a megfelelő delegálást az F5 delegálási fiókhoz.
    * Az alábbi példában az APM delegálási fiók konfigurálva van a FRP-App1. redemo. Live alkalmazás KCD.

        ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure25.png)

1. Adja meg a fenti hivatkozási dokumentumban említett részleteket [](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Függelék – SAML – F5 BIG-IP változó-hozzárendelések az alábbiakban láthatók:

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5 (speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure27.png) 

1. Az alábbi lista az alapértelmezett SAML-attribútumok teljes listáját tartalmazza. A GivenName a következő karakterláncot használja.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6. Session. SAML. Last. assertionID | _9a4e4ddd-148f-45c4-b959-f4d148172e00 |
| eb46b6b6. Session. SAML. Last. assertionIssueInstant  | 2019-06-16T19:18:03.054 Z |
| eb46b6b6. Session. SAML. Last. assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/objectidentifier | 90d5f0e5-8f46-4bfd-b40f-ec973d00fcb7 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/tenantid | e6abffcf-4d23-4388-91c2-bfdfcbb1530c |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | user0@superdemo.live |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name | user0@superdemo.live |
| eb46b6b6.session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6. Session. SAML. Last. hallgatóság | `https://kerbapp.superdemo.live` |
| eb46b6b6. Session. SAML. Last. authNContextClassRef | urn: Oasis: nevek: TC: SAML: 2.0: AC: classs: Password |
| eb46b6b6. Session. SAML. Last. authNInstant | 2019-06-16T19:18:00.318 Z |
| eb46b6b6. Session. SAML. Last. Identity | user0@superdemo.live |
| eb46b6b6. Session. SAML. Last. inResponseTo | _b9c67faa63a224d7a63f4f3cbb09f78dc05fab |
| eb46b6b6. Session. SAML. Last. nameIDValue | user0@superdemo.live |
| eb46b6b6. Session. SAML. Last. nameIdFormat | urn: Oasis: nevek: TC: SAML: 1.1: nameid-Format: emailAddress |
| eb46b6b6. Session. SAML. Last. responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. responseId | _a1eca95a-6c41-449e-bb53-1477ba106470 |
| eb46b6b6. Session. SAML. Last. responseIssueInstant | 2019-06-16T19:18:03.070 Z |
| eb46b6b6. Session. SAML. Last. responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. Session. SAML. Last. result | 1 |
| eb46b6b6. Session. SAML. Last. samlVersion | 2.0 |
| eb46b6b6. Session. SAML. Last. sessionIndex | _9a4e4ddd-148f-45c4-b959-f4d148172e00 |
| eb46b6b6. Session. SAML. Last. statusValue | urn: Oasis: nevek: TC: SAML: 2.0: Status: sikeres |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataNotOnOrAfter | 2019-06-16T19:23:03.054 Z |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. subjectConfirmMethod | urn: Oasis: nevek: TC: SAML: 2.0: cm: tulajdonos |
| eb46b6b6. Session. SAML. Last. validityNotBefore | 2019-06-16T19:13:03.054 Z |
| eb46b6b6. Session. SAML. Last. validityNotOnOrAfter | 2019-06-16T20:13:03.054 Z |

### <a name="create-f5-test-user"></a>F5 test User létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az F5 billentyűvel. A felhasználók az F5 platformon való hozzáadásához az [F5 ügyfél-támogatási csapat](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) használható. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen az F5 csempére kattint, automatikusan be kell jelentkeznie az F5-be, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az F5 kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Az F5 egyszeri bejelentkezés konfigurálása a fejléc-alapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezés konfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)


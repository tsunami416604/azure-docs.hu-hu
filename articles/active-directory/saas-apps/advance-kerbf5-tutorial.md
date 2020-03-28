---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az F5-tel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az F5 között.
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
ms.date: 11/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984fd0c7946a50922315269c87e08b1c35b74348
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74074758"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az F5-tel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az F5-öt az Azure Active Directoryval (Azure AD). Ha integrálja az F5-öt az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az F5-höz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve f5 az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* F5 egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az F5 támogatja az **SP és az IDP** által kezdeményezett sso-t
* Az F5 SSO három különböző módon konfigurálható.

- [Az F5 egyszeri bejelentkezéskonfigurálása speciális Kerberos-alkalmazáshoz](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Az F5 egyszeri bejelentkezésének konfigurálása fejlécalapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezéskonfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>F5 hozzáadása a galériából

Az F5 Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az F5-öt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **F5** értéket a keresőmezőbe.
1. Válassza az **F5** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az F5-hez

Konfigurálja és tesztelje az Azure AD SSO-t az F5-tel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az F5-ben.

Az Azure AD SSO f5-tel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az F5-SSO](#configure-f5-sso)** -az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre F5 tesztfelhasználót](#create-f5-test-user)** – b.Simon megfelelője az F5-ben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **F5** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YourCustomFQDN>.f5.com/`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<YourCustomFQDN>.f5.com/`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az F5 ügyféltámogatási csapatával](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) az értékek lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **F5 beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t az F5-höz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **F5**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-f5-sso"></a>F5 sso konfigurálása

- [Az F5 egyszeri bejelentkezésének konfigurálása fejlécalapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezéskonfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Az F5 egyszeri bejelentkezéskonfigurálása speciális Kerberos-alkalmazáshoz

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az F5 (Advanced Kerberos) vállalati webhelyére, és hajtsa végre az alábbi lépéseket:

1. Importálnia kell a metaadat-tanúsítványt az F5 (Advanced Kerberos) rendszerbe, amelyet a rendszer a telepítési folyamat későbbi részében fog használni. Nyissa meg **a Rendszer> tanúsítványkezelés > forgalomvédelem >> az SSL-tanúsítványlista lapot.** Kattintson **az Import** a jobb oldali sarokban.

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Az SAML IDP beállításához nyissa meg **az Access > összevonási > az SAML-szolgáltató > > létrehozása metaadatokból**című >.

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure05.png)
 
1. A 3.

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure07.png)

 1. Az SAML SP beállításához nyissa meg **az Access > Összevonás> az SAML szolgáltatásösszevonás > > a Helyi SP-szolgáltatások > létrehozás a.**

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Kattintson az **OK** gombra.

1. Válassza ki az SP konfigurációját, és kattintson a **Kötés/UnBind IdP-összekötők elemre.**

     ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Kattintson az **Új sor hozzáadása** gombra, és válassza ki az előző lépésben létrehozott külső **IdP-összekötőt.**

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure10.png)
 
1. A Kerberos egyszeri bejelentkezés konfigurálásához az **Access > az egyszeri bejelentkezés > a Kerberos**

    >[!Note]
    >Létre kell hoznia és meg kell adnia a Kerberos delegálási fiókot. KCD-szakasz hivatkozása (a függelék hivatkozása változó referenciákra)

    • Felhasználónév forrása`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Felhasználói birodalom forrása`session.logon.last.domain`

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure11.png)

1. Az Access-profil konfigurálásához **> A profil-/házirendek > Access-profil (munkamenet-házirendenként)**.

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain TEXT superdemo.live

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Új csomópont hozzáadásához nyissa meg **a Helyi forgalmi > csomópontok > csomópontlista > +**.

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Új készlet létrehozásához nyissa meg **a Helyi forgalom > a készletek > a készletlistát > létrehozás a lapot.**

     ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure22.png)

 1. Új virtuális kiszolgáló létrehozásához nyissa meg **a Virtuális kiszolgálók > a virtuális kiszolgálók helyi > > +** című helyi forgalmi >.

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure23.png)

1. Adja meg az előző lépésben létrehozott hozzáférési profilt.

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>A Kerberos-delegálás beállítása 

>[!Note]
>További részletek [itt](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **1. lépés: Delegálási fiók létrehozása**

    * Példa
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **2. lépés: Az spn beállítása (az APM delegálási fiókján)**

    *  Példa
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **3. lépés: SPN-delegálás (az App Service-fiókhoz)**

    * Állítsa be a megfelelő delegálást az F5 delegálási fiókhoz.
    * Az alábbi példában az APM delegálási fiók konfigurálása folyamatban van a KCD-hez az FRP-App1.superdemo.live alkalmazáshoz.

        ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure25.png)

1. Adja meg a fenti referenciadokumentumban említett [részleteket,](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Függelék- SAML – F5 BIG-IP változó leképezések alább látható:

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5 (Speciális Kerberos) konfiguráció](./media/advance-kerbf5-tutorial/configure27.png) 

1. Az alábbiakban az alapértelmezett SAML attribútumok teljes listája látható. A GivenName a következő karakterláncot használja.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | felhasználó0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | felhasználó0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oázis:nevek:tc:SAML:2.0:ac:classes:Jelszó |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormátum | urn:oázis:nevek:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oázis:nevek:tc:SAML:2.0:állapot:Siker |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMódszer | urn:oázis:nevek:tc:SAML:2,0:cm:hordozó |
| eb46b6b6.session.saml.last.validityNotElőtte | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>F5 tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót az F5-ben. Az [F5 ügyféltámogatási csapatával](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) együttműködve adja hozzá a felhasználókat az F5 platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az F5 csempére kattint, automatikusan be kell jelentkeznie arra az F5-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az F5-öt az Azure AD-vel](https://aad.portal.azure.com/)

- [Az F5 egyszeri bejelentkezésének konfigurálása fejlécalapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezéskonfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)


---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a FortiGate SSL VPN használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és FortiGate SSL VPN között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299711"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a FortiGate SSL VPN-lel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a FortiGate SSL VPN-t Azure Active Directory (Azure AD) használatával. Ha integrálja a FortiGate SSL VPN-t az Azure AD-vel, a következőket teheti:

* A FortiGate SSL VPN-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SSL VPN FortiGate az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* FortiGate SSL VPN egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A FortiGate SSL VPN támogatja az **SP** által kezdeményezett SSO-t
* A FortiGate SSL VPN konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>FortiGate SSL VPN hozzáadása a katalógusból

A FortiGate SSL VPN Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a FortiGate SSL VPN-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **FortiGate SSL VPN** kifejezést a keresőmezőbe.
1. Válassza az **FORTIGATE SSL VPN** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Azure AD SSO konfigurálása és tesztelése a FortiGate SSL VPN-hez

Konfigurálja és tesztelje az Azure AD SSO-t a FortiGate SSL VPN használatával egy **B. Simon**nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a FortiGate SSL VPN-ben.

Az Azure AD SSO FortiGate SSL VPN-sel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[FORTIGATE SSL VPN SSO konfigurálása](#configure-fortigate-ssl-vpn-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **FORTIGATE SSL VPN-teszt felhasználó létrehozása** – ha a FortiGate SSL VPN-en található, a felhasználó Azure ad-képviseletéhez csatolt B. Simon-ügyféllel rendelkezik.
1. **[SSO tesztelése](#test-single-sign-on)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **FortiGate SSL VPN-** alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<FQDN>/remote/login`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<FQDN>/remote/saml/metadata`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://><FQDN/remote/saml/login`

    d. A **kijelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-cím, azonosító, válasz URL-cím és kijelentkezési URL-cím alapján. Az értékek megszerzéséhez forduljon a [FORTIGATE SSL VPN-ügyfélszolgálati csapatához](mailto:tac_amer@fortinet.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A FortiGate SSL VPN-alkalmazás meghatározott formátumban várja az SAML-kijelentéseket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a FortiGate SSL VPN-alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name |  Forrás attribútum|
    | ------------ | --------- |
    | username | User. userPrincipalName |
    | csoport | User. groups |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **FORTIGATE SSL VPN beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a FortiGate SSL VPN-hez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **FORTIGATE SSL VPN**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-a-security-group-for-the-test-user"></a>Biztonsági csoport létrehozása a tesztelési felhasználó számára

Ebben a szakaszban egy biztonsági csoportot hoz létre Azure Active Directory a tesztelési felhasználó számára. Ezt a biztonsági csoportot a FortiGate fogja használni a felhasználói hálózati hozzáférés biztosításához a VPN-en keresztül.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, majd válassza a **csoportok**lehetőséget.
1. Válassza az **új csoport** lehetőséget a képernyő tetején.
1. Az **új csoport** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **csoport típusa** mezőben válassza a **Biztonság**elemet.
   1. A **Név** mezőbe írja a következőt: `FortiGateAccess`.
   1. A **csoport leírása** mezőbe írja be a értéket `Group for granting FortiGate VPN access` .
   1. Az **Azure ad-szerepkörökhöz hozzá lehet rendelni a csoport (előzetes verzió)** beállításait, válassza a **nem**lehetőséget.
   1. A **tagság típusa** mezőben válassza a **hozzárendelt**elemet.
   1. A **tagok**területen válassza a **nincs kiválasztott tag**elemet.
   1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
   1. Válassza a **Létrehozás** lehetőséget.
1. Miután visszatért a **csoportok** panelre Azure Active Directoryban, keresse meg a FortiGate- **hozzáférési** csoportot, és jegyezze fel az **objektum azonosítóját** későbbi használatra.

## <a name="configure-fortigate-ssl-vpn-sso"></a>FortiGate SSL VPN SSO konfigurálása

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>A Base64 SAML-tanúsítvány feltöltése a FortiGate készülékre

A FortiGate alkalmazás SAML-konfigurációjának a bérlőn való befejezése után letöltötte a Base64 kódolású SAML-tanúsítványt. Ezt fel kell tölteni a FortiGate készülékre:

1. Jelentkezzen be a FortiGate-berendezés felügyeleti portálján.
1. A bal oldali menüben kattintson a System ( **rendszeren**) elemre.
1. A **rendszerben**kattintson a **tanúsítványok**elemre.
1. Kattintson **Import**a  ->  **távoli tanúsítvány**importálása elemre.
1. Tallózással keresse meg a FortiGate alkalmazás üzembe helyezését az Azure-bérlőn, válassza ki a tanúsítványt, majd kattintson az **OK** gombra.

A tanúsítvány feltöltése után jegyezze fel a nevét a **System**  >  **rendszertanúsítványok**  >  **távoli tanúsítványa**alatt. Alapértelmezés szerint REMOTE_Cert_**n** lesz, ahol **n** egy egész érték.

### <a name="perform-fortigate-command-line-configuration"></a>FortiGate-parancssori konfiguráció végrehajtása

A következő lépésekhez az Azure kijelentkezési URL-címének konfigurálására van szükség. Ez az URL-cím kérdőjelet (?) tartalmaz. A karakter sikeres elküldéséhez speciális lépések szükségesek. A lépéseket nem lehet végrehajtani a FortiGate CLI-konzolról. Ehelyett hozzon létre egy SSH-munkamenetet a FortiGate-alkalmazáshoz egy olyan eszközzel, mint a Putty. Ha a FortiGate készülék egy Azure-beli virtuális gép, a következő lépéseket hajthatja végre az Azure-beli virtuális gép soros konzoljának használatával.

A lépések végrehajtásához a korábban rögzített értékeket kell megadnia:

- Entitás azonosítója
- Válasz URL-cím
- Kijelentkezési URL-cím
- Azure bejelentkezési URL-cím
- Azure AD-azonosító
- Azure kijelentkezési URL-címe
- Base64 SAML-tanúsítvány neve (REMOTE_Cert_N)

1. Hozzon létre egy SSH-munkamenetet a FortiGate-alkalmazáshoz, és jelentkezzen be egy FortiGate-rendszergazdai fiókkal.
1. Hajtsa végre a következő parancsokat:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > Az **Azure kijelentkezési URL-címe** tartalmaz egy `?` karaktert. Meg kell adnia egy speciális billentyűkombinációt, hogy helyesen adja meg a FortiGate soros konzoljának URL-címét. Általában az URL-cím `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > A soros konzolon az Azure kijelentkezési URL-címének megadásához írja be a következőt: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Ezután válassza a CTRL + V billentyűkombinációt, majd illessze be a többi URL-címet a sor befejezéséhez: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

### <a name="configure-fortigate-for-group-matching"></a>FortiGate konfigurálása csoport egyeztetéséhez

Ebben a szakaszban úgy konfigurálja a FortiGate, hogy felismerje annak a biztonsági csoportnak az azonosítóját, amelyben a tesztelési felhasználó található. Ez lehetővé teszi, hogy a FortiGate a csoporttagság alapján hozzon döntést a hozzáférési döntésekhez.

Ezeknek a lépéseknek a végrehajtásához szüksége lesz a korábban létrehozott **FortiGateAccess** biztonsági csoport objektum-azonosítójára.

1. Hozzon létre egy SSH-munkamenetet a FortiGate-alkalmazáshoz, és jelentkezzen be egy FortiGate-rendszergazdai fiókkal.
1. Hajtsa végre a következő parancsokat:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>FortiGate VPN-portálok és tűzfal-házirend létrehozása

Ebben a szakaszban FortiGate VPN-portálokat és tűzfal-házirendet konfigurál, amely hozzáférést biztosít a fent létrehozott biztonsági csoporthoz, **FortiGateAccess** .

A [FortiGate támogatási csapatával](mailto:tac_amer@fortinet.com) vegye fel a VPN-portálokat és a tűzfal-házirendet a FortiGate VPN-platformra. Az egyszeri bejelentkezés használata előtt ezeket a lépéseket végre kell hajtani.

## <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a FortiGate SSL VPN csempére kattint, automatikusan be kell jelentkeznie a FortiGate SSL VPN-be, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

A Microsoft és a FortiGate azt ajánlja, hogy a Fortinet VPN-ügyfelet használja a FortiClient a legjobb végfelhasználói élmény érdekében.

## <a name="additional-resources"></a>További erőforrások

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a FortiGate SSL VPN-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

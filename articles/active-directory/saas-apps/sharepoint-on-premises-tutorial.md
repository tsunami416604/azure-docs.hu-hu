---
title: 'Oktatóanyag: Azure Active Directory a helyszíni SharePoint-integrációval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a helyszíni SharePoint között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 7feb62bb3e38452a441c505107569457d7c90a3f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233451"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Oktatóanyag: a helyszíni SharePoint-integráció Azure Active Directory

Ez az oktatóanyag azt ismerteti, hogyan integrálható a helyszíni SharePoint a Azure Active Directory (Azure AD) szolgáltatással.
A helyszíni SharePoint és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a helyszíni SharePointhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a helyszíni SharePointba (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció helyszíni SharePoint-környezetbe való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* SharePoint helyszíni egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A helyszíni SharePoint az **SP** által kezdeményezett egyszeri bejelentkezést támogatja

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>A SharePoint helyszíni hozzáadása a katalógusból

A helyi SharePoint-környezet Azure AD-be való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a helyszíni SharePoint-szolgáltatást.

**Ha a katalógusból szeretné felvenni a helyszíni SharePoint-szolgáltatást, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

    > [!NOTE]   
    > Ha az elem nem érhető el, a bal oldali navigációs panel felső részén található rögzített **összes szolgáltatás** hivatkozásra kattintva is megnyithatja. A következő áttekintésben a **Azure Active Directory** hivatkozás az **Identity (identitás** ) szakaszban található, vagy a szűrő szövegmező használatával kereshető.

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **helyszíni SharePoint**kifejezést, válassza a **helyi SharePoint** lehetőséget az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Helyszíni SharePoint a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a helyszíni SharePointon a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználó és a helyszíni SharePoint-beli kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a helyszíni SharePoint-környezettel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[SharePoint helyszíni egyszeri bejelentkezésének konfigurálása](#configure-sharepoint-on-premises-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Hozzon létre egy Azure ad biztonsági csoportot a Azure Portalban](#create-an-azure-ad-security-group-in-the-azure-portal)** – az Azure ad-ben az egyszeri bejelentkezéshez új biztonsági csoportot engedélyezhet.
5. **[Hozzáférés biztosítása a helyszíni SharePoint-biztonsági csoport](#grant-access-to-sharepoint-on-premises-security-group)** számára – hozzáférés engedélyezése egy adott csoport számára az Azure ad-hez.
6. **[Rendelje hozzá az Azure ad biztonsági csoportot a Azure Portal](#assign-the-azure-ad-security-group-in-the-azure-portal)** – az adott csoport az Azure ad-hez való hozzárendeléséhez a hitelesítéshez.
7. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a helyszíni SharePoint szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **SharePoint helyszíni alkalmazás-** integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Helyszíni SharePoint-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `urn:sharepoint:federation`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon [a helyszíni SharePoint-ügyfél ügyfélszolgálatához](https://support.office.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

    > [!Note]
    > Jegyezze meg a fájl elérési útját, amelyre a tanúsítványfájl letöltötte, mert később a konfigurációhoz szükséges PowerShell-szkriptben kell használni.

6. A helyszíni **SharePoint-környezet beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint. Az **egyszeri bejelentkezési szolgáltatás URL-címéhez**használja a következő minta értékét: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ az Azure ad-előfizetés bérlői azonosítója.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    > [!NOTE]
    > A helyszíni SharePoint-alkalmazás SAML 1,1-tokent használ, így az Azure AD a (z) a SharePoint-kiszolgálótól érkező, a hitelesítés után megjelenő, a SAML 1,1-et kibocsátó kérést jogkivonat.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>A SharePoint helyszíni egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a SharePoint helyi vállalati webhelyére rendszergazdaként.

2. **Új megbízható identitás-szolgáltató konfigurálása a SharePoint Server 2016-ben**

    Jelentkezzen be a SharePoint Server 2016-kiszolgálóra, és nyissa meg a SharePoint 2016 felügyeleti rendszerhéjat. Adja meg $realm értékeit (az azonosító értékét a SharePoint helyszíni tartomány és URL-címek szakaszának Azure Portal), $wsfedurl (egyszeri bejelentkezési szolgáltatás URL-címe) és $filepath (a fájl elérési útját, amelyre a tanúsítványfájl letöltötte) Azure Portal és Futtatás az alábbi parancsokkal konfigurálhatja az új megbízható identitás-szolgáltatót.

    > [!TIP]
    > Ha most ismerkedik a PowerShell használatával, vagy szeretne többet megtudni a PowerShell működéséről, tekintse meg a [SharePoint PowerShellt](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Ezután kövesse az alábbi lépéseket az alkalmazás megbízható identitás-szolgáltatójának engedélyezéséhez:

    a. A központi felügyelet területen navigáljon a **webalkalmazás kezelése lapra** , és válassza ki azt a webalkalmazást, amelyet az Azure ad-vel szeretne védeni.

    b. A menüszalagon kattintson a **hitelesítésszolgáltatók** elemre, és válassza ki a használni kívánt zónát.

    c. Válassza ki a **megbízható identitás-szolgáltatót** , és válassza ki az imént regisztrált Szolgáltató *AzureAD*.

    d. A bejelentkezési oldal URL-címe beállításnál válassza az **Egyéni bejelentkezés lapot** , és adja meg a "/_trust/" értéket.

    e. Kattintson az **OK** gombra.

    ![A hitelesítési szolgáltató konfigurálása](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > A külső felhasználók némelyike nem fogja tudni használni ezt az egyszeri bejelentkezési integrációt, mert az UPN-hez hasonló érték tartozik, mint a `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Hamarosan lehetővé tesszük az ügyfeleknek az alkalmazás konfigurációját, hogy hogyan kezelje az UPN-t a felhasználó típusától függően. Ezt követően az összes vendégnek képesnek kell lennie az egyszeri bejelentkezés zökkenőmentes használatára a szervezeti alkalmazottak számára.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** elemre.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure AD biztonsági csoport létrehozása a Azure Portalban

1. Kattintson **Azure Active Directory > minden csoport**elemre.

    ![Azure AD biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Kattintson az **új csoport**elemre:

    ![Azure AD biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Adja meg a **csoport típusát**, a **csoport nevét**, a **csoport leírását**, a **tagság típusát**. Kattintson a nyílra a tagok kiválasztásához, majd keressen rá, vagy kattintson arra a tagra, amelyet hozzá szeretne adni a csoporthoz. Kattintson a **kiválasztás** elemre a kijelölt tagok hozzáadásához, majd kattintson a **Létrehozás**gombra.

    ![Azure AD biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Azure Active Directory biztonsági csoportok a helyszíni SharePointhoz való hozzárendeléséhez telepítenie és konfigurálnia kell a [AzureCP](https://yvand.github.io/AzureCP/) a helyszíni SharePoint-farmban, vagy fejlesztenie és konfigurálnia kell egy alternatív egyéni jogcím-szolgáltatót a sharepointhoz.  Ha nem használja a AzureCP-t, tekintse meg a dokumentum végén található további információk szakaszt a saját egyéni jogcím-szolgáltató létrehozásához.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Hozzáférés biztosítása a helyszíni SharePoint-biztonsági csoport számára

**Biztonsági csoportok és engedélyek konfigurálása az alkalmazás regisztrálásához**

1. A Azure Portal válassza a **Azure Active Directory**, majd a **Alkalmazásregisztrációk**lehetőséget.

    ![Vállalati alkalmazások panel](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. A keresőmezőbe írja be a (z) kifejezést, és válassza a helyszíni **SharePoint**lehetőséget.

    ![Helyszíni SharePoint a találatok listájában](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Kattintson a **manifest (jegyzékfájl**) elemre.

    ![Jegyzékfájl lehetőség](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Módosítsa `groupMembershipClaims`: `NULL`, `groupMembershipClaims`: `SecurityGroup`. Ezután kattintson a Save (Mentés) gombra.

    ![Jegyzékfájl szerkesztése](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Kattintson a **Beállítások**elemre, majd a **szükséges engedélyek**elemre.

    ![Szükséges engedélyek](./media/sharepoint-on-premises-tutorial/settings.png)

6. Kattintson a **Hozzáadás** elemre, majd **válasszon ki egy API**-t.

    ![API-hozzáférés](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Adja hozzá a **Windows Azure Active Directory** és a **Microsoft Graph API**-t, de egyszerre csak egyet lehet kijelölni.

    ![API kiválasztása](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Válassza a Windows Azure Active Directory lehetőséget, tekintse meg a címtáradatok olvasása elemet, majd kattintson a kiválasztás elemre. Lépjen vissza, és adja hozzá a Microsoft Graph, majd válassza a címtárbeli információk olvasása lehetőséget is.  Kattintson a kiválasztás elemre, majd kattintson a kész gombra.

    ![Hozzáférés engedélyezése](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Most a szükséges beállítások területen kattintson az **engedélyek megadása** elemre, majd az Igen gombra az engedélyek megadásához.

    ![Engedélyek megadása](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Az értesítések területen ellenőrizze, hogy az engedélyek megadása sikeres volt-e.  Ha nem, akkor a AzureCP nem fog megfelelően működni, és nem lesz lehetséges a helyszíni SharePoint konfigurálása Azure Active Directory biztonsági csoportokkal.

10. Konfigurálja a AzureCP a helyszíni SharePoint-farmon vagy egy alternatív egyéni jogcím-szolgáltatói megoldáson.  Ebben a példában a AzureCP-t használjuk.

    > [!NOTE]
    > Vegye figyelembe, hogy a AzureCP nem Microsoft-termék, vagy nem támogatott a Microsoft technikai támogatási szolgálata. AzureCP letöltése, telepítése és konfigurálása a helyszíni SharePoint-farmon https://yvand.github.io/AzureCP/ 

11. **Hozzáférés biztosítása a Azure Active Directory biztonsági csoporthoz a helyszíni SharePointban** : – a csoportoknak hozzáférést kell biztosítani az alkalmazáshoz a helyszíni SharePointban.  A következő lépésekkel állíthatja be a webalkalmazás eléréséhez szükséges engedélyeket.

12. A központi felügyelet területen kattintson az alkalmazások kezelése, webalkalmazások kezelése elemre, majd válassza ki a webalkalmazást a menüszalag aktiválásához, majd kattintson a felhasználói házirend elemre.

    ![Központi felügyelet](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. A szabályzat a webalkalmazáshoz lapon kattintson a felhasználók hozzáadása elemre, majd válassza ki a zónát, majd kattintson a Tovább gombra.  Kattintson a címjegyzékre.

    ![Webalkalmazásra vonatkozó szabályzat](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Ezután keresse meg és adja hozzá a Azure Active Directory biztonsági csoportot, majd kattintson az OK gombra.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Válassza ki az engedélyeket, majd kattintson a Befejezés gombra.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Tekintse meg a házirend a webalkalmazáshoz című témakört, és adja hozzá a Azure Active Directory csoportot.  A csoportjogcím a Felhasználónév Azure Active Directory biztonsági csoport objektumának AZONOSÍTÓját jeleníti meg.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Keresse meg a SharePoint-webhely gyűjteményét, és adja hozzá a csoportot is. Kattintson a hely beállításai lehetőségre, majd a hely engedélyei lehetőségre, és adja meg az engedélyeket.  Keresse meg a csoport szerepkör jogcímet, rendelje hozzá a jogosultsági szintet, majd kattintson a megosztás lehetőségre.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Egy megbízható identitás-szolgáltató konfigurálása több webalkalmazáshoz

A konfiguráció egyetlen webalkalmazás esetében működik, de további konfigurálásra van szükség, ha ugyanazt a megbízható identitás-szolgáltatót szeretné használni több webalkalmazáshoz. Tegyük fel például, hogy kibővítettünk egy webalkalmazást az URL-`https://portal.contoso.local` használatára, és most a felhasználókat a `https://sales.contoso.local` is szeretné hitelesíteni. Ehhez frissítenie kell az WReply paramétert, és frissítenie kell az alkalmazás regisztrációját az Azure AD-ben a válasz URL-címének hozzáadásához.

1. A Azure Portal nyissa meg az Azure AD-címtárat. Kattintson a **Alkalmazásregisztrációk**, majd **az összes alkalmazás megtekintése**elemre. Kattintson a korábban létrehozott alkalmazásra (SharePoint SAML-integráció).

2. Kattintson a **Beállítások**elemre.

3. A beállítások panelen kattintson a **Válasz URL-címek**elemre.

4. Adja hozzá a további webalkalmazás URL-címét az URL-címhez csatolt `/_trust/default.aspx`hoz (például `https://sales.contoso.local/_trust/default.aspx`), majd kattintson a **Save (Mentés**) gombra.

5. A SharePoint-kiszolgálón nyissa meg a **sharepoint 2016 felügyeleti rendszerhéját** , és hajtsa végre a következő parancsokat a korábban használt megbízható Identity token-kiállító neve alapján.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. A központi felügyelet lapon nyissa meg a webalkalmazást, és engedélyezze a meglévő megbízható identitás-szolgáltatót. Ne feledje, hogy a bejelentkezési oldal URL-címét egyéni bejelentkezési oldalként is konfigurálja `/_trust/`.

7. A központi felügyelet lapon kattintson a webalkalmazásra, és válassza a **felhasználói házirend**elemet. Adja hozzá a megfelelő engedélyekkel rendelkező felhasználót a jelen cikkben korábban bemutatott módon.

### <a name="fixing-people-picker"></a>Személyek Választójának kijavítása

A felhasználók mostantól bejelentkezhetnek a SharePoint 2016-be az Azure AD-identitások használatával, de továbbra is lehetőség van a felhasználói élmény javítására. A felhasználók keresése például több keresési eredményt is tartalmaz a People választóban. A rendszer a jogcím-leképezésben létrehozott három jogcím-típusra vonatkozóan keresési eredményt ad. Ha a felhasználók közül szeretne kiválasztani egy felhasználót, pontosan be kell írnia a felhasználónevét, és ki kell választania **a jogcím** eredményét.

![Jogcímek keresési eredményei](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

A keresett értékeket nem érvényesíti a rendszer, ami olyan hibákhoz vezethet, vagy a felhasználók véletlenül nem megfelelő jogcím-típust választanak ki, mint például a **vezetéknév** jogcímet. Így megakadályozható, hogy a felhasználók hozzáférjenek az erőforrásokhoz.

Ennek a forgatókönyvnek a támogatásához egy [AzureCP](https://yvand.github.io/AzureCP/) nevű nyílt forráskódú megoldás létezik, amely egyéni jogcím-szolgáltatót biztosít a SharePoint 2016-hez. Az Azure AD Graph használatával oldja fel, hogy a felhasználók hogyan adja meg és hajtják végre az érvényesítést. További információ: [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Rendelje hozzá az Azure AD biztonsági csoportot a Azure Portal

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza **a helyszíni SharePoint**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **helyszíni SharePoint**lehetőséget.

    ![A helyszíni SharePoint-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása**elemre.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Keresse meg a használni kívánt biztonsági csoportot, majd kattintson a csoportra, és adja hozzá a tagok kiválasztása szakaszhoz. Kattintson a **kiválasztás**, majd a **hozzárendelés**elemre.

    ![Keresés a biztonsági csoportban](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Győződjön meg arról, hogy a menüsorban található értesítések azt jelzik, hogy a csoport sikeresen hozzá lett-e rendelve a Azure Portal vállalati alkalmazásához.

### <a name="create-sharepoint-on-premises-test-user"></a>SharePoint helyszíni tesztelési felhasználó létrehozása

Ebben a szakaszban létrehoz egy Britta Simon nevű felhasználót a helyszíni SharePointban. A helyszíni SharePoint helyszíni [támogatási csapatával](https://support.office.com/) a felhasználókat a SharePoint helyszíni platformján veheti fel. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SharePoint helyszíni csempére kattint, automatikusan be kell jelentkeznie a helyszíni SharePointba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

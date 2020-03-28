---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a helyszíni SharePointtal | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a helyszíni SharePoint között.
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
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 22b673ef481593247b6ee1007c13390a498c66be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a helyszíni SharePointtal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a helyszíni SharePointot az Azure Active Directoryval (Azure AD). Ha a helyszíni SharePointot integrálja az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki a helyszíni SharePoint-hozzáféréssel rendelkezik.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a SharePoint-on a helyszíni azure-fiókokkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A helyszíni SharePoint egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A helyszíni SharePoint támogatja az **SP** által kezdeményezett egyszeri megosztási eljárást
* A Helyszíni SharePoint konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
* Kérjük, olvassa el ezt [a linket,](https://docs.microsoft.com/archive/blogs/kaevans/sharepoint-2013-user-profile-sync-for-claims-users) hogy megtudja, hogyan szinkronizálhatja a felhasználói profilokat a SharePoint helyszíni és az Azure AD

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>SharePoint hozzáadása a katalógusból

A helyszíni SharePoint-integráció konfigurálásához hozzá kell adnia a helyszíni SharePoint-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.

    > [!NOTE]
    > Ha az elem nem érhető el, akkor a bal oldali navigációs panel tetején található Rögzített **Minden szolgáltatás** linken keresztül is megnyitható. A következő áttekintésben az **Azure Active Directory-kapcsolat** az **Identitás** szakaszban található, vagy a szűrő szövegmezőjével kereshető.

1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be **a helyszíni SharePoint kifejezést** a keresőmezőbe.
1. Válassza a **helyszíni SharePoint lehetőséget az** eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharepoint-on-premises"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a helyszíni SharePoint-alapú szolgáltatásokhoz

Konfigurálja és tesztelje az Azure AD egyszeri szolgáltatást a helyszíni SharePointtal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az egyszeri bejelentkezés működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a SharePoint helyszíni.

Az Azure AD egyszeri szolgáltatás helyszíni SharePoint-szolgáltatással való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a helyszíni SharePoint egyszeri bejelentkezést](#configure-sharepoint-on-premises-single-sign-on)** – az alkalmazás oldalon az egyszeri bejelentkezés beállításainak konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Hozzon létre egy Azure AD-biztonsági csoportot az Azure Portalon](#create-an-azure-ad-security-group-in-the-azure-portal)** – az Azure AD új biztonsági csoportjának egyszeri bejelentkezéshez való engedélyezéséhez.
5. **[Hozzáférést biztosít a helyszíni SharePoint biztonsági csoporthoz](#grant-access-to-sharepoint-on-premises-security-group)** – hozzáférést biztosít egy adott csoport számára az Azure AD-hez.
6. **[Rendelje hozzá az Azure AD-biztonsági csoportot az Azure Portalon](#assign-the-azure-ad-security-group-in-the-azure-portal)** – az adott csoport hozzárendelése az Azure AD-hez a hitelesítéshez.
7. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a helyszíni SharePointszolgáltatással hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **helyszíni SharePoint-alkalmazások integrációs** lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:


    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`urn:sharepoint:federation`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [a helyszíni SharePoint ügyféltámogatási csapatával,](https://support.office.com/) és ezeket az értékeket szeretné letudni. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

    > [!Note]
    > Kérjük, vegye figyelembe a fájl elérési útját, amelyre letöltötte a tanúsítványfájlt, mivel a konfigurációhoz később használnia kell.

6. A SharePoint helyszíni beállítása szakaszban másolja a megfelelő **URL-cím(eke)t** a követelménynek megfelelően. Az **egyszeri bejelentkezési szolgáltatás URL-címéhez**használja a következő minta értékét:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ az Azure Ad-előfizetés bérlői azonosítója.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

    > [!NOTE]
    > A helyszíni SharePoint-alkalmazás SAML 1.1-es jogkivonatot használ, így az Azure AD elvárja, hogy a WS Fed kérés a SharePoint-kiszolgálótól, és a hitelesítés után kiadja az SAML 1.1-et. Token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>A SharePoint helyszíni egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a helyszíni sharePoint-alapú vállalati webhelyre rendszergazdaként.

2. **Új megbízható identitásszolgáltató konfigurálása a SharePoint Server 2016-ban**

    Jelentkezzen be a SharePoint Server 2016-kiszolgálóra, és nyissa meg a SharePoint 2016 felügyeleti rendszerhéjat. Töltse ki a $realm értékeit (azonosító érték az Azure Portal on-premises Domain és URL-címei szakaszból), $wsfedurl (egyszeri bejelentkezési szolgáltatás URL-címe) és $filepath (fájlelérési út, amelyre a tanúsítványfájlt letöltötte) az Azure Portalról, és futtassa az alábbi parancsokat egy új megbízható identitásszolgáltató konfigurálásához.

    > [!TIP]
    > Ha most használja a PowerShellt, vagy szeretne többet megtudni a PowerShell működéséről, olvassa el a [SharePoint PowerShell című témakört.](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps)

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

    Ezután kövesse az alábbi lépéseket az alkalmazás megbízható identitásszolgáltatójának engedélyezéséhez:

    a. A központi felügyelet, keresse meg a **Webalkalmazás kezelése,** és válassza ki a webalkalmazás, amely az Azure AD-vel biztosítani kívánt.

    b. A menüszalagon kattintson a **Hitelesítésszolgáltatók** elemre, és válassza ki a használni kívánt zónát.

    c. Válassza a **Megbízható identitásszolgáltató lehetőséget,** és válassza ki az *AzureAD*nevű azonosítószolgáltatót.

    d. A bejelentkezési lap URL-címbeállításában válassza az **Egyéni bejelentkezés i lapot,** és adja meg a "/_trust/" értéket.

    e. Kattintson az **OK** gombra.

    ![A hitelesítésszolgáltató konfigurálása](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Néhány külső felhasználó nem fogja tudni használni ezt az egyszeri bejelentkezési integrációt, mivel `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`az upn-jük valami ilyesmivel fog rendelkezni. Hamarosan lehetővé tesszük az ügyfelek app config, hogyan kell kezelni az UPN attól függően, hogy a felhasználó típusát. Ezt követően az összes vendég felhasználó számára lehetővé kell tenni, hogy az Egyszeri bejelentkezés zökkenőmentesen használja a szervezet alkalmazottaiként.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban`brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Hozzon létre egy Azure AD-biztonsági csoportot az Azure Portalon

1. Kattintson az **Azure Active Directory > az Összes csoport elemre.**

    ![Azure AD-biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Kattintson **az Új csoport gombra:**

    ![Azure AD-biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Adja meg a **Csoport típusát**, A **csoport nevét**, a Csoport **leírását**, **a tagság típusát.** Kattintson a nyílra, hogy válassza ki a tagokat, majd keresse meg, vagy kattintson a tag szeretne hozzáadni a csoporthoz. Kattintson a **Kijelölés gombra** a kijelölt tagok hozzáadásához, majd kattintson a **Létrehozás gombra.**

    ![Azure AD-biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Az Azure Active Directory biztonsági csoportok hozzárendeléséhez a helyszíni SharePoint-környezethez telepíteni és konfigurálni kell az [AzureCP-t](https://yvand.github.io/AzureCP/) a helyszíni SharePoint-farmon, vagy fejleszteni és konfigurálni kell egy alternatív egyéni jogcímszolgáltatót a SharePointhoz.  Tekintse meg a további információkat a dokumentum végén a saját egyéni jogcímszolgáltató létrehozásához, ha nem használja az AzureCP-t.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Hozzáférés megadása a helyszíni SharePoint biztonsági csoporthoz

**Biztonsági csoportok és engedélyek konfigurálása az alkalmazásregisztrációhoz**

1. Az Azure Portalon válassza az **Azure Active Directory**lehetőséget, majd válassza az **Alkalmazásregisztrációk**lehetőséget.

    ![A vállalati alkalmazások panelje](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. A keresőmezőbe írja be és válassza **a helyszíni SharePoint elemet.**

    ![Helyszíni SharePoint az eredménylistában](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Kattintson a **Manifest gombra.**

    ![Jegyzékjegyzék-beállítás](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Módosítsa `groupMembershipClaims` `NULL`: `groupMembershipClaims`, `SecurityGroup`a következőre: . Ezután kattintson a Mentés gombra

    ![Jegyzékfájl szerkesztése](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Kattintson a **Beállítások**gombra, majd a **Szükséges engedélyek**elemre.

    ![Szükséges engedélyek](./media/sharepoint-on-premises-tutorial/settings.png)

6. Kattintson a **Hozzáadás** **gombra, majd válasszon egy API-t.**

    ![API-hozzáférés](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Adja hozzá a **Windows Azure Active Directoryt** és a **Microsoft Graph API-t**is, de egyszerre csak egyet választhat.

    ![API kiválasztása](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Válassza a Windows Azure Active Directory t, jelölje be a Címtáradatok olvasása jelölőnégyzetet, és kattintson a Kijelölés gombra. Lépjen vissza, és adja hozzá a Microsoft Graph-ot, és válassza a Címtáradatok olvasása lehetőséget is.  Kattintson a Kijelölés gombra, és kattintson a Kész gombra.

    ![Hozzáférés engedélyezése](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Most a Szükséges beállítások csoportban kattintson az **Engedélyek megadása,** majd az Igen gombra az engedélyek megadásához.

    ![Engedélyek megadása](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Ellenőrizze az értesítések alatt, hogy az engedélyek sikeresek voltak-e.  Ha nem, akkor az AzureCP nem fog megfelelően működni, és nem lesz lehetséges a SharePoint konfigurálása a helyszínen az Azure Active Directory biztonsági csoportok.

10. Konfigurálja az AzureCP-t a helyszíni SharePoint-farmon vagy egy alternatív egyéni jogcímszolgáltatói megoldásban.  Ebben a példában az AzureCP-t használjuk.

    > [!NOTE]
    > Kérjük, vegye figyelembe, hogy az AzureCP nem microsoftos termék, és a Microsoft technikai támogatási szolgálata sem támogatja. Az AzureCP letöltése, telepítése és konfigurálása a helyszíni SharePoint-farmon[https://yvand.github.io/AzureCP/](https://yvand.github.io/AzureCP/) 

11. **Hozzáférést biztosít az Azure Active Directory biztonsági csoport a helyszíni SharePoint** :- A csoportok hozzáférést kell biztosítani az alkalmazáshoz a SharePoint helyszíni.  Az alábbi lépésekkel állíthatja be a webalkalmazás eléréséhez szükséges engedélyeket.

12. A Központi felügyelet, kattintson az Alkalmazáskezelés, Kezelése webes alkalmazások, majd válassza ki a webalkalmazást, hogy aktiválja a menüszalagot, és kattintson a Felhasználói házirend.

    ![Központi adminisztráció](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. A Webes alkalmazás házirendje csoportban kattintson a Felhasználók hozzáadása elemre, majd jelölje ki a zónát, majd kattintson a Tovább gombra.  Kattintson a címjegyzékre.

    ![Webalkalmazás házirendje](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Ezután keresse meg és adja hozzá az Azure Active Directory biztonsági csoportot, és kattintson az OK gombra.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Válassza ki az engedélyeket, majd kattintson a Befejezés gombra.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Lásd: A webalkalmazásszabályzat a hozzáadott Azure Active Directory-csoport.  A csoportjogcím az Azure Active Directory security group objektumazonosítóját jeleníti meg a felhasználónévhez.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Tallózással keresse meg a SharePoint-webhelycsoportot, és adja hozzá a csoportot is. Kattintson a Webhely beállításai, majd a Webhely engedélyek és engedélyek megadása elemre.  Keresse meg a csoportszerepkör-jogcímet, rendelje hozzá a jogosultsági szintet, és kattintson a Megosztás gombra.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Egy megbízható identitásszolgáltató konfigurálása több webalkalmazáshoz

A konfiguráció egyetlen webalkalmazáshoz működik, de további konfigurációra van szüksége, ha ugyanazt a megbízható identitásszolgáltatót kívánja használni több webalkalmazáshoz. Tegyük fel például, hogy kiterjesztettünk `https://portal.contoso.local` egy webalkalmazást az URL-cím használatára, és most a felhasználókat `https://sales.contoso.local` is hitelesíteni szeretnénk. Ehhez frissítenie kell az identitásszolgáltatót a WReply paraméter tiszteletben tartandó, és frissítenie kell az alkalmazás regisztrációját az Azure AD-ben egy válasz URL-cím hozzáadásához.

1. Az Azure Portalon nyissa meg az Azure AD-címt. Kattintson **az Alkalmazásregisztrációk**menü **Az összes alkalmazás megtekintése parancsra.** Kattintson a korábban létrehozott alkalmazásra (SharePoint SAML-integráció).

2. Kattintson a **Beállítások** elemre.

3. A beállítások panelen kattintson a **Válasz URL-címek gombra.**

4. Adja hozzá a további webalkalmazás URL-címét az `https://sales.contoso.local/_trust/default.aspx`URL-hez (például ) hozzáfűzve, `/_trust/default.aspx` majd kattintson a Mentés **gombra.**

5. A SharePoint-kiszolgálón nyissa meg a **SharePoint 2016 felügyeleti rendszerhéjat,** és hajtsa végre a következő parancsokat a korábban használt megbízható identitásjogkivonat-kibocsátó nevével.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```

6. A központi felügyelet, nyissa meg a webalkalmazást, és engedélyezze a meglévő megbízható identitásszolgáltató. Ne felejtse el a bejelentkezési lap URL-címét egyéni bejelentkezési lapként is konfigurálni. `/_trust/`

7. A Központi felügyelet csoportban kattintson a webalkalmazásra, és válassza a **Felhasználói házirend**lehetőséget. Adjon hozzá egy felhasználót a megfelelő engedélyekkel, ahogy azt a cikk ben korábban bemutatottak.

### <a name="fixing-people-picker"></a>Rögzítése Emberek Picker

A felhasználók most már bejelentkezhetnek a SharePoint 2016-ba az Azure AD-ből származó identitások használatával, de még mindig vannak lehetőségek a felhasználói élmény javítására. Ha például egy felhasználót keres, akkor több keresési eredmény jelenik meg a személyek választójában. A jogcímleképezésben létrehozott 3 jogcímtípus mindegyikéhez van keresési eredmény. Ha a személyek et választót használó felhasználót szeretne kiválasztani, **name** pontosan be kell írnia a felhasználónevét, és ki kell választania a névjogcím-jogcím eredményét.

![Jogcímek keresési eredményei](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

A keresett értékek nem érvényesíthetők, ami elíráshoz vezethet, vagy a felhasználók véletlenül rossz jogcímtípust választanak hozzá, például a **Vezetéknév** jogcímet. Ez megakadályozhatja, hogy a felhasználók sikeresen hozzáférjenek az erőforrásokhoz.

Ebben a forgatókönyvben segítséget nyújt, van egy nyílt forráskódú megoldás nevű [AzureCP,](https://yvand.github.io/AzureCP/) amely egyéni jogcímszolgáltató a SharePoint 2016.To assist with this scenario, there is an open-source solution called AzureCP that provides a custom claims provider for SharePoint 2016. A Microsoft Graph API-t fogja használni a felhasználók beírásának feloldásához, és az érvényesítés végrehajtásához. További információ az [AzureCP-ben.](https://yvand.github.io/AzureCP/)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Rendelje hozzá az Azure AD biztonsági csoportot az Azure Portalon

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **helyszíni SharePoint**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza **a helyszíni SharePoint elemet.**

    ![A helyszíni SharePoint hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása gombra**.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. Keresse meg a használni kívánt biztonsági csoportot, majd kattintson a csoportra a Tagok kiválasztása szakaszhoz való hozzáadásához. Kattintson **a Kijelölés**gombra, majd a **Hozzárendelés gombra.**

    ![Keresés biztonsági csoportja](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Ellenőrizze a menüsorban található értesítéseket, amelyek értesítést kapnak arról, hogy a csoport sikeresen hozzá lett rendelve az Azure Portalon lévő Enterprise alkalmazáshoz.

### <a name="create-sharepoint-on-premises-test-user"></a>Helyszíni SharePoint-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy felhasználó nevű Britta Simon sharePoint-ban helyszíni. Együttműködve [a Helyszíni SharePoint támogatási csapatával,](https://support.office.com/) hogy felvegye a felhasználókat a helyszíni SharePoint platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a helyszíni SharePoint csempére kattint, automatikusan be kell jelentkeznie a helyszíni SharePoint-dokumentumba, amelyhez beállítja az egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
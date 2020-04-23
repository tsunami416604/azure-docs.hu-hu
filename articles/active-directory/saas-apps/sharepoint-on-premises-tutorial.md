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
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867033"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a helyszíni SharePointtal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a helyszíni SharePointot az Azure Active Directoryval (Azure AD). Ha a helyszíni SharePointot integrálja az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki a helyszíni SharePoint-hozzáféréssel rendelkezik.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a SharePoint-on a helyszíni azure-fiókokkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

Az Azure Active Directory helyszíni SharePoint-integrációjának konfigurálásához a következő elemekre van szükség:

* Egy Azure Active Directory-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* SharePoint 2013-farm vagy újabb.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure Active Directory egyszeri bejelentkezést egy tesztkörnyezetben. Az Azure Active Directory felhasználói hozzáférhetnek a helyszíni SharePoint szolgáltatáshoz.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>A nagyvállalati alkalmazások létrehozása az Azure Portalon

A helyszíni SharePoint-integráció konfigurálásához hozzá kell adnia a helyszíni SharePoint-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

Ha a sharePointot a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

 > [!NOTE]
 > Ha az elem nem érhető el, akkor a bal oldali navigációs panel tetején található Rögzített **Minden szolgáltatás** linken keresztül is megnyitható. A következő áttekintésben az **Azure Active Directory-kapcsolat** az **Identitás** szakaszban található, vagy a szűrő szövegmezőjével kereshető.

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

4. A keresőmezőbe írja be **a helyszíni SharePoint parancsot,** és válassza **a helyszíni SharePoint elemet** az eredménypanelről.

    <kbd>![Helyszíni SharePoint az eredménylistában](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Adja meg a SharePoint OnPrem nevét, és kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

1. Az új vállalati alkalmazásban kattintson a Tulajdonságok gombra, és ellenőrizze a **szükséges felhasználói hozzárendelés** értékét

   <kbd>![Helyszíni SharePoint az eredménylistában](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

a mi forgatókönyvünkben ez az érték **nem**.

## <a name="configure-and-test-azure-ad"></a>Az Azure AD konfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja az Azure AD egyszeri bejelentkezés a SharePoint helyszíni.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat a helyszíni SharePoint-ban létre kell hozni.

Az Azure Active Directory egyszeri bejelentkezésének konfigurálásához és teszteléséhez a helyszíni SharePoint szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
1. **[Konfigurálja a Helyszíni SharePoint-ot](#configure-sharepoint-on-premises)** – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
1. **[Hozzon létre egy Azure AD-tesztfelhasználót az Azure Portalon](#create-an-azure-ad-test-user-in-the-azure-portal)** – hozzon létre új felhasználót az Azure AD-ben egyszeri bejelentkezéshez.
1. **[Hozzon létre egy Azure AD-biztonsági csoportot az Azure Portalon](#create-an-azure-ad-security-group-in-the-azure-portal)** – hozzon létre új biztonsági csoportot az Azure AD-ben egyszeri bejelentkezéshez.
1. **[Engedélyeket adhat az Azure Active Directory-fióknak a helyszíni SharePoint-ban](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** – engedélyeket adhat az Azure AD-felhasználónak.
1. **[Engedélyeket adhat az Azure AD-csoportnak a helyszíni SharePoint-ban](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** – engedélyeket adhat az Azure AD-csoportnak.
1. **[Hozzáférést biztosít hatni a vendégfiókhoz a helyszíni SharePoint-hoz az Azure Portalon](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** – adjon engedélyeket a vendégfiókhoz az Azure AD-ben a helyszíni SharePoint-szolgáltatásban.
1. **[A megbízható identitásszolgáltató konfigurálása több webalkalmazáshoz](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** – ugyanaza megbízható identitásszolgáltató használata több webalkalmazáshoz

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a helyszíni SharePointszolgáltatással hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg az Azure AD könyvtárat, kattintson a **Vállalati alkalmazások**elemre, kattintson a **korábban létrehozott vállalati alkalmazás nevére,** és kattintson **az Egyszeri bejelentkezés gombra.**

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen kattintson az **SAML** módra az egyszeri bejelentkezés engedélyezéséhez.
 
3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Helyszíni SharePoint-tartomány és URL-címek egyszeri bejelentkezési adatai](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`urn:<sharepointFarmName>:<federationName>`

    1. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<YourSharePointSiteURL>/_trust/`

    1. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YourSharePointSiteURL>/`
    1. kattintson a mentés.

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. A SharePoint helyszíni beállítása szakaszban másolja a megfelelő **URL-cím(eke)t** a követelménynek megfelelően.
    
    1. **Bejelentkezés i URL-címe**
    
        Copy login URL replace **/saml2** a végén a **/wsfed**, úgy nézne **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** ki (ez az URL nem pontos)

    1. **Azure Hirdetés-azonosító**
    1. **Kijelentkezés URL-címe**
    > [!NOTE]
    > Ez az URL nem használható a SharePoint ban is: a **/saml2** kapcsolót **a /wsfed**kapcsolóra kell cserélni. A helyszíni SharePoint-alkalmazás SAML 1.1-es jogkivonatot használ, így az Azure AD elvárja, hogy a WS Fed kérés a SharePoint-kiszolgálótól, és a hitelesítés után kiadja az SAML 1.1-et. Token.

### <a name="configure-sharepoint-on-premises"></a>A SharePoint helyszíni konfigurálása

1. **Új megbízható identitásszolgáltató létrehozása a SharePoint Server 2016-ban**

    Jelentkezzen be a SharePoint-kiszolgálóra, és nyissa meg a SharePoint felügyeleti rendszerhéjat. Töltse ki az értékeket:
    1. **$realm** (Azonosító érték a Helyszíni SharePoint-tartomány és URL-címek szakaszból az Azure Portalon).
    1. **$wsfedurl** (egyszeri bejelentkezési szolgáltatás URL-címe).
   1. **$filepath** (fájl elérési útja, amelyre letöltötte a tanúsítványfájlt) az Azure Portalról.

    Új megbízható identitásszolgáltató konfigurálásához futtassa a következő parancsokat.

    > [!TIP]
    > Ha most használja a PowerShellt, vagy szeretne többet megtudni a PowerShell működéséről, olvassa el a [SharePoint PowerShell című témakört.](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps)


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **Az alkalmazás megbízható identitásszolgáltatójának engedélyezése**

    a. A központi felügyelet, keresse meg a **Webalkalmazás kezelése,** és válassza ki a webalkalmazás, amely az Azure AD-vel biztosítani kívánt.

    b. A menüszalagon kattintson a **Hitelesítésszolgáltatók** elemre, és válassza ki a használni kívánt zónát.

    c. Válassza a **Megbízható identitásszolgáltató lehetőséget,** és válassza ki az *AzureAD*nevű azonosítószolgáltatót.

    d. Kattintson az **OK** gombra.

    ![A hitelesítésszolgáltató konfigurálása](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Azure AD-tesztfelhasználó létrehozása az Azure Portalon

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, a Felhasználók **csoportban** válassza a **Felhasználók lehetőséget.**

2. Ezután válassza a képernyő tetején az **Összes felhasználó,** majd az **Új felhasználó** lehetőséget.

3. Válassza a **Felhasználó létrehozása** lehetőséget, és a Felhasználó tulajdonságai között hajtsa végre a következő lépéseket.  
   Előfordulhat, hogy a bérlői utótag vagy bármely ellenőrzött tartomány használatával hozhat létre felhasználókat az Azure AD-ben. 

    a. A **Név** mezőbe írja be a felhasználónevet, a **TestUser értéket**használtuk.
  
    b. A **Felhasználónév** mezőtípusban`TestUser@yourcompanydomain.extension`  
    A TestUser@contoso.com például

    ![A Felhasználó párbeszédpanel](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

    e. Most már megoszthatja TestUser@contoso.com a webhelyet, és engedélyezheti a felhasználónak, hogy hozzáférjen hozzá.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Hozzon létre egy Azure AD-biztonsági csoportot az Azure Portalon

1. Kattintson az **Azure Active Directory > csoportok**elemre.

2. Kattintson **az Új csoport gombra:**

3. Adja meg a **Csoport típusát**, A **csoport nevét**, a Csoport **leírását**, **a tagság típusát.** Kattintson a nyílra, hogy válassza ki a tagokat, majd keresse meg, vagy kattintson a tag szeretne hozzáadni a csoporthoz. Kattintson a **Kijelölés gombra** a kijelölt tagok hozzáadásához, majd kattintson a **Létrehozás gombra.**

![Azure AD-biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Engedélyek megadása az Azure Active Directory-fiókhoz a helyszíni SharePointban

Az Azure Active Directory-felhasználó helyszíni SharePoint-szolgáltatásban való elérésének engedélyezéséhez meg kell osztania a webhelycsoportot, vagy hozzá kell adnia az Azure Active Directory-felhasználót a webhelycsoport egyik csoportjához. A felhasználók most már bejelentkezhetnek a SharePoint 201x-be az Azure AD-ből származó identitások használatával, de még mindig vannak lehetőségek a felhasználói élmény javítására. Ha például egy felhasználót keres, akkor több keresési eredmény jelenik meg a személyek választójában. A jogcímleképezésben létrehozott jogcímtípusok mindegyikéhez keresési eredmény található. Ha a személyek et választót használó felhasználót szeretne kiválasztani, **name** pontosan be kell írnia a felhasználónevét, és ki kell választania a névjogcím-jogcím eredményét.

![Jogcímek keresési eredményei](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

A keresett értékek nem érvényesíthetők, ami elíráshoz vezethet, vagy a felhasználók véletlenül rossz jogcímtípust választanak. Ez megakadályozhatja, hogy a felhasználók sikeresen hozzáférjenek az erőforrásokhoz.

**A személyek választójának megoldásához** ebben a forgatókönyvben van egy [azurecp](https://yvand.github.io/AzureCP/) nevű nyílt forráskódú megoldás, amely egyéni jogcímszolgáltatót biztosít a SharePoint 2013, 2016 és 2019 számára. A Microsoft Graph API-t fogja használni a felhasználók beírásának feloldásához, és az érvényesítés végrehajtásához. További információ az [AzureCP-ben.](https://yvand.github.io/AzureCP/)

  > [!NOTE]
  > Az AzureCP nélkül csoportok at adhat hozzá az Azure AD-csoport azonosítójának hozzáadásával, de ez nem a felhasználó barátságos és megbízható. Van, hogyan néz ki:  
  >   
  >![Azure AD-csoport hozzáadása a SharePoint-csoporthoz](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Engedélyek megadása az Azure AD-csoportnak a helyszíni SharePointban

Az Azure Active Directory biztonsági csoportjainak hozzárendelése a Helyszíni SharePointhoz, egyéni jogcímszolgáltatót kell használnia a SharePoint Serverhez. A példában az AzureCP-t használtuk.

 > [!NOTE]
 > Kérjük, vegye figyelembe, hogy az AzureCP nem microsoftos termék, és a Microsoft technikai támogatási szolgálata sem támogatja. Töltse le, telepítse és konfigurálja az AzureCP-t a helyszíni SharePoint-farmon. https://yvand.github.io/AzureCP/ 

1. Konfigurálja az AzureCP-t a helyszíni SharePoint-farmon vagy egy alternatív egyéni jogcímszolgáltatói megoldásban. az AzureCP-konfiguráció lépései a következő helyen érhetők el:https://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. Az Azure Portalon nyissa meg az Azure AD-címt. Kattintson a **Vállalati alkalmazások**menügombra, kattintson a **korábban létrehozott vállalati alkalmazás nevére,** majd az **Egyszeri bejelentkezés gombra.**

1. Az **Egyszeri bejelentkezés beállítása a SAM-mel**lapon szerkesztsd a Felhasználói **attribútumok & jogcímek szakaszt.**

1. Kattintson a **Csoportjogcím hozzáadása gombra.**

1. Válassza ki, hogy mely csoportokat kell visszaküldeni a jogcímben, a mi esetünkben válassza a **Minden csoport** lehetőséget, a Forrás attribútum szakaszban válassza a **Csoportazonosító** lehetőséget, és kattintson a **Mentés gombra.**

Az Azure Active Directory biztonsági csoport hoz való hozzáférés biztosításához a helyszíni SharePointban meg kell osztania a webhelycsoportot, vagy hozzá kell adnia az Azure Active Directory biztonsági csoportját a webhelycsoport egyik csoportjához.

1. Tallózással keresse meg a SharePoint-webhelycsoport webhelybeállításai csoportjában a "Személyek és csoportok" elemre. Válassza ki a SharePoint-csoportot, majd kattintson az Új, "Felhasználók hozzáadása ehhez a csoporthoz" elemre, és kezdje el beírni a csoport nevét, amelyet a Személyek választója az Azure Active Directory biztonsági csoportnak jelenít meg.

    ![Azure AD-csoport hozzáadása a SharePoint-csoporthoz](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Hozzáférés megadása vendégfiókhoz a helyszíni SharePoint-fiókhoz az Azure Portalon

mostantól egységes módon biztosíthatja a SharePoint-webhelyhez való hozzáférést egy vendégfiókhoz. Előfordul, hogy az upn lesz módosítva. A jdoe@outlook.com felhasználó a `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. Lehetséges, hogy zökkenőmentes élményt, miközben megosztja a webhely külső felhasználókkal, szükséges lenne, hogy adjunk hozzá néhány módosítást a **felhasználói attribútumok & jogcímek** az Azure Portalon.

1. Az Azure Portalon nyissa meg az Azure AD-címt. Kattintson a **Vállalati alkalmazások**menügombra, kattintson a **korábban létrehozott vállalati alkalmazás nevére,** majd az **Egyszeri bejelentkezés gombra.**

1. Az **Egyszeri bejelentkezés beállítása a SAM-mel**lapon szerkesztsd a Felhasználói **attribútumok & jogcímek szakaszt.**

1. a **Kötelező jogcímzónában** kattintson az **Egyedi felhasználói azonosító (névazonosító) elemre.**

1. Módosítsa a **Forrás attribútumtulajdonságot** a **user.localuserprincipalname** értékre, és mentse a **.**

    ![Felhasználói attribútumok & jogcímek kezdőbetűjével](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. A menüszalag használata lépjen vissza **az SAML-alapú bejelentkezésre,** most a **Felhasználói attribútumok & jogcímek** szakasz a következőkre néz ki: 

    ![Felhasználói attribútumok & jogcímek végleges](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Ebben a beállításban nem szükséges a vezetéknév és a keresztnév.

1. Az Azure Portalbal a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget.

1. kattintson az **Új vendég felhasználó**

1. Válassza a **Felhasználó meghívása** lehetőséget, töltse ki a felhasználó tulajdonságait, majd kattintson a **Meghívás gombra.**

1. Most már megoszthatja MyGuestAccount@outlook.com a webhelyet, és engedélyezheti a felhasználónak, hogy hozzáférjen hozzá.

    ![Webhely megosztása vendégfiókkal](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>A megbízható identitásszolgáltató konfigurálása több webalkalmazáshoz

A konfiguráció egyetlen webalkalmazáshoz működik, de további konfigurációra van szüksége, ha ugyanazt a megbízható identitásszolgáltatót kívánja használni több webalkalmazáshoz. Tegyük fel például, hogy kiterjesztettünk `https://sales.contoso.com` egy webalkalmazást az URL-cím használatára, és most a felhasználókat `https://marketing.contoso.com` is hitelesíteni szeretnénk. Ehhez frissítenie kell az identitásszolgáltatót a WReply paraméter tiszteletben tartandó, és frissítenie kell az alkalmazás regisztrációját az Azure AD-ben egy válasz URL-cím hozzáadásához.

1. Az Azure Portalon nyissa meg az Azure AD-címt. Kattintson a **Vállalati alkalmazások**menügombra, kattintson a **korábban létrehozott vállalati alkalmazás nevére,** majd az **Egyszeri bejelentkezés gombra.**

2. Az **Egyszeri bejelentkezés beállítása SAM-mel**lapon szerkesztsd az egyszerű **SAML-konfigurációt.**

    ![egyszerű SAML-konfiguráció szerkesztése](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. A **Válasz URL-cím (Helyességi feltétel fogyasztói szolgáltatás URL-címe)** adja hozzá a további webes alkalmazások URL-címét, és kattintson a **Mentés gombra.**

    ![egyszerű SAML-konfiguráció szerkesztése](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. A SharePoint-kiszolgálón nyissa meg a **SharePoint 201x felügyeleti rendszerhéjat,** és hajtsa végre a következő parancsokat a korábban használt megbízható identitásjogkivonat-kibocsátó nevével.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. A központi felügyelet, nyissa meg a webalkalmazást, és engedélyezze a meglévő megbízható identitásszolgáltató.

Előfordulhat, hogy más forgatókönyv, ahol szeretne hozzáférést biztosítani a helyszíni SharePoint a belső felhasználók számára, ebben a forgatókönyvben kellene telepítenie a Microsoft Azure Active Directory Connect, amely lehetővé teszi, hogy szinkronizálja a helyszíni felhasználók az Azure Active Directory, ez a beállítás része lenne egy másik cikk. 

## <a name="additional-resources"></a>További háttéranyagok

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a hibrid identitás az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)

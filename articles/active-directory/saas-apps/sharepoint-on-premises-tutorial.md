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
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81867033"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a helyi SharePoint-környezettel

Ez az oktatóanyag azt ismerteti, hogyan integrálható a helyszíni SharePoint a Azure Active Directory (Azure AD) szolgáltatással. Ha a helyszíni SharePointot az Azure AD-vel integrálja, a következőket teheti:

* A helyszíni SharePoint-hozzáféréshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a helyi SharePointba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Azure Active Directory a helyszíni SharePoint-integráció konfigurálásához a következő elemek szükségesek:

* Azure Active Directory előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Egy SharePoint 2013-Farm vagy újabb.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban a Azure Active Directory egyszeri bejelentkezést egy tesztkörnyezetben konfigurálja és teszteli. A Azure Active Directory felhasználók hozzáférhetnek a Sharepointhoz a helyszínen.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Vállalati alkalmazások létrehozása Azure Portal

A helyi SharePoint-környezet Azure AD-be való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a helyszíni SharePoint-szolgáltatást.

Ha a katalógusból szeretné felvenni a helyszíni SharePoint-szolgáltatást, hajtsa végre a következő lépéseket:

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

 > [!NOTE]
 > Ha az elem nem érhető el, a bal oldali navigációs panel felső részén található rögzített **összes szolgáltatás** hivatkozásra kattintva is megnyithatja. A következő áttekintésben a **Azure Active Directory** hivatkozás az **Identity (identitás** ) szakaszban található, vagy a szűrő szövegmező használatával kereshető.

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

4. A keresőmezőbe írja be a helyszíni **SharePoint**kifejezést, válassza a **helyi SharePoint** elemet az eredmények panelen.

    <kbd>![Helyszíni SharePoint a találatok listájában](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Adja meg a SharePoint-helyszíni nevét, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

1. Az új vállalati alkalmazásban kattintson a Tulajdonságok elemre, és jelölje be a **szükséges felhasználói hozzárendelés** értékét.

   <kbd>![Helyszíni SharePoint a találatok listájában](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

Ebben az esetben ez az érték a **nem**értékre van állítva.

## <a name="configure-and-test-azure-ad"></a>Az Azure AD konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja a helyszíni SharePoint szolgáltatással.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználó és a helyszíni SharePoint-beli kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Azure Active Directory egyszeri bejelentkezés konfigurálásához és teszteléséhez a helyszíni SharePoint használatával a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
1. A helyszíni **[SharePoint konfigurálása](#configure-sharepoint-on-premises)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
1. **[Hozzon létre egy Azure ad-tesztelési felhasználót a Azure Portalban](#create-an-azure-ad-test-user-in-the-azure-portal)** – hozzon létre egy új felhasználót az Azure ad-ben az egyszeri bejelentkezéshez.
1. **[Hozzon létre egy Azure ad biztonsági csoportot a Azure Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** – hozzon létre új biztonsági csoportot az Azure ad-ben az egyszeri bejelentkezéshez.
1. **[Engedélyek megadása Azure Active Directory fiókhoz a helyszíni SharePointban](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** – engedélyeket adhat az Azure ad-felhasználónak.
1. **[Engedélyek megadása az Azure ad-csoportnak a helyszíni SharePointban](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** – engedélyeket ad az Azure ad-csoportnak.
1. **[Hozzáférés biztosítása egy vendég fiókhoz a helyszíni SharePoint számára a Azure Portalban](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** – engedélyeket ad a vendég fióknak az Azure ad-ben a helyszíni sharepointhoz.
1. **[A megbízható identitás-szolgáltató konfigurálása több webalkalmazáshoz](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** – hogyan használhatja ugyanazt a megbízható identitás-szolgáltatót több webalkalmazáshoz

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a helyszíni SharePoint szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)nyissa meg az Azure ad-címtárat, kattintson a **vállalati alkalmazások**elemre, kattintson a **korábban létrehozott vállalati alkalmazás nevére** , és kattintson az **egyszeri bejelentkezés**lehetőségre.

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen kattintson az **SAML** mód lehetőségre az egyszeri bejelentkezés engedélyezéséhez.
 
3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Helyszíni SharePoint-tartomány és URL-címek egyszeri bejelentkezési adatai](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`urn:<sharepointFarmName>:<federationName>`

    1. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<YourSharePointSiteURL>/_trust/`

    1. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<YourSharePointSiteURL>/`
    1. kattintson a Save (Mentés) gombra.

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. A helyszíni **SharePoint-környezet beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.
    
    1. **Bejelentkezési URL-cím**
    
        A bejelentkezési URL-cím másolása a **/saml2** végén a **/wsfed**-vel való kiváltáshoz hasonlóan **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** fog kinézni (ez az URL-cím nem pontos)

    1. **Azure AD-azonosító**
    1. **Kijelentkezési URL-cím**
    > [!NOTE]
    > Ez az URL-cím nem használható a SharePointban: a **/saml2** a **/wsfed**-mel kell helyettesítenie. A helyszíni SharePoint-alkalmazás SAML 1,1-tokent használ, így az Azure AD a (z) a SharePoint-kiszolgálótól érkező, a hitelesítés után megjelenő, a SAML 1,1-et kibocsátó kérést jogkivonat.

### <a name="configure-sharepoint-on-premises"></a>A helyszíni SharePoint konfigurálása

1. **Új megbízható identitás-szolgáltató létrehozása a SharePoint Server 2016-ben**

    Jelentkezzen be a SharePoint-kiszolgálóra, és nyissa meg a SharePoint felügyeleti rendszerhéjat. Adja meg az értékeket:
    1. **$Realm** (az azonosító értékét a helyi SharePoint-tartomány és URL-címek szakaszból a Azure Portal).
    1. **$wsfedurl** (egyszeri bejelentkezési szolgáltatás URL-címe).
   1. **$filepath** (a fájl elérési útja, amelyhez letöltötte a tanúsítványfájl) a Azure Portalról.

    Futtassa az alábbi parancsokat egy új megbízható identitás-szolgáltató konfigurálásához.

    > [!TIP]
    > Ha most ismerkedik a PowerShell használatával, vagy szeretne többet megtudni a PowerShell működéséről, tekintse meg a [SharePoint PowerShellt](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


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
1. **Az alkalmazás megbízható identitás-szolgáltatójának engedélyezése**

    a. A központi felügyelet területen navigáljon a **webalkalmazás kezelése lapra** , és válassza ki azt a webalkalmazást, amelyet az Azure ad-vel szeretne védeni.

    b. A menüszalagon kattintson a **hitelesítésszolgáltatók** elemre, és válassza ki a használni kívánt zónát.

    c. Válassza ki a **megbízható identitás-szolgáltatót** , és válassza ki az imént regisztrált Szolgáltató *AzureAD*.

    d. Kattintson az **OK** gombra.

    ![A hitelesítési szolgáltató konfigurálása](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Azure AD-teszt felhasználó létrehozása a Azure Portalban

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Azure Portalban.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory**lehetőséget, majd a Pan **felügyelete** területen válassza a **felhasználók**lehetőséget.

2. Ezután válassza ki az **összes felhasználót** , majd az **új felhasználó** lehetőséget a képernyő tetején.

3. Válassza a **felhasználó létrehozása** lehetőséget, majd a felhasználó tulajdonságainál végezze el a következő lépéseket.  
   Előfordulhat, hogy a bérlői utótag vagy bármely ellenőrzött tartomány használatával létrehozhat felhasználókat az Azure AD-ben. 

    a. A **név** mezőben adja meg a felhasználónevet, a **tesztfelhasználó**használtuk.
  
    b. A **Felhasználónév** mezőbe írja be a következőt:`TestUser@yourcompanydomain.extension`  
    Például: TestUser@contoso.com

    ![A felhasználó párbeszédpanel](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

    e. Most már megoszthatja a webhelyet TestUser@contoso.com , és engedélyezheti, hogy ez a felhasználó hozzáférjen.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure AD biztonsági csoport létrehozása a Azure Portalban

1. Kattintson **Azure Active Directory > csoportok**elemre.

2. Kattintson az **új csoport**elemre:

3. Adja meg a **csoport típusát**, a **csoport nevét**, a **csoport leírását**, a **tagság típusát**. Kattintson a nyílra a tagok kiválasztásához, majd keressen rá, vagy kattintson arra a tagra, amelyet hozzá szeretne adni a csoporthoz. Kattintson a **kiválasztás** elemre a kijelölt tagok hozzáadásához, majd kattintson a **Létrehozás**gombra.

![Azure AD biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Engedélyek megadása a helyi SharePoint-fiók Azure Active Directoryéhez

Ahhoz, hogy hozzáférést biztosítson a helyi SharePoint Azure Active Directory felhasználójának, meg kell osztania a webhelycsoportot, vagy hozzá kell adnia a Azure Active Directory felhasználót a webhelycsoport egyik csoportjához. A felhasználók mostantól bejelentkezhetnek a SharePoint-201x az Azure AD-beli identitások használatával, de a felhasználói élmény fokozása továbbra is lehetőség van. A felhasználók keresése például több keresési eredményt is tartalmaz a People választóban. A jogcím-hozzárendelésben létrehozott összes jogcím-típushoz keresési eredmények tartoznak. Ha a felhasználók közül szeretne kiválasztani egy felhasználót, pontosan be kell írnia a felhasználónevét, és ki kell választania **a jogcím** eredményét.

![Jogcímek keresési eredményei](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

A keresett értékeket nem lehet érvényesíteni, ami a helytelen jogcím típusának véletlen elírását vagy a felhasználók véletlenül történő kiválasztását eredményezheti. Így megakadályozható, hogy a felhasználók hozzáférjenek az erőforrásokhoz.

Ha ezt az esetet **szeretné kijavítani, a** [AzureCP](https://yvand.github.io/AzureCP/) nevű nyílt forráskódú megoldás egy egyéni jogcím-szolgáltatót biztosít a SharePoint 2013, 2016 és 2019 számára. A Microsoft Graph API használatával oldja meg, hogy a felhasználók hogyan adja meg és hajtják végre az érvényesítést. További információ: [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > AzureCP nélkül hozzáadhat csoportokat az Azure AD-csoport AZONOSÍTÓjának hozzáadásával, de ez nem a felhasználó barátságos és megbízható. A következőképpen néz ki:  
  >   
  >![Azure AD-csoport hozzáadása a SharePoint-csoporthoz](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Engedélyek megadása az Azure AD-csoportnak a helyszíni SharePointban

Azure Active Directory biztonsági csoportok helyi SharePointhoz való hozzárendeléséhez a SharePoint-kiszolgáló egyéni jogcím-szolgáltatóját kell használnia. A példánkban a AzureCP használta.

 > [!NOTE]
 > Vegye figyelembe, hogy a AzureCP nem Microsoft-termék, vagy nem támogatott a Microsoft technikai támogatási szolgálata. Töltse le, telepítse és konfigurálja az AzureCP-t https://yvand.github.io/AzureCP/a helyszíni SharePoint-farmon. 

1. Konfigurálja a AzureCP a helyszíni SharePoint-farmon vagy egy alternatív egyéni jogcím-szolgáltatói megoldáson. a AzureCP-konfiguráció lépései a következő címen érhetők el:https://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. A Azure Portal nyissa meg az Azure AD-címtárat. Kattintson a **vállalati alkalmazások**elemre, majd a **korábban létrehozott vállalati alkalmazás nevére** , és kattintson az **egyszeri bejelentkezés**lehetőségre.

1. Az **egyszeri bejelentkezés a Sam**-mel beállítás lapon szerkessze a **felhasználói attribútumok & jogcímek** szakaszt.

1. Kattintson a **Csoport hozzáadása**elemre.

1. Válassza ki a felhasználóhoz társított csoportokat a jogcímben, a mi esetünkben válassza az **összes csoport** elemet, majd a forrás attribútum szakaszban válassza a **csoport azonosítója** elemet, majd kattintson a **Mentés**gombra.

Ahhoz, hogy hozzáférést biztosítson a helyszíni SharePoint Azure Active Directory biztonsági csoportjához, meg kell osztania a webhelycsoportot, vagy hozzá kell adnia a Azure Active Directory biztonsági csoportot a webhelycsoport egyik csoportjához.

1. Tallózással keresse meg a SharePoint-webhely gyűjteményét, a webhelycsoport hely beállításai részen kattintson a "személyek és csoportok" elemre. Válassza ki a SharePoint-csoportot, majd kattintson az új elemre, majd a "felhasználók hozzáadása ehhez a csoporthoz" lehetőségre, és kezdje meg a csoport nevének megadását, amelyet a People Picker megjelenít a Azure Active Directory biztonsági csoportban.

    ![Azure AD-csoport hozzáadása a SharePoint-csoporthoz](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Hozzáférés biztosítása egy vendég fiókhoz a helyszíni SharePoint számára a Azure Portal

Mostantól konzisztens módon lehet hozzáférést biztosítani a SharePoint-webhelyhez egy vendég fiókhoz. Az egyszerű felhasználónév módosul. A felhasználó jdoe@outlook.com a következőhöz hasonló `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`lesz:. A webhelynek a külső felhasználókkal való megosztása közben zökkenőmentes élményre lehet szükség, ha a Azure Portal a **felhasználói attribútumok & a jogcímek** című szakaszban néhány módosítást kell hozzáadnia.

1. A Azure Portal nyissa meg az Azure AD-címtárat. Kattintson a **vállalati alkalmazások**elemre, majd a **korábban létrehozott vállalati alkalmazás nevére** , és kattintson az **egyszeri bejelentkezés**lehetőségre.

1. Az **egyszeri bejelentkezés a Sam**-mel beállítás lapon szerkessze a **felhasználói attribútumok & jogcímek** szakaszt.

1. a **szükséges jogcímek** zónában kattintson az **egyedi felhasználói azonosítóra (név azonosító)**.

1. Módosítsa a **forrás attribútum** tulajdonságot a **User. localuserprincipalname** értékre, és **mentse**.

    ![Felhasználói attribútumok & jogcímek kezdeti](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. A menüszalag használatával térjen vissza az **SAML-alapú bejelentkezéshez** most a **felhasználói attribútumok & a jogcímek** szakasz a következőképpen fog kinézni: 

    ![Felhasználói attribútumok & jogcímek végleges](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Ebben a beállításban nem szükséges a vezetékneve és a megadott név.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, majd válassza a **felhasználók**lehetőséget.

1. kattintson az **új vendég felhasználó** elemre

1. Válassza a **felhasználó meghívása** lehetőséget, és töltse ki a felhasználó tulajdonságait, és kattintson a **meghívás**elemre.

1. Most már megoszthatja a webhelyet MyGuestAccount@outlook.com , és engedélyezheti, hogy ez a felhasználó hozzáférjen.

    ![Webhely megosztása a vendég fiókkal](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>A megbízható identitás-szolgáltató konfigurálása több webalkalmazáshoz

A konfiguráció egyetlen webalkalmazás esetében működik, de további konfigurálásra van szükség, ha ugyanazt a megbízható identitás-szolgáltatót szeretné használni több webalkalmazáshoz. Tegyük fel például, hogy kibővítettünk egy webalkalmazást az `https://sales.contoso.com` URL-cím használatára, és most a felhasználókat `https://marketing.contoso.com` is hitelesíteni szeretné. Ehhez frissítenie kell az WReply paramétert, és frissítenie kell az alkalmazás regisztrációját az Azure AD-ben a válasz URL-címének hozzáadásához.

1. A Azure Portal nyissa meg az Azure AD-címtárat. Kattintson a **vállalati alkalmazások**elemre, majd a **korábban létrehozott vállalati alkalmazás nevére** , és kattintson az **egyszeri bejelentkezés**lehetőségre.

2. Az **egyszeri bejelentkezés a Sam használatával beállításnál**szerkessze az **ALAPszintű SAML-konfigurációt**.

    ![alapszintű SAML-konfiguráció szerkesztése](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** lapon adja meg a további webalkalmazások URL-címét, majd kattintson a **Mentés**gombra.

    ![alapszintű SAML-konfiguráció szerkesztése](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. A SharePoint-kiszolgálón nyissa meg a **SharePoint 201X felügyeleti rendszerhéját** , és hajtsa végre a következő parancsokat a korábban használt megbízható azonosító jogkivonat-kiállító neve alapján.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. A központi felügyelet lapon nyissa meg a webalkalmazást, és engedélyezze a meglévő megbízható identitás-szolgáltatót.

Előfordulhat, hogy a belső felhasználók számára szeretne hozzáférést biztosítani a helyszíni Sharepointhoz, ebben a forgatókönyvben olyan Microsoft Azure Active Directory Connect kell telepítenie, amely lehetővé teszi, hogy a helyszíni felhasználókat Azure Active Directoryokkal szinkronizálja, ez a beállítás egy másik cikk részét képezi. 

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a hibrid identitás a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)

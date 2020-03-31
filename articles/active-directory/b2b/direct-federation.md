---
title: Közvetlen összevonás egy identitásszolgáltatóval a B2B - Azure AD
description: Közvetlenül összeolvad egy SAML vagy WS-Fed identitásszolgáltatóval, hogy a vendégek bejelentkezhessenek az Azure AD-alkalmazásokba
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050879"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Közvetlen összevonás az AD FS-sel és a vendégfelhasználók külső szolgáltatóival (előzetes verzió)
|     |
| --- |
| A közvetlen összevonás az Azure Active Directory nyilvános előzetes verziójú szolgáltatása. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ez a cikk azt ismerteti, hogyan lehet beállítani a közvetlen összevonás egy másik szervezet b2B együttműködés. Közvetlen összevonást állíthat be minden olyan szervezettel, amelynek identitásszolgáltatója (IdP) támogatja az SAML 2.0 vagy a WS-Fed protokollt.
Amikor közvetlen összevonást állít be egy partner IdP-jével, az adott tartomány új vendégfelhasználói saját IdP által felügyelt szervezeti fiókjukkal jelentkezhetnek be az Azure AD-bérlőbe, és elkezdhetik az önnel való együttműködést. Nincs szükség arra, hogy a vendégfelhasználó hozzon létre egy külön Azure AD-fiókot.
> [!NOTE]
> A közvetlen összevonási vendégfelhasználóknak a bérlői környezetet `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>`tartalmazó hivatkozással kell bejelentkezniük `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`(például vagy , vagy ellenőrzött tartomány esetén). Az alkalmazásokhoz és erőforrásokhoz mutató közvetlen hivatkozások is működnek, amennyiben tartalmazzák a bérlői környezetet. A közvetlen összevonási felhasználók jelenleg nem tudnak bejelentkezni olyan közös végpontok használatával, amelyek nem rendelkeznek bérlői környezettel. Ha például `https://myapps.microsoft.com` `https://portal.azure.com`a `https://teams.microsoft.com` , a, vagy hibát okoz.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Mikor hitelesítegy egy vendégfelhasználót a közvetlen összevonással?
Miután beállította a közvetlen összevonást egy szervezettel, a meghívott új vendégfelhasználók hitelesítése közvetlen összevonással lesz hitelesítve. Fontos megjegyezni, hogy a közvetlen összevonás beállítása nem változtatja meg a hitelesítési módszert azon vendégfelhasználók számára, akik már beváltották a meghívást. Néhány példa:
 - Ha a vendégfelhasználók már beváltották a meghívókat Öntől, és ezt követően közvetlen összevonást állított be a szervezetükkel, akkor ezek a vendégfelhasználók továbbra is ugyanazt a hitelesítési módszert fogják használni, amelyet a közvetlen összevonás beállítása előtt használtak.
 - Ha közvetlen összevonást állít be egy partnerszervezettel, és meghívja a vendégfelhasználókat, majd a partnerszervezet később az Azure AD-re költözik, a már beváltott meghívókat bekérő vendégfelhasználók továbbra is a közvetlen összevonást fogják használni, feltéve, hogy a közvetlen a bérlőben létezik.
 - Ha közvetlen összevonást töröl egy partnerszervezettel, a közvetlen összevonást jelenleg használó vendégfelhasználók nem tudnak bejelentkezni.

A következő esetekben frissítheti a vendégfelhasználó hitelesítési módszerét a vendég felhasználói fiók törlésével a címtárból, majd újra meghívja őket.

A közvetlen összevonás tartománynévterekhez, például contoso.com és fabrikam.com van kötve. Amikor közvetlen összevonási konfigurációt hoz létre az AD FS-sel vagy egy külső idp-vel, a szervezetek egy vagy több tartománynévteret társítanak ezekhez az idP-khez. 

## <a name="end-user-experience"></a>Végfelhasználói élmény 
A közvetlen összevonás, a vendég felhasználók jelentkezzen be az Azure AD-bérlő saját szervezeti fiók használatával. Amikor megosztott erőforrásokat használnak, és bejelentkezésre kérnek, a közvetlen összevonási felhasználók átlesznek irányítva az IdP-jükre. Sikeres bejelentkezés után azok az Azure AD-ba az erőforrások eléréséhez. A közvetlen összevonási felhasználók frissítési jogkivonatai 12 óráig érvényesek, ami az [áthaladási frissítési jogkivonat alapértelmezett hossza](../develop/active-directory-configurable-token-lifetimes.md#exceptions) az Azure AD-ben. Ha az összevont idp sso engedélyezve van, a felhasználó sso-t fog tapasztalni, és nem jelenik meg a bejelentkezési kérdés a kezdeti hitelesítés után.

## <a name="limitations"></a>Korlátozások

### <a name="dns-verified-domains-in-azure-ad"></a>DNS-ellenőrzéssel ellenőrzött tartományok az Azure AD-ben
A tartományt, amelyet össze szeretne adni, ***nem*** ellenőrizheti a DNS-t az Azure AD-ben. Közvetlen összevonást állíthat be nem felügyelt (e-mail-ellenőrzésű vagy "vírusos") Azure AD-bérlőkkel, mert nincsenek DNS-ellenőrzésük alatt.

### <a name="authentication-url"></a>Hitelesítés URL-címe
A közvetlen összevonás csak olyan házirendek esetében engedélyezett, ahol a hitelesítési URL-tartomány a céltartománynak felel meg, vagy ahol a hitelesítési URL az engedélyezett identitásszolgáltatók egyike (ez a lista változhat):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Ha például **a fabrikam.com**közvetlen összevonását `https://fabrikam.com/adfs` állítja be, a hitelesítési URL-cím megfelel az érvényesítésen. Az ugyanabban a tartományban lévő `https://sts.fabrikam.com/adfs`állomás is átmegy, például . A hitelesítési `https://fabrikamconglomerate.com/adfs` URL-cím vagy `https://fabrikam.com.uk/adfs` ugyanahhoz a tartományhoz azonban nem fog átmenni.

### <a name="signing-certificate-renewal"></a>Tanúsítvány megújítása aláírás
Ha megadja a metaadat-URL-címet az identitásszolgáltató beállításaiban, az Azure AD automatikusan megújítja az aláíró tanúsítványt, amikor lejár. Ha azonban a tanúsítvány tágított bármilyen okból a lejárati idő előtt, vagy ha nem ad meg metaadat-URL-címet, az Azure AD nem lesz képes megújítani. Ebben az esetben manuálisan kell frissítenie az aláíró tanúsítványt.

### <a name="limit-on-federation-relationships"></a>Összevonási kapcsolatok korlátozása
Jelenleg legfeljebb 1000 összevonási kapcsolat támogatott. Ez a korlát magában foglalja a [belső összevonásokat](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) és a közvetlen összevonásokat is.

### <a name="limit-on-multiple-domains"></a>Korlát több tartományra
Jelenleg nem támogatjuk a közvetlen összevonást több, ugyanabból a bérlőből származó tartományokkal.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Beállíthatok közvetlen összevonást egy olyan tartománnyal, amelyhez nem felügyelt (e-mail-ellenőrzéslel rendelkező) bérlő létezik? 
Igen. Ha a tartomány nincs ellenőrizve, és a bérlő nem ment át [rendszergazdai átvételen,](../users-groups-roles/domains-admin-takeover.md)beállíthatja a közvetlen összevonást az adott tartománnyal. Nem felügyelt, vagy e-mail-ellenőrzött, bérlők jönnek létre, amikor a felhasználó beváltja a B2B meghívást, vagy önkiszolgáló regisztrációt hajt végre az Azure AD-re egy olyan tartomány használatával, amely jelenleg nem létezik. Ezekkel a tartományokkal közvetlen összevonást állíthat be. Ha egy DNS-ellenőrzésű tartománnyal próbál közvetlen összevonást beállítani, akár az Azure Portalon, akár a PowerShellen keresztül, hibaüzenet jelenik meg.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Ha a közvetlen összevonás és az e-mail egyszeri jelkód-hitelesítés egyaránt engedélyezve van, melyik módszer élvez elsőbbséget?
Ha közvetlen összevonásjön létre egy partnerszervezettel, az elsőbbséget élvez az e-mail egyszeri jelkód-hitelesítéssel szemben az adott szervezet új vendégfelhasználói számára. Ha egy vendégfelhasználó egyszeri jelkód-hitelesítéssel váltott be meghívót a közvetlen összevonás beállítása előtt, továbbra is egyszeri jelkód-hitelesítést fog használni. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>A közvetlen összevonás egy részlegesen szinkronizált bérlő miatt kezeli a bejelentkezési problémákat?
Nem, ebben a forgatókönyvben az [e-mail egyszeri jelkód-funkciót](one-time-passcode.md) kell használni. A "részlegesen szinkronizált bérleti" egy partner Azure AD-bérlő, ahol a helyszíni felhasználói identitások nincsenek teljesen szinkronizálva a felhőben. Az a vendég, akinek a személyazonossága még nem létezik a felhőben, de megpróbálja beváltani a B2B meghívót, nem tud bejelentkezni. Az egyszeri jelkód funkció lehetővé tenné a vendég számára, hogy bejelentkezzen. A közvetlen összevonási szolgáltatás olyan forgatókönyveket címez, amelyekben a vendég saját IdP által felügyelt szervezeti fiókkal rendelkezik, de a szervezet egyáltalán nem rendelkezik Azure AD-jelenléttel.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>1. lépés: A partnerszervezet identitásszolgáltatójának konfigurálása
Először a partnerszervezetnek konfigurálnia kell az identitásszolgáltatóját a szükséges jogcímekkel és függő entitásmegbízhatóságokkal. 

> [!NOTE]
> Annak szemléltetésére, hogyan konfigurálhat identitásszolgáltatót a közvetlen összevonáshoz, példaként az Active Directory összevonási szolgáltatásokat (AD FS) használjuk. Tekintse meg a [Közvetlen összevonás konfigurálása az AD FS-sel](direct-federation-adfs.md)című témakört, amely példákat tartalmaz arra, hogyan konfigurálható az AD FS SAML 2.0 vagy WS-Fed identitásszolgáltatóként a közvetlen összevonás előkészítéseként.

### <a name="saml-20-configuration"></a>SAML 2.0 konfiguráció

Az Azure AD B2B konfigurálható úgy, hogy az SAML protokollt az alábbiakban felsorolt speciális követelményekkel rendelkező identitásszolgáltatókkal egytelenítsen. Az SAML-identitásszolgáltató és az Azure AD közötti megbízhatósági kapcsolat beállításáról az [Egyszerbejelentkezési szolgáltatás saml 2.0-s identitásszolgáltatójának (IdP) című](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)témakörben talál további információt.  

> [!NOTE]
> A közvetlen összevonás céltartománya nem ellenőrizhető az Azure AD-n. A hitelesítési URL-tartománynak meg kell egyeznie a céltartománnyal, vagy egy engedélyezett identitásszolgáltató tartományának kell lennie. A [részleteket](#limitations) a Korlátozások című részben találja. 

#### <a name="required-saml-20-attributes-and-claims"></a>Szükséges SAML 2.0 attribútumok és jogcímek
Az alábbi táblázatok az adott attribútumokra és jogcímekre vonatkozó követelményeket mutatják be, amelyeket a külső identitásszolgáltatónál kell konfigurálni. A közvetlen összevonás beállításához a következő attribútumokat kell fogadni az SAML 2.0 válaszban az identitásszolgáltatótól. Ezek az attribútumok az online biztonsági tokenszolgáltatás XML-fájljára való hivatkozással vagy manuális beírással konfigurálhatók.

Az IdP SAML 2.0 válaszához szükséges attribútumok:

|Attribútum  |Érték  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partner IdP kibocsátóuri-ja, például`http://www.example.com/exk10l6w90DHM0yi...`         |


Az IdP által kiadott SAML 2.0 token szükséges jogcímek:

|Attribútum  |Érték  |
|---------|---------|
|NameID formátum     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|e-mail cím     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed konfiguráció 
Az Azure AD B2B konfigurálható úgy, hogy a WS-Fed protokollt használó identitásszolgáltatókkal egytelenítsen bizonyos speciális követelményekkel az alábbiakban felsoroltak szerint. Jelenleg a két WS-Fed szolgáltatót tesztelték az Azure AD-vel való kompatibilitás szempontjából, beleértve az AD FS-t és a Shibboleth-et. A WS-Fed-kompatibilis szolgáltatók és az Azure AD közötti függő entitásmegbízhatóság létrehozásáról az [Azure AD-identitásszolgáltató kompatibilitási dokumentumaiban](https://www.microsoft.com/download/details.aspx?id=56843)található "STS-integrációs papír ws protokollok használatával" című témakörben talál további információt.

> [!NOTE]
> A közvetlen összevonás céltartománya nem ellenőrizhető az Azure AD-n. A hitelesítési URL-tartománynak meg kell egyeznie a céltartománysal vagy az engedélyezett identitásszolgáltató tartományával. A [részleteket](#limitations) a Korlátozások című részben találja. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Szükséges WS-Fed attribútumok és jogcímek

Az alábbi táblázatok az adott attribútumokra és jogcímekre vonatkozó követelményeket mutatják be, amelyeket a külső WS-Fed identitásszolgáltatónál kell konfigurálni. A közvetlen összevonás beállításához a következő attribútumokat kell fogadni a WS-Fed üzenetben az identitásszolgáltatótól. Ezek az attribútumok az online biztonsági tokenszolgáltatás XML-fájljára való hivatkozással vagy manuális beírással konfigurálhatók.

A WS-Fed üzenet szükséges attribútumai az IdP-től:
 
|Attribútum  |Érték  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A partner IdP kibocsátóuri-ja, például`http://www.example.com/exk10l6w90DHM0yi...`         |

Az IdP által kiadott WS-Fed tokenhez szükséges jogcímek:

|Attribútum  |Érték  |
|---------|---------|
|Nem módosítható azonosító     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|e-mail cím     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>2. lépés: A közvetlen összevonás konfigurálása az Azure AD-ben 
Ezután konfigurálja az összevonást az Azure AD 1. Használhatja az Azure AD portal vagy a PowerShell. A közvetlen összevonási házirend érvénybe lépése 5-10 percet is igénybe vehet. Ez alatt az idő alatt ne kíséreljen meg meghívót beváltani a közvetlen összevonási tartományra. A következő attribútumokra van szükség:
- A partneri azonosító kibocsátói URI-ja
- A partner IdP passzív hitelesítési végpontja (csak a https támogatott)
- Tanúsítvány

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Közvetlen összevonás konfigurálása az Azure AD-portálon

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com/) A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **Szervezeti kapcsolatok lehetőséget.**
3. Válassza **az Identitásszolgáltatók**lehetőséget, majd az **Új SAML/WS-Fed IdP**lehetőséget.

    ![Képernyőkép új SAML vagy WS-Fed idP hozzáadásához](media/direct-federation/new-saml-wsfed-idp.png)

4. Az **Új SAML/WS-Fed IdP** lap **Identitásszolgáltató protokoll jain**válassza az **SAML** vagy **a WS-FED**lehetőséget.

    ![Képernyőkép az ELEMZÉS gombbal az SAML vagy a WS-Fed IdP oldalon](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Adja meg a partnerszervezet tartománynevét, amely a közvetlen összevonás céltartomány-neve lesz.
6. A metaadatadatok részleteinek feltöltéséhez metaadatfájlt tölthet fel. Ha úgy dönt, hogy manuálisan adja meg a metaadatokat, adja meg a következő adatokat:
   - A partner IdP tartományneve
   - A partnerazonosító entitásazonosítója
   - A partner IdP passzív kérővagy végpontja
   - Tanúsítvány
   > [!NOTE]
   > A metaadat-URL nem kötelező, de erősen ajánljuk. Ha megadja a metaadat-URL-címet, az Azure AD automatikusan megújíthatja az aláíró tanúsítványt, amikor lejár. Ha a tanúsítvány tágít a lejárati idő előtt, vagy ha nem ad meg metaadat-URL-címet, az Azure AD nem lesz képes megújítani. Ebben az esetben manuálisan kell frissítenie az aláíró tanúsítványt.

7. Kattintson a **Mentés** gombra. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Közvetlen összevonás konfigurálása az Azure AD-ben a PowerShell használatával

1. Telepítse az Azure AD PowerShell graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját. (Ha részletes lépésekre van szüksége, a vendégfelhasználó hozzáadásának rövid útmutatója tartalmazza [a Legújabb AzureADPreview modul telepítése](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)című szakaszt.) 
2. Futtassa az alábbi parancsot: 
   ```powershell
   Connect-AzureAD
   ```
1. A bejelentkezési kérdésnél jelentkezzen be a felügyelt globális rendszergazdai fiókkal. 
2. Futtassa a következő parancsokat, és cserélje le az összevonási metaadatfájl értékeit. Az AD FS-kiszolgáló és az Okta esetében az összevonási `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`fájl a federationmetadata.xml, például: . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>3. lépés: Közvetlen összevonás tesztelése az Azure AD-ben
Most tesztelje a közvetlen összevonási beállításokat egy új B2B vendégfelhasználó meghívásával. További információt az [Azure AD B2B együttműködési felhasználóinak hozzáadása az Azure Portalon című témakörben talál.](add-users-administrator.md)
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hogyan szerkeszthetek közvetlen összevonási kapcsolatot?

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com/) A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **Szervezeti kapcsolatok lehetőséget.**
3. **Identitásszolgáltatók** kiválasztása
4. Az **SAML/WS-Fed identitásszolgáltatók**csoportban válassza ki a szolgáltatót.
5. Az identitásszolgáltató részletei ablaktáblán frissítse az értékeket.
6. Kattintson a **Mentés** gombra.


## <a name="how-do-i-remove-direct-federation"></a>Hogyan távolíthatom el a közvetlen összevonást?
Eltávolíthatja a közvetlen összevonási beállításokat. Ha így tesz, a közvetlen összevonási vendégfelhasználók, akik már beváltották a meghívóikat, nem tudnak bejelentkezni. De újra hozzáférést adhat nekik az erőforrásokhoz, ha kiirtja őket a könyvtárból, és újra meghívja őket. Az Azure AD-portálon lévő identitásszolgáltatóval való közvetlen összevonás eltávolítása:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com/) A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **Szervezeti kapcsolatok lehetőséget.**
3. Válassza **az Identitásszolgáltatók lehetőséget.**
4. Jelölje ki az identitásszolgáltatót, majd kattintson a **Törlés gombra.** 
5. A törlés megerősítéséhez válassza az **Igen** lehetőséget. 

Az identitásszolgáltatóval való közvetlen összevonás eltávolítása a PowerShell használatával:
1. Telepítse az Azure AD PowerShell graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa az alábbi parancsot: 
   ```powershell
   Connect-AzureAD
   ```
3. A bejelentkezési kérdésnél jelentkezzen be a felügyelt globális rendszergazdai fiókkal. 
4. Írja be a következő parancsot:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

---
title: Állítsa be a közvetlen összevonási egy identitásszolgáltatóval B2B - Azure Active Directory-hez |} A Microsoft Docs
description: Közvetlenül egy SAML vagy WS-Fed identitásszolgáltatóval összevonni, így a vendégek jelentkezhetnek be az Azure AD-alkalmazások
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bc3c1325e8379082134e2cbec1586f7d338ee61
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797939"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Közvetlen összevonás az AD FS és a külső szolgáltatók a vendégfelhasználók számára (előzetes verzió)
|     |
| --- |
| Közvetlen összevonási az Azure Active Directory nyilvános előzetes verziójú funkció. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ez a cikk ismerteti, hogyan állítható be a B2B-együttműködés másik szervezettel közvetlen összevonási. Beállíthatja a közvetlen összevonás az minden olyan szervezet, amelynek identitásszolgáltató (IdP) támogatja az SAML 2.0 vagy WS-Fed protokollt.
Az identitásszolgáltató egy partner által létrehozott közvetlen összevonási beállításakor új vendégfelhasználókat ebből a tartományból segítségével saját identitásszolgáltató által felügyelt szervezeti fiókkal jelentkezzen be az Azure AD-bérlőhöz, és indítsa el a dolgoznak együtt. Nem kell a Vendég felhasználó hozzon létre külön Azure AD-fiókot.
> [!NOTE]
> Közvetlen összevonási vendégfelhasználók kell jelentkezzen be egy hivatkozást, amely tartalmazza a bérlő környezetben (például `https://myapps.microsoft.com/?tenantid=<tenant id>` vagy `https://portal.azure.com/<tenant id>`, vagy egy ellenőrzött tartomány esetén `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). Alkalmazások és erőforrások mutató közvetlen hivatkozásokat is működik, amíg a bérlő környezet tartalmazzák. Közvetlen összevonási felhasználók nem tudnak jelenleg jelentkezzen be a közös végpontok, amelyeken nincs bérlői környezet. Például `https://myapps.microsoft.com`, `https://portal.azure.com`, vagy `https://teams.microsoft.com` hibát eredményez.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Ha a vendégfelhasználó adatokkal van hitelesítve közvetlen összevonási?
Miután beállította az adott szervezetnél közvetlen összevonási, minden olyan új vendégfelhasználókat, hogy meghívja a hitelesítése közvetlen összevonási használatával. Fontos megjegyezni, hogy közvetlen összevonási beállítása nem módosítja a vendég felhasználók, akik már beváltott egy meghívást arra, hogy a hitelesítési módszert. Néhány példa:
 - Ha vendég felhasználók már beváltott, amit ezt követően állítsa be a szervezettel közvetlen összevonási Segítségkérések, vendég felhasználók továbbra is ugyanazt a hitelesítési módszert használják a közvetlen összevonási beállítása előtt használatára.
 - Állítsa be a közvetlen összevonás az erőforráspartner-szervezet és a vendégfelhasználók meghívása, és később a fiókpartner-szervezet helyezi az Azure AD, ha a vendégfelhasználók számára már beváltott meghívók továbbra is közvetlen összevonási, közvetlenül a lehető leghosszabbak használata a bérlő összevonási szabályzat létezik.
 - Ha törli az erőforráspartner-szervezet közvetlen összevonási, bármely vendég felhasználók közvetlen összevonással jelenleg nem lehet bejelentkezni az lesz.

Ezek az esetek bármelyikében a vendégfelhasználó hitelesítési módszer a Vendég felhasználói fiókhoz törlésével a címtárból, és azokat reinviting frissítheti.

Tartományi névtér használatára korlátozzuk, például a contoso.com és fabrikam.com közvetlen összevonási vannak kötve. Az AD FS vagy egy külső identitásszolgáltató egy közvetlen összevonási konfiguráció létrehozásakor a szervezetek egy vagy több tartomány névtereket, hogy ezek az identitásszolgáltató társítja. 

## <a name="end-user-experience"></a>Végfelhasználói élmény 
Közvetlen összevonással vendégfelhasználók jelentkezzen be az Azure AD-bérlő saját szervezeti fiókkal. Amikor megosztott erőforrásokat érnek el, és jelentkezzen be a rendszer kéri, közvetlen összevonási felhasználókat a rendszer átirányítja az identitásszolgáltató. A sikeres bejelentkezést követően visszakerül az Azure AD-erőforrások eléréséhez. A közvetlen összevonási felhasználók frissítési jogkivonatok érvényesek 12 órán át, a [csatlakoztatott frissítési jogkivonat hosszának alapértelmezett](../develop/active-directory-configurable-token-lifetimes.md#exceptions) az Azure ad-ben. Az összevont identitásszolgáltató egyszeri bejelentkezés engedélyezve van, ha a felhasználó egyszeri bejelentkezés fog tapasztalni, és nem jelenik meg minden olyan bejelentkezési kérések jelenhetnek kezdeti hitelesítést követően.

## <a name="limitations"></a>Korlátozások

### <a name="dns-verified-domains-in-azure-ad"></a>DNS-ellenőrzött tartományok az Azure ad-ben
Összevonási csak tartományok számára engedélyezett a közvetlen ***nem*** DNS ellenőrzése az Azure ad-ben. Közvetlen összevonási a nem felügyelt (e-mailben ellenőrzött vagy "vírusos") az Azure AD bérlők, mert a DNS-ellenőrzése nem engedélyezett.
### <a name="authentication-url"></a>Hitelesítési URL-címe
Közvetlen összevonási csak engedélyezett szabályzatok, a hitelesítési URL-cím-tartomány megfelel-e a cél tartománynak, vagy ahol a hitelesítési URL-cím egyike, ezek engedélyezett Identitásszolgáltatók (ebben a listában a változhat):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Például, ha a közvetlen összevonási beállítása **fabrikam.com**, a hitelesítési URL-cím `https://fabrikam.com/adfs` fogja továbbítani az ellenőrzést. Egy gazdagép ugyanabban a tartományban is továbbítja, például `https://sts.fabrikam.com/adfs`. Azonban a hitelesítési URL-cím `https://fabrikamconglomerate.com/adfs` vagy `https://fabrikam.com.uk/adfs` az ugyanabban a tartományban nem adja át.

### <a name="signing-certificate-renewal"></a>Aláíró tanúsítvány megújítása
Ha az identitás-szolgáltató beállításait a metaadatok URL-címet ad meg, az Azure AD automatikusan megújul az aláíró tanúsítvány a lejárat után. Azonban ha a tanúsítvány a lejárati idő előtt rotálásakor bármilyen okból, vagy ha nem ad meg a metaadatok URL-címe, az Azure AD nem tudja újíthatja meg. Ebben az esetben kell manuálisan frissítse az aláíró tanúsítvány.
## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Állítható be a tartomány, amelyhez egy nem felügyelt bérlőt (e-mailben ellenőrzött) létezik közvetlen összevonási? 
Igen. Ha még nem lett ellenőrizve a tartományhoz, és a bérlő még nem esett át egy [alá vonhatja rendszergazdai átvétellel](../users-groups-roles/domains-admin-takeover.md), állíthat be közvetlen összevonási. Nem felügyelt, vagy e-mailben ellenőrzött, bérlők jönnek létre, amikor egy felhasználó egy B2B meghívó visszaváltja vagy hajt végre egy önkiszolgáló, amely jelenleg nem létezik tartomány használata az Azure ad. Beállíthatja a közvetlen összevonás az ezekből a tartományokból. Ha megpróbál egy DNS-ellenőrzött tartomány, az Azure Portalon vagy a PowerShell használatával, közvetlen összevonási beállítása látni fogja a hibát.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Ha közvetlen összevonási és e-mailben kapott egyszeri jelszót hitelesítést egyaránt engedélyezve van, melyik módszert élvez elsőbbséget?
Ha közvetlen összevonási létrejön a a fiókpartner-szervezet, adott szervezet új vendégfelhasználó e-mailben kapott egyszeri jelszót hitelesítésnél élvez elsőbbséget. Vendégfelhasználó meghívó egyszer használatos jelszót hitelesítéssel közvetlen összevonási beállítása előtt válthatók be, ha azok továbbra is egyszeri jelszó-hitelesítés használatára. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Nem közvetlen összevonási bejelentkezési problémák miatt a részlegesen szinkronizált bérlős?
Nem, a [e-mailben kapott egyszeri jelszót](one-time-passcode.md) funkció ebben a forgatókönyvben használandó. Egy "részlegesen szinkronizált bérlős" hivatkozik egy partner Azure AD-bérlővel, ahol a helyszíni felhasználói identitások nem teljesen szinkronizált a felhőben. A Vendég, akinek identitása még nem létezik a felhőben, de akik próbál a B2B-meghívó beváltása nem lehet bejelentkezni. A kapott egyszer használatos jelszót funkció lehetővé tenné a Vendég való bejelentkezéshez. A közvetlen összevonási szolgáltatás címek forgatókönyvek, ahol a Vendég rendelkezik saját identitásszolgáltató által felügyelt szervezeti fiókkal, de a szervezet rendelkezik nem Azure ad-ben jelenlét egyáltalán.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>1\. lépés: A fiókpartner-szervezet identitásszolgáltató konfigurálása
Először a fiókpartner-szervezet kell az identitásszolgáltató konfigurálása a szükséges jogcímek és a függő entitások megbízhatóságainak. 

> [!NOTE]
> Közvetlen összevonási identitásszolgáltatót konfigurálása mutatja be, az Active Directory összevonási szolgáltatások (AD FS) fogjuk használni példaként. Tekintse meg a cikket [közvetlen összevonás konfigurálása az AD FS-sel](direct-federation-adfs.md), amely példákat AD FS konfigurálása, a SAML 2.0 vagy WS-Fed identitásszolgáltató közvetlen összevonási előkészítésekor.

### <a name="saml-20-configuration"></a>SAML 2.0 konfigurálása

Az Azure AD B2B identitásszolgáltatókkal, az alább felsorolt különleges követelményeket az SAML protokollt használó összevonásához konfigurálható. Az identitásszolgáltató SAML és az Azure AD közötti megbízhatósági kapcsolat beállításával kapcsolatos további információkért lásd: [egy SAML 2.0 identitásszolgáltató (IdP) használata az egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Megjegyzés: A céltartomány közvetlen összevonáshoz nem lehet DNS-ellenőrzése az Azure ad-ben. A hitelesítési URL-cím-tartomány meg kell egyeznie a céltartományba, vagy engedélyezett Identitásszolgáltatók a tartományban kell lennie. Tekintse meg a [korlátozások](#limitations) című szakasz részletezi. 

#### <a name="required-saml-20-attributes-and-claims"></a>Szükséges SAML 2.0 attribútumokról és jogcímekről
Az alábbi táblázatokban adott attribútumok és a jogcímeket kell konfigurálni, a külső identitásszolgáltató. Közvetlen összevonási beállításával kapcsolatban a következő attribútumokkal kell érkeznie az identitásszolgáltatótól az SAML 2.0 válaszként. Ezek az attribútumok konfigurálható az online biztonsági jogkivonatokkal kapcsolatos szolgáltatástól XML-fájl összekapcsolása, vagy írja be őket manuálisan.

Az identitásszolgáltató SAML 2.0 válasza a szükséges attribútumok:

|Attribútum  |Value  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A kibocsátó URI-t a partner identitásszolgáltató, például `http://www.example.com/exk10l6w90DHM0yi...`         |


Az identitásszolgáltató a kiállított SAML 2.0-token szükséges jogcímek:

|Attribútum  |Value  |
|---------|---------|
|NameID Format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|e-mail cím     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed konfiguráció 
Az Azure AD B2B identitásszolgáltatókat, amelyek a WS-Fed protokollt használja az egyes konkrét követelmények, az alább felsorolt összevonni kívánt konfigurálható. A két WS-Fed szolgáltató jelenleg teszteltük, az Azure AD-kompatibilitás az AD FS és a Shibboleth tartalmazzák. Között WS-Fed megfelelő szolgáltató és az Azure AD függő entitások megbízhatóságainak létrehozásával kapcsolatos további információkért lásd: a "STS-integráció WS protokollok használatával tanulmány" érhető el a [az Azure AD Identity Provider kompatibilitási Docs](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> A célként megadott tartományt összevonásra közvetlen nem lehet DNS-ellenőrzése az Azure ad-ben. A hitelesítési URL-cím-tartomány vagy a céltartományra, vagy a tartomány engedélyezett Identitásszolgáltatók egyeznie kell. Tekintse meg a [korlátozások](#limitations) című szakasz részletezi. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Szükséges WS-Fed attribútumokról és jogcímekről

Az alábbi táblázatokban adott attribútumok és a jogcímeket kell konfigurálni, a WS-Fed külső identitásszolgáltató. Közvetlen összevonási beállításával kapcsolatban a következő attribútumokkal kell érkeznie, a WS-Fed üzenetben az identitásszolgáltatótól. Ezek az attribútumok konfigurálható az online biztonsági jogkivonatokkal kapcsolatos szolgáltatástól XML-fájl összekapcsolása, vagy írja be őket manuálisan.

Az identitásszolgáltató a WS-Fed üzenetben szükséges attribútumok:
 
|Attribútum  |Érték  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Célközönség     |`urn:federation:MicrosoftOnline`         |
|Kiállító     |A kibocsátó URI-t a partner identitásszolgáltató, például `http://www.example.com/exk10l6w90DHM0yi...`         |

A WS-Fed token az identitásszolgáltató által kiadott jogcímeket szükséges:

|Attribútum  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|e-mail cím     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>2\. lépés: Az Azure AD közvetlen összevonás konfigurálása 
Ezután konfigurálhatja összevonási, az Azure AD-ben az 1. lépésben konfigurált identitásszolgáltatóval. Az Azure AD portálon vagy a PowerShell is használhatja. 5 – 10 percet, mielőtt a közvetlen összevonási házirend érvénybe lép igénybe vehet. Ebben az időszakban ne kísérelje meg a közvetlen összevonási tartomány meghívó beváltása. A következő attribútumok szükség:
- URI-ját partner identitásszolgáltató kibocsátója
- Passzív hitelesítési végpontja partner identitásszolgáltató (csak https támogatott)
- Tanúsítvány

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Közvetlen összevonás konfigurálásához az Azure AD portálon

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza ki **szervezeti kapcsolatok**.
3. Válassza ki **Identitásszolgáltatók**, majd válassza ki **új SAML/WS-Fed identitásszolgáltató**.

    ![Képernyőkép ábrázoló gombra egy új SAML vagy WS-Fed identitásszolgáltató hozzáadása](media/direct-federation/new-saml-wsfed-idp.png)

4. Az a **új SAML/WS-Fed identitásszolgáltató** lap **Identitásszolgáltatói protokoll**válassza **SAML** vagy **WS-FED**.

    ![Képernyőkép parse gombja SAML vagy WS-Fed identitásszolgáltató lapon](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Adja meg a partner szervezet tartománynév, amely a célként megadott tartománynév közvetlen összevonási lesz
6. Feltölthet egy metaadatfájl feltöltése metaadatok részletei. Ha a bemeneti metaadatok manuális választja, adja meg a következőket:
   - Identitásszolgáltató partner tartományneve
   - Identitásszolgáltató partner entitás azonosítója
   - Passzív kérelmező végpontját partner identitásszolgáltató
   - Tanúsítvány
   > [!NOTE]
   > Metaadatok URL-címe megadása nem kötelező, azonban erősen ajánlott. A metaadatok URL-címet ad meg, ha az Azure AD a lejárat után automatikusan az aláíró tanúsítvány is megújítása. Ha a tanúsítvány a lejárati idő előtt rotálásakor bármilyen okból, vagy ha nem ad meg a metaadatok URL-címe, az Azure AD nem tudja újíthatja meg. Ebben az esetben kell manuálisan frissítse az aláíró tanúsítvány.

7. Kattintson a **Mentés** gombra. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Közvetlen összevonás konfigurálásához az Azure AD PowerShell-lel

1. Telepítse a legújabb verziót az Azure AD PowerShell modul a Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Ha részletes lépéseit, a rövid útmutatóban a vendégfelhasználó hozzáadása tartalmazza-e a szakasz [telepítse a legújabb AzureADPreview modulban](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Futtassa a következő parancsot: 
   ```powershell
   Connect-AzureAD
   ```
1. A bejelentkezési parancssorba jelentkezzen be a felügyelt globális rendszergazdai fiókkal. 
2. Futtassa a következő parancsokat, és cserélje le az összevonási metaadatokat tartalmazó fájlban szereplő értékek alapján. Az AD FS-kiszolgáló és az Okta, az összevonási fájlban federationmetadata.XML mintát követi, például: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>3\. lépés: Az Azure AD közvetlen összevonási tesztelése
Most már a közvetlen összevonási beállításának ellenőrzéséhez B2B új vendégfelhasználó meghívásával. További információkért lásd: [hozzáadása az Azure AD B2B együttműködési felhasználókat az Azure Portalon](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hogyan tudom szerkeszteni egy közvetlen összevonási kapcsolat?

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza ki **szervezeti kapcsolatok**.
3. Válassza ki **Identitásszolgáltatók**
4. A **SAML/WS-Fed Identitásszolgáltatók**, válassza ki a szolgáltatót.
5. Az identity provider részleteket tartalmazó ablaktáblán frissítse az értékeket.
6. Kattintson a **Mentés** gombra.


## <a name="how-do-i-remove-direct-federation"></a>Hogyan távolíthatom el a közvetlen összevonási?
A közvetlen összevonási beállításának távolíthatja el. Ha így tesz, a meghívást már beváltott közvetlen összevonási vendég felhasználók nem jelentkezhetnek be. De átadásával hozzáférés az erőforrásokhoz való újra törlésével őket a könyvtárból, és reinviting őket. Egy identitásszolgáltatóval közvetlen összevonási eltávolítása az Azure AD portálon:

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza ki **szervezeti kapcsolatok**.
3. Válassza ki **Identitásszolgáltatók**.
4. Az identitásszolgáltató, majd válassza ki és **törlése**. 
5. Válassza ki **Igen** a törlés megerősítéséhez. 

Egy identitásszolgáltatóval közvetlen összevonási eltávolítása a PowerShell használatával:
1. Telepítse a legújabb verziót az Azure AD PowerShell modul a Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Futtassa a következő parancsot: 
   ```powershell
   Connect-AzureAD
   ```
3. A bejelentkezési parancssorba jelentkezzen be a felügyelt globális rendszergazdai fiókkal. 
4. Írja be a következő parancsot:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

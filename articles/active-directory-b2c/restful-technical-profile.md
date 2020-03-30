---
title: RESTful technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Definiáljon egy RESTful technikai profilt egy egyéni szabályzatban az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332612"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>RESTful technikai profil definiálása az Azure Active Directory B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja a saját RESTful szolgáltatás integrálását. Az Azure AD B2C adatokat küld a RESTful szolgáltatás egy bemeneti jogcímgyűjtés és adatok fogadása vissza a kimeneti jogcímek gyűjteménye. További [információ: REST API-jogcímek integrálása az Azure AD B2C egyéni szabályzatában.](custom-policy-rest-api-intro.md)  

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `Proprietary`parancsra kell állítani. A **kezelő** attribútumnak tartalmaznia kell az Azure AD B2C által használt `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`protokollkezelő szerelvény teljesen minősített nevét: .

A következő példa egy RESTful technikai profilt mutat be:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

Az **InputClaims** elem a REST API-nak küldő jogcímek listáját tartalmazza. A jogcím nevét is leképezheti a REST API-ban definiált névre. A következő példa a szabályzat és a REST API közötti leképezést mutatja be. A **givenName** jogcímet a REST API **keresztnév**ként, **a vezetéknevet** pedig **lastName**név ként küldi el a rendszer. Az **e-mail** jogcím a következőképpen van beállítva.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Az **InputClaimsTransformations** elem tartalmazhat **InputClaimsTransformation** elemek gyűjteményét, amelyek a bemeneti jogcímek módosítására vagy újak létrehozására szolgálnak a REST API-ba való küldés előtt.

## <a name="send-a-json-payload"></a>JSON-tartalom küldése

A REST API technikai profilja lehetővé teszi, hogy egy összetett JSON-tartalom küldése egy végpontra.

Összetett JSON-tartalom küldése:

1. Készítse el a JSON hasznos teher a [GenerateJson](json-transformations.md) jogcímek átalakítása.
1. A REST API technikai profiljában:
    1. Adjon hozzá egy bemeneti jogcím-átalakítást a `GenerateJson` jogcím-transzformációra való hivatkozással.
    1. A `SendClaimsIn` metaadatok beállításbeállítása`body`
    1. Állítsa `ClaimUsedForRequestPayload` a metaadat-beállítást a JSON-hasznos adatot tartalmazó jogcím nevére.
    1. A bemeneti jogcímben adjon hozzá egy hivatkozást a JSON-hasznos adatot tartalmazó bemeneti jogcímre.

A következő `TechnicalProfile` példa egy külső e-mail szolgáltatás (ebben az esetben sendgrid) használatával küld egy ellenőrző e-mailt.

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem a REST API által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy le kell képeznie a házirendben definiált jogcím nevét a REST API-ban definiált névhez. Olyan jogcímeket is megadhat, amelyeket a REST API-identitásszolgáltató `DefaultValue` nem ad vissza, feltéve, hogy beállítja az attribútumot.

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

A következő példa a REST API által visszaadott jogcímet mutatja be:

- A **LoyaltyNumber** jogcímnevéhez leképezett **MembershipId** jogcím.

A technikai profil is visszaadja a jogcímeket, amelyeket nem ad vissza az identitásszolgáltató:

- A **loyaltyNumberIsNew** jogcím, amelynek `true`alapértelmezett értéke .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ServiceUrl | Igen | A REST API-végpont URL-címe. |
| AuthenticationType (Hitelesítéstípusa) | Igen | A RESTful jogcímszolgáltató által végrehajtott hitelesítés típusa. Lehetséges `None`értékek: `Basic` `Bearer`, `ClientCertificate`, , vagy . Az `None` érték azt jelzi, hogy a REST API nem névtelen. Az `Basic` érték azt jelzi, hogy a REST API HTTP alapszintű hitelesítéssel van biztosítva. Csak ellenőrzött felhasználók, beleértve az Azure AD B2C, hozzáférhetnek az API-t. Az `ClientCertificate` (ajánlott) érték azt jelzi, hogy a REST API korlátozza a hozzáférést az ügyféltanúsítvány-hitelesítés használatával. Csak a megfelelő tanúsítványokkal rendelkező szolgáltatások, például az Azure AD B2C férhetnek hozzá az API-hoz. Az `Bearer` érték azt jelzi, hogy a REST API korlátozza a hozzáférést az ügyfél OAuth2 bemutatóhely-jogkivonathasználatával. |
| AllowInsecureAuthInProduction| Nem| Azt jelzi, `AuthenticationType` hogy `none` a beállítás éles`DeploymentMode` környezetben állítható-e be (a [TrustFrameworkPolicy](trustframeworkpolicy.md) beállítása `Production`, vagy nincs megadva). Lehetséges értékek: igaz vagy hamis (alapértelmezett). |
| Sendclaimsin | Nem | Itt adható meg, hogy a bemeneti jogcímek hogyan kerüljön elküldésre a RESTful jogcímszolgáltatónak. Lehetséges `Body` értékek: `Form`(alapértelmezett), , `Header`vagy . `QueryString` Az `Body` érték az a bemeneti jogcím, amelyet a kérelem törzsében JSON formátumban küld a rendszer. Az `Form` érték az a bemeneti jogcím, amelyet a kérelem törzsében egy "&" elválasztott kulcsérték-formátumban küld a rendszer. Az `Header` érték a kérelem fejlécében küldött bemeneti jogcím. Az `QueryString` érték a kérelem lekérdezési karakterláncában küldött bemeneti jogcím. Az egyes által meghívott HTTP-műveletek a következők:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| Jogcímformátum | Nem | Jelenleg nem használt, figyelmen kívül hagyható. |
| JogcímUsedForRequestPayload| Nem | A REST API-nak küldendő hasznos tartalmat tartalmazó karakterláncjogcím neve. |
| Hibakeresési mód | Nem | A technikai profilt hibakeresési módban futtatja. Lehetséges értékek: `true` `false` vagy (alapértelmezett). Hibakeresési módban a REST API több információt adhat vissza. Lásd a [Visszatérő hibaüzenet](#returning-error-message) szakaszt. |
| IncludeClaimResolvingInClaimsHandling  | Nem | Bemeneti és kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` `false`  vagy (alapértelmezett). Ha a technikai profilban jogcímfeloldót szeretne `true`használni, állítsa ezt a beállításra. |
| ResolveJsonPathsInJsonTokens  | Nem | Azt jelzi, hogy a technikai profil feloldja-e a JSON-útvonalakat. Lehetséges értékek: `true` `false` vagy (alapértelmezett). Ezekkel a metaadatokkal adatokat olvashat be egy beágyazott JSON-elemből. Egy [OutputClaim](technicalprofiles.md#outputclaims)alkalmazásban `PartnerClaimType` állítsa be a kimenetet a kimenetre kívánt JSON-elérésiút-elemet. Például: `firstName.localized`vagy `data.0.to.0.email`.|
| UseClaimAsBearerToken| Nem| A tulajdonosi jogkivonatot tartalmazó jogcím neve.|

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

Ha a hitelesítés típusa `None`a beállítás, a **Kriptográfiai kulcsok** elem nem lesz használva.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Ha a hitelesítés típusa `Basic`a beállítás, a **Kriptográfiai kulcsok** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Igen | A hitelesítéshez használt felhasználónév. |
| BasicAuthenticationPassword | Igen | A hitelesítéshez használt jelszó. |

A következő példa egy alapfokú hitelesítéssel rendelkező technikai profilt mutat be:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Ha a hitelesítés típusa `ClientCertificate`a beállítás, a **Kriptográfiai kulcsok** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Ügyféltanúsítvány | Igen | A hitelesítéshez használt X509-tanúsítvány (RSA-kulcskészlet). |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Ha a hitelesítés típusa `Bearer`a beállítás, a **Kriptográfiai kulcsok** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Nem | Az OAuth 2.0 bemutatóra token. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Hibaüzenet visszaadása

Előfordulhat, hogy a REST API-nak hibaüzenetet kell visszaadnia, például " A felhasználó nem található a CRM rendszerben". Hiba esetén a REST API-nak http 4xx hibaüzenetet kell visszaadnia, például 400(rossz kérés) vagy 409 (ütközés) válaszállapotkódot. A választörzs JSON formátumú hibaüzenetet tartalmaz:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| version | Igen | A REST API verziója. Például: 1.0.1 |
| status | Igen | 409-nek kell lennie |
| code | Nem | Hibakód a RESTful végpontszolgáltatótól, amely `DebugMode` akkor jelenik meg, ha engedélyezve van. |
| Kérelemazonosító | Nem | A RESTful végpontszolgáltató tól érkező kérelemazonosító, `DebugMode` amely akkor jelenik meg, ha engedélyezve van. |
| userMessage (felhasználóÜzenet) | Igen | A felhasználó számára megjelenő hibaüzenet. |
| developerMessage | Nem | A probléma részletes leírása és megoldása, amely akkor `DebugMode` jelenik meg, ha engedélyezve van. |
| moreInfo | Nem | Olyan URI, amely további információkra `DebugMode` mutat, amelyek akkor jelennek meg, ha engedélyezve van. |


A következő példa egy C# osztályt mutat be, amely hibaüzenetet ad vissza:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>További lépések

A RESTful technikai profil használatára vonatkozó példákat az alábbi cikkekben talál:

- [Rest API-jogcímek cseréjének integrálása az Azure AD B2C egyéni szabályzatába](custom-policy-rest-api-intro.md)
- [Forgatókönyv: Rest API-jogcímek cseréjének integrálása az Azure AD B2C felhasználói útba a felhasználói bevitel érvényesítéseként](custom-policy-rest-api-claims-validation.md)
- [Forgatókönyv: REST API-jogcímek cseréje az Azure Active Directory B2C egyéni szabályzataihoz](custom-policy-rest-api-claims-validation.md)
- [A REST API-szolgáltatások biztonságossá tétele](secure-rest-api.md)


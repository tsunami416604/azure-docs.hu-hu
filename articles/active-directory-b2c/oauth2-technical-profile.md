---
title: OAuth2 technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: OAuth2 technikai profildefiniálása egyéni szabályzatban az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 86ec7a5745a58546faf6f0ff15d6dc5f452baa88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184043"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OAuth2 technikai profil definiálása az Azure Active Directory B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja az OAuth2 protokoll identitásszolgáltató. Az OAuth2 az engedélyezés és a delegált hitelesítés elsődleges protokollja. További információ: [RFC 6749 The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749). Az OAuth2 technikai profillal összeegyítheti egy OAuth2 alapú identitásszolgáltatóval, például a Facebookkal. Az identitásszolgáltatóval való összeegyezés lehetővé teszi a felhasználók számára, hogy meglévő közösségi vagy vállalati identitásukkal jelentkezzenek be.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `OAuth2`parancsra kell állítani. Például a **Facebook-OAUTH** technikai profil `OAuth2`protokollja a következő:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

Az **InputClaims** és **az InputClaimsTransformations** elemek nem szükségesek. Előfordulhat azonban, hogy további paramétereket szeretne küldeni az identitásszolgáltatónak. A következő példa hozzáadja a **domain_hint** `contoso.com` lekérdezési karakterlánc-paraméter értékét az engedélyezési kérelemhez.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem az OAuth2 identitásszolgáltató által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy a házirendben definiált jogcím nevét hozzá kell képeznie az identitásszolgáltatóban megadott névhez. Az identitásszolgáltató által vissza nem adott jogcímeket is megadhat, amíg beállítja az `DefaultValue` attribútumot.

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

A következő példa a Facebook-identitásszolgáltató által visszaadott jogcímeket mutatja be:

- A **first_name** jogcím a **givenName** jogcímhez van rendelve.
- A **last_name** követelés a **vezetéknév-követelésre** van leképezve.
- A **displayName** jogcím névleképezés nélkül.
- Az **e-mail** jogcím névleképezés nélkül.

A technikai profil olyan jogcímeket is visszaad, amelyeket az identitásszolgáltató nem ad vissza:

- Az identitásszolgáltató nevét tartalmazó **identityProvider** jogcím.
- A **authenticationSource** jogcím a **socialIdpAuthentication**alapértelmezett értékével.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az identitásszolgáltató alkalmazásazonosítója. |
| IdTokenAudience | Nem | A id_token közönsége. Ha meg van adva, az Azure AD B2C ellenőrzi, hogy a jogkivonat az identitásszolgáltató által visszaadott jogcímben van-e, és megegyezik-e a megadott jogkivonattal. |
| authorization_endpoint | Igen | Az engedélyezési végpont URL-címe a 6749-es RFC-kód szerint. |
| AccessTokenEndpoint | Igen | A tokenvégpont URL-címe az RFC 6749 szerint. |
| ClaimsEndpoint | Igen | A felhasználói információs végpont URL-címe a 6749-es RFC-nek. |
| AccessTokenResponseformat | Nem | A hozzáférési jogkivonat végponti hívásának formátuma. Például a Facebook http GET metódust igényel, de a hozzáférési jogkivonat-válasz JSON formátumban van. |
| AdditionalRequestQueryParameters | Nem | További kérelemlekérdezési paraméterek. Előfordulhat például, hogy további paramétereket szeretne küldeni az identitásszolgáltatónak. Vesszővel határoló jel használatával több paramétert is felvehet. |
| ClaimsEndpointAccessTokenName | Nem | A hozzáférési jogkivonat lekérdezési karakterlánc-paraméterének neve. Egyes identitásszolgáltatók jogcímvégpontjai támogatják a GET HTTP-kérelmet. Ebben az esetben a tulajdonosi jogkivonatot az engedélyezési fejléc helyett egy lekérdezési karakterlánc-paraméter rel küldi el a rendszer. |
| ClaimsEndpointFormatName | Nem | A query string format paraméter neve. Beállíthatja például a nevet `format` ebben a LinkedIn-jogcímvégpontban. `https://api.linkedin.com/v1/people/~?format=json` |
| ClaimsEndpointFormat | Nem | A query karakterlánc-formátum paraméter értéke. Beállíthatja például az értéket `json` ebben a `https://api.linkedin.com/v1/people/~?format=json`LinkedIn-jogcímvégpontban. |
| Szolgáltatóneve | Nem | Az identitásszolgáltató neve. |
| response_mode | Nem | Az a módszer, amelyet az identitásszolgáltató az eredmény az Azure AD B2C-nek való visszaküldéséhez használ. Lehetséges `query`értékek: `form_post` , (alapértelmezett) vagy `fragment`. |
| scope | Nem | Az OAuth2 identitásszolgáltató specifikációja szerint meghatározott kérelem hatóköre. Mint `openid`például a , `profile`és `email`. |
| HttpKötés | Nem | A várt HTTP-kötés a hozzáférési jogkivonathoz és a jogcímjog végpontjaihoz. Lehetséges `GET` értékek: `POST`vagy .  |
| ResponseErrorCodeParamName | Nem | A HTTP 200 (Ok) névnél visszaadott hibaüzenetet tartalmazó paraméter neve. |
| ExtraParamsInAccessTokenEndpointResponse | Nem | Azokat a további paramétereket tartalmazza, amelyeket egyes identitásszolgáltatók visszaadhatnak az **AccessTokenEndpoint** válaszában. Például az **AccessTokenEndpoint** válasza tartalmaz egy `openid`további paramétert, például a , amely a **ClaimsEndpoint** kérelem lekérdezési karakterláncában access_token kívül kötelező paraméter. Több paraméternevet ki kell kerülni, és vesszővel el kell választani a "," határolójeltől. |
| ExtraParamsInClaimsEndpointRequest | Nem | Azokat a további paramétereket tartalmazza, amelyeket egyes identitásszolgáltatók visszaadhatnak a **ClaimsEndpoint-kérelemben.** Több paraméternevet ki kell kerülni, és vesszővel el kell választani a "," határolójeltől. |
| IncludeClaimResolvingInClaimsHandling  | Nem | Bemeneti és kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` `false`  vagy (alapértelmezett). Ha a technikai profilban jogcímfeloldót szeretne `true`használni, állítsa ezt a beállításra. |
| ResolveJsonPathsInJsonTokens  | Nem | Azt jelzi, hogy a technikai profil feloldja-e a JSON-útvonalakat. Lehetséges értékek: `true` `false` vagy (alapértelmezett). Ezekkel a metaadatokkal adatokat olvashat be egy beágyazott JSON-elemből. Egy [OutputClaim](technicalprofiles.md#outputclaims)alkalmazásban `PartnerClaimType` állítsa be a kimenetet a kimenetre kívánt JSON-elérésiút-elemet. Például: `firstName.localized`vagy `data.0.to.0.email`.|

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A **Kriptográfiai kulcsok** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Az identitásszolgáltató alkalmazás ügyféltkati titka. A titkosítási kulcs csak **response_types** akkor szükséges, ha `code`a response_types metaadat értéke . Ebben az esetben az Azure AD B2C egy másik hívást kezdeményez egy hozzáférési jogkivonat engedélyezési kódjának cseréjéhez. Ha a metaadatok `id_token`a beállításra vannak állítva, akkor kihagyhatja a titkosítási kulcsot. |

## <a name="redirect-uri"></a>Átirányítási URI

Az identitásszolgáltató átirányítási URL-címének konfigurálásakor írja be a . `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp` Győződjön meg arról, hogy cserélje **le a bérlő** nevét (például contosob2c.onmicrosoft.com) és **policyId** a szabályzat azonosítóját (például b2c_1a_policy). Az átirányítási URI-nak kisbetűsnek kell lennie.

Ha a **b2clogin.com** tartományt használja **login.microsoftonline.com** ügyeljen arra, hogy b2clogin.com használjon login.microsoftonline.com helyett.

Példák:

- [A Google+ hozzáadása OAuth2 identitásszolgáltatóként egyéni irányelvek használatával](identity-provider-google-custom.md)














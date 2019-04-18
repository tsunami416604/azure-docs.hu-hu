---
title: Egy egyéni házirendek az Azure Active Directory B2C az OAuth2 technikai profil meghatározása |} A Microsoft Docs
description: Adja meg egy egyéni házirendet az Azure Active Directory B2C az OAuth2 technikai profil.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fde556c60f823f4bd287ca5672503158c7292f51
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918926"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Az OAuth2 technikai profil meghatározása az Azure Active Directory B2C egyéni házirendek

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C az OAuth2 protokoll identitásszolgáltató támogatja. Ez a delegált hitelesítési és engedélyezési elsődleges protokollja. További információkért lásd: a [RFC 6749 az OAuth 2.0 engedélyezési keretrendszer](https://tools.ietf.org/html/rfc6749). Az OAuth2-technikai profilban meg az OAuth2 használatával is összevonható alapú identitásszolgáltató, például a Facebook és a Live.com, így Ön a felhasználók jelentkezzen be a meglévő közösségi vagy vállalati identitásokat.

## <a name="protocol"></a>Protokoll

A **neve** attribútuma a **protokoll** elemet hozzá kell beállítani `OAuth2`. Például a protokoll a **Facebook-OAUTH** technikai profil `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...    
```

## <a name="input-claims"></a>A bemeneti jogcímek

A **InputClaims** és **InputClaimsTransformations** elemek nem szükségesek. Azonban előfordulhat, hogy szeretne küldeni a további paraméterek segítségével az identitásszolgáltatókhoz. Az alábbi példa hozzáadja a **domain_hint** lekérdezési karakterlánc paraméterének értékét `contoso.com` az engedélyezési kérés.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem az OAuth2 identitásszolgáltató által visszaküldött jogcímek listáját tartalmazza. Szükség lehet a meghatározott az identitásszolgáltató nevét a szabályzatban meghatározott jogcím nevének való leképezéséhez. Nem adott vissza az identitásszolgáltató mindaddig, amíg meg jogcímeket is használható a `DefaultValue` attribútum.

A **OutputClaimsTransformations** elemet tartalmazhat egy gyűjteményét **OutputClaimsTransformation** elemek, amelyek segítségével módosíthatja a kimeneti jogcímek, és hozzon létre újakat.

Az alábbi példa bemutatja a jogcímeket, a Facebook-identitás szolgáltató által visszaadott:

- A **first_name** jogcím van leképezve a **givenName** jogcím.
- A **first_name** jogcím van leképezve a **Vezetéknév** jogcím.
- A **displayName** jogcím anélkül, hogy a felhasználónév-leképezés …
- A **e-mail** jogcím a felhasználónév-leképezés nélkül.

A technikai profil is az identitásszolgáltató nem adott vissza jogcímeket adja vissza: 

- A **identityProvider** jogcímet, amelyet az identitásszolgáltató nevét tartalmazza.
- A **authenticationSource** jogcím alapértelmezett értéke **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az identitásszolgáltató az alkalmazás azonosítója. |
| IdTokenAudience | Nem | A id_token célközönségét. Ha meg van adva, az Azure AD B2C-vel ellenőrzi, hogy a jogkivonat az identitásszolgáltató által visszaadott jogcím szerepel-e, és megadott egyenlő. |
| authorization_endpoint | Igen | RFC 6749 megfelelően az engedélyezési végpont URL-címe |
| AccessTokenEndpoint | Igen | RFC 6749 megfelelően a jogkivonat-végpont URL-címe |  
| ClaimsEndpoint | Igen | A végpont URL-címét a felhasználó információt RFC 6749 megfelelően. | 
| AccessTokenResponseFormat | Nem | A hozzáférési jogkivonat-végpont hívás formátumát. Például Facebook egy HTTP GET módszert igényel, de a hozzáférési jogkivonat-válasz JSON formátumban vannak. |
| AdditionalRequestQueryParameters | Nem | További kérelem lekérdezési paraméterek. Például előfordulhat, hogy szeretne küldeni további paraméterek segítségével az identitásszolgáltatókhoz. Elválasztó karakterként használatával több paramétereket is megadhat. | 
| ClaimsEndpointAccessTokenName | Nem | A hozzáférési jogkivonat lekérdezésisztring-paraméter neve. Néhány Identitásszolgáltatók jogcímek végpontokat támogatja a GET HTTP kérést. Ebben az esetben a tulajdonosi jogkivonat a lekérdezési sztring paramétereként helyett az engedélyezési fejléc zajlik. |
| ClaimsEndpointFormatName | Nem | A formátum lekérdezésisztring-paraméter neve. Például beállíthatja a nevet a következőként `format` a LinkedIn a jogcím-végpont `https://api.linkedin.com/v1/people/~?format=json`. | 
| ClaimsEndpointFormat | Nem | A formátum lekérdezésisztring-paraméter értéke. Például beállíthatja a értékként `json` a LinkedIn a jogcím-végpont `https://api.linkedin.com/v1/people/~?format=json`. | 
| ProviderName | Nem | Az identitásszolgáltató neve. |
| response_mode | Nem | A módszer, amely az identitásszolgáltató nem küldi vissza az eredményt az Azure AD B2C-t használja. A lehetséges értékek: `query`, `form_post` (alapértelmezett), vagy `fragment`. |
| scope | Nem | A hozzáférési kérés alapján az OAuth2 identity provider specifikáció hatókörét. Például `openid`, `profile`, és `email`. |
| HttpBinding | Nem | A várt HTTP-kötést a hozzáférési jogkivonatot, és a jogcímek jogkivonat végpontokhoz. A lehetséges értékek: `GET` vagy `POST`.  |
| ResponseErrorCodeParamName | Nem | Neve a paraméter, amely a HTTP 200 (Ok) keresztül visszaadott hibaüzenet tartalmazza. |
| ExtraParamsInAccessTokenEndpointResponse | Nem | A válasz, a visszaadható a további paramétereket tartalmaz **AccessTokenEndpoint** néhány identitásszolgáltatók által. Például válasza **AccessTokenEndpoint** például tartalmaz egy kiegészítő paraméterrel `openid`, azaz mellett a access_token a paramétert kötelező megadni egy **ClaimsEndpoint** kérelem lekérdezési karakterlánc. Több paraméterneveket kell escape-karakterrel és a vesszővel elválasztva ',' elválasztó karaktert. |
| ExtraParamsInClaimsEndpointRequest | Nem | A további paraméterek, a visszaadható tartalmazza a **ClaimsEndpoint** néhány Identitásszolgáltatók kérelmét. Több paraméterneveket kell escape-karakterrel és a vesszővel elválasztva ',' elválasztó karaktert. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | A titkos ügyfélkulcsot az identity provider alkalmazás. A titkosítási kulcsot kötelező megadni, ha csak a **response_types** metaadat értéke `code`. Ebben az esetben az Azure AD B2C-vel hívást egy másik az engedélyezési kódot, a hozzáférési jogkivonatot. Ha a metaadat értéke `id_token` kihagyhatja a titkosítási kulcs.  |  

## <a name="redirect-uri"></a>Átirányítási URI

Az identitásszolgáltató az átirányítási URL-cím konfigurálásakor adja meg a `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Cserélje le **bérlői** a bérlő nevét (például: contosob2c.onmicrosoft.com) és **policyId** azonosítóval a szabályzat (például b2c_1a_policy). Az átirányítási URI-t kell lennie az összes kisbetűt.

Ha használja a **b2clogin.com** helyett tartományi **login.microsoftonline.com** ügyeljen arra, hogy a b2clogin.com használata helyett login.microsoftonline.com.

Példák:

- [Adja hozzá a Google + identitás-szolgáltatóként OAuth2 egyéni szabályzatok használatával](active-directory-b2c-custom-setup-goog-idp.md)














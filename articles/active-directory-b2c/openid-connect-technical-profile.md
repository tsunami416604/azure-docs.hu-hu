---
title: OpenId Connect technikai profil meghatározása egy egyéni házirendek az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az Azure Active Directory B2C egyéni szabályzat OpenId Connect technikai profil.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e8ee72b9add1947929e5c772edafd55d28edd79b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54851045"
---
# <a name="define-a-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OpenId Connect technikai profil meghatározása az Azure Active Directory B2C egyéni házirendek

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C támogatást biztosít a [OpenId Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protokoll identitásszolgáltató. OpenID Connect 1.0 határozza meg az identitási rétegben OAuth 2.0-s felett, és a legmodernebb a modern hitelesítési protokollok jelöli.  OpenId-kapcsolattal a technikai profilban, akkor is összevonható az OpenId Connect-alapú identitásszolgáltató, például az Azure AD lehetővé teszi felhasználók jelentkezzen be a meglévő közösségi vagy vállalati identitásokat.

## <a name="protocol"></a>Protokoll

A **neve** attribútuma a **protokoll** elemet hozzá kell beállítani `OpenIdConnect`. Például a protokoll a **MSA-OIDC** technikai profil `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

A **OutputClaims** elem a jogcímek, az OpenId Connect identitásszolgáltató által visszaadott listáját tartalmazza. Szükség lehet a meghatározott az identitásszolgáltató nevét a szabályzatban meghatározott jogcím nevének való leképezéséhez. Is használható az identitásszolgáltató nem adott vissza jogcímeket, amennyiben beállította a `DefaultValue` attribútum.

A **OutputClaimsTransformations** elemet tartalmazhat egy gyűjteményét **OutputClaimsTransformation** elemek, amelyek segítségével módosíthatja a kimeneti jogcímek, és hozzon létre újakat.

Az alábbi példa bemutatja a Microsoft Account identitásszolgáltató által visszaküldött jogcímek:

- A **sub** jogcímet, amelyet le van képezve a **socialIdpUserId** jogcím.
- A **neve** jogcímet, amelyet le van képezve a **displayName** jogcím.
- A **e-mail** felhasználónevek hozzárendelése nélkül.

A technikai profil is az identitásszolgáltató nem adott vissza jogcímeket adja vissza:

- A **identityProvider** jogcímet, amelyet az identitásszolgáltató nevét tartalmazza.
- A **authenticationSource** jogcím alapértelmezett értéke **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az identitásszolgáltató az alkalmazás azonosítója. |
| IdTokenAudience | Nem | A id_token célközönségét. Ha meg van adva, az Azure AD B2C-vel ellenőrzi, hogy a jogkivonat az identitásszolgáltató által visszaadott jogcím szerepel-e, és megadott egyenlő. |
| METADATA | Igen | Egy mutató URL-cím egy JSON konfigurációs dokumentum az OpenID Connect-felderítési specifikáció, amely más néven a jól ismert openid konfigurációs végpont megfelelően formázott. |
| ProviderName | Nem | Az identitásszolgáltató neve. |
| response_types | Nem | Válasz típusa az OpenID Connect Core 1.0 specifikáció szerint. A lehetséges értékek: `id_token`, `code`, vagy `token`. |
| response_mode | Nem | A módszer, amely az identitásszolgáltató nem küldi vissza az eredményt az Azure AD B2C-t használja. A lehetséges értékek: `query`, `form_post` (alapértelmezett), vagy `fragment`. |
| scope | Nem | A hozzáférési kérelem, az OpenID Connect Core 1.0 specifikáció alapján hatókörét. Például `openid`, `profile`, és `email`. |
| HttpBinding | Nem | A várt HTTP-kötést a hozzáférési jogkivonatot, és a jogcímek jogkivonat végpontokhoz. A lehetséges értékek: `GET` vagy `POST`.  |
| ValidTokenIssuerPrefixes | Nem | Egy kulcs, amely segítségével jelentkezzen be a bérlők mindegyike egy több-bérlős identitásszolgáltató, például az Azure Active Directory használatakor. |
| UsePolicyInRedirectUri | Nem | Azt jelzi, hogy egy szabályzatot használja, ha az átirányítási URI-t hozhat létre. Az identitásszolgáltató az alkalmazás konfigurálásakor adja meg az átirányítási URI-t kell. Az átirányítási URI-t mutat az Azure AD B2C- `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` (a login.microsoftonline.com a saját bérlő name.b2clogin.com változhat).  Ha megad `false`, átirányítási URI-t használ minden egyes házirend hozzá kell adnia. Például: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nem | Azt jelzi, hogy e sikertelen egy külső szolgáltatás kérelmet kell megjelölni, ha a Http-állapotkód: az 5xx tartományban. A mező alapértelmezett értéke: `false`. |
| DiscoverMetadataByTokenIssuer | Nem | Azt jelzi, hogy OIDC metaadatok kell felderítése a kibocsátó a JWT-jogkivonat használatával. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | A titkos ügyfélkulcsot az identity provider alkalmazás. A titkosítási kulcsot kötelező megadni, ha csak a **response_types** metaadat értéke `code`. Ebben az esetben az Azure AD B2C-vel hívást egy másik az engedélyezési kódot, a hozzáférési jogkivonatot. Ha a metaadat értéke `id_token` kihagyhatja a titkosítási kulcs.  |  

## <a name="redirect-uri"></a>Átirányítási Uri
 
Az átirányítási URI-ját az identitásszolgáltató konfigurálásakor adja meg a `https://login.microsoftonline.com/te/tenant/oauth2/authresp`. Cserélje le **bérlői** a bérlő neve (például: contosob2c.onmicrosoft.com) vagy a bérlő azonosítója. Az átirányítási URI-t kell lennie az összes kisbetűt.

Ha használja a **b2clogin.com** helyett tartományi **login.microsoftonline.com** ügyeljen arra, hogy a b2clogin.com használata helyett login.microsoftonline.com.

Példák:

- [Adja hozzá a Microsoft-fiók (MSA) identitás-szolgáltatóként egyéni szabályzatok használatával](active-directory-b2c-custom-setup-msa-idp.md)
- [Jelentkezzen be az Azure AD-fiókok](active-directory-b2c-setup-aad-custom.md)
- [Engedélyezése a felhasználók számára, hogy jelentkezzen be egy több-bérlős Azure ad-ben identitásszolgáltatótól az egyéni szabályzatok használatával](active-directory-b2c-setup-commonaad-custom.md)

 















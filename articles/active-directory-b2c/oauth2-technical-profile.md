---
title: OAuth2 műszaki profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: OAuth2-technikai profilt definiálhat egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 78ce6466521c7903187798d902056948c659653c
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509852"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OAuth2 műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a OAuth2 protokoll identitás-szolgáltatója számára. A OAuth2 az engedélyezés és a delegált hitelesítés elsődleges protokollja. További információ: [RFC 6749 The OAuth 2,0 Authorization Framework](https://tools.ietf.org/html/rfc6749). A OAuth2-alapú technikai profillal OAuth2-alapú összevonása, például a Facebook használatával is elvégezhető. Az egyesítő lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="protocol"></a>Protokoll

A **protokoll** elem **Name** attribútumát be kell állítani `OAuth2` . A **Facebook-OAUTH** technikai profilhoz tartozó protokoll például a következő `OAuth2` :

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** és a **InputClaimsTransformations** elemek nem szükségesek. Előfordulhat azonban, hogy további paramétereket szeretne küldeni az identitás-szolgáltatónak. A következő példa hozzáadja a **domain_hint** lekérdezési karakterlánc paramétert az `contoso.com` engedélyezési kérelem értékével.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza a OAuth2-identitás szolgáltatója által visszaadott jogcímek listáját. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. Olyan jogcímeket is megadhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja az `DefaultValue` attribútumot.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő példa a Facebook-identitás szolgáltatója által visszaadott jogcímeket mutatja be:

- Az **first_name** jogcím a **givenName** jogcímhez van rendelve.
- A **last_name** jogcím a **vezetéknév** jogcímere van leképezve.
- Név-hozzárendelés nélküli **DisplayName** jogcím.
- A név leképezése nélküli **e-mail-** jogcím.

A technikai profil az Identitáskezelő által nem visszaadott jogcímeket is visszaadja:

- Az **identityProvider** -jogcím, amely tartalmazza az identitás-szolgáltató nevét.
- A **authenticationSource** jogcím alapértelmezett **socialIdpAuthentication**-értékkel rendelkezik.

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
| client_id | Igen | Az identitás-szolgáltató alkalmazás-azonosítója. |
| IdTokenAudience | Nem | A id_token célközönsége. Ha meg van adva, Azure AD B2C ellenőrzi, hogy a jogkivonat az identitás-szolgáltató által visszaadott jogcímben van-e, és megegyezik-e a megadott értékkel. |
| authorization_endpoint | Igen | Az engedélyezési végpont URL-címe RFC 6749-ként. |
| AccessTokenEndpoint | Igen | A jogkivonat-végpont URL-címe RFC 6749-ként. |
| ClaimsEndpoint | Igen | A felhasználói adatok végpontjának URL-címe RFC 6749-ként. |
| end_session_endpoint | Igen | A végponti munkamenet végpontjának URL-címe az RFC 6749 alapján. |
| AccessTokenResponseFormat | Nem | A hozzáférési jogkivonat-végpont hívásának formátuma. Például a Facebook HTTP GET metódust igényel, de a hozzáférési jogkivonat válasza JSON formátumú. |
| AdditionalRequestQueryParameters | Nem | További lekérdezési paraméterek. Előfordulhat például, hogy további paramétereket szeretne küldeni az identitás-szolgáltatónak. Vesszővel elválasztó használatával több paramétert is megadhat. |
| ClaimsEndpointAccessTokenName | Nem | A hozzáférési jogkivonat lekérdezési karakterlánc-paraméterének neve. Egyes identitás-szolgáltatók jogcímei végpontok támogatják a HTTP-kérések beolvasását. Ebben az esetben a tulajdonosi jogkivonatot egy lekérdezési karakterlánc paraméterrel kell elküldeni az engedélyezési fejléc helyett. |
| ClaimsEndpointFormatName | Nem | A Format lekérdezési karakterlánc paraméterének neve. Például megadhatja a nevet `format` ebben a LinkedIn jogcím-végpontban `https://api.linkedin.com/v1/people/~?format=json` . |
| ClaimsEndpointFormat | Nem | A Format lekérdezési karakterlánc paraméter értéke. Például megadhatja az értéket `json` ebben a LinkedIn jogcím-végpontban `https://api.linkedin.com/v1/people/~?format=json` . |
| ProviderName | Nem | Az identitás-szolgáltató neve. |
| response_mode | Nem | Az a metódus, amelyet az Identitáskezelő használ az eredmény Azure AD B2Cba való visszaküldéséhez. Lehetséges értékek: `query` , `form_post` (alapértelmezett) vagy `fragment` . |
| scope | Nem | A kérelem hatóköre, amely a OAuth2-identitás szolgáltatójának specifikációja szerint van meghatározva. Például: `openid` , `profile` , és `email` . |
| HttpBinding | Nem | A hozzáférési jogkivonat és a jogcímek jogkivonat-végpontjának várt HTTP-kötése. Lehetséges értékek: `GET` vagy `POST` .  |
| ResponseErrorCodeParamName | Nem | A HTTP 200 (ok) protokollon keresztül visszaadott hibaüzenetet tartalmazó paraméter neve. |
| ExtraParamsInAccessTokenEndpointResponse | Nem | Azokat a felesleges paramétereket tartalmazza, amelyeket egyes identitás-szolgáltatók a **AccessTokenEndpoint** válaszában adhatnak vissza. A **AccessTokenEndpoint** válasza például egy további paramétert tartalmaz `openid` , amely egy kötelező paraméter, amely a Access_token mellett egy **ClaimsEndpoint** kérelem lekérdezési karakterláncában szerepel. Több paraméter nevét el kell kerülni és el kell különíteni a vessző "," elválasztóval. |
| ExtraParamsInClaimsEndpointRequest | Nem | Azokat a felesleges paramétereket tartalmazza, amelyeket egyes identitás-szolgáltatók a **ClaimsEndpoint** -kérelemben adhatnak vissza. Több paraméter nevét el kell kerülni és el kell különíteni a vessző "," elválasztóval. |
| IncludeClaimResolvingInClaimsHandling  | Nem | A bemeneti és a kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` , vagy `false` (alapértelmezett). Ha a technikai profilban a jogcím-feloldót szeretné használni, állítsa be a következőt: `true` . |
| ResolveJsonPathsInJsonTokens  | Nem | Azt jelzi, hogy a technikai profil feloldja-e a JSON-útvonalakat. Lehetséges értékek: `true` , vagy `false` (alapértelmezett). A metaadatok használatával beolvashatja az adatokat egy beágyazott JSON-elemből. A [OutputClaim](technicalprofiles.md#output-claims)állítsa be a `PartnerClaimType` elemet a kimenetként használni kívánt JSON-útvonal elemre. Például: `firstName.localized` , vagy `data.0.to.0.email` .|
|token_endpoint_auth_method| Nem| Meghatározza, hogy a Azure AD B2C hogyan küldi el a hitelesítési fejlécet a jogkivonat-végpontnak. Lehetséges értékek: `client_secret_post` (alapértelmezett) és `client_secret_basic` (nyilvános előzetes verzió). További információ: [OpenID Connect ügyfél-hitelesítés szakasz](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
|SingleLogoutEnabled| Nem| Azt jelzi, hogy a technikai profilba való bejelentkezés során a rendszer megpróbál-e kijelentkezni az összevont identitás-szolgáltatókról. További információ: Azure AD B2C- [munkamenet](session-behavior.md#sign-out)kijelentkezése.  Lehetséges értékek: `true` (alapértelmezett) vagy `false` .|

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Az Identity Provider alkalmazás ügyfél-titka. A titkosítási kulcs csak akkor szükséges, ha a **response_types** metaadatok értéke `code` . Ebben az esetben Azure AD B2C egy másik hívást kezdeményez a hozzáférési token engedélyezési kódjának cseréjéhez. Ha a metaadatok értéke, akkor `id_token` kihagyhatja a titkosítási kulcsot. |

## <a name="redirect-uri"></a>Átirányítási URI

Ha az Identitáskezelő átirányítási URI-JÁT konfigurálja, írja be a (z `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/oauth2/authresp` ) értéket. Ne felejtse el lecserélni a `{tenant-name}` bérlő nevét (például contosob2c). Az átirányítási URI-nak minden kisbetűsnek kell lennie.

Angol nyelvű Példák:

- [Google + hozzáadása OAuth2-identitás-szolgáltatóként egyéni szabályzatok használatával](identity-provider-google.md)

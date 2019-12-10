---
title: OAuth2 műszaki profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: OAuth2-technikai profilt definiálhat egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33bad4982d54eb18e91be28511fb9137223f4a91
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950969"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OAuth2 műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a OAuth2 protokoll identitás-szolgáltatója számára. A OAuth2 az engedélyezés és a delegált hitelesítés elsődleges protokollja. További információ: [RFC 6749 The OAuth 2,0 Authorization Framework](https://tools.ietf.org/html/rfc6749). A OAuth2-alapú technikai profillal OAuth2-alapú összevonása, például a Facebook használatával is elvégezhető. Az egyesítő lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem **Name** attribútumát `OAuth2`értékre kell állítani. A **Facebook-OAUTH** technikai profilhoz tartozó protokoll például `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** és a **InputClaimsTransformations** elemek nem szükségesek. Előfordulhat azonban, hogy további paramétereket szeretne küldeni az identitás-szolgáltatónak. A következő példa hozzáadja a **domain_hint** lekérdezési karakterlánc paramétert `contoso.com` értékkel az engedélyezési kérelemhez.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza a OAuth2-identitás szolgáltatója által visszaadott jogcímek listáját. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. Olyan jogcímeket is tartalmazhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja a `DefaultValue` attribútumot.

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

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az identitás-szolgáltató alkalmazás-azonosítója. |
| IdTokenAudience | Nem | A id_token célközönsége. Ha meg van adva, Azure AD B2C ellenőrzi, hogy a jogkivonat az identitás-szolgáltató által visszaadott jogcímben van-e, és megegyezik-e a megadott értékkel. |
| authorization_endpoint | Igen | Az engedélyezési végpont URL-címe RFC 6749-ként. |
| AccessTokenEndpoint | Igen | A jogkivonat-végpont URL-címe RFC 6749-ként. |
| ClaimsEndpoint | Igen | A felhasználói adatok végpontjának URL-címe RFC 6749-ként. |
| AccessTokenResponseFormat | Nem | A hozzáférési jogkivonat-végpont hívásának formátuma. Például a Facebook HTTP GET metódust igényel, de a hozzáférési jogkivonat válasza JSON formátumú. |
| AdditionalRequestQueryParameters | Nem | További lekérdezési paraméterek. Előfordulhat például, hogy további paramétereket szeretne küldeni az identitás-szolgáltatónak. Vesszővel elválasztó használatával több paramétert is megadhat. |
| ClaimsEndpointAccessTokenName | Nem | A hozzáférési jogkivonat lekérdezési karakterlánc-paraméterének neve. Egyes identitás-szolgáltatók jogcímei végpontok támogatják a HTTP-kérések beolvasását. Ebben az esetben a tulajdonosi jogkivonatot egy lekérdezési karakterlánc paraméterrel kell elküldeni az engedélyezési fejléc helyett. |
| ClaimsEndpointFormatName | Nem | A Format lekérdezési karakterlánc paraméterének neve. Megadhatja például, hogy a név `format` ebben a LinkedIn jogcím-végpontban `https://api.linkedin.com/v1/people/~?format=json`. |
| ClaimsEndpointFormat | Nem | A Format lekérdezési karakterlánc paraméter értéke. Például beállíthatja az értéket a LinkedIn jogcímek végpontjának `https://api.linkedin.com/v1/people/~?format=json``json`. |
| ProviderName | Nem | Az identitás-szolgáltató neve. |
| response_mode | Nem | Az a metódus, amelyet az Identitáskezelő használ az eredmény Azure AD B2Cba való visszaküldéséhez. Lehetséges értékek: `query`, `form_post` (alapértelmezett) vagy `fragment`. |
| scope | Nem | A kérelem hatóköre, amely a OAuth2-identitás szolgáltatójának specifikációja szerint van meghatározva. Például `openid`, `profile`és `email`. |
| HttpBinding | Nem | A hozzáférési jogkivonat és a jogcímek jogkivonat-végpontjának várt HTTP-kötése. Lehetséges értékek: `GET` vagy `POST`.  |
| ResponseErrorCodeParamName | Nem | A HTTP 200 (ok) protokollon keresztül visszaadott hibaüzenetet tartalmazó paraméter neve. |
| ExtraParamsInAccessTokenEndpointResponse | Nem | Azokat a felesleges paramétereket tartalmazza, amelyeket egyes identitás-szolgáltatók a **AccessTokenEndpoint** válaszában adhatnak vissza. A **AccessTokenEndpoint** válasza például egy további paramétert tartalmaz, például `openid`, amely egy kötelező paraméter, amely a access_token mellett egy **ClaimsEndpoint** kérelem lekérdezési karakterláncában szerepel. Több paraméter nevét el kell kerülni és el kell különíteni a vessző "," elválasztóval. |
| ExtraParamsInClaimsEndpointRequest | Nem | Azokat a felesleges paramétereket tartalmazza, amelyeket egyes identitás-szolgáltatók a **ClaimsEndpoint** -kérelemben adhatnak vissza. Több paraméter nevét el kell kerülni és el kell különíteni a vessző "," elválasztóval. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Az Identity Provider alkalmazás ügyfél-titka. A titkosítási kulcs csak akkor szükséges, ha a **response_types** metaadatok értéke `code`. Ebben az esetben Azure AD B2C egy másik hívást kezdeményez a hozzáférési token engedélyezési kódjának cseréjéhez. Ha a metaadatok értéke `id_token`, akkor kihagyhatja a titkosítási kulcsot. |

## <a name="redirect-uri"></a>Átirányítási URI

Az Identitáskezelő átirányítási URL-címének konfigurálásakor adja meg a `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Ne felejtse el **lecserélni a bérlő nevét** (például contosob2c.onmicrosoft.com) és a **policyId** a szabályzat azonosítójával (például b2c_1a_policy). Az átirányítási URI-nak minden kisbetűsnek kell lennie.

Ha a **b2clogin.com** tartományt használja a **login.microsoftonline.com** helyett, ügyeljen arra, hogy a login.microsoftonline.com helyett a b2clogin.com használja.

Példák:

- [Google + hozzáadása OAuth2-identitás-szolgáltatóként egyéni szabályzatok használatával](active-directory-b2c-custom-setup-goog-idp.md)














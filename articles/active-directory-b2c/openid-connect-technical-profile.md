---
title: OpenID Connect műszaki profil definiálása egyéni szabályzatban Azure Active Directory B2Cban | Microsoft Docs
description: Definiáljon egy OpenID Connect műszaki profilt egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1e8f03b17c5e8ea68affa9fe83875382fd5d8512
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716700"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OpenID Connect műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C támogatást nyújt az [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protokoll identitás-szolgáltatója számára. Az OpenID Connect 1,0 a OAuth 2,0-es számú identitási réteget definiálja, és a modern hitelesítési protokollokban lévő Art állapotot jelöli. Az OpenID Connect technikai profillal összevonása az OpenID Connect-alapú identitás-szolgáltatóval, például az Azure AD-vel. Az egyesítő lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="protocol"></a>Protocol

A **protokoll** elem `OpenIdConnect` **Name** attribútumát be kell állítani. A **MSA-OIDC** technikai profilhoz `OpenIdConnect`tartozó protokoll például a következő:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** és a **InputClaimsTransformations** elemek nem szükségesek. Előfordulhat azonban, hogy további paramétereket szeretne küldeni az identitás-szolgáltatónak. A következő példa hozzáadja a **domain_hint** lekérdezési karakterlánc paramétert az engedélyezési `contoso.com` kérelem értékével.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza az OpenID Connect Identity Provider által visszaadott jogcímek listáját. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. Olyan jogcímeket is megadhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja `DefaultValue` az attribútumot.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő példában a Microsoft-fiók identitás-szolgáltatója által visszaadott jogcímek láthatók:

- Az **issuerUserId** jogcímhez hozzárendelt **aljogcím.**
- A **DisplayName** jogcímhez hozzárendelt **név** jogcím.
- Az **e-mail** név leképezése nélkül.

A technikai profil az Identitáskezelő által nem visszaadott jogcímeket is visszaadja:

- Az **identityProvider** -jogcím, amely tartalmazza az identitás-szolgáltató nevét.
- A **authenticationSource** jogcím alapértelmezett **socialIdpAuthentication**-értékkel rendelkezik.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az identitás-szolgáltató alkalmazás-azonosítója. |
| IdTokenAudience | Nem | A id_token célközönsége. Ha meg van adva, Azure AD B2C ellenőrzi, hogy a jogkivonat az identitás-szolgáltató által visszaadott jogcímben van-e, és megegyezik-e a megadott értékkel. |
| METADATA | Igen | Egy URL-cím, amely egy JSON-konfigurációs dokumentumra mutat, amely az OpenID Connect Discovery specifikációjának megfelelően van formázva, amely a jól ismert OpenID konfigurációs végpontként is ismert. |
| ProviderName | Nem | Az identitás-szolgáltató neve. |
| response_types | Nem | A válasz típusa az OpenID Connect Core 1,0 specifikációnak megfelelően. Lehetséges értékek: `id_token`, `code`, vagy `token`. |
| response_mode | Nem | Az a metódus, amelyet az Identitáskezelő használ az eredmény Azure AD B2Cba való visszaküldéséhez. Lehetséges értékek: `query`, `form_post` (alapértelmezett) vagy `fragment`. |
| scope | Nem | Az OpenID Connect Core 1,0 specifikáció alapján meghatározott kérelem hatóköre. Például: `profile` `email`,,és. `openid` |
| HttpBinding | Nem | A hozzáférési jogkivonat és a jogcímek jogkivonat-végpontjának várt HTTP-kötése. Lehetséges értékek: `GET` vagy `POST`.  |
| ValidTokenIssuerPrefixes | Nem | Olyan kulcs, amely az egyes bérlők számára való bejelentkezéshez használható több-bérlős identitás-szolgáltató, például Azure Active Directory használata esetén. |
| UsePolicyInRedirectUri | Nem | Azt jelzi, hogy az átirányítási URI létrehozásakor szabályzatot kell-e használni. Ha az alkalmazást az identitás-szolgáltatóban konfigurálja, meg kell adnia az átirányítási URI-t. Az átirányítási URI Azure ad B2Cre mutat `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` , (a login.microsoftonline.com változhat a Your-Tenant-Name.b2clogin.com használatával).  Ha megadja `false`, minden használt szabályzathoz hozzá kell adnia egy átirányítási URI-t. Például: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nem | Azt jelzi, hogy egy külső szolgáltatásra irányuló kérést hibaként kell-e megjelölni, ha a http-állapotkód a 5xx tartományban van. A mező alapértelmezett értéke: `false`. |
| DiscoverMetadataByTokenIssuer | Nem | Azt jelzi, hogy a OIDC metaadatait fel kell-e deríteni a JWT jogkivonat kiállítójának használatával. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Az Identity Provider alkalmazás ügyfél-titka. A titkosítási kulcs csak akkor szükséges, ha a **response_types** - `code`metaadatok értéke. Ebben az esetben Azure AD B2C egy másik hívást kezdeményez a hozzáférési token engedélyezési kódjának cseréjéhez. Ha a metaadatok `id_token` értéke, akkor kihagyhatja a titkosítási kulcsot.  |

## <a name="redirect-uri"></a>Átirányítási URI

Ha az Identitáskezelő átirányítási URI-JÁT konfigurálja, írja be `https://login.microsoftonline.com/te/tenant/oauth2/authresp`a (z) értéket. Ne felejtse el **lecserélni** a bérlő nevét (például contosob2c.onmicrosoft.com) vagy a bérlő azonosítóját. Az átirányítási URI-nak minden kisbetűsnek kell lennie.

Ha a **b2clogin.com** tartományt használja a **login.microsoftonline.com** helyett, ügyeljen arra, hogy a login.microsoftonline.com helyett a b2clogin.com használja.

Példák:

- [Microsoft-fiók (MSA) hozzáadása identitás-szolgáltatóként egyéni szabályzatok használatával](active-directory-b2c-custom-setup-msa-idp.md)
- [Bejelentkezés Azure AD-fiókok használatával](active-directory-b2c-setup-aad-custom.md)
- [Lehetővé teszi, hogy a felhasználók egyéni szabályzatok használatával jelentkezzenek be egy több-bérlős Azure AD-identitás-szolgáltatóba](active-directory-b2c-setup-commonaad-custom.md)

 















---
title: OpenID Connect műszaki profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Definiáljon egy OpenID Connect műszaki profilt egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e259a57a9cd6b24362862ffd6cb738157ca912d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80332764"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OpenID Connect műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt az [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protokoll identitás-szolgáltatójának támogatásához. Az OpenID Connect 1,0 a OAuth 2,0-es számú identitási réteget definiálja, és a modern hitelesítési protokollokban lévő Art állapotot jelöli. Az OpenID Connect technikai profillal összevonása az OpenID Connect-alapú identitás-szolgáltatóval, például az Azure AD-vel. Az egyesítő lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem `OpenIdConnect` **Name** attribútumát be kell állítani. A **MSA-OIDC** technikai profilhoz tartozó protokoll például a következő `OpenIdConnect`:

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
| IdTokenAudience | Nem | A id_token célközönsége. Ha meg van adva, Azure AD B2C ellenőrzi `aud` , hogy az identitás-szolgáltató által visszaadott jogkivonatban szereplő jogcím egyenlő-e a IdTokenAudience-metaadatokban megadott értékkel.  |
| METAADATOK | Igen | Egy olyan URL-cím, amely az OpenID Connect Identity Provider konfigurációs dokumentumra mutat, amely az OpenID Well-known Configuration Endpoint néven is ismert. Az URL-cím tartalmazhatja a `{tenant}` kifejezést, amelyet a rendszer a bérlő nevével cserél le.  |
| authorization_endpoint | Nem | Egy olyan URL-cím, amely egy OpenID Connect Identity Provider konfigurációs engedélyezési végpontra mutat. Authorization_endpoint metaadatok értéke elsőbbséget élvez az OpenID jól `authorization_endpoint` ismert konfigurációs végpontjában megadott értékkel. Az URL-cím tartalmazhatja a `{tenant}` kifejezést, amelyet a rendszer a bérlő nevével cserél le. |
| kiállító | Nem | Az OpenID Connect-identitás szolgáltatójának egyedi azonosítója. A kiállítói metaadatok értéke elsőbbséget élvez az OpenID `issuer` jól ismert konfigurációs végpontjában megadott értékkel.  Ha meg van adva, Azure AD B2C ellenőrzi `iss` , hogy az identitás-szolgáltató által visszaadott jogkivonatban szereplő jogcím egyenlő-e a kiállítói metaadatokban megadott értékkel. |
| ProviderName | Nem | Az identitás-szolgáltató neve.  |
| response_types | Nem | A válasz típusa az OpenID Connect Core 1,0 specifikációnak megfelelően. Lehetséges értékek: `id_token`, `code`, vagy `token`. |
| response_mode | Nem | Az a metódus, amelyet az Identitáskezelő használ az eredmény Azure AD B2Cba való visszaküldéséhez. Lehetséges értékek: `query`, `form_post` (alapértelmezett) vagy `fragment`. |
| scope | Nem | Az OpenID Connect Core 1,0 specifikáció alapján meghatározott kérelem hatóköre. Például: `openid`, `profile`, és `email`. |
| HttpBinding | Nem | A hozzáférési jogkivonat és a jogcímek jogkivonat-végpontjának várt HTTP-kötése. Lehetséges értékek: `GET` vagy `POST`.  |
| ValidTokenIssuerPrefixes | Nem | Olyan kulcs, amely az egyes bérlők számára való bejelentkezéshez használható több-bérlős identitás-szolgáltató, például Azure Active Directory használata esetén. |
| UsePolicyInRedirectUri | Nem | Azt jelzi, hogy az átirányítási URI létrehozásakor szabályzatot kell-e használni. Ha az alkalmazást az identitás-szolgáltatóban konfigurálja, meg kell adnia az átirányítási URI-t. Az átirányítási URI a következőre `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`mutat: Azure ad B2C.  Ha megadja `false`, minden használt szabályzathoz hozzá kell adnia egy átirányítási URI-t. Például: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nem | Azt jelzi, hogy egy külső szolgáltatásra irányuló kérést hibaként kell-e megjelölni, ha a http-állapotkód a 5xx tartományban van. A mező alapértelmezett értéke: `false`. |
| DiscoverMetadataByTokenIssuer | Nem | Azt jelzi, hogy a OIDC metaadatait fel kell-e deríteni a JWT jogkivonat kiállítójának használatával. |
| IncludeClaimResolvingInClaimsHandling  | Nem | A bemeneti és a kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true`, vagy `false`  (alapértelmezett). Ha a technikai profilban a jogcím-feloldót szeretné használni, állítsa be a következőt: `true`. |

### <a name="ui-elements"></a>Felhasználói felület elemei
 
A következő beállításokkal megadhatja a hiba esetén megjelenő hibaüzenetet. A metaadatokat az OpenID Connect műszaki profiljában kell konfigurálni. A hibaüzenetek [honosítható](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | Nem | A felhasználónak megjelenítendő üzenet, ha a megadott felhasználónévvel rendelkező fiók nem található a címtárban. |
| UserMessageIfInvalidPassword | Nem | A felhasználónak megjelenítendő üzenet, ha a jelszó helytelen. |
| UserMessageIfOldPasswordUsed| Nem |  A felhasználó számára a régi jelszó használatakor megjelenítendő üzenet.|

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Az Identity Provider alkalmazás ügyfél-titka. A titkosítási kulcs csak akkor szükséges, ha a **response_types** metaadatok értéke `code`. Ebben az esetben Azure AD B2C egy másik hívást kezdeményez a hozzáférési token engedélyezési kódjának cseréjéhez. Ha a metaadatok `id_token` értéke, akkor kihagyhatja a titkosítási kulcsot.  |

## <a name="redirect-uri"></a>Átirányítási URI

Ha az Identitáskezelő átirányítási URI-JÁT konfigurálja, írja be `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`a (z) értéket. Ne felejtse el `{your-tenant-name}` lecserélni a bérlő nevét. Az átirányítási URI-nak minden kisbetűsnek kell lennie.

Példák:

- [Microsoft-fiók (MSA) hozzáadása identitás-szolgáltatóként egyéni szabályzatok használatával](identity-provider-microsoft-account-custom.md)
- [Bejelentkezés Azure AD-fiókok használatával](identity-provider-azure-ad-single-tenant-custom.md)
- [Lehetővé teszi, hogy a felhasználók egyéni szabályzatok használatával jelentkezzenek be egy több-bérlős Azure AD-identitás-szolgáltatóba](identity-provider-azure-ad-multi-tenant-custom.md)

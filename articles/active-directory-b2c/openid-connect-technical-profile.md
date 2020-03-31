---
title: OpenID Connect technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: OpenID Connect technikai profildefiniálása egyéni szabályzatban az Azure Active Directory B2C-ben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332764"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OpenID Connect technikai profil definiálása az Azure Active Directory B2C egyéni házirendjében

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja az [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protokoll identitásszolgáltatóját. Az OpenID Connect 1.0 egy identitásréteget határoz meg az OAuth 2.0 tetején, és a modern hitelesítési protokollok korszerű ségét képviseli. Az OpenID Connect technikai profillal összeegyeztetheti egy OpenID Connect alapú identitásszolgáltatóval, például az Azure AD-vel. Az identitásszolgáltatóval való összeegyezés lehetővé teszi a felhasználók számára, hogy meglévő közösségi vagy vállalati identitásukkal jelentkezzenek be.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `OpenIdConnect`parancsra kell állítani. Az **MSA-OIDC** technikai profil protokollja `OpenIdConnect`például a következő:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

A **OutputClaims** elem az OpenID Connect identitásszolgáltató által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy a házirendben definiált jogcím nevét hozzá kell képeznie az identitásszolgáltatóban megadott névhez. Az identitásszolgáltató által nem visszaadott jogcímeket is megadhat, `DefaultValue` feltéve, hogy beállítja az attribútumot.

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

A következő példa a Microsoft-fiók identitásszolgáltatója által visszaadott jogcímeket mutatja be:

- A **issuerUserId** jogcímhez leképezett **aljogcím.**
- A **displayName** jogcímhez leképezett **névjogcím.**
- Az **e-mail** névleképezés nélkül.

A technikai profil olyan jogcímeket is visszaad, amelyeket az identitásszolgáltató nem ad vissza:

- Az identitásszolgáltató nevét tartalmazó **identityProvider** jogcím.
- A **authenticationSource** jogcím a **socialIdpAuthentication**alapértelmezett értékével.

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
| client_id | Igen | Az identitásszolgáltató alkalmazásazonosítója. |
| IdTokenAudience | Nem | A id_token közönsége. Ha meg van adva, az Azure `aud` AD B2C ellenőrzi, hogy az identitásszolgáltató által visszaadott jogkivonatban lévő jogcím megegyezik-e az IdTokenAudience metaadataiban megadott jogcímvel.  |
| Metaadat | Igen | Egy URL-cím, amely egy OpenID Connect identitásszolgáltató konfigurációs dokumentumra mutat, amely et jól ismert OpenID-végpontnak is neveznek. Az URL-cím `{tenant}` tartalmazhatja a kifejezést, amely et a bérlő neve váltja fel.  |
| authorization_endpoint | Nem | Egy URL-cím, amely egy OpenID Connect identitásszolgáltató konfigurációs engedélyezési végpontjára mutat. A authorization_endpoint metaadatok értéke elsőbbséget `authorization_endpoint` élvez az OpenID jól ismert konfigurációs végpontban megadottértékkel szemben. Az URL-cím `{tenant}` tartalmazhatja a kifejezést, amely et a bérlő neve váltja fel. |
| kiállító | Nem | Az OpenID Connect identitásszolgáltató egyedi azonosítója. A kibocsátó metaadatainak értéke elsőbbséget `issuer` élvez az OpenID jól ismert konfigurációs végpontban megadottakkal szemben.  Ha meg van adva, az Azure `iss` AD B2C ellenőrzi, hogy az identitásszolgáltató által visszaadott jogkivonatban lévő jogcím megegyezik-e a kibocsátó metaadataiban megadott jogcímvel. |
| Szolgáltatóneve | Nem | Az identitásszolgáltató neve.  |
| response_types | Nem | A válasz típusa az OpenID Connect Core 1.0 specifikációszerint. Lehetséges `id_token`értékek: `code`, `token`vagy . |
| response_mode | Nem | Az a módszer, amelyet az identitásszolgáltató az eredmény az Azure AD B2C-nek való visszaküldéséhez használ. Lehetséges `query`értékek: `form_post` , (alapértelmezett) vagy `fragment`. |
| scope | Nem | Az OpenID Connect Core 1.0 specifikációja szerint meghatározott kérelem hatóköre. Mint `openid`például a , `profile`és `email`. |
| HttpKötés | Nem | A várt HTTP-kötés a hozzáférési jogkivonathoz és a jogcímjog végpontjaihoz. Lehetséges `GET` értékek: `POST`vagy .  |
| ValidTokenIssuerPrefixes | Nem | Egy kulcs, amely segítségével jelentkezzen be az egyes bérlők használata esetén egy több-bérlős identitásszolgáltató, például az Azure Active Directory használatával. |
| UsePolicyInRedirecturi | Nem | Azt jelzi, hogy kell-e házirendet használni az átirányítási URI létrehozásához. Amikor konfigurálja az alkalmazást az identitásszolgáltatóban, meg kell adnia az átirányítási URI-t. Az átirányítási URI az Azure AD `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`B2C, .  Ha megadja `false`a , minden egyes használt házirendhez hozzá kell adnia egy átirányítási URI-t. Például: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nem | Azt jelzi, hogy a külső szolgáltatásra irányuló kérést hibaként kell-e megjelölni, ha a Http állapotkód az 5xx tartományban van. A mező alapértelmezett értéke: `false`. |
| DiscoverMetadataByTokenIssuer | Nem | Azt jelzi, hogy az OIDC-metaadatokat a JWT-jogkivonat kibocsátójával kell-e felderíteni. |
| IncludeClaimResolvingInClaimsHandling  | Nem | Bemeneti és kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` `false`  vagy (alapértelmezett). Ha a technikai profilban jogcímfeloldót szeretne `true`használni, állítsa ezt a beállításra. |

### <a name="ui-elements"></a>Felhasználói felület elemei
 
A következő beállításokkal konfigurálhatja a hiba esetén megjelenő hibaüzenetet. A metaadatokat az OpenID Connect technikai profilban kell konfigurálni. A hibaüzenetek [honosíthatók](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| UserMessageifClaimsPrincipalDoesnotExist | Nem | Az üzenet, amelyet meg jelenít a felhasználó nak, ha a megadott felhasználónévvel rendelkező fiók nem található a címtárban. |
| UserMessageIfInvalidPassword | Nem | A jelszó helytelen esetén a felhasználónak megjelenítendő üzenet. |
| UserMessageIfOldPasswordUsed| Nem |  Régi jelszó esetén a felhasználó nak megjelenítendő üzenet.|

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A **Kriptográfiai kulcsok** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Az identitásszolgáltató alkalmazás ügyféltkati titka. A titkosítási kulcs csak **response_types** akkor szükséges, ha `code`a response_types metaadat értéke . Ebben az esetben az Azure AD B2C egy másik hívást kezdeményez egy hozzáférési jogkivonat engedélyezési kódjának cseréjéhez. Ha a metaadatok `id_token` beállításszerint vannak beállítva, kihagyhatja a titkosítási kulcsot.  |

## <a name="redirect-uri"></a>Uri átirányítása

Az identitásszolgáltató átirányítási URI-jának konfigurálásakor írja be a . `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` Győződjön meg `{your-tenant-name}` róla, hogy cserélje ki a bérlő nevét. Az átirányítási URI-nak kisbetűsnek kell lennie.

Példák:

- [Microsoft-fiók (MSA) hozzáadása identitásszolgáltatóként egyéni házirendek használatával](identity-provider-microsoft-account-custom.md)
- [Bejelentkezés Azure AD-fiókkal](identity-provider-azure-ad-single-tenant-custom.md)
- [A felhasználók bejelentkezhetnek egy több-bérlős Azure AD-identitásszolgáltatóba egyéni szabályzatok használatával](identity-provider-azure-ad-multi-tenant-custom.md)

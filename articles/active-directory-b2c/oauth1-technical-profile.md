---
title: OAuth1 technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: OAuth 1.0 technikai profildefiniálása egyéni szabályzatban az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7f734844859d44e66bddbc2ddd999659e52f9668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184077"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OAuth1 technikai profil definiálása az Azure Active Directory B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) támogatja az [OAuth 1.0 protokoll](https://tools.ietf.org/html/rfc5849) identitásszolgáltató. Ez a cikk a műszaki profil sajátosságait ismerteti a szabványosprotokollt támogató jogcímszolgáltatóval való interakcióhoz. Az OAuth1 technikai profillal összeegyo-profillal, például a Twitterrel összeegyeztethető. Az identitásszolgáltatóval való fededés lehetővé teszi a felhasználók számára, hogy meglévő közösségi vagy vállalati identitásukkal jelentkezzenek be.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `OAuth1`parancsra kell állítani. Például a **Twitter-OAUTH1** technikai profiljának `OAuth1`protokollja .

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

Az **InputClaims** és **az InputClaimsTransformations** elemek üresek vagy hiányoznak.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem az OAuth1 identitásszolgáltató által visszaadott jogcímek listáját tartalmazza. Előfordulhat, hogy a házirendben definiált jogcím nevét hozzá kell képeznie az identitásszolgáltatóban megadott névhez. Az identitásszolgáltató által vissza nem adott jogcímeket is megadhat, feltéve, hogy beállítja a **DefaultValue** attribútumot.

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

A következő példa a Twitter identitásszolgáltató által visszaadott jogcímeket mutatja be:

- A **user_id** a **kibocsátóUserId** jogcímhez leképezett jogcímhez leképezett jogcím.
- A **screen_name** jogcím, amely a **displayName** jogcímhez van rendelve.
- Az **e-mail** jogcím névleképezés nélkül.

A technikai profil olyan jogcímeket is visszaad, amelyeket az identitásszolgáltató nem ad vissza:

- Az identitásszolgáltató nevét tartalmazó **identityProvider** jogcím.
- A **authenticationSource** jogcím alapértelmezett `socialIdpAuthentication`értéke .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az identitásszolgáltató alkalmazásazonosítója. |
| Szolgáltatóneve | Nem | Az identitásszolgáltató neve. |
| request_token_endpoint | Igen | A kérelem token végpontjának URL-címe az 5849-es RFC-nek. |
| authorization_endpoint | Igen | Az engedélyezési végpont URL-címe az 5849-es RFC-nek. |
| access_token_endpoint | Igen | A tokenvégpont URL-címe az 5849-es RFC-nek. |
| ClaimsEndpoint | Nem | A felhasználói információs végpont URL-címe. |
| Jogcímválasz formátuma | Nem | A jogcímek válaszformátuma.|

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A **Kriptográfiai kulcsok** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Az identitásszolgáltató alkalmazás ügyféltkati titka.   |

## <a name="redirect-uri"></a>Átirányítási URI

Az identitásszolgáltató átirányítási URL-címének konfigurálásakor írja be a . `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp` Győződjön meg arról, hogy cserélje **le a bérlő** nevét (például contosob2c.onmicrosoft.com) és **policyId** a szabályzat azonosítóját (például b2c_1a_policy). Az átirányítási URI-nak kisbetűsnek kell lennie. Adjon hozzá egy átirányítási URL-címet az identitásszolgáltató bejelentkezését használó összes házirendhez.

Ha a **b2clogin.com** tartományt használja **login.microsoftonline.com** ügyeljen arra, hogy b2clogin.com használjon login.microsoftonline.com helyett.

Példák:

- [A Twitter hozzáadása OAuth1 identitásszolgáltatóként egyéni házirendek használatával](identity-provider-twitter-custom.md)














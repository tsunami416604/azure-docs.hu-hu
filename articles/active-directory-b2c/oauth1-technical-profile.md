---
title: OAuth1 műszaki profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: OAuth 1,0 technikai profilt definiálhat egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a4f26f180e34ee9c7a0222b0d7f6be95c78de1b4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840383"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>OAuth1 műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Azure Active Directory B2C (Azure AD B2C) támogatást nyújt a [OAuth 1,0 protokoll](https://tools.ietf.org/html/rfc5849) identitás-szolgáltatója számára. Ez a cikk a szabványos protokollt támogató jogcím-szolgáltatóval való interakcióra szolgáló technikai profil sajátosságait ismerteti. A OAuth1-alapú technikai profillal OAuth1-alapú összevonása (például Twitter) is használható. Az egyesítő lehetővé teszi, hogy a felhasználók bejelentkezzenek a meglévő közösségi vagy vállalati identitásokkal.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem **Name** attribútumát `OAuth1`értékre kell állítani. A **Twitter-OAUTH1** technikai profilhoz tartozó protokoll például `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Bemeneti jogcímek

A **szabályzattípushoz** és a **InputClaimsTransformations** elemek üresek vagy hiányoznak.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem tartalmazza a OAuth1-identitás szolgáltatója által visszaadott jogcímek listáját. Előfordulhat, hogy le kell képeznie a szabályzatban definiált jogcím nevét az identitás-szolgáltatóban definiált névre. Olyan jogcímeket is tartalmazhat, amelyeket nem ad vissza az identitás-szolgáltató, ha beállítja a **DefaultValue** attribútumot.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

A következő példa a Twitter Identity Provider által visszaadott jogcímeket mutatja be:

- A **issuerUserId** jogcímhez rendelt **user_id** jogcím.
- A **DisplayName** jogcímhez hozzárendelt **screen_name** jogcím.
- A név leképezése nélküli **e-mail-** jogcím.

A technikai profil az Identitáskezelő által nem visszaadott jogcímeket is visszaadja:

- Az **identityProvider** -jogcím, amely tartalmazza az identitás-szolgáltató nevét.
- Az **authenticationSource** jogcím `socialIdpAuthentication`alapértelmezett értéke.

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

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_id | Igen | Az identitás-szolgáltató alkalmazás-azonosítója. |
| ProviderName | Nem | Az identitás-szolgáltató neve. |
| request_token_endpoint | Igen | A kérelem jogkivonat-végpontjának URL-címe RFC 5849-ként. |
| authorization_endpoint | Igen | Az engedélyezési végpont URL-címe RFC 5849-ként. |
| access_token_endpoint | Igen | A jogkivonat-végpont URL-címe RFC 5849-ként. |
| ClaimsEndpoint | Nem | A felhasználói információs végpont URL-címe |
| ClaimsResponseFormat | Nem | A jogcímek válaszának formátuma.|

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem a következő attribútumot tartalmazza:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| client_secret | Igen | Az Identity Provider alkalmazás ügyfél-titka.   |

## <a name="redirect-uri"></a>Átirányítási URI

Az Identitáskezelő átirányítási URL-címének konfigurálásakor adja meg a `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Győződjön meg arról, hogy lecseréli a **bérlőt** a bérlő nevére (például contosob2c.onmicrosoft.com), és **policyId** a szabályzat azonosítójával (például b2c_1a_policy). Az átirányítási URI-nak minden kisbetűsnek kell lennie. Adjon hozzá egy átirányítási URL-címet az összes olyan házirendhez, amely az identitás-szolgáltatói bejelentkezést használja.

Ha a **b2clogin.com** tartományt használja a **login.microsoftonline.com** helyett, ügyeljen arra, hogy a login.microsoftonline.com helyett a b2clogin.com használja.

Példák:

- [Twitter hozzáadása OAuth1-identitás-szolgáltatóként egyéni szabályzatok használatával](identity-provider-twitter-custom.md)














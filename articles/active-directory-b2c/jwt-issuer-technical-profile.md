---
title: Technikai profil definiálása jwt-kibocsátó számára egyéni házirendben
titleSuffix: Azure AD B2C
description: A JSON-webtoken (JWT) kiállító technikai profiljának definiálása az Azure Active Directory B2C egyéni szabályzatában.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c23648d70192607b2a5b977dcdd445931e995154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671788"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Technikai profil definiálása JWT-jogkivonat-kibocsátószámára egy Azure Active Directory B2C egyéni szabályzatban

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) többféle biztonsági jogkivonatot bocsát ki az egyes hitelesítési folyamatok feldolgozása során. A JWT-token kibocsátójának technikai profilja jwt-jogkivonatot bocsát ki, amely visszakerül a függő entitás alkalmazáshoz. Általában ez a technikai profil az utolsó vezénylési lépés a felhasználói út.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `None`parancsra kell állítani. Állítsa a **OutputTokenFormat** elemet a programra. `JWT`

A következő példa a `JwtIssuer`következő technikai profilt mutatja be:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Bemeneti, kimeneti és megmaradó jogcímek

Az **InputClaims**, **OutputClaims**és **PersistClaims** elemek üresek vagy hiányoznak. Az **InutputClaimsTransformations** és **a OutputClaimsTransformations** elemek is hiányoznak.

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Igen | A jogcím, amelyet az OAuth2 engedélyezési kódokon és a frissítési jogkivonatokon belül felhasználói identitásjogcímként kell használni. Alapértelmezés szerint a beállítását `objectId`a értékre kell állítania, kivéve, ha más SubjectNamingInfo jogcímtípust ad meg. |
| SendTokenResponseBodyWithJsonNumbers | Nem | Mindig a `true`beállítás . Olyan örökölt formátum esetén, ahol a numerikus értékek JSON-számok helyett karakterláncként vannak megadva, állítsa a értéket. `false` Ez az attribútum szükséges az ügyfelek, amelyek függőséget egy korábbi megvalósítás, amely visszaadta az ilyen tulajdonságokat, például karakterláncok. |
| token_lifetime_secs | Nem | A jogkivonat élettartama. A védett erőforráshoz való hozzáféréshez használt OAuth 2.0-as tulajdonosi jogkivonat élettartama. Az alapértelmezett érték 3600 másodperc (1 óra). A minimális (bezárólag) 300 másodperc (5 perc). A maximális (bezárólag) 86.400 másodperc (24 óra). |
| id_token_lifetime_secs | Nem | Azonosító token élettartama. Az alapértelmezett érték 3600 másodperc (1 óra). A minimális (bezárólag) 300 másodperc (5 perc). A maximális (bezárólag) másodperc 86.400 (24 óra). |
| refresh_token_lifetime_secs | Nem | A token élettartamának frissítése. Az a maximális időszak, amely előtt egy frissítési jogkivonat egy új hozzáférési jogkivonat beszerzésére használható, ha az alkalmazás megkapta a offline_access hatókört. Az alapértelmezett érték 120 9600 másodperc (14 nap). A minimális (bezárólag) 86 400 másodperc (24 óra). A maximális (bezárólag) 7 776 000 másodperc (90 nap). |
| rolling_refresh_token_lifetime_secs | Nem | A token csúszó ablakának élettartama frissítése. Ezen időszak letelte után a felhasználó kénytelen újrahitelesíteni, függetlenül az alkalmazás által beszerzett legutóbbi frissítési jogkivonat érvényességi idejétől. Ha nem szeretné érvényesíteni a csúszó ablak élettartamát, `true`állítsa a allow_infinite_rolling_refresh_token értékét a értékre. Az alapértelmezett érték 7 776 000 másodperc (90 nap). A minimális (bezárólag) 86 400 másodperc (24 óra). A maximális (bezárólag) 31 536 000 másodperc (365 nap). |
| allow_infinite_rolling_refresh_token | Nem | Ha a `true`beállítás, a frissítési jogkivonat csúszó ablak élettartama soha nem jár le. |
| IssuanceClaimPattern | Nem | A Kibocsátó (iss) jogcímet szabályozza. Az egyik érték:<ul><li>AuthorityAndTenantGuid - Az iss jogcím tartalmazza `login.microsoftonline` `tenant-name.b2clogin.com`a tartománynevét, például vagy , és a bérlői azonosító https:\//login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp – Az iss-jogcím tartalmazza `login.microsoftonline` `tenant-name.b2clogin.com`a tartománynevét, például vagy a bérlőazonosítót és a függő entitás házirendjének nevét. https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> Alapértelmezett érték: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimClaimPattern | Nem | A `acr` jogcím értékét szabályozza.<ul><li>Nincs – az Azure AD B2C nem adja ki az acr-jogcímet</li><li>PolicyId - `acr` a jogcím tartalmazza a házirend nevét</li></ul>Az érték beállításának beállításai a TFP (megbízhatósági keretházirend) és az ACR (hitelesítési környezet hivatkozása). Javasoljuk, hogy ezt az értéket tfp-re állítsa `<Item>` be, hogy beállítsa az értéket, győződjön meg a `Key="AuthenticationContextReferenceClaimPattern"` létezéséről, és az `None`érték . A függő entitás házirendjében adja `<OutputClaims>` hozzá `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`az elemet, adja hozzá ezt az elemet. Győződjön meg arról is, hogy a házirend tartalmazza a jogcím típusát`<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| Nem | A hozzáférési [jogkivonat](authorization-code-flow.md#4-refresh-the-token) POST-kérelem frissítése során végrehajtandó felhasználói `/token` út azonosítója a végpontra. |

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A Kriptográfiai kulcsok elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| issuer_secret | Igen | A JWT-token aláírásához használt X509-tanúsítvány (RSA-kulcskészlet). Ez az `B2C_1A_TokenSigningKeyContainer` a kulcs, amelyet az [Egyéni házirendek első lépései](custom-policy-get-started.md)című részben elkosztotott. |
| issuer_refresh_token_key | Igen | A frissítési jogkivonat titkosításához használt X509-tanúsítvány (RSA-kulcskészlet). A kulcsot `B2C_1A_TokenEncryptionKeyContainer` az [Egyéni házirendekkel való ismerkedés című](custom-policy-get-started.md) részben konfigurálta |















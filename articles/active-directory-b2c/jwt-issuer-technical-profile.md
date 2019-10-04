---
title: JWT-jogkivonat kiállítójának műszaki profiljának meghatározása egyéni szabályzatban Azure Active Directory B2Cban | Microsoft Docs
description: Egy JWT-jogkivonat kiállítójának műszaki profiljának meghatározása egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a5b8f299826c5688eb80eaea11ffc3b2b5176297
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959665"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>JWT jogkivonat-kiállító technikai profiljának meghatározása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) számos különböző típusú biztonsági jogkivonatot bocsát ki, mivel az egyes hitelesítési folyamatokat dolgozza fel. A JWT jogkivonat-kiállító technikai profilja olyan JWT-jogkivonatot bocsát ki, amely vissza lesz visszaadva a függő entitás alkalmazásának. Ez a technikai profil általában a felhasználói út utolsó előkészítési lépése.

## <a name="protocol"></a>Protocol

A **protokoll** elem **Name** attribútumát `None` értékre kell beállítani. A **OutputTokenFormat** elem beállítása a következőre: `JWT`.

Az alábbi példa a `JwtIssuer` technikai profilját mutatja be:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Bemeneti, kimeneti és megőrzési jogcímek

A **szabályzattípushoz**, a **OutputClaims**és a **PersistClaims** elemek üresek vagy hiányoznak. A **InutputClaimsTransformations** és a **OutputClaimsTransformations** elemek is hiányoznak.

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Igen | Az a jogcím, amelyet felhasználói identitási jogcímként kell használni a OAuth2-engedélyezési kódokban és a jogkivonatok frissítésében. Alapértelmezés szerint `objectId` értékre kell állítania, hacsak nem ad meg másik SubjectNamingInfo-jogcím-típust. |
| SendTokenResponseBodyWithJsonNumbers | Nem | Mindig a következőre van beállítva: `true`. Olyan örökölt formátum esetén, ahol a numerikus értékek JSON-számok helyett karakterláncként vannak megadva, `false` értékre kell állítani. Ez az attribútum olyan ügyfelek esetében szükséges, akik egy korábbi implementációtól függenek, amely a tulajdonságokat karakterláncként adja vissza. |
| token_lifetime_secs | Nem | Hozzáférési jogkivonat élettartama. A védett erőforrásokhoz való hozzáféréshez használt OAuth 2,0 tulajdonosi jogkivonat élettartama. Az alapértelmezett érték 3 600 másodperc (1 óra). A minimális (inkluzív) érték 300 másodperc (5 perc). A maximális érték 86 400 másodperc (24 óra). |
| id_token_lifetime_secs | Nem | AZONOSÍTÓ jogkivonat élettartama. Az alapértelmezett érték 3 600 másodperc (1 óra). A minimális (inkluzív) érték 300 másodperc (5 perc). A maximális (inkluzív) érték 86 400 (24 óra). |
| refresh_token_lifetime_secs | Nem | A jogkivonat élettartamának frissítése. Az a maximális időtartam, ameddig egy frissítési jogkivonat felhasználható új hozzáférési jogkivonat beszerzéséhez, ha az alkalmazás megkapta a offline_access hatókört. Az alapértelmezett érték 120, 9600 másodperc (14 nap). A minimális (inkluzív) érték 86 400 másodperc (24 óra). A maximális érték 7 776 000 másodperc (90 nap). |
| rolling_refresh_token_lifetime_secs | Nem | A jogkivonat-toló ablak élettartamának frissítése. Az időszak lejárta után a felhasználónak újra hitelesítenie kell magát, függetlenül attól, hogy milyen érvényességi időszakra van az alkalmazás által beszerzett legutóbbi frissítési jogkivonat. Ha nem szeretné kikényszeríteni a csúszó ablak élettartamát, állítsa a allow_infinite_rolling_refresh_token értékét `true` értékre. Az alapértelmezett érték 7 776 000 másodperc (90 nap). A minimális (inkluzív) érték 86 400 másodperc (24 óra). A maximális érték 31 536 000 másodperc (365 nap). |
| allow_infinite_rolling_refresh_token | Nem | Ha `true` értékre van beállítva, a frissítési jogkivonat csúszó ablakának élettartama soha nem jár le. |
| IssuanceClaimPattern | Igen | A kiállítói (ISS) jogcímet vezérli. Az értékek egyike:<ul><li>AuthorityAndTenantGuid – az ISS-jogcím magában foglalja a tartománynevet, például `login.microsoftonline` vagy `tenant-name.b2clogin.com`, valamint a bérlői azonosító https: \//login. microsoftonline. com/00000000-0000-0000-0000-000000000000/v 2.0/</li><li>AuthorityWithTfp – az ISS-jogcím magában foglalja a tartománynevet, például `login.microsoftonline` vagy `tenant-name.b2clogin.com`, a bérlő azonosítóját és a függő entitás szabályzatának nevét. https: \//login. microsoftonline. com/TFP/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-vagy-Sign-in/v 2.0/</li></ul> |
| AuthenticationContextReferenceClaimPattern | Nem | A `acr` jogcím értékét vezérli.<ul><li>Nincs – Azure AD B2C nem adja ki az ACR-jogcímet</li><li>PolicyId – a `acr` jogcím tartalmazza a szabályzat nevét</li></ul>Az érték beállításának beállításai a következők: TFP (megbízhatósági keretrendszer házirendje) és ACR (hitelesítési környezet referenciája). Javasoljuk, hogy ezt az értéket TFP adja meg az érték beállításához, győződjön meg arról, hogy a `<Item>` a `Key="AuthenticationContextReferenceClaimPattern"` létezik, és az érték `None`. A függő entitás házirendjében vegyen fel `<OutputClaims>` elemet, adja hozzá ezt az elemet `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Győződjön meg arról is, hogy a szabályzat tartalmazza a `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` jogcím típusát |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A CryptographicKeys elem a következő attribútumokat tartalmazza:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| issuer_secret | Igen | A JWT-token aláírásához használt X509-tanúsítvány (RSA-kulcs). Ez az a `B2C_1A_TokenSigningKeyContainer` kulcs, amelyet az [Egyéni szabályzatok első lépéseiben](active-directory-b2c-get-started-custom.md)alakítottunk ki. |
| issuer_refresh_token_key | Igen | A frissítési jogkivonat titkosításához használt X509-tanúsítvány (RSA-kulcs). A `B2C_1A_TokenEncryptionKeyContainer` kulcsot konfigurálta az [Egyéni szabályzatok első lépéseiben](active-directory-b2c-get-started-custom.md) |















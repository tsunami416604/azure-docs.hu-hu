---
title: A technikai profil definiálása a JWT jogkivonat kibocsátója egy egyéni házirendek az Azure Active Directory B2C |} A Microsoft Docs
description: A JWT jogkivonat kibocsátója az Azure Active Directory B2C egy egyéni házirendek definiálása a technikai profil.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 247ebdc8156453062eefe6738c5c281d393a9923
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436054"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>A technikai profil meghatározása az egyéni Azure Active Directory B2C-házirendek a JWT jogkivonat kibocsátója

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C számos különböző típusú biztonsági jogkivonatokat bocsát ki, amint az egyes hitelesítési folyamatok feldolgozza azokat. A JWT jogkivonat kibocsátója műszaki profilt a JWT jogkivonat a függő entitás alkalmazásnak visszaadott bocsát ki. A technikai profil általában az utolsó vezénylési lépés a felhasználói interakciósorozatban szereplő a.

## <a name="protocol"></a>Protokoll

A **neve** attribútuma a **protokoll** elemet hozzá kell beállítani `None`. Állítsa be a **OutputTokenFormat** elem `JWT`.

Az alábbi példa bemutatja a technikai profil `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```
 
## <a name="input-output-and-persist-claims"></a>Bemeneti, kimeneti és a jogcímek megőrzése

A **InputClaims**, **OutputClaims**, és **PersistClaims** elemek a következők üres vagy nincs jelen. A **InutputClaimsTransformations** és **OutputClaimsTransformations** elemek a következők is hiányzik.

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Igen | A jogcímet, amely a felhasználói identitás kell használni az OAuth2 engedélyezési kód jogcím és frissítési jogkivonatok. Alapértelmezés szerint be kell állítani `objectId`, ha nem ad meg egy másik SubjectNamingInfo jogcím típusa. | 
| SendTokenResponseBodyWithJsonNumbers | Nem | Mindig `true`. Örökölt formátumban, ahol numerikus értékeket karakterláncként JSON számok helyett, beállításra `false`. Ez az attribútum szükség van az ügyfelek, amelyek végrehajtása függőség a korábbi implementálhat, amely az ilyen tulajdonságok karakterláncként ad vissza. | 
| token_lifetime_secs | Nem | Hozzáférési jogkivonatok élettartama. Az OAuth 2.0 a védett erőforrás eléréséhez használt tulajdonosi jogkivonatának életartama. Az alapértelmezett érték 3600 másodperc (1 óra). A (inkluzív) minimális érték 300 másodpercig (5 perc). A (inkluzív) maximális érték 86 400 másodperc (24 óra). | 
| id_token_lifetime_secs | Nem | Azonosító jogkivonat élettartama. Az alapértelmezett érték 3600 másodperc (1 óra). A (inkluzív) minimális érték 300 másodpercig (5 perc). (A határokat is beleértve) a maximális érték másodperc 86,400 (24 óra). | 
| refresh_token_lifetime_secs | Nem | Frissítse a jogkivonatok élettartamának. Amely előtt a frissítési jogkivonatok segítségével egy új hozzáférési jogkivonat beszerzése, ha az alkalmazás a offline_access hatókör volt megadva a maximális időtartam. Az alapértelmezett érték 120,9600 másodperc (14 nap). A (inkluzív) minimális érték 86 400 másodperc (24 óra). (A határokat is beleértve) a maximális érték 7,776,000 másodperc (90 nap). | 
| rolling_refresh_token_lifetime_secs | Nem | Frissítési jogkivonat csúszóablak-élettartama. Ez az időtartam elteltével a felhasználónak kötelező hitelesítse magát újra, attól függetlenül, az érvényességi időtartam legutóbbi az alkalmazás által beszerzett jogkivonat frissítésére. Ha nem szeretné kényszeríteni a csúszóablak-élettartama, állítsa a allow_infinite_rolling_refresh_token `true`. Az alapértelmezett érték 7,776,000 másodperc (90 nap). A (inkluzív) minimális érték 86 400 másodperc (24 óra). (A határokat is beleértve) a maximális érték 31,536,000 másodperc (365 napos). | 
| allow_infinite_rolling_refresh_token | Nem | Ha beállítása `true`, a csúszóablakon frissítési jogkivonat élettartama soha nem jár le. |
| IssuanceClaimPattern | Igen | Azt szabályozza, hogy a kibocsátói (iss) jogcím. Az értékek egyikét:<ul><li>AuthorityAndTenantGuid - az iss jogcím tartalmazza a tartomány nevét, például `login.microsoftonline` vagy `tenant-name.b2clogin.com`, és a bérlő azonosítója https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - az iss jogcím tartalmazza a tartomány nevét, például `login.microsoftonline` vagy `tenant-name.b2clogin.com`, a bérlő azonosítóját és a függő entitás házirend neve. https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> | 
| AuthenticationContextReferenceClaimPattern | Nem | Szabályozza a `acr` jogcím értéke.<ul><li>Nincs – Azure AD B2C-vel nem jogcímet ad az acr</li><li>PolicyId – a `acr` jogcím a szabályzat nevét tartalmazza.</li></ul>Az érték a lehetőségeket TFP (bizalmi keretrendszer házirend) és az ACR (hitelesítési környezeti hivatkozás). Ajánlott, ha az érték TFP, az érték beállítása, biztosítása a `<Item>` az a `Key="AuthenticationContextReferenceClaimPattern"` létezik értéke pedig `None`. Adja hozzá a függő entitás házirendjében `<OutputClaims>` cikkhez, ez az elem hozzáadása `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Ügyeljen arra, hogy a házirend tartalmazza a jogcím típusa `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` | 

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A CryptographicKeys elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| issuer_secret | Igen | A X509 (RSA key set). a JWT jogkivonat aláírásához használni kívánt tanúsítványt. Ez a `B2C_1A_TokenSigningKeyContainer` kulcs, konfigurálva a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md). | 
| issuer_refresh_token_key | Igen | A X509 (RSA key set) tanúsítványt használja, a frissítési jogkivonat titkosítása. Konfigurált a `B2C_1A_TokenEncryptionKeyContainer` kulcs az [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) |















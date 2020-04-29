---
title: Egy SAML-kiállító technikai profiljának meghatározása egyéni házirendben
titleSuffix: Azure AD B2C
description: Definiáljon egy Security Assertion Markup Language token (SAML) kiállító technikai profilját egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f7beccde92030d1e01633f4e4044849d7e91d05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229952"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Az SAML-token kiállítójának műszaki profiljának meghatározása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) számos különböző típusú biztonsági jogkivonatot bocsát ki, mivel az egyes hitelesítési folyamatokat dolgozza fel. Az SAML-token kiállítójának műszaki profilja olyan SAML-jogkivonatot bocsát ki, amely vissza lesz visszaküldve a függő entitás alkalmazásának (szolgáltatónak). Ez a technikai profil általában a felhasználói út utolsó előkészítési lépése.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem `None` **Name** attribútumát be kell állítani. Állítsa a **OutputTokenFormat** elemet a `SAML2`következőre:.

A következő példa egy technikai profilt mutat be `Saml2AssertionIssuer`:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Bemeneti, kimeneti és megőrzési jogcímek

A **szabályzattípushoz**, a **OutputClaims**és a **PersistClaims** elemek üresek vagy hiányoznak. A **InutputClaimsTransformations** és a **OutputClaimsTransformations** elemek is hiányoznak.

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| IssuerUri | Nem | Az SAML-válaszban megjelenő kiállító neve. Az értéknek meg kell egyeznie a függő entitás alkalmazásban konfigurált névvel. |

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A CryptographicKeys elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| MetadataSigning | Igen | Az SAML-metaadatok aláírásához használt X509-tanúsítvány (RSA-kulcs). Azure AD B2C ezt a kulcsot használja a metaadatok aláírásához. |
| SamlMessageSigning| Igen| Adja meg az SAML-üzenetek aláírásához használandó X509-tanúsítványt (RSA-kulcs). Azure AD B2C ezt a kulcsot használja a függő entitásnak küldött válasz `<samlp:Response>` aláírására.|

## <a name="session-management"></a>Munkamenet-kezelés

Az Azure AD B2C SAML-munkamenetek konfigurálásához egy függő entitás alkalmazása, az `UseTechnicalProfileForSessionManagement` elem attribútuma, a [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO-munkamenetre való hivatkozás.

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan használhatja az SAML kiállítói technikai profilt:

- [SAML-alkalmazás regisztrálása Azure AD B2C](connect-with-saml-service-providers.md)













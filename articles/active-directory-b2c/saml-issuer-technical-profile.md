---
title: SamL-kibocsátó technikai profiljának definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Technikai profildefiniálása egy biztonsági helyestmondás-azonosító nyelv (SAML) kiállítóhoz az Azure Active Directory B2C egyéni szabályzatában.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967269"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>SamL-jogkivonat-kibocsátó technikai profiljának definiálása az Azure Active Directory B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) többféle biztonsági jogkivonatot bocsát ki az egyes hitelesítési folyamatok feldolgozása során. Az SAML token kibocsátójának technikai profilja saml-jogkivonatot bocsát ki, amely visszakerül a függő entitás alkalmazás (szolgáltató) számára. Általában ez a technikai profil az utolsó vezénylési lépés a felhasználói út.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `None`parancsra kell állítani. Állítsa a **OutputTokenFormat** elemet a programra. `SAML2`

A következő példa a `Saml2AssertionIssuer`következő technikai profilt mutatja be:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
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
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Bemeneti, kimeneti és megmaradó jogcímek

Az **InputClaims**, **OutputClaims**és **PersistClaims** elemek üresek vagy hiányoznak. Az **InutputClaimsTransformations** és **a OutputClaimsTransformations** elemek is hiányoznak.

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| IssuerUri | Nem | Az SAML-válaszban megjelenő kibocsátó neve. Az értéknek meg kell egyeznie a függő entitás alkalmazásban konfigurált névvel. |

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A Kriptográfiai kulcsok elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Metaadat-aláírás | Igen | Az SAML metaadatok aláírásához használt X509-tanúsítvány (RSA-kulcskészlet). Az Azure AD B2C ezt a kulcsot használja a metaadatok aláírásához. |
| SamlMessageAláírása| Igen| Adja meg az SAML-üzenetek aláírásához használni kívánt X509-tanúsítványt (RSA-kulcskészlet). Az Azure AD B2C ezt `<samlp:Response>` a kulcsot használja a válaszküldés aláírásához a függő entitásnak.|

## <a name="session-management"></a>Munkamenet-kezelés

Az Azure AD B2C SAML-munkamenetek konfigurálásához egy függő `UseTechnicalProfileForSessionManagement` entitás alkalmazás között, az elem attribútuma, hivatkozás [a SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO-munkamenet.

## <a name="next-steps"></a>További lépések

Az alábbi cikkben például saml-kibocsátó technikai profiljának használatával kapcsolatos a következő cikk:

- [SAML-alkalmazás regisztrálása az Azure AD B2C-ben](connect-with-saml-service-providers.md)













---
title: Egyéni szabályzat használatával egy hozzáférési jogkivonatot át az alkalmazását az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan adhat át egy hozzáférési jogkivonatot OAuth2.0 identitásszolgáltatókhoz való egyéni házirendet egy jogcímet, az Azure Active Directory B2C alkalmazását.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b291c8e15b771163d7986f78695bc74ef3084162
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150801"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Egyéni szabályzat használatával egy hozzáférési jogkivonatot át alkalmazását az Azure Active Directory B2C-vel

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.

> [!Important]
> A nyilvános előzetes verziójú szolgáltatás átmenetileg nem érhető el.

A [egyéni házirendet](active-directory-b2c-get-started-custom.md) az Azure Active Directory (Azure AD) B2C lehetőséget kínál a felhasználók az alkalmazás regisztráljon vagy jelentkezzen be egy identitásszolgáltatóval. Ha ez történik, az Azure AD B2C-t kap egy [hozzáférési jogkivonat](active-directory-b2c-reference-tokens.md) az identitásszolgáltatótól. Az Azure AD B2C használja ezt a jogkivonatot a felhasználóval kapcsolatos információk lekéréséhez. Hozzáadhat egy jogcímtípust és a kimenő jogcímet a jogkivonat segítségével átadása az alkalmazásokat, amelyek az Azure AD B2C-ben regisztrálja az egyéni házirend. 

Az Azure AD B2C jelenleg csak az támogatja a hozzáférési jogkivonatot az átadott [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) Identitásszolgáltatók, például a Facebook és [Google](active-directory-b2c-custom-setup-goog-idp.md). Minden egyéb identitás-szolgáltatóktól a jogcím vissza üres.

## <a name="prerequisites"></a>Előfeltételek

- Az egyéni házirend az OAuth 2.0 identitásszolgáltató van konfigurálva.

## <a name="add-the-claim-elements"></a>Az igényt elemek hozzáadása 

1. Nyissa meg a *TrustframeworkExtensions.xml* fájlt, és adja hozzá a következő **takar** elem azonosítója, amelyet az `identityProviderAccessToken` , a **ClaimsSchema** elem:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Adja hozzá a **kimeneti jogcím** elem a **TechnicalProfile** elem egyes OAuth 2.0 identitásszolgáltató, adja meg hozzáférési jogkivonatát. Az alábbi példa bemutatja az elem, a Facebook technikai profil hozzá:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Mentse a *TrustframeworkExtensions.xml* fájlt.
4. Nyissa meg a függő entitás házirendfájl például *SignUpOrSignIn.xml*, és adja hozzá a **kimeneti jogcím** elem a **TechnicalProfile**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Mentse a szabályzatot fájlt.

## <a name="test-your-policy"></a>A házirend tesztelése

Ha teszteli az alkalmazások Azure AD B2C-ben, hasznos lehet az Azure AD B2C jogkivonat vissza lehet `https://jwt.ms` lehet majd tekinteni a jogcímek, az.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **identitás-kezelőfelületi keretrendszer**.
5. Egyéni szabályzatok lapon kattintson **szabályzat feltöltése**.
6. Válassza ki **szabályzat felülírása, ha létezik**, és keressen rá, és válassza ki a *TrustframeworkExtensions.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.
8. Ismételje meg az 5 – 7 a függő entitás fájlt például *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Szabályzat futtatása

1. Nyissa meg a módosított szabályzatot. Ha például *B2C_1A_signup_signin*.
2. A **alkalmazás**, válassza ki az alkalmazását, amely korábban regisztrálva. Megtekintheti a tokent az alábbi példában a **válasz URL-cím** megjelennie `https://jwt.ms`.
3. Kattintson a **Futtatás most** parancsra.

    Az alábbi példához hasonló kell megjelennie:

    ![A dekódolt jogkivonat](./media/idp-pass-through-custom/idp-pass-through-custom-token.png)

## <a name="next-steps"></a>További lépések

További tudnivalók a tokeneket a [Azure Active Directory-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md).






---
title: Hozzáférési jogkivonat áthárítása egyéni szabályzaton keresztül az alkalmazásnak
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat át egy hozzáférési jogkivonatot az OAuth 2.0 identitásszolgáltatók jogcímként egy egyéni szabályzaton keresztül az alkalmazás az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5ef8f742914129d868152814d84d2112267c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187790"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Hozzáférési jogkivonat áthárítása egyéni szabályzaton keresztül az alkalmazásnak az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) [egyéni szabályzata](custom-policy-get-started.md) lehetővé teszi az alkalmazás felhasználói számára, hogy regisztráljanak vagy jelentkezzenek be egy identitásszolgáltatónál. Ha ez történik, az Azure AD B2C kap egy [hozzáférési jogkivonatot](tokens-overview.md) az identitásszolgáltatótól. Az Azure AD B2C ezt a jogkivonatot használja a felhasználó adatainak lekéréséhez. Hozzáad egy jogcímtípust és kimeneti jogcímet az egyéni szabályzathoz, hogy a jogkivonatot továbbítsa az Azure AD B2C-ben regisztrált alkalmazásoknak.

Az Azure AD B2C támogatja az [OAuth 2.0](authorization-code-flow.md) és az [OpenID Connect](openid-connect.md) identitásszolgáltatók hozzáférési jogkivonatának átadását. Az összes többi identitásszolgáltató esetében a jogcím üresen kerül visszaadásra.

## <a name="prerequisites"></a>Előfeltételek

* Az egyéni házirend OAuth 2.0 vagy OpenID Connect identitásszolgáltatóval van konfigurálva.

## <a name="add-the-claim-elements"></a>Jogcímelemek hozzáadása

1. Nyissa meg a *TrustframeworkExtensions.xml* fájlt, és adja `identityProviderAccessToken` hozzá a következő **ClaimType** elemet a **ClaimsSchema** elemazonosítójával:

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

2. Adja hozzá a **OutputClaim** elemet a **TechnicalProfile** elemhez minden OAuth 2.0 identitásszolgáltatóhoz, amelyhez hozzáférési jogkivonatot szeretne. A következő példa a Facebook technikai profiljához hozzáadott elemet mutatja be:

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
4. Nyissa meg a függő entitás házirendfájlját, például *a SignUpOrSignIn.xml fájlt,* és adja hozzá a **OutputClaim** elemet a **TechnicalProfile profilhoz:**

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

5. Mentse a házirendfájlt.

## <a name="test-your-policy"></a>A szabályzat tesztelése

Amikor az alkalmazások at Azure AD B2C tesztelése, hasznos lehet, hogy `https://jwt.ms` az Azure AD B2C jogkivonat ot vissza, hogy képes legyen áttekinteni a benne lévő jogcímek.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, kattintson a felső menü **Directory + előfizetésszűrőre,** és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Válassza **az Identitáskezelési keretrendszert**.
5. Az Egyéni házirendek lapon kattintson a **Feltöltési szabályzat gombra.**
6. Válassza **a Házirend felülírása, ha létezik lehetőséget,** majd keresse meg és jelölje ki a *TrustframeworkExtensions.xml* fájlt.
7. Válassza a **Feltöltés** lehetőséget.
8. Ismételje meg az 5–7. *SignUpOrSignIn.xml*

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a módosított házirendet. Például *B2C_1A_signup_signin*.
2. Az **Alkalmazás területen**válassza ki a korábban regisztrált alkalmazást. Az alábbi példában a jogkivonat megtekintéséhez `https://jwt.ms`a **Válasz URL-címének** meg kell jelennie.
3. Válassza **a Futtatás most**lehetőséget.

    A következő példához hasonló példát kell látnia:

    ![Dekódolt token jwt.ms idp_access_token kiemelve](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>További lépések

További információ az [Azure Active Directory B2C tokenhivatkozásában](tokens-overview.md)található jogkivonatokról.

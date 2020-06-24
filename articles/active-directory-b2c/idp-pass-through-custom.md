---
title: Hozzáférési token átadása az alkalmazás egyéni szabályzatán keresztül
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat hozzáférési jogkivonatot a OAuth 2,0-es identitás-szolgáltatók számára jogcímként egy egyéni szabályzattal az alkalmazásához Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7307d047861a4fd6ba2065d231fde8040a383cfb
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85201837"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Hozzáférési token átadása egy egyéni szabályzaton keresztül az alkalmazáshoz Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) [Egyéni szabályzata](custom-policy-get-started.md) lehetővé teszi az alkalmazás felhasználói számára, hogy regisztráljon vagy jelentkezzen be egy identitás-szolgáltatóval. Ha ez történik, Azure AD B2C [hozzáférési jogkivonatot](tokens-overview.md) kap az identitás-szolgáltatótól. Azure AD B2C a token használatával kéri le a felhasználó adatait. A jogcímek és a kimeneti jogcímek egyéni szabályzatba való felvételével a jogkivonatot átadja a Azure AD B2Cban regisztrált alkalmazásoknak.

Azure AD B2C támogatja a [OAuth 2,0](authorization-code-flow.md) és az [OpenID Connect](openid-connect.md) Identity Providers hozzáférési jogkivonatának átadását. Az összes többi Identity Provider esetében a rendszer üresen adja vissza a jogcímet.

## <a name="prerequisites"></a>Előfeltételek

* Az egyéni házirend egy OAuth 2,0 vagy OpenID Connect Identity szolgáltatóval van konfigurálva.

## <a name="add-the-claim-elements"></a>Jogcím-elemek hozzáadása

1. Nyissa meg *TrustframeworkExtensions.xml* fájlt, és adja hozzá a következő **claimType** elemet egy azonosítóval `identityProviderAccessToken` a **ClaimsSchema** elemhez:

    ```xml
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

2. Adja hozzá a **OutputClaim** elemet a **kivonatjogcím** elemhez minden olyan OAuth 2,0-identitáshoz, amelyhez hozzá szeretné adni a hozzáférési jogkivonatot. A következő példa a Facebook technikai profiljához hozzáadott elemet mutatja be:

    ```xml
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
4. Nyissa meg a függő entitás házirend-fájlját, például *SignUpOrSignIn.xml*, és adja hozzá a **OutputClaim** elemet a **kivonatjogcím**:

    ```xml
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

5. Mentse a házirend-fájlt.

## <a name="test-your-policy"></a>A szabályzat tesztelése

Az alkalmazások Azure AD B2C-ben történő tesztelésekor hasznos lehet, hogy a Azure AD B2C token visszaadja a `https://jwt.ms` jogcímeket, hogy áttekintse a benne lévő jogcímeket.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy az Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben kattintson a **könyvtár + előfizetés** szűrőre, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **identitási élmény keretrendszert**.
5. Az egyéni házirendek lapon kattintson a **házirend feltöltése**elemre.
6. Ha létezik, válassza a **házirend felülírása**lehetőséget, majd keresse meg és válassza ki a *TrustframeworkExtensions.xml* fájlt.
7. Válassza a **Feltöltés** lehetőséget.
8. Ismételje meg az 5 – 7. lépést a függő entitás fájljánál, például *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a módosított szabályzatot. Például *B2C_1A_signup_signin*.
2. **Alkalmazás**esetén válassza ki a korábban regisztrált alkalmazást. Az alábbi példában szereplő token megjelenítéséhez a **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
3. Válassza a **Futtatás most**lehetőséget.

    Az alábbi példához hasonlónak kell megjelennie:

    ![Dekódolású token a jwt.ms-ben idp_access_token blokk kiemelve](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>További lépések

További információ a tokenekről: [Azure Active Directory B2C jogkivonat-hivatkozás](tokens-overview.md).

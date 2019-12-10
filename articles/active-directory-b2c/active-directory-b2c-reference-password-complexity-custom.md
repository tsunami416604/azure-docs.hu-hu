---
title: Jelszó bonyolultságának konfigurálása egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: A jelszó-összetettségi követelmények konfigurálása Azure Active Directory B2C egyéni házirendjének használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e8718a04f9d63897b2d2472dd0cdffb196c41435
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949790"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>A jelszó bonyolultságának konfigurálása egyéni szabályzatok használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) esetében beállíthatja a felhasználók által a fiók létrehozásakor biztosított jelszavak összetettségi követelményeit. Alapértelmezés szerint a Azure AD B2C **erős** jelszavakat használ. Ez a cikk bemutatja, hogyan konfigurálhatja a jelszó-bonyolultságot az [Egyéni házirendekben](active-directory-b2c-overview-custom.md). A [felhasználói folyamatokban](active-directory-b2c-reference-password-complexity.md)is konfigurálhatja a jelszó bonyolultságát.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő témakörben ismertetett lépéseket: Ismerkedés az [Egyéni szabályzatokkal Active Directory B2Cban](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Elemek hozzáadása

1. Másolja az alapszintű csomaggal letöltött *SignUpOrSignIn. XML* fájlt, és nevezze el a *SingUpOrSignInPasswordComplexity. XML*néven.
2. Nyissa meg a *SingUpOrSignInPasswordComplexity. XML* fájlt, és módosítsa a **PolicyId** és a **PublicPolicyUri** egy új házirend-névre. Például *B2C_1A_signup_signin_password_complexity*.
3. Adja hozzá a következő **claimType** elemeket `newPassword` és `reenterPassword`azonosítókkal:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. A [predikátumok](predicates.md) `IsLengthRange` vagy `MatchesRegex`metódusi típussal rendelkeznek. A `MatchesRegex` típus egy reguláris kifejezésnek felel meg. A `IsLengthRange` típus minimális és maximális hosszúságú karakterláncot használ. Adjon hozzá egy **predikátum** elemet a **BuildingBlocks** elemhez, ha az nem létezik a következő **predikátum** -elemekkel:

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Az egyes **InputValidation** elemek a definiált **predikátum** -elemek használatával épülnek fel. Ez az elem lehetővé teszi a `and`hoz és `or`hoz hasonló logikai összesítések elvégzését. Adjon hozzá egy **InputValidations** elemet a **BuildingBlocks** elemhez, ha az nem létezik a következő **InputValidation** elemmel:

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. Győződjön meg arról, hogy a **PolicyProfile** technikai profilja a következő elemeket tartalmazza:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Mentse a házirend-fájlt.

## <a name="test-your-policy"></a>A szabályzat tesztelése

Az alkalmazások Azure AD B2C-ben történő tesztelésekor hasznos lehet, ha az Azure AD B2C-jogkivonat visszaadott `https://jwt.ms`, hogy át tudja tekinteni a benne lévő jogcímeket.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **identitási élmény keretrendszert**.
5. Az egyéni házirendek lapon kattintson a **házirend feltöltése**elemre.
6. Ha létezik, válassza a **házirend felülírása**lehetőséget, majd keresse meg és válassza ki a *SingUpOrSignInPasswordComplexity. XML* fájlt.
7. Kattintson a **Feltöltés** gombra.

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a módosított szabályzatot. Például *B2C_1A_signup_signin_password_complexity*.
2. **Alkalmazás**esetén válassza ki a korábban regisztrált alkalmazást. A token megjelenítéséhez a **Válasz URL-címének** `https://jwt.ms`nak kell megjelennie.
3. Kattintson a **Futtatás most** parancsra.
4. Válassza a **regisztráció most**lehetőséget, adjon meg egy e-mail-címet, és adjon meg egy új jelszót. Útmutatást a jelszóra vonatkozó korlátozásokban talál. Fejezze be a felhasználói adatok beírását, majd kattintson a **Létrehozás**gombra. Ekkor meg kell jelennie a visszaadott token tartalmának.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [konfigurálhatja a jelszó módosítását egyéni házirendek használatával Azure Active Directory B2Cban](active-directory-b2c-reference-password-change-custom.md).



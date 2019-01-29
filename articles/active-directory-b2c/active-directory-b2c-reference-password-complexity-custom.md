---
title: Állítsa be az Azure Active Directory B2C-vel egyéni szabályzatok használatával, a jelszó erősségét |} A Microsoft Docs
description: Hogyan konfigurálható az Azure Active Directory B2C egy egyéni házirend használatával, jelszó-összetettségi követelményeknek.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e8e3157bc9dfc97d364effee2ea90cfad85d18ff
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167827"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurálja a jelszó összetettségét, az Azure Active Directory B2C-vel egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C-vel konfigurálhatja úgy a bonyolultsági feltételeknek-fiók létrehozása során a felhasználó által megadott jelszót. Alapértelmezés szerint az Azure AD B2C-t használja **erős** jelszavakat. Ez a cikk bemutatja, hogyan állítsa be a jelszó erősségét [egyéni szabályzatok](active-directory-b2c-overview-custom.md). Állítsa a jelszó erősségét lehetőség arra is [felhasználókövetési adatai](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a [az Active Directory B2C-vel egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Az elemek hozzáadása

1. Másolás a *SignUpOrSignIn.xml* fájlt, hogy letöltötte az alapszintű csomaggal, és adja neki *SingUpOrSignInPasswordComplexity.xml*.
2. Nyissa meg a *SingUpOrSignInPasswordComplexity.xml* fájlt, és módosítsa a **PolicyId** és a **PublicPolicyUri** az új házirend nevét. Ha például *B2C_1A_signup_signin_password_complexity*.
3. Adja hozzá a következő **takar** azonosítói elemmel `newPassword` és `reenterPassword`:

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

4. [Predikátumokat](predicates.md) metódus típusú `IsLengthRange` vagy `MatchesRegex`. A `MatchesRegex` típusának segítségével egyezés reguláris kifejezéssel. A `IsLengthRange` írja be a minimális és maximális karakterlánchossz vesz igénybe. Adjon hozzá egy **predikátumokat** elem a **BuildingBlocks** elem, ha még nem létezik, az alábbi **predikátum** elemek:

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

5. Minden egyes **InputValidation** elem a definiált használatával összeállított **predikátum** elemeket. Ez az elem lehetővé teszi, hogy hajtsa végre a logikai összesítések hasonló `and` és `or`. Adjon hozzá egy **InputValidations** elem a **BuildingBlocks** elem, ha még nem létezik, az alábbi **InputValidation** elem:

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

6. Győződjön meg arról, hogy a **PolicyProfile** technikai profil a következő elemeket tartalmazza:

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

7. Mentse a szabályzatot fájlt.

## <a name="test-your-policy"></a>A házirend tesztelése

Ha teszteli az alkalmazások Azure AD B2C-ben, hasznos lehet az Azure AD B2C jogkivonat vissza lehet `https://jwt.ms` lehet majd tekinteni a jogcímek, az.

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **identitás-kezelőfelületi keretrendszer**.
5. Egyéni szabályzatok lapon kattintson **szabályzat feltöltése**.
6. Válassza ki **szabályzat felülírása, ha létezik**, és keressen rá, és válassza ki a *SingUpOrSignInPasswordComplexity.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.

### <a name="run-the-policy"></a>Szabályzat futtatása

1. Nyissa meg a módosított szabályzatot. Ha például *B2C_1A_signup_signin_password_complexity*.
2. A **alkalmazás**, válassza ki az alkalmazását, amely korábban regisztrálva. A jogkivonatot, hogy a **válasz URL-cím** megjelennie `https://jwt.ms`.
3. Kattintson a **Futtatás most** parancsra.
4. Válassza ki **regisztráció**, írjon be egy e-mail címet, és a egy új jelszót. Útmutató a jelszóra jelennek meg. Befejezés, írja be a felhasználói adatokat, és kattintson a **létrehozás**. A visszaadott jogkivonat tartalma kell megjelennie.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [konfigurálja a jelszó módosítása az Azure Active Directory B2C-vel egyéni szabályzatok használatával](active-directory-b2c-reference-password-change-custom.md).



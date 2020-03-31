---
title: A jelszó összetettségének konfigurálása egyéni házirendekkel
titleSuffix: Azure AD B2C
description: A jelszó összetettségi követelményeinek konfigurálása egyéni szabályzattal az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b16790e288f6569f08ce14e5a7c751bbd8083faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138434"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>A jelszó összetettségének konfigurálása egyéni házirendekkel az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) konfigurálhatja a felhasználó által a fiók létrehozásakor megadott jelszavak összetettségi követelményeit. Alapértelmezés szerint az Azure AD B2C **erős** jelszavakat használ. Ez a cikk bemutatja, hogyan konfigurálhatja a jelszó összetettségét az [egyéni házirendekben.](custom-policy-overview.md) Lehetőség van a jelszó összetettségének konfigurálására is a [felhasználói folyamatokban.](user-flow-password-complexity.md)

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az Egyéni házirendek – Első lépések című [lépéseit.](custom-policy-get-started.md) Rendelkeznie kell egy működő egyéni szabályzat a regisztrációhoz és a helyi fiókkal való bejelentkezéshez.


## <a name="add-the-elements"></a>Az elemek hozzáadása

A jelszó összetettségének konfigurálásához `newPassword` felülbírálja a és `reenterPassword` a [jogcímtípusokat](claimsschema.md) [a predikátum-érvényesítésre](predicates.md#predicatevalidations)való hivatkozással. A PredicateValidations elem predikátumok készletét csoportosítja, hogy felhasználói bevitel-érvényesítést hozzon létre, amely alkalmazható egy jogcímtípusra. Nyissa meg a házirend bővítményfájlját. <em> `SocialAndLocalAccounts/` </em>Például.

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja `newPassword` hozzá `reenterPassword` a és a jogcímeket a **ClaimsSchema** elemhez.

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predikátumok](predicates.md) határozza meg az alapvető érvényesítési érték ének ellenőrzésére a jogcímtípus, és igaz vagy hamis értéket ad vissza. Az ellenőrzés egy megadott metóduselem és a metódusra vonatkozó paraméterek használatával történik. Adja hozzá a következő predikátumokat a **BuildingBlocks** `</ClaimsSchema>` elemhez, közvetlenül az elem bezárása után:

    ```XML
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Adja hozzá a következő predikátum-érvényesítéseket a **BuildingBlocks** elemhez, közvetlenül az `</Predicates>` elem bezárása után:

    ```XML
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. A következő technikai profilok [az Active Directory technikai profiljai,](active-directory-technical-profile.md)amelyek adatokat olvasnak és írnak az Azure Active Directoryba. Felülbírálja ezeket a technikai profilokat a bővítményfájlban. Ezzel `PersistedClaims` letilthatja az erős jelszóházirendet. Keresse meg a **ClaimsProviders** elemet.  Adja hozzá a következő jogcímszolgáltatókat az alábbiak szerint:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Mentse a házirendfájlt.

## <a name="test-your-policy"></a>A szabályzat tesztelése

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Válassza **az Identitáskezelési keretrendszert**.
5. Az Egyéni házirendek lapon kattintson a **Feltöltési szabályzat gombra.**
6. Válassza **a Házirend felülírása lehetőséget, ha létezik,** majd keresse meg és jelölje ki a *TrustFrameworkExtensions.xml* fájlt.
7. Kattintson a **Feltöltés** gombra.

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a regisztrációs vagy bejelentkezési szabályzatot. Például *B2C_1A_signup_signin*.
2. Az **Alkalmazás területen**válassza ki a korábban regisztrált alkalmazást. A token megtekintéséhez a Válasz `https://jwt.ms` **URL-címének** meg kell jelennie.
3. Kattintson a **Futtatás most** parancsra.
4. Válassza **a Regisztráció most**lehetőséget, adjon meg egy e-mail címet, és adjon meg egy új jelszót. Útmutató jelenik meg a jelszó korlátozásokat. Fejezze be a felhasználói adatok megadását, majd kattintson a **Létrehozás gombra.** Látnia kell a visszaadott jogkivonat tartalmát.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogy [miként konfigurálhatja a jelszómódosítást az Azure Active Directory B2C egyéni szabályzatai használatával.](custom-policy-password-change.md)
- További információ a [predikátumok](predicates.md) és [predicatevalidations](predicates.md#predicatevalidations) elemek az IEF referencia.

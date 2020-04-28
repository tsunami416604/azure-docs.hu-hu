---
title: Jelszó bonyolultságának konfigurálása egyéni szabályzatok használatával
titleSuffix: Azure AD B2C
description: A jelszó-összetettségi követelmények konfigurálása Azure Active Directory B2C egyéni házirendjének használatával.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79138434"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>A jelszó bonyolultságának konfigurálása egyéni szabályzatok használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) esetében beállíthatja a felhasználók által a fiók létrehozásakor biztosított jelszavak összetettségi követelményeit. Alapértelmezés szerint a Azure AD B2C **erős** jelszavakat használ. Ez a cikk bemutatja, hogyan konfigurálhatja a jelszó-bonyolultságot az [Egyéni házirendekben](custom-policy-overview.md). A [felhasználói folyamatokban](user-flow-password-complexity.md)is konfigurálhatja a jelszó bonyolultságát.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit. A bejelentkezéshez és a helyi fiókokkal való bejelentkezéshez egyéni szabályzatot kell használnia.


## <a name="add-the-elements"></a>Elemek hozzáadása

A jelszó bonyolultságának konfigurálásához bírálja `newPassword` felül `reenterPassword` a és a [jogcím típusait](claimsschema.md) a [predikátumok érvényességére](predicates.md#predicatevalidations)mutató hivatkozással. A PredicateValidations elem olyan predikátumok halmazát csoportosítja, amelyekkel egy felhasználói bemeneti ellenőrzés alkotható, amely alkalmazható a jogcím típusára. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például <em> `SocialAndLocalAccounts/` </em>:.

1. Keresse meg a [BuildingBlocks](buildingblocks.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Keresse meg a [ClaimsSchema](claimsschema.md) elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá `newPassword` a `reenterPassword` és a jogcímeket a **ClaimsSchema** elemhez.

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. A [predikátumok](predicates.md) alapszintű ellenőrzést határoznak meg a jogcím típusának ellenőrzéséhez, és igaz vagy hamis értéket ad vissza. Az érvényesítés egy megadott metódus-elem és a metódushoz tartozó paraméterek készletének használatával történik. Adja hozzá a következő predikátumokat a **BuildingBlocks** elemhez közvetlenül az `</ClaimsSchema>` elem bezárása után:

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

1. Adja hozzá a következő predikátum-érvényesítéseket a **BuildingBlocks** elemhez közvetlenül az `</Predicates>` elem bezárása után:

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

1. A következő műszaki profilok [Active Directory műszaki profilok](active-directory-technical-profile.md), amelyek az Azure Active Directoryba való adatolvasást és-írást írják le. Bírálja felül ezeket a technikai profilokat a kiterjesztési fájlban. Ezzel `PersistedClaims` a beállítással letilthatja az erős jelszavas házirendet. Keresse meg a **ClaimsProviders** elemet.  Adja hozzá a következő jogcím-szolgáltatókat az alábbiak szerint:

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

1. Mentse a házirend-fájlt.

## <a name="test-your-policy"></a>A szabályzat tesztelése

### <a name="upload-the-files"></a>A fájlok feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **identitási élmény keretrendszert**.
5. Az egyéni házirendek lapon kattintson a **házirend feltöltése**elemre.
6. Ha létezik, válassza a **házirend felülírása**lehetőséget, majd keresse meg és válassza ki a *TrustFrameworkExtensions. XML* fájlt.
7. Kattintson a **Feltöltés** gombra.

### <a name="run-the-policy"></a>A házirend futtatása

1. Nyissa meg a regisztrálási vagy bejelentkezési szabályzatot. Például *B2C_1A_signup_signin*.
2. **Alkalmazás**esetén válassza ki a korábban regisztrált alkalmazást. A token megjelenítéséhez a **Válasz URL-címének** meg kell jelennie `https://jwt.ms`.
3. Kattintson a **Futtatás most** parancsra.
4. Válassza a **regisztráció most**lehetőséget, adjon meg egy e-mail-címet, és adjon meg egy új jelszót. Útmutatást a jelszóra vonatkozó korlátozásokban talál. Fejezze be a felhasználói adatok beírását, majd kattintson a **Létrehozás**gombra. Ekkor meg kell jelennie a visszaadott token tartalmának.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [konfigurálhatja a jelszó módosítását egyéni házirendek használatával Azure Active Directory B2Cban](custom-policy-password-change.md).
- További információ a [predikátumok](predicates.md) és a [PredicateValidations](predicates.md#predicatevalidations) elemeiről a IEF-hivatkozásban.

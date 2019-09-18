---
title: A közösségi fiók jogcímek átalakításának példái a Azure Active Directory B2C identitás-keretrendszer sémája esetében | Microsoft Docs
description: A közösségi fiók jogcím-átalakítási példái a Azure Active Directory B2C identitás-keretrendszer sémájának.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a25c5fed4fd88866f46a9ca254213cae5db893a4
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063590"
---
# <a name="social-accounts-claims-transformations"></a>A közösségi fiókok jogcímei átalakítások

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure ad B2C) esetében a közösségi fiók identitásait egy `userIdentities` **alternativeSecurityIdCollection** -jogcím egyik attribútuma tárolja. A **alternativeSecurityIdCollection** minden eleme megadja a kiállítót (identitás-szolgáltató neve, például Facebook.com) és a `issuerUserId`, amely a kiállító egyedi felhasználói azonosítója.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Ez a cikk példákat mutat be a közösségi fiókra vonatkozó jogcímek átalakítására a Azure AD B2Cban az identitási élmény keretrendszere sémájában. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

A felhasználó alternativeSecurityId tulajdonságának JSON-ábrázolását hozza létre, amely a Azure Active Directory hívásokban használható. További információ: [AlternativeSecurityId sémája](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#alternativesecurityid-type).

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | Karakterlánc | A ClaimType, amely a közösségi identitás szolgáltatója által használt egyedi felhasználói azonosítót határozza meg. |
| InputClaim | identitásszolgáltató | Karakterlánc | Az a ClaimType, amely megadja a közösségi fiók identitás-szolgáltatójának nevét, például facebook.com. |
| outputClaim | alternativeSecurityId | Karakterlánc | A ClaimsTransformation meghívása után létrehozott ClaimType. A közösségi fiók felhasználójának identitásával kapcsolatos információkat tartalmaz. A **kiállító** a `identityProvider` jogcím értéke. A **issuerUserId** a `key` jogcím értéke Base64 formátumban. |

A jogcímek átalakítása `alternativeSecurityId` claimType létrehozásához. Ezt a közösségi identitás-szolgáltató technikai profiljai használják, például `Facebook-OAUTH`:. A következő jogcím-átalakítás fogadja a felhasználó közösségi fiókjának AZONOSÍTÓját és az identitás-szolgáltató nevét. A technikai profil kimenete egy JSON-karakterlánc-formátum, amely az Azure AD-címtár szolgáltatásaiban használható.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="socialIdpUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **kulcs**: 12334
    - **identityProvider**: Facebook.com
- Kimeneti jogcímek:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

`AlternativeSecurityId` Egy`alternativeSecurityIdCollection` jogcím hozzáadására szolgál.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | Karakterlánc | A kimeneti jogcímhez hozzáadni kívánt ClaimType. |
| InputClaim | gyűjtemény | alternativeSecurityIdCollection | A jogcím-átalakítás által használt ClaimTypes, ha elérhetők a házirendben. Ha meg van adni, a jogcím- `item` átalakítás hozzáadja a gyűjtemény végéhez. |
| outputClaim | gyűjtemény | alternativeSecurityIdCollection | A ClaimsTransformation után létrehozott ClaimTypes meghívása megtörtént. Az új gyűjtemény, amely a bemenetből `collection` és `item`a-ból származó elemeket is tartalmazza. |

Az alábbi példa egy új közösségi identitást csatol egy meglévő fiókkal. Új közösségi identitás összekapcsolása:
1. A **HRE-UserReadUsingAlternativeSecurityId** és a **HRE-UserReadUsingObjectId** műszaki profilokban adja meg a felhasználó **alternativeSecurityIds** -jogcímeit.
1. Kérje meg a felhasználót, hogy jelentkezzen be az egyik olyan identitás-szolgáltatóval, amely nincs társítva ehhez a felhasználóhoz.
1. A **CreateAlternativeSecurityId** jogcím-átalakítás használatával hozzon létre egy új **alternativeSecurityId** jogcím-típust a következő névvel`AlternativeSecurityId2`
1. Hívja meg a **AddItemToAlternativeSecurityIdCollection** jogcímek átalakítását, és adja hozzá a **AlternativeSecurityId2** -jogcímet a meglévő **AlternativeSecurityIds** -jogcímhez.
1. **AlternativeSecurityIds** jogcím megőrzése a felhasználói fiók számára

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **elem**: {"kiállító": "Facebook.com", "issuerUserId": "MTIzNDU ="}
    - **gyűjtemény**: [{"kiállító": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Kimeneti jogcímek:
    - **gyűjtemény**: [{"kiállító": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"kiállító": "facebook.com", "issuerUserId": "MTIzNDU ="}]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

A **alternativeSecurityIdCollection** jogcím kiállítóinak listáját adja vissza egy új **StringCollection stb** -jogcímbe.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Az ClaimType (kiállító) listájának beolvasásához használandó alkalmazás. |
| outputClaim | identityProvidersCollection | StringCollection stb | A ClaimsTransformation után létrehozott ClaimTypes meghívása megtörtént. Az alternativeSecurityIdCollection bemeneti jogcímevel társított Identitáskezelő-szolgáltatók listája |

A következő jogcím-átalakítás beolvassa a felhasználói **alternativeSecurityIds** jogcímet, és Kinyeri a fiókhoz társított Identity Provider-nevek listáját. A kimeneti **identityProvidersCollection** használatával jelenítheti meg a felhasználó számára a fiókhoz társított identitás-szolgáltatók listáját. Vagy az identitás-szolgáltató kijelölése lapon szűrheti a kimeneti **identityProvidersCollection** -jogcím alapján az identitás-szolgáltatók listáját. Így a felhasználó a fiókhoz még nem társított új közösségi identitás összekapcsolására is választhatja.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Bemeneti jogcímek:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"kiállító": "facebook.com", "issuerUserId": "MTIzNDU ="}]
- Kimeneti jogcímek:
    - **identityProvidersCollection**: ["Facebook.com", "Google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Eltávolít egy **AlternativeSecurityId** egy **alternativeSecurityIdCollection** -jogcímből.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identitásszolgáltató | Karakterlánc | Az a ClaimType, amely tartalmazza a gyűjteményből eltávolítandó identitás-szolgáltató nevét. |
| InputClaim | gyűjtemény | alternativeSecurityIdCollection | A jogcím-átalakítás által használt ClaimTypes. A jogcím-átalakítás eltávolítja a identityProvider a gyűjteményből. |
| outputClaim | gyűjtemény | alternativeSecurityIdCollection | A ClaimsTransformation után létrehozott ClaimTypes meghívása megtörtént. Az új gyűjteményt, miután a identityProvider eltávolította a gyűjteményből. |

Az alábbi példa egy meglévő fiókkal kapcsolja össze a közösségi identitás egyikét. Közösségi identitás leválasztása:
1. A **HRE-UserReadUsingAlternativeSecurityId** és a **HRE-UserReadUsingObjectId** műszaki profilokban adja meg a felhasználó **alternativeSecurityIds** -jogcímeit.
2. Kérje meg a felhasználót, hogy válassza ki, hogy melyik közösségi fiókot kívánja eltávolítani az ehhez a felhasználóhoz hozzárendelt identitás-szolgáltatók listájából.
3. Hívjon fel egy jogcím-átalakítási technikai profilt, amely meghívja a **RemoveAlternativeSecurityIdByIdentityProvider** jogcímek átalakítását, amely eltávolította a kiválasztott közösségi identitást az identitás-szolgáltató neve alapján.
4. A **alternativeSecurityIds** jogcímet a felhasználói fiók számára is megőrzi.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
    - **identityProvider**: Facebook.com
    - **gyűjtemény**: [{"kiállító": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"kiállító": "facebook.com", "issuerUserId": "MTIzNDU ="}]
- Kimeneti jogcímek:
    - **gyűjtemény**: [{"kiállító": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]

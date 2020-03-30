---
title: Közösségi fiókkövetelések átalakítása példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: A közösségi fiók átalakítási példái az Azure Active Directory B2C Identitáskezelési keretrendszer (IEF) sémájához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb713651aca266ab2546ff26c3cd0175a4cbc289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183754"
---
# <a name="social-accounts-claims-transformations"></a>Közösségi számlák követelések átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) a `userIdentities` közösségi fiók identitások egy **alternatívSecurityIdCollection** jogcímtípus attribútumában tárolódnak. Az **alternatívSecurityIdCollection** minden eleme megadja a kibocsátót (az identitásszolgáltató nevét, például facebook.com) és a `issuerUserId`, amely a kibocsátó egyedi felhasználói azonosítója.

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

Ez a cikk példákat az Azure AD B2C identitáskezelési keretrendszer sémája közösségi fiók jogcímátalakításai használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

JSON-ábrázolást hoz létre a felhasználó alternatívSecurityId tulajdonságáról, amely az Azure Active Directory hívásaiban használható. További információ: [AlternativeSecurityId](https://docs.microsoft.com/graph/api/resources/alternativesecurityid) séma.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | kulcs | sztring | A jogcímtípus, amely a közösségi identitásszolgáltató által használt egyedi felhasználói azonosítót adja meg. |
| InputClaim (Bemeneti jogcím) | identityProvider | sztring | A Jogcímtípus, amely megadja a közösségi fiók identitásszolgáltatójának nevét, például facebook.com. |
| OutputClaim (Kimeneti jogcím) | alternatívbiztonsági azonosító | sztring | A ClaimsTransformation meghívása után létrehozott Jogcímtípus. A közösségi fiók felhasználójának identitásával kapcsolatos információkat tartalmazza. A **kibocsátó** a `identityProvider` követelés értéke. A **issuerUserId** a `key` jogcím értéke base64 formátumban. |

Ezzel a jogcímátalakítással létrehozhat egy `alternativeSecurityId` Jogcímtípust. Ez által használt minden társadalmi identitás szolgáltató technikai `Facebook-OAUTH`profilok, mint például a . A következő jogcímek átalakítása megkapja a felhasználói közösségi fiók azonosítóját és az identitásszolgáltató nevét. A technikai profil kimenete egy JSON-karakterlánc-formátum, amely használható az Azure AD címtárszolgáltatásokban.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti igények:
    - **kulcs**: 12334
    - **identityProvider**: Facebook.com
- Kimeneti jogcímek:
    - **alternativeSecurityId**: { "kibocsátó": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTZMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Hozzáad `AlternativeSecurityId` egy `alternativeSecurityIdCollection` jogcímet.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | item | sztring | A kimeneti jogcímhez hozzáadandó Jogcímtípus. |
| InputClaim (Bemeneti jogcím) | Gyűjtemény | alternativeSecurityIdCollection | A jogcímátalakítás által használt jogcímtípusok, ha a házirendben elérhetők. Ha rendelkezésre áll, `item` a jogcímek átalakítása hozzáadja a gyűjtemény végén. |
| OutputClaim (Kimeneti jogcím) | Gyűjtemény | alternativeSecurityIdCollection | A Jogcímátalakítás meghívása után létrehozott jogcímtípusok. Az új gyűjtemény, amely a `collection` `item`bemeneti és a elemét is tartalmazza. |

A következő példa egy új közösségi identitást kapcsol össze egy meglévő fiókkal. Új társadalmi identitás összekapcsolása:
1. Az **AAD-UserReadUsingAlternativeSecurityId** és **AAD-UserReadUsingObjectId** technikai profilok, kimenet a felhasználó **alternatívSecurityIds** jogcím.
1. Kérje meg a felhasználót, hogy jelentkezzen be az egyik olyan identitásszolgáltatónál, amely nincs ezzel a felhasználóval társítva.
1. A **CreateAlternativeSecurityId** jogcímek átalakításával hozzon létre egy új **alternatívSecurityId** jogcímtípust, amelynek neve`AlternativeSecurityId2`
1. Hívja meg az **AddItemToAlternativeSecurityIdCollection** jogcímek átalakítását az **AlternativeSecurityId2** jogcím hozzáadásához a meglévő **AlternativeSecurityIds** jogcímhez.
1. Az **alternatívbiztonsági azonosítók** felhasználói fiókhoz való jogigényének megőrzése

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

- Bemeneti igények:
    - **tétel**: { "kibocsátó": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **gyűjtemény**: [ { "kibocsátó": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTZZMjcw" } ]
- Kimeneti jogcímek:
    - **gyűjtemény**: [ { "kibocsátó": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTZZZZzcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Az **alternativeSecurityIdCollection** jogcím kibocsátóinak listáját egy új **stringCollection** jogcímbe adja vissza.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Az identitásszolgáltatók (kibocsátó) listájának leéséhez használandó Jogcímtípus. |
| OutputClaim (Kimeneti jogcím) | identityProvidersCollection | karakterláncgyűjtemény | A Jogcímátalakítás meghívása után létrehozott jogcímtípusok. Az alternatívSecurityIdCollection bemeneti jogcímhez társítható identitásszolgáltatók listája |

A következő jogcímek átalakítása beolvassa a felhasználói **alternatívSecurityIds** jogcím, és kinyeri a fiókhoz társított identitásszolgáltató nevek listáját. A kimeneti **identitássalCollection** használatával megjelenítheti a felhasználónak a fiókhoz társított identitásszolgáltatók listáját. Vagy az identitásszolgáltató kiválasztása lapon szűrje az identitásszolgáltatók listáját a kimeneti **identitás-szolgáltatókgyűjtemény** jogcím alapján. Így a felhasználó kiválaszthatja, hogy összekapcsolhassa a fiókhoz még nem társított új közösségi identitást.

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

- Bemeneti igények:
    - **alternativeSecurityIdCollection**: { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
- Kimeneti jogcímek:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>EltávolításAlternativeSecurityIdByIdentityProvider

Eltávolítja az **AlternativeSecurityId azonosítót** egy **alternatívSecurityIdCollection** jogcímből.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | identityProvider | sztring | A gyűjteményből eltávolítandó identitásszolgáltató nevét tartalmazó Jogcímtípus. |
| InputClaim (Bemeneti jogcím) | Gyűjtemény | alternativeSecurityIdCollection | A jogcímátalakítás által használt jogcímtípusok. A jogcímek átalakítása eltávolítja az identityProvider a gyűjteményből. |
| OutputClaim (Kimeneti jogcím) | Gyűjtemény | alternativeSecurityIdCollection | A Jogcímátalakítás meghívása után létrehozott jogcímtípusok. Az új gyűjtemény, miután az identityProvider eltávolította a gyűjteményből. |

A következő példa lekapcsolja az egyik közösségi identitást egy meglévő fiókkal. A társadalmi identitás leválasztása:
1. Az **AAD-UserReadUsingAlternativeSecurityId** és **AAD-UserReadUsingObjectId** technikai profilok, kimenet a felhasználó **alternatívSecurityIds** jogcím.
2. Kérje meg a felhasználót, hogy válassza ki, hogy melyik közösségi fiókot távolítsa el a listáról az ehhez a felhasználóhoz társított identitásszolgáltatókközül.
3. Hívjon meg egy jogcímátalakítási technikai profilt, amely meghívja az **RemoveAlternativeSecurityIdByIdentityProvider** jogcímek átalakítását, amely eltávolította a kiválasztott közösségi identitást az identitásszolgáltató nevének használatával.
4. Az **alternatívbiztonsági azonosítók** felhasználói fiókhoz való jogigényének megőrzése.

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

- Bemeneti igények:
    - **identityProvider**: facebook.com
    - **gyűjtemény**: [ { "kibocsátó": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTZZZZzcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Kimeneti jogcímek:
    - **gyűjtemény**: [ { "kibocsátó": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTZZMjcw" } ]

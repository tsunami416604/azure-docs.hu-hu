---
title: Közösségi fiók jogcím-átalakítási példák az az identitás élmény keretrendszer sémát az Azure Active Directory B2C |} A Microsoft Docs
description: Közösségi fiók jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C a.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9fab5394fadc029b9415370c6bc8c0a3a3642054
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156700"
---
# <a name="social-accounts-claims-transformations"></a>Közösségi fiókok a jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory (Azure AD) B2C-vel, a közösségi fiók identitások tárolódnak a `userIdentities` attribútuma egy **alternativeSecurityIdCollection** jogcím típusa. Minden eleme a **alternativeSecurityIdCollection** adja meg a kibocsátó (identity szolgáltató neve, például Facebook.com weboldalt) és a `issuerUserId`, amely az a kibocsátó egyedi felhasználói azonosító. 

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

Ez a cikk példákat ad az Azure AD B2C-ben a közösségi fiók a jogcímek átalakítása az identitás-kezelőfelületi keretrendszer séma használatával. További információkért lásd: [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Az Azure Active Directory felé irányuló hoz létre a felhasználó alternativeSecurityId tulajdonság használható JSON-ábrázolását. További információkért lásd: [AlternativeSecurityId a séma](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#AlternativeSecurityIdType).

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | kulcs | sztring | A ClaimType, amely meghatározza a közösségi identitásszolgáltató által használt egyedi felhasználói azonosító. |
| Bemeneti jogcím | identitásszolgáltató | sztring | A ClaimType, amely meghatározza a közösségi fiók identitását szolgáltatójának neve, például Facebook.com weboldalt. |
| OutputClaim | alternativeSecurityId | sztring | A takar, amelyek a ClaimsTransformation meghívása után jön létre. Az identitás közösségi fiók felhasználói információkat tartalmaz. A **kibocsátó** értéke a `identityProvider` jogcím. A **issuerUserId** értéke a `key` jogcím base64 formátumban. |

A jogcím-átalakítás létrehozásához használja a `alternativeSecurityId` takar. Használja azt minden közösségi szolgáltató technikai profil, például `Facebook-OAUTH`. A következő jogcímek átalakítását kap a felhasználó közösségi fiók Azonosítóját és az identitásszolgáltató nevét. A technikai profil kimenete az Azure AD directory szolgáltatások használható JSON-karakterlánc formátumú.  

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

- A bemeneti jogcímek:
    - **kulcs**: 12334
    - **identityProvider**: Facebook.com
- Kimeneti jogcímek:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Hozzáad egy `AlternativeSecurityId` , egy `alternativeSecurityIdCollection` jogcím. 

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | Elem | sztring | A kimeneti jogcímek hozzáadandó takar. |
| Bemeneti jogcím | gyűjtemény | alternativeSecurityIdCollection | A ClaimTypes, ha a szabályzatban elérhető a jogcímek átalakításáról által használt. Ha meg van adva, a jogcímek átalakításáról hozzáadja a `item` végén található a gyűjteményben. |
| OutputClaim | gyűjtemény | alternativeSecurityIdCollection | A ClaimTypes, amelyek előállítják a ClaimsTransformation meghívása után. Az új gyűjtemény bemeneti mindkét elemeket tartalmazó `collection` és `item`. |

Az alábbi példa egy új közösségi identitását egy meglévő fiókon az hivatkozásokat tartalmaz. Új közösségi identitás mutató hivatkozást: 
1. Az a **AAD-UserReadUsingAlternativeSecurityId** és **AAD-UserReadUsingObjectId** technikai profilok, a felhasználó kimeneti **alternativeSecurityIds** jogcím.
1. Kérje meg a felhasználót, hogy jelentkezzen be az egyik az Identitásszolgáltatók, amelyek nem tartoznak a felhasználóhoz. 
1. Használatával a **CreateAlternativeSecurityId** jogcím-átalakítás, hozzon létre egy új **alternativeSecurityId** jogcím nevű típusa `AlternativeSecurityId2` 
1. Hívja a **AddItemToAlternativeSecurityIdCollection** jogcím-átalakítás hozzáadása a **AlternativeSecurityId2** jogcímet a meglévő **AlternativeSecurityIds** az igényt. 
1. Továbbra is fennáll a **alternativeSecurityIds** jogcímet a felhasználói fiók

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

- A bemeneti jogcímek:
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]
- Kimeneti jogcímek:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"kiállító": "Facebook.com weboldalt", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

A tanúsítványkibocsátók listájának visszaadása a **alternativeSecurityIdCollection** be egy új jogcím **Publishedresources** jogcím.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Az Identitásszolgáltatók (kibocsátó) listájának beolvasásához használandó takar. |
| OutputClaim | identityProvidersCollection | Publishedresources | A ClaimTypes, amelyek előállítják a ClaimsTransformation meghívása után. Az Identitásszolgáltatók listája a alternativeSecurityIdCollection bemeneti jogcímek társítása |

A következő jogcímek átalakítását olvassa be a felhasználó **alternativeSecurityIds** jogcím és a kivonatot a fiókhoz társított az identity provider nevek listája. Használja a kimeneti **identityProvidersCollection** a felhasználói fiókhoz tartozó Identitásszolgáltatók listájának megjelenítéséhez. Másik lehetőségként az identitásszolgáltató kiválasztása oldal, a kimeneti alapján Identitásszolgáltatók listájának szűrése **identityProvidersCollection** jogcím. Így a felhasználó mutató hivatkozást, amely még nem a fiókjához társított közösségi identitás választhat. 

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

- A bemeneti jogcímek:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"kiállító": "Facebook.com weboldalt", "issuerUserId": "MTIzNDU=" } ]
- Kimeneti jogcímek:
    - **identityProvidersCollection**: ["Facebook.com weboldalt", "google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Eltávolít egy **AlternativeSecurityId** a egy **alternativeSecurityIdCollection** jogcím. 

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | identitásszolgáltató | sztring | A ClaimType, amely tartalmazza az identitásszolgáltató el kell távolítani a gyűjtemény neve. |
| Bemeneti jogcím | gyűjtemény | alternativeSecurityIdCollection | A ClaimTypes használják a jogcímek átalakítását. A jogcímek átalakításáról eltávolítja az identitásszolgáltató a gyűjteményből. |
| OutputClaim | gyűjtemény | alternativeSecurityIdCollection | A ClaimTypes, amelyek előállítják a ClaimsTransformation meghívása után. Az új gyűjtemény, miután az identitásszolgáltató eltávolít a gyűjteményből. |

Az alábbi példa a közösségi identitását egy meglévő fiókon az egyik csatolás megszüntetése. Leválasztja a közösségi identitás: 
1. Az a **AAD-UserReadUsingAlternativeSecurityId** és **AAD-UserReadUsingObjectId** technikai profilok, a felhasználó kimeneti **alternativeSecurityIds** jogcím.
2. Kérje meg a felhasználó számára, hogy melyik közösségi fiók távolítsa el a felhasználóhoz társított lista identitás-szolgáltatóktól származó. 
3. Hívja a jogcímek átalakítása technikai profil, amely meghívja a **RemoveAlternativeSecurityIdByIdentityProvider** jogcím-átalakítás, eltávolítani a kiválasztott közösségi identitást, az identitásszolgáltató neve.
4. Továbbra is fennáll a **alternativeSecurityIds** jogcímet a felhasználói fiókot.

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

- A bemeneti jogcímek:
    - **identityProvider**: Facebook.com weboldalt
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"kiállító": "Facebook.com weboldalt", "issuerUserId": "MTIzNDU=" } ]
- Kimeneti jogcímek:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]

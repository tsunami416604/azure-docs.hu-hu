---
title: Egész szám-jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C |} A Microsoft Docs
description: Egész szám jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C a.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 20c109f188acfe37292aa31dfe057ced8b73199b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694751"
---
# <a name="integer-claims-transformations"></a>Egész szám jogcím-átalakítás

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat ad az egész jogcímek átalakítása az identitás-kezelőfelületi keretrendszer séma használatával az Azure Active Directory (Azure AD) B2C-ben. További információkért lásd: [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

Hosszú adattípus alakít át egy karakterlánc típusú adatokat.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | Bemeneti jogcím | hosszú | A ClaimType alakítandó karakterlánc. |
| OutputClaim | outputClaim | string | A takar, amelyek a ClaimsTransformation meghívása után jön létre. |

Ebben a példában a `numericUserId` hosszú érték típusú jogcímet alakítja át egy `UserId` jogcím-karakterlánc típusú értéket.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- A bemeneti jogcímek:
    - **bemeneti jogcím**: 12334 (hosszú)
- Kimeneti jogcímek: 
    - **kimeneti jogcím**: "12334" (karakterlánc)


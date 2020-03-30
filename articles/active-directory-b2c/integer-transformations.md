---
title: Egész jogcímek átalakítási példái egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Egész jogcímek átalakítási példák az Azure Active Directory B2C Identitáskezelési keretrendszer (IEF) sémája.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ece25e95bbbe9f1a1fa591c29ea9ffda0fefd369
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187678"
---
# <a name="integer-claims-transformations"></a>Egész jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz az Identitáskezelési keretrendszer séma egész jogcímek átalakítása az Azure Active Directory B2C (Azure AD B2C) használatával. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringclaim

Hosszú adattípust karakterlánc-adattípussá alakít át.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | long | A Karakterláncra konvertálandó Jogcímtípus. |
| OutputClaim (Kimeneti jogcím) | outputClaim | sztring | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ebben a példában a `numericUserId` hosszú értékű jogcím `UserId` karakterlánctípusú jogcímre alakul át.

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

- Bemeneti igények:
    - **inputClaim**: 12334 (hosszú)
- Kimeneti jogcímek:
    - **outputClaim**: "12334" (karakterlánc)


---
title: Egész szám típusú jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Egész szám értékű jogcím-átalakítási példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78187678"
---
# <a name="integer-claims-transformations"></a>Egész szám típusú jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának egész számú jogcím-átalakításának használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Hosszú adattípust alakít át karakterlánc adattípusra.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | hosszú | A ClaimType karakterlánccá alakítja át. |
| OutputClaim | outputClaim | sztring | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Ebben a példában a `numericUserId` Long értékkel rendelkező jogcím egy karakterlánc típusú értékű `UserId` jogcímre lett konvertálva.

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

- Bemeneti jogcímek:
    - **inputClaim**: 12334 (hosszú)
- Kimeneti jogcímek:
    - **outputClaim**: "12334" (karakterlánc)


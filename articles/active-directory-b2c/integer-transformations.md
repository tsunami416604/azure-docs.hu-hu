---
title: Egész szám típusú jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Egész szám értékű jogcím-átalakítási példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7295e03f0a0f94b3450b99acc4d10d6ff86c92e7
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948883"
---
# <a name="integer-claims-transformations"></a>Egész szám típusú jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának egész számú jogcím-átalakításának használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Hosszú adattípust alakít át karakterlánc adattípusra.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | hosszú | A ClaimType karakterlánccá alakítja át. |
| outputClaim | outputClaim | sztring | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Ebben a példában a Long értékkel rendelkező `numericUserId` jogcím egy olyan `UserId` jogcímre lett konvertálva, amely egy karakterlánc típusú értékkel rendelkezik.

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


---
title: Egész szám értékű jogcím-átalakítási példák a Azure Active Directory B2C Identity Experience Framework sémájához | Microsoft Docs
description: Egész szám értékű jogcím-átalakítási példák a Azure Active Directory B2C Identity Experience Framework sémájára.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82f7fecfb35c63d586993fed73a83209782a890
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064212"
---
# <a name="integer-claims-transformations"></a>Egész szám típusú jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának egész számú jogcím-átalakításának használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Hosszú adattípust alakít át karakterlánc adattípusra.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | long | A ClaimType karakterlánccá alakítja át. |
| outputClaim | outputClaim | Karakterlánc | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

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


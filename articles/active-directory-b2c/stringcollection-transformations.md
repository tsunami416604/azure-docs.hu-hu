---
title: StringCollection stb jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: A StringCollection stb jogcím-átalakítási példákat tartalmaz a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fbbd7b4bdddf2b58e66cb1203414b5a63eec2f27
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951003"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection stb jogcímek átalakításai

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a karakterlánc-gyűjtési jogcímek átalakítására a Azure Active Directory B2C (Azure AD B2C) identitási élmény keretrendszere-sémájának használatával. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Karakterlánc-jogcímet hoz létre egy új StringCollection stb jogcímhez.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | sztring | A kimeneti jogcímhez hozzáadni kívánt ClaimType. |
| InputClaim | gyűjtemény | StringCollection stb | Választható Ha meg van adva, a jogcím-átalakítás átmásolja az elemeket ebből a gyűjteményből, és hozzáadja az elemet a kimeneti gyűjteményi jogcím végéhez. |
| outputClaim | gyűjtemény | StringCollection stb | A ClaimsTransformation után létrehozott ClaimTypes meghívása megtörtént. |

A jogcím-átalakítás használatával hozzáadhat egy karakterláncot egy új vagy egy meglévő StringCollection stb. Ez általában egy **HRE-UserWriteUsingAlternativeSecurityId** technikai profilban használatos. Új közösségi fiók létrehozása előtt a **CreateOtherMailsFromEmail** jogcím-átalakítás beolvassa a claimType, és hozzáadja az értéket a **otherMails** -claimType.

A következő jogcím-átalakítás hozzáadja az **e-mail-** claimType az **otherMails** claimType.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com"]
  - **elem**: "admin@contoso.com"
- Kimeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Egy karakterlánc-paramétert szúr be egy új StringCollection stb jogcímbe.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | gyűjtemény | StringCollection stb | Választható Ha meg van adva, a jogcím-átalakítás átmásolja az elemeket ebből a gyűjteményből, és hozzáadja az elemet a kimeneti gyűjteményi jogcím végéhez. |
| InputParameter | item | sztring | A kimeneti jogcímhez hozzáadandó érték. |
| outputClaim | gyűjtemény | StringCollection stb | A ClaimsTransformation meghívása után előállított ClaimTypes. |

Ezzel a jogcím-átalakítással adhat hozzá új vagy meglévő StringCollection stb karakterlánc-értéket. A következő példa egy állandó e-mail-címet (admin@contoso.com) hoz létre a **otherMails** jogcímhez.

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com"]
- Bemeneti paraméterek
  - **elem**: "admin@contoso.com"
- Kimeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

A megadott karakterlánc-gyűjtemény első elemének beolvasása.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | gyűjtemény | StringCollection stb | A jogcím-átalakítás által az elemek beolvasásához használt ClaimTypes. |
| outputClaim | extractedItem | sztring | A ClaimsTransformation után létrehozott ClaimTypes meghívása megtörtént. A gyűjtemény első eleme. |

A következő példa beolvassa a **otherMails** jogcímet, és az első tételt visszaküldi az **e-mail-** jogcímbe.

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti jogcímek:
  - **gyűjtemény**: ["someone@outlook.com", "someone@contoso.com"]
- Kimeneti jogcímek:
  - **extractedItem**: "someone@outlook.com"


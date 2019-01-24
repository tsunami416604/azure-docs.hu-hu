---
title: Publishedresources-jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C |} A Microsoft Docs
description: Publishedresources jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C a.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7bec6846a1bb22893beed8086b6d9e88babc1906
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847944"
---
# <a name="stringcollection-claims-transformations"></a>Publishedresources jogcím-átalakítás

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat ad az Azure Active Directory (Azure AD) B2C-ben a karakterlánc gyűjtemény a jogcímek átalakítása az identitás-kezelőfelületi keretrendszer séma használatával. További információkért lásd: [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Egy karakterlánc szabálykészlethez Publishedresources új jogcímet ad. 

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | Elem | sztring | A kimeneti jogcímek hozzáadandó takar. |
| Bemeneti jogcím | gyűjtemény | Publishedresources | [Opcionális] Ha meg van adva, a jogcímek átalakításáról másolja át az elemeket a gyűjteményhez, és hozzáadja az elem a kimeneti gyűjtemény jogcímek végéhez. |
| OutputClaim | gyűjtemény | Publishedresources | A ClaimTypes, amelyek előállítják a ClaimsTransformation meghívása után. |

Használja a jogcím-átalakítás karakterláncnak hozzá egy új vagy meglévő Publishedresources. A gyakran használják a **AAD-UserWriteUsingAlternativeSecurityId** technikai profil. Egy új közösségi fiók jön létre, mielőtt **CreateOtherMailsFromEmail** jogcímek átalakítását a ClaimType olvassa be, és hozzáadja az értéket a **otherMails** takar. 

A következő jogcímek átalakítását hozzáadja a **e-mail** takar a **otherMails** takar.

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

- A bemeneti jogcímek:
    - **gyűjtemény**: ["someone@outlook.com"]
    - **elem**: "admin@contoso.com"
- Kimeneti jogcímek: 
    - **gyűjtemény**: ["someone@outlook.com","admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Új Publishedresources jogcímet ad hozzá egy karakterlánc-paramétert. 

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | gyűjtemény | Publishedresources | [Opcionális] Ha meg van adva, a jogcímek átalakításáról másolja át az elemeket a gyűjteményhez, és hozzáadja az elem a kimeneti gyűjtemény jogcímek végéhez. |
| InputParameter | Elem | sztring | A kimeneti jogcímek hozzáadandó érték. |
| OutputClaim | gyűjtemény | Publishedresources | A ClaimTypes ez ClaimsTransformation meghívása után előállított. |

Használja a jogcím-átalakítás karakterlánc-érték hozzáadása egy új vagy meglévő Publishedresources. Az alábbi példa hozzáad egy állandó e-mail-címet (admin@contoso.com), a **otherMails** jogcím. 

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

- A bemeneti jogcímek:
    - **gyűjtemény**: ["someone@outlook.com"]
- Bemeneti paraméterek 
    - **elem**: "admin@contoso.com"
- Kimeneti jogcímek:
    - **gyűjtemény**: ["someone@outlook.com","admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

A megadott karakterlánc gyűjteményt az első elem beolvasása. 

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| Bemeneti jogcím | gyűjtemény | Publishedresources | A ClaimTypes lekérni az elemet használják a jogcímek átalakítását. |
| OutputClaim | extractedItem | sztring | A ClaimTypes, amelyek előállítják a ClaimsTransformation meghívása után. Az első elem a gyűjteményben. |

Az alábbi példa beolvassa a **otherMails** jogcím és az első elemét adja vissza a **e-mail** jogcím. 

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

- A bemeneti jogcímek:
    - **gyűjtemény**: ["someone@outlook.com","someone@contoso.com"]
- Kimeneti jogcímek: 
    - **extractedItem**: "someone@outlook.com"


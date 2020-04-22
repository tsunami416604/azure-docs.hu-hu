---
title: StringCollection jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: StringCollection jogcímek átalakítási példák az Azure Active Directory B2C Identity Experience Framework (IEF) sémája.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cac7e6feb632456b63b97ead057f9ecaf49322ea
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729718"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection jogcímátalakítások

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz az Azure Active Directory B2C (Azure AD B2C) identitáskezelési keretrendszer sémájának karakterlánc-gyűjteményjog-átalakításai használatára. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AdditemToStringgyűjtemény

Karakterláncjogcímet ad hozzá egy új egyedi értékekhez, karakterlánc-gyűjteményjogcím.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | item | sztring | A kimeneti jogcímhez hozzáadandó Jogcímtípus. |
| InputClaim (Bemeneti jogcím) | Gyűjtemény | karakterláncgyűjtemény | [Nem kötelező] Ha meg van adva, a jogcímek átalakítása átmásolja az elemeket ebből a gyűjteményből, és hozzáadja az elemet a kimeneti gyűjtemény jogcímének végéhez. |
| OutputClaim (Kimeneti jogcím) | Gyűjtemény | karakterláncgyűjtemény | A jogcímátalakítás meghívása után létrehozott Jogcímtípus a bemeneti jogcímben megadott értékkel. |

Ezzel a jogcím-átalakítással karakterláncot adhat hozzá egy új vagy meglévő karakterláncgyűjteményhez. Ez gyakran használják a **AAD-UserWriteUsingAlternativeSecurityId** technikai profil. Új közösségi fiók létrehozása előtt a **CreateOtherMailsFromEmail** jogcímek átalakítása beolvassa a ClaimType típust, és hozzáadja az értéket a **többiMails** ClaimType-hoz.

A következő jogcímek átalakítása hozzáadja a ClaimType **e-mailt** **a többiMails** ClaimType-hoz.

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

- Bemeneti igények:
  - **gyűjtemény**:someone@outlook.com[" "]
  - **tétel**:admin@contoso.com" "
- Kimeneti jogcímek:
  - **gyűjtemény**:someone@outlook.com["admin@contoso.com", " "]

## <a name="addparametertostringcollection"></a>AddParameterToStringGyűjtemény

Karakterlánc-paraméter hozzáadása egy új egyedi értékekhez CollectionCollection jogcím.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | Gyűjtemény | karakterláncgyűjtemény | [Nem kötelező] Ha meg van adva, a jogcímek átalakítása átmásolja az elemeket ebből a gyűjteményből, és hozzáadja az elemet a kimeneti gyűjtemény jogcímének végéhez. |
| InputParameter (Beviteli paraméter) | item | sztring | A kimeneti jogcímhez hozzáadandó érték. |
| OutputClaim (Kimeneti jogcím) | Gyűjtemény | karakterláncgyűjtemény | A jogcímátalakítás meghívása után létrehozott Jogcímtípus a bemeneti paraméterben megadott értékkel. |

Ezzel a jogcím-átalakítással karakterlánc-értéket adhat hozzá egy új vagy meglévő karakterláncgyűjteményhez. A következő példa egy állandóadmin@contoso.come-mail címet ( ) ad hozzá a **többiMails** jogcímhez.

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

- Bemeneti igények:
  - **gyűjtemény**:someone@outlook.com[" "]
- Bemeneti paraméterek
  - **tétel**:admin@contoso.com" "
- Kimeneti jogcímek:
  - **gyűjtemény**:someone@outlook.com["admin@contoso.com", " "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Leveszi az első elemet a megadott karakterlánc-gyűjteményből.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | Gyűjtemény | karakterláncgyűjtemény | A jogcímátalakítás által az elem leéséhez használt jogcímtípusok. |
| OutputClaim (Kimeneti jogcím) | kibontásElem | sztring | A Jogcímátalakítás meghívása után létrehozott jogcímtípusok. A gyűjtemény első eleme. |

A következő példa beolvassa a **többiMails követelést,** és visszaküldi az első elemet az **e-mail** jogcímbe.

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

- Bemeneti igények:
  - **gyűjtemény**:someone@outlook.com["someone@contoso.com", " "]
- Kimeneti jogcímek:
  - **kivonattétel:**"someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

Ellenőrzi, hogy a StringCollection jogcímtípus tartalmaz-e elemet

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | karakterláncgyűjtemény | A keresett jogcímtípus. |
|InputParameter (Beviteli paraméter)|item|sztring|A keresni való érték.|
|InputParameter (Beviteli paraméter)|ignoreCase|sztring|Itt adható meg, hogy ez az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterláncok kis- és nagybetűs étkeztetését.|
| OutputClaim (Kimeneti jogcím) | outputClaim | logikai | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. Logikai jelző, ha a gyűjtemény ilyen karakterláncot tartalmaz |

A következő példa `roles` ellenőrzi, hogy a stringCollection jogcímtípus tartalmazza-e a **rendszergazda**értékét.

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Bemeneti igények:
    - **inputClaim**: ["olvasó", "szerző", "admin"]
- Bemeneti paraméterek:
    - **elem**: "Admin"
    - **ignoreCase**: "igaz"
- Kimeneti jogcímek:
    - **outputClaim**: "igaz"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Ellenőrzi, hogy a StringCollection jogcímtípus tartalmaz-e jogcímértéket.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | Gyűjtemény | karakterláncgyűjtemény | A keresett jogcímtípus. |
| InputClaim (Bemeneti jogcím) | item|sztring| A keresni kívánt értéket tartalmazó jogcímtípus.|
|InputParameter (Beviteli paraméter)|ignoreCase|sztring|Itt adható meg, hogy ez az összehasonlítás figyelmen kívül hagyja-e az összehasonlított karakterláncok kis- és nagybetűs étkeztetését.|
| OutputClaim (Kimeneti jogcím) | outputClaim | logikai | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. Logikai jelző, ha a gyűjtemény ilyen karakterláncot tartalmaz |

A következő példa `roles` ellenőrzi, hogy a stringCollection jogcímtípus tartalmazza-e a `role` jogcímtípus értékét.

```XML
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Bemeneti igények:
    - **gyűjtemény**: ["olvasó", "szerző", "admin"]
    - **elem**: "Admin"
- Bemeneti paraméterek:
    - **ignoreCase**: "igaz"
- Kimeneti jogcímek:
    - **outputClaim**: "igaz"

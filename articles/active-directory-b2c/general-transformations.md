---
title: Általános jogcímek átalakítása példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Általános jogcímek átalakítási példák az Azure Active Directory B2C Identitáskezelési keretrendszer (IEF) sémája.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: afdf2f531ede30d868123d89cac94fcfae070384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188545"
---
# <a name="general-claims-transformations"></a>Általános jogcímek átalakítása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat az Azure Active Directory B2C (Azure AD B2C) identitáskezelési keretrendszer sémája általános jogcímátalakításaihasználatára szolgál. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>CopyClaim (Másolatigénylés)

Jogcím értékének másolása egy másiknak. Mindkét jogcímnek azonos típusúnak kell lennie.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim | húr, int | A másolandó jogcímtípus. |
| OutputClaim (Kimeneti jogcím) | outputClaim | húr, int | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ezzel a jogcímátalakítással egy karakterlánc vagy numerikus jogcím egy másik jogcímbe másolhat egy értéket. A következő példa az e-mail jogcímhez másolja a externalEmail jogcímértékét.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti igények:
    - **inputClaim**:bob@contoso.com
- Kimeneti jogcímek:
    - **outputClaim**:bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

Ellenőrzi, hogy a **inputClaim** létezik-e vagy sem, és ennek megfelelően beállítja **a outputClaim értéket** igaz vagy hamis értékre.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | inputClaim |Bármelyik | Az input-követelést, amelynek létezését ellenőrizni kell. |
| OutputClaim (Kimeneti jogcím) | outputClaim | logikai | A Jogcímátalakítás meghívása után létrehozott Jogcímtípus. |

Ezzel a jogcímátalakítással ellenőrizheti, hogy létezik-e jogcím, vagy tartalmaz-e értéket. A visszatérési érték egy logikai érték, amely azt jelzi, hogy a jogcím létezik-e. A következő példa ellenőrzi, hogy az e-mail cím létezik-e.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti igények:
  - **inputClaim**:someone@contoso.com
- Kimeneti jogcímek:
  - **outputClaim**: igaz

## <a name="hash"></a>Kivonat

Hash a megadott egyszerű szöveg segítségével a sót, és egy titkot. A használt kivonatoló algoritmus az SHA-256.

| Elem | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (Bemeneti jogcím) | Sima | sztring | A titkosítandó bemeneti állítás |
| InputClaim (Bemeneti jogcím) | Só | sztring | A só paraméter. Létrehozhat egy véletlenszerű értéket `CreateRandomString` a jogcímek átalakítása használatával. |
| InputParameter (Beviteli paraméter) | randomizerTitkos | sztring | Egy meglévő Azure AD **B2C-házirendkulcsra**mutat. Új házirendkulcs létrehozása: Az Azure AD B2C-bérlőben a **Kezelés csoportban**válassza **az Identitáskezelési keretrendszer lehetőséget.** Válassza a **házirend kulcsok** a bérlőben elérhető kulcsok megtekintéséhez. Válassza a **Hozzáadás** lehetőséget. A **Beállítások területen**válassza a **Kézi**lehetőséget. Adjon meg egy nevet *(az előtag B2C_1A_* lehet, hogy automatikusan hozzáadódik.). A **Titkos** lista mezőbe írja be a használni kívánt titkos kulcsot, például a 1234567890 értéket. A **kulcshasználathoz**válassza az **Aláírás**lehetőséget. Kattintson a **Létrehozás** gombra. |
| OutputClaim (Kimeneti jogcím) | hash | sztring | A jogcímátalakítás meghívása után létrehozott Jogcímtípus. A jogcím `plaintext` konfigurálva a inputClaim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Példa

- Bemeneti igények:
  - **egyszerű szöveg:**MyPass@word1
  - **só**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Kimeneti jogcímek:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=

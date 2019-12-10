---
title: Általános jogcím-átalakítási példák egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Általános jogcím-átalakítási példák a Azure Active Directory B2C Identity Experience Framework (IEF) sémájához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 639277177bf63e659e5b0ea804eca5e20f956831
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948849"
---
# <a name="general-claims-transformations"></a>Általános jogcím-átalakítások

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk példákat tartalmaz a Azure Active Directory B2C (Azure AD B2C) Identity Experience Framework sémájának általános jogcímek átalakítására. További információ: [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Ellenőrzi, hogy a **inputClaim** létezik-e vagy sem, és a **outputClaim** igaz vagy hamis értékre állítja.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Bármelyik | Az a bemeneti jogcím, amelynek létezését ellenőrizni kell. |
| outputClaim | outputClaim | logikai | A ClaimsTransformation után létrehozott ClaimType meghívása megtörtént. |

Ezzel a jogcím-átalakítással ellenőrizhető, hogy létezik-e jogcím, vagy tartalmaz-e értéket. A visszatérési érték egy logikai érték, amely jelzi, hogy a jogcím létezik-e. A következő példa ellenőrzi, hogy létezik-e e-mail-cím.

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

- Bemeneti jogcímek:
  - **inputClaim**: someone@contoso.com
- Kimeneti jogcímek:
  - **outputClaim**: true

## <a name="hash"></a>Kivonat

A megadott egyszerű szöveg kivonatolása a só és a titok használatával. A használt kivonatoló algoritmus az SHA-256.

| Tétel | TransformationClaimType | Adattípus | Megjegyzések |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | egyszerű szöveges | sztring | A titkosítani kívánt bemeneti jogcím |
| InputClaim | só | sztring | A Salt paraméter. A `CreateRandomString` jogcím-átalakítás használatával véletlenszerű értéket hozhat létre. |
| InputParameter | randomizerSecret | sztring | Egy meglévő Azure AD B2C házirend- **kulcsra**mutat. Új házirend-kulcs létrehozásához: a Azure AD B2C-bérlőben válassza a **kezelés**alatt az **identitási élmény keretrendszert**. Válassza ki a **házirend-kulcsok** elemet a bérlőben elérhető kulcsok megtekintéséhez. Válassza a **Hozzáadás** lehetőséget. A **Beállítások**lapon válassza a **manuális**lehetőséget. Adjon meg egy nevet (az előtagot *B2C_1A_* lehet automatikusan hozzáadni.). A **titkos** szövegmezőbe írja be a használni kívánt titkos kulcsot (például 1234567890). A **kulcshasználat**beállításnál válassza az **aláírás**lehetőséget. Kattintson a **Létrehozás** gombra. |
| outputClaim | hash | sztring | A jogcím-átalakítás után létrehozott ClaimType meghívása megtörtént. A `plaintext` inputClaim konfigurált jogcím. |

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

- Bemeneti jogcímek:
  - **egyszerű szöveg**: MyPass@word1
  - **só**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Kimeneti jogcímek:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U =

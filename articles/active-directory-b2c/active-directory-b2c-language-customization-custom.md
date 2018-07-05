---
title: Nyelvi testreszabás az Azure Active Directory B2C-vel egyéni szabályzatok |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az egyéni házirendek több nyelv tartalom honosítása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6269ac65e5db20521346d5312bcbadd0905c36e2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440564"
---
# <a name="language-customization-in-custom-policies"></a>Az egyéni szabályzat nyelvi testreszabás

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban.
> 

Az egyéni házirendek nyelvi testreszabás ugyanúgy működik mint beépített szabályzatokat.  Tekintse meg a beépített [dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) , amely bemutatja, hogyan egy kiválasztott a paramétereket és a böngésző beállításai alapján a működését.

## <a name="enable-supported-languages"></a>Engedélyezze a támogatott nyelvek
Ha nincs megadva a felhasználói felület – területi beállításokat, és ezeken a nyelveken egyik kéri a felhasználó böngészőjében, támogatott nyelvek jelennek meg a felhasználó számára.  

Támogatott nyelvek meghatározott `<BuildingBlocks>` a következő formátumban:

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

Alapértelmezett nyelv és támogatott nyelveket viselkedése ugyanúgy, mint a beépített szabályzatokat.

## <a name="enable-custom-language-strings"></a>Egyéni nyelvi karakterláncok engedélyezése

Egyéni nyelvi karakterláncok létrehozása két lépésből áll:
1. Szerkessze a `<ContentDefinition>` a lap, adja meg a kívánt nyelvhez egy erőforrás-azonosító
2. Hozzon létre a `<LocalizedResources>` a megfelelő azonosítóval a `<BuildingBlocks>`

Vegye figyelembe, hogy elhelyezhet egy `<ContentDefinition>` és `<BuildingBlock>` a kiterjesztés vagy a függő házirend fájl attól függően, hogy a módosítások kell lennie az öröklés szabályzatok, vagy nem is.

### <a name="edit-the-contentdefinition-for-the-page"></a>Az oldal a ContentDefinition szerkesztése

Minden laphoz szeretné megkeresni, megadhatja a `<ContentDefinition>` milyen nyelvű erőforrások keresse ki az egyes nyelvi kódot.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

Ebben a példában francia (fr) és az angol (en) egyéni karakterláncok hozzáadódnak az egyesített regisztrálási vagy bejelentkezési oldal.  A `LocalizedResourcesReferenceId` minden `LocalizedResourcesReference` ugyanaz, mint a területi beállítás, de használhat bármilyen karakterlánc-azonosító néven  Minden egyes nyelvi és oldal kombinációjával, létre kell hoznia egy megfelelő `<LocalizedResources>` az alábbiak.


### <a name="create-the-localizedresources"></a>A LocalizedResources létrehozása

A felülbírálások tárolják a `<BuildingBlocks>` , és nincs egy `<LocalizedResources>` minden oldalon és a nyelvi megadott a `<ContentDefinition>` minden egyes laphoz.  Minden egyes felülbírálás a következőként van megadva egy `<LocalizedString>` például a következő mintát:

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

A lap karakterlánc elemeinek négy típusa van:

**ClaimsProvider** -címkék Identitásszolgáltatók (Facebook, Google, az Azure AD stb.) **Takar** -címkék a-attribútumok és a kapcsolódó súgószöveg, vagy érvényesítési hibák mezőben **UxElement** - egyéb karakterlánc-elemek az oldalon, hogy vannak-e, például gombok, hivatkozásokat vagy szöveges alapértelmezésszerint**ErrorMessage** -űrlapon ellenőrzési hibaüzenetek

Ügyeljen arra, hogy a `StringId`s felel meg az oldal, használja ezeket a felülbírálásokat egyéb házirend érvényesítése feltöltéskor által le van tiltva.  
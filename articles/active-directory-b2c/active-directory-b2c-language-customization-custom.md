---
title: Az Azure Active Directory B2C egyéni házirendek nyelvi testreszabási |} Microsoft Docs
description: Ismerje meg, hogyan használható az egyéni házirendeket több nyelvhez tartalom localize.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 11/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c8deabd4d0a4126365b014875624525d5b1f3063
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711756"
---
# <a name="language-customization-in-custom-policies"></a>Az egyéni házirendek nyelvi testreszabása

> [!NOTE]
> A funkció jelenleg nyilvános előzetes verziójához.
> 

Az egyéni házirendek nyelvi testreszabási működik, mint beépített házirendek.  Tekintse meg a beépített [dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) , amely leírja, hogyan egy nyelvet a paraméterek és a böngésző beállításai alapján kell kiválasztani a viselkedését.

## <a name="enable-supported-languages"></a>Engedélyezze a támogatott nyelvek
Ha nincs megadva a felhasználói felület – területi beállításokat, és a felhasználó kéri az egyiken, támogatott nyelvek jelennek meg a felhasználó számára.  

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

Alapértelmezett nyelv és a támogatott nyelveket működik ugyanúgy, mint a beépített házirendek.

## <a name="enable-custom-language-strings"></a>Egyéni nyelvi karakterláncok engedélyezése

Egyéni nyelvi karakterláncok létrehozása két lépésből áll:
1. Szerkessze a `<ContentDefinition>` adjon meg a kívánt nyelvhez erőforrás-Azonosítót a lap
2. Hozzon létre a `<LocalizedResources>` a megfelelő azonosítók a `<BuildingBlocks>`

Ne feledje, hogy helyezhet el egy `<ContentDefinition>` és `<BuildingBlock>` a kiterjesztés vagy a függő házirend-fájl attól függően, hogy a módosítások a öröklő házirendek találhatók, vagy nem is.

### <a name="edit-the-contentdefinition-for-the-page"></a>A lap ContentDefinition szerkesztése

Az összes lapon localize, kívánt adhat meg a `<ContentDefinition>` milyen nyelvi erőforrásokat, amelyet meg kíván keresni az egyes nyelvi kódot.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

Ez a példa francia (fr) és az angol (en) egyéni karakterláncok kerülnek az egyesített előfizetési vagy a bejelentkezési oldalon.  A `LocalizedResourcesReferenceId` minden `LocalizedResourcesReference` ugyanaz, mint a területi beállítás, de bármilyen karakterlánc azonosítóként használata  Minden egyes nyelvet és a lap kombináció, létre kell hoznia egy megfelelő `<LocalizedResources>` az alábbiak.


### <a name="create-the-localizedresources"></a>A LocalizedResources létrehozása

A felülbírálások találhatók a `<BuildingBlocks>` , és nincs olyan `<LocalizedResources>` minden oldalon és a nyelvi megadott a `<ContentDefinition>` az összes lapon.  Minden felülbírálás van megadva egy `<LocalizedString>` például a következő mintát:

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

Az oldalon karakterlánc elem négy típusa van:

**ClaimsProvider** -címkéket az identitás-szolgáltatóktól (Facebook, Google, az Azure AD stb.) **ClaimType** -címkék a attribútumok és a kapcsolódó súgószöveg, vagy érvényesítési hibákat mezőben **UxElement** - egyéb karakterlánc, amely alapértelmezés szerint például gombok, hivatkozásokat vagy szöveges vannak-ealapelemeinek**ErrorMessage** -űrlapon ellenőrzési hibaüzenetek

Győződjön meg arról, hogy a `StringId`s felel meg a lap ezeket a felülbírálásokat egyéb házirend érvényesítésének feltöltéskor letiltotta a használatát.  
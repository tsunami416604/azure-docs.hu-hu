---
title: Honosítás – Azure Active Directory B2C
description: A Azure Active Directory B2C egyéni szabályzatának honosítási elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126756"
---
# <a name="localization"></a>Honosítás

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **honosítási** elem lehetővé teszi, hogy a felhasználói útvonalakhoz tartozó szabályzatban több területi beállítást vagy nyelvet támogasson. A házirendek honosítási támogatása a következőket teszi lehetővé:

- Állítsa be a szabályzat támogatott nyelveinek explicit listáját, és válasszon ki egy alapértelmezett nyelvet.
- Adja meg a nyelvspecifikus karakterláncokat és gyűjteményeket.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

A **honosítási** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Engedélyezve | Nem | Lehetséges értékek: `true` vagy `false`. |

A **honosítási** elem a következő XML-elemeket tartalmazza

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | A támogatott nyelvek listája. |
| LocalizedResources | 0: n | A honosított erőforrások listája. |

## <a name="supportedlanguages"></a>SupportedLanguages

A **SupportedLanguages** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| DefaultLanguage | Igen | A honosított erőforrások alapértelmezettként használandó nyelve. |
| MergeBehavior | Nem | Olyan értékek enumerálási értékei, amelyek össze vannak egyesítve egy olyan ClaimType, amely ugyanabban az azonosítóban szerepel egy szülő házirendben. Ezt az attribútumot akkor használja, ha az alapházirendben megadott jogcímet felülírja. Lehetséges értékek: `Append`, `Prepend`vagy `ReplaceAll`. A `Append` érték azt határozza meg, hogy a rendszer az adatgyűjtést a szülő házirendben megadott gyűjtemény végéhez fűzi. A `Prepend` érték azt adja meg, hogy a rendszer a szülő házirendben megadott gyűjtemény előtt hozzá lehessen adni a jelen lévő adatgyűjteményt. A `ReplaceAll` érték azt határozza meg, hogy a fölérendelt házirendben definiált adatgyűjteményt figyelmen kívül kell hagyni, ehelyett az aktuális házirendben definiált adatmennyiséget kell használnia. |

### <a name="supportedlanguages"></a>SupportedLanguages

A **SupportedLanguages** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Megjeleníti az RFC 5646-címkék nyelvi címkéjének megfelelő tartalmat a nyelvek azonosításához. |

## <a name="localizedresources"></a>LocalizedResources

A **LocalizedResources** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Egy azonosító, amely a honosított erőforrások egyedi azonosítására szolgál. |

A **LocalizedResources** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Meghatározza a különböző kultúrákban található teljes gyűjteményeket. A gyűjtemények különböző számú elemet és különböző sztringeket tartalmazhatnak különböző kultúrákban. Ilyen gyűjtemény például a jogcím-típusokban megjelenő enumerálások. Például egy ország/régió lista jelenik meg a felhasználó számára egy legördülő listában. |
| LocalizedStrings | 0: n | Meghatározza az összes karakterláncot, kivéve a gyűjteményekben megjelenő karakterláncokat különböző kultúrákban. |

### <a name="localizedcollections"></a>LocalizedCollections

A **LocalizedCollections** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | A támogatott nyelvek listája. |

#### <a name="localizedcollection"></a>LocalizedCollection

A **LocalizedCollection** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ElementType | Igen | Egy ClaimType elemre vagy egy felhasználói felületi elemre hivatkozik a házirend fájljában. |
| ElementId | Igen | A ClaimsSchema szakaszban már definiált jogcím-típusra mutató hivatkozást tartalmazó karakterlánc, amely akkor használatos, ha a **ElementType** claimType van beállítva. |
| TargetCollection | Igen | A célhely gyűjteménye. |

A **LocalizedCollection** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0: n | Meghatároz egy elérhető lehetőséget, amellyel a felhasználó kiválaszthat egy jogcímet a felhasználói felületen, például egy értéket a legördülő menüben. |

Az **Item** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Szöveg | Igen | Az ehhez a beállításhoz tartozó felhasználói felületen megjeleníteni kívánt felhasználóbarát megjelenítési karakterlánc. |
| Érték | Igen | A beállítás kiválasztásához társított karakterlánc-jogcím értéke. |
| SelectByDefault | Nem | Azt jelzi, hogy ez a beállítás alapértelmezés szerint ki van-e választva a felhasználói felületen. Lehetséges értékek: true vagy FALSE. |

A következő példa a **LocalizedCollections** elem használatát mutatja be. Két **LocalizedCollection** elemet tartalmaz, amelyek közül az egyik az angol, a másik pedig a spanyol. Állítsa be a jogcím **korlátozási** gyűjteményét `Gender` az angol és a spanyol nyelvű elemek listájával.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

A **LocalizedStrings** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Honosított karakterlánc. |

A **LocalizedString** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ElementType | Igen | Egy jogcím típusú elemre vagy a szabályzat felhasználói felületi elemére mutató hivatkozás. Lehetséges értékek: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`vagy `GetLocalizedStringsTransformationClaimType`. A `ClaimType` érték az egyik jogcím-attribútum honosítására szolgál a elemtípusú megadott módon. A `UxElement` érték a elemtípusú megadott felhasználói felületi elemek egyikének honosítására szolgál. A `ErrorMessage` érték a elemtípusú megadott rendszerüzenetek egyikének honosítására szolgál. A `Predicate` érték a elemtípusú megadott, a [predikátumban](predicates.md) található hibaüzenetek egyikének honosítására szolgál. A `InputValidation` érték a elemtípusú megadott [PredicateValidation](predicates.md) csoportba tartozó hibaüzenetek egyikének honosítására szolgál. A `GetLocalizedStringsTransformationClaimType` érték a honosított karakterláncok jogcímbe másolására szolgál. További információ: [GetLocalizedStringsTransformation jogcímek átalakítása](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId | Igen | Ha a **ElementType** `ClaimType`, `Predicate`vagy `InputValidation`értékre van beállítva, akkor ez az elem a ClaimsSchema szakaszban már definiált jogcím-típusra mutató hivatkozást tartalmaz. |
| StringId | Igen | Ha a **ElementType** értéke `ClaimType`, akkor ez az elem egy jogcím típusú attribútumra mutató hivatkozást tartalmaz. Lehetséges értékek: `DisplayName`, `AdminHelpText`vagy `PatternHelpText`. A `DisplayName` érték a jogcím megjelenítendő nevének megadására szolgál. A `AdminHelpText` érték a jogcím felhasználójának Súgó szövegének megadására szolgál. A `PatternHelpText` érték a jogcím mintájának Súgó szövegének megadására szolgál. Ha a **ElementType** értéke `UxElement`, akkor ez az elem egy felhasználói felületi elem attribútumára mutató hivatkozást tartalmaz. Ha a **ElementType** értéke `ErrorMessage`, akkor ez az elem egy hibaüzenet azonosítóját adja meg. A `UxElement`-azonosítók teljes listájáért tekintse meg a [honosítási karakterlánc-azonosítókat](localization-string-ids.md) .|


Az alábbi példa egy honosított regisztrációs oldalt mutat be. Az első három **LocalizedString** érték a jogcím attribútumot állítja be. A harmadik megváltoztatja a Folytatás gomb értékét. Az utolsó megváltoztatja a hibaüzenetet.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Az alábbi példa a `IsLengthBetween8And64`azonosítójú **predikátum** **UserHelpText** mutatja be. Valamint egy honosított **UserHelpText** a **PredicateGroup** azonosítóval, amely a `StrongPassword`azonosítójú **PredicateValidation** `CharacterClasses`.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Honosítás beállítása

Ebből a cikkből megtudhatja, hogyan támogathatja a házirendben szereplő több területi vagy nyelvi beállítást a felhasználói útvonalakhoz. A honosításhoz három lépés szükséges: állítsa be a támogatott nyelvek explicit listáját, adjon meg nyelvspecifikus karakterláncokat és gyűjteményeket, és szerkessze a lap ContentDefinition.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>A támogatott nyelvek explicit listájának beállítása

A **BuildingBlocks** elem alatt adja hozzá a **honosítás** elemet a támogatott nyelvek listájához. Az alábbi példa bemutatja, hogyan határozhatja meg a honosítási támogatást mind az angol (alapértelmezett), mind a spanyol nyelv esetében:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Következő lépések

A honosítási példákat a következő cikkekben találja:

- [Nyelvi Testreszabás egyéni házirenddel Azure Active Directory B2C](custom-policy-localization.md)
- [Nyelvi Testreszabás felhasználói folyamatokkal Azure Active Directory B2C](user-flow-language-customization.md)

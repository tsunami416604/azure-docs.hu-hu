---
title: Honosítás – Azure Active Directory B2C
description: Adja meg egy egyéni szabályzat honosítási elemét az Azure Active Directory B2C-ben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126756"
---
# <a name="localization"></a>Honosítás

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **Honosítás** i elem lehetővé teszi, hogy több területi beállítás vagy nyelv támogatását is lehetővé tegye a felhasználói utak házirendjében. A házirendek honosítási támogatása lehetővé teszi a következőket:

- Állítsa be a támogatott nyelvek explicit listáját egy házirendben, és válasszon egy alapértelmezett nyelvet.
- Adjon meg nyelvspecifikus karakterláncokat és gyűjteményeket.

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

A **Honosítási** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Engedélyezve | Nem | Lehetséges `true` értékek: `false`vagy . |

A **Honosítási** elem a következő XML-elemeket tartalmazza

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Támogatott nyelvek | 1:n | A támogatott nyelvek listája. |
| Honosított erőforrások | 0:n | Honosított erőforrások listája. |

## <a name="supportedlanguages"></a>Támogatott nyelvek

A **SupportedLanguages** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| DefaultLanguage | Igen | A honosított erőforrások alapértelmezett nyelve. |
| MergeBehavior (Körviselkedés) | Nem | Az azonos azonosítóval rendelkező szülőházirendben található bármely ClaimType-al egyesített értékek felsorolási értékei. Ezt az attribútumot akkor használja, ha felülírja az alapházirendben megadott jogcímet. Lehetséges `Append`értékek: `Prepend`, `ReplaceAll`vagy . Az `Append` érték azt adja meg, hogy a jelen lévő adatok gyűjtését hozzá kell fűzni a szülőházirendben megadott gyűjtemény végéhez. Az `Prepend` érték azt adja meg, hogy a jelen lévő adatok gyűjtését a szülőházirendben megadott gyűjtemény előtt hozzá kell adni. Az `ReplaceAll` érték azt adja meg, hogy a szülőházirendben definiált adatok gyűjtését figyelmen kívül kell hagyni, ehelyett az aktuális házirendben meghatározott adatokat használva. |

### <a name="supportedlanguages"></a>Támogatott nyelvek

A **SupportedLanguages** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Támogatott nyelv | 1:n | Az 5646-os számú RFC-dokumentumra vonatkozó nyelvi címkének megfelelő tartalmat jelenít meg – Címkék a nyelvek azonosítására. |

## <a name="localizedresources"></a>Honosított erőforrások

A **LocalizedResources** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A honosított erőforrások egyedi azonosítására használt azonosító. |

A **Honosított erőforrások** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Honosított gyűjtemények | 0:n | Meghatározza a teljes gyűjtemények különböző kultúrákban. A gyűjtemény különböző számú elemet és különböző karakterláncokat tartalmazhat a különböző kultúrákhoz. Gyűjtemények például a jogcímtípusokban megjelenő felsorolások. Például egy ország/régió lista jelenik meg a felhasználó számára egy legördülő listában. |
| Honosított karakterláncok | 0:n | Meghatározza az összes karakterláncot, kivéve azokat a karakterláncokat, amelyek különböző kultúrákban jelennek meg. |

### <a name="localizedcollections"></a>Honosított gyűjtemények

A **LocalizedCollections** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Honosítottgyűjtemény | 1:n | A támogatott nyelvek listája. |

#### <a name="localizedcollection"></a>Honosítottgyűjtemény

A **LocalizedCollection** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Elemtípusa | Igen | A házirendfájlban egy ClaimType elemre vagy egy felhasználói felületelemre hivatkozik. |
| ElementId | Igen | Olyan karakterlánc, amely a ClaimsSchema szakaszban már definiált jogcímtípusra mutató hivatkozást tartalmaz, ha az **ElementType** jogcímtípusra van állítva. |
| Célgyűjtemény | Igen | A célgyűjtemény. |

A **LocalizedCollection** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0:n | Meghatározza azt a rendelkezésre álló lehetőséget, amelyet a felhasználó a felhasználói felületen egy jogcímhez, például egy legördülő lista értékéhez választhat. |

Az **Elem** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Szöveg | Igen | A felhasználóbarát megjelenítési karakterlánc, amelyet a felhasználónak meg kell jelenítenie a felhasználói felületen ehhez a beállításhoz. |
| Érték | Igen | A beállítás kiválasztásához társított karakterlánc-jogcímérték. |
| SelectByDefault (Alapértelmezett beállítás) | Nem | Azt jelzi, hogy ezt a beállítást alapértelmezés szerint be kell-e jelölni a felhasználói felületen. Lehetséges értékek: Igaz vagy Hamis. |

A következő példa a **LocalizedCollections** elem használatát mutatja be. Két **LocalizedCollection** elemet tartalmaz, az egyik az angol, a másik a spanyol. Mindkettő a `Gender` **jogcím korlátozási** gyűjteményét angol és spanyol nyelvű elemek listájával állítja be.

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

### <a name="localizedstrings"></a>Honosított karakterláncok

A **LocalizedStrings** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Honosított karakterlánc | 1:n | Honosított karakterlánc. |

A **LocalizedString** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Elemtípusa | Igen | Hivatkozás a házirend jogcímtípus-elemére vagy felhasználói felületének elemére. `ClaimType`Lehetséges értékek: `UxElement` `ErrorMessage`, `Predicate`, `GetLocalizedStringsTransformationClaimType`, vagy . Az `ClaimType` érték a karakterlánc-azonosítóban megadott jogcímattribútumok egyikének honosítására szolgál. Az `UxElement` érték a felhasználói felület egyik elemének honosítására szolgál a Karakterlánc-azonosítóban megadottak szerint. Az `ErrorMessage` érték a karakterlánc-azonosítóban megadott rendszerhibaüzenetek egyikének honosítására szolgál. Az `Predicate` érték a Húrazonosítóban megadott [predikátumhibaüzenetek egyikének honosítására](predicates.md) szolgál. Az `InputValidation` érték a [PredicateValidation](predicates.md) csoport egyik hibaüzenetének honosítására szolgál a StringId azonosítóban megadottak szerint. Az `GetLocalizedStringsTransformationClaimType` érték a honosított karakterláncok jogcímekbe másolására szolgál. További információ: [GetLocalizedStringsTransformation jogcímek átalakítása](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId | Igen | Ha az **ElementType** `Predicate`beállítása `InputValidation` `ClaimType`a , vagy , ez az elem a ClaimsSchema szakaszban már definiált jogcímtípusra mutató hivatkozást tartalmaz. |
| Karakterlánc-azonosító | Igen | Ha az **ElementType** `ClaimType`beállítása, akkor ez az elem egy jogcímtípusú attribútumra mutató hivatkozást tartalmaz. Lehetséges `DisplayName`értékek: `AdminHelpText`, `PatternHelpText`vagy . Az `DisplayName` érték a jogcím megjelenítési nevének beállítására szolgál. Az `AdminHelpText` érték a jogcímfelhasználó súgószövegnevének beállítására szolgál. Az `PatternHelpText` érték a jogcímminta súgószövegének beállítására szolgál. Ha az **ElementType** beállítása `UxElement`a beállítás, akkor ez az elem egy felhasználói felületelem attribútumára mutató hivatkozást tartalmaz. Ha az **ElementType** `ErrorMessage`érték, akkor ez az elem egy hibaüzenet azonosítóját adja meg. Az azonosítók teljes listáját a [Honosítási karakterlánc-azonosítók](localization-string-ids.md) című témakörben található. `UxElement`|


A következő példa egy honosított regisztrációs lapot mutat be. Az első három **LocalizedString** érték a jogcím attribútumot állítja be. A harmadik módosítja a folytatás gomb értékét. Az utolsó módosítja a hibaüzenetet.

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

A következő példa egy honosított **UserHelpText** of **Predicate** id. `IsLengthBetween8And64` És egy honosított **UserHelpText** a **PredicateGroup** azonosítóval `CharacterClasses` **PredicateValidation** azonosítóval `StrongPassword`.

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

Ez a cikk bemutatja, hogyan támogathatja a felhasználói utazások házirendjében több területi beállításokat vagy nyelveket. A honosítás három lépést igényel: a támogatott nyelvek explicit listájának beállítása, nyelvspecifikus karakterláncok és gyűjtemények megadása, valamint a lap ContentDefinition szerkesztése.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>A támogatott nyelvek explicit listájának beállítása

A **BuildingBlocks** elem alatt adja hozzá a **Hoonizálás** elemet a támogatott nyelvek listájával. A következő példa bemutatja, hogyan határozhatja meg a honosítási támogatást angol (alapértelmezett) és spanyol nyelven egyaránt:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>További lépések

A honosítási példákat az alábbi cikkekben talál:

- [Nyelvi testreszabás egyéni szabályzattal az Azure Active Directory B2C-ben](custom-policy-localization.md)
- [Nyelvi testreszabás felhasználói folyamatokkal az Azure Active Directory B2C-ben](user-flow-language-customization.md)

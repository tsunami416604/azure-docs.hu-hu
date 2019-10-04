---
title: Honosítás – Azure Active Directory B2C
description: A Azure Active Directory B2C egyéni szabályzatának honosítási elemének megadásához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ec9b4e7ce761d524d047f4d12cab9e5b782e6032
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033462"
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
| Enabled | Nem | Lehetséges értékek: `true` vagy `false`. |

A **honosítási** elem a következő XML-elemeket tartalmazza

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | A támogatott nyelvek listája. |
| LocalizedResources | 0: n | A honosított erőforrások listája. |

## <a name="supportedlanguages"></a>SupportedLanguages

A **SupportedLanguages** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| DefaultLanguage | Igen | A honosított erőforrások alapértelmezettként használandó nyelve. |
| MergeBehavior | Nem | Olyan értékek enumerálási értékei, amelyek össze vannak egyesítve egy olyan ClaimType, amely ugyanabban az azonosítóban szerepel egy szülő házirendben. Ezt az attribútumot akkor használja, ha az alapházirendben megadott jogcímet felülírja. Lehetséges értékek: `Append`, `Prepend`, vagy `ReplaceAll`. Az `Append` érték azt határozza meg, hogy az aktuális adatgyűjteményt a szülő házirendben megadott gyűjtemény végéhez kell fűzni. Az `Prepend` érték azt határozza meg, hogy a rendszer a szülő házirendben megadott gyűjtemény előtt hozzá lehessen adni a jelen lévő adatgyűjteményt. Az `ReplaceAll` érték azt határozza meg, hogy a fölérendelt házirendben definiált adatgyűjtést figyelmen kívül kell hagyni, ehelyett az aktuális házirendben definiált adatmennyiséget kell használnia. |

### <a name="supportedlanguages"></a>SupportedLanguages

A **SupportedLanguages** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Megjeleníti az RFC 5646-címkék nyelvi címkéjének megfelelő tartalmat a nyelvek azonosításához. |

## <a name="localizedresources"></a>LocalizedResources

A **LocalizedResources** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Igen | Egy azonosító, amely a honosított erőforrások egyedi azonosítására szolgál. |

A **LocalizedResources** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Meghatározza a különböző kultúrákban található teljes gyűjteményeket. A gyűjtemények különböző számú elemet és különböző sztringeket tartalmazhatnak különböző kultúrákban. Ilyen gyűjtemény például a jogcím-típusokban megjelenő enumerálások. Például egy ország/régió lista jelenik meg a felhasználó számára egy legördülő listában. |
| LocalizedStrings | 0: n | Meghatározza az összes karakterláncot, kivéve a gyűjteményekben megjelenő karakterláncokat különböző kultúrákban. |

### <a name="localizedcollections"></a>LocalizedCollections

A **LocalizedCollections** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
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

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0: n | Meghatároz egy elérhető lehetőséget, amellyel a felhasználó kiválaszthat egy jogcímet a felhasználói felületen, például egy értéket a legördülő menüben. |

Az **Item** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Text | Igen | Az ehhez a beállításhoz tartozó felhasználói felületen megjeleníteni kívánt felhasználóbarát megjelenítési karakterlánc. |
| Value | Igen | A beállítás kiválasztásához társított karakterlánc-jogcím értéke. |
| SelectByDefault | Nem | Azt jelzi, hogy ez a beállítás alapértelmezés szerint ki van-e választva a felhasználói felületen. Lehetséges értékek: IGAZ vagy hamis. |

A következő példa a **LocalizedCollections** elem használatát mutatja be. Két **LocalizedCollection** elemet tartalmaz, amelyek közül az egyik az angol, a másik pedig a spanyol. Állítsa be a jogcím `Gender` korlátozási gyűjteményét az angol és a spanyol nyelvű elemek listájával.

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

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Honosított karakterlánc. |

A **LocalizedString** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ElementType | Igen | Egy jogcím típusú elemre vagy a szabályzat felhasználói felületi elemére mutató hivatkozás. Lehetséges értékek: `ClaimType` `UxElement` ,`Predicate`,,, vagy. `ErrorMessage` Az `ClaimType` érték az egyik jogcím-attribútum honosítása a elemtípusú megadott módon történik. Az `UxElement` érték az elemtípusú megadott felhasználói felületi elemek egyikének honosítására szolgál. A `ErrorMessage` rendszer a elemtípusú megadott rendszerüzenetek egyikének honosítására használja az értéket. Az `Predicate` érték az egyik [predikátum](predicates.md) hibaüzenetének honosítására szolgál, a elemtípusú megadott módon. Az `InputValidation` érték a elemtípusú megadott [PredicateValidation](predicates.md) csoportba tartozó hibaüzenetek egyikének honosítására szolgál. |
| ElementId | Igen | Ha a **ElementType** `ClaimType`értéke, `Predicate`, vagy `InputValidation`, ez az elem a ClaimsSchema szakaszban már definiált jogcím-típusra mutató hivatkozást tartalmaz. |
| StringId | Igen | Ha a **ElementType** beállítás értéke `ClaimType`, ez az elem egy jogcím típusú attribútumra mutató hivatkozást tartalmaz. Lehetséges értékek: `DisplayName`, `AdminHelpText`, vagy `PatternHelpText`. Az `DisplayName` érték a jogcím megjelenítendő nevének megadására szolgál. Az `AdminHelpText` érték a jogcím felhasználójának Súgó szövegének megadására szolgál. Az `PatternHelpText` érték a jogcím mintájának Súgó szövegének megadására szolgál. Ha a **ElementType** `UxElement`értéke, ez az elem egy felhasználói felületi elem attribútumára mutató hivatkozást tartalmaz. Ha a **ElementType** `ErrorMessage`értéke, akkor ez az elem egy hibaüzenet azonosítóját adja meg. Az`UxElement` azonosítók teljes listájáért tekintse meg a [honosítási karakterlánc](localization-string-ids.md) -azonosítókat.|


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

A következő példa egy honosított **UserHelpText** mutatja be az azonosítóval `IsLengthBetween8And64`. Valamint egy honosított **UserHelpText** , `CharacterClasses` amely a **PredicateValidation** azonosítóval rendelkező `StrongPassword` **PredicateGroup** .

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

### <a name="provide-language-specific-strings-and-collections"></a>Nyelvspecifikus karakterláncok és gyűjtemények megadása

A **SupportedLanguages** elem lezárása után vegyen fel **LocalizedResources** elemeket a **honosítási** elemen belül. Az egyes oldalakhoz ( **LocalizedResources** ) és a támogatni kívánt nyelvekhez adja hozzá az elemeket. Az egyesített regisztrációs vagy bejelentkezési oldal, az angol, a spanyol és a franciaországi regisztrációs és multi-Factor Authentication-(MFA-) lapok testreszabásához adja hozzá a következő **LocalizedResources** elemeket.

- Egyesített regisztrációs vagy bejelentkezési oldal, angol nyelven`<LocalizedResources Id="api.signuporsignin.en">`
- Egyesített regisztrációs vagy bejelentkezési oldal, Spanyol`<LocalizedResources Id="api.signuporsignin.es">`
- Egyesített regisztrációs vagy bejelentkezési oldal, Franciaország`<LocalizedResources Id="api.signuporsignin.fr">`
- Regisztráció, angol nyelven`<LocalizedResources Id="api.localaccountsignup.en">`
- Regisztráció, Spanyol`<LocalizedResources Id="api.localaccountsignup.es">`
- Regisztráció, Franciaország`<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, angol`<LocalizedResources Id="api.phonefactor.en">`
- MFA, Spanyol`<LocalizedResources Id="api.phonefactor.es">`
- MFA, Franciaország`<LocalizedResources Id="api.phonefactor.fr">`

Minden **LocalizedResources** elem tartalmazza az összes szükséges **LocalizedStrings** elemet, amely több **LocalizedString** elemet és **LocalizedCollections** elemet tartalmaz több **LocalizedCollection** elemek.  A következő példa hozzáadja a regisztrációs oldalt az angol nyelvű honosításhoz:

Megjegyzés: Ez a példa a `Gender` és `City` a jogcím típusára hivatkozik. A példa használatához győződjön meg arról, hogy definiálja ezeket a jogcímeket. További információ: [ClaimsSchema](claimsschema.md).

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

A regisztrációs oldal honosítása spanyol nyelven.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>A lap ContentDefinition szerkesztése

A honosítani kívánt összes oldalon meg kell adnia a **ContentDefinition**keresendő nyelvi kódokat.

A következő példában az angol (en) és a spanyol (es) egyéni sztringek hozzáadódnak a regisztrációs laphoz. Az egyes **LocalizedResourcesReference** **LocalizedResourcesReferenceId** megegyeznek a területi beállításokkal, de az azonosítóhoz bármilyen karakterláncot használhat. Minden nyelv és oldal kombináció esetében mutasson a korábban létrehozott megfelelő **LocalizedResources** .

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

A következő példa a végső XML-t mutatja be:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```

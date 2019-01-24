---
title: A honosítás – az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a honosítás elem egyéni szabályzat az Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a0abf2fd3502238af0e0f02cb8f9917cfb90b586
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54854973"
---
# <a name="localization"></a>Honosítás

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **honosítási** elem lehetővé teszi, hogy több területi beállításokat, illetve azokat a nyelveket támogatja a felhasználói utak esetében a házirendben. A szabályzatok a honosítási támogatás lehetővé teszi:

- Állítsa be egy szabályzatot a támogatott nyelveket explicit listáját, és válasszon egy alapértelmezett nyelvet.
- Adja meg a nyelvspecifikus karakterláncok és gyűjteményeket.

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

A **honosítási** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Engedélyezve | Nem | A lehetséges értékek: `true` vagy `false`. |

A **honosítási** elem a következő XML-elemeket tartalmazza

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Támogatott nyelvek listája. | 
| LocalizedResources | 0:n | Honosított erőforrások listája. |

## <a name="supportedlanguages"></a>SupportedLanguages

A **SupportedLanguages** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| DefaultLanguage | Igen | Honosított erőforrások alapértelmezés szerint használandó nyelvet. |
| MergeBehavior | Nem | Egy enumerálási együtt bármely takar egyesítendő értékek szerepelnek a szülő házirend ugyanezzel az azonosítóval. Akkor használja ezt az attribútumot, ha alap szabályzatban megadott jogcím felülírja. A lehetséges értékek: `Append`, `Prepend`, vagy `ReplaceAll`. A `Append` az érték határozza meg, hogy a jelenlegi adatgyűjtés sémaelemhez hozzáadódik a szülő házirendben megadott gyűjtemény végéhez. A `Prepend` az érték határozza meg, hogy ezen adatok gyűjtését a szülő házirendben megadott gyűjtemény előtt kell adni. A `ReplaceAll` az érték határozza meg, hogy a szülő házirendben meghatározott adatgyűjtés figyelmen kívül lehet hagyni, inkább az aktuális házirendben definiált adatok használatával. |

### <a name="supportedlanguages"></a>SupportedLanguages

A **SupportedLanguages** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Tartalom, amely megfelel egy nyelvcímkét RFC 5646 - nyelvek azonosító címkék szerint jeleníti meg. | 

## <a name="localizedresources"></a>LocalizedResources

A **LocalizedResources** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Honosított erőforrások egyedi azonosításához használt azonosító. |

A **LocalizedResources** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Teljes gyűjtemények különböző kulturális környezetek határozza meg. Egy gyűjtemény elemek és számos országban különböző karakterláncok különböző számú rendelkezhet. Gyűjtemények közé tartoznak az enumerálások jelennek meg, a jogcím-típusok. Például ország/régió listáját egy legördülő lista a felhasználó számára látható. |
| LocalizedStrings | 0:n | Meghatározza a karakterláncokat, kivéve azokat, amelyek megjelennek a gyűjtemények, a különböző kulturális környezetek karakterláncok mindegyikét. |

### <a name="localizedcollections"></a>LocalizedCollections

A **LocalizedCollections** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Támogatott nyelvek listája. |

#### <a name="localizedcollection"></a>LocalizedCollection

A **LocalizedCollection** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Elemtípus | Igen | Egy takar elemet vagy egy felhasználói felület elem a házirend-fájl hivatkozik. |
| ElementId | Igen | Egy karakterlánc, amely már tartalmaz egy hivatkozást egy jogcímtípust akkor használatos, ha az ClaimsSchema szakaszban definiált **ElementType** egy takar értékre van állítva. |
| TargetCollection | Igen | A célgyűjtemény ügyfélszámítógépein. |

A **LocalizedCollection** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0:n | A felhasználó számára egy jogcímet a felhasználói felületen, például egy értéket a legördülő listában válassza ki valamelyik elérhető lehetőség határozza meg. |

A **elem** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Szöveg | Igen | A megjelenített felhasználóbarát mezőnevét karakterlánc, hogy megjelenjen-e a felhasználó a felhasználói felületen ezt a lehetőséget választja. |
| Érték | Igen | A karakterlánc jogcím társított Ez a beállítás értéke. |

Az alábbi példa bemutatja a használatát a **LocalizedCollections** elemet. Tartalmaz két **LocalizedCollection** elemeit, így az angol és spanyol egy másik. Mindkettő a **korlátozás** gyűjtemény a jogcím `Gender` az angol és spanyol elemek listáját.

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
| LocalizedString | 1:n | Honosított karakterlánc. |

A **LocalizedString** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Elemtípus | Igen | A hivatkozás egy jogcím típusú elem vagy egy felhasználói felület elem a házirendben. A lehetséges értékek: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`, vagy a. A `ClaimType` honosítani az egyik jogcím attribútumait, a karakterlánc megadott értéket használja. A `UxElement` honosítani a felhasználói felületi elemekre a elemtípusú megadott egyik értéket használja. A `ErrorMessage` honosítása, a rendszer hibaüzenetek a elemtípusú megadott értéket használja. A `Predicate` honosítani az egyik érték szolgál a [predikátum](predicates.md) hibaüzeneteket, a karakterlánc megadott módon. A `InputValidation` honosítani az egyik érték szolgál a [PredicateValidation](predicates.md) hibaüzenetek a elemtípusú megadott csoportban. |
| ElementId | Igen | Ha **ElementType** értékre van állítva `ClaimType`, `Predicate`, vagy `InputValidation`, ez az elem már az ClaimsSchema szakaszban meghatározott jogcím típusa hivatkozást tartalmaz. | 
| StringId | Igen | Ha **ElementType** értékre van állítva `ClaimType`, ez az elem egy jogcímtípust attribútuma hivatkozást tartalmaz. A lehetséges értékek: `DisplayName`, `AdminHelpText`, vagy `PatternHelpText`. A `DisplayName` érték szolgál a jogcím megjelenítendő nevét. A `AdminHelpText` értéket állítsa be a jogcím felhasználói súgó szövege nevét használja. A `PatternHelpText` értéket használja a jogcím-minta súgószöveg beállításához. Ha **ElementType** értékre van állítva `UxElement`, ez az elem egy attribútum egy felhasználói felület elem hivatkozást tartalmaz. Ha **ElementType** értékre van állítva `ErrorMessage`, ezt az elemet adja meg a hibaüzenetet azonosítóját. Lásd: [honosítási karakterlánc azonosítók](localization-string-ids.md) teljes listája a `UxElement` azonosítók.|


Az alábbi példa egy honosított előfizetési lapját jeleníti meg. Az első három **LocalizedString** értékeket a jogcím attribútum értéke. A harmadik módosítja a Folytatás gombra értékét. Az utolsót módosítja a hibaüzenetet.

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

Az alábbi példa egy honosított bemutatja a **UserHelpText** , **predikátum** azonosítójú `IsLengthBetween8And64`. És a egy honosított **UserHelpText** , **PredicateGroup** azonosítójú `CharacterClasses` , **PredicateValidation** azonosítójú `StrongPassword`.

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

## <a name="set-up-localization"></a>A honosítás beállítása

Ez a cikk bemutatja, hogyan támogatja a több területi beállításokat, illetve azokat a nyelveket a házirendet a felhasználói utak. Honosítási három lépésből áll: beállításról a támogatott nyelvek explicit listáját adja meg a nyelvspecifikus karakterláncok és a gyűjtemények, és az oldal a ContentDefinition szerkesztése.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Állítsa be a explicit támogatott nyelvek listája

Alatt a **BuildingBlocks** elemben adja hozzá a **honosítási** elem a támogatott nyelvek listáját. Az alábbi példa bemutatja, hogyan adhat meg a honosítási támogatás angol (alapértelmezett) és spanyol:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Adja meg a nyelvspecifikus karakterláncok és a gyűjtemények 

Adjon hozzá **LocalizedResources** belüli elemeket a **honosítási** elem lezárása után a **SupportedLanguages** elemet. Hozzáadhat **LocalizedResources** elemei az egyes oldalak (tartalomdefiníció) és a támogatni kívánt nyelvet. Testre szabhatja az egyesített regisztrálási vagy bejelentkezési oldal, a regisztráció és a többtényezős hitelesítés (MFA) lapok angol, spanyol és Franciaország, adja hozzá a következő **LocalizedResources** elemeket.  
- Egyesített regisztrálási vagy bejelentkezési oldal, angol nyelven `<LocalizedResources Id="api.signuporsignin.en">`
- Egyesített regisztrálási vagy bejelentkezési oldal, spanyol `<LocalizedResources Id="api.signuporsignin.es">`
- Egyesített regisztrálási vagy bejelentkezési oldal, Franciaország `<LocalizedResources Id="api.signuporsignin.fr">` 
- Előfizetési, angol nyelven `<LocalizedResources Id="api.localaccountsignup.en">`
- Előfizetési, spanyol `<LocalizedResources Id="api.localaccountsignup.es">`
- Előfizetési, Franciaország `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, angol nyelven `<LocalizedResources Id="api.phonefactor.en">`
- MFA, spanyol `<LocalizedResources Id="api.phonefactor.es">`
- MFA, Franciaország `<LocalizedResources Id="api.phonefactor.fr">`

Minden egyes **LocalizedResources** elem tartalmazza a szükséges összes **LocalizedStrings** több olyan elemet **LocalizedString** elemek és  **LocalizedCollections** több olyan elemet **LocalizedCollection** elemeket.  Az alábbi példa hozzáadja a regisztrációs oldalon angol nyelvű verzióban: 

Megjegyzés: Ebben a példában egy hivatkozik `Gender` és `City` jogcím-típusokat. Ebben a példában használja, győződjön meg róla, hogy azokat a jogcímeket határoz meg. További információkért lásd: [ClaimsSchema](claimsschema.md).

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

A regisztrációs oldalon honosításának spanyol.

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

### <a name="edit-the-contentdefinition-for-the-page"></a>Az oldal a ContentDefinition szerkesztése 

Minden egyes megkeresni a kívánt lapra, adja meg a keresendő nyelvkódokról a **ContentDefinition**.

A következő példában az angol (en) és spanyol (es) egyéni karakterláncok hozzáadódnak a regisztrációs oldalon. A **LocalizedResourcesReferenceId** minden **LocalizedResourcesReference** ugyanaz, mint a területi beállítás, de használhat bármilyen karakterlánc azonosítóként. Minden egyes nyelvi és oldal kombinációjával, a megfelelő pont **LocalizedResources** korábban létrehozott.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

Az alábbi példa bemutatja a végső XML:

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





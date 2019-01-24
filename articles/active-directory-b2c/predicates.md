---
title: Predikátumok és PredicateValidations – az Azure Active Directory B2C |} A Microsoft Docs
description: Közösségi fiók jogcímek átalakítása példák az identitás élmény keretrendszer sémát az Azure Active Directory B2C a.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 24aa6bf67b3b841b950e047a5c2509f04a3546b7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850858"
---
# <a name="predicates-and-predicatevalidations"></a>Predikátumok és PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **predikátumokat** és **PredicateValidations** elemek lehetővé teszik annak érdekében, hogy csak a megfelelően formázott adatok írja be a rendszer az Azure Active Directory (Azure AD) B2C-bérlő egy érvényesítési folyamat végrehajtásához .  

Az alábbi ábrán az elemek közötti kapcsolat látható:  

![Predikátumok](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikátumok  

A **predikátum** elem egy alapvető érvényesítési ellenőrzéséhez egy jogcímtípust értékét határozza meg, és adja vissza `true` vagy `false`. Az érvényesítés egy meghatározott történik **metódus** elem és a egy **paraméter** módszer vonatkozó elemek. Egy predikátum például ellenőrizheti, hogy egy karakterlánc jogcím értékének hossza megadott minimális és maximális paraméterek tartományán belül, vagy e tartalmazza-e egy karakterláncértéket jogcím egy karakterkészlet. A **UserHelpText** elem a felhasználók számára biztosít egy hibaüzenet, ha az ellenőrzés sikertelen. Az érték **UserHelpText** elem honosítható használatával [nyelvi testreszabás](localization.md).

A **predikátumokat** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Predikátum | 1:n | Predikátumok listája. | 

A **predikátum** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A predikátum használt azonosító. Más elemek használhatja ezt az azonosítót a házirendben. |
| Módszer | Igen | Az érvényesítéshez használandó módszer típusa. Érvényes értékek: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**, vagy **IsDateRange**. A **IsLengthRange** érték ellenőrzi, hogy a megadott minimális és maximális paraméter tartományán belül van-e egy karakterlánc jogcím értékének hossza. A **MatchesRegex** értékét ellenőrzi, hogy egy karakterlánc jogcím értéke megegyezik-e egy reguláris kifejezést. A **IncludesCharacters** értékét ellenőrzi, hogy jogcím egy karakterláncértéket tartalmaz egy karakterkészlet. A **IsDateRange** értékét ellenőrzi, hogy egy olyan dátumértéket jogcím megadott minimális és maximális paraméterek számos között. |

A **predikátum** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | A felhasználók számára, ha az ellenőrzés sikertelen, egy hibaüzenet. Ez a karakterlánc honosítható használatával a [nyelvi testreszabás](localization.md) |
| Paraméterek | 1:1 | A karakterlánc érvényesítési metódus típusú paramétereit. | 

A **paraméterek** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Paraméter | 1:n | A karakterlánc érvényesítési metódus típusú paramétereit. | 

A **paraméter** elem tartalmazza a következő attribútumokat:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Azonosító | 1:1 | A paraméter-azonosítója. |

A következő példa bemutatja egy `IsLengthRange` a paraméterekkel rendelkező metódus `Minimum` és `Maximum` , amelyek meghatározzák, hogy a karakterlánc hosszának tartományán:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

A következő példa bemutatja egy `MatchesRegex` paraméterrel metódus `RegularExpression` , amely meghatározza, hogy egy adott reguláris kifejezésnek:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

A következő példa bemutatja egy `IncludesCharacters` paraméterrel metódus `CharacterSet` , amely meghatározza, hogy karakterkészlet:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

A következő példa bemutatja egy `IsDateRange` a paraméterekkel rendelkező metódus `Minimum` és `Maximum` dátumtartományát, amelyek meghatározzák az formátumú, `yyyy-MM-dd` és `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations 

A predikátum meghatározása egy jogcímtípust összevetni az érvényesítés közben a **PredicateValidations** csoport predikátumok kialakításához arra az egy felhasználó bemenetellenőrzésből alkalmazható a jogcím típusa egy készletét. Minden egyes **PredicateValidation** elem tartalmaz egy **PredicateGroup** készletét tartalmazó elemek **PredicateReference** elemeket, amelyeket egy mutat**Predikátum**. Az érvényesítés át, a jogcím értékét kell átadnia mindegyik bármely predikátum összes teszt a **PredicateGroup** azok készletével együtt **PredicateReference** elemeket.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

A **PredicateValidations** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | A predikátum érvényesítési listája. | 

A **PredicateValidation** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A predikátum érvényesítési használt azonosító. A **takar** elem használhatja ezt az azonosítót a házirendben. |

A **PredicateValidation** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | Predikátum csoportok listája. | 

A **PredicateGroups** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | Predikátumok listája. | 

A **PredicateGroup** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A predikátum csoport használt azonosító.  |

A **PredicateGroup** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  A predikátum, hogy a felhasználók tudni, hogy milyen előnyöket leírását, adjon meg. | 
| PredicateReferences | 1:n | Predikátumhivatkozások listáját. | 

A **PredicateReferences** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| MatchAtLeast | Nem | Itt adhatja meg, hogy az értéknek meg kell felelnie legalább, hogy számos predikátum el kell fogadni a bemenet-definíciói. |

A **PredicateReferences** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | A predikátum hivatkozik. | 

A **PredicateReference** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A predikátum érvényesítési használt azonosító.  |


## <a name="configure-password-complexity"></a>Jelszó bonyolultsága konfigurálása

A **predikátumokat** és **PredicateValidationsInput** szabályozhatja, hogy a bonyolultsági feltételeknek-fiók létrehozása során a felhasználó által megadott jelszót. Alapértelmezés szerint az Azure AD B2C-t használja az erős jelszavak. Az Azure AD B2C-t is támogatja a konfigurációs beállítások vezérléséhez, amellyel az ügyfelek jelszavak bonyolultságát. Jelszó bonyolultsága ezen predikátum elemek használatával adhatja meg: 

- **IsLengthBetween8And64** használatával a `IsLengthRange` metódus, ellenőrzi, hogy a jelszónak 8 – 64 karakter hosszúságúnak kell lennie.
- **Kis** használatával a `IncludesCharacters` metódus, ellenőrzi, hogy a jelszó tartalmazza-e egy kisbetű.
- **Nagybetűk** használatával a `IncludesCharacters` metódus, ellenőrzi, hogy a jelszó tartalmazza, egy nagybetű.
- **Szám** használatával a `IncludesCharacters` metódus, ellenőrzi, hogy a jelszó egy számjegyet tartalmaz-e.
- **Symbol** használatával a `IncludesCharacters` metódus, ellenőrzi, hogy a jelszó következő szimbólumok egyikét tartalmazza `@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **PIN-kód** használatával a `MatchesRegex` metódus, ellenőrzi, hogy a jelszó csak számokat tartalmaz-e.
- **AllowedAADCharacters** használatával a `MatchesRegex` metódus, ellenőrzi, hogy a jelszó csak érvénytelen karaktert lett megadva.
- **DisallowedWhitespace** használatával a `MatchesRegex` metódus, ellenőrzi, hogy a jelszó nem kezdődik és nem végződhet térközkarakterrel.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Alapszintű ellenőrzés határozza meg, miután összevonni őket, és létrehozhat egy csoportot, amely a szabályzatban használhatja jelszóházirendek:

- **SimplePassword** érvényesíti a DisallowedWhitespace AllowedAADCharacters és IsLengthBetween8And64
- **StrongPassword** érvényesíti a DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Az utolsó csoport `CharacterClasses` fut egy további készletét predikátumok `MatchAtLeast` 3 értékre. A felhasználó jelszavának 8 – 16 karakter, és hármat a következő karakterek között kell lennie: Kisbetűk, nagybetűk, számmal vagy szimbólummal.
- **CustomPassword** csak DisallowedWhitespace, AllowedAADCharacters érvényesíti. Felhasználói biztosíthat, így minden bármely hosszúságú jelszót, mindaddig, amíg a karakterek érvényesek.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

A jogcímtípus, adja hozzá a **PredicateValidationReference** elemet, és adja meg az azonosító a predikátum ellenőrzések, például SimplePassword, StrongPassword vagy CustomPassword egyikét.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

A következő bemutatja, hogyan vannak rendszerezve a elemeket az Azure AD B2C-vel a hibaüzenetet jeleníti meg:

![Predikátum folyamat](./media/predicates/predicates-pass.png)

 ## <a name="configure-a-date-range"></a>Egy adott időtartományban konfigurálása

Az a **predikátumokat** és **PredicateValidations** elemek minimális és maximális dátum értékét szabályozhatja a **UserInputType** használatával egy `DateTimeDropdown`. Ehhez hozzon létre egy **predikátum** együtt a `IsDateRange` metódust, és adja meg a minimális és maximális paraméterek.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Adjon hozzá egy **PredicateValidation** való hivatkozással a `DateRange` predikátum.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Adja hozzá a jogcímtípus **PredicateValidationReference** elemet, és adja meg az azonosítója, mint `CustomDateRange`. 
    
```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

---
title: Predikátumok és predikátumokÉrvényesítések
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C egyéni szabályzatai használatával megakadályozhatja, hogy az Azure AD B2C-bérlő bekerüljön a hibásan formázott adatok.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc61ef5980a8019514f05c1db47f2300fff3603b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187236"
---
# <a name="predicates-and-predicatevalidations"></a>Predikátumok és predikátumokÉrvényesítések

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **predikátumok** és **predicatevalidations** elemek lehetővé teszik, hogy egy érvényesítési folyamat, annak biztosítása érdekében, hogy csak megfelelően formázott adatok at az Azure Active Directory B2C (Azure AD B2C) bérlő.

Az alábbi ábra az elemek közötti kapcsolatot mutatja be:

![Predikátumok és predikátum-érvényesítési viszonyt bemutató diagram](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikátumok

A **Predikátum** elem alapvető érvényesítést határoz meg a `true` `false`jogcímtípus értékének ellenőrzésére, és a visszáru vagy a . Az ellenőrzés egy meghatározott **Metódus** elem és a metódushoz tartozó **paraméterelemek** készletének használatával történik. Egy predikátum például ellenőrizheti, hogy egy karakterlánc jogcímértékének hossza a megadott minimális és maximális paraméterek tartományán belül van-e, vagy hogy egy karakterlánc-jogcímérték tartalmaz-e karakterkészletet. A **UserHelpText** elem hibaüzenetet ad a felhasználóknak, ha az ellenőrzés sikertelen. A **UserHelpText** elem értéke honosítható a [nyelvi testreszabással.](localization.md)

A **Predicates** elemnek közvetlenül a [BuildingBlocks](buildingblocks.md) elemBen lévő **ClaimsSchema** elemet követve kell megjelennie.

A **Predicates** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Predikátum | 1:n | Egy lista a predikátumokról. |

A **predikátumelem** a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Az állítmányhoz használt azonosító. Más elemek is használhatják ezt az azonosítót a házirendben. |
| Módszer | Igen | Az ellenőrzéshez használandó metódustípus. Lehetséges értékek: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**vagy **IsDateRange**. Az **IsLengthRange** érték azt ellenőrzi, hogy a karakterlánc-jogcímérték hossza a megadott minimális és maximális paraméterek tartományán belül van-e. A **MatchesRegex** érték azt ellenőrzi, hogy egy karakterlánc-jogcímértéke megegyezik-e egy reguláris kifejezéssel. A **IncludesCharacters** érték ellenőrzi, hogy a karakterlánc jogcímértéke tartalmaz-e karakterkészletet. Az **IsDateRange** érték ellenőrzi, hogy a dátumjogcímértéke a megadott minimális és maximális paramétertartomány között van-e. |
| HelpText | Nem | Hibaüzenet a felhasználók számára, ha az ellenőrzés sikertelen. Ez a karakterlánc a [nyelvi testreszabással](localization.md) honosítható |

A **predikátum** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | (Elavult) Hibaüzenet a felhasználók számára, ha az ellenőrzés sikertelen. |
| Paraméterek | 1:1 | A karakterlánc-érvényesítés metódustípusának paraméterei. |

A **Paraméterek** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Paraméter | 1:n | A karakterlánc-érvényesítés metódustípusának paraméterei. |

A **Paraméter** elem a következő attribútumokat tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Azonosító | 1:1 | A paraméter azonosítója. |

A következő példa `IsLengthRange` egy olyan `Minimum` metódust mutat be, amely tartalmazza a paramétereket, és `Maximum` amely megadja a karakterlánc hossztartományát:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

A következő példa `MatchesRegex` egy olyan `RegularExpression` metódust mutat be, amelynek paramétere reguláris kifejezést ad meg:

```XML
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

A következő példa `IncludesCharacters` egy olyan `CharacterSet` metódust mutat be, amely nek nincs meg a karakterkészlete:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

A következő példa `IsDateRange` egy paraméteres `Minimum` `Maximum` módszert mutat be, amely `yyyy-MM-dd` a `Today`dátumtartományt adja meg a és a formátumával.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>Predikátumérvényesítések

Míg a predikátumok határozzák meg az érvényesítést, hogy ellenőrizze egy jogcímtípussal szemben, a **PredicateValidations** egy sor predikátumcsoportot csoportosít, hogy felhasználói bevitel-érvényesítést hozzon létre, amely alkalmazható egy jogcímtípusra. Minden **predikátumérvényesítési** elem **predikátumcsoportelemeket** tartalmaz, amelyek **predikátumra** mutató predikátumra mutató predikátumra mutató predikátum-elemeket tartalmaznak. **Predicate** Az érvényesítéshez a jogcím értékének át kell mennie az összes **predikátumcsoport** összes állítmánytesztjén a **PredicateReference** elemek készletével.

A **PredicateValidations** elemnek közvetlenül a [BuildingBlocks](buildingblocks.md) elem **Predicates** elemét követve kell megjelennie.

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

A **PredicateValidations** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Predikátumérvényesítés | 1:n | Az alapigazolások listája. |

A **PredicateValidation** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Az alapadat-érvényesítéshez használt azonosító. A **ClaimType** elem használhatja ezt az azonosítót a házirendben. |

A **PredicateValidation** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Predikátumcsoportok | 1:n | Predikátumcsoportok listája. |

A **PredicateGroups** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Predikátumcsoport | 1:n | Egy lista a predikátumokról. |

A **PredicateGroup** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A predikátumcsoporthoz használt azonosító.  |

A **PredicateGroup** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  A predikátum leírása, amely hasznos lehet a felhasználók számára, hogy tudják, milyen értéket kell beírniuk. |
| Predikátumreferenciák | 1:n | Az alapreferenciák listája. |

A **PredicateReferences** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| MatchatLeast között | Nem | Itt adható meg, hogy az értéknek legalább ennyi predikátumdefinícióval meg kell egyeznie a bemenet elfogadásához. Ha nincs megadva, az értéknek meg kell egyeznie az összes predikátumdefinícióval. |

A **PredicateReferences** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Predikátumhivatkozás | 1:n | Egy predikátumra való utalás. |

A **PredicateReference** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Az alapadat-érvényesítéshez használt azonosító.  |


## <a name="configure-password-complexity"></a>Jelszó összetettségének konfigurálása

A **Predicates** és **predicateValidationsInput** szabályozhatja a felhasználó által megadott jelszavak összetettségi követelményeit a fiók létrehozásakor. Alapértelmezés szerint az Azure AD B2C erős jelszavakat használ. Az Azure AD B2C is támogatja a konfigurációs beállításokat az ügyfelek által használható jelszavak összetettségének szabályozására. A jelszó összetettségét a következő predikátumelemek kelítheti:

- **IsLengthBetween8And64** a `IsLengthRange` metódus használatával ellenőrzi, hogy a jelszó nak 8 és 64 karakter között kell lennie.
- A `IncludesCharacters` **kisbetűs** módszerrel ellenőrzi, hogy a jelszó kisbetűt tartalmaz-e.
- A `IncludesCharacters` **nagybetűs** módszerrel ellenőrzi, hogy a jelszó nagybetűt tartalmaz-e.
- **Szám** a `IncludesCharacters` módszerrel, ellenőrzi, hogy a jelszó tartalmaz-e számjegyet.
- **A** szimbólum `IncludesCharacters` a módszerrel ellenőrzi, hogy a jelszó tartalmazza-e a több szimbólumkarakter egyikét.
- **PIN-kód** a `MatchesRegex` módszerrel, ellenőrzi, hogy a jelszó csak számokat tartalmaz-e.
- **AllowedAADCharacter** a `MatchesRegex` metódust, ellenőrzi, hogy a jelszó csak érvénytelen karakter tadta meg.
- **DisallowedWhitespace** a `MatchesRegex` metódus használatával, ellenőrzi, hogy a jelszó nem kezdődik, vagy végződik egy szóköz karaktert.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Miután definiálta az alapvető érvényesítéseket, kombinálhatja őket, és létrehozhat egy jelszóházirend-készletet, amelyet a házirendben használhat:

- **A SimplePassword** ellenőrzi a DisallowedWhitespace, Az AllowedAADCharacters és az IsLengthBetween8And64 karaktereket.
- **A StrongPassword** ellenőrzi a DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Az utolsó `CharacterClasses` csoport egy további predikátumkészletet futtat 3-as beállítással. `MatchAtLeast` A felhasználói jelszónak 8 és 16 karakter között kell lennie, és három karakternek kell lennie: Kisbetű, Nagybetű, Szám vagy Szimbólum.
- **CustomPassword** érvényesíti csak DisallowedWhitespace, AllowedAADCharacters. Tehát a felhasználó bármilyen jelszót megadhat bármilyen hosszúságú, amíg a karakterek érvényesek.

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

A jogcím típusában adja hozzá a **PredicateValidationReference** elemet, és adja meg az azonosítót az egyik predikátumérvényesítésként, például SimplePassword, StrongPassword vagy CustomPassword.

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

Az alábbiakban bemutatjuk, hogyan vannak rendszerezve az elemek, amikor az Azure AD B2C megjeleníti a hibaüzenetet:

![Példa predikátum és predikátumdiagramCsoport jelszó összetettségére](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Dátumtartomány konfigurálása

A **Predicates** és **predicateValidations** elemekkel a **UserInputType** minimális és maximális `DateTimeDropdown`dátumértékeit a használatával szabályozhatja. Ehhez hozzon létre egy **predikátuma** a `IsDateRange` módszerrel, és adja meg a minimális és maximális paramétereket.

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

Adjon hozzá egy **predikátumérvényesítést** a `DateRange` predikátumra való hivatkozással.

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

A jogcím típusában adja hozzá a PredicateValidationReference elemet, és adja meg az azonosítót a ( **predateValidationReference)** elemként, és adja meg az azonosítót. `CustomDateRange`

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

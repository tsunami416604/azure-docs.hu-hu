---
title: Érvényesítési technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Jogcímek ellenőrzése egy érvényesítési technikai profil használatával egy egyéni szabályzat az Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1eaf159149bb353b1cf0474aad5bc233decddc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481568"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Érvényesítési technikai profil definiálása az Azure Active Directory B2C egyéni házirendjében

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az érvényesítési technikai profil bármely protokoll, például az [Azure Active Directory](active-directory-technical-profile.md) vagy a REST [API](restful-technical-profile.md)szokásos technikai profilja. Az érvényesítési technikai profil kimeneti jogcímeket ad vissza, vagy 4xx HTTP állapotkódot ad vissza a következő adatokkal. További információ: [Hibaüzenet visszaadása](restful-technical-profile.md#returning-error-message)

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Az érvényesítési technikai profil kimeneti jogcímeinek hatóköre az érvényesítési technikai profilt [megidéző, önérvényesítő technikai profilra](self-asserted-technical-profile.md) és annak érvényesítési technikai profiljaira korlátozódik. Ha a következő vezénylési lépésben szeretné használni a kimeneti jogcímeket, adja hozzá a kimeneti jogcímeket az érvényesítési technikai profilt meghívó, önérvényesítő technikai profilhoz.

Az érvényesítési technikai profilok végrehajtása a **ValidationTechnicalProfiles** elemben megjelenő sorrendben történik. Az érvényesítési technikai profilban beállíthatja, hogy a későbbi érvényesítési technikai profilok végrehajtása folytatódjon-e, ha az érvényesítési technikai profil hibát okoz, vagy sikeres.

Az érvényesítési technikai profil feltételesen végrehajtható a **ValidationTechnicalProfile** elemben meghatározott előfeltételek alapján. Ellenőrizheti például, hogy létezik-e egy adott jogcím, vagy hogy a jogcím egyenlő-e vagy sem a megadott értékkel.

Az önérvényesítő technikai profil meghatározhatja a kimeneti jogcímek egy részének vagy egészének érvényesítéséhez használandó érvényesítési technikai profilt. A hivatkozott műszaki profil valamennyi bemeneti igényének szerepelnie kell a hivatkozó érvényesítési technikai profil kimeneti jogcímében.

> [!NOTE]
> Csak az önérvényesítő technikai profilok használhatják az érvényesítési technikai profilokat. Ha nem önérvényesítő technikai profilok kimeneti jogcímeinek érvényesítésére van szükség, fontolja meg egy további vezénylési lépés használatát a felhasználói útsorán az érvényesítésért felelős technikai profil befogadásához.

## <a name="validationtechnicalprofiles"></a>ValidtechnicalProfiles

A **ValidationTechnicalProfiles** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ValidtechnicalProfil | 1:n | A hivatkozó technikai profil kimeneti jogcímeinek egy részének vagy egészének érvényesítéséhez használandó technikai profil. |

A **ValidationTechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirendben vagy a szülőházirendben már definiált technikai profil azonosítója. |
|ContinueOnerror (Folytatási hiba)|Nem| Annak jelzése, hogy a későbbi érvényesítési technikai profilok érvényesítése folytatódjon-e, ha ez az érvényesítési technikai profil hibát jelez. Lehetséges `true` értékek: `false` vagy (alapértelmezett, további érvényesítési profilok feldolgozása leáll, és egy hiba tért vissza). |
|Folyamatos siker | Nem | Annak jelzése, hogy a későbbi érvényesítési profilok érvényesítése folytatódjon-e, ha ez az ellenőrzési technikai profil sikeres. Lehetséges `true` értékek: `false`vagy . Az alapértelmezett `true`beállítás , ami azt jelenti, hogy a további ellenőrzési profilok feldolgozása folytatódik. |

A **ValidationTechnicalProfile** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Előfeltételek | 0:1 | Azon előfeltételek listája, amelyeknek meg kell felelniük az érvényesítési technikai profil végrehajtásához. |

**Az előfeltétel** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| `Type` | Igen | Az előfeltételhez végrehajtandó ellenőrzés vagy lekérdezés típusa. Vagy `ClaimsExist` meg van adva annak biztosítására, hogy a műveleteket akkor hajtsák `ClaimEquals` végre, ha a megadott jogcímek léteznek a felhasználó aktuális jogcímkészletében, vagy meg van adva, hogy a műveleteket akkor kell végrehajtani, ha a megadott jogcím létezik, és értéke megegyezik a megadott értékkel. |
| `ExecuteActionsIf` | Igen | Azt jelzi, hogy az előfeltételben lévő műveleteket el kell-e végezni, ha a vizsgálat igaz vagy hamis. |

**Az előfeltétel** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Érték | 1:n | Az ellenőrzés által használt adatok. Ha az ellenőrzés típusa `ClaimsExist`a mező, akkor ez a mező egy ClaimTypeReferenceId azonosítót ad meg, amelyhez le szeretne kérdezni. Ha az ellenőrzés `ClaimEquals`típusa a mező, akkor ez a mező egy ClaimTypeReferenceId azonosítót ad meg, amelyhez le kell kérdezni. Míg egy másik értékelem tartalmazza az ellenőrizendő értéket.|
| Műveletek | 1:1 | A művelet, amelyet meg kell tenni, ha az előfeltétel ellenőrzése egy vezénylési lépésen belül igaz. A **művelet** értéke `SkipThisValidationTechnicalProfile`. Itt adhatja meg, hogy a társított érvényesítési technikai profilt ne hajtsa végre. |

### <a name="example"></a>Példa

A következő példa ezeket az érvényesítési technikai profilokat használja:

1. Az első érvényesítési technikai profil ellenőrzi a felhasználói hitelesítő adatokat, és nem folytatódik, ha hiba történik, például érvénytelen felhasználónév vagy rossz jelszó.
2. A következő érvényesítési technikai profil nem hajtható végre, ha a userType jogcím nem létezik, vagy ha a userType értéke `Partner`. Az érvényesítési technikai profil megpróbálja olvasni a felhasználói profilt a belső ügyfél-adatbázisból, és folytatja, ha hiba történik, például a REST API-szolgáltatás nem érhető el, vagy bármilyen belső hiba.
3. Az utolsó érvényesítési technikai profil nem hajtható végre, ha a userType jogcím nem létezik, vagy ha a userType értéke `Customer`. Az érvényesítési technikai profil megpróbálja olvasni a felhasználói profilt a belső partner adatbázisból, és folytatja, ha hiba történik, például a REST API-szolgáltatás nem érhető el, vagy bármilyen belső hiba.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```

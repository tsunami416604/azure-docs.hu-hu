---
title: Vezérlőelem-hivatkozás megjelenítése
titleSuffix: Azure AD B2C
description: Hivatkozás az Azure AD B2C kijelzővezérlőkhöz. A megjelenítési vezérlőkkel testre szabhatja az egyéni házirendekben meghatározott felhasználói utakat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188732"
---
# <a name="display-controls"></a>Vezérlők megjelenítése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **megjelenítési vezérlő** egy olyan felhasználói felületelem, amely speciális funkciókkal rendelkezik, és együttműködik az Azure Active Directory B2C (Azure AD B2C) háttérszolgáltatással. Lehetővé teszi a felhasználó számára, hogy olyan műveleteket hajtson végre az oldalon, amelyek [érvényesítési technikai profilt](validation-technical-profile.md) hívnak meg a háttérben. A kijelzővezérlők megjelennek az oldalon , és [egy önérvényesítő technikai profil](self-asserted-technical-profile.md)hivatkozik rá.

Az alábbi képen egy önérvényesítő regisztrációs oldal látható, amelykét megjelenítési vezérlővel, amelyek ellenőrzik az elsődleges és másodlagos e-mail-címet.

![Példa renderelt kijelzővezérlőre](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

 Egy [önérvényesítő technikai profil](self-asserted-technical-profile.md) [Metaadatok](self-asserted-technical-profile.md#metadata) szakaszában a hivatkozott [ContentDefinition-nek](contentdefinitions.md) a 2.0.0-s vagy újabb lapszerződés-verzióra kell `DataUri` beállítania. Példa:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Megjelenítésvezérlők definiálása

A **DisplayControl** elem a következő jellemzőket tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A kijelző vezérlőelemhez használt azonosító. Ez lehet [hivatkozni](#referencing-display-controls). |
| UserInterfaceControlType | Igen | A kijelző vezérlőjének típusa. Jelenleg támogatott az [VerificationControl](display-control-verification.md) |

A **DisplayControl** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| InputClaims (InputClaims) | 0:1 | **Az InputClaims** a felhasználótól begyűjtendő jogcímek értékének előre feltöltésére szolgál. |
| Megjelenítési jogcímek | 0:1 | **A DisplayClaims** a felhasználótól begyűjtendő jogcímek reprezentatosa. |
| Kimeneti jogcímek | 0:1 | **A OutputClaims** a **DisplayControl**ideiglenesen mentendő jogcímekre szolgál. |
| Műveletek | 0:1 | **A műveletek** az érvényesítési technikai profilok felsorolására szolgálnak az előtérben végrehajtott felhasználói műveletekhez. |

### <a name="input-claims"></a>Bemeneti jogcímek

A megjelenítési vezérlőben **az InputClaims** elemek segítségével előre feltöltheti az oldalon lévő felhasználótól begyűjtendő jogcímek értékét. Az **InputClaimsTransformations** definiálható az önérvényesítő technikai profilban, amely erre a megjelenítési vezérlőre hivatkozik.

A következő példa előre feltölti az ellenőrizendő e-mail címet a már meglévő címmel.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Jogcímek megjelenítése

A megjelenítési vezérlőtípusok mindegyikének különböző megjelenítési jogcímek, [kimeneti jogcímek](#output-claims)és [műveletek](#display-control-actions) szükséges készletét igényli.

Az [önérvényesítő technikai profilban definiált](self-asserted-technical-profile.md#display-claims) **megjelenítési jogcímekhez** hasonlóan a megjelenítési jogcímek a felhasználótól a kijelző vezérlőelemen belül begyűjtendő jogcímeket jelölik. A hivatkozott **ClaimType** elemnek meg kell adnia a **UserInputType** elemet az Azure AD B2C által támogatott felhasználói beviteli típushoz, például `TextBox` vagy `DropdownSingleSelect`. Ha egy **művelethez**megjelenítési jogcímértékre van `true` szükség, állítsa be a **Kötelező** attribútumot, hogy a felhasználó taszítson értéket az adott megjelenítési jogcímhez.

Bizonyos képernyővezérlési típusokesetében bizonyos megjelenítési jogcímek szükségesek. Az **VerificationCode** például az **VerificationControl**típusú kijelzővezérléshez szükséges. A **ControlClaimType** attribútummal megadhatja, hogy melyik DisplayClaim van kijelölve a szükséges jogcímhez. Példa:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Kimeneti jogcímek

A megjelenítési vezérlő **kimeneti jogcímeket** nem küldi el a következő vezénylési lépés. A rendszer ideiglenesen csak az aktuális megjelenítésvezérlési munkamenethez menti őket. Ezek az ideiglenes jogcímek megoszthatók ugyanazon kijelzővezérlő különböző lépései között.

Buborékfel a kimeneti jogcímek a következő vezénylési lépés, használja a **OutputClaims** a tényleges önérvényesítő technikai profil, amely hivatkozik erre a megjelenítési vezérlő.

### <a name="display-control-actions"></a>Vezérlőműveletek megjelenítése

**A műveletek** a megjelenítési vezérlő műveletek eljárások, amelyek az Azure AD B2C háttérrendszer, amikor a felhasználó egy bizonyos műveletet hajt végre az ügyféloldalon (a böngésző). Például az érvényesítések, amelyeket akkor kell végrehajtani, amikor a felhasználó kiválaszt egy gombot az oldalon.

A művelet az **érvényesítési technikai profilok**listáját határozza meg. A kijelzővezérlő megjelenítési jogcímeinek egy részének vagy egészének ellenőrzésére szolgálnak. Az érvényesítési technikai profil ellenőrzi a felhasználói bevitelt, és hibát adhat vissza a felhasználónak. Használhatja **ContinueOnError**, **ContinueOnSuccess**, és **előfeltételei** a kijelző vezérlő művelet hasonló, ahogyan ők használják [az érvényesítési technikai profilok](validation-technical-profile.md) egy önálló érvényesítési technikai profil.

A következő példa egy kódot küld e-mailben vagy SMS-ben az **mfaType** jogcím felhasználó általi kiválasztása alapján.

```XML
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Hivatkozás megjelenítési vezérlőkre

A kijelző-vezérlőkre az [önérvényesítő technikai profil](self-asserted-technical-profile.md) [megjelenítési jogra](self-asserted-technical-profile.md#display-claims) vonatkozó állításai hivatkoznak.

Példa:

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

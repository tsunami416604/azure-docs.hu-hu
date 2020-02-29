---
title: Vezérlőelem-hivatkozás megjelenítése
titleSuffix: Azure AD B2C
description: Azure AD B2C megjelenítési vezérlőkre mutató hivatkozás. A megjelenítési vezérlők használatával testreszabhatja az egyéni házirendekben definiált felhasználói útvonalakat.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188732"
---
# <a name="display-controls"></a>Vezérlőelemek megjelenítése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **megjelenítési vezérlő** egy olyan felhasználói felületi elem, amely speciális funkciókkal rendelkezik, és együttműködik a Azure Active Directory B2C (Azure ad B2C) háttér-szolgáltatással. Lehetővé teszi a felhasználó számára, hogy műveleteket hajtson végre az oldalon, amely a háttérbeli [érvényesítési technikai profilt](validation-technical-profile.md) hívja meg. A megjelenítési vezérlők az oldalon jelennek meg, és egy [önérvényesített technikai profil](self-asserted-technical-profile.md)hivatkozik rá.

Az alábbi ábrán egy önjelölt regisztrációs oldal látható, amelyben két megjelenítési vezérlőelem van, amelyek egy elsődleges és egy másodlagos e-mail-címet érvényesítenek.

![Példa megjelenített megjelenítési vezérlőelemre](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

 Az [önérvényesített műszaki profilok](self-asserted-technical-profile.md) [metaadatok](self-asserted-technical-profile.md#metadata) szakaszában a hivatkozott [ContentDefinition](contentdefinitions.md) rendelkeznie kell `DataUri` az oldal 2.0.0 vagy újabb verzióra kell beállítani. Például:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Megjelenítési vezérlők definiálása

A **DisplayControl** elem a következő attribútumokat tartalmazza:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A megjelenítési vezérlőelemhez használt azonosító. Erre [hivatkozhat](#referencing-display-controls). |
| UserInterfaceControlType | Igen | A megjelenítési vezérlőelem típusa Jelenleg támogatott a [VerificationControl](display-control-verification.md) |

A **DisplayControl** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Szabályzattípushoz | 0:1 | A **szabályzattípushoz** a felhasználó által összegyűjtött jogcímek értékének előre való feltöltésére szolgálnak. |
| DisplayClaims | 0:1 | A **DisplayClaims** a felhasználótól gyűjtött jogcímek ábrázolására szolgálnak. |
| OutputClaims | 0:1 | A **OutputClaims** a **DisplayControl**ideiglenesen menteni kívánt jogcímeket jelölik. |
| Műveletek | 0:1 | A **műveletekkel** listázhatja az ellenőrzési technikai profilokat, amelyeket a rendszer az előtér felhasználói műveleteihez hív meg. |

### <a name="input-claims"></a>Bemeneti jogcímek

A megjelenítési vezérlőkben a **szabályzattípushoz** elemek használatával előre kitöltheti a felhasználók által az oldalon gyűjtött jogcímek értékét. Bármely **InputClaimsTransformations** megadható az önérvényesített technikai profilban, amely erre a megjelenítési vezérlőelemre hivatkozik.

A következő példa előre feltölti az e-mail-címet, hogy ellenőrizni lehessen a már meglévő címmel.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Jogcímek megjelenítése

A megjelenítési vezérlők mindegyik típusának különböző megjelenítési jogcímeket, [kimeneti jogcímeket](#output-claims)és végrehajtandó [műveleteket](#display-control-actions) kell megadnia.

Az [önérvényesített technikai profilban](self-asserted-technical-profile.md#display-claims)definiált **megjelenítési jogcímek** esetében a megjelenítési jogcímek a felhasználó által a megjelenítési vezérlőn belül összegyűjtött jogcímeket jelölik. A hivatkozott **claimType** elemnek meg kell adnia a Azure ad B2C által támogatott felhasználói beviteli típus **UserInputType** elemét, például `TextBox` vagy `DropdownSingleSelect`. Ha egy **művelethez**meg kell adni egy megjelenítési jogcímet, állítsa be a **kötelező** attribútumot úgy, hogy `true`, hogy a felhasználó az adott megjelenítési jogcím értékének megadását kényszerítse.

Bizonyos megjelenítési jogcímek a megjelenítési vezérlők bizonyos típusaihoz szükségesek. Például **VerificationCode** szükséges a **VerificationControl**típusú megjelenítési vezérlőelemhez. A **ControlClaimType** attribútum használatával megtudhatja, hogy melyik DisplayClaim van kijelölve ehhez a szükséges jogcímhez. Például:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Kimeneti jogcímek

A megjelenítési vezérlő **kimeneti jogcímeit** a rendszer nem küldi el a következő előkészítési lépéshez. Ezeket a rendszer ideiglenesen csak az aktuális megjelenítési vezérlő munkamenet számára menti. Ezek az ideiglenes jogcímek megoszthatók ugyanazon megjelenítési vezérlő különböző műveletei között.

Ha ki szeretné próbálni a kimeneti jogcímeket a következő előkészítési lépésre, használja a tényleges önérvényesített technikai profil **OutputClaims** , amely erre a megjelenítési vezérlőelemre hivatkozik.

### <a name="display-control-actions"></a>Vezérlési műveletek megjelenítése

A megjelenítési vezérlők **műveletei** olyan eljárások, amelyek a Azure ad B2C háttérben történnek, amikor egy felhasználó egy bizonyos műveletet hajt végre az ügyfél oldalán (a böngészőben). Például az a művelet, amelyet akkor kell végrehajtania, amikor a felhasználó kiválaszt egy gombot az oldalon.

Egy művelet meghatározza az **érvényesítési műszaki profilok**listáját. Ezek a megjelenítési vezérlők egy vagy több megjelenítési jogcíme érvényesítésére szolgálnak. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott adatokat, és hibát jelez a felhasználó számára. A **ContinueOnError**, a **ContinueOnSuccess**és az **előfeltételeket** a Display Control művelethez hasonlóan használhatja, mint ahogyan azokat az [érvényesítési technikai](validation-technical-profile.md) profilokban használják egy önjelölt technikai profilban.

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

## <a name="referencing-display-controls"></a>A megjelenítési vezérlőkre hivatkozik

A megjelenítési vezérlőkre az [önérvényesített technikai profil](self-asserted-technical-profile.md) [megjelenítési jogcímeiben](self-asserted-technical-profile.md#display-claims) hivatkozunk.

Például:

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

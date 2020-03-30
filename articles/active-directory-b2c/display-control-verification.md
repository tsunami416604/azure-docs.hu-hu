---
title: Jogcímek ellenőrzése megjelenítési vezérlőkkel
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan használhatja az Azure AD B2C megjelenítési vezérlőket az egyéni szabályzatok által biztosított felhasználói utak jogcímének ellenőrzéséhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188783"
---
# <a name="verification-display-control"></a>Ellenőrző kijelző-vezérlés

Ellenőrző [kijelző-vezérlő](display-controls.md) vel ellenőrizhet egy jogcímet, például egy e-mail címet vagy telefonszámot, amelyet a felhasználónak küldött ellenőrző kóddal együtt.

## <a name="verificationcontrol-actions"></a>VerificationControl műveletek

Az ellenőrző kijelző vezérlése két lépésből áll (műveletek):

1. Kérjen egy célt a felhasználótól, például egy e-mail címet vagy telefonszámot, amelyre az ellenőrző kódot el kell küldeni. Amikor a felhasználó a **Kód küldése** gombot választja, a rendszer végrehajtja az ellenőrző kijelző **vezérlőJének SendCode műveletét.** A **SendCode művelet** létrehoz egy kódot, létrehozza az elküldendő tartalmat, és elküldi azt a felhasználónak. A cím értéke előre kitölthető, és második tényezős hitelesítésként szolgálhat.

    ![Példa kódküldési műveletre](media/display-control-verification/display-control-verification-email-action-01.png)

1. A kód elküldése után a felhasználó beolvassa az üzenetet, beírja a vezérlőbe az ellenőrző kódot, és kiválasztja a **Kód ellenőrzése lehetőséget.** A **Kód ellenőrzése**lehetőség kiválasztásával a **VerifyCode művelet** végrehajtásra kerül a címhez társított kód ellenőrzéséhez. Ha a felhasználó az **Új kód küldése**lehetőséget választja, az első művelet ismét végrehajtásra kerül.

    ![Példa lap a kódművelet ellenőrzésére](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificationControl szükséges elemek

Az **VerificationControl-nak** a következő elemeket kell tartalmaznia:

- Az típusa `DisplayControl` a `VerificationControl`.
- `DisplayClaims`
  - **Küldés** - Egy vagy több jogcím, amely meghatározza, hogy hová kell küldeni az ellenőrző kódot. Például *e-mail vagy* *országkód* és *telefonszám.*
  - **Ellenőrző kód** – A felhasználó által a kód elküldése után megadott ellenőrzőkód-jogcím. Ezt az igényt szükség szerint `ControlClaimType` kell beállítani, `VerificationCode`és a beállítását a .
- Kimeneti jogcím (nem kötelező) vissza kell küldeni a saját érvényesítésű oldalra, miután a felhasználó befejezte az ellenőrzési folyamatot. Például *e-mail vagy* *országkód* és *telefonszám.* Az önérvényesítő technikai profil a jogcímek az adatok megőrzése, vagy buborék fel a kimeneti jogcímek a következő vezénylési lépés.
- Két `Action`s a következő nevekkel:
  - **SendCode** - Kódot küld a felhasználónak. Ez a művelet általában két ellenőrzési technikai profilt tartalmaz, amelyek kód generálását és elküldését teszik.
  - **VerifyCode** - Ellenőrzi a kódot. Ez a művelet általában egyetlen ellenőrzési technikai profilt tartalmaz.

Az alábbi példában egy **e-mail** szövegdoboz jelenik meg az oldalon. Amikor a felhasználó megadja az e-mail címét, és kiválasztja a **SendCode**lehetőséget, a **SendCode** művelet az Azure AD B2C háttérrendszerében aktiválódik.

Ezután a felhasználó beírja az **verificationCode-ot,** és kiválasztja a **VerifyCode lehetőséget** a **VerifyCode** művelet aktiválásához a háttérben. Ha az összes ellenőrzés sikeres, az **VerificationControl** teljesnek minősül, és a felhasználó folytathatja a következő lépéssel.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

---
title: B2B együttműködési API és testreszabás – Azure Active Directory
description: Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f62ece12732c45037bcad328eec162bebb4e6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387201"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B együttműködési API és testreszabás

Sok ügyfelünk azt közölte nekünk, hogy a Meghívási folyamatot testre szeretné szabni a szervezeteknek legmegfelelőbb módon. Az API-val csak ezt teheti meg. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>A meghívó API képességei

Az API a következő képességeket kínálja:

1. Külső felhasználó meghívása *bármely* e-mail-címmel.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Szabja testre a felhasználókat, hogy a meghívót elfogadják.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Válassza ki, hogy elküldje a szabványos meghívó e-mailt nekünk

    ```
    "sendInvitationMessage": true
    ```

   egy olyan üzenettel, amely testre szabható a címzett számára

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. És válassza ki a CC: olyan személyeket, akiket szeretne megtartani a hurokban, hogy meghívja ezt a közreműködőt.

5. Vagy teljes mértékben testreszabhatja a meghívót és a bevezetési munkafolyamatot úgy, hogy nem küld értesítést az Azure AD-n keresztül.

    ```
    "sendInvitationMessage": false
    ```

   Ebben az esetben olyan beváltási URL-címet kap vissza az API-ból, amelyet beágyazhat egy e-mail-sablonba, IM-re vagy más, tetszés szerinti terjesztési módszerbe.

6. Végül, ha Ön rendszergazda, meghívhatja a felhasználót tagként.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Engedélyezési modell

Az API-t a következő engedélyezési módokon lehet futtatni:

### <a name="app--user-mode"></a>Alkalmazás + felhasználói mód

Ebben a módban az API-t használó személyeknek az engedélyekkel kell rendelkezniük a B2B-meghívások létrehozásához.

### <a name="app-only-mode"></a>Csak alkalmazás mód

Csak az alkalmazás kontextusában az alkalmazásnak szüksége van a felhasználó. INVITE. All hatókörre a meghívás sikerességéhez.

További információkért lásd:https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

A PowerShell használatával egyszerűen hozzáadhat és meghívhat külső felhasználókat a szervezet számára. Meghívás létrehozása a parancsmag használatával:

```powershell
New-AzureADMSInvitation
```

A következő lehetőségek közül választhat:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Meghívás állapota

Miután meghívót küldött egy külső felhasználótól, a **Get-AzureADUser** parancsmag segítségével megtekintheti, hogy elfogadták-e. A Get-AzureADUser következő tulajdonságai vannak feltöltve, amikor egy külső felhasználó elküld egy meghívót:

* A **UserState** jelzi, hogy a meghívás **PendingAcceptance** vagy **elfogadva**van-e.
* A **UserStateChangedOn** a **UserState** tulajdonság legutóbbi módosításának időbélyegét jeleníti meg.

A **Filter (szűrés** ) lehetőséggel **UserState**alapján szűrheti az eredményeket. Az alábbi példa azt mutatja be, hogyan szűrheti az eredményeket, hogy csak a függőben lévő meghívóval rendelkező felhasználók jelenjenek meg. A példa a **Format-List** kapcsolót is megjeleníti, amely lehetővé teszi a megjelenítendő tulajdonságok megadását. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Győződjön meg arról, hogy a AzureAD PowerShell-modul vagy a AzureADPreview PowerShell-modul legújabb verziója van telepítve. 

## <a name="see-also"></a>Lásd még

Tekintse meg a Meghívási API-referenciát a alkalmazásban [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) .

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [A B2B együttműködés Meghívási e-mail elemei](invitation-email-elements.md)
- [VÁLLALATKÖZI együttműködés meghívásának beváltása](redemption-experience.md)
- [B2B Collaboration-felhasználók hozzáadása meghívás nélkül](add-user-without-invite.md)
---
title: B2B együttműködési API és testreszabás – Azure Active Directory
description: Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a090ee3f9588ff6bff01e12db469bf04407a7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263464"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Az Azure Active Directory B2B együttműködési API-ja és testreszabása

Sok ügyfelünk mondta el nekünk, hogy a meghívási folyamatot úgy szeretné testreszabni, hogy az a legjobban működjön a szervezeteik számára. Api-nkkal pontosan ezt teheti. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>A meghívó API képességei

Az API a következő képességeket kínálja:

1. Külső felhasználó meghívása *bármilyen* e-mail címmel.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Testreszabhatja, hogy a felhasználók hol landoljanak, miután elfogadták a meghívást.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Válassza ki, hogy küldje el a szokásos meghívó e-mail rajtunk keresztül

    ```
    "sendInvitationMessage": true
    ```

   a címzettnek küldött üzenettel, amelyet testre szabhatunk

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. És válassza a cc: azokat az embereket, akiket folyamatosan szeretne beadni a meghívóhoz, az együttműködőt.

5. Vagy teljesen testreszabhatja a meghívó- és bevezetési munkafolyamatot úgy, hogy nem küld értesítéseket az Azure AD-n keresztül.

    ```
    "sendInvitationMessage": false
    ```

   Ebben az esetben visszakap egy beváltási URL-t az API-ból, amelyet beágyazhat egy e-mail sablonba, iM-be vagy más, ön által választott terjesztési módszerbe.

6. Végül, ha egy admin, akkor választhat, hogy meghívja a felhasználótagot.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Engedélyezési modell

Az API a következő engedélyezési módokban futtatható:

### <a name="app--user-mode"></a>Alkalmazás + Felhasználói mód

Ebben a módban bárki is használja az API-t, rendelkeznie kell a B2B-meghívók létrehozásához szükséges engedélyekkel.

### <a name="app-only-mode"></a>Csak alkalmazás mód

Csak alkalmazáskörnyezetben az alkalmazásnak szüksége van a User.Invite.All hatókörre a meghívás sikeressedéséhez.

További információ:https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

A PowerShell segítségével külső felhasználókat vehet fel és hívhat meg egy szervezetbe. Hozzon létre meghívót a parancsmag használatával:

```powershell
New-AzureADMSInvitation
```

A következő beállításokat használhatja:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -Meghívó küldése
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Meghívó állapota

Miután meghívót küldött egy külső felhasználónak, a **Get-AzureADUser** parancsmag használatával ellenőrizheti, hogy elfogadták-e azt. A Get-AzureADUser következő tulajdonságai kerülnek feltöltésre, amikor egy külső felhasználó meghívót küld:

* **UserState** jelzi, hogy a meghívás **Függőben Elfogadás** vagy **Elfogadva**.
* **UserStateChangedOn** a **UserState** tulajdonság legutóbbi módosításának időbélyegét jeleníti meg.

A **Szűrő** beállítással az eredményeket **UserState**szerint szűrheti. Az alábbi példa bemutatja, hogyan szűrheti az eredményeket, hogy csak azok a felhasználók jelenjenek meg, akiknek függőben lévő meghívója van. A példa a **Formátumlista** beállítást is megjeleníti, amely lehetővé teszi a megjelenítendő tulajdonságok megadását. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Győződjön meg arról, hogy az AzureAD PowerShell-modul vagy az AzureADPreview PowerShell-modul legújabb verzióját használja. 

## <a name="see-also"></a>Lásd még

Nézze meg a meghívó [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)API hivatkozását a ban.

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [A B2B együttműködési meghívó e-mail elemei](invitation-email-elements.md)
- [B2B együttműködési meghívás visszaváltás](redemption-experience.md)
- [B2B együttműködési felhasználók hozzáadása meghívás nélkül](add-user-without-invite.md)
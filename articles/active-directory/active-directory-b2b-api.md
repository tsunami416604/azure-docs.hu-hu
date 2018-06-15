---
title: Az Azure Active Directory B2B együttműködés API és a Testreszabás |} Microsoft Docs
description: Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/11/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f1bd93ac2ef6aa75e07eeec3e3cb2222b6febc1c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932370"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Az Azure Active Directory B2B együttműködés API és a Testreszabás

Adja meg, hogy szeretné-e a meghívó folyamat oly módon, amely a legjobban a vállalatuk testreszabása sok ügyfél volt. Az API-val most, hogy elvégezhető. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Az API-t meghívó képességei
Az API-t a következő szolgáltatásokat biztosítja:

1. A külső felhasználó meghívása *bármely* e-mail címét.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Ha azt szeretné, hogy a felhasználók elfogadják a meghívó után megnyílik a testreszabása

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Válassza ki a szabványos meghívó levél velünk keresztül

    ```
    "sendInvitationMessage": true
    ```

  a címzett testre szabható üzenetet

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Konzisztencia-ellenőrzési és: személyek meg szeretné tartani a fióknevet a közreműködő kapcsolatos ciklusban.

5. Vagy teljesen testre szabhatja a meghívó és a bevezetési munkafolyamat megadásával nem az Azure AD keresztül értesítések küldéséhez.

    ```
    "sendInvitationMessage": false
    ```

  Ebben az esetben vissza egy érvényesítési URL-címet a API, amely beágyazása e-mail sablont, IM vagy más elosztási módszer az Ön által választott.

6. Végül ha Ön rendszergazda, ha szeretné, hívhat meg a felhasználó tagja.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Engedélyezési modellt
Az API-t a következő hitelesítési módok is futtatható:

### <a name="app--user-mode"></a>Alkalmazás + felhasználói mód
Ebben a módban személy, aki segítségével API igényeinek a engedélye B2B meghívókat kell létrehozni.

### <a name="app-only-mode"></a>Egyetlen módja
Alkalmazás csak a környezetben a sikeres meghívó User.Invite.All hatóköre kell az alkalmazást.

További információkért tekintse meg: https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Már lehetséges a PowerShell segítségével szeretne hozzáadni egy szervezet külső felhasználók könnyen meghívása. A parancsmaggal meghívót létrehozni:

```
New-AzureADMSInvitation
```

A következő beállításokat is használhatja:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Is megtekintheti a meghívó API-referencia [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [A B2B együttműködés meghívó e-mail elemei](active-directory-b2b-invitation-email.md)
- [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md)
- [Adja hozzá a B2B együttműködés felhasználók nélkül](active-directory-b2b-add-user-without-invite.md)


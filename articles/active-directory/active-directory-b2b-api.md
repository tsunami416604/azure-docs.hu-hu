---
title: "Az Azure Active Directory B2B együttműködés API és a Testreszabás |} Microsoft Docs"
description: "Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/11/2017
ms.author: sasubram
ms.openlocfilehash: 9e1dcd38e7c05a234e4b8d7a6190770d813a227f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Az Azure Active Directory B2B együttműködés API és a Testreszabás

Adja meg, hogy szeretné-e a meghívó folyamat oly módon, amely a legjobban a vállalatuk testreszabása sok ügyfél volt. Az API-val most, hogy elvégezhető. [https://Developer.microsoft.com/Graph/docs/API-Reference/V1.0/Resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

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
Alkalmazás csak a környezetben a sikeres a meghívót a User.ReadWrite.All vagy Directory.ReadWrite.All hatókört kell az alkalmazást.

További információkért lásd: https://graph.microsoft.io/docs/authorization/permission_scopes


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

Is megtekintheti a meghívó API-hivatkozás [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hogyan rendszergazdák Azure Active Directory B2B együttműködés felhasználók hozzá?](active-directory-b2b-admin-add-users.md)
* [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](active-directory-b2b-iw-add-users.md)
* [A B2B együttműködés meghívó e-mail elemei](active-directory-b2b-invitation-email.md)
* [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md)
* [Az Azure AD B2B együttműködés licencelés](active-directory-b2b-licensing.md)
* [Hibaelhárítás az Azure Active Directory B2B együttműködés](active-directory-b2b-troubleshooting.md)
* [Az Azure Active Directory B2B együttműködés gyakori kérdések (GYIK)](active-directory-b2b-faq.md)
* [Többtényezős hitelesítés a B2B-együttműködés felhasználói számára](active-directory-b2b-mfa-instructions.md)
* [Adja hozzá a B2B együttműködés felhasználók nélkül](active-directory-b2b-add-user-without-invite.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)

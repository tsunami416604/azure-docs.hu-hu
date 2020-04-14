---
title: Belső felhasználók meghívása a B2B együttműködésre - Azure AD
description: Ha belső felhasználói fiókkal rendelkezik a partnerek, forgalmazók, szállítók, szállítók és más vendégek számára, az Azure AD B2B-együttműködésre válthat, ha meghívja őket, hogy jelentkezzenek be saját külső hitelesítő adataikkal vagy bejelentkezéssel. Használja a PowerShellt vagy a Microsoft Graph meghívási API-t.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 097de14a3451e8d352dceb17436ae8423aa06533
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265940"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Belső felhasználók meghívása a B2B együttműködésre

|     |
| --- |
| A belső felhasználók meghívása a B2B együttműködés használatára az Azure Active Directory nyilvános előzetes verzióbeli szolgáltatása. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). |
|     |

Az Azure AD B2B együttműködés rendelkezésre állása előtt a szervezetek együttműködhetnek a forgalmazókkal, a beszállítókkal, a szállítókkal és más vendégfelhasználókkal a belső hitelesítő adatok beállításával. Ha ilyen belső vendégfelhasználókkal rendelkezik, megkérheti őket a B2B-együttműködés használatára, hogy kihasználhassa az Azure AD B2B előnyeit. A B2B vendégfelhasználók saját identitásuk és hitelesítő adataik segítségével jelentkezhetnek be, és önnek nem kell jelszót fenntartania vagy kezelnie a fiók életciklusát.

Meghívó küldése egy meglévő belső fiókba lehetővé teszi a felhasználó objektumazonosítójának, upn-, csoporttagságának és alkalmazás-hozzárendelésének megőrzését. Nem kell manuálisan törölnie és újra meghívnia a felhasználót, vagy újra hozzárendelnie az erőforrásokat. A felhasználó meghívásához a meghívó API-t fogja használni a belső felhasználói objektum és a vendégfelhasználó e-mail-címének és a meghívónak a továbbadására. Amikor a felhasználó elfogadja a meghívást, a B2B szolgáltatás a meglévő belső felhasználói objektumot B2B-felhasználóra módosítja. A jövőben a felhasználónak be kell jelentkeznie a felhőalapú erőforrás-szolgáltatásokba a B2B hitelesítő adataival. Továbbra is használhatják a belső hitelesítő adatokat a helyszíni erőforrások eléréséhez, de ezt megakadályozhatja a belső fiók jelszavának alaphelyzetbe állításával vagy módosításával.

> [!NOTE]
> A meghívás egyirányú. Belső felhasználókat meghívhat a B2B-együttműködés használatára, de a B2B hitelesítő adatok at nem távolíthatja el, miután hozzáadták őket. Ha a felhasználót csak belső felhasználóra szeretné visszaváltani, törölnie kell a felhasználói objektumot, és létre kell hoznia egy újat.

A nyilvános előzetes verzióban a jelen cikkben ismertetett módszer, amely belső felhasználókat hív meg a B2B-együttműködésre, a következő esetekben nem használható:

- A belső felhasználóhoz már hozzá van rendelve Exchange-licenc.
- A felhasználó olyan tartományból származik, amely a címtárban közvetlen összevonásra van beállítva.
- A belső felhasználó csak felhőalapú fiók, és a fő fiók nem az Azure AD-ben.

Ezekben az esetekben, ha a belső felhasználót B2B-felhasználóra kell módosítani, törölnie kell a belső fiókot, és meghívót kell küldenie a felhasználónak a B2B együttműködésre.

**Helyszíni szinkronizált felhasználók:** A helyszíni és a felhő közötti szinkronizálásra kijelölt felhasználói fiókok esetében a helyszíni címtár marad a hitelesítésforrás, miután felkérték őket a B2B-együttműködés használatára. A helyszíni fiókon végzett módosítások szinkronizálódnak a felhőfiókkal, beleértve a fiók letiltását vagy törlését. Ezért nem akadályozhatja meg, hogy a felhasználó bejelentkezik a helyszíni fiókjukba, miközben megtartja a felhőbeli fiókját egyszerűen a helyszíni fiók törlésével. Ehelyett beállíthatja a helyszíni fiók jelszavát egy véletlenszerű GUID vagy más ismeretlen értékre.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Hogyan lehet meghívni a belső felhasználókat a B2B együttműködésre?

A PowerShell vagy a meghívó API segítségével b2B meghívót küldhet a belső felhasználónak. Győződjön meg arról, hogy a meghívóhoz használni kívánt e-mail cím a belső felhasználói objektum külső e-mail címeként van beállítva.

- Csak felhőalapú felhasználók esetén használja a User.OtherMails tulajdonságban található e-mail címet a meghívóhoz.
- Egy helyszíni szinkronizált felhasználó esetén a User.Mail tulajdonság értékét kell használnia a meghívóhoz.
- A felhasználó Mail tulajdonságában lévő tartománynak meg kell egyeznie azzal a fiókkal, amelyet a bejelentkezéshez használnak. Ellenkező esetben egyes szolgáltatások, például a Teams, nem tudják hitelesíteni a felhasználót.

Alapértelmezés szerint a meghívó e-mailt küld a felhasználónak, amely tudatja velük, hogy meghívták őket, de letilthatja ezt az e-mailt, és elküldheti a sajátját.

> [!NOTE]
> Saját e-mail vagy más kommunikáció küldéséhez használhatja a New-AzureADMSInvitation with -SendInvitationMessage:$false segítségével a felhasználókat csendben meghívja, majd elküldheti saját e-mail üzenetét a konvertált felhasználónak. Lásd: [Azure AD B2B együttműködési API és testreszabás.](customize-invitation-api.md)

## <a name="use-powershell-to-send-a-b2b-invitation"></a>B2B-meghívó küldése a PowerShell használatával

A következő paranccsal meghívhatja a felhasználót a B2B együttműködésre:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>B2B-meghívó küldése a meghívó API-val

Az alábbi minta bemutatja, hogyan hívja meg a meghívásos API-t egy belső felhasználó meghívására B2B felhasználóként.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>"",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>""
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {"id": "<<ID for the user you want to convert>>"}
}
```

Az API-ra adott válasz ugyanaz a válasz, amelyet akkor kap, amikor új vendégfelhasználót hív meg a címtárba.

## <a name="next-steps"></a>További lépések

- [B2B együttműködési meghívás visszaváltás](redemption-experience.md)
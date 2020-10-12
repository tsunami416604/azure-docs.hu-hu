---
title: Belső felhasználók meghívása a B2B-együttműködésbe – Azure AD
description: Ha rendelkezik belső felhasználói fiókkal a partnerek, a terjesztők, a szállítók, a szállítók és az egyéb vendégek számára, az Azure AD B2B együttműködésre való meghívásával meghívja őket, hogy jelentkezzenek be a saját külső hitelesítő adataikkal vagy bejelentkezéssel. Használja a PowerShellt vagy a Microsoft Graph meghívó API-t.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03c2c55988df04cc45ef4a1d66d959513c1626d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909323"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Belső felhasználók meghívása B2B együttműködésre

> [!NOTE]
> A belső felhasználók meghívása a B2B együttműködés használatára a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure AD B2B-együttműködés rendelkezésre állása előtt a szervezetek a belső hitelesítő adatok beállításával együttműködhetnek a forgalmazókkal, a szállítókkal, a szállítókkal és a többi vendég felhasználóval. Ha ilyen belső vendég felhasználókkal rendelkezik, meghívhatja őket a B2B-együttműködés használatára, így kihasználhatja az Azure AD B2B előnyeinek előnyeit is. A B2B vendég felhasználói saját identitásuk és hitelesítő adataik használatával jelentkezhetnek be, és nem kell megtartania a jelszavakat, és nem kell kezelnie a fiókok életciklusát.

Egy meglévő belső fiókra irányuló Meghívás küldése lehetővé teszi a felhasználó objektumazonosító, UPN, csoporttagság és alkalmazás-hozzárendelések megőrzését. Nem kell manuálisan törölnie és újból meghívnia a felhasználót, vagy újra hozzá kell rendelnie az erőforrásokat. A felhasználó meghívásához a meghívó API-val át kell adni a belső felhasználói objektumot és a vendég felhasználó e-mail-címét a meghívóval együtt. Ha a felhasználó elfogadja a meghívót, a VÁLLALATKÖZI szolgáltatás megváltoztatja a meglévő belső felhasználói objektumot egy B2B-felhasználóhoz. A jövőben a felhasználónak a B2B hitelesítő adataival kell bejelentkeznie a felhőalapú erőforrások szolgáltatásaiba. Továbbra is használhatják a belső hitelesítő adataikat a helyszíni erőforrásokhoz való hozzáféréshez, de ezt megakadályozhatja a belső fiók jelszavának alaphelyzetbe állításával vagy módosításával.

> [!NOTE]
> A meghívás egyirányú. Meghívhatja a belső felhasználókat a B2B együttműködés használatára, de a hozzáadásuk után nem távolíthatja el a B2B hitelesítő adatokat. Ha a felhasználót csak belső felhasználónak szeretné módosítani, törölnie kell a felhasználói objektumot, és létre kell hoznia egy újat.

Nyilvános előzetes verzióban a belső felhasználók VÁLLALATKÖZI együttműködésre való meghívásához a jelen cikkben ismertetett módszer nem használható ezekben a példányokban:

- A belső felhasználóhoz már hozzá van rendelve egy Exchange-licenc.
- A felhasználó olyan tartományból származik, amely közvetlen összevonás beállítására van beállítva a címtárban.
- A belső felhasználó csak felhőalapú fiók, a fő fiókjuk pedig nem az Azure AD-ben.

Ezekben a példányokban, ha a belső felhasználót egy B2B-felhasználóra kell módosítani, törölje a belső fiókot, és küldje el a felhasználónak a B2B-együttműködésre vonatkozó meghívót.

Helyszíni **szinkronizált felhasználók**: a helyszíni és a felhő között szinkronizált felhasználói fiókok esetében a helyszíni címtár a szolgáltató forrása marad, miután meghívást kapnak a B2B együttműködés használatára. A helyszíni fiók módosításai szinkronizálva lesznek a Felhőbeli fiókkal, beleértve a fiók letiltását vagy törlését is. Ezért nem akadályozhatja meg, hogy a felhasználó bejelentkezzen a helyszíni fiókjába, miközben a Felhőbeli fiókját egyszerűen törli a helyszíni fiókból. Ehelyett beállíthatja a helyszíni fiók jelszavát véletlenszerű GUID azonosítóra vagy más ismeretlen értékre.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Belső felhasználók meghívása B2B-együttműködésre

A PowerShell vagy a meghívó API használatával VÁLLALATKÖZI meghívást küldhet a belső felhasználónak. Győződjön meg arról, hogy a meghívóhoz használni kívánt e-mail-cím a belső felhasználói objektum külső e-mail-címe.

- Csak felhőalapú felhasználó esetén használja a meghívóhoz tartozó user. OtherMails tulajdonságban található e-mail-címet.
- Helyszíni szinkronizált felhasználó esetén a meghívóhoz tartozó user. mail tulajdonság értékét kell használnia.
- A felhasználó mail tulajdonságában szereplő tartománynak meg kell egyeznie a bejelentkezéshez használt fiókkal. Ellenkező esetben előfordulhat, hogy egyes szolgáltatások, például a csapatok nem fogják tudni hitelesíteni a felhasználót.

Alapértelmezés szerint a meghívás elküldi a felhasználónak egy e-mailt, amely arról tájékoztatja őket, hogy meghívást kaptak, de ezt az e-mailt elhagyhatja, és elküldheti saját helyette.

> [!NOTE]
> Saját e-mail-cím vagy más kommunikáció elküldéséhez használhatja a New-AzureADMSInvitation with-SendInvitationMessage: $falset a felhasználók csendes meghívásához, majd küldje el a saját e-mail-üzenetét az átalakított felhasználónak. Lásd: [Az Azure ad B2B együttműködési API és a Testreszabás](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>B2B-Meghívás küldése a PowerShell használatával

A következő parancs használatával meghívhatja a felhasználót a B2B együttműködésre:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>B2B-Meghívás küldése a meghívót használó API-val

Az alábbi minta azt szemlélteti, hogyan hívhatja meg a meghívó API-t egy belső felhasználó B2B-felhasználóként való meghívásához.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Az API-ra adott válasz ugyanazt a választ kapja, amikor új vendég felhasználót hív meg a címtárban.

## <a name="next-steps"></a>Következő lépések

- [VÁLLALATKÖZI együttműködés meghívásának beváltása](redemption-experience.md)

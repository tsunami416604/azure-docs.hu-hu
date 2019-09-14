---
title: B2B-együttműködés hibaelhárítása – Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-együttműködés gyakori problémáinak elhárítása
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: v-miegge
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: f91ddee8668316df69c98ed14fbcabcb06b6da82
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983394"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B-együttműködés hibaelhárítása

Íme néhány jogorvoslat az Azure Active Directory (Azure AD) B2B-együttműködés gyakori problémáira.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Hozzáadtam egy külső felhasználót, de nem látják őket a globális címjegyzékben vagy a People Picker

Azokban az esetekben, amikor a külső felhasználók nincsenek kitöltve a listában, az objektum eltarthat néhány percig.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>A B2B vendég felhasználó nem jelenik meg a SharePoint Online-ban/OneDrive People Picker

A meglévő vendég felhasználók keresésének lehetősége a SharePoint Online-ban (Spongya) az emberek választója alapértelmezés szerint ki van kapcsolva a régi viselkedésnek megfelelően.

Ezt a funkciót a bérlői és a webhelycsoport szintjén a "ShowPeoplePickerSuggestionsForGuestUsers" beállítás használatával engedélyezheti. A szolgáltatást a set-SPOTenant és a set-SPOSite parancsmagok segítségével állíthatja be, amelyek lehetővé teszik a tagok számára a címtárban lévő összes vendég felhasználó keresését. A bérlői hatókör változásai nincsenek hatással a már kiépített Spongya-webhelyekre.

## <a name="invitations-have-been-disabled-for-directory"></a>A meghívások le lettek tiltva a címtárban

Ha értesítést kap arról, hogy nincs engedélye a felhasználók meghívására, ellenőrizze, hogy a felhasználói fiókja jogosult-e külső felhasználók meghívására Azure Active Directory > felhasználói beállítások > külső felhasználók számára a külső együttműködési beállítások kezelése >.

![A külső felhasználók beállításait ábrázoló képernyőfelvétel](media/troubleshoot/external-user-settings.png)

Ha nemrég módosította ezeket a beállításokat, vagy hozzárendelte a vendég meghívó szerepkört egy felhasználóhoz, előfordulhat, hogy a módosítások érvénybe léptetéséhez 15-60 perces késleltetés szükséges.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>A meghívott felhasználó hibaüzenetet kap a beváltáskor

Gyakori hibák a következők:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>A meghívó rendszergazdája nem engedélyezte, hogy a EmailVerified-felhasználók a bérlőn jöjjenek létre

Ha olyan felhasználókat hívja meg, akik szervezete Azure Active Directory használ, de az adott felhasználó fiókja nem létezik (például a felhasználó nem létezik az Azure AD-contoso.com). Előfordulhat, hogy a contoso.com rendszergazdája olyan házirendet tartalmaz, amely megakadályozza a felhasználók létrehozását. A felhasználónak ellenőriznie kell a rendszergazdát annak megállapításához, hogy a külső felhasználók engedélyezettek-e. Előfordulhat, hogy a külső felhasználó rendszergazdájának engedélyeznie kell az e-mailek ellenőrzött felhasználóit a tartományban (lásd ezt a [cikket](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) az e-mailek ellenőrzött felhasználóinak engedélyezéséről).

![Hiba történt a bérlő nem engedélyezi az e-mailek ellenőrzött felhasználóinak használatát](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>A külső felhasználó már nem létezik egy összevont tartományban

Ha összevonási hitelesítést használ, és a felhasználó még nem létezik a Azure Active Directoryban, a felhasználó nem hívható meg.

A probléma megoldásához a külső felhasználó rendszergazdájának szinkronizálnia kell a felhasználó fiókját a Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hogyan működik a\#"", amely nem általában érvényes karakter, szinkronizáljon az Azure ad-vel?

a\#(z) "" az Azure ad B2B Collaboration vagy a külső felhasználók számára az UPN-ben foglalt karakter, mert a meghívott@fabrikam.onmicrosoft.comfiók user@contoso.com user_contoso válik. com # ext #. \# Ezért a helyszíni UPN-ből érkező egyszerű felhasználónevek nem jelentkezhetnek be a Azure Portalba. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Hibaüzenet jelenik meg, amikor külső felhasználókat veszek fel egy szinkronizált csoportba

A külső felhasználók csak a "hozzárendelt" vagy "biztonsági" csoportokba vehetők fel, és nem a helyszínen elsajátított csoportokba.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>A külső felhasználó nem kapott e-mailt a beváltáshoz

A meghívás az INTERNETSZOLGÁLTATÓval vagy a levélszemét szűrővel ellenőrizze, hogy a következő címek engedélyezettek-e:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Megfigyelhető, hogy az egyéni üzenet nem jelenik meg a Meghívási üzenetekben időnként

Az adatvédelmi törvények betartása érdekében az API-k nem tartalmaznak egyéni üzeneteket az e-mail-meghívóban a következő esetekben:

- A meghívó nem rendelkezik e-mail-címmel a meghívó bérlőben
- Ha egy appservice-résztvevő elküldi a meghívót

Ha ez a forgatókönyv fontos Önnek, letilthatja az API-meghívói e-mail-címét, és elküldheti azt az Ön által választott e-mail mechanizmuson keresztül. Forduljon a szervezet jogi tanácsadásához, és győződjön meg arról, hogy az Ön által küldött e-mailek is megfelelnek az adatvédelmi törvényeknek.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>"AADSTS65005" hibaüzenet jelenik meg, amikor megpróbál bejelentkezni egy Azure-erőforrásba

A vendég fiókkal rendelkező felhasználók nem jelentkezhetnek be, és a következő hibaüzenetet kapják:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

A felhasználó rendelkezik Azure-beli felhasználói fiókkal, és olyan vírusos bérlő, aki el lett hagyva vagy nem felügyelt. Emellett a bérlő nem rendelkezik globális vagy vállalati rendszergazdákkal.

A probléma megoldásához át kell vennie az elhagyott bérlőt. Tekintse át a nem [felügyelt címtár átvétele rendszergazdaként Azure Active Directory-ben](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)című témakört. Az internetre irányuló DNS-t a szóban forgó tartományi utótaghoz is el kell érni ahhoz, hogy közvetlen bizonyítékot szolgáltasson a névtér irányításához. Ha a bérlőt visszaküldi a felügyelt állapotba, beszéljen az ügyféllel, hogy a felhasználók és az ellenőrzött tartománynév a legjobb megoldás-e a szervezet számára.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Egy igény szerinti vagy "vírusos" Bérlővel rendelkező vendég felhasználó nem tudja alaphelyzetbe állítani a jelszavát

Ha az identitás bérlője egy igény szerinti (JIT) vagy vírusos bérlő (azaz egy különálló, nem felügyelt Azure-bérlő), csak a vendég felhasználó állíthatja alaphelyzetbe a jelszavát. Néha a szervezet [átveszi az olyan vírusos bérlők felügyeletét](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) , amelyek akkor jönnek létre, amikor az alkalmazottak a munkahelyi e-mail-címeiket használják a szolgáltatások regisztrálására. Miután a szervezet átvesz egy vírusos bérlőt, csak az adott szervezet rendszergazdája állíthatja alaphelyzetbe a felhasználó jelszavát, vagy engedélyezheti a SSPR. Ha szükséges, a meghívó szervezetnél távolítsa el a vendég felhasználói fiókot a címtárból, és küldje el újra a meghívót.

## <a name="next-steps"></a>További lépések

[Támogatás kérése B2B-együttműködéshez](get-support.md)

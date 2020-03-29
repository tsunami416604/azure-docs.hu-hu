---
title: B2B-együttműködés hibaelhárítása - Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory B2B-együttműködéssel kapcsolatos gyakori problémák megoldása
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 03/19/2020
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f8bafb04d0a5d9c6d25a7ed7e155888d492e9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050783"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Az Azure Active Directory B2B együttműködéssel kapcsolatos hibaelhárítása

Íme néhány megoldás az Azure Active Directory (Azure AD) B2B együttműködésével kapcsolatos gyakori problémákra.

   > [!IMPORTANT]
   > **2021. március 31-től a**Microsoft már nem támogatja a meghívók beváltását nem felügyelt Azure AD-fiókok és bérlők létrehozásával a B2B együttműködési forgatókönyvekhez. Az előkészítés során arra bátorítjuk ügyfeleinket, hogy válasszák az [e-mailes egyszeri jelkód-hitelesítést.](one-time-passcode.md) Örömmel fogadjuk a nyilvános előnézeti funkcióval kapcsolatos visszajelzéseit, és izgatottan várjuk, hogy még több együttműködési módot hozzon létre.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Külső felhasználót adtam hozzá, de nem látom őket a globális címjegyzékben vagy a személyek választójában

Azokban az esetekben, ahol a külső felhasználók nincsenek feltöltve a listában, az objektum replikálása eltarthat néhány percig.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>A B2B-vendégfelhasználó nem jelenik meg a SharePoint Online-ban/a OneDrive-felhasználók keresőjében

A SharePoint Online (SPO) személyek választójában meglévő vendégfelhasználók keresésének lehetősége alapértelmezés szerint ki van kapcsolva, hogy megfeleljen az örökölt viselkedésnek.

Ezt a szolgáltatást a "ShowPeoplePickerSuggestionsForGuestUsers" beállítással engedélyezheti a bérlői és webhelycsoport szintjén. A szolgáltatást a Set-SPOTenant és a Set-SPOSite parancsmagokkal állíthatja be, amelyek lehetővé teszik a tagok számára a címtárban lévő összes vendégfelhasználó keresését. A bérlői hatókör változásai nincsenek hatással a már kiépített SPO-helyekre.

## <a name="invitations-have-been-disabled-for-directory"></a>A meghívók le vannak tiltva a könyvtárhoz

Ha értesítést kap arról, hogy nincs engedélye a felhasználók meghívására, ellenőrizze, hogy a felhasználói fiókja jogosult-e külső felhasználók meghívására az Azure Active Directory > Felhasználói beállítások > Külső felhasználók > Külső együttműködési beállítások kezelése:

![A Külső felhasználók beállításait bemutató képernyőkép](media/troubleshoot/external-user-settings.png)

Ha a közelmúltban módosította ezeket a beállításokat, vagy a Vendégmeghívó szerepkört egy felhasználóhoz rendelte, előfordulhat, hogy 15–60 perces késleltetés tanusítanak a módosítások érvénybe lépése előtt.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>A meghívott felhasználó hibaüzenetet kap a beváltás során

Gyakori hibák a következők:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>A meghívott rendszergazdája nem engedélyezte az EmailVerified-felhasználók számára a létrehozást a bérlőjükön

Amikor olyan felhasználókat hív meg, akiknek a szervezete az Azure Active Directoryt használja, de ha az adott felhasználó fiókja nem létezik (például a felhasználó nem létezik az Azure AD-ben contoso.com). Előfordulhat, hogy a contoso.com rendszergazdája rendelkezik olyan házirenddel, amely megakadályozza a felhasználók létrehozását. A felhasználónak ellenőriznie kell a rendszergazdánál, hogy a külső felhasználók engedélyezettek-e. Előfordulhat, hogy a külső felhasználó rendszergazdájának engedélyeznie kell az e-mailben ellenőrzött felhasználókat a tartományukban (lásd az e-mailben ellenőrzött felhasználók engedélyezéséről [szóló cikket).](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

![Hiba, amely szerint a bérlő nem engedélyezi az e-mailben ellenőrzött felhasználókszámára](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Külső felhasználó még nem létezik összevont tartományban

Ha összevonási hitelesítést használ, és a felhasználó még nem létezik az Azure Active Directoryban, a felhasználó nem hívható meg.

A probléma megoldásához a külső felhasználó rendszergazdájának szinkronizálnia kell a felhasználó fiókját az Azure Active Directoryval.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hogyan lehet\#a ' ', amely általában nem érvényes karakter, szinkronizálni az Azure AD-vel?

"\#" az Azure AD B2B együttműködéshez vagy külső felhasználókszámára fenntartott user@contoso.com karakter az UPN-ekben, mivel a meghívott fiók user_contoso.com#EXT# lesz.@fabrikam.onmicrosoft.com Ezért \# a helyszíni upn-ek nem jelentkezhetnek be az Azure Portalra. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Hibaüzenet jelenik meg, amikor külső felhasználókat ad hozzá egy szinkronizált csoporthoz

Külső felhasználók csak a "hozzárendelt" vagy "Biztonság" csoportok, és nem a csoportok, amelyek a helyszínen elsajátított.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>A külső felhasználóm nem kapott beváltási e-mailt

A meghívottnak ellenőriznie kell az isp vagy spam szűrőjét, hogy megbizonyosodjon arról, hogy a következő cím engedélyezett:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Úgy vettem észre, hogy az egyéni üzenet nem kap szerepel a meghívó üzenetek et időnként

Az adatvédelmi törvényeknek való megfelelés érdekében API-ink nem tartalmaznak egyéni üzeneteket az e-mail meghívóban, ha:

- A meghívó nem rendelkezik e-mail címmel a meghívó bérlőben
- Amikor egy egyszerű alkalmazásküldi a meghívót

Ha ez a forgatókönyv fontos az Ön számára, letilthatja az API meghívó e-mail, és küldje el az Ön által választott e-mail mechanizmuson keresztül. Konzultáljon a szervezet jogi képviselőjével, és győződjön meg arról, hogy az így küldött e-mailek megfelelnek az adatvédelmi törvényeknek is.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>"AADSTS65005" hibaüzenet jelenik meg, amikor megpróbál bejelentkezni egy Azure-erőforrásba

A vendégfiókkal rendelkező felhasználó nem tud bejelentkezni, és a következő hibaüzenet jelenik meg:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

A felhasználó rendelkezik egy Azure-felhasználói fiókkal, és egy vírusos bérlő, aki elhagyott vagy nem felügyelt. Emellett nincsenek globális vagy vállalati rendszergazdák a bérlőben.

A probléma megoldásához át kell vennie az elhagyott bérlőt. Tekintse meg [a Nem felügyelt címtár átirányítása rendszergazdaként az Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) A szóban forgó tartományutótag internetfelé irányuló DNS-ének is hozzá kell férnie ahhoz, hogy közvetlen bizonyítékot szolgáltasson a névtér irányításáról. Miután a bérlő tért vissza egy felügyelt állapotba, kérjük, beszélje meg az ügyféllel, hogy elhagyja a felhasználók és az ellenőrzött tartománynév a legjobb megoldás a szervezet számára.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>A just-in-time vagy "vírusos" bérlővel rendelkező vendégfelhasználó nem tudja alaphelyzetbe állítani a jelszavát

Ha az identitás-bérlő egy just-in-time (JIT) vagy vírusos bérlő (azaz egy különálló, nem felügyelt Azure-bérlő), csak a vendég felhasználó alaphelyzetbe állíthatja a jelszavát. Előfordulhat, hogy egy szervezet [átveszi a vírusos bérlők felügyeletét,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) amelyek akkor jönnek létre, amikor az alkalmazottak a munkahelyi e-mail-címükkel regisztrálnak a szolgáltatásokra. Miután a szervezet átveszi a vírusos bérlőt, csak a szervezet rendszergazdája állíthatja vissza a felhasználó jelszavát, vagy engedélyezheti az SSPR-t. Szükség esetén meghívó szervezetként eltávolíthatja a vendégfelhasználói fiókot a címtárból, és újra elküldheti a meghívót.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Egy vendégfelhasználó nem tudja használni az AzureAD PowerShell V1 modult

2019. november 18-tól a címtárban lévő vendégfelhasználók (definíció szerint olyan felhasználói fiókok, ahol a **userType** tulajdonság **a Vendég)** le vannak tiltva az AzureAD PowerShell V1 modul használatában. A jövőben a felhasználónak tagfelhasználónak kell lennie (ahol **a userType** egyenlő **a taggal),** vagy az AzureAD PowerShell V2 modult kell használnia.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>Egy Azure US Government-bérlőben nem tudok b2B együttműködési vendégfelhasználót meghívni

Az Azure US Government-felhőn belül a B2B-együttműködés jelenleg csak az Azure US Government felhőben található bérlők között támogatott, és mindkettő támogatja a B2B-együttműködést. Ha olyan bérlőt hív meg egy bérlőhöz, amely nem része az Azure US Government felhőjének, vagy amely még nem támogatja a B2B együttműködést, hibaüzenetet kap. További részletekért és korlátozásokért lásd: [Azure Active Directory Premium P1 és P2 variációk.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)

## <a name="next-steps"></a>További lépések

[Támogatás a B2B együttműködéshez](get-support.md)

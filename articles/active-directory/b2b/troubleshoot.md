---
title: B2B-együttműködés – Azure Active Directory hibaelhárítása |} A Microsoft Docs
description: Jogorvoslatok az Azure Active Directory B2B együttműködés szolgáltatással kapcsolatos gyakori problémák
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: f772c14f414e4e1c5401e3c85de7009355453c98
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668530"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Az Azure Active Directory B2B-együttműködés hibaelhárítása

Az alábbiakban néhány gyakori problémák az Azure Active Directory (Azure AD) B2B-együttműködés kártérítése.


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Tudom hozzáadott külső felhasználó, de nem látja ezeket a globális címjegyzékben vagy az a személy kijelölése

Azokban az esetekben, ahol a külső felhasználók nem kerülnek be a listát az objektum replikálása néhány percig is eltarthat.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B-Vendég felhasználó nem jelenik meg a SharePoint Online és onedrive vállalati verzió személy kijelölése 
 
Keresse meg a SharePoint online-hoz (SPO) személy kijelölése a meglévő vendégfelhasználókat lehetővé teszi örökölt viselkedés megfelelően alapértelmezés szerint ki.

Ez a funkció a bérlő és a hely gyűjtemény szintjén "ShowPeoplePickerSuggestionsForGuestUsers" beállítás használatával engedélyezheti. A funkciót, a Set-SPOTenant és Set-SPOSite parancsmagok, amelyek lehetővé teszik a Keresés az összes meglévő vendégfelhasználókat a címtár tagjai használatával állíthatja be. A bérlői hatókörben módosítása nem érinti a már üzembe helyezett SPO-helyek.

## <a name="invitations-have-been-disabled-for-directory"></a>Meghívók le vannak tiltva könyvtár

Értesítést kap, hogy nincs engedélye felhasználókat meghívni, győződjön meg arról, hogy a felhasználói fiók jogosult külső felhasználók a felhasználói beállítások szakasz meghívása:

![Külső felhasználói beállítások](media/troubleshoot/external-user-settings.png)

Ha nemrég módosította ezeket a beállításokat, vagy a Vendégmeghívó szerepkörrel egy felhasználóhoz, van egy 15 – 60 perc késleltetéssel a módosítások életbe léptetéséhez.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>A felhasználót, hogy tudom hívni során érvényesítési hiba fogad

Gyakori hibák a következők:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Meghívott rendszergazda letiltotta az EmailVerified felhasználókat a bérlőben létrehozása folyamatban

Amikor a felhasználók figyelmét, amelyek a szervezet az Azure Active Directory, de ha az adott felhasználói fiók nem létezik (például a felhasználó nem létezik az Azure ad-ben a contoso.com). A rendszergazda a contoso.com, előfordulhat, hogy tartalmaznak egy házirend meggátolja, hogy a felhasználók létrehozása folyamatban. A felhasználónak ellenőriznie kell meghatározni, ha a külső felhasználók számára engedélyezett a rendszergazdától. Előfordulhat, hogy a külső felhasználó rendszergazda kell e-mailben ellenőrzött felhasználók a tartományhoz (Ez [cikk](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) az e-mailben ellenőrzött felhasználók).

![Hiba meg ezen a bérlőn nem teszi lehetővé e-mailben ellenőrzött felhasználók felvételét](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Külső felhasználó nem létezik már egy összevont tartományban

Ha a felhasználó még nem létezik az Azure Active Directory összevonási hitelesítést használ, a felhasználó nem hívható meg.

A probléma megoldásához, a külső felhasználó rendszergazda szinkronizálnia kell a felhasználói fiók az Azure Active Directoryhoz.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hogyan biztosítja a\#", amely általában nem érvényes karakter, az Azure AD sync?

"\#" van egy Azure AD B2B együttműködés vagy a külső felhasználók UPN-EK fenntartott karaktert, mert a meghívott fiókkal user@contoso.com user_contoso.com#EXT# válik@fabrikam.onmicrosoft.com. Ezért \# az egyszerű felhasználónevek helyszíni érkező nem engedélyezett az Azure Portalra való bejelentkezéshez. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Külső felhasználók hozzáadásakor egy szinkronizált csoportot hibaüzenetet kap

A külső felhasználók is hozzáadhatók, csak "hozzárendelt" vagy "Security" csoport és a csoportokat, amelyek a helyszíni lemezkép.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>A külső felhasználó nem kapott egy e-mailt beváltásához

A meghívott ellenőrizze azok Internetszolgáltató vagy a levélszemét szűrőt annak biztosítására, hogy engedélyezve van-e a következő címre: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>E figyelje meg, hogy az egyéni üzenet nem kap mellékelt ajánlati üzenetek esetenként

Ahhoz, hogy megfeleljenek adatvédelmi törvények, az API-k nem tartalmaznak személyes üzeneteket az e-mailes meghívót a ha:

- A meghívót küldő személy nem rendelkezik e-mail-címmel a meghívó bérlőben
- Ha egy App Service egyszerű küldi el a meghívót

Fontos, hogy ez a forgatókönyv esetén az API-t meghívó e-mail le, és küldje el a kiválasztott e-mail mechanizmus segítségével. Tekintse meg a szervezet védőt, hogy minden e-mailt küld, így is megfelel a szigorú adatvédelmi törvényeinek betartásáért.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>A just-in-time vagy "vírusos" bérlőhöz vendégfelhasználó nem tudja alaphelyzetbe állítani a jelszavát

Ha a szolgáltatásidentitás bérlője egy – igény (szerinti JIT) vagy a bérlő ugrásszerű (azaz egy különálló, nem felügyelt Azure-bérlőhöz), csak a meghívott felhasználónak alaphelyzetbe állíthatja a jelszavát. Egyes esetekben egy szervezet fog [átveszi a vírusos bérlő](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) , amely jönnek létre, ha az alkalmazottak saját munkahelyi e-mail-címek használatával szolgáltatások regisztrálhat. A szervezetnek szüksége van a vírusos bérlő felett, miután csak egy adott szervezet rendszergazdája alaphelyzetbe a jelszót, vagy SSPR engedélyezése. Ha szükséges, mint a meghívó szervezetet, a Vendég felhasználói fiókhoz eltávolítása a címtárból, és egy meghívó újraküldése.

## <a name="next-steps"></a>További lépések

- [Támogatás kérése a B2B-együttműködés](get-support.md)

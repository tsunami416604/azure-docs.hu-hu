---
title: "Hibaelhárítás az Azure Active Directory B2B együttműködés |} Microsoft Docs"
description: "Jogorvoslatok Azure Active Directory B2B együttműködés kapcsolatos általános problémák"
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
ms.date: 05/25/2017
ms.author: sasubram
ms.openlocfilehash: 6773aeb6f8470bb8d35764d579681b4167e5175d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Hibaelhárítás az Azure Active Directory B2B együttműködés

Az alábbiakban néhány jogorvoslatok kapcsolatos Azure Active Directory (Azure AD) B2B együttműködés gyakori problémákat.


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Tudom felvételét a külső felhasználó, de ne legyenek láthatóak a globális címlista vagy a személyek kiválasztása

Azokban az esetekben, ahol a külső felhasználók nem kerül a listában az objektum percet is igénybe vehet néhány replikálásához.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>A B2B Vendég felhasználó nem látható a SharePoint Online/OneDrive személyek kiválasztása 
 
Keresse meg a SharePoint Online (SPO) személyek kiválasztása a meglévő vendégfelhasználók teszi örökölt értéke alapértelmezés szerint értéke OFF.

Ez a funkció a bérlői és a hely gyűjtemény szintjén "ShowPeoplePickerSuggestionsForGuestUsers" beállítás használatával engedélyezheti. Beállíthatja, hogy a funkciót, a Set-SPOTenant és a Set-SPOSite parancsmagok, amelyek lehetővé teszik az összes meglévő vendég felhasználók kereséséhez a könyvtárban tagok használatával. A bérlői hatókört változásai nem befolyásolják a már kiépített SPO helyek.

## <a name="invitations-have-been-disabled-for-directory"></a>Könyvtár meghívókat le van tiltva

Ha Ön nem jogosult a meghívott felhasználóknak akkor kapnak értesítést, győződjön meg arról, hogy a felhasználói fiók engedélyezve van-e a külső felhasználók a felhasználói beállítások szakasz meghívott:

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Ha nemrég módosította ezeket a beállításokat, vagy a Vendég meghívó szerepkört a felhasználóhoz rendelt, előfordulhat, a 15-60 perc késleltetéssel a módosítások érvénybe lépéséhez.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>A felhasználót, hogy I meghívott hiba fogad érvényesítési során

Gyakori hibák a következők:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Meghívott rendszergazda nem engedélyezte a bérlőben jöjjön létre EmailVerified felhasználók

Amikor felhasználókat fióknevet, amelynek a szervezet által használt Azure Active Directory, de ha az adott felhasználói fiók nem létezik (például a felhasználó nem létezik az Azure AD contoso.com). A rendszergazda a contoso.com is tartalmaznak egy házirend meggátolja, hogy a felhasználók létrehozása folyamatban. A felhasználó ellenőriznie kell meghatározni, ha a külső felhasználók számára engedélyezett-e a rendszergazdától. A külső felhasználó rendszergazda is engedélyeznie kell a tartomány felhasználói e-mailek ellenőrzése (Ez [cikk](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) az e-mailek ellenőrzése felhasználók).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Külső felhasználó nem létezik már egy összevont tartományban

Ha a felhasználó már nem létezik az Azure Active Directory összevonási hitelesítést használ, a felhasználót nem hívhat meg.

A probléma megoldásához, a külső felhasználó rendszergazda szinkronizálnia kell a felhasználói fiók az Azure Active Directoryhoz.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hogyan biztosítja a\#", amely nincs általában érvénytelen karakter, és az Azure AD sync?

"\#" nem egy foglalt karakter UPN-EK az Azure AD B2B együttműködés vagy külső felhasználók számára, mert a meghívott fiók user@contoso.com user_contoso.com# válikEXT@fabrikam.onmicrosoft.com. Ezért \# az UPN-EK a helyszíni érkező nem engedélyezett az Azure-portálon bejelentkezhet. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Ha a külső felhasználók felvétele egy szinkronizált csoportot hibaüzenetet kap

Külső felhasználók csak "hozzárendelt" vagy "Biztonság" csoportok és nem a csoportokat, amelyek a helyszíni lemezkép formátumú felveheti.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>A külső felhasználó nem kapta meg a beváltani e-mailt

A meghívott érdemes egyeztetni az internetszolgáltató által biztosított és a levélszemét szűrő annak ellenőrzéséhez, hogy engedélyezett-e a következő címre:Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>I figyelje meg, hogy az egyéni üzenet nem kérdezhető le a meghívó üzeneteket mellékelt néha

Adatvédelmi törvényekkel ahhoz, hogy az API-k nem tartalmaznak egyéni üzenetek az e-mailek ajánlati során:

- A meghívó hívja fel a bérlő nem rendelkezik egy e-mail címet
- Amikor egy App Service principal elküldi a meghívó

Ebben a forgatókönyvben az Ön számára fontosak, ha az API-t meghívó e-mail mellőzése, és küldje el az e-mailek mechanizmus az Ön által választott. Tekintse meg a szervezet védőt való győződjön meg arról, hogy minden e-mailek küldésekor, így is adatvédelmi törvényekkel összhangban.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hogyan rendszergazdák Azure Active Directory B2B együttműködés felhasználók hozzá?](active-directory-b2b-admin-add-users.md)
* [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](active-directory-b2b-iw-add-users.md)
* [A B2B együttműködés meghívó e-mail elemei](active-directory-b2b-invitation-email.md)
* [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md)
* [Az Azure AD B2B együttműködés licencelés](active-directory-b2b-licensing.md)
* [Az Azure Active Directory B2B együttműködés gyakori kérdések (GYIK)](active-directory-b2b-faq.md)
* [Az Azure Active Directory B2B együttműködés API és a Testreszabás](active-directory-b2b-api.md)
* [Többtényezős hitelesítés a B2B-együttműködés felhasználói számára](active-directory-b2b-mfa-instructions.md)
* [Adja hozzá a B2B együttműködés felhasználók nélkül](active-directory-b2b-add-user-without-invite.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)

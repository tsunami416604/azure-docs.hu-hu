---
title: Az Azure AD SSPR követelményeinek |} Microsoft Docs
description: Az Azure AD az önkiszolgáló jelszó követelményeinek alaphelyzetbe állítása és hogyan kielégítése érdekében
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5409bf198d0e3f6537619ef4698d9f2e31bd27c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257588"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Jelszó alaphelyzetbe állítása, anélkül, hogy a végfelhasználói regisztrálási telepítése

Ha szeretné telepíteni az Azure Active Directory (Azure AD) önkiszolgáló jelszó-változtatási (SSPR), hitelesítési adatok kell elhelyezkednie. Egyes szervezeteknél a felhasználók saját maguk hitelesítési adatok megadása. Azonban számos szervezet szeretné szinkronizálni az adatokat, amelyek már létezik az Active Directoryban. A szinkronizált adatok szeretné elérhetővé tenni az Azure AD- és felhasználói beavatkozás nélkül, ha SSPR meg:
   * Helyes formátumban az adatokat a helyszíni címtárba.
   * Konfigurálása [a gyorsbeállítások használatával az Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md).

Megfelelően működjön, telefonszámok a formátumúnak kell lennie *+ országhívószám PhoneNumber*, például + 1 4255551234.

> [!NOTE]
> Szükség van az országhívószámot és a telefonszámot közé.
>
> Jelszó alaphelyzetbe állítása nem támogatja a telefonmellék. Még a formátumban + 1 4255551234 X 12345 bővítmények előtt távolítsa el a a hívást kezdeményeznek.

## <a name="fields-populated"></a>Mezői

Ha az Azure AD Connectben az alapértelmezett beállításokat használja, a következő megfeleltetéseket történik:

| Helyszíni Active Directory | Azure AD |
| --- | --- |
| TelephoneNumber | Irodai telefon |
| Mobileszköz | Mobiltelefon |

Amikor a felhasználó ellenőriztette a mobiltelefon számával, a telefonszám mező hitelesítési kapcsolattartási adatokat az Azure ad-ben alatti is tölti fel ezt a számot.

## <a name="authentication-contact-info"></a>Hitelesítési kapcsolattartási adatok

Egy globális rendszergazda manuálisan állíthatja be a hitelesítési kapcsolattartási adatokat egy felhasználó az alábbi képernyőképen látható módon.

![Contact][Contact]

Ha a telefonszám mező fel van töltve, és az SSPR-házirendben engedélyezve van a mobiltelefon, a felhasználó jelenik meg, hogy a jelszó alaphelyzetbe állítása regisztrációs lapon és a jelszó során visszaállítása munkafolyamat. 

A telefonszámok mező nem használatos a jelszó-visszaállításhoz.

Ha az e-mailek mező nem üres, és e-mailt az SSPR-házirendben engedélyezve van, a felhasználó jelenik meg, hogy e-mailt a jelszó alaphelyzetbe állítása regisztrációs oldalon és során a jelszó alaphelyzetbe állítása a munkafolyamat.

Ha a másodlagos e-mail mező fel van töltve, és e-mailt az SSPR-házirendben engedélyezve van, a felhasználó fog **nem** lásd: e-maileket a jelszó alaphelyzetbe állítása regisztrációs lapjához, de megjelennek a során a jelszó alaphelyzetbe állítása a munkafolyamat. 


## <a name="security-questions-and-answers"></a>Biztonsági kérdések és válaszok

A biztonsági kérdések és válaszok az Azure AD-bérlő biztonságos helyen tárolja, és csak felhasználó keresztül elérhető a [SSPR regisztrációs portál](https://aka.ms/ssprsetup). A rendszergazdák nem tekintse meg vagy egy másik felhasználói kérdések és válaszok tartalmának módosítása.

### <a name="what-happens-when-a-user-registers"></a>Mi történik, ha a felhasználó regisztrálása

Amikor a felhasználó regisztrál, a regisztrációs lapjához beállítja a következő mezőket:

* **Hitelesítéshez megadott telefonját**
* **Hitelesítési E-mail**
* **Biztonsági kérdések és válaszok**

Ha a megadott értéket **mobiltelefon** vagy **másodlagos e-mail-címet**, felhasználók azonnal használhatja ezeket az értékeket visszaállíthassák a jelszavukat, még akkor is, ha azok még nem regisztrált a szolgáltatáshoz. Ezenkívül felhasználók láthatják ezeket az értékeket, amikor első alkalommal regisztrálja, és módosíthatják azokat, ha szeretnék. Miután regisztrálása sikeres volt, ezek az értékek maradnak a a **hitelesítéshez megadott telefonját** és **hitelesítési E-mail** mezők, illetve.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Állítsa be, és a PowerShell segítségével a hitelesítési adatokat olvasni.

A következő mezőket a PowerShell segítségével állítható be:

* **Másodlagos e-mail**
* **Mobiltelefon**
* **Irodai telefon**: csak akkor állítható, ha egy helyszíni címtár nem még szinkronizálása

### <a name="use-powershell-version-1"></a>1-es verziójú PowerShell használata

A kezdéshez kell [töltse le és telepítse az Azure AD PowerShell modult](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Miután van telepítve, használhatja az egyes mezők konfigurálásához szükséges lépésekről.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>A PowerShell használatával az 1-es hitelesítési adatok beállítása

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>A hitelesítési adatok PowerShell 1-es verziójával

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Olvassa el a hitelesítéshez megadott telefonját és hitelesítési E-mail beállítások

Olvasni a **hitelesítéshez megadott telefonját** és **hitelesítési E-mail** 1-es verziójú PowerShell használata esetén az alábbi parancsokat használja:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>PowerShell 2-es verzióját használja.

A kezdéshez kell [töltse le és telepítse az Azure AD 2-es PowerShell modul](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Miután van telepítve, használhatja az egyes mezők konfigurálásához szükséges lépésekről.

A következő parancsokat a PowerShell Install-Module-t támogató újabb verzióiban gyors telepítéséhez. (Az első sorban ellenőrzi, hogy ha a modul már telepítve van.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>A PowerShell-lel 2-es verzióját a hitelesítési adatok beállítása

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>A hitelesítési adatok PowerShell 2-es verzió

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globális rendszergazdák módosíthatja a felhasználó hitelesítési kapcsolattartási adatai"

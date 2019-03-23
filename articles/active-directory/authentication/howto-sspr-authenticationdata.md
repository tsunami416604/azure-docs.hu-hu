---
title: Az Azure AD SSPR adatok követelményei – Azure Active Directory
description: Adatok követelményei az Azure AD önkiszolgáló jelszó-visszaállítás, és hogyan kielégítése érdekében
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a0d7edb6c7faafcad55e827c2d9e3d2eeea40f5
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371366"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Jelszó-visszaállítás anélkül, hogy a végfelhasználói regisztrációs üzembe helyezése

Az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) üzembe helyezéséhez hitelesítési adatok megtalálhatónak kell lennie. Egyes szervezeteknél lehetőséget adja meg a hitelesítési adataikat, maguk a felhasználók számára. Azonban számos szervezet inkább szinkronizálása az Active Directoryban már meglévő adataival. A szinkronizált adatok szeretné elérhetővé tenni az Azure AD és felhasználói beavatkozás nélkül, ha az SSPR meg:

* Megfelelően formázza az adatokat a helyszíni címtárban.
* Konfigurálása [a gyorsbeállítások használatával az Azure AD Connect](../hybrid/how-to-connect-install-express.md).

Megfelelően működjön, telefonszámokat a következő formátumban kell lennie *és CountryCode PhoneNumber*, például + 1 4255551234.

> [!NOTE]
> Szükség van egy országhívószámát és telefonszámát közötti hely.
>
> Új jelszó kérése a telefonos mellékek nem támogatja. A + 1 4255551234 X 12345 formátumban, még akkor is, bővítmények előtt távolítsa el a a hívást kezdeményeznek.

## <a name="fields-populated"></a>Mezői

Ha az Azure AD Connect az alapértelmezett beállításokat használja, a következő hozzárendeléseket születnek:

| Helyszíni Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Irodai telefon |
| mobil | Mobiltelefon |

Amikor a felhasználó ellenőriztette a mobiltelefon számával, a telefonos hitelesítési kapcsolattartási adatok az Azure ad-ben mezőre is tölti fel ezt a számot.

## <a name="authentication-contact-info"></a>Hitelesítési kapcsolattartási adatok

Globális rendszergazda manuálisan is állíthat be a hitelesítési kapcsolattartási adatok egy felhasználó az alábbi képernyőfelvételen a név jelenik meg.

![Hitelesítés a felhasználó adatai forduljon az Azure AD-ben][Contact]

Ha a telefonszám mező fel van töltve, és az SSPR-házirendben engedélyezve van a mobiltelefon, a felhasználó jelenik meg, hogy a szám, a jelszó alaphelyzetbe állítása való regisztrációhoz, és közben a jelszó alaphelyzetbe állítása munkafolyamat.

A másodlagos telefonszám mező nem használatos a jelszó-visszaállításhoz.

Ha az E-mail mező fel van töltve, és e-mailt az SSPR-házirendben engedélyezve van, a felhasználó jelenik meg, hogy e-mailt a jelszó alaphelyzetbe állítása való regisztrációhoz, és közben a jelszó alaphelyzetbe állítása a munkafolyamatot.

Ha a másodlagos e-mail-cím mező fel van töltve, és e-mailt az SSPR-házirendben engedélyezve van, a felhasználó fog **nem** tekintse meg, hogy e-mailjeit a jelszó-visszaállítási regisztrációs oldal, de akkor fogja látni az során a jelszó alaphelyzetbe állítása a munkafolyamatot.

## <a name="security-questions-and-answers"></a>Biztonsági kérdések és válaszok

Biztonsági kérdések és válaszok az Azure AD-bérlő biztonságosan tárolódnak, és csak keresztül a felhasználók a [önkiszolgáló jelszó-változtatási](https://aka.ms/ssprsetup). A rendszergazdák nem láthatják, állítsa be, vagy egy másik felhasználói kérdések és válaszok tartalmát módosítjuk.

## <a name="what-happens-when-a-user-registers"></a>Mi történik, ha a felhasználó regisztrálása

Amikor egy felhasználó regisztrálja magát, a regisztrációs oldalhoz beállítja a következő mezőket:

* **Hitelesítéshez használt telefon**
* **Hitelesítő e-mail-cím**
* **Biztonsági kérdések és válaszok**

Ha a megadott értéket **mobiltelefon** vagy **másodlagos e-mail cím**, felhasználók azonnal használhatja ezeket az értékeket a jelszavaikat, még akkor is, ha azok még nem regisztrált a szolgáltatáshoz. Emellett a felhasználók ezeket az értékeket látják azokat először regisztráljon, és módosíthatják azokat, ha szeretne. Miután regisztrálta sikeresen, ezeket az értékeket a őrzött a **hitelesítéshez használt telefon** és **hitelesítési E-mail** mezőket, illetve.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Állítsa be, és olvassa el a Powershellen keresztül a hitelesítési adatok

A következő mezőket a PowerShell használatával állítható:

* **Másodlagos e-mail-cím**
* **Mobiltelefon**
* **Irodai telefon**: Csak akkor állítható, ha egy helyszíni címtár nem vagyunk szinkronizálása

### <a name="use-powershell-version-1"></a>1. verzió PowerShell-lel

Első lépésként kell [töltse le és telepítse az Azure AD PowerShell modul](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Ha már van telepítve, használhatja az egyes mezők konfigurálása szükséges.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Állítsa be a hitelesítési adatokat a PowerShell-lel 1-es verzió

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>A hitelesítési adatokat a PowerShell-lel 1-es verzió

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Olvassa el a hitelesítéshez használt telefon és a hitelesítési E-mail beállítások

Olvasni a **hitelesítéshez használt telefon** és **hitelesítési E-mail** PowerShell 1-es verzió használata esetén használja a következő parancsokat:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>2. verziójú PowerShell-lel

Első lépésként kell [töltse le és telepítse az Azure AD 2. verziójú PowerShell-modul](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Ha már van telepítve, használhatja az egyes mezők konfigurálása szükséges.

A PowerShell legújabb verzióit támogatja az Install-Module gyors telepítéséhez futtassa a következő parancsokat. (Az első sorban ellenőrzi, hogy ha a modul már telepítve van.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Állítsa be a hitelesítési adatokat a 2. verziójú PowerShell-lel

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>A hitelesítési adatokat a 2. verziójú PowerShell-lel

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../user-help/active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "A globális rendszergazdák módosíthatja a felhasználó hitelesítési kapcsolattartási adatok"

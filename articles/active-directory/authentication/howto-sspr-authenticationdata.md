---
title: Az Azure AD SSPR adatkövetelményei – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-alaphelyzetbe állításának és azok teljesítésének adatvédelmi követelményei
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a14338e552250ac63c344365099a16f20616ea9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74964028"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Jelszó-visszaállítás központi telepítése végfelhasználói regisztráció nélkül

Az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) üzembe helyezéséhez hitelesítési adatokat kell bemutatni. Egyes szervezetek a felhasználók maguk adják meg a hitelesítési adataikat. Más szervezetek inkább az Active Directoryban már létező adatokkal szeretne szinkronizálni. Ezeket a szinkronizált adatokat az Azure AD és az SSPR is elérhetővé teszi anélkül, hogy felhasználói beavatkozást igényelne, ha megfelel az alábbi követelményeknek:

* Megfelelően formázza a helyszíni címtárban lévő adatokat.
* Konfigurálja [az Azure AD Connectet a gyorsbeállítások használatával.](../hybrid/how-to-connect-install-express.md)

A megfelelő működéshez a telefonszámok formátumának *+CountryCode Telefonszám,* például +1 4255551234 formátumúnak kell lennie.

> [!NOTE]
> Az országkód és a telefonszám között szóköznek kell lennie.
>
> A jelszó-visszaállítás nem támogatja a telefonbővítményeket. Még a +1 4255551234X12345 formátumban is a bővítmények et eltávolítja a hívás megkezdése előtt.

## <a name="fields-populated"></a>Mezők feltöltve

Ha az Azure AD Connect alapértelmezett beállításait használja, a következő leképezések készülnek:

| Helyszíni Active Directory | Azure AD |
| --- | --- |
| telefonszáma | Munkahelyi telefon |
| mobil | Mobiltelefon |

Miután a felhasználó ellenőrzi a mobiltelefonszámát, a *Telefon* mező **hitelesítési kapcsolattartási adatok** az Azure AD is feltölti ezt a számot.

## <a name="authentication-contact-info"></a>Hitelesítési kapcsolattartási adatok

Az Azure Portalon egy Azure AD-felhasználó **hitelesítési metódusalapján** a globális rendszergazda manuálisan beállíthatja a hitelesítési kapcsolattartási adatokat, ahogy az a következő példa képernyőképen látható:

![Az Azure AD egyik felhasználójának hitelesítési kapcsolattartási adatai][Contact]

* Ha a **Telefon** mező ki van töltve, és a **mobiltelefon** engedélyezve van az SSPR-házirendben, a felhasználó ezt a számot látja a jelszó-visszaállítási regisztrációs lapon és a jelszó-visszaállítási munkafolyamat során.
* Az **Alternatív telefon** mező nem használható jelszó-visszaállításhoz.
* Ha az **E-mail** mező ki van töltve, és az **E-mail** engedélyezve van az SSPR-házirendben, a felhasználó látja az e-mailt a jelszó-visszaállításregisztrációs lapon és a jelszó-visszaállítási munkafolyamat során.
* Ha a **Másodlagos e-mail** mező ki van töltve, és az **E-mail** engedélyezve van az SSPR-házirendben, a felhasználó **nem fogja** látni az e-mailt a jelszó-visszaállításregisztrációs lapon, de a jelszó-visszaállítási munkafolyamat során látja.

## <a name="security-questions-and-answers"></a>Biztonsági kérdések és válaszok

A biztonsági kérdések és válaszok biztonságosan tárolódnak az Azure AD-bérlőben, és csak az [SSPR regisztrációs portálon](https://aka.ms/ssprsetup)keresztül érhetők el a felhasználók számára. A rendszergazdák nem láthatják, nem állíthatják be és nem módosíthatják más felhasználók kérdéseit és válaszait.

## <a name="what-happens-when-a-user-registers"></a>Mi történik, ha a felhasználó regisztrál?

Amikor egy felhasználó regisztrál, a regisztrációs oldal a következő mezőket állítja be:

* **Hitelesítési telefon**
* **Hitelesítési e-mail**
* **Biztonsági kérdések és válaszok**

Ha megadott egy értéket **a mobiltelefon** vagy alternatív **e-mail,** a felhasználók azonnal használhatják ezeket az értékeket, hogy állítsa vissza a jelszavakat, akkor is, ha még nem regisztrált a szolgáltatás. Ezenkívül a felhasználók az első regisztrációkor látják ezeket az értékeket, és módosíthatják azokat, ha akarják. A sikeres regisztráció után ezek az értékek megmaradnak a **Hitelesítési telefon** és a **Hitelesítési e-mail** mezőkben.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>A hitelesítési adatok beállítása és olvasása a PowerShellen keresztül

A következő mezők állíthatók be a PowerShellen keresztül:

* **Másodlagos e-mail cím**
* **Mobiltelefon**
* **Irodai telefon**: Csak akkor állítható be, ha nem szinkronizál egy helyszíni könyvtárral

### <a name="use-powershell-version-1"></a>A PowerShell 1-es verziójának használata

A kezdéshez le kell [töltenie és telepítenie kell az Azure AD PowerShell-modult.](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule) A telepítés után az egyes mezők konfigurálásához használhatja a következő lépéseket.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>A hitelesítési adatok beállítása a PowerShell 1-es verziójával

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>A hitelesítési adatok olvasása a PowerShell 1-es verziójával

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Olvassa el a Hitelesítési telefon és a hitelesítési e-mail beállításait

A **Hitelesítési telefon** és a **hitelesítési e-mail** elolvasásához a PowerShell 1-es verziójának használatakor a következő parancsokat használja:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>A PowerShell 2-es verziójának használata

A kezdéshez le kell [töltenie és telepítenie kell az Azure AD 2-es verziójú PowerShell-modult.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) A telepítés után az egyes mezők konfigurálásához használhatja a következő lépéseket.

A PowerShell telepítése-modult támogató legújabb verzióiból történő gyors telepítéshez futtassa a következő parancsokat. (Az első sor ellenőrzi, hogy a modul már telepítve van-e.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>A hitelesítési adatok beállítása a PowerShell 2-es verziójával

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>A hitelesítési adatok olvasása a PowerShell 2-es verziójával

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó alaphelyzetbe állítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció az önkiszolgáló jelszó alaphelyzetbe állításához](../user-help/active-directory-passwords-reset-register.md)
* [Van engedélyezési kérdése?](concept-sspr-licensing.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, valami eltört. Hogyan háríthatók el az SSPR hibái?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "A globális rendszergazdák módosíthatják a felhasználó hitelesítési kapcsolattartási adatait"

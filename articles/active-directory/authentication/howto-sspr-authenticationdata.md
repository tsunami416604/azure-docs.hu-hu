---
title: Az Azure AD SSPR-adatkövetelményei – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszavának alaphelyzetbe állításához és az azokhoz való hozzáféréshez szükséges adatkövetelmények
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964028"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Jelszó-visszaállítás üzembe helyezése végfelhasználói regisztráció nélkül

A Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) üzembe helyezéséhez a hitelesítési adatértékeknek jelen kell lenniük. Néhány szervezet saját maga adja meg a saját hitelesítési adatbevitelét. Más szervezetek szívesebben szinkronizálnak olyan adatokat, amelyek már léteznek a Active Directoryban. A szinkronizált adat az Azure AD és a SSPR számára érhető el anélkül, hogy felhasználói beavatkozásra lenne szükség, ha megfelel a következő követelményeknek:

* Megfelelően formázza a helyszíni címtárban tárolt adatait.
* Konfigurálja [Azure ad Connect az expressz beállítások használatával](../hybrid/how-to-connect-install-express.md).

A megfelelő működéshez a telefonszámoknak a *+ országhívószám telefonszám*formátumban kell lenniük, például: + 1 4255551234.

> [!NOTE]
> Az országkód és a telefonszám között szóköz szükséges.
>
> A jelszó-visszaállítás nem támogatja a telefonos bővítményeket. A rendszer a hívás elhelyezése előtt is eltávolítja a bővítményeket a + 1 4255551234X12345 formátumban.

## <a name="fields-populated"></a>Mezők kitöltve

Ha a Azure AD Connect alapértelmezett beállításait használja, a következő leképezések lesznek:

| Helyszíni Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Irodai telefon |
| mobil | Mobiltelefon |

Miután egy felhasználó ellenőrizte mobiltelefonszámát, az Azure AD-ben a **hitelesítési kapcsolattartási adatok** területen lévő *telefonos* mező is fel lesz töltve.

## <a name="authentication-contact-info"></a>Hitelesítési kapcsolattartási adatok

A Azure Portal egy Azure AD-felhasználójának **hitelesítési módszerek** lapján a globális rendszergazda manuálisan állíthatja be a hitelesítési kapcsolattartási adatokat, ahogy az alábbi ábrán is látható:

![Hitelesítési kapcsolattartási adatok az Azure AD-ben egy felhasználónál][Contact]

* Ha a **telefon** mező fel van töltve, és a **mobiltelefon** engedélyezve van a SSPR-házirendben, a felhasználó ezt a számot látja a jelszó-visszaállítási regisztrációs lapon és a jelszó-visszaállítási munkafolyamatban.
* A **másodlagos telefon** mező nincs használatban a jelszó-visszaállításhoz.
* Ha az **e** -mail-mező fel van töltve, és az **e-mailek** engedélyezve vannak a SSPR szabályzatban, a felhasználó a jelszó-visszaállítási regisztrációs oldalon és a jelszó-visszaállítási munkafolyamatban láthatja a levelezést.
* Ha a **másodlagos e-mail** mező fel van töltve, és az **e-mailek** engedélyezve vannak a SSPR szabályzatban, a felhasználó **nem** látja ezt az e-mailt a jelszó-visszaállítási regisztrációs lapon, de a jelszó-visszaállítási munkafolyamat során megjelenik.

## <a name="security-questions-and-answers"></a>Biztonsági kérdések és válaszok

A biztonsági kérdéseket és válaszokat a rendszer biztonságosan tárolja az Azure AD-bérlőben, és csak a [SSPR regisztrációs portálon](https://aka.ms/ssprsetup)keresztül érhető el a felhasználók számára. A rendszergazdák nem láthatják, nem állíthatják be és nem módosíthatják más felhasználók kérdéseit és válaszait.

## <a name="what-happens-when-a-user-registers"></a>Mi történik, ha egy felhasználó regisztrálja

Amikor egy felhasználó regisztrál, a regisztrációs oldal a következő mezőket állítja be:

* **Hitelesítő telefon**
* **Hitelesítési E-mail**
* **Biztonsági kérdések és válaszok**

Ha a **mobiltelefon** vagy a **másodlagos e-mail**esetében értéket adott meg, a felhasználók azonnal felhasználhatják ezeket az értékeket a jelszavuk visszaállítására, még akkor is, ha nincsenek regisztrálva a szolgáltatáshoz. Emellett a felhasználók ezeket az értékeket is láthatják, amikor első alkalommal regisztrálják magukat, és ha szeretné, módosíthatja azokat. A sikeres regisztráció után ezek az értékek a **hitelesítés telefonos** és a **hitelesítő e-mail-** mezőiben megmaradnak.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>A hitelesítési információk beállítása és olvasása a PowerShell-lel

A következő mezők állíthatók be a PowerShell használatával:

* **Másodlagos e-mail cím**
* **Mobiltelefon**
* **Office Phone**: csak akkor állítható be, ha nem a helyszíni címtárral szinkronizál

### <a name="use-powershell-version-1"></a>A PowerShell 1-es verziójának használata

Első lépésként [le kell töltenie és telepítenie kell az Azure ad PowerShell-modult](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). A telepítése után a következő lépésekkel konfigurálhatja az egyes mezőket.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>A hitelesítési adatértékek beállítása a PowerShell 1-es verziójával

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>A hitelesítési információk beolvasása a PowerShell 1-es verziójával

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>A hitelesítés telefonos és hitelesítési e-mail-beállításainak beolvasása

Ha a PowerShell 1-es verziójának használatakor szeretné beolvasni a **hitelesítési telefonos** és a **hitelesítési e-mailt** , használja a következő parancsokat:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>A PowerShell 2-es verziójának használata

Első lépésként [le kell töltenie és telepítenie kell az Azure ad 2-es verziójú PowerShell-modulját](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). A telepítése után a következő lépésekkel konfigurálhatja az egyes mezőket.

A következő parancsok futtatásával gyorsan telepítheti a PowerShell legújabb verzióit, amelyek támogatják a telepítő modult. (Az első sor ellenőrzi, hogy a modul már telepítve van-e.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>A hitelesítési adatértékek beállítása a PowerShell 2-es verziójával

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>A hitelesítési információk beolvasása a PowerShell 2-es verziójával

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Következő lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../user-help/active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Úgy gondolom, hogy valami megszakadt. Hogyan a SSPR hibáinak megoldása?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "A globális rendszergazdák módosíthatják a felhasználó hitelesítési kapcsolattartási adatait"

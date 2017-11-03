---
title: "Az Azure AD SSPR követelményeinek |} Microsoft Docs"
description: "Az Azure AD az önkiszolgáló jelszó követelményeinek alaphelyzetbe állítása és hogyan kielégítése érdekében"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7936f47007285e3f7fa1d3220efa022a6e3881ca
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Jelszó alaphelyzetbe állítása, anélkül, hogy a végfelhasználói regisztrálási telepítése

Hitelesítési adatok jelen önkiszolgáló jelszó alaphelyzetbe állítása (SSPR) telepítéséhez szükséges. Egyes szervezetek adja meg a hitelesítési adatok maguk a felhasználók rendelkeznek, de számos szervezet szeretné szinkronizálni az Active Directory meglévő adatai. Ha megfelelően vannak formázva adatokat a helyszíni címtárban, és konfigurálja [gyorsbeállítások használata az Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md), hogy adatokat szeretné elérhetővé tenni az Azure AD és SSPR szükséges felhasználói beavatkozás nélkül.

Bármely telefonszámok formátumban + országhívószám PhoneNumber példa kell lennie: + 1 4255551234 megfelelően működjön.

> [!NOTE]
> Jelszó alaphelyzetbe állítása nem támogatja a telefonmellék. Még a formátumban + 1 4255551234 X 12345 bővítmények előtt távolítsa el a a hívást kezdeményeznek.

## <a name="fields-populated"></a>Mezői

Ha az alapértelmezett beállítások használata az Azure AD Connectben a következő megfeleltetéseket történik.

| A helyszíni AD | Azure AD | Az Azure AD-alapú hitelesítés kapcsolattartási adatai |
| --- | --- | --- |
| TelephoneNumber | Irodai telefon | Telefonszámok |
| Mobileszköz | Mobiltelefon | Telefonszám |


## <a name="security-questions-and-answers"></a>Biztonsági kérdések és válaszok

Biztonsági kérdések és válaszok az Azure AD-bérlő biztonságos helyen tárolja, és csak felhasználó keresztül elérhető a [SSPR regisztrációs portál](https://aka.ms/ssprsetup). A rendszergazdák nem tekintse meg vagy egy másik felhasználói kérdések és válaszok tartalmának módosítása.

### <a name="what-happens-when-a-user-registers"></a>Mi történik, ha a felhasználó regisztrálása

Amikor a felhasználó regisztrál, a regisztrációs lapjához beállítja a következő mezőket:

* Hitelesítéshez megadott telefonját
* Hitelesítési E-mail
* Biztonsági kérdések és válaszok

Ha a megadott értéket **mobiltelefon** vagy **másodlagos E-mail**, felhasználók azonnal használhatja ezeket az értékeket visszaállíthassák a jelszavukat, még akkor is, ha azok még nem regisztrált a szolgáltatáshoz. Továbbá a felhasználók tekintse meg ezeket az értékeket, amikor első alkalommal regisztrál, és módosíthatók, ha ki szeretnék. Miután sikeresen regisztrálják, ezeket az értékeket a maradnak a **hitelesítéshez megadott telefonját** és **hitelesítési E-mail** mezők, illetve.

## <a name="set-and-read-authentication-data-using-powershell"></a>Állítsa be, és a PowerShell használatával hitelesítési adatokat olvasni.

A következő mezők PowerShell segítségével állítható be

* Másodlagos E-mail
* Mobiltelefon
* Irodai telefon - csak akkor állítható, ha egy helyszíni címtár nem szinkronizálása

### <a name="using-powershell-v1"></a>PowerShell V1 használatával

A kezdéshez kell [töltse le és telepítse az Azure AD PowerShell modult](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Ha már van telepítve, követheti az egyes mezők konfigurálásához szükséges lépésekről.

#### <a name="set-authentication-data-with-powershell-v1"></a>A PowerShell V1 hitelesítési adatok beállítása

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>Hitelesítési adatok PowerShellPowerShell 1-es verzió

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>Hitelesítési telefon és hitelesítési E-mail csak olvasható Powershell V1 használja az alábbi parancsokkal

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>PowerShell V2 használatával

A kezdéshez kell [töltse le és telepítse az Azure AD V2 PowerShell-modul](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Ha már van telepítve, követheti az egyes mezők konfigurálásához szükséges lépésekről.

A PowerShell újabb verzióiban támogatja az Install-modul telepítése gyorsan, futtassa az alábbi parancsokat (az első sor egyszerűen ellenőrzi, hogy ha már van telepítve):

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>A PowerShell V2 hitelesítési adatok beállítása

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>A PowerShell V2 hitelesítési adatokat olvasni.

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Következő lépések

* [Hogyan végezze el a sikeres bevezetéshez az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-best-practices.md)
* [A jelszó megváltoztatására](active-directory-passwords-update-your-own-password.md).
* [Az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).
* [Licencelés kérdése van?](active-directory-passwords-licensing.md)
* [Hitelesítési módszerek állnak rendelkezésre a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik a házirend-beállításokban az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-policy.md)
* [A jelszóvisszaírás és miért fontos információk?](active-directory-passwords-writeback.md)
* [Hogyan jelentést az önkiszolgáló jelszó-Változtatási tevékenység?](active-directory-passwords-reporting.md)
* [Mik az önkiszolgáló jelszó-Változtatási közül az összes, és mit azokat a következőket:?](active-directory-passwords-how-it-works.md)
* [Szerintem valami nem működik. Hogyan hibáinak elhárítása az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-troubleshoot.md)
* [A rendszer nem jelzett valahol máshol kérdést kell](active-directory-passwords-faq.md)

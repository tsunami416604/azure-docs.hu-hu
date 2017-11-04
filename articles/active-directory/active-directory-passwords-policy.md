---
title: "Házirend: Azure AD SSPR |} Microsoft Docs"
description: "Az Azure AD az önkiszolgáló jelszó-változtatási házirend-beállításokban"
services: active-directory
keywords: "Az Active directory-jelszókezelés, jelszókezelés, az Azure AD self service jelszó alaphelyzetbe állítása"
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
ms.openlocfilehash: 5c33f08e54d522e0eea13a3e267f14f407fc59b6
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Jelszóházirendek és -korlátozások az Azure Active Directoryban

Ez a cikk ismerteti a jelszóházirendek és az Azure AD-bérlő tárolt felhasználói fiókok társított bonyolultsági követelményeknek.

## <a name="administrator-password-policy-differences"></a>Rendszergazdai jelszó házirend különbségek

A Microsoft új jelszó kérésére vonatkozó erős alapértelmezett **két kapus** szabályzat alkalmazását írja elő minden Azure rendszergazdai szerepkörhöz (például: globális rendszergazda, ügyfélszolgálati rendszergazda, jelszórendszergazda stb.)

Ezzel letiltja a rendszergazdák a biztonsági kérdések, és kikényszeríti a következő.

Két kapu házirend igénylő kétféle hitelesítési adatok (e-mail cím **és** telefonszám), az alábbi esetekben alkalmazza

* Az összes Azure-rendszergazdai szerepkörök
  * Segélyszolgálat rendszergazda
  * Szolgáltatás-rendszergazda
  * Számlázási rendszergazda
  * Partner Tier1 támogatása
  * Partner Tier2 támogatása
  * Exchange szolgáltatás-rendszergazda
  * Lync szolgáltatás-rendszergazda
  * Felhasználói fiók rendszergazdája
  * Directory írók
  * Globális rendszergazda/vállalati rendszergazda
  * A SharePoint szolgáltatás-rendszergazda
  * Megfelelőségi rendszergazda
  * Alkalmazás-rendszergazda
  * Biztonsági rendszergazda
  * Kiemelt szerepkör rendszergazda
  * Intune szolgáltatás-rendszergazda
  * Application Proxy szolgáltatás-rendszergazda
  * CRM szolgáltatás-rendszergazda
  * A Power BI szolgáltatás-rendszergazda
  
* 30 nap eltelt a próbaverzió **vagy**
* Megtalálható a személyes tartomány (contoso.com) **vagy**
* Az Azure AD Connect visszaszinkronizálja a helyszíni címtárban szereplő identitásokat

### <a name="exceptions"></a>Kivételek
Egy kapu házirend igénylő hitelesítési adatok egy részét (e-mail cím **vagy** telefonszám), az alábbi esetekben alkalmazza

* A próbaverzió első 30 napnyi **vagy**
* Személyes tartomány nincs jelen (*. onmicrosoft.com) **és** az Azure AD Connect nem szinkronizál identitások


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Összes felhasználói fiók UserPrincipalName házirendeket

Összes felhasználói fiók, amely be kell jelentkeznie az Azure AD egy egyedi felhasználói egyszerű felhasználónév (UPN) attribútum értéke annak a fióknak rendelkeznie kell. Vázol fel az alábbi táblázatban a házirendeket, amelyek érvényesek a helyszíni Active Directory felhasználói fiókok szinkronizálása a felhőbe, és csak felhőalapú felhasználói fiókokhoz.

| Tulajdonság | UserPrincipalName követelmények |
| --- | --- |
| Karakterből állhat |<ul> <li>A – Z</li> <li>a - z-ig</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Karakterek nem használhatók. |<ul> <li>A "@", amely a felhasználó nevét, a tartomány nem az elválasztó karaktert.</li> <li>A pont karakter nem tartalmazhat "." közvetlenül megelőző a "@" szimbólummal</li></ul> |
| Hossz megkötések |<ul> <li>Teljes hossza nem haladhatja meg a 113 karaktert</li><li>64 karakter hosszúságú lehet, mielőtt a "@" szimbólummal</li><li>48 karakter után a "@" szimbólummal</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Jelszó-házirenddel, csak felhőalapú felhasználói fiókokhoz

A következő táblázat ismerteti a rendelkezésre álló jelszó házirend-beállításokat felhasználói fiókokhoz, amelyek létrehozása és kezelése az Azure AD alkalmazható.

| Tulajdonság | Követelmények |
| --- | --- |
| Karakterből állhat |<ul><li>A – Z</li><li>a - z-ig</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Karakterek nem használhatók. |<ul><li>Unicode-karaktereket</li><li>Tárolóhelyek</li><li> **Csak az erős jelszavak**: egy karaktersor nem tartalmazhat "." közvetlenül megelőző a "@" szimbólummal</li></ul> |
| Jelszó-korlátozások |<ul><li>minimum 8 karakter, és legfeljebb 16 karakter</li><li>**Csak az erős jelszavak**: 3 igényel a következő 4-ből:<ul><li>Kisbetűk</li><li>Nagybetűk</li><li>Számok (0-9)</li><li>Szimbólumok (lásd a fenti jelszó korlátozások)</li></ul></li></ul> |
| Jelszavak érvényességi időtartamát |<ul><li>Alapértelmezett érték: **90** nap </li><li>Érték használatával lehet konfigurálni az Active Directory modul Windows Powershellhez készült Azure a Set-MsolPasswordPolicy parancsmagjával.</li></ul> |
| Jelszó lejáratáról szóló értesítés |<ul><li>Alapértelmezett érték: **14** napig (lejárt jelszó)</li><li>Érték használatával lehet konfigurálni a Set-MsolPasswordPolicy parancsmagjával.</li></ul> |
| Jelszó lejárata |<ul><li>Alapértelmezett érték: **hamis** nap (azt jelzi, hogy a jelszó lejárati engedélyezve van) </li><li>Érték egyéni felhasználói fiókokat a Set-MsolUser parancsmag használatával konfigurálhatók. </li></ul> |
| Jelszó **módosítása** előzmények |Utolsó jelszó **nem** használható újra amikor **módosítása** jelszót. |
| Jelszó **alaphelyzetbe** előzmények | Utolsó jelszó **előfordulhat, hogy** használható újra amikor **alaphelyzetbe állítása** elfelejtett jelszavát. |
| A Fiókzárolásra |10 sikertelen bejelentkezési kísérlet után (helytelen jelszót) a felhasználó rendszer zárolja egy percig. További helytelen bejelentkezési kísérletek zár ki a felhasználó időtartamának növelése. |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Jelszó lejárati házirendek beállítása az Azure Active Directoryban

Egy Microsoft felhőszolgáltatásra globális rendszergazdája használhatja a Microsoft Active Directory modul Windows Powershellhez készült Azure hozzon létre felhasználói jelszavak nem járnak le. Használhatja a Windows PowerShell-parancsmagokkal eltávolítani az-véglegesek konfigurációs, vagy hogy mely felhasználói jelszavak beállítása nem jár le. Ez az útmutató többi szolgáltatók, például a Microsoft Intune és az Office 365, amely identitás- és a directory Services Microsoft Azure Active Directory is támaszkodjon vonatkozik.

> [!NOTE]
> Csak a címtár-szinkronizálás keresztül nem szinkronizált felhasználói fiókok jelszavainak beállítható úgy, hogy nem jár le. További információ a címtár-szinkronizálás:[AD az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>Állítsa be, vagy ellenőrizze a jelszóházirendek PowerShell használatával

A kezdéshez kell [töltse le és telepítse az Azure AD PowerShell modult](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Ha már van telepítve, akkor is az alábbi lépésekkel konfigurálhatja az egyes mezők.

### <a name="how-to-check-expiration-policy-for-a-password"></a>A jelszó-elévülési szabályzatának ellenőrzése
1. Csatlakozás Windows PowerShell vállalati rendszergazdai hitelesítő adataival.
2. Hajtsa végre a következő parancsok egyikét:

   * Tekintse meg, hogy egyetlen felhasználó jelszavának beállítása nem jár le, futtassa a következő parancsmagot a felhasználó egyszerű felhasználóneve (UPN) használatával (például aprilr@contoso.onmicrosoft.com) vagy a felhasználó azonosítója az ellenőrizni kívánt:`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * A "Jelszó sohasem jár le" beállítást az összes felhasználó megtekintéséhez futtassa a következő parancsmagot:`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Állítsa be a jelszó lejárati dátuma

1. Csatlakozás Windows PowerShell vállalati rendszergazdai hitelesítő adataival.
2. Hajtsa végre a következő parancsok egyikét:

   * Egy felhasználó jelszavát, hogy a jelszó lejárati beállításához futtassa a következő parancsmagot a felhasználó egyszerű felhasználóneve (UPN) használatával vagy a felhasználó felhasználói Azonosítóját:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Az összes olyan felhasználó jelszavának beállítása a szervezeten belül, hogy a várósorban, használja a következő parancsmagot:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Állítsa be a jelszó soha nem jár le

1. Csatlakozás Windows PowerShell vállalati rendszergazdai hitelesítő adataival.
2. Hajtsa végre a következő parancsok egyikét:

   * Soha le nem járó egy felhasználó jelszavának megadásához a következő parancsmag használatával futtassa az egyszerű felhasználónév (UPN) vagy a felhasználó felhasználói Azonosítóját:`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Az összes olyan felhasználó jelszavának beállítása nem jár le egy szervezet, futtassa a következő parancsmagot:`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokat követve az Azure AD jelszóátállításáról olvashat további információkat.

* [Hogyan végezze el a sikeres bevezetéshez az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-best-practices.md)
* [A jelszó megváltoztatására](active-directory-passwords-update-your-own-password.md).
* [Az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).
* [Licencelés kérdése van?](active-directory-passwords-licensing.md)
* [Milyen adatok SSPR használja, és milyen adatokat kell tölteni a felhasználók számára?](active-directory-passwords-data.md)
* [Hitelesítési módszerek állnak rendelkezésre a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [A jelszóvisszaírás és miért fontos információk?](active-directory-passwords-writeback.md)
* [Hogyan jelentést az önkiszolgáló jelszó-Változtatási tevékenység?](active-directory-passwords-reporting.md)
* [Mik az önkiszolgáló jelszó-Változtatási közül az összes, és mit azokat a következőket:?](active-directory-passwords-how-it-works.md)
* [Szerintem valami nem működik. Hogyan hibáinak elhárítása az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-troubleshoot.md)
* [A rendszer nem jelzett valahol máshol kérdést kell](active-directory-passwords-faq.md)

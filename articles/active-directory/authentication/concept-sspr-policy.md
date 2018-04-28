---
title: Az önkiszolgáló jelszó-átállítási házirendek – Azure Active Directoryban
description: Az Azure AD az önkiszolgáló jelszó-változtatási házirend-beállításokban
services: active-directory
keywords: Az Active directory-jelszókezelés, jelszókezelés, az Azure AD self service jelszó alaphelyzetbe állítása
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: b8079c5dffd4d426d62267e77f226d2dda183665
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Jelszóházirendek és -korlátozások az Azure Active Directoryban

Ez a cikk ismerteti a jelszóházirendek és az Azure Active Directory (Azure AD-) bérlőben tárolt felhasználói fiókok társított bonyolultsági követelményeknek.

## <a name="administrator-password-policy-differences"></a>Rendszergazdai jelszó házirend különbségek

A Microsoft érvényesíti erős alapértelmezett *két-kapu* jelszó-visszaállítási házirend bármely Azure rendszergazdai szerepkörhöz. 

A két-kapu házirendnek a rendszergazdák használhatja a biztonsági kérdések nem rendelkezik.

 A két-kapu házirend a hitelesítési adatok, például az e-mail címet két adatokra van szükség *és* egy telefonszámot. A két-kapu házirend vonatkozik, az alábbi esetekben:

* A következő Azure-rendszergazdai szerepkörök érintettek:
  * Segélyszolgálat rendszergazda
  * Szolgáltatás-rendszergazda
  * Számlázási adminisztrátor
  * 1. szintű partnertámogatás
  * 2. szintű partnertámogatás
  * Exchange-szolgáltatások rendszergazdája
  * Lync-szolgáltatások rendszergazdája
  * Felhasználói fiók rendszergazdája
  * Címtárírók
  * Globális rendszergazda vagy a vállalati rendszergazda
  * A SharePoint szolgáltatás-rendszergazda
  * Szabályozási ügyintéző
  * Alkalmazás-rendszergazda
  * Biztonsági rendszergazda
  * Kiemelt szerepkörű rendszergazda
  * A Microsoft Intune szolgáltatás-rendszergazda
  * Application proxy szolgáltatás-rendszergazda
  * CRM szolgáltatás-rendszergazda
  * A Power BI szolgáltatás-rendszergazda
  
* Ha 30 nap eltelt a próba-előfizetésre

  vagy

* A személyes tartomány, például contoso.com

  vagy

* Az Azure AD Connect visszaszinkronizálja a helyszíni címtárban szereplő identitásokat

### <a name="exceptions"></a>Kivételek
Egy egy-kapu házirendhez a hitelesítési adatok, például az e-mail címet egy részét *vagy* telefonszám. Egy egy-kapu házirend vonatkozik, az alábbi esetekben:

* A próba-előfizetés az első 30 napon belül van

  vagy

* A személyes tartomány nincs jelen (*. onmicrosoft.com) 

  és 

  Az Azure AD Connect nem identitások szinkronizálása


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Összes felhasználói fiók UserPrincipalName házirendeket

Összes felhasználói fiók, amely be kell jelentkeznie az Azure AD egy egyedi felhasználói egyszerű felhasználónév (UPN) attribútum értéke annak a fióknak rendelkeznie kell. A következő táblázat vázol fel a házirendeket is a helyszíni Active Directory felhasználói fiókoknak, a felhőhöz, és csak felhőalapú felhasználói fiókokhoz szinkronizált vonatkoznak:

| Tulajdonság | UserPrincipalName követelmények |
| --- | --- |
| Karakterből állhat |<ul> <li>A – Z</li> <li>a - z-ig</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Karakterek nem használhatók. |<ul> <li>A "\@ \" a felhasználónév, a tartomány nem az elválasztó karaktert.</li> <li>A pont karakter nem tartalmazhat "." közvetlenül megelőző a "\@ \" szimbólum</li></ul> |
| Hossz megkötések |<ul> <li>A teljes hossza nem haladhatja meg a 113 karakter</li><li>Mielőtt legfeljebb 64 karakter lehet a "\@ \" szimbólum</li><li>Után legfeljebb 48 karakterből állhat a "\@ \" szimbólum</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Jelszó-házirenddel, csak felhőalapú felhasználói fiókokhoz

A következő táblázat ismerteti a rendelkezésre álló jelszó házirend-beállításokat felhasználói fiókokhoz, amelyek létrehozása és kezelése az Azure AD alkalmazható:

| Tulajdonság | Követelmények |
| --- | --- |
| Karakterből állhat |<ul><li>A – Z</li><li>a - z-ig</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Karakterek nem használhatók. |<ul><li>Unicode-karaktereket.</li><li>Szóközöket.</li><li> Csak az erős jelszavak</li></ul> |
| Jelszó-korlátozások |<ul><li>Legalább 8 karakter, és legfeljebb 16 karakter hosszúságú lehet.</li><li>Csak az erős jelszavak: a következő három kívüli négy igényel:<ul><li>Kisbetűk.</li><li>Nagybetűs karaktereket.</li><li>Számok (0-9).</li><li>A szimbólumok (lásd az előző Jelszó korlátozások).</li></ul></li></ul> |
| Jelszavak érvényességi időtartamát |<ul><li>Alapértelmezett érték: **90** nap.</li><li>Érték konfigurálható használatával a `Set-MsolPasswordPolicy` az Active Directory modul Windows Powershellhez készült Azure parancsmagjával.</li></ul> |
| Jelszó lejáratáról szóló értesítés |<ul><li>Alapértelmezett érték: **14** napig (lejárt jelszó).</li><li>Érték konfigurálható használatával a `Set-MsolPasswordPolicy` parancsmag.</li></ul> |
| Jelszó lejárata |<ul><li>Alapértelmezett érték: **hamis** nap (azt jelzi, hogy a jelszó lejárati engedélyezve van).</li><li>Az érték beállítható az egyes felhasználói fiókok használatával a `Set-MsolUser` parancsmag.</li></ul> |
| Jelszó-változtatási |Az utolsó jelszó *nem* újra használható, ha a felhasználó megváltoztatja a jelszavát. |
| Jelszó-átállítási előzmények | Az utolsó jelszó *is* újra használható, amikor a felhasználó visszaállítja az elfelejtett jelszavát. |
| Fiókzárolás |10 sikertelen bejelentkezési kísérlet után helytelen jelszóval a felhasználó zárolva van egy percig. További helytelen bejelentkezési kísérletek zár ki a felhasználói idő időtartamok növelése. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Jelszó lejárati házirendek beállítása az Azure ad-ben

Egy Microsoft felhőszolgáltatásra globális rendszergazdának a Microsoft Azure AD-modullal a Windows PowerShell segítségével állítsa be a felhasználói jelszavakat nem jár le. Használhatja a Windows PowerShell-parancsmagokkal eltávolítani az-véglegesek-konfiguráció, vagy hogy mely felhasználói jelszavak beállítása nem jár le. 

Ez az útmutató más szolgáltatók, köztük a Intune és az Office 365, amely identitás- és a directory services Azure ad-val is támaszkodjon vonatkozik. Jelszó lejárata része a csak a házirendet, amely módosíthatja.

> [!NOTE]
> Csak a címtár-szinkronizálás keresztül nem szinkronizált felhasználói fiókok jelszavainak beállítható úgy, hogy nem jár le. További információ a címtár-szinkronizálás: [AD az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Állítsa be, vagy ellenőrizze a jelszóházirendek PowerShell használatával

A kezdéshez kell [töltse le és telepítse az Azure AD PowerShell modult](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Után van telepítve, a következő lépések segítségével konfigurálhatja az egyes mezők.

### <a name="how-to-check-the-expiration-policy-for-a-password"></a>A jelszó-elévülési szabályzatának ellenőrzése
1. A Windows PowerShell csatlakozni a vállalati rendszergazda hitelesítő adataival.
2. Hajtsa végre a következő parancsok egyikét:

   * Ha egy felhasználó jelszó soha nem jár le van állítva megtekintéséhez futtassa a következő parancsmag által megadott egyszerű felhasználónév használatával (például *aprilr@contoso.onmicrosoft.com*) vagy a felhasználó azonosítója az ellenőrizni kívánt: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Hogy a **jelszó sohasem jár le** beállítása az összes felhasználó számára, futtassa az alábbi parancsmagot: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Állítsa be a jelszó lejárati dátuma

1. A Windows PowerShell csatlakozni a vállalati rendszergazda hitelesítő adataival.
2. Hajtsa végre a következő parancsok egyikét:

   * Egy felhasználó jelszavának megadásához, hogy a jelszó lejárati az egyszerű felhasználónév vagy a felhasználó a felhasználói azonosító használatával futtassa a következő parancsmagot: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Az összes olyan felhasználó jelszavának beállítása a szervezeten belül, hogy a várósorban, használja a következő parancsmagot: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Állítsa be a jelszó soha nem jár le

1. A Windows PowerShell csatlakozni a vállalati rendszergazda hitelesítő adataival.
2. Hajtsa végre a következő parancsok egyikét:

   * A jelszó soha nem jár le egy felhasználó beállítását, az egyszerű felhasználónév vagy a felhasználó a felhasználói azonosító használatával futtassa az alábbi parancsmagot: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Az összes olyan felhasználó jelszavának beállítása nem jár le egy szervezet, futtassa a következő parancsmagot: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > Jelszavak beállítása `-PasswordNeverExpires $true` életkor alapján még a `pwdLastSet` attribútum. Beállíthatja a felhasználói jelszavakat nem jár le, és 90 nap folytassa, ha a jelszavak lejárnak. Alapján a `pwdLastSet` attribútumot, ha módosítja a lejárati `-PasswordNeverExpires $false`, jelszavak, amelyek rendelkeznek egy `pwdLastSet` régebbi, mint 90 nappal a felhasználónak a módosításukra, a következő bejelentkezéskor. Ez a módosítás hatással lehet a felhasználók nagy számú. 

## <a name="next-steps"></a>További lépések

A következő cikkekben talál további információt nyújtanak azokról az Azure AD használatával új jelszó.

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Új jelszó kérése vagy jelszó módosítása](../active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](../active-directory-passwords-reset-register.md).
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

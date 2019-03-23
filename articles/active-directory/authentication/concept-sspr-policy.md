---
title: Az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása a házirendek – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállítási házirend-beállítások konfigurálása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d9055ef11bc5c117efc6d4de87d4ca8ec73a661
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360503"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Jelszóházirendek és -korlátozások az Azure Active Directoryban

Ez a cikk ismerteti a jelszóházirendek és -felhasználói fiókok az Azure Active Directory (Azure AD) bérlő a társított összetettségi követelményeknek.

## <a name="administrator-reset-policy-differences"></a>Rendszergazda, alaphelyzetbe állítása házirend különbségek

**Microsoft erős alapértelmezett szigorú *két-kapu* jelszó-visszaállítási házirendjének minden olyan Azure-rendszergazdai szerepkörhöz** ezt a szabályzatot a felhasználók számára megadott, és nem módosítható a eltérő lehet. Mindig tesztelje felhasználói jelszó-átállítási funkcióra nélkül bármely Azure-rendszergazdai szerepkörök rendelve.

Az a két-kezdő szabályzat **rendszergazdák nem rendelkezik a biztonsági kérdések használata**.

A két-kezdő szabályzat adatokra van szükség két hitelesítési adatok, például egy **e-mail-cím**, **hitelesítő alkalmazás**, vagy egy **telefonszám**. A két-kezdő szabályzat érvényesül, a következő körülmények között:

* A következő Azure-rendszergazdai szerepkörök érintettek:
  * Ügyfélszolgálati adminisztrátor
  * Szolgáltatástámogatási rendszergazda
  * Számlázási rendszergazda
  * 1. szintű partnertámogatás
  * 2. szintű partnertámogatás
  * Exchange-rendszergazda
  * Skype Vállalati verzió-rendszergazda
  * Felhasználói adminisztrátor
  * Címtárírók
  * Globális rendszergazda vagy a vállalati rendszergazda
  * SharePoint-rendszergazda
  * Szabályozási ügyintéző
  * Alkalmazás-rendszergazda
  * Biztonsági rendszergazda
  * Kiemelt szerepkörű rendszergazda
  * Intune-rendszergazda
  * Alkalmazásproxy szolgáltatásadminisztrátora
  * Dynamics 365-rendszergazda
  * A Power BI-szolgáltatásadminisztrátor
  * Hitelesítési rendszergazda
  * A kiemelt hitelesítést rendszergazda

* Ha a 30 nap eltelt a egy próbaverziós előfizetés; vagy
* Egy személyes tartomány jelen, mint például a contoso.com; vagy
* Az Azure AD Connect visszaszinkronizálja identitásokat a helyszíni címtárból

### <a name="exceptions"></a>Kivételek

Egy egy-kezdő szabályzat megköveteli a hitelesítési adatok, például egy e-mail-cím, egy darab *vagy* telefonszám. Egy egy-kezdő szabályzat érvényesül, a következő körülmények között:

* Próba-előfizetés; az első 30 napján belül vagy
* Egy személyes tartomány nem lesznek jelen (*. onmicrosoft.com); és
* Az Azure AD Connect nem szinkronizál identitásokat

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName házirendek vonatkoznak az összes felhasználói fiók

Minden felhasználói fiók, amelyet az Azure AD-bejelentkezés rendelkeznie kell a fiókhoz társított egyedi felhasználói egyszerű felhasználónév (UPN) attribútum értékét. Az alábbi táblázat ismerteti a házirendekben, amelyek mind a helyszíni Active Directory felhasználói fiókoknak, szinkronizált a felhőbe, és csak felhőalapú felhasználói fiókokhoz érvényesek:

| Tulajdonság | UserPrincipalName követelmények |
| --- | --- |
| Karakterek használhatók |<ul> <li>A – Z</li> <li>a – z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Karakterek nem használhatók |<ul> <li>Bármely "\@ \" karakter, amely nem van elválasztva a tartomány felhasználónév.</li> <li>A pont karakter nem tartalmazhat "." közvetlenül a "\@ \" szimbólum</li></ul> |
| Megkötés hossz alapján |<ul> <li>A teljes hossza nem haladhatja meg a 113 karaktert</li><li>Mielőtt legfeljebb 64 karakter lehet a "\@ \" szimbólum</li><li>Után legfeljebb 48 karakterből állhat a "\@ \" szimbólum</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Jelszó-házirenddel, csak felhőalapú felhasználói fiókok

A következő táblázat ismerteti a felhasználói fiókok létrehozása és felügyelete az Azure ad-ben, a alkalmazni jelszóval szabályzatbeállítások:

| Tulajdonság | Követelmények |
| --- | --- |
| Karakterek használhatók |<ul><li>A – Z</li><li>a – z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ " ( ) ;</li></ul> |
| Karakterek nem használhatók |<ul><li>Unicode-karaktereket.</li><li>Tárolóhelyek.</li><li> A pont karakter nem tartalmazhat "." közvetlenül a "\@ \" szimbólum".</li></ul> |
| Jelszó-korlátozások |<ul><li>Legalább 8 karakter és legfeljebb 16 karakter.</li><li>Az alábbi három közül négy van szükség:<ul><li>Kisbetűs karaktert.</li><li>Nagybetűs karaktereket.</li><li>Számok (0 – 9).</li><li>A szimbólumok (lásd az előző Jelszó korlátozásai).</li></ul></li></ul> |
| Jelszó lejárati időtartama |<ul><li>Alapértelmezett érték: **90** nap.</li><li>Az érték konfigurálható használatával a `Set-MsolPasswordPolicy` az Azure Active Directory modul a Windows PowerShell parancsmagot.</li></ul> |
| Jelszó lejáratáról szóló értesítés |<ul><li>Alapértelmezett érték: **14** nap (elteltével jelszó lejár).</li><li>Az érték konfigurálható használatával a `Set-MsolPasswordPolicy` parancsmagot.</li></ul> |
| Jelszó lejárata |<ul><li>Alapértelmezett érték: **hamis** nap (azt jelzi, hogy a jelszó lejárati engedélyezve van).</li><li>Az érték használatával konfigurálhatók az egyes felhasználói fiókok esetében a `Set-MsolUser` parancsmagot.</li></ul> |
| Jelszó-változások nyomon követése |A legutóbbi jel *nem* újra használni, amikor a felhasználó megváltoztatja a jelszót. |
| Jelszó-visszaállítási előzmények | A legutóbbi jel *is* újra használni, amikor a felhasználó elfelejtett jelszó alaphelyzetbe állítása. |
| Fiókzárolás |10 sikertelen bejelentkezési kísérlet után a helytelen jelszóval a felhasználó a rendszer zárolja egy percet. További helytelen bejelentkezési kísérletek növelése az idő időtartamok zár ki a felhasználót. [Intelligens zárolás](howto-password-smart-lockout.md) nyomon követi az utolsó három hibás jelszókivonatokat elkerülése érdekében ugyanazt a jelszót a fiókzárolási számláló növekszik. Ha valaki több alkalommal belép a rossz jelszót, ez a viselkedés nem okoz a fiók zárolása. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Jelszóelévülési házirendek beállítása az Azure ad-ben

Egy globális rendszergazdai vagy a felhasználói a Microsoft felhőalapú szolgáltatás segítségével a Microsoft Azure AD-modul Windows Powershellhez készült állítsa be a felhasználói jelszavakat nem jár le. Távolítsa el a Windows PowerShell-parancsmagok is használhatja a-JE neomezená konfigurációs, vagy hogy mely felhasználói jelszavak vannak beállítva, hogy soha ne járjon le. 

Ez az útmutató más szolgáltatók, például az Intune és az Office 365, Azure AD-identitás- és címtárszolgáltatásokat is támaszkodik, amely vonatkozik. Jelszó lejárati idejét a módosítható házirend csak egy része.

> [!NOTE]
> Csak a címtár-szinkronizálás – nem szinkronizált felhasználói fiókok jelszavainak beállítható úgy, hogy nem járnak le. További információ a címtár-szinkronizálás: [AD az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Állítsa be, vagy ellenőrizze a jelszóházirendek PowerShell-lel

Első lépésként kell [töltse le és telepítse az Azure AD PowerShell modul](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Ha már van telepítve, a következő lépéseket segítségével konfigurálhatja az egyes mezők.

### <a name="check-the-expiration-policy-for-a-password"></a>A jelszó-elévülési szabályzatának ellenőrzése

1. Csatlakozás Windows PowerShell a felhasználó rendszergazda vagy a vállalati rendszergazdai hitelesítő adatok használatával.
1. Hajtsa végre a következő parancsok egyikét:

   * Ha soha ne járjon le a felhasználóhoz tartozó jelszó értéke megtekintéséhez futtassa az alábbi parancsmagot az egyszerű felhasználónév használatával (például *aprilr\@contoso.onmicrosoft.com*) vagy a felhasználó ellenőrizni kívánja a felhasználói azonosító: `Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`
   * Megtekintheti a **jelszó sohasem jár le** beállítás az összes felhasználó számára, futtassa a következő parancsmagot: `Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`

### <a name="set-a-password-to-expire"></a>Állítsa be a jelszó lejár

1. Csatlakozás Windows PowerShell a felhasználó rendszergazda vagy a vállalati rendszergazdai hitelesítő adatok használatával.
1. Hajtsa végre a következő parancsok egyikét:

   * Egy felhasználó jelszavának beállításához, úgy, hogy a jelszó lejár, az egyszerű felhasználónév vagy a felhasználó a felhasználói azonosító használatával futtassa a következő parancsmagot: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None`
   * Az összes olyan felhasználó jelszavának beállítása a szervezet úgy, hogy a lejárat után, használja a következő parancsmagot: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None`

### <a name="set-a-password-to-never-expire"></a>Soha ne járjon le a jelszó beállítása

1. Csatlakozás Windows PowerShell a felhasználó rendszergazda vagy a vállalati rendszergazdai hitelesítő adatok használatával.
1. Hajtsa végre a következő parancsok egyikét:

   * Egy felhasználó soha ne járjon le a jelszó beállítása, az egyszerű felhasználónév vagy a felhasználó a felhasználói azonosító használatával futtassa a következő parancsmagot: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration`
   * Az összes olyan felhasználó jelszavának beállítása soha ne járjon le a szervezetekben, futtassa a következő parancsmagot: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration`

   > [!WARNING]
   > Jelszavak beállítása `-PasswordPolicies DisablePasswordExpiration` életkor alapján még a `pwdLastSet` attribútum. Ha soha ne járjon le a felhasználói jelszavakat, és 90 napig folytassa, a jelszavak lejárnak. Alapján a `pwdLastSet` attribútumot, ha módosítja a lejárat `-PasswordPolicies None`, minden jelszót, amely rendelkezik egy `pwdLastSet` régebbi, mint 90 nappal a felhasználónak a következő bejelentkezéskor módosítania őket. Ez a módosítás hatással lehet a felhasználók nagy száma. 

## <a name="next-steps"></a>További lépések

A következő cikkek nyújtanak jelszó-visszaállítás, az Azure AD-n keresztül további információt:

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Új jelszó kérése vagy jelszó módosítása](../user-help/active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](../user-help/active-directory-passwords-reset-register.md).
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

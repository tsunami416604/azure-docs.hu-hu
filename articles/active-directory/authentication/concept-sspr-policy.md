---
title: Önkiszolgáló jelszó-visszaállítási szabályzatok – Azure Active Directory
description: Tudnivalók a különböző Azure Active Directory önkiszolgáló jelszó-visszaállítási házirend-beállításokról
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8b6d08dd2073de80ac0f7fd08f510d9cda80545
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143234"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Önkiszolgáló jelszó-visszaállítási szabályzatok és korlátozások a Azure Active Directory

Ez a cikk a Azure Active Directory (Azure AD) bérlő felhasználói fiókjaihoz társított jelszóházirend-és összetettségi követelményeket ismerteti.

## <a name="administrator-reset-policy-differences"></a>A rendszergazdai visszaállítási szabályzat eltérései

A **Microsoft minden Azure-beli rendszergazdai szerepkör esetében kikényszeríti az alapértelmezett *kétkapus jelszó-* visszaállítási szabályzatot**. Ez a szabályzat különbözhet a felhasználók számára megadott beállításoktól, és ez a szabályzat nem módosítható. A jelszó-visszaállítási funkciót mindig a felhasználó által hozzárendelt Azure rendszergazdai szerepkörök nélkül kell tesztelni.

A rendszergazdák kétkapus szabályzattal **nem tudják használni a biztonsági kérdéseket**.

A kétkapus szabályzathoz két hitelesítési adat szükséges, például *e-mail-cím*, *hitelesítő alkalmazás*vagy *telefonszám*. Kétkapus szabályzat a következő esetekben érvényes:

* A rendszer az alábbi Azure-rendszergazdai szerepköröket érinti:
  * Segélyszolgálat rendszergazdája
  * Szolgáltatás-rendszergazda
  * Számlázási rendszergazda
  * Partneri Tier1-támogatás
  * Partneri szint-támogatás
  * Exchange-rendszergazda
  * Skype Vállalati verzió-rendszergazda
  * Felhasználói rendszergazda
  * Címtár-írók
  * Globális rendszergazda vagy vállalati rendszergazda
  * SharePoint-rendszergazda
  * Megfelelőségi rendszergazda
  * Alkalmazás-rendszergazda
  * Biztonsági rendszergazda
  * Kiemelt szerepkörű rendszergazda
  * Intune-rendszergazda
  * Alkalmazásproxy szolgáltatás rendszergazdája
  * Dynamics 365-rendszergazda
  * Power BI-szolgáltatásadminisztrátor
  * Hitelesítés rendszergazdája
  * Kiemelt jogosultságú hitelesítés rendszergazdája

* Ha 30 nap telt el egy próbaverziós előfizetésben; vagy
* Egyéni tartomány lett konfigurálva az Azure AD-bérlőhöz, például *contoso.com*; vagy
* Azure AD Connect az identitások szinkronizálása a helyszíni címtárból

### <a name="exceptions"></a>Kivételek

Az egy-kapus szabályzathoz egy hitelesítő adat szükséges, például egy e-mail-cím vagy telefonszám. Az egykapus szabályzat a következő esetekben érvényes:

* A próbaverziós előfizetés első 30 napján belül van; vagy
* Nincs beállítva egyéni tartomány az Azure AD-bérlőhöz, ezért az alapértelmezett **. onmicrosoft.com*használja. Az alapértelmezett **. onmicrosoft.com* tartomány nem ajánlott éles használatra; és
* Az Azure AD Connect nem szinkronizálja az identitásokat

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Az összes felhasználói fiókra érvényes UserPrincipalName szabályzatok

Minden, az Azure AD-be bejelentkezni kívánt felhasználói fióknak rendelkeznie kell egy egyedi egyszerű felhasználónév (UPN) attribútum értékkel, amely hozzá van rendelve a fiókhoz. A következő táblázat azokat a házirendeket ismerteti, amelyek a felhőben és a csak felhőalapú felhasználói fiókokba szinkronizált helyszíni Active Directory tartományi szolgáltatások felhasználói fiókokra vonatkoznak:

| Tulajdonság | UserPrincipalName-követelmények |
| --- | --- |
| Engedélyezett karakterek |<ul> <li>A – Z</li> <li>a – z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Nem engedélyezett karakterek |<ul> <li>\@ \" Bármely olyan karakter, amely nem választja el a felhasználónevet a tartományból.</li> <li>A "." karakter nem szerepelhet közvetlenül a "\@ \" Symbol</li></ul> |
| Megkötések hossza |<ul> <li>A teljes hossz nem haladhatja meg a 113 karaktert</li><li>A "\@ \" szimbólum előtt legfeljebb 64 karakter lehet.</li><li>A "\@ \" szimbólum után legfeljebb 48 karakter lehet.</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>A csak a felhőbeli felhasználói fiókokra vonatkozó jelszószabályzatok

A következő táblázat az Azure AD-ben létrehozott és kezelt felhasználói fiókokra vonatkozó jelszóházirend-beállításokat ismerteti:

| Tulajdonság | Követelmények |
| --- | --- |
| Engedélyezett karakterek |<ul><li>A – Z</li><li>a – z</li><li>0 – 9</li> <li>@ # $% ^ & *-_! + = [] {} &#124; \: ',. ? / \`~ " ( ) ;</li> <li>üres terület</li></ul> |
| Nem engedélyezett karakterek | Unicode-karakterek. |
| Jelszó korlátozásai |<ul><li>Legalább 8 karakterből és legfeljebb 256 karakterből állhat.</li><li>A három közül a következők közül hármat igényel:<ul><li>Kisbetűs karakterek.</li><li>Nagybetűs karakterek.</li><li>Számok (0-9).</li><li>Szimbólumok (lásd a jelszó korábbi korlátozásait).</li></ul></li></ul> |
| Jelszó lejárati időtartama (jelszó maximális kora) |<ul><li>Alapértelmezett érték: **90** nap.</li><li>Az érték konfigurálható a Windows PowerShell Azure Active Directory `Set-MsolPasswordPolicy` moduljának parancsmagjának használatával.</li></ul> |
| Jelszó lejárati értesítése (ha a felhasználók értesítést kapnak a jelszó lejáratáról) |<ul><li>Alapértelmezett érték: **14** nap (a jelszó lejárata előtt).</li><li>Az érték a `Set-MsolPasswordPolicy` parancsmag használatával konfigurálható.</li></ul> |
| Jelszó lejárata (hagyja, hogy a jelszavak soha ne járjanak le) |<ul><li>Alapértelmezett érték: **false** (azt jelzi, hogy a jelszó lejárati dátuma).</li><li>Az érték egyéni felhasználói fiókokhoz is konfigurálható a `Set-MsolUser` parancsmag használatával.</li></ul> |
| Jelszó-módosítási előzmények | Az utolsó jelszó *nem* használható újra, amikor a felhasználó megváltoztatja a jelszót. |
| Jelszó-visszaállítási előzmények | Az *utolsó jelszó újra használható,* amikor a felhasználó visszaállít egy elfelejtett jelszót. |
| Fiókzárolási | 10 sikertelen bejelentkezési kísérlet után a rendszer egy percig kizárja a felhasználót. A további helytelen bejelentkezési kísérletek miatt a felhasználó kizárja az időtartam növelését. Az [intelligens zárolási](howto-password-smart-lockout.md) szolgáltatás az utolsó három rossz jelszó-kivonatot követi, így elkerülhető, hogy a zárolási számláló ugyanazon a jelszónál legyen növelve. Ha valaki többször is ugyanazt a jelszót adja meg, akkor ez a viselkedés nem eredményezi a fiók zárolását. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Jelszó-elévülési szabályzatok beállítása az Azure AD-ben

A Microsoft Cloud Service *globális rendszergazdája* vagy *felhasználói rendszergazdája* a *Windows PowerShell Microsoft Azure ad moduljának* használatával beállíthatja, hogy a felhasználói jelszavak ne járjanak le. A Windows PowerShell-parancsmagok használatával eltávolíthatja a soha nem lejáró konfigurációt, vagy megtekintheti, hogy a rendszer mely felhasználói jelszavakat állítsa le soha nem jár le.

Ez az útmutató más szolgáltatókra is vonatkozik, például az Intune-ra és az Office 365-re, amely az identitás-és Címtárszolgáltatások Azure AD-szolgáltatására is támaszkodik. A jelszó lejárata a szabályzat egyetlen része, amely módosítható.

> [!NOTE]
> Csak a címtár-szinkronizálással szinkronizált felhasználói fiókok jelszavai konfigurálhatók úgy, hogy ne járjanak le. További információ a címtár-szinkronizálásról: az [ad és az Azure ad közötti kapcsolat](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Jelszószabályzatok beállítása vagy ellenőrzése a PowerShell-lel

Első lépésként [töltse le és telepítse az Azure ad PowerShell-modult](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0) , és [kapcsolja össze az Azure ad-Bérlővel](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples). A modul telepítése után a következő lépésekkel konfigurálhatja az egyes mezőket.

### <a name="check-the-expiration-policy-for-a-password"></a>A jelszó elévülési szabályzatának keresése

1. Kapcsolódjon a Windows PowerShellhez a felhasználói rendszergazda vagy a vállalati rendszergazda hitelesítő adataival.
1. Futtassa a következő parancsok egyikét:

   * Ha szeretné megtekinteni, hogy egy adott felhasználó jelszava úgy van-e beállítva, hogy soha ne járjon le, futtassa a következő parancsmagot az egyszerű felhasználónév (például: *april\@contoso.onmicrosoft.com*) vagy annak a felhasználónak az azonosítójának használatával, amelyet meg szeretne nézni:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Ha látni szeretné, hogy a **jelszó soha ne járjon le** minden felhasználónál, futtassa a következő parancsmagot:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>A lejárati jelszó beállítása

1. Kapcsolódjon a Windows PowerShellhez a felhasználói rendszergazda vagy a vállalati rendszergazda hitelesítő adataival.
1. Hajtsa végre a következő parancsok egyikét:

   * Ha egy felhasználó jelszavát szeretné beállítani, hogy a jelszó lejárjon, futtassa a következő parancsmagot a felhasználó UPN vagy felhasználói AZONOSÍTÓjának használatával:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * A szervezetben lévő összes felhasználó jelszavának beállításához használja a következő parancsmagot:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Jelszó beállítása soha nem jár le

1. Kapcsolódjon a Windows PowerShellhez a felhasználói rendszergazda vagy a vállalati rendszergazda hitelesítő adataival.
1. Hajtsa végre a következő parancsok egyikét:

   * Ha az egyik felhasználó jelszavát szeretné beállítani, hogy soha ne járjon le, futtassa a következő parancsmagot a felhasználó UPN vagy felhasználói AZONOSÍTÓjának használatával:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Ha egy szervezet összes felhasználójának jelszavát szeretné beállítani, hogy soha ne járjon le, futtassa a következő parancsmagot:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > A jelszavak az `-PasswordPolicies DisablePasswordExpiration` `pwdLastSet` attribútum alapján még mindig korra vannak állítva. Ha az `pwdLastSet` attribútum alapján módosítja a lejárati időt `-PasswordPolicies None`, a 90 napnál `pwdLastSet` régebbi jelszavakhoz a felhasználónak a következő bejelentkezéskor módosítania kell a felhasználókat. Ez a változás nagy mennyiségű felhasználót érinthet.

## <a name="next-steps"></a>További lépések

A SSPR használatának megkezdéséhez tekintse meg az [oktatóanyag: a felhasználók számára a fiók zárolásának feloldását vagy a jelszavak visszaállítását Azure Active Directory önkiszolgáló jelszó-visszaállítással](tutorial-enable-sspr.md).

Ha Ön vagy a felhasználók problémákat tapasztalnak a SSPR kapcsolatban, olvassa el az [önkiszolgáló jelszó-visszaállítás hibaelhárítása](active-directory-passwords-troubleshoot.md) című témakört.

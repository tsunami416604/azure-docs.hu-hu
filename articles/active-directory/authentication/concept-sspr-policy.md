---
title: Önkiszolgáló jelszó-visszaállítási házirendek – Azure Active Directory
description: Ismerje meg az Azure Active Directory önkiszolgáló jelszó-alaphelyzetbe állítási házirendjének különböző beállításait
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
ms.openlocfilehash: 5f5987bee82dc22d3742cb5d87040930e5d2c52d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393039"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Önkiszolgáló jelszó-alaphelyzetbe állítási házirendek és korlátozások az Azure Active Directoryban

Ez a cikk ismerteti a jelszó szabályzatok és összetettségi követelmények az Azure Active Directory (Azure AD) bérlőben társított felhasználói fiókok.

## <a name="administrator-reset-policy-differences"></a>A rendszergazdai visszaállítási szabályzat eltérései

**A Microsoft erős alapértelmezett *kétkapus jelszó-visszaállítási* szabályzatot kényszerít ki bármely Azure-rendszergazdai szerepkörhöz.** Ez a házirend eltérhet a felhasználók számára definiált házirendtől, és ez a házirend nem módosítható. Mindig tesztelje a jelszó-visszaállítási funkciót felhasználóként anélkül, hogy Azure-rendszergazdai szerepkörök et rendelne hozzá.

A kétkapus házirend esetén **a rendszergazdák nem használhatják a biztonsági kérdéseket.**

A kétkapus házirendhez két hitelesítési adat szükséges, például egy *e-mail-cím,* *egy hitelesítő alkalmazás*vagy egy *telefonszám.* A kétkapus szabályzat a következő esetekben alkalmazandó:

* A következő Azure-rendszergazdai szerepkörök mindegyike érintett:
  * Ügyfélszolgálati rendszergazda
  * Szolgáltatás-rendszergazda
  * Számlázási rendszergazda
  * Partner tier1 támogatás
  * Partner tier2 támogatás
  * Exchange-rendszergazda
  * Skype Vállalati verzió-rendszergazda
  * Rendszergazda
  * Címtár írók
  * Globális rendszergazda vagy vállalati rendszergazda
  * SharePoint-rendszergazda
  * Megfelelőségi rendszergazda
  * Alkalmazás-rendszergazda
  * Biztonsági rendszergazda
  * Kiemelt szerepkör-rendszergazda
  * Intune-rendszergazda
  * Alkalmazásproxy-szolgáltatás rendszergazdája
  * A Dynamics 365 rendszergazdája
  * Power BI-szolgáltatásadminisztrátor
  * Hitelesítési rendszergazda
  * Kiemelt hitelesítési rendszergazda

* Ha 30 nap telt el próba-előfizetés; Vagy
* Egyéni tartomány van konfigurálva az Azure AD-bérlőhöz, például *contoso.com;* Vagy
* Az Azure AD Connect szinkronizálja az identitásokat a helyszíni címtárból

### <a name="exceptions"></a>Kivételek

Az egykapus házirendhez egy hitelesítési adat szükséges, például egy e-mail cím vagy telefonszám. Az egykapus szabályzat a következő esetekben alkalmazandó:

* Ez a próba-előfizetés első 30 napán belül van; Vagy
* Az Azure AD-bérlőhöz nincs egyéni tartomány konfigurálva, így az alapértelmezett **.onmicrosoft.com*. Az alapértelmezett **.onmicrosoft.com* tartomány nem ajánlott éles használatra; És
* Az Azure AD Connect nem szinkronizálja az identitásokat

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Az összes felhasználói fiókra vonatkozó UserPrincipalName házirendek

Minden felhasználói fiók, amelynek be kell jelentkeznie az Azure AD-ben rendelkeznie kell egy egyedi egyszerű felhasználónév (UPN) attribútum érték társítva a fiókhoz. Az alábbi táblázat ismerteti azokat a házirendeket, amelyek a felhővel és a csak felhőalapú felhasználói fiókokkal szinkronizált helyszíni Active Directory tartományi szolgáltatások felhasználói fiókokra egyaránt vonatkoznak:

| Tulajdonság | UserPrincipalName követelmények |
| --- | --- |
| Megengedett karakterek |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Karakterek nem engedélyezettek |<ul> <li>Bármely\@ \" " karakter, amely nem választja el a felhasználónevet a tartománytól.</li> <li>Nem tartalmazhat pontkaraktert "." közvetlenül\@ \" a " szimbólum ot megelőzően</li></ul> |
| Hossz-megkötések |<ul> <li>A teljes hossz nem haladhatja meg a 113 karaktert.</li><li>Legfeljebb 64 karakter lehet a\@ \" " szimbólum előtt</li><li>Legfeljebb 48 karakter lehet a\@ \" " szimbólum után</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>A csak a felhőbeli felhasználói fiókokra vonatkozó jelszószabályzatok

Az alábbi táblázat az Azure AD-ben létrehozott és kezelt felhasználói fiókokra alkalmazott jelszóházirend-beállításokat ismerteti:

| Tulajdonság | Követelmények |
| --- | --- |
| Megengedett karakterek |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \`~ " ( ) ;</li> <li>üres terület</li></ul> |
| Karakterek nem engedélyezettek | Unicode karakterek. |
| Jelszókorlátozások |<ul><li>Legalább 8 karakter, de legfeljebb 256 karakter.</li><li>Az alábbiak közül négyből hármat igényel:<ul><li>Kisbetűk.</li><li>Nagybetűk.</li><li>Számok (0-9).</li><li>Szimbólumok (lásd a korábbi jelszókorlátozásokat).</li></ul></li></ul> |
| A jelszó lejárati ideje (A jelszó maximális élettartama) |<ul><li>Alapértelmezett érték: **90** nap.</li><li>Az érték konfigurálható az `Set-MsolPasswordPolicy` Azure Active Directory modul windows PowerShell hez parancsmag használatával.</li></ul> |
| Jelszó lejárati értesítése (Ha a felhasználók értesítést kapnak a jelszó lejáratáról) |<ul><li>Alapértelmezett érték: **14** nap (a jelszó lejárta előtt).</li><li>Az érték a `Set-MsolPasswordPolicy` parancsmag használatával konfigurálható.</li></ul> |
| A jelszó lejárata (A jelszavak soha nem járnak le) |<ul><li>Alapértelmezett érték: **false** (azt jelzi, hogy a jelszó lejárati dátummal rendelkezik).</li><li>Az érték a parancsmag használatával konfigurálható az `Set-MsolUser` egyes felhasználói fiókokhoz.</li></ul> |
| Jelszómódosítási előzmények | Az utolsó jelszó nem használható *újra,* amikor a felhasználó megváltoztatja a jelszót. |
| Jelszó-visszaállítási előzmények | Az utolsó jelszó újra *használható,* amikor a felhasználó visszaállítja az elfelejtett jelszót. |
| Fiók zárolása | 10 sikertelen bejelentkezési kísérlet után a felhasználó egy percre zárolva van. További helytelen bejelentkezési kísérletek zár ki a felhasználó tág időtartamok. [Az intelligens zárolás](howto-password-smart-lockout.md) nyomon követi az utolsó három rossz jelszókimondottakat, hogy elkerülje a zárolási számláló növekményét ugyanahhoz a jelszóhoz. Ha valaki többször is beírja ugyanazt a rossz jelszót, ez a viselkedés nem zárja ki a fiókot. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Jelszóelévési szabályzatok beállítása az Azure AD-ben

A Microsoft felhőszolgáltatás *globális rendszergazdája* vagy *felhasználói rendszergazdája* a *Microsoft Azure AD Modul a Windows PowerShell hez* segítségével beállíthatja a felhasználói jelszavak at, hogy ne járjon le. A Windows PowerShell-parancsmagokkal eltávolíthatja a soha le nem járt konfigurációt, vagy megtekintheti, hogy mely felhasználói jelszavak vannak beállítva, hogy soha nem járnak le.

Ez az útmutató más szolgáltatókra, például az Intune-ra és az Office 365-re vonatkozik, amelyek az Azure AD-t is igénybe veszik az identitás- és címtárszolgáltatásokhoz. A jelszó lejárata a házirend egyetlen módosítható része.

> [!NOTE]
> Csak a címtár-szinkronizálással nem szinkronizált felhasználói fiókok jelszavai állíthatók be úgy, hogy azok ne járjanak le. A címtár-szinkronizálásról az [AD csatlakoztatása az Azure AD-vel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)című témakörben talál további információt.

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Jelszószabályzatok beállítása vagy ellenőrzése a PowerShell-lel

Első lépésekhez [töltse le és telepítse az Azure AD PowerShell-modult.](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0) A modul telepítése után az alábbi lépésekkel konfigurálhatja az egyes mezőket.

### <a name="check-the-expiration-policy-for-a-password"></a>Jelszó ellenőrzése az elévülési szabályzatban

1. Csatlakozzon a Windows PowerShellhez a felhasználói rendszergazda vagy a vállalati rendszergazdai hitelesítő adatok használatával.
1. Futtassa az alábbi parancsok egyikét:

   * Ha meg szeretné tudni, hogy egyetlen felhasználó jelszava soha nem jár le, futtassa a következő parancsmalát az egyszerű felhasználónév (például *\@aprilr contoso.onmicrosoft.com)* vagy az ellenőrizni kívánt felhasználó felhasználói azonosítójával:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Ha látni szeretné, hogy a **Jelszó soha nem jár le** az összes felhasználó számára, futtassa a következő parancsmast:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Jelszó bebeállítása a lejáratra

1. Csatlakozzon a Windows PowerShellhez a felhasználói rendszergazda vagy a vállalati rendszergazdai hitelesítő adatok használatával.
1. Hajtsa végre az alábbi parancsok egyikét:

   * Ha egy felhasználó jelszavát úgy szeretné beállítani, hogy a jelszó lejárjon, futtassa a következő parancsmast a felhasználó egyszerű felhasználón vagy felhasználói azonosítójával:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Ha a szervezet összes felhasználójának jelszavait úgy szeretné beállítani, hogy azok lejárjanak, használja a következő parancsmast:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Jelszó beállítása úgy, hogy soha ne járjon le

1. Csatlakozzon a Windows PowerShellhez a felhasználói rendszergazda vagy a vállalati rendszergazdai hitelesítő adatok használatával.
1. Hajtsa végre az alábbi parancsok egyikét:

   * Ha be szeretné állítani egy felhasználó jelszavát úgy, hogy soha ne járjon le, futtassa a következő parancsmast a felhasználó egyszerű felhasználónvagy felhasználói azonosítójával:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Ha úgy szeretné beállítani a szervezet összes felhasználójának jelszavát, hogy soha ne járjon le, futtassa a következő parancsmast:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > A jelszavak `-PasswordPolicies DisablePasswordExpiration` az `pwdLastSet` attribútum alapján továbbra is korra vannak állítva. Az `pwdLastSet` attribútum alapján, ha módosítja `-PasswordPolicies None`a lejárati, `pwdLastSet` a rendszer minden jelszó, amely egy régebbi, mint 90 nap szükséges a felhasználónak, hogy módosítsa őket a következő bejelentkezéskor. Ez a módosítás sok felhasználót érinthet.

## <a name="next-steps"></a>További lépések

Az SSPR használatának első [lépései: Oktatóanyag: A felhasználók zárolásának engedélyezése a fiók zárolásához vagy a jelszavak alaphelyzetbe állításához az Azure Active Directory önkiszolgáló jelszó-visszaállításhasználatával.](tutorial-enable-sspr.md)

Ha Önnek vagy felhasználóinak problémái vannak az SSPR-rel, olvassa el [az Önkiszolgáló jelszó-visszaállítás hibaelhárítása című témakört.](active-directory-passwords-troubleshoot.md)

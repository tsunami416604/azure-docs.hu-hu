---
title: Önkiszolgáló jelszó-visszaállítás Windows rendszeren – Azure Active Directory
description: Önkiszolgáló jelszó-visszaállítás engedélyezése elfelejtett jelszó használatával a Windows bejelentkezési képernyőjén
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d1ffec6a849cb97a6151717c3e30dc362b1403
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826604"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Útmutató: a jelszó-visszaállítás engedélyezése a Windows bejelentkezési képernyőjéről

A Windows 7, 8, 8,1 és 10 rendszerű gépek esetében engedélyezheti a felhasználók számára, hogy új jelszót állítsanak be a Windows bejelentkezési képernyőjén. A felhasználóknak már nem kell megkeresniük egy webböngészővel rendelkező eszközt a [SSPR-portál](https://aka.ms/sspr)eléréséhez.

![Példa a Windows 7 és a 10 bejelentkezési képernyőre a SSPR hivatkozás látható](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Általános korlátozások

- A jelszó alaphelyzetbe állítása jelenleg nem támogatott Távoli asztal vagy Hyper-V bővített munkamenetekben.
- Néhány külső hitelesítő adat szolgáltatója ismert, hogy problémákat okoz a szolgáltatással kapcsolatban.
- Ha a [EnableLUA beállításkulcs](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) módosításával tiltja le az UAC-t, a probléma problémákat okozhat.
- Ez a funkció a 802.1 x hálózati hitelesítéssel rendelkező hálózatok esetében nem működik, és a "azonnali végrehajtás a felhasználó bejelentkezése előtt" beállítást. A 802.1 x hálózati hitelesítéssel telepített hálózatok esetében ajánlott a számítógép-hitelesítés használata a funkció engedélyezéséhez.
- A hibrid Azure AD-hez csatlakoztatott számítógépeknek az új jelszó használatához és a gyorsítótárazott hitelesítő adatok frissítéséhez hálózati kapcsolattal kell rendelkezniük a tartományvezérlőhöz. Ez azt jelenti, hogy az eszközöknek a szervezet belső hálózatán vagy egy helyszíni tartományvezérlőhöz való hálózati hozzáféréssel rendelkező VPN-en kell lenniük. 
- Ha rendszerképet használ, a Sysprep futtatása előtt győződjön meg arról, hogy a webes gyorsítótár törlődik a beépített rendszergazda számára a profilmásolási lépés végrehajtása előtt. A lépéssel kapcsolatos további információkért tekintse meg az [Egyéni alapértelmezett felhasználói profil használata esetén](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)a terméktámogatási cikkben.
- A következő beállítások ismertek a Windows 10-es eszközökön található jelszavak használatának és alaphelyzetbe állításának megakadályozása érdekében
    - Ha a v1809 előtt a Windows 10-es verziójában a Ctrl + Alt + Del billentyűkombináció szükséges, a **jelszó alaphelyzetbe állítása** nem fog működni.
    - Ha a zárolási képernyő értesítései ki vannak kapcsolva, a **jelszó alaphelyzetbe állítása** nem fog működni.
    - Az HideFastUserSwitching értéke engedélyezve vagy 1
    - Az DontDisplayLastUserName értéke engedélyezve vagy 1
    - Az NoLockScreen értéke engedélyezve vagy 1
    - A EnableLostMode be van állítva az eszközön
    - Az Explorer. exe helyére egyéni rendszerhéj van lecserélve
- A következő adott három beállítás kombinációja miatt a funkció nem működik.
    - Interaktív bejelentkezés: nem szükséges a CTRL + ALT + DEL = letiltva
    - DisableLockScreenAppNotifications = 1 vagy engedélyezve
    - A Windows SKU nem otthoni vagy Professional kiadás

## <a name="windows-10-password-reset"></a>Windows 10 jelszó alaphelyzetbe állítása

### <a name="windows-10-prerequisites"></a>Windows 10 előfeltételek

- A rendszergazdának engedélyeznie kell az Azure AD önkiszolgáló jelszó-visszaállítást a Azure Portal.
- **A funkció használata előtt a felhasználóknak regisztrálniuk kell a SSPR**
- Hálózati proxyra vonatkozó követelmények
   - Windows 10-eszközök 
       - A 443- `passwordreset.microsoftonline.com` es port a és a`ajax.aspnetcdn.com`
       - A Windows 10 rendszerű eszközök csak a gépi szintű proxy konfigurációját támogatják
- Futtassa legalább a Windows 10 2018-es verzióját (v1803), és az eszközöknek a következőknek kell lenniük:
    - Azure AD-hez csatlakoztatva
    - csatlakozik a Hibrid Azure AD-hez

### <a name="enable-for-windows-10-using-intune"></a>Windows 10 engedélyezése az Intune használatával

A legrugalmasabb módszer az, ha az Intune használatával telepíti a konfigurációs módosítást, amely engedélyezi az új jelszó kérését a bejelentkezési képernyőről. Az Intune lehetővé teszi, hogy a konfigurációs módosítást az Ön által meghatározott gépek adott csoportján telepítse. Ehhez a metódushoz regisztrálni kell az Intune-ba az eszközt.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Eszközkonfigurációs szabályzat létrehozása az Intune-ban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) és kattintson az **Intune** elemre.
1. Hozzon létre egy új eszköz konfigurációs profilt az **eszköz konfigurációs**  >  **profiljainak**  >  **profil létrehozása**
   - Adjon kifejező nevet a profilnak
   - Ha kívánja, megadhat egy kifejező leírást is a profilhoz
   - Platform: **Windows 10 és újabb**
   - Profiltípus: **Egyéni**
1. **Beállítások** konfigurálása
   - **Adja hozzá** a következő OMA-URI beállítást a Jelszó-visszaállítási hivatkozás használatának engedélyezéséhez
      - Adjon meg egy kifejező nevet, amelyből kiderül, hogy a beállítás mire szolgál
      - Ha kívánja, megadhat egy kifejező leírást is a beállításhoz
      - Állítsa az **OMA-URI** beállítást `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset` értékre
      - Állítsa az **Adattípus**beállítást **Egész szám** értékre
      - Állítsa az **Érték** beállítást **1** értékre
      - Kattintson **az OK** gombra
   - Kattintson **az OK** gombra
1. Kattintson a **Létrehozás** gombra
1. Ezt a házirendet meghatározott felhasználókhoz, eszközökhöz vagy csoportokhoz lehet hozzárendelni. További információt a következő cikkben talál: [Microsoft Intune felhasználói és eszköz profiljának társítása](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Windows 10 engedélyezése a beállításjegyzék használatával

1. Bejelentkezés a Windows rendszerű számítógépre rendszergazdai hitelesítő adatok használatával
1. Futtassa a **regedit** parancsot rendszergazdaként
1. Állítsa be a következő beállításkulcsot
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 jelszó-visszaállítás hibaelhárítása

Az Azure AD auditnaplója információkat tartalmaz az IP-címről és az ügyféltípusról, ahol az új jelszó kérése megtörtént.

![Windows 7 jelszó alaphelyzetbe állítása az Azure AD naplójában](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Ha a felhasználó egy Windows 10-es eszköz bejelentkezési képernyőjéről állítja vissza a jelszavát, a rendszer egy alacsony jogosultságú ideiglenes fiókot `defaultuser1` hoz létre. Ezzel a fiókkal a jelszó-visszaállítási folyamat biztonságos marad. Maga a fiók véletlenszerűen generált jelszót tartalmaz, nem jelenik meg az eszköz bejelentkezéséhez, és automatikusan el lesz távolítva, miután a felhasználó alaphelyzetbe állítja a jelszavát. Több `defaultuser` profil is létezik, de nyugodtan figyelmen kívül hagyható.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 és 8,1 jelszó alaphelyzetbe állítása

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 és 8,1 előfeltételek

- A rendszergazdának engedélyeznie kell az Azure AD önkiszolgáló jelszó-visszaállítást a Azure Portal.
- **A funkció használata előtt a felhasználóknak regisztrálniuk kell a SSPR**
- Hálózati proxyra vonatkozó követelmények
   - Windows 7, 8 és 8,1 rendszerű eszközök
       - 443-es port`passwordreset.microsoftonline.com`
- Javított Windows 7 vagy Windows 8,1 operációs rendszer.
- TLS 1,2 engedélyezve a [Transport Layer Security (TLS) beállításjegyzék-beállításokban](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)található útmutatás használatával.
- Ha a gépen több külső hitelesítő adat-szolgáltató van engedélyezve, a felhasználók több felhasználói profilt is láthatnak a bejelentkezési képernyőn.

> [!WARNING]
> A TLS 1,2-et engedélyezni kell, nem csak automatikus egyeztetésre beállítva

### <a name="install"></a>Telepítés

1. Töltse le a megfelelő telepítőt az engedélyezni kívánt Windows-verzióhoz.
   - A szoftver a Microsoft letöltőközpontban érhető el[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Jelentkezzen be arra a gépre, amelyre telepíteni szeretné, majd futtassa a telepítőt.
1. A telepítés után az újraindítás kifejezetten ajánlott.
1. Az újraindítás után a bejelentkezési képernyőn válasszon ki egy felhasználót, és kattintson a "Elfelejtett jelszó" lehetőségre. a jelszó-visszaállítási munkafolyamat elindításához.
1. A jelszó alaphelyzetbe állításához a képernyőn megjelenő lépéseket követve fejezze be a munkafolyamatot.

![Például a Windows 7 a "Elfelejtett jelszó?" gombra kattintott SSPR folyamat](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Csendes telepítés

- A beavatkozás nélküli telepítéshez használja az "msiexec/i SsprWindowsLogon. PROD. msi/Qn" parancsot.
- A beavatkozás nélküli eltávolításhoz használja az "msiexec/x SsprWindowsLogon. PROD. msi/Qn" parancsot.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7, 8 és 8,1 jelszó-visszaállítás hibaelhárítása

Az események a gépen és az Azure AD-ben is naplózva lesznek. Az Azure AD-események tartalmazzák az IP-címről és a jelszó alaphelyzetbe állítására szolgáló ClientType vonatkozó információkat is.

![Windows 7 jelszó alaphelyzetbe állítása az Azure AD naplójában](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Ha további naplózásra van szükség, a számítógépen található beállításkulcs módosítható a részletes naplózás engedélyezéséhez. Csak hibaelhárítási célokra engedélyezze a részletes naplózást.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- A részletes naplózás engedélyezéséhez hozzon létre egy `REG_DWORD: "EnableLogging"` értéket, és állítsa 1 értékre.
- A részletes naplózás letiltásához módosítsa a `REG_DWORD: "EnableLogging"` értéket 0-ra.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Most, hogy beállította a jelszó-visszaállítást a Windows-eszközökön, milyen módosításokat végez a felhasználó? Honnan fogják tudni, hogy a bejelentkezési képernyőn új jelszót kérhetnek?

![Példa a Windows 7 és a 10 bejelentkezési képernyőre a SSPR hivatkozás látható](./media/howto-sspr-windows/windows-reset-password.png)

Amikor a felhasználók megpróbálnak bejelentkezni, mostantól egy **új jelszó** kérése vagy **elfelejtett jelszó** hivatkozás jelenik meg, amely megnyitja az önkiszolgáló jelszó-visszaállítási funkciót a bejelentkezési képernyőn. Ezzel a funkcióval a felhasználók visszaállíthatják a jelszavukat anélkül, hogy egy másik eszközt kellene használniuk egy webböngésző eléréséhez.

A felhasználók a funkcióval kapcsolatban a [Munkahelyi vagy iskolai jelszó visszaállítása](../user-help/active-directory-passwords-update-your-own-password.md) témakörben találhatnak útmutatást.

## <a name="next-steps"></a>További lépések

[Az engedélyezéshez szükséges hitelesítési módszerek megtervezése](concept-authentication-methods.md)

[Windows 10 konfigurálása](https://docs.microsoft.com/windows/configuration/)

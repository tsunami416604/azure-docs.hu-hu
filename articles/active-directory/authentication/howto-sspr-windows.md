---
title: Önkiszolgáló jelszó-visszaállítás Windows rendszerhez – Azure Active Directory
description: Az önkiszolgáló jelszó-visszaállítás engedélyezése az elfelejtett jelszó használatával a Windows bejelentkezési képernyőjén
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
ms.openlocfilehash: 0a8675756aeef1140dbebd94023d7f7fb4c7af99
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652262"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Útmutató: Jelszó-visszaállítás engedélyezése a Windows bejelentkezési képernyőjéről

Windows 7, 8, 8.1 és 10 rendszert futtató gépek esetén engedélyezheti a felhasználóknak, hogy a Windows bejelentkezési képernyőjén alaphelyzetbe állítsák jelszavukat. A felhasználóknak már nem kell megtalálniuk egy webböngészővel rendelkező eszközt az [SSPR portál](https://aka.ms/sspr)eléréséhez.

![Példa Windows 7 és 10 bejelentkezési képernyők SSPR linkkel](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Általános korlátozások

- A jelszó-visszaállítás jelenleg nem támogatott távoli asztalról vagy Hyper-V-vel bővített munkamenetből.
- Egyes külső hitelesítő adatokat szolgáltató szolgáltatók róluk ismert, hogy problémákat okoznak ezzel a funkcióval.
- Az UAC letiltása az [EnableLUA rendszerleíró kulcs](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) módosításával ismert, hogy problémákat okoz.
- Ez a szolgáltatás nem működik a 802.1x hálózati hitelesítéssel rendelkező hálózatok nál, és a "Közvetlenül a felhasználói bejelentkezés előtt" lehetőség. A 802.1x hálózati hitelesítéssel rendelkező hálózatok esetében ajánlott a gépi hitelesítés használata a szolgáltatás engedélyezéséhez.
- Hibrid Azure AD-hez csatlakozó gépek kell a hálózati kapcsolat látótávolság a tartományvezérlő az új jelszó használatához és a gyorsítótárazott hitelesítő adatok frissítése.
- Ha lemezképet használ, a sysprep futtatása előtt győződjön meg arról, hogy a webes gyorsítótár törlődik a beépített rendszergazda számára a CopyProfile lépés végrehajtása előtt. Erről a lépésről további információt a Teljesítmény gyenge érték [egyéni alapértelmezett felhasználói profil használata esetén](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)című támogatási cikk tartalmaz.
- A következő beállítások akadályozzák a jelszavak windows 10-es eszközökön történő használatát és alaphelyzetbe állítását
    - Ha a Windows 10 10-es verzióiban a Ctrl+Alt+Del billentyűkombinációt a v1809 előtti verziókban kell megadni, **a jelszó alaphelyzetbe állítása** nem fog működni.
    - Ha a zárolási képernyőn megjelenő értesítések ki vannak kapcsolva, **a Jelszó alaphelyzetbe állítása** nem fog működni.
    - HideFastUserSwitching van beállítva, hogy engedélyezve van, vagy 1
    - A DontDisplayLastUserName beállítása engedélyezve van, vagy 1
    - A NoLockScreen beállítása engedélyezve van, vagy 1
    - Az EnableLostMode beállítás az eszközön
    - Explorer.exe programot egyéni rendszerhéj váltja fel
- Az alábbi három beállítás kombinációja azt eredményezheti, hogy ez a szolgáltatás nem működik.
    - Interaktív bejelentkezés: Nincs szükség CTRL+ALT+DEL = Letiltva
    - DisableLockScreenAppNotifications = 1 vagy Enabled
    - IsContentDeliveryPolicyEnforced = 1 vagy True

## <a name="windows-10-password-reset"></a>Windows 10 jelszó alaphelyzetbe állítása

### <a name="windows-10-prerequisites"></a>A Windows 10 előfeltételei

- A rendszergazdának engedélyeznie kell az Azure AD önkiszolgáló jelszó-alaphelyzetbe állítását az Azure Portalról.
- **A felhasználóknak regisztrálniuk kell az SSPR-re, mielőtt használnák ezt a funkciót**
- Hálózati proxykövetelmények
   - Windows 10-eszközök 
       - A 443-as port és a `passwordreset.microsoftonline.com``ajax.aspnetcdn.com`
       - A Windows 10-es eszközök csak a gépszintű proxykonfigurációt támogatják
- Futtasson legalább Windows 10 verziót, amely 2018 áprilisi frissítése (v1803), és az eszközöknek vagy a következőknek kell lenniük:
    - Azure AD-hez csatlakoztatva
    - csatlakozik a Hibrid Azure AD-hez

### <a name="enable-for-windows-10-using-intune"></a>Engedélyezés windows 10-hez az Intune használatával

A legrugalmasabb módszer az, ha az Intune használatával telepíti a konfigurációs módosítást, amely engedélyezi az új jelszó kérését a bejelentkezési képernyőről. Az Intune lehetővé teszi, hogy a konfigurációs módosítást az Ön által meghatározott gépek adott csoportján telepítse. Ehhez a metódushoz regisztrálni kell az Intune-ba az eszközt.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Eszközkonfigurációs szabályzat létrehozása az Intune-ban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) és kattintson az **Intune** elemre.
1. Új eszközkonfigurációs profil létrehozása az **Eszközkonfigurációs** > **profilok** > **profil létrehozása elemre**
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
      - Kattintson **az OK gombra**
   - Kattintson **az OK gombra**
1. Kattintson a **Létrehozás gombra.**
1. Ez a házirend adott felhasználókhoz, eszközökhöz vagy csoportokhoz rendelhető. További információ A Felhasználói [és eszközprofilok hozzárendelése a Microsoft Intune-ban](https://docs.microsoft.com/intune/device-profile-assign)című cikkben található.

### <a name="enable-for-windows-10-using-the-registry"></a>Engedélyezés a Windows 10-hez a rendszerleíró adatbázis használatával

1. Bejelentkezés a Windows rendszerű számítógépre rendszergazdai hitelesítő adatokkal
1. Futtassa a **regedit** parancsot rendszergazdaként
1. Állítsa be a következő beállításkulcsot
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>A Windows 10 jelszó-visszaállítással kapcsolatos hibáinak elhárítása

Az Azure AD auditnaplója információkat tartalmaz az IP-címről és az ügyféltípusról, ahol az új jelszó kérése megtörtént.

![Példa a Windows 7 jelszó-visszaállításra az Azure AD naplózási naplójában](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Amikor a felhasználók visszaállítják jelszavukat egy Windows 10-es `defaultuser1` eszköz bejelentkezési képernyőjéről, létrejön egy alacsony jogosultságú ideiglenes fiók. Ezzel a fiókkal biztosíthatja a jelszó-visszaállítási folyamat biztonságát. Maga a fiók véletlenszerűen generált jelszóval rendelkezik, nem jelenik meg az eszközbejelentkezéshez, és automatikusan törlődik, miután a felhasználó visszaállítja a jelszavát. Több `defaultuser` profil is létezhet, de biztonságosan figyelmen kívül hagyható.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 és 8.1 jelszó-visszaállítás

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 és 8.1 előfeltételei

- A rendszergazdának engedélyeznie kell az Azure AD önkiszolgáló jelszó-alaphelyzetbe állítását az Azure Portalról.
- **A felhasználóknak regisztrálniuk kell az SSPR-re, mielőtt használnák ezt a funkciót**
- Hálózati proxykövetelmények
   - Windows 7,8 és 8.1-es eszközök
       - 443-as porttól`passwordreset.microsoftonline.com`
- Javított Windows 7 vagy Windows 8.1 operációs rendszer.
- A TLS 1.2 engedélyezve van a [Transport Layer Security (TLS) beállításjegyzék-beállításaiban](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)található útmutatás használatával.
- Ha egynél több harmadik fél hitelesítő adatszolgáltatója van engedélyezve a számítógépen, a felhasználók egynél több felhasználói profilt fognak látni a bejelentkezési képernyőn.

> [!WARNING]
> TLS 1.2 engedélyezni kell, nem csak be kell állítani, hogy automatikus egyeztetés

### <a name="install"></a>Telepítés

1. Töltse le az engedélyezni kívánt Windows-verzióhoz megfelelő telepítőt.
   - A szoftver elérhető a Microsoft letöltőközpontjában a[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Jelentkezzen be arra a számítógépre, ahová telepíteni szeretné, és futtassa a telepítőt.
1. A telepítés után erősen ajánlott az újraindítás.
1. Az újraindítás után, a bejelentkezési képernyőn válasszon egy felhasználót, és kattintson az "Elfelejtett jelszó?" a jelszó-visszaállítási munkafolyamat elindításához.
1. Végezze el a munkafolyamatot a képernyőn megjelenő lépéseket követve a jelszó alaphelyzetbe állításához.

![Példa Windows 7 kattintott "Elfelejtett jelszó?" SSPR-folyamat](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Csendes telepítés

- Csendes telepítéshez használja az "msiexec /i SsprWindowsLogon.PROD.msi /qn" parancsot.
- Néma eltávolításhoz használja az "msiexec /x SsprWindowsLogon.PROD.msi /qn" parancsot.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>A Windows 7, 8 és 8.1 jelszó-visszaállításhibáinak elhárítása

Az események a gépen és az Azure AD-ben is naplózva lesznek. Az Azure AD-események információkat tartalmaz nak az IP-cím és clienttype, ahol a jelszó-visszaállítás történt.

![Példa a Windows 7 jelszó-visszaállításra az Azure AD naplózási naplójában](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Ha további naplózásra van szükség, a számítógépen lévő beállításkulcs módosítható a részletes naplózás engedélyezéséhez. Részletes naplózás engedélyezése csak hibaelhárítási célokra.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- A részletes naplózás engedélyezéséhez `REG_DWORD: "EnableLogging"`hozzon létre egy t, és állítsa 1-re.
- A részletes naplózás letiltásához `REG_DWORD: "EnableLogging"` módosítsa a 0-t.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Most, hogy beállította a jelszó-visszaállítást a Windows-eszközökhöz, milyen változások at a felhasználó? Honnan fogják tudni, hogy a bejelentkezési képernyőn új jelszót kérhetnek?

![Példa Windows 7 és 10 bejelentkezési képernyők SSPR linkkel](./media/howto-sspr-windows/windows-reset-password.png)

Amikor a felhasználók megpróbálnak bejelentkezni, most megjelenik egy **Jelszó alaphelyzetbe állítása** vagy **az Elfelejtett jelszó** hivatkozás, amely megnyitja az önkiszolgáló jelszó-visszaállítási felületet a bejelentkezési képernyőn. Ezzel a funkcióval a felhasználók visszaállíthatják a jelszavukat anélkül, hogy egy másik eszközt kellene használniuk egy webböngésző eléréséhez.

A felhasználók a funkcióval kapcsolatban a [Munkahelyi vagy iskolai jelszó visszaállítása](../user-help/active-directory-passwords-update-your-own-password.md) témakörben találhatnak útmutatást.

## <a name="next-steps"></a>További lépések

[Az engedélyezéshez szükséges hitelesítési módszerek megtervezése](concept-authentication-methods.md)

[A Windows 10 konfigurálása](https://docs.microsoft.com/windows/configuration/)

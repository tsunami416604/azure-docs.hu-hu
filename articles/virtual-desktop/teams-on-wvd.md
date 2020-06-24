---
title: Microsoft Teams a Windows rendszerű virtuális asztalon – Azure
description: A Microsoft Teams használata a Windows rendszerű virtuális asztali gépeken.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/29/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b2ef8a944af9f80dd65ce75869bcf4e3156c63f
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254905"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>A Microsoft Teams használata a Windows rendszerű virtuális asztalon

>[!IMPORTANT]
>A Microsoft Teams Media Optimization jelenleg nyilvános előzetes verzióban érhető el. Javasoljuk, hogy a csapatok éles számítási feladatokhoz való telepítése előtt értékelje az optimalizált csapatok felhasználói élményét. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.

>[!NOTE]
>A Microsoft Teams szolgáltatáshoz készült média-optimalizálás csak Windows 10-es gépeken futó Windows asztali ügyfélprogram esetében érhető el. A média-optimalizáláshoz a Windows asztali ügyfél verziója 1.2.1026.0 vagy újabb verzió szükséges.

A Microsoft Teams a Windows rendszerű virtuális asztalon támogatja a csevegést és az együttműködést. A Media optimizations szolgáltatással a hívás és az értekezlet funkció is támogatott. Ha többet szeretne megtudni arról, hogyan használhatja a Microsoft Teams-t a virtuális asztali infrastruktúra (VDI) környezetekben, tekintse meg a következő témakört: [csoportok virtualizált asztali infrastruktúrához](/microsoftteams/teams-for-vdi/)

A Microsoft Teams-hez készült Media Optimization szolgáltatással a Windows asztali ügyfél a csapatok hívásai és értekezletei számára helyileg kezeli a hang-és videofájlokat. A Microsoft Teams szolgáltatást továbbra is használhatja a Windows rendszerű virtuális asztalon más ügyfelekkel az optimalizált hívás és az értekezletek nélkül. A Teams csevegési és együttműködési funkciói minden platformon támogatottak. Ha a távoli munkamenetben szeretné átirányítani a helyi eszközöket, tekintse meg az [RDP protokoll tulajdonságainak testreszabása a gazdagéphez](#customize-remote-desktop-protocol-properties-for-a-host-pool)című szakaszt.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Microsoft Teams szolgáltatást használhassa a Windows rendszerű virtuális asztalon, ezeket a következő műveleteket kell végrehajtania:

- [Készítse elő a hálózatát](/microsoftteams/prepare-network/) a Microsoft Teams szolgáltatásban.
- Telepítse a [Windows asztali ügyfelet](connect-windows-7-and-10.md) egy Windows 10 vagy Windows 10 IoT Enterprise rendszerű eszközre, amely megfelel a Microsoft Teams [Windows rendszerű számítógépekre vonatkozó hardverkövetelmények](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/).
- Csatlakozhat egy Windows 10 rendszerű többmunkamenetes vagy Windows 10 Enterprise rendszerű virtuális géphez (VM).
- Telepítse a Teams Desktop alkalmazást a gazdagépre számítógépenkénti telepítés használatával. A Microsoft Teams-hez készült média-optimalizáláshoz a Teams Desktop-alkalmazás 1.3.00.4461 vagy újabb verziója szükséges.

## <a name="install-the-teams-desktop-app"></a>A Teams asztali alkalmazás telepítése

Ebből a szakaszból megtudhatja, hogyan telepítheti a Teams Desktop alkalmazást a Windows 10-es többmunkamenetes vagy Windows 10 Enterprise rendszerű virtuálisgép-rendszerképbe. További információért tekintse meg [a Teams asztali alkalmazás telepítése vagy frissítése a VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi/)-ben című témakört.

### <a name="prepare-your-image-for-teams"></a>A rendszerkép előkészítése a csapatok számára

A csapatok számára a média optimalizálásának engedélyezéséhez állítsa be a következő beállításkulcsot a gazdagépen:

1. A Start menüben futtassa a **Regedit parancsot** rendszergazdaként. Navigáljon **HKEY_LOCAL_MACHINE \software\microsoft\teams**.
2. Hozza létre a következő értéket a csapatok kulcsához:

| Name             | Típus   | Az adatértékek/értékek  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>A Teams WebSocket szolgáltatás telepítése

Telepítse a [WebSocket szolgáltatást](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4yj0i) a virtuálisgép-rendszerképre. Ha telepítési hibába ütközik, telepítse a [legújabb Microsoft Visual C++ újraterjeszthető](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) csomagot, és próbálkozzon újra.

### <a name="install-microsoft-teams"></a>A Microsoft Teams telepítése

A Teams Desktop alkalmazást számítógépenként vagy felhasználónkénti telepítéssel is telepítheti. A Microsoft Teams telepítése a Windows rendszerű virtuális asztali környezetben:

1. Töltse le a környezetének megfelelő [Teams msi-csomagot](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/) . Javasoljuk, hogy a 64 bites telepítőt egy 64 bites operációs rendszeren használja.

      > [!NOTE]
      > A Microsoft Teams-hez készült média-optimalizáláshoz a Teams Desktop-alkalmazás 1.3.00.4461 vagy újabb verziója szükséges.

2. A következő parancsok egyikének futtatásával telepítse az MSI-t a gazdagép virtuális gépre:

    - Felhasználónkénti telepítés

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSERS=1
        ```

        Ez a folyamat az alapértelmezett telepítés, amely a csapatokat a **% AppData%** felhasználói mappába telepíti. A csapatok nem működnek megfelelően a felhasználónkénti telepítéssel a nem állandó beállításokon.

    - Számítógépenkénti telepítés

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1 ALLUSERS=1
        ```

        Ezzel a csapatokat a programfájlok (x86) mappába telepíti egy 64 bites operációs rendszeren, valamint egy 32 bites operációs rendszer Program Files mappájába. Ezen a ponton az arany-rendszerkép beállítása befejeződött. A nem állandó telepítésekhez a csapatok számítógépenkénti telepítése szükséges.

        Amikor legközelebb megnyitja a csapatokat egy munkamenetben, a rendszer kérni fogja a hitelesítő adatait.

        > [!NOTE]
        > A felhasználók és a rendszergazdák jelenleg nem tudják letiltani a csapatok automatikus indítását a bejelentkezés során.

3. A következő parancs futtatásával távolíthatja el az MSI-t a gazdagép virtuális gépről:

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Ezzel eltávolítja a csapatokat a programfájlok (x86) mappából vagy a program Files mappából az operációs rendszer környezete alapján.

      > [!NOTE]
      > Ha a ALLUSER = 1 MSI-beállítással rendelkező csapatokat telepít, az automatikus frissítések le lesznek tiltva. Javasoljuk, hogy havonta legalább egyszer frissítse a csapatokat. Ha többet szeretne megtudni a csapatok asztali alkalmazás üzembe helyezéséről, tekintse meg a [csapatok asztali alkalmazás üzembe helyezése a virtuális gépen](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/)című témakört.

### <a name="verify-media-optimizations-loaded"></a>A média-optimalizálások betöltésének ellenőrzése

A WebSocket szolgáltatás és a csapatok asztali alkalmazás telepítése után kövesse az alábbi lépéseket annak ellenőrzéséhez, hogy a csapatok média-optimalizációja be van-e töltve:

1. Válassza ki a felhasználói profil rendszerképét, majd válassza a **Névjegy**elemet.
2. Válassza a **verzió**elemet.

      Ha a média-optimalizálás be van töltve, a szalagcím megjeleníti a **WVD média optimalizált beállításait**. Ha a szalagcímben a **WVD adathordozó nincs csatlakoztatva**, lépjen ki a csapatok alkalmazásból, és próbálkozzon újra.

3. Válassza ki a felhasználói profil rendszerképét, majd válassza a **Beállítások**lehetőséget.

      Ha a média-optimalizálás betöltődik, a helyileg elérhető hangeszközök és fényképezőgépek enumerálása az eszköz menüjében történik. Ha a menüben a **távoli hang**látható, lépjen ki a csapatok alkalmazásból, és próbálkozzon újra. Ha az eszközök továbbra sem jelennek meg a menüben, lépjen vissza a [Microsoft Teams telepítéséhez](#install-microsoft-teams) , és ellenőrizze, hogy befejeződött-e a telepítési folyamat.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

A virtualizált környezetekben lévő csapatok használata eltér a nem virtualizált környezetekben található csapatok használatával. A virtualizált környezetekben található csapatok korlátaival kapcsolatos további információkért tekintse meg a [virtualizált asztali infrastruktúra csapatait](/microsoftteams/teams-for-vdi#known-issues-and-limitations/).

### <a name="client-deployment-installation-and-setup"></a>Ügyfelek központi telepítése, telepítése és beállítása

- A gépi telepítéssel a VDI-csoportok nem frissülnek automatikusan ugyanúgy, mint a nem VDI-csapatok ügyfelei. Az ügyfél frissítéséhez új MSI-fájl telepítésével frissítenie kell a virtuális gép rendszerképét.
- A csapatok jelenleg csak az UTC időzónát mutatják a csevegésben, a csatornákon és a naptárban.
- A csapatok multimédia-optimalizálása csak Windows 10 rendszerű számítógépeken támogatott a Windows asztali ügyfél esetében.
- A végpontokon definiált explicit HTTP-proxyk használata nem támogatott.

### <a name="calls-and-meetings"></a>Hívások és értekezletek

- A Windows rendszerű virtuális asztali környezetekben a Teams asztali ügyfél nem támogatja az élő eseményeket. Egyelőre azt javasoljuk, hogy a [csapat webes ügyfelétől](https://teams.microsoft.com) érkező élő eseményeket a távoli munkamenetben vegyen részt.
- Ha a Teams alkalmazást a hívás vagy az értekezlet során minimalizálja, előfordulhat, hogy a bejövő videó nem jelenik meg az alkalmazás kibontásakor.
- A hívások vagy értekezletek jelenleg nem támogatják az alkalmazások megosztását. Az asztali munkamenetek támogatják az asztali megosztást.
- Ha egy többmonitoros telepítésben asztali megosztást használ, minden figyelő meg lesz osztva.
- A vezérlés és az irányítás szabályozása jelenleg nem támogatott.
- A Windows rendszerű virtuális asztali csapatok csak egy bejövő videó bemenetet támogatnak egyszerre. Ez azt jelenti, hogy ha valaki megpróbálja megosztani a képernyőjét, a képernyője az értekezlet vezetője képernyőjén fog megjelenni.
- A WebRTC korlátozások miatt a bejövő és a kimenő videó stream-feloldása 720p-ra van korlátozva.
- A Teams alkalmazás nem támogatja a HID-gombokat vagy a vezérelt vezérlőket más eszközökkel.

A virtualizált környezetekkel nem kapcsolatos ismert problémák esetén lásd: [támogatási csapatok a szervezetben](/microsoftteams/known-issues/)

## <a name="feedback"></a>Visszajelzés

Küldjön visszajelzést a Microsoft Teams szolgáltatáshoz a Windows Virtual Desktopban a csapatok [UserVoice webhelyén](https://microsoftteams.uservoice.com/).

## <a name="collect-teams-logs"></a>Csapatok naplóinak összegyűjtése

Ha a Windows rendszerű virtuális asztali környezetben problémák merülnek fel a csapatok asztali alkalmazásával kapcsolatban, Gyűjtse össze az ügyfél naplóit a gazdagép virtuális gépen található **% AppData% \Microsoft\Teams\logs.txt** alatt.

Ha a hívásokkal és értekezletekkel kapcsolatos problémákba ütközik, Gyűjtse össze a csapat webes ügyfél naplóit a **CTRL**  +  **ALT**  +  **SHIFT**  +  **1**billentyűkombinációval. A naplókat a rendszer a **%USERPROFILE%\Downloads\MSTeams diagnosztikai naplóba DATE_TIME.txt** fogja írni a gazda virtuális gépen.

## <a name="contact-microsoft-teams-support"></a>Kapcsolatfelvétel a Microsoft Teams ügyfélszolgálatával

Ha kapcsolatba szeretne lépni a Microsoft Teams ügyfélszolgálatával, lépjen a [Microsoft 365 felügyeleti központba](/microsoft-365/admin/contact-support-for-business-products).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Gazdagépek RDP protokoll tulajdonságainak testreszabása

A gazdagépek RDP protokoll (RDP) tulajdonságainak, például a többmonitoros élménynek vagy a mikrofon és a hangvezéreltség beállításának testreszabásával optimális felhasználói élményt biztosíthat a felhasználóknak az igényeik alapján.

Az eszközök átirányításának engedélyezése nem szükséges a média-optimalizálással rendelkező csapatok használata esetén. Ha Media Optimization nélküli csapatokat használ, állítsa be a következő RDP-tulajdonságokat a mikrofon és a kamera átirányításának engedélyezéséhez:

- `audiocapturemode:i:1`engedélyezi a hangrögzítést a helyi eszközről, és átirányítja a hangalkalmazásokat a távoli munkamenetbe.
- `audiomode:i:0`hang lejátszása a helyi számítógépen.
- `camerastoredirect:s:*`átirányítja az összes kamerát.

További információért tekintse meg [a gazdagépek RDP protokoll tulajdonságainak testreszabása](customize-rdp-properties.md)című témakört.

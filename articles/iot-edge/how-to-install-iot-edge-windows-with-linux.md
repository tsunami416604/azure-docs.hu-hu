---
title: Az Azure IoT Edge telepítése Windows és Linux-tárolók |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat a Windows és Linux-tárolók
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: kgremban
ms.openlocfilehash: ea576c0d434d4db7077fc41bc1f5bbbc89e7779e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576647"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Az Azure IoT Edge-futtatókörnyezet telepíthető Windows, Linux-tárolók használata

Az Azure IoT Edge-futtatókörnyezet minden IoT Edge-eszközön van telepítve. Három összetevőből áll. A **IoT Edge biztonsági démon** biztosít, és fenntartja az Edge-eszközön a biztonsági követelményeknek. A démon a naplózásra kerül minden rendszerindításkor elindul, és csatlakoztatja az eszközt az IoT Edge-ügynök elindításával. A **IoT Edge-ügynök** elősegíti a központi telepítési és figyelési modulja a peremhálózati eszközön, beleértve az IoT Edge hubot. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

Ez a cikk felsorolja a lépéseket az Azure IoT Edge-futtatókörnyezet telepítéséhez a Windows x64 (Intel vagy AMD) a rendszer. Windows támogatási jelenleg előzetes verzióban érhető el.

>[!NOTE]
Linux-tárolók használata a Windows rendszerekben a javasolt vagy támogatott éles konfiguráció nem az Azure IoT Edge-hez. Azonban ez használható fejlesztési és tesztelési célra.

## <a name="supported-windows-versions"></a>Támogatott Windows-verziók
Az Azure IoT Edge lehet használt fejlesztési és tesztelési következő verzióiban Windows, Linux-tárolók használata esetén:
  * Windows 10-es vagy újabb asztali operációs rendszert.
  * A Windows Server 2016-ra, vagy új kiszolgálói operációs rendszerek.

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése 

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis] [ lnk-oci] container modul (például Docker). 

Használhat [Docker for Windows] [ lnk-docker-for-windows] fejlesztési és tesztelési célra. Konfigurálja a Docker for Windows [Linux-tárolók használata][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon telepítése

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagok feltételei vonatkoznak rá a licencet (a címtárban licenccel) a csomagokban található. Kérjük, olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

Egy adott IoT Edge-eszköz kiépítése az IoT Hub által biztosított eszközök kapcsolatok karakterlánc segítségével manuálisan. Másik lehetőségként használhatja a Device Provisioning Service-eszközök automatikus kiépítésére, amely akkor hasznos, ha sok eszköz kiépítéséhez van. Üzembe helyezési válaszaitól függően válassza ki a megfelelő telepítési parancsfájlt. 

### <a name="install-and-manually-provision"></a>Telepítés és manuális üzembe helyezése

1. Kövesse a [egy új Azure IoT Edge-eszköz regisztrálása] [ lnk-dcs] regisztrálja az eszközt, és az eszköz kapcsolati karakterláncának beolvasása. 

2. IoT Edge-eszközén futtassa a PowerShellt rendszergazdaként. 

3. Töltse le és telepítse az IoT Edge-futtatókörnyezet. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. Amikor a rendszer egy **DeviceConnectionString**, adja meg a lekért IoT Hub kapcsolati karakterláncot. Nem tartalmazzák az ajánlatok köré a kapcsolati karakterláncot. 

### <a name="install-and-automatically-provision"></a>Telepítés, és automatikusan üzembe helyezése

1. Kövesse a [létrehozásával és kiépítésével egy szimulált TPM-eszköz peremhálózati eszköz, a Windows] [ lnk-dps] a Device Provisioning Service beállítása és lekérése a **hatókör azonosítója**, a TPM-eszköz szimulálása eszköz- és lekérése a **regisztrációs azonosító**, majd hozzon létre egyéni regisztrációt. Miután az eszköz regisztrálva van az IoT hub, a telepítés folytatásához.  

   >[!TIP]
   >Tartsa meg az ablak, amely a TPM-szimulátor fut, nyissa meg a telepítés során, és a tesztelés. 

2. IoT Edge-eszközén futtassa a PowerShellt rendszergazdaként. 

3. Töltse le és telepítse az IoT Edge-futtatókörnyezet. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Linux
   ```

4. Amikor a rendszer kéri, adja meg a **ScopeId azonosító** és **RegistrationID** a kiépítési szolgáltatás és az eszköz.

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Az IoT Edge-szolgáltatás állapotát ellenőrizheti: 

```powershell
Get-Service iotedge
```

Vizsgálja meg a szolgáltatás naplók az elmúlt 5 percben használatával:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

És futó rendelkező modulok listája:

```powershell
iotedge list
```

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok][lnk-modules].

Ha az Edge-futtatókörnyezet megfelelően telepíti a problémák merülnek fel, tekintse meg a [hibaelhárítási] [ lnk-trouble] lapot.


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-modules]: how-to-deploy-modules-portal.md

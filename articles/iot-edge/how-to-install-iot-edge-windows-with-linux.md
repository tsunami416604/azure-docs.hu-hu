---
title: Az Azure IoT Edge telepítése Windows és Linux-tárolók |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat a Windows és Linux-tárolók
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: d6852b5b1fe3d0b3c248fc1948fa4c3a9428de89
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125408"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Az Azure IoT Edge-futtatókörnyezet telepíthető Windows, Linux-tárolók használata

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható. 

Az IoT Edge-futtatókörnyezet működését, és milyen összetevők járnak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk felsorolja a lépéseket az Azure IoT Edge-futtatókörnyezet telepítéséhez a Linux-tárolókat a Windows x64 (Intel vagy AMD) a rendszer. Windows támogatási jelenleg előzetes verzióban érhető el.

>[!NOTE]
Linux-tárolók használata a Windows rendszerekben a javasolt vagy támogatott éles konfiguráció nem az Azure IoT Edge-hez. Azonban ez használható fejlesztési és tesztelési célra.

## <a name="supported-windows-versions"></a>Támogatott Windows-verziók
Az Azure IoT Edge lehet használt fejlesztési és tesztelési következő verzióiban Windows, Linux-tárolók használata esetén:
  * Windows 10-es vagy újabb asztali operációs rendszert.
  * A Windows Server 2016-ra, vagy új kiszolgálói operációs rendszerek.

További információt arról, hogy mely jelenleg támogatott operációs rendszerekkel, [Azure IoT Edge-támogatás](support.md#operating-systems). 

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése 

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis] [ lnk-oci] container modul (például Docker). 

Használhat [Docker for Windows] [ lnk-docker-for-windows] fejlesztési és tesztelési célra. Konfigurálja a Docker for Windows [Linux-tárolók használata][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon telepítése

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagok feltételei vonatkoznak rá a licencet (a címtárban licenccel) a csomagokban található. Kérjük, olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

Egy adott IoT Edge-eszköz kiépítése az IoT Hub által biztosított eszközök kapcsolatok karakterlánc segítségével manuálisan. Másik lehetőségként használhatja a Device Provisioning Service-eszközök automatikus kiépítésére, amely akkor hasznos, ha sok eszköz kiépítéséhez van. Üzembe helyezési válaszaitól függően válassza ki a megfelelő telepítési parancsfájlt. 

### <a name="option-1-install-and-manually-provision"></a>1. lehetőség: Telepítés és a manuális üzembe helyezése

1. Kövesse a [egy új Azure IoT Edge-eszköz regisztrálása] [ lnk-dcs] regisztrálja az eszközt, és az eszköz kapcsolati karakterláncának beolvasása. 

2. IoT Edge-eszközén futtassa a PowerShellt rendszergazdaként. 

3. Töltse le és telepítse az IoT Edge-futtatókörnyezet. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. Amikor a rendszer egy **DeviceConnectionString**, adja meg a lekért IoT Hub kapcsolati karakterláncot. Nem tartalmazzák az ajánlatok köré a kapcsolati karakterláncot. 

### <a name="option-2-install-and-automatically-provision"></a>2. lehetőség: Telepítse és automatikus kiépítése

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

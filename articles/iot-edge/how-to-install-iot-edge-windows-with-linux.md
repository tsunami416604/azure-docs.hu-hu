---
title: Az Azure IoT Edge telepítése Windows és Linux-tárolók |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat a Windows és Linux-tárolók
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 7bae81f2f8b9ef9f7da45aed3bf8d7b6e15c9d0b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568267"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Telepítse az Azure IoT Edge-modul Windows, Linux-tárolók használata

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

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis](https://www.opencontainers.org/) container modul (például Docker). 

Használhat [Docker for Windows](https://www.docker.com/docker-windows) fejlesztési és tesztelési célra. Konfigurálja a Docker for Windows [Linux-tárolók használata](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon telepítése

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagok feltételei vonatkoznak rá a licencet (a címtárban licenccel) a csomagokban található. Kérjük, olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

Egy adott IoT Edge-eszköz kiépítése az IoT Hub által biztosított eszközök kapcsolatok karakterlánc segítségével manuálisan. Másik lehetőségként használhatja a Device Provisioning Service-eszközök automatikus kiépítésére, amely akkor hasznos, ha sok eszköz kiépítéséhez van. Üzembe helyezési válaszaitól függően válassza ki a megfelelő telepítési parancsfájlt. 

### <a name="option-1-install-and-manually-provision"></a>1. lehetőség: Telepítés és a manuális üzembe helyezése

1. Kövesse a [egy új Azure IoT Edge-eszköz regisztrálása](how-to-register-device-portal.md) regisztrálja az eszközt, és az eszköz kapcsolati karakterláncának beolvasása. 

2. IoT Edge-eszközén futtassa a PowerShellt rendszergazdaként. 

3. Töltse le és telepítse az IoT Edge-futtatókörnyezet. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. Amikor a rendszer egy **DeviceConnectionString**, adja meg a lekért IoT Hub kapcsolati karakterláncot. Nem tartalmazzák az ajánlatok köré a kapcsolati karakterláncot. 

### <a name="option-2-install-and-automatically-provision"></a>2. lehetőség: Telepítse és automatikus kiépítése

1. Kövesse a [létrehozásával és kiépítésével egy szimulált TPM-eszköz peremhálózati eszköz, a Windows](how-to-auto-provision-simulated-device-windows.md) a Device Provisioning Service beállítása és lekérése a **hatókör azonosítója**, TPM-eszköz szimulálása és lekérése a  **Regisztrációs azonosító**, majd hozzon létre egyéni regisztrációt. Miután az eszköz regisztrálva van az IoT hub, a telepítés folytatásához.  

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

## <a name="tips-and-suggestions"></a>Tippek és javaslatok

Ha a hálózat rendelkezik egy proxykiszolgáló, kövesse a [a proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md) telepítéséhez és az IoT Edge-futtatókörnyezet elindításához.

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha az Edge-futtatókörnyezet megfelelően telepíti a problémák merülnek fel, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

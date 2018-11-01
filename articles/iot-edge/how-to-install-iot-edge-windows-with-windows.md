---
title: Az Azure IoT Edge Windows telepítése a Windows-tárolók |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat a Windows a Windows-tárolókkal
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: d85355b50bad9f05acc7da92d763d011e6f807b6
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741079"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Az Azure IoT Edge-futtatókörnyezet telepíthető Windows használható a Windows-tárolókkal

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható. 

Az IoT Edge-futtatókörnyezet működését, és milyen összetevők járnak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk felsorolja a lépéseket az Azure IoT Edge-futtatókörnyezet telepítéséhez Windows-tárolók a Windows x64 (Intel vagy AMD) a rendszer. 

Windows támogatási jelenleg előzetes verzióban érhető el.

## <a name="supported-windows-versions"></a>Támogatott Windows-verziók
Az Azure IoT Edge a Windows-tárolókkal használható:
  * A Windows 10-es/IoT Enterprise vagy IoT Core-2018 április (Build 17134) frissítéséhez.
  * A Windows Server 1803-as verzióban

További információt arról, hogy mely jelenleg támogatott operációs rendszerekkel, [Azure IoT Edge-támogatás](support.md#operating-systems).

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése 

>[!NOTE]
>Tároló-motor telepítéséhez Windows IoT Core-on, kövesse a lépéseket a [kiépítése IoT Core-eszköz cikkekbe](how-to-install-iot-core.md) majd folytassa az alábbi utasításokat.

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis](https://www.opencontainers.org/) container modul (például Docker). Használhat [Docker for Windows](https://www.docker.com/docker-windows) fejlesztési és tesztelési célra. 

Konfigurálja a Docker for Windows [használhatók a Windows-tárolók](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon telepítése

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagok feltételei vonatkoznak rá a licencet (a címtárban licenccel) a csomagokban található. Kérjük, olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

Egy adott IoT Edge-eszköz kiépítése az IoT Hub által biztosított eszközök kapcsolatok karakterlánc segítségével manuálisan. Másik lehetőségként használhatja a Device Provisioning Service-eszközök automatikus kiépítésére, amely akkor hasznos, ha sok eszköz kiépítéséhez van. Üzembe helyezési válaszaitól függően válassza ki a megfelelő telepítési parancsfájlt. 

### <a name="install-and-manually-provision"></a>Telepítés és manuális üzembe helyezése

1. Kövesse a [egy új Azure IoT Edge-eszköz regisztrálása](how-to-register-device-portal.md) regisztrálja az eszközt, és az eszköz kapcsolati karakterláncának beolvasása. 

2. IoT Edge-eszközén futtassa a PowerShellt rendszergazdaként. 

3. Töltse le és telepítse az IoT Edge-futtatókörnyezet. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. Amikor a rendszer egy **DeviceConnectionString**, adja meg a lekért IoT Hub kapcsolati karakterláncot. Nem tartalmazzák az ajánlatok köré a kapcsolati karakterláncot. 

### <a name="install-and-automatically-provision"></a>Telepítés, és automatikusan üzembe helyezése

1. Kövesse a [létrehozásával és kiépítésével egy szimulált TPM-eszköz peremhálózati eszköz, a Windows](how-to-auto-provision-simulated-device-windows.md) a Device Provisioning Service beállítása és lekérése a **hatókör azonosítója**, TPM-eszköz szimulálása és lekérése a  **Regisztrációs azonosító**, majd hozzon létre egyéni regisztrációt. Miután az eszköz regisztrálva van az IoT hub, a telepítés folytatásához.  

   >[!TIP]
   >Tartsa meg az ablak, amely a TPM-szimulátor fut, nyissa meg a telepítés során, és a tesztelés. 

2. IoT Edge-eszközén futtassa a PowerShellt rendszergazdaként. 

3. Töltse le és telepítse az IoT Edge-futtatókörnyezet. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. Amikor a rendszer kéri, adja meg a **ScopeId azonosító** és **RegistrationID** a kiépítési szolgáltatás és az eszköz. 

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Az IoT Edge-szolgáltatás állapotát ellenőrizheti: 

```powershell
Get-Service iotedge
```

Vizsgálja meg az utolsó 5 percben használatával naplói:

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

---
title: Az Azure IoT Edge Windows telepítése a Windows-tárolók |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat a Windows a Windows-tárolókkal
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: b2878f9d7aa32b2502487cdc081896e5ba306af1
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346376"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Az Azure IoT Edge-futtatókörnyezet telepíthető Windows használható a Windows-tárolókkal

Az Azure IoT Edge-futtatókörnyezet minden IoT Edge-eszközön van telepítve. Három összetevőt tartalmaz. A **IoT Edge biztonsági démon** biztosít, és fenntartja az Edge-eszközön a biztonsági követelményeknek. A démon a naplózásra kerül minden rendszerindításkor elindul, és csatlakoztatja az eszközt az IoT Edge-ügynök elindításával. A **IoT Edge-ügynök** elősegíti a központi telepítési és figyelési modulja a peremhálózati eszközön, beleértve az IoT Edge hubot. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

Ez a cikk felsorolja a lépéseket az Azure IoT Edge-futtatókörnyezet telepítéséhez a Windows x64 (Intel vagy AMD) a rendszer. 

Windows támogatási jelenleg előzetes verzióban érhető el.

## <a name="supported-windows-versions"></a>Támogatott Windows-verziók
Az Azure IoT Edge a Windows-tárolókkal használható:
  * A Windows 10-es/IoT Enterprise vagy IoT Core-2018 április (Build 17134) frissítéséhez.
  * A Windows Server 1803-as verzióban

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése 

>[!NOTE]
>Tároló-motor telepítéséhez Windows IoT Core-on, kövesse a lépéseket a [kiépítése IoT Core-eszköz cikkekbe] [ lnk-iot-core] majd folytassa az alábbi utasításokat.

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis] [ lnk-oci] container modul (pl. Docker). Használhat [Docker for Windows] [ lnk-docker-for-windows] fejlesztési és tesztelési célra. 

**Győződjön meg, hogy a Docker for Windows [Windows-tárolók használatára konfigurálva][lnk-docker-config]**

## <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon telepítése

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagok feltételei vonatkoznak rá a licencet (a címtárban licenccel) a csomagokban található. Kérjük, olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

### <a name="download-the-edge-daemon-package-and-install"></a>Az Edge-démon csomag letöltése és telepítése

Egy rendszergazda PowerShell-ablakban hajtsa végre a következő parancsokat:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Telepítse a (ezt a lépést kihagyhatja az Edge-eszköz IoT core) használatával vcruntime:

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Létrehozása és elindítása **iotedge** szolgáltatás:

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

A szolgáltatás által használt portok számára tűzfalkivételek hozzáadása:

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Hozzon létre egy **iotedge.reg** fájlt az alábbi tartalommal, és a Windows beállításjegyzék importálása a dupla kattintással vagy használatával a `reg import iotedge.reg` parancsot:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon konfigurálása

A démon a konfigurációs fájlban a következő konfigurálható `C:\ProgramData\iotedge\config.yaml`.

A peremhálózati eszköz segítségével manuálisan konfigurálható a [eszköz kapcsolati karakterláncának] [ lnk-dcs] vagy [automatikusan a Device Provisioning Service használatával] [ lnk-dps].

* A kézi konfigurálás, állítsa vissza a **manuális** üzembe helyezési mód. Frissítse az értéket a **device_connection_string** a kapcsolati karakterlánccal az IoT Edge-eszközről.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* Az automatikus konfiguráláshoz, állítsa vissza a **dps** üzembe helyezési mód. Frissítse a **scope_id** és **registration_id** az IoT Hub-DPS példány és az IoT Edge-eszköz TPM Modullal rendelkező értékeivel. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Edge-eszköz használatával nevét `hostname` parancsot a PowerShellben, és állítsa be az értéket **állomásnév:** található a konfigurációs yaml. Példa:

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

Ezután adja meg az ip-cím és port: **workload_uri** és **management_uri** a a **csatlakoztatása:** és **figyelni:** szakaszai a konfiguráció.

Adja meg az IP-cím lekéréséhez `ipconfig` a PowerShell-ablakban, és másolja az ip-címét a **vEthernet (nat)** csatoló (az ip-címet a rendszer eltérő lehet) az alábbi példában látható módon:  

![nat][img-nat]

Frissítés a **workload_uri** és **management_uri** a a **csatlakoztatása:** szakaszában a konfigurációs fájlban. Cserélje le **\<GATEWAY_ADDRESS\>** másolt IP-címmel. 

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

Adja meg ugyanazt a címet, az a **figyelni:** szakaszában a konfigurációt, az átjáró-címet az IP-cím használatával.

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

A PowerShell ablakában hozzon létre egy környezeti változó **IOTEDGE_HOST** együtt a **management_uri** címet.

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

Végül ellenőrizze a **hálózati:** menüpont **moby_runtime:** uncommented, és állítsa **nat**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "nat"
```

Mentse a konfigurációs fájlt, és indítsa újra a szolgáltatást:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ha használta a **manuális konfigurációs** az előző szakasz lépéseit, az IoT Edge-futtatókörnyezet kell lennie, sikeresen üzembe helyezi és futtatja az eszközön. Ha használta a **automatikus konfiguráció** lépéseit, majd hajtsa végre az alábbi lépéseket, hogy a futtatókörnyezet regisztrálhatja az eszközt az IoT hubbal, az Ön nevében. Lásd az alábbi lépésekkel [létrehozásával és kiépítésével egy szimulált TPM-eszköz peremhálózati eszköz, a Windows](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable).

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
  sort-object @{Expression="TimeCreated";Descending=$false}
```

És futó rendelkező modulok listája:

```powershell
iotedge list
```

## <a name="next-steps"></a>További lépések

Ha az Edge-futtatókörnyezet telepítése megfelelően, a kivétel a problémák merülnek fel a [hibaelhárítási] [ lnk-trouble] lap.


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md


---
title: Windows Azure IoT peremhálózati telepítése Windows tárolókhoz |} Microsoft Docs
description: Az Azure IoT peremhálózati telepítési utasításokat a Windows a Windows-tárolók
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 1ae51d948fdaa5654c59549d384b784f0e87dcc3
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112619"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Azure IoT peremhálózati futásidejű telepítése Windows Windows tárolók használata

Az Azure IoT peremhálózati futásidejű minden IoT peremhálózati eszközön van telepítve. Azt a három részből áll. A **IoT peremhálózati biztonsági démon** biztosít, és kezeli a peremhálózati eszköz a biztonsági követelményeket. A démon a minden rendszerindításkor, elindul, és az eszközt az IoT-Edge ügynök elindításával betöltéséhez. A **IoT peremhálózati ügynök** elősegíti a központi telepítés és a peremhálózati eszköz, beleértve a peremhálózati IoT hub modulok ellenőrzésére. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

Ez a cikk felsorolja azokat a lépéseket, a Windows-x64 (Intel vagy AMD) telepítése az Azure IoT peremhálózati futásidejű rendszer. 

A Windows támogatási jelenleg előzetes verzió.

## <a name="supported-windows-versions"></a>A Windows támogatott verziói
Windows-tárolók Azure IoT élt használható:
  * Windows 10/IoT vállalati és április 2018 az IoT-Core rendszerhez (Build 17134).
  * Windows Server 1803

## <a name="install-the-container-runtime"></a>A tároló futásidejű telepítése 

>[!NOTE]
>Tároló motor telepítésre Windows IoT-Core, kövesse a lépéseket a [IoT Core eszköz cikk kiépítése] [ lnk-iot-core] majd folytassa az alábbi utasításokat.

Az Azure IoT peremhálózati támaszkodik egy [OCI-kompatibilis] [ lnk-oci] tároló futásidejű (pl. Docker). Használhat [Docker for Windows] [ lnk-docker-for-windows] a fejlesztéshez és teszteléshez. 

**Győződjön meg arról, Docker for Windows [Windows-tárolók használatára konfigurálva][lnk-docker-config]**

## <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT peremhálózati biztonsági démon telepítése

>[!NOTE]
>Az Azure IoT peremhálózati szoftvercsomagok (a címtárban licenc) a csomagban található licencfeltételek vonatkoznak. Olvassa el a licencfeltételeket, a csomag használata előtt. A telepítés és a csomag használat számít ezek a feltételek elfogadását. Ha Ön nem fogadja el a licencfeltételeket, ne használja a csomag.

### <a name="download-the-edge-daemon-package-and-install"></a>A peremhálózati démon csomag letöltése és telepítése

Egy rendszergazda PowerShell-ablakban hajtható végre a következő parancsokat:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Telepíti a vcruntime (ezt a lépést kihagyhatja az IoT-core peremhálózati eszközön):

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

Hozzon létre egy **iotedge.reg** fájlt a következő tartalmat, és importálja a Windows beállításjegyzékében dupla kattintással, vagy használja a `reg import iotedge.reg` parancs:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Az Azure IoT peremhálózati biztonsági démon konfigurálása

A démon a konfigurációs fájlban a következő konfigurálható `C:\ProgramData\iotedge\config.yaml`.

A peremhálózati eszköz segítségével manuálisan konfigurálható egy [eszköz kapcsolati karakterlánc] [ lnk-dcs] vagy [eszköz kiépítése szolgáltatáshoz történő automatikus] [ lnk-dps].

* Kézi konfigurálás, állítsa vissza a **manuális** létesítés. Frissítse az értéket a **device_connection_string** saját IoT peremhálózati eszközön kapcsolati karakterlánccal.

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

* Automatikus konfigurációs, állítsa vissza a **terjesztési pontok** létesítés. Módosítsa a **scope_id** és **registration_id** az IoT Hub terjesztési pontok-példányt és az IoT-peremhálózati eszköz TPM-mel értékeivel. 

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

A nevét, a peremhálózati eszköz használatával `hostname` a PowerShell parancsot, és állítsa be a következő **állomásnév:** az a konfiguráció yam. Példa:

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

Ezt követően kell az IP-cím és port: **workload_uri** és **management_uri** a a **csatlakozás:** a konfigurációs szakasza.

Adja meg az IP-címhez `ipconfig` a PowerShell-ablakban válassza ki az IP-címét a **vEthernet (nat)** csatoló (az IP-cím a rendszerben lehet különböző) az alábbi példában látható módon:  

![nat][img-nat]

```yaml
connect:
  management_uri: "http://172.29.240.1:15580"
  workload_uri: "http://172.29.240.1:15581"
```

Adja meg az azonos címeket a **figyelni:** a konfigurációs szakasza. Példa:

```yaml
listen:
  management_uri: "http://172.29.240.1:15580"
  workload_uri: "http://172.29.240.1:15581"
```

A PowerShell-ablakban, hozzon létre egy környezeti változó **IOTEDGE_HOST** rendelkező a **management_uri** címét, például:

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://172.29.240.1:15580")
```

Végül győződjön meg arról a **hálózati:** beállítás alatt **moby_runtime:** uncommented, és állítsa be **nat**

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

## <a name="verify-successful-installation"></a>Sikeres telepítésének ellenőrzése

Ha követte a **kézi konfigurálás** az előző szakaszban található lépéseket, a peremhálózati IoT futásidejű kell lennie, sikeresen telepített és az eszközön futó. Ha követte a **automatikus konfiguráció** lépéseit, majd végre kell hajtania az alábbi lépéseket, hogy a futtatókörnyezet lehet az eszköz regisztrálása az IoT hub, az Ön nevében. A következő lépéseket lásd: [létrehozása és biztosítása a szimulált TPM peremhálózati eszköz a Windows](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable).

Az IoT-Edge-szolgáltatás állapotát ellenőrizheti: 

```powershell
Get-Service iotedge
```

Vizsgálja meg a szolgáltatás az utolsó 5 percig használatával:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

És futtató rendelkező modulok listáját:

```powershell
iotedge list
```

## <a name="next-steps"></a>További lépések

Ha problémába ütközik a peremhálózati futtatókörnyezetben telepítése, kivételt a [hibaelhárítási] [ lnk-trouble] lap.


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


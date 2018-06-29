---
title: Windows Azure IoT peremhálózati telepítése Linux tárolókhoz |} Microsoft Docs
description: Az Azure IoT peremhálózati telepítési utasításokat Windows Linux tárolókhoz
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: cd517d7e652b38c7ecf28a17657936698416413a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036004"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Azure IoT peremhálózati futásidejű telepítse a Windows, Linux tárolók használata

Az Azure IoT peremhálózati futásidejű minden IoT peremhálózati eszközön van telepítve. Azt a három részből áll. A **IoT peremhálózati biztonsági démon** biztosít, és kezeli a peremhálózati eszköz a biztonsági követelményeket. A démon a minden rendszerindításkor, elindul, és az eszközt az IoT-Edge ügynök elindításával betöltéséhez. A **IoT peremhálózati ügynök** elősegíti a központi telepítés és a peremhálózati eszköz, beleértve a peremhálózati IoT hub modulok ellenőrzésére. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

Ez a cikk felsorolja azokat a lépéseket, a Windows-x64 (Intel vagy AMD) telepítése az Azure IoT peremhálózati futásidejű rendszer. A Windows támogatási jelenleg előzetes verzió.

>[!NOTE]
Linux-tárolók használata a Windows rendszerekben a beállítás nem támogatott vagy ajánlott éles Azure IoT szegély. Azonban használat fejlesztési és tesztelési célokra.

## <a name="supported-windows-versions"></a>A Windows támogatott verziói
Az Azure IoT peremhálózati lehet használt fejlesztési és tesztelési Windows, a következő verzióiban Linux tárolók használata esetén:
  * Windows 10-es vagy újabb asztali operációs rendszert.
  * Windows Server 2016 vagy új kiszolgálói operációs rendszerek.

## <a name="install-the-container-runtime"></a>A tároló futásidejű telepítése 

Az Azure IoT peremhálózati támaszkodik egy [OCI-kompatibilis] [ lnk-oci] tároló futásidejű (pl. Docker). 

Használhat [Docker for Windows] [ lnk-docker-for-windows] a fejlesztéshez és teszteléshez. Győződjön meg arról, Docker for Windows [Linux-tárolók használatára konfigurálva][lnk-docker-config]

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

Telepítse a vcruntime használatával:

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Létrehozása és elindítása *iotedge* szolgáltatás:

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

A démon a konfigurációs fájlban a következő konfigurálható `C:\ProgramData\iotedge\config.yaml` a peremhálózati eszköz konfigurálható <!--[automatically via Device Provisioning Service][lnk-dps] or--> manuálisan használatával egy [eszköz kapcsolati karakterlánc][lnk-dcs].

Kézi konfigurálás, adja meg az eszköz kapcsolati karakterlánc **kiépítés:** szakasza **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
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

Adja meg az IP-címhez `ipconfig` a PowerShell-ablakban válassza ki az IP-címét a **vEthernet (DockerNAT)**"csatoló (az IP-cím a rendszerben lehet különböző) az alábbi példában látható módon:

![DockerNat][img-docker-nat]

```yaml
connect:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

Adja meg az azonos címeket a **figyelni:** a konfigurációs szakasza. Példa:

```yaml
listen:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

A PowerShell-ablakban, hozzon létre egy környezeti változó **IOTEDGE_HOST** rendelkező a **management_uri** címét, például:

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://10.0.75.1:15580")
```

Végül győződjön meg arról a **hálózati:** beállítás alatt **moby_runtime:** uncommented, és állítsa be **azure iot-él**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

Mentse a konfigurációs fájlt, és indítsa újra a szolgáltatást:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Sikeres telepítésének ellenőrzése

Az IoT-Edge-szolgáltatás állapotát ellenőrizheti: 

```powershell
Get-Service iotedge
```

Ellenőrizze a szolgáltatás eseménynaplóit az utolsó 5 perccel használatával:

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
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows


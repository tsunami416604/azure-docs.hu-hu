---
title: Az Azure IoT Edge telepítése Linux rendszeren |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat a ARM32 Linux rendszeren
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 5b5212d5e1663fee01ff87642432818071d4f4dd
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988534"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Telepítse az Azure IoT Edge-futtatókörnyezet (ARM32v7/armhf) Linux rendszeren

Az Azure IoT Edge-futtatókörnyezet minden IoT Edge-eszközön van telepítve. Három összetevőből áll. A **IoT Edge biztonsági démon** biztosít, és fenntartja az Edge-eszközön a biztonsági követelményeknek. A démon a naplózásra kerül minden rendszerindításkor elindul, és csatlakoztatja az eszközt az IoT Edge-ügynök elindításával. A **IoT Edge-ügynök** elősegíti a központi telepítési és figyelési modulja a peremhálózati eszközön, beleértve az IoT Edge hubot. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

Ez a cikk a Linux ARM32v7/armhf Edge-eszközön (például a Raspberry Pi) az Azure IoT Edge-futtatókörnyezet telepítésének lépéseit sorolja fel.

>[!NOTE]
>A Linux-szoftver tárházakban csomagok feltételei vonatkoznak rá a licenc minden csomagban található (/ usr/megosztása/docs/*csomagnév –*). Olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis] [ lnk-oci] tároló-futtatókörnyezet. A termelési forgatókönyvekhez, azt javasoljuk, használja a [Moby-alapú] [ lnk-moby] motor lejjebb találja. Legyen az egyetlen tároló motor hivatalosan támogatott az Azure IoT Edge segítségével. A docker CE/EE tárolórendszerképek Moby-alapú futásidejű kompatibilisek.

Az alábbi parancsokat a Moby-alapú motor és a parancssori felület (CLI) telepítése. A parancssori felület az éles környezetekben üzemelő példányok esetén nem kötelező, de hasznos, ha fejlesztési.

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>Az IoT Edge biztonsági démon telepítése

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon konfigurálása


A démon a konfigurációs fájlban a következő konfigurálható `/etc/iotedge/config.yaml`. A fájl írásvédett alapértelmezés szerint, szükség lehet emelt szintű engedélyekkel a szerkesztéshez.

```bash
sudo nano /etc/iotedge/config.yaml
```

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

Miután megadta a kiépítési adatokat a konfigurációban, a démon újraindításához:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ha használta a **manuális konfigurációs** az előző szakasz lépéseit, az IoT Edge-futtatókörnyezet kell lennie, sikeresen üzembe helyezi és futtatja az eszközön. Ha használta a **automatikus konfiguráció** lépéseit, majd hajtsa végre az alábbi lépéseket, hogy a futtatókörnyezet regisztrálhatja az eszközt az IoT hubbal, az Ön nevében. Lásd az alábbi lépésekkel [létrehozásával és kiépítésével egy szimulált TPM-eszköz peremhálózati eszköz egy Linux rendszerű virtuális gépre](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Az IoT Edge-démon használatával állapotát ellenőrizheti:

```cmd/sh
systemctl status iotedge
```

Vizsgálja meg a démon naplóit használatával:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

És futó rendelkező modulok listája:

```cmd/sh
sudo iotedge list
```
>[!NOTE]
>Az erőforrás által korlátozott eszközök, például RaspberryPi, azt javasoljuk, hogy *OptimizeForPerformance* környezeti változó értéke *hamis* az utasításoknak a [ hibaelhárítási útmutató.][lnk-trouble]


## <a name="next-steps"></a>További lépések

Ha az Edge-futtatókörnyezet telepítése megfelelően, a kivétel a problémák merülnek fel a [hibaelhárítási] [ lnk-trouble] lap.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-trouble]: https://docs.microsoft.com/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/

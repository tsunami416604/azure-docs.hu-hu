---
title: Az Azure IoT Edge telepítése Linux rendszeren |} A Microsoft Docs
description: A linuxon futó Azure IoT Edge telepítési utasításokat
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: kgremban
ms.openlocfilehash: be8fb801acfb625685604a302051d813bfd97939
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325302"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Telepítse az Azure IoT Edge-futtatókörnyezet (x64) linuxon

Az Azure IoT Edge-futtatókörnyezet minden IoT Edge-eszközön van telepítve. Három összetevőből áll. A **IoT Edge biztonsági démon** biztosít, és fenntartja az Edge-eszközön a biztonsági követelményeknek. A démon a naplózásra kerül minden rendszerindításkor elindul, és csatlakoztatja az eszközt az IoT Edge-ügynök elindításával. A **IoT Edge-ügynök** elősegíti a központi telepítési és figyelési modulja a peremhálózati eszközön, beleértve az IoT Edge hubot. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

Ez a cikk a Linux x64 (Intel vagy AMD) az Azure IoT Edge-futtatókörnyezet telepítésének lépéseit sorolja fel Edge-eszköz.

>[!NOTE]
>A Linux-szoftver tárházakban csomagok feltételei vonatkoznak rá a licenc minden csomagban található (/ usr/megosztása/docs/*csomagnév –*). Olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

## <a name="register-microsoft-key-and-software-repository-feed"></a>A Microsoft kulcs és a szoftverfrissítési tárház hírcsatorna regisztrálása

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése 

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis] [ lnk-oci] tároló-futtatókörnyezet. A termelési forgatókönyvekhez, azt javasoljuk, használja a [Moby-alapú] [ lnk-moby] motor lejjebb találja. Legyen az egyetlen tároló motor hivatalosan támogatott az Azure IoT Edge segítségével. A docker CE/EE tárolórendszerképek Moby futásidejű kompatibilisek.

Frissítse az apt-get paranccsal.

```bash
sudo apt-get update
```

Telepítse a Moby motor. 

```bash
sudo apt-get install moby-engine
```

Telepítse a Moby parancssori felület (CLI). A parancssori felület az éles környezetekben üzemelő példányok esetén nem kötelező, de hasznos, ha fejlesztési.

```bash
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon telepítése

Az alábbi parancsok is a standard verzióját telepíteni fogja a **iothsmlib** Ha még nem létezik.

```bash
sudo apt-get update
sudo apt-get install iotedge
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

## <a name="next-steps"></a>További lépések

Ha az Edge-futtatókörnyezet megfelelően telepíti a problémák merülnek fel, tekintse meg a [hibaelhárítási] [ lnk-trouble] lapot.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md

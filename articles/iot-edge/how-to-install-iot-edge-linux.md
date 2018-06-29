---
title: Azure IoT peremhálózati telepítése Linux |} Microsoft Docs
description: Az Azure IoT peremhálózati telepítési utasításokat Linux rendszeren
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: d1df27300ac2e7f4732d32f54b2a4a4803f421f2
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100442"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Az Azure IoT peremhálózati runtime telepítését Linux (x64)

Az Azure IoT peremhálózati futásidejű minden IoT peremhálózati eszközön van telepítve. Azt a három részből áll. A **IoT peremhálózati biztonsági démon** biztosít, és kezeli a peremhálózati eszköz a biztonsági követelményeket. A démon a minden rendszerindításkor, elindul, és az eszközt az IoT-Edge ügynök elindításával betöltéséhez. A **IoT peremhálózati ügynök** elősegíti a központi telepítés és a peremhálózati eszköz, beleértve a peremhálózati IoT hub modulok ellenőrzésére. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

Ez a cikk a Linux x64 (Intel vagy AMD) az Azure IoT peremhálózati runtime telepítésének lépéseit sorolja fel peremhálózati eszköz.

>[!NOTE]
>A Linux-szoftver adattárak csomagokat a rendszer megvizsgálja a licencfeltételeket, minden csomagban található (/ usr/megosztás/doc/*csomagnév*). Olvassa el a licencfeltételeket, a csomag használata előtt. A telepítés és a csomag használat számít ezek a feltételek elfogadását. Ha Ön nem fogadja el a licencfeltételeket, ne használja a csomag.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft-kulcs és a szoftverfrissítési tárház adatcsatorna regisztrálása

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
```

### <a name="debian-9"></a>Debian 9

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/debian/9/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>A tároló futásidejű telepítése 

Az Azure IoT peremhálózati támaszkodik egy [OCI-kompatibilis] [ lnk-oci] tároló futásidejű (pl. Docker). Ha már rendelkezik Docker CE/EE telepítve a peremhálózati eszközön, továbbra is használni a fejlesztéshez és teszteléshez Azure IoT oldala. 

Éles forgatókönyvek esetén ajánlott használja a [Moby-alapú] [ lnk-moby] alább motor. Az Azure IoT peremhálózati hivatalosan támogatott csak tároló motor. Docker CE/EE tároló lemezkép teljes mértékben kompatibilis a Moby futásidejű.

*Az alábbi utasítások moby motor és a parancssori felület (CLI) telepítése. A parancssori felület fejlesztési hasznosak lehetnek, de az üzemi környezetek nem kötelező.*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT peremhálózati biztonsági démon telepítése

Az alábbi parancsok is telepíteni fogja a szokásos verzióját a **iothsmlib** Ha még nincs telepítve.

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Az Azure IoT peremhálózati biztonsági démon konfigurálása

A démon a konfigurációs fájlban a következő konfigurálható `/etc/iotedge/config.yaml` a peremhálózati eszköz konfigurálható <!--[automatically via Device Provisioning Service][lnk-dps] or--> manuálisan használatával egy [eszköz kapcsolati karakterlánc][lnk-dcs].

Kézi konfigurálás, adja meg az eszköz kapcsolati karakterlánc **kiépítés** szakasza **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*A fájl írásvédett alapértelmezés szerint, előfordulhat, hogy szeretné használni, `sudo` szerkesztheti. Például `sudo nano /etc/iotedge/config.yaml`*

Írja be a kiépítési adatokat a konfigurációban, után indítsa újra a démon:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Sikeres telepítésének ellenőrzése

A állapotának IoT peremhálózati démon használva:

```cmd/sh
systemctl status iotedge
```

Vizsgálja meg a démon naplóit használatával:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

És futtató rendelkező modulok listáját:

```cmd/sh
sudo iotedge list
```

## <a name="next-steps"></a>További lépések

Ha problémába ütközik a peremhálózati futtatókörnyezetben telepítése, kivételt a [hibaelhárítási] [ lnk-trouble] lap.

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md

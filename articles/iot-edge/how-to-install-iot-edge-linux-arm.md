---
title: Azure IoT peremhálózati telepítése Linux |} Microsoft Docs
description: Az Azure IoT peremhálózati telepítési utasításokat a ARM32 Linux rendszeren
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 2f25faa6957f9a8a42e64829e62506ae2add13ae
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096158"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Azure IoT peremhálózati runtime telepítését Linux (ARM32v7/armhf)

Az Azure IoT peremhálózati futásidejű minden IoT peremhálózati eszközön van telepítve. Azt a három részből áll. A **IoT peremhálózati biztonsági démon** biztosít, és kezeli a peremhálózati eszköz a biztonsági követelményeket. A démon a minden rendszerindításkor, elindul, és az eszközt az IoT-Edge ügynök elindításával betöltéséhez. A **IoT peremhálózati ügynök** elősegíti a központi telepítés és a peremhálózati eszköz, beleértve a peremhálózati IoT hub modulok ellenőrzésére. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

Ez a cikk a Linux ARM32v7/armhf peremhálózati eszközön (például málna Pi) az Azure IoT peremhálózati runtime telepítésének lépéseit sorolja fel.

>[!NOTE]
>A Linux-szoftver adattárak csomagokat a rendszer megvizsgálja a licencfeltételeket, minden csomagban található (/ usr/megosztás/doc/*csomagnév*). Olvassa el a licencfeltételeket, a csomag használata előtt. A telepítés és a csomag használat számít ezek a feltételek elfogadását. Ha Ön nem fogadja el a licencfeltételeket, ne használja a csomag.

## <a name="install-the-container-runtime"></a>A tároló futásidejű telepítése

Az Azure IoT peremhálózati támaszkodik egy [OCI-kompatibilis] [ lnk-oci] tároló futásidejű (pl. Docker). Ha már rendelkezik Docker CE/EE telepítve a peremhálózati eszközön, továbbra is használni a fejlesztéshez és teszteléshez Azure IoT oldala. 

Éles forgatókönyvek esetén ajánlott használja a [Moby-alapú] [ lnk-moby] alább motor. Az Azure IoT peremhálózati hivatalosan támogatott csak tároló motor. Docker CE/EE tároló lemezkép teljes mértékben kompatibilis a Moby futásidejű.

Az alábbi parancsok moby motor és a parancssori felület (CLI) telepítése. A parancssori felület fejlesztési hasznosak lehetnek, de az üzemi környezetek nem kötelező.

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

## <a name="install-the-iot-edge-security-daemon"></a>Az IoT-Edge biztonsági démon telepítése

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
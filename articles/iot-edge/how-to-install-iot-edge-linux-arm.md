---
title: Telepítse az Azure IoT Edge a Linux ARM32 |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat a linuxon futó ARM32 eszközökön, például a Raspberry PI
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 9007ee95dc25854a55006284ce6fc574fd8e7968
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948519"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Telepítse az Azure IoT Edge-futtatókörnyezet (ARM32v7/armhf) Linux rendszeren

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható. 

Az IoT Edge-futtatókörnyezet működését, és milyen összetevők járnak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk a Linux ARM32v7/armhf Edge-eszközön az Azure IoT Edge-futtatókörnyezet telepítésének lépéseit sorolja fel. Például ezeket a lépéseket a Raspberry Pi-eszközök esetében akkor működik. Tekintse meg [Azure IoT Edge-támogatás](support.md#operating-systems) által jelenleg támogatott ARM32 operációs rendszerek listáját. 

>[!NOTE]
>A Linux-szoftver tárházakban csomagok feltételei vonatkoznak rá a licenc minden csomagban található (/ usr/megosztása/docs/*csomagnév –*). Olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis] [ lnk-oci] tároló-futtatókörnyezet. A termelési forgatókönyvekhez, azt javasoljuk, használja a [Moby-alapú] [ lnk-moby] motor lejjebb találja. Legyen az egyetlen tároló motor hivatalosan támogatott az Azure IoT Edge segítségével. A docker CE/EE tárolórendszerképek Moby-alapú futásidejű kompatibilisek.

Az alábbi parancsokat a Moby-alapú motor és a parancssori felület (CLI) telepítése. A parancssori felület az éles környezetekben üzemelő példányok esetén nem kötelező, de hasznos, ha fejlesztési.

```bash

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

A **IoT Edge biztonsági démon** biztosít, és fenntartja az Edge-eszközön a biztonsági követelményeknek. A démon a naplózásra kerül minden rendszerindításkor elindul, és csatlakoztatja az eszközt az IoT Edge-futtatókörnyezet a többi elindításával. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="connect-your-device-to-an-iot-hub"></a>Az eszköz csatlakoztatása az IoT hub 

Állítsa be az IoT Edge-futtatókörnyezet, a fizikai eszköz, amely az Azure IoT hub eszközidentitás-mutató hivatkozást. 

A démon a konfigurációs fájlban a következő konfigurálható `/etc/iotedge/config.yaml`. A fájl írásvédett alapértelmezés szerint, szükség lehet emelt szintű engedélyekkel a szerkesztéshez.

Egy adott IoT Edge-eszköz kiépítése az IoT Hub által biztosított eszközök kapcsolatok karakterlánc segítségével manuálisan. Másik lehetőségként használhatja a Device Provisioning Service-eszközök automatikus kiépítésére, amely akkor hasznos, ha sok eszköz kiépítéséhez van. Üzembe helyezési válaszaitól függően válassza ki a megfelelő telepítési parancsfájlt. 

### <a name="option-1-manual-provisioning"></a>1. lehetőség: Manuális kiépítése

A manuális üzembe helyezi az eszközt, meg kell adnia azt egy [eszköz kapcsolati karakterláncának] [ lnk-dcs] , hogy egy új eszköz regisztrációja az IoT hub létrehozásához.


Nyissa meg a konfigurációs fájlban. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg az üzembe helyezési fájl, és vonja vissza a **manuális** üzembe helyezési mód. Frissítse az értéket a **device_connection_string** a kapcsolati karakterlánccal az IoT Edge-eszközről.

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

Mentse és zárja be a fájlt. 

   `CTRL + X`, `Y`, `Enter`

Miután megadta a kiépítési adatokat a konfigurációs fájlban, a démon újraindításához:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>2. lehetőség: Az Automatikus kiépítés

Automatikus kiépítésére egy eszközt, [Device Provisioning Service beállítása és lekérése a regisztrációs Eszközazonosító][lnk-dps]. Csak olyan eszközökre, amelyeken egy platformmegbízhatósági modul (TPM) lapka automatikus üzembe helyezés működik. Például Raspberry Pi-eszközök nem biztosítja a TPM-hez alapértelmezés szerint. 

Nyissa meg a konfigurációs fájlban. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg az üzembe helyezési fájl, és vonja vissza a **dps** üzembe helyezési mód. Frissítse a **scope_id** és **registration_id** az IoT Hub Device Provisioning service és az IoT Edge-eszköz TPM Modullal rendelkező értékeivel. 

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

Mentse és zárja be a fájlt. 

   `CTRL + X`, `Y`, `Enter`

Miután megadta a kiépítési adatokat a konfigurációs fájlban, a démon újraindításához:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ha használta a **manuális konfigurációs** az előző szakasz lépéseit, az IoT Edge-futtatókörnyezet kell lennie, sikeresen üzembe helyezi és futtatja az eszközön. Ha használta a **automatikus konfiguráció** lépéseit, majd hajtsa végre az alábbi lépéseket, hogy a futtatókörnyezet regisztrálhatja az eszközt az IoT hubbal, az Ön nevében. Lásd az alábbi lépésekkel [létrehozásával és kiépítésével egy szimulált TPM-eszköz peremhálózati eszköz egy Linux rendszerű virtuális gépre](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Az IoT Edge-démon használatával állapotát ellenőrizheti:

```bash
systemctl status iotedge
```

Vizsgálja meg a démon naplóit használatával:

```bash
journalctl -u iotedge --no-pager --no-full
```

És futó rendelkező modulok listája:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Tippek és javaslatok

Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. A modul telepítése után jelentkezzen ki a gépet, és jelentkezzen be újra az engedélyek automatikusan frissíti a. Addig használja **sudo** elé bármely `iotedge` a parancsokat.

A korlátozott erőforráshoz eszközökön, azt javasoljuk, hogy beállította a *OptimizeForPerformance* környezeti változót, *hamis* az utasításoknak a [hibaelhárítási útmutató ][lnk-trouble].

Ha a hálózat, amely rendelkezik egy proxykiszolgáló, kövesse a [a proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md).

## <a name="next-steps"></a>További lépések

Ha az Edge-futtatókörnyezet megfelelően telepíti a problémák merülnek fel, tekintse meg a [hibaelhárítási] [ lnk-trouble] lapot.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-trouble]: https://docs.microsoft.com/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/

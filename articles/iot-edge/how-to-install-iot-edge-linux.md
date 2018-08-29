---
title: Az Azure IoT Edge telepítése Linux rendszeren |} A Microsoft Docs
description: A linuxon futó Azure IoT Edge telepítési utasításokat
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 56223b2ed8e9d9b1a08f5313940920113a650bfe
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128332"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Telepítse az Azure IoT Edge-futtatókörnyezet (x64) linuxon

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható. 

Az IoT Edge-futtatókörnyezet működését, és milyen összetevők járnak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk a Linux x64 (Intel vagy AMD) az Azure IoT Edge-futtatókörnyezet telepítésének lépéseit sorolja fel Edge-eszköz. Tekintse meg [Azure IoT Edge-támogatás](support.md#operating-systems) által jelenleg támogatott AMD64 operációs rendszerek listáját. 

>[!NOTE]
>A Linux-szoftver tárházakban csomagok feltételei vonatkoznak rá a licenc minden csomagban található (/ usr/megosztása/docs/*csomagnév –*). Olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

## <a name="register-microsoft-key-and-software-repository-feed"></a>A Microsoft kulcs és a szoftverfrissítési tárház hírcsatorna regisztrálása

Az operációs rendszertől függően válassza ki a megfelelő parancsfájlokat az eszköz előkészítése az IoT Edge modul telepítése. 

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
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

A **IoT Edge biztonsági démon** biztosít, és fenntartja az Edge-eszközön a biztonsági követelményeknek. A démon a naplózásra kerül minden rendszerindításkor elindul, és csatlakoztatja az eszközt az IoT Edge-futtatókörnyezet a többi elindításával. 

A telepítési parancsot is a standard verzióját telepíti a **iothsmlib** Ha még nem létezik.

Frissítse az apt-get paranccsal.

```bash
sudo apt-get update
```

A biztonsági démon telepítése. A csomag telepített `/etc/iotedge/`.

```bash
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon konfigurálása

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

Automatikus kiépítésére egy eszközt, [Device Provisioning Service beállítása és lekérése a regisztrációs Eszközazonosító] [ lnk-dps] (DPS). Csak olyan eszközökre, amelyeken egy platformmegbízhatósági modul (TPM) lapka automatikus üzembe helyezés működik. Például Raspberry Pi-eszközök nem biztosítja a TPM-hez alapértelmezés szerint. 

Nyissa meg a konfigurációs fájlban. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg az üzembe helyezési fájl, és vonja vissza a **dps** üzembe helyezési mód. Frissítse a **scope_id** és **registration_id** az IoT Hub Device Provisioning Service és az IoT Edge-eszköz TPM Modullal rendelkező értékeivel. 

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

## <a name="next-steps"></a>További lépések

Ha az Edge-futtatókörnyezet megfelelően telepíti a problémák merülnek fel, tekintse meg a [hibaelhárítási] [ lnk-trouble] lapot.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md

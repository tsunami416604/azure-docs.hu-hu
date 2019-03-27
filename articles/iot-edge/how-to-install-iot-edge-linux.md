---
title: Az Azure IoT Edge telepítése Linux rendszeren |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat az Ubuntu rendszert futtató AMD64-es Linux rendszerű eszközökön
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1d1e0f100a90c28bd7469991dee559abcd88f9a2
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499470"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Telepítse az Azure IoT Edge-futtatókörnyezet (x64) linuxon

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható.

További tudnivalókért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk az Ubuntu Linux x64 (Intel vagy AMD) az Azure IoT Edge-futtatókörnyezet telepítésének lépéseit sorolja fel IoT Edge-eszköz. Tekintse meg [Azure IoT Edge-támogatás](support.md#operating-systems) AMD64 támogatott operációs rendszerek listáját.

> [!NOTE]
> A Linux-szoftver tárházakban csomagok feltételei vonatkoznak rá a licenc minden csomagban található (/ usr/megosztása/docs/*csomagnév –*). Olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

## <a name="register-microsoft-key-and-software-repository-feed"></a>A Microsoft kulcs és a szoftverfrissítési tárház hírcsatorna regisztrálása

Készítse elő az eszközt az IoT Edge modul telepítése.


Telepítse az adattár-konfiguráció. Válasszon a **16.04** vagy **18.04** a kiadását Ubuntu megfelelő kódrészletet.

> [!IMPORTANT]
> Ellenőrizze, hogy a kódtöredék a helyes kódot boxból Ubuntu verziójának kiválasztása.

* A **Ubuntu 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* A **Ubuntu 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
Telepítse az adattár-konfiguráció. Válasszon a **16.04** vagy **18.04** a kiadását Ubuntu megfelelő kódrészletet.

Másolja ki a létrehozott lista.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Telepítse a Microsoft GPG nyilvános kulcs

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-the-container-runtime"></a>A tároló-modul telepítése

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis](https://www.opencontainers.org/) tároló-futtatókörnyezet. A termelési forgatókönyvekhez, azt javasoljuk, hogy használja a [Moby-alapú](https://mobyproject.org/) motor lejjebb találja. Legyen az egyetlen tároló motor hivatalosan támogatott az Azure IoT Edge segítségével. A docker CE/EE tárolórendszerképek Moby futásidejű kompatibilisek.

Apt frissítést végez.

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

A **IoT Edge biztonsági démon** biztosít, és fenntartja az IoT Edge-eszközön a biztonsági követelményeknek. A démon a naplózásra kerül minden rendszerindításkor elindul, és csatlakoztatja az eszközt az IoT Edge-futtatókörnyezet a többi elindításával.

A telepítési parancsot is a standard verzióját telepíti a **iothsmlib** Ha még nem létezik.

Apt frissítést végez.

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

### <a name="option-1-manual-provisioning"></a>Option 1: Manuális kiépítése

A manuális üzembe helyezi az eszközt, meg kell adnia azt egy [eszköz kapcsolati karakterláncának](how-to-register-device-portal.md) , hogy egy új eszköz regisztrációja az IoT hub létrehozásához.

Nyissa meg a konfigurációs fájlban.

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg az üzembe helyezési fájl. Állítsa vissza a **manuális** Kiépítési mód, és győződjön meg arról, hogy megjegyzésként szerepel a dps Kiépítési mód. Frissítse az értéket a **device_connection_string** a kapcsolati karakterlánccal az IoT Edge-eszközről.

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

### <a name="option-2-automatic-provisioning"></a>Option 2: Automatikus felhasználóátadás

Automatikus kiépítésére egy eszközt, [Device Provisioning Service beállítása és lekérése a regisztrációs Eszközazonosító](how-to-auto-provision-simulated-device-linux.md). Csak olyan eszközökre, amelyeken egy platformmegbízhatósági modul (TPM) lapka automatikus üzembe helyezés működik. Például Raspberry Pi-eszközök nem biztosítja a TPM-hez alapértelmezés szerint.

Nyissa meg a konfigurációs fájlban.

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg az üzembe helyezési fájl. Állítsa vissza a **dps** Kiépítési mód, és tegye megjegyzésbe a manuális szakaszban. Frissítse a **scope_id** és **registration_id** az IoT Hub Device Provisioning Service és az IoT Edge-eszköz TPM Modullal rendelkező értékeivel.

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

Ha használta a **manuális konfigurációs** az előző szakasz lépéseit, az IoT Edge-futtatókörnyezet kell lennie, sikeresen üzembe helyezi és futtatja az eszközön. Ha használta a **automatikus konfiguráció** lépéseit, majd hajtsa végre az alábbi lépéseket, hogy a futtatókörnyezet regisztrálhatja az eszközt az IoT hubbal, az Ön nevében. Lásd az alábbi lépésekkel [létrehozása és üzembe helyezése Linux rendszerű virtuális gépen egy szimulált TPM-eszköz IoT Edge-eszköz](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

A korlátozott erőforráshoz eszközökön, azt javasoljuk, hogy beállította a *OptimizeForPerformance* környezeti változót, *hamis* az utasításoknak a [hibaelhárítási útmutató ](troubleshoot.md).

Ha a hálózat, amely rendelkezik egy proxykiszolgáló, kövesse a [a proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Távolítsa el az IoT Edge

Ha szeretné eltávolítani a IoT Edge-telepítést, a Linux rendszerű eszközről, használja az alábbi parancsokat a parancssorban.

Távolítsa el az IoT Edge-futtatókörnyezetet.

```bash
sudo apt-get remove --purge iotedge
```

Ha eltávolítják az IoT Edge-futtatókörnyezet, a tároló által létrehozott, le vannak állítva, de továbbra is létezik az eszközön. Tekintse meg, melyiket továbbra is megtekintheti az összes tárolót.

```bash
sudo docker ps -a
```

A tárolók törlése az eszközről, többek között a két futtatókörnyezet tárolóiból.

```bash
sudo docker rm -f <container name>
```

Végezetül távolítsa el a tároló-futtatókörnyezet az eszközről.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha az IoT Edge-futtatókörnyezet megfelelően telepíti a problémák merülnek fel, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

Egy meglévő telepítéshez az IoT Edge a legújabb verzióra frissítéséhez lásd [az IoT Edge biztonsági démon és a futtatókörnyezet frissítése](how-to-update-iot-edge.md).

---
title: A Azure IoT Edge telepítése Linux ARM32 | Microsoft Docs
description: Azure IoT Edge telepítési utasítások Linux rendszeren ARM32-eszközökön, például málna PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.openlocfilehash: 72349597a2831a64cb11ff66b30e6948f34cc921
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224696"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren (ARM32v7/armhf)

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható. 

Az IoT Edge-futtatókörnyezet működését, és milyen összetevők járnak kapcsolatos további információkért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk azokat a lépéseket ismerteti, amelyekkel telepítheti az Azure IoT Edge futtatókörnyezetet egy Linux ARM32v7/armhf IoT Edge eszközön. Ezek a lépések például a málna PI-eszközök esetében fognak működni. A támogatott ARM32 operációs rendszerek listáját itt tekintheti meg: [Azure IoT Edge támogatott rendszerek](support.md#operating-systems). 

>[!NOTE]
>A Linux-szoftver tárházakban csomagok feltételei vonatkoznak rá a licenc minden csomagban található (/ usr/megosztása/docs/*csomagnév –* ). Olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

## <a name="install-the-latest-version"></a>A legújabb verzió telepítése

A következő részekben a Azure IoT Edge szolgáltatás legújabb verzióját telepítheti a linuxos ARM-eszközökre. 

### <a name="install-the-container-runtime"></a>A tároló-modul telepítése

Azure IoT Edge egy [OCI-kompatibilis tároló-](https://www.opencontainers.org/) futtatókörnyezetre támaszkodik. A termelési forgatókönyvekhez, azt javasoljuk, használja a [Moby-alapú](https://mobyproject.org/) motor lejjebb találja. Legyen az egyetlen tároló motor hivatalosan támogatott az Azure IoT Edge segítségével. A Docker CE/EE tároló lemezképei kompatibilisek a Moby-alapú futtatókörnyezettel.

A következő parancsok a Moby-alapú motort és a parancssori felületet (CLI) is telepítik. A parancssori felület az éles környezetekben üzemelő példányok esetén nem kötelező, de hasznos, ha fejlesztési.

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

### <a name="install-the-iot-edge-security-daemon"></a>A IoT Edge biztonsági démon telepítése

A **IoT Edge biztonsági démon** a IoT Edge eszközön biztosít és tart fenn biztonsági szabványokat. A démon a naplózásra kerül minden rendszerindításkor elindul, és csatlakoztatja az eszközt az IoT Edge-futtatókörnyezet a többi elindításával. 


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

A IoT Edge sikeres telepítése után a kimenet rákérdez a konfigurációs fájl frissítésére. Az eszköz kiépítés befejezéséhez kövesse a [Azure IoT Edge biztonsági démon konfigurálása](#configure-the-azure-iot-edge-security-daemon) című szakasz lépéseit. 

## <a name="install-a-specific-version"></a>Adott verzió telepítése

Ha a Azure IoT Edge adott verzióját szeretné telepíteni, közvetlenül a IoT Edge GitHub-tárházból is megcélozhatja az összetevők fájljait. Használja az `curl` előző részekben felsorolt parancsokat az összes IoT Edge-összetevő beszerzéséhez az eszközre: a Moby Engine és a CLI, a libiothsm, végül pedig a IoT Edge biztonsági démon. Az egyetlen különbség, hogy a **aka.MS** URL-címeket közvetlenül a használni kívánt összetevők verziójára mutató hivatkozásokkal cseréli le.

Navigáljon a [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)kiadásokhoz, és keresse meg a célként használni kívánt kiadási verziót. Bontsa ki az **eszközök** szakaszt a verzióhoz, és válassza ki a IoT Edge eszköz architektúrájának megfelelő fájlokat. Minden IoT Edge kiadás **iotedge** -és **libiothsm** -fájlokat tartalmaz. Nem minden kiadás tartalmaz **Moby-Engine** vagy **Moby-CLI-** t. Ha még nincs telepítve a Moby Container Engine, tekintse át a korábbi kiadásokat, amíg meg nem találja a Moby-összetevőket tartalmazó elemeket. 

A IoT Edge sikeres telepítése után a kimenet rákérdez a konfigurációs fájl frissítésére. Kövesse a következő szakaszban leírt lépéseket az eszköz kiépítés befejezéséhez. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>A Azure IoT Edge biztonsági démon konfigurálása

Állítsa be az IoT Edge-futtatókörnyezet, a fizikai eszköz, amely az Azure IoT hub eszközidentitás-mutató hivatkozást. 

A démon a konfigurációs fájlban a következő konfigurálható `/etc/iotedge/config.yaml`. A fájl alapértelmezés szerint írásvédett, ezért a szerkesztéshez emelt szintű engedélyekre lehet szükség.

Egy adott IoT Edge-eszköz kiépítése az IoT Hub által biztosított eszközök kapcsolatok karakterlánc segítségével manuálisan. Másik lehetőségként használhatja a Device Provisioning Service-eszközök automatikus kiépítésére, amely akkor hasznos, ha sok eszköz kiépítéséhez van. Üzembe helyezési válaszaitól függően válassza ki a megfelelő telepítési parancsfájlt. 

### <a name="option-1-manual-provisioning"></a>1\. lehetőség: Manuális kiépítés

Az eszköz manuális kiépítéséhez meg kell adnia azt egy eszköz- [kapcsolódási karakterlánctal](how-to-register-device-portal.md) , amelyet létrehozhat egy új IoT Edge-eszköz regisztrálásával az IoT hub-ban.

Nyissa meg a konfigurációs fájlban. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg a fájl üzembe helyezési konfigurációit, és tegye meg a **manuális kiépítési konfiguráció** szakasz megjegyzését. Frissítse az értéket a **device_connection_string** a kapcsolati karakterlánccal az IoT Edge-eszközről. Ügyeljen rá, hogy a többi kiépítési szakaszt is jegyezze fel.

```yaml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# DPS TPM provisioning configuration
# provisioning:
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   attestation:
#     method: "tpm"
#     registration_id: "{registration_id}"
```

Mentse és zárja be a fájlt. 

`CTRL + X`, `Y`, `Enter`

Miután megadta a kiépítési adatokat a konfigurációs fájlban, a démon újraindításához:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>2\. lehetőség: Automatikus kiépítés

Automatikus kiépítésére egy eszközt, [Device Provisioning Service beállítása és lekérése a regisztrációs Eszközazonosító](how-to-auto-provision-simulated-device-linux.md). A IoT Edge az automatikus kiépítés használatakor számos igazolási mechanizmust támogat, de a hardverre vonatkozó követelmények is hatással vannak a választásokra. A málna PI-eszközök például nem rendelkeznek alapértelmezett platformmegbízhatósági modul (TPM) csiptel.

Nyissa meg a konfigurációs fájlban. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg a fájl kiépítési konfigurációit, és adja meg az igazolási mechanizmusnak megfelelő szakaszt. TPM-igazolás használata esetén például frissítse a **scope_id** és a **registration_id** értékeit a IOT hub Device PROVISIONING szolgáltatás és a tpm-sel rendelkező IoT Edge eszköz értékeit.

```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
```

Mentse és zárja be a fájlt. 

`CTRL + X`, `Y`, `Enter`

Miután megadta a kiépítési adatokat a konfigurációs fájlban, a démon újraindításához:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ha használta a **manuális konfigurációs** az előző szakasz lépéseit, az IoT Edge-futtatókörnyezet kell lennie, sikeresen üzembe helyezi és futtatja az eszközön. Ha az **automatikus konfigurálási** lépéseket használta, néhány további lépést is végre kell hajtania, hogy a futtatókörnyezet regisztrálja az eszközt az IoT hub-ban az Ön nevében. A következő lépésekért lásd: [szimulált TPM-IoT Edge eszköz létrehozása és kiépítése Linux rendszerű virtuális gépen](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

A korlátozott erőforráshoz eszközökön, azt javasoljuk, hogy beállította a *OptimizeForPerformance* környezeti változót, *hamis* az utasításoknak a [hibaelhárítási útmutató ](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Ha a hálózat, amely rendelkezik egy proxykiszolgáló, kövesse a [a proxykiszolgálón keresztül kommunikáljon az IoT Edge-eszköz konfigurálása](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>IoT Edge eltávolítása

Ha el szeretné távolítani a IoT Edge telepítését a Linux-eszközről, használja a következő parancsokat a parancssorból. 

Távolítsa el az IoT Edge-futtatókörnyezetet. 

```bash
sudo apt-get remove --purge iotedge
```

A IoT Edge futtatókörnyezet eltávolításakor a létrehozott tároló leállt, de továbbra is létezik az eszközön. Megtekintheti az összes tárolót, hogy azok megmaradjanak-e. 

```bash
sudo docker ps -a
```

Törölje a tárolókat az eszközről, beleértve a két futtató tárolót is. 

```bash
sudo docker rm -f <container name>
```

Végül távolítsa el a Container Runtime eszközt az eszközről. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha problémákat tapasztal a IoT Edge futtatókörnyezet megfelelő telepítésével kapcsolatban, tekintse meg a [Hibaelhárítás](troubleshoot.md#stability-issues-on-resource-constrained-devices) lapot.

Ha egy meglévő telepítést szeretne frissíteni a IoT Edge legújabb verziójára, tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

---
title: Az Azure IoT Edge telepítése Linux rendszeren |} A Microsoft Docs
description: Az Azure IoT Edge telepítési utasításokat az Ubuntu rendszert futtató AMD64-es Linux rendszerű eszközökön
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: bbab0d8d0947c18cf8e6c178d12fdbd7b335d2b6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485902"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Telepítse az Azure IoT Edge-futtatókörnyezet (x64) linuxon

Az Azure IoT Edge-futtatókörnyezet az eszköz milyen bekapcsolja az IoT Edge-eszköz. A futtatókörnyezet kis Raspberry Pi-t vagy akkora, mint egy ipari kiszolgáló eszközökön is telepíthető. Miután egy eszközt az IoT Edge-futtatókörnyezet van beállítva, a üzembe helyezése a üzleti logika, hogy a felhőben is elindítható.

További tudnivalókért lásd: [megismerheti az Azure IoT Edge-futtatókörnyezet és az architektúrára](iot-edge-runtime.md).

Ez a cikk az Ubuntu Linux x64 (Intel vagy AMD) az Azure IoT Edge-futtatókörnyezet telepítésének lépéseit sorolja fel IoT Edge-eszköz. Tekintse meg [Azure IoT Edge által támogatott rendszerek](support.md#operating-systems) AMD64 támogatott operációs rendszerek listáját.

> [!NOTE]
> A Linux-szoftver tárházakban csomagok feltételei vonatkoznak rá a licenc minden csomagban található (/ usr/megosztása/docs/*csomagnév –* ). Olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

## <a name="install-the-latest-version"></a>Telepítse a legújabb verziót

Az alábbi szakaszok segítségével az Azure IoT Edge szolgáltatást az eszközök legújabb verziójának telepítéséhez. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>A Microsoft kulcs és a szoftverfrissítési tárház hírcsatorna regisztrálása

Készítse elő az eszközt az IoT Edge modul telepítése.


Telepítse az adattár-konfiguráció. Válasszon a **16.04** vagy **18.04** kódrészlet az Ubuntu kiadásának szükség szerint:

> [!NOTE]
> Ellenőrizze, hogy a kódtöredék a helyes kódot boxból Ubuntu verziójának kiválasztása.

* A **Ubuntu 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* A **Ubuntu 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
Másolja ki a létrehozott lista.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Telepítse a Microsoft GPG nyilvános kulcs

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>A tároló-modul telepítése

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

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>A Linux-kernel Moby kompatibilitás ellenőrzése

Sok embedded eszközgyártók adjon ki kiváló eszköz lemezképek egyéni Linux-kernelek vannak, amelyek esetleg hiányzik a szükséges tároló-futtatókörnyezet kompatibilitási funkciókat tartalmaznak. Ha problémákat tapasztal, amikor telepítése az ajánlott [Moby](https://github.com/moby/moby) tároló-futtatókörnyezet, akkor előfordulhat, hogy tudni hibaelhárítás a Linux kernel konfiguráció használatával a [jelölőnégyzet-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) megadott parancsfájlt a hivatalos [Moby Github-adattár](https://github.com/moby/moby) az eszközön az alábbi parancsok futtatásával.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Mindezzel biztosíthatja a egy részletes kimenet, amely tartalmazza az Moby modul által használt kernel-szolgáltatások állapotát. Győződjön meg arról, hogy az összes elem mellett célszerű `Generally Necessary` és `Network Drivers` annak biztosításához, hogy a kernel teljes mértékben kompatibilis a Moby futásidejű engedélyezve vannak.  Ha azonosította a hiányzó szolgáltatásokat, előfordulhat, hogy engedélyezni kell a kernel forrásból újraépítése, és válassza ki a megfelelő rendszermag .config bekerülhetnek a kapcsolódó modulok.  Ehhez hasonlóan a kernel konfigurációs generátor például defconfig vagy menuconfig használja, ha szüksége lesz található, és megfelelő funkciók, és ennek megfelelően építse újra a kernel.  Az újonnan módosított kernel üzembe helyezett, futtassa a konfiguráció ellenőrzése újra győződjön meg arról, hogy az azonosított funkciók sikeresen engedélyezve van.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon telepítése

A **IoT Edge biztonsági démon** biztosít, és fenntartja az IoT Edge-eszközön a biztonsági követelményeknek. A démon a naplózásra kerül minden rendszerindításkor elindul, és csatlakoztatja az eszközt az IoT Edge-futtatókörnyezet a többi elindításával.

A telepítési parancsot is a standard verzióját telepíti a **libiothsm** Ha még nem létezik.

Apt frissítést végez.

   ```bash
   sudo apt-get update
   ```

A biztonsági démon telepítése. A csomag telepített `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Miután sikeresen telepítette az IoT Edge, a kimeneti kérni fogja a konfigurációs fájl frissítése. Kövesse a [konfigurálása az Azure IoT Edge biztonsági démon](#configure-the-azure-iot-edge-security-daemon) szakaszban az eszköz üzembe helyezése. 

## <a name="install-a-specific-version"></a>Egy adott verzió telepítése

Ha azt szeretné, az Azure IoT Edge egy adott verzióját, a közvetlenül az IoT Edge GitHub-adattárból összetevőfájlok célba. Kövesse az alábbi lépéseket való csatlakoztatásával minden az IoT Edge-összetevőit az eszköz: a Moby motor és a parancssori felület, a libiothsm, és végül az IoT Edge biztonsági démon.

1. Keresse meg a [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases), és keresse meg a végleges verziót, amely a cél. 

2. Bontsa ki a **eszközök** azt a verziót a következő szakaszban.

3. Lehet, hogy van, vagy nem lehet a Moby motorját bármely adott kiadás frissítéseket. Ha látja kezdődő fájlokat **moby-motor** és **moby-cli**, ezen összetevők frissítése a következő parancsokat használja. Ha nem látja minden Moby fájlokat, és még nincs telepítve az eszközön Moby, lépjen vissza a korábbi kiadás eszközök keresztül amíg meg nem látja őket. 

   1. Keresse meg a **moby-motor** fájlt, amely megfelel az IoT Edge-eszköz architektúra. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe.

   2. Használja a következő parancsot a másolt hivatkozást, hogy a Moby motor verziójának telepítéséhez: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Keresse meg a **moby-cli** fájlt, amely megfelel az IoT Edge-eszköz architektúra. A Moby CLI egy összetevő, de a fejlesztés során hasznos lehet. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe. 

   4. Használja a következő parancsot a másolt hivatkozást, hogy a Moby CLI verziójának telepítéséhez: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Minden megjelenés kell rendelkeznie az IoT Edge biztonsági démon és a hsmlib új fájlokat. A következő parancsok használatával ezek az összetevők frissítését. 

   1. Keresse meg a **libiothsm – standard** fájlt, amely megfelel az IoT Edge-eszköz architektúra. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe. 

   2. Használja a következő parancsot a másolt hivatkozást, hogy a hsmlib verziójának telepítéséhez:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Keresse meg a **iotedge** fájlt, amely megfelel az IoT Edge-eszköz architektúra. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe. 

   4. Az IoT Edge biztonsági démon verzió telepítéséhez használja az alábbi parancsban a másolt hivatkozást. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Miután sikeresen telepítette az IoT Edge, a kimeneti kérni fogja a konfigurációs fájl frissítése. Kövesse a következő szakaszban az eszköz kiépítése befejeződik. 

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

### <a name="option-2-automatic-provisioning"></a>Option 2: Az Automatikus kiépítés

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

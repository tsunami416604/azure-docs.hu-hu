---
title: Az Azure IoT Edge telepítése Linuxon | Microsoft dokumentumok
description: Az Azure IoT Edge telepítési utasításai Ubuntut vagy Raspbiant futtató Linux-eszközökön
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535901"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Az Azure IoT Edge-futtatókörnyezet telepítése Debian-alapú Linux rendszereken

Az Azure IoT Edge-futásidejű, mi bekapcsolja az eszközt egy IoT Edge-eszköz. A futásidejű olyan kicsi eszközökre is telepíthető, mint a Raspberry Pi vagy az ipari kiszolgáló. Miután egy eszköz konfigurálva van az IoT Edge-futásidejű, megkezdheti az üzleti logika üzembe helyezését a felhőből. További információ: [Az Azure IoT Edge futásidejű és architektúrájának megismerése.](iot-edge-runtime.md)

Ez a cikk az Azure IoT Edge-futásidejű x64-es, ARM32-es vagy ARM64-es Linux-eszközön történő telepítésének lépéseit sorolja fel. Telepítőcsomagokat biztosítunk az Ubuntu Server 16.04, ubuntu server 18.04 és Raspbian Stretch számára. A támogatott Linux operációs rendszerek és architektúrák listáját az [Azure IoT Edge által támogatott rendszerek](support.md#operating-systems) ben található.

> [!NOTE]
> A Linux szoftvertárolókban található csomagokra az egyes csomagokban található licencfeltételek vonatkoznak (/usr/share/doc/*package-name*). A csomag használata előtt olvassa el a licencfeltételeket. A csomag telepítése és használata azt jelenti, hogy ön elfogadja ezeket a feltételeket. Ha nem ért egyet a licencfeltételekkel, ne használja a csomagot.

## <a name="install-the-latest-runtime-version"></a>A legújabb futásidejű verzió telepítése

Az alábbi szakaszok segítségével telepítse az Azure IoT Edge futásidejű az eszközre legújabb verzióját.

>[!NOTE]
>Az ARM64-eszközök támogatása [nyilvános előzetes verzióban érhető el.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

### <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft-kulcs- és szoftvertár-hírcsatorna regisztrálása

Készítse elő az eszközt az IoT Edge futásidejű telepítéséhez.

Telepítse a tárház konfigurációját. Válassza ki a **16.04** vagy **18.04** parancsot, amely megfelel az eszköz operációs rendszerének:

* **Ubuntu Server 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Másolja a létrehozott listát.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

A Microsoft GPG nyilvános kulcsának telepítése

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>A tároló futási idejének telepítése

Az Azure IoT Edge egy [OCI-kompatibilis](https://www.opencontainers.org/) tároló futásórára támaszkodik. Éles környezetben azt javasoljuk, hogy használja a [Moby-alapú](https://mobyproject.org/) motor az alábbiakban megadott. A Moby motor az egyetlen tárolómotor, amelyet hivatalosan az Azure IoT Edge támogat. A Docker CE/EE tárolórendszerképek kompatibilisek a Moby-futásidővel.

Csomaglisták frissítése az eszközön.

   ```bash
   sudo apt-get update
   ```

Telepítse a Moby motort.

   ```bash
   sudo apt-get install moby-engine
   ```

Telepítse a Moby parancssori felületet (CLI). A CLI fejlesztési célokra hasznos, de éles környezetben választható.

   ```bash
   sudo apt-get install moby-cli
   ```

Ha hibákat tapasztal a Moby-tároló futásidejének telepítésekor, kövesse a [Linux kernel Moby-kompatibilitásának ellenőrzése](#verify-your-linux-kernel-for-moby-compatibility)című témakör lépéseit, amelyet a cikk későbbi részében ismertetett.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Az Azure IoT Edge biztonsági démon telepítése

Az **IoT Edge biztonsági démon** biztosítja és fenntartja a biztonsági szabványokat az IoT Edge-eszközön. A démon minden rendszerindításkor elindul, és az IoT Edge futásidő többi részének elindításával elindítja az eszközt.

A telepítési parancs telepíti a **libiothsm** standard verzióját is, ha még nincs jelen.

Csomaglisták frissítése az eszközön.

   ```bash
   sudo apt-get update
   ```

Telepítse a biztonsági démont. A csomag a `/etc/iotedge/`rendszerhez van telepítve.

   ```bash
   sudo apt-get install iotedge
   ```

Az IoT Edge sikeres telepítése után a kimenet a konfigurációs fájl frissítését kéri. Kövesse a biztonsági [démon konfigurálása](#configure-the-security-daemon) szakaszban az eszközkiépítés befejezéséhez című szakasz lépéseit.

## <a name="install-a-specific-runtime-version"></a>Adott futásidejű verzió telepítése

Ha a Moby és az Azure IoT Edge futásidejű egy adott verzióját szeretné telepíteni a legújabb verziók használata helyett, az összetevőfájlokat közvetlenül az IoT Edge GitHub-tárházból célozhatja meg. Az alábbi lépésekkel az összes IoT Edge-összetevőt bejuttathatja az eszközre: a Moby motorés a CLI, a libiothsm, és végül az IoT Edge biztonsági démon. Ugrás a következő szakaszra, [konfigurálja a biztonsági démont,](#configure-the-security-daemon)ha nem szeretne egy adott futásidejű verzióra váltani.

1. Keresse meg az [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases), és keresse meg a kívánt kiadási verziót.

2. Bontsa ki az **adott verzióhoz** az Eszközök szakaszt.

3. Lehet, hogy nem frissíti a Moby motor t any a given release. Ha olyan fájlokat lát, amelyek **a moby-motorral** és **a moby-cli-vel**kezdődnek, az alábbi parancsokkal frissítheti ezeket az összetevőket. Ha nem lát Moby-fájlokat, lépjen vissza a régebbi kiadási eszközökön, amíg meg nem találja a legújabb verziót.

   1. Keresse meg az IoT Edge-eszköz architektúrájának megfelelő **moby-motorfájlt.** Kattintson a jobb gombbal a fájlhivatkozásra, és másolja a hivatkozás címét.

   2. A Moby motor ezen verziójának telepítéséhez használja a következő parancsban található másolt hivatkozást:

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Keresse meg az IoT Edge-eszköz architektúrájának megfelelő **moby-cli** fájlt. A Moby CLI egy opcionális összetevő, de hasznos lehet a fejlesztés során. Kattintson a jobb gombbal a fájlhivatkozásra, és másolja a hivatkozás címét.

   4. A Moby CLI ezen verziójának telepítéséhez használja a következő parancsban található másolt hivatkozást:

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Minden kiadásnak új fájlokkal kell rendelkeznie az IoT Edge biztonsági démonhoz és a hsmlibhoz. Az összetevők frissítéséhez használja az alábbi parancsokat.

   1. Keresse meg az IoT Edge-eszköz architektúrájának megfelelő **libiothsm-std** fájlt. Kattintson a jobb gombbal a fájlhivatkozásra, és másolja a hivatkozás címét.

   2. A hsmlib adott verziójának telepítéséhez használja a következő parancsban található másolt hivatkozást:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Keresse meg az IoT Edge-eszköz architektúrájának megfelelő **iotedge** fájlt. Kattintson a jobb gombbal a fájlhivatkozásra, és másolja a hivatkozás címét.

   4. Az IoT Edge biztonsági démon ezen verziójának telepítéséhez használja a következő parancsban található másolt hivatkozást.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Az IoT Edge sikeres telepítése után a kimenet a konfigurációs fájl frissítését kéri. Kövesse a következő szakaszban az eszközkiépítés befejezéséhez.

## <a name="configure-the-security-daemon"></a>A biztonsági démon konfigurálása

Konfigurálja az IoT Edge futásidejű összekapcsolásával a fizikai eszköz egy Azure IoT-hubban létező eszközidentitással.

A démon konfigurálható a konfigurációs fájl `/etc/iotedge/config.yaml`használatával a rendszerben. A fájl írásvédett alapértelmezés szerint, előfordulhat, hogy emelt szintű engedélyekre van szüksége a szerkesztéshez.

Egyetlen IoT Edge-eszköz manuálisan kiépíthető az IoT Hub által biztosított eszközkapcsolati karakterlánc használatával. Vagy használhatja az eszközkiépítési szolgáltatás automatikusan eszközök kiépítése, ami akkor hasznos, ha sok eszközt kell kiépíteni. A ttól függően, hogy a kiépítési választás, válassza ki a megfelelő telepítési parancsfájlt.

### <a name="option-1-manual-provisioning"></a>1. lehetőség: Kézi kiépítés

Az eszköz manuális kiépítéséhez meg kell adnia egy [eszközkapcsolati karakterláncot,](how-to-register-device.md#register-in-the-azure-portal) amelyet létrehozhat egy új eszköz regisztrálásával az IoT hubban.

Nyissa meg a konfigurációs fájlt.

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg a kiépítési konfigurációk a fájl, és uncomment a **Kézi kiépítési konfiguráció szakasz.** Frissítse a **device_connection_string** értékét az IoT Edge-eszköz kapcsolati karakterláncával. Győződjön meg arról, hogy minden más kiépítési szakaszok megjegyzésekkel. Győződjön meg arról, hogy a **kiépítés:** sor nem rendelkezik előző szóközzel, és hogy a beágyazott elemeket két szóköz bevan húzva.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Vágólap tartalmának a `Shift+Right Click` Nano `Shift+Insert`programba történő beillesztéséhez vagy a billentyű t.

Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

Miután megadta a létesítési adatokat a konfigurációs fájlban, indítsa újra a démont:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>2. lehetőség: Automatikus kiépítés

Az IoT Edge-eszközök automatikusan kiépíthetők az [Azure IoT Hub-eszközlétesítési szolgáltatás (DPS)](../iot-dps/index.yml)használatával. Jelenleg az IoT Edge két tanúsítványmechanizmust támogat az automatikus üzembe építés használatakor, de a hardverkövetelmények hatással lehetnek a választási lehetőségekre. A Raspberry Pi-eszközök például alapértelmezés szerint nem rendelkeznek platformmegbízhatósági modul (TPM) chippel. További információkért tekintse át a következő cikkeket:

* [IoT Edge-eszköz létrehozása és kiépítése virtuális TPM-mel Linux os virtuális gépen](how-to-auto-provision-simulated-device-linux.md)
* [IoT Edge-eszköz létrehozása és kiépítése X.509-es tanúsítványokkal](how-to-auto-provision-x509-certs.md)
* [IoT Edge-eszköz létrehozása és kiépítése szimmetrikus kulcsigazolás sal](how-to-auto-provision-symmetric-keys.md)

Ezek a cikkek végigvezetik a dps-i igénylések beállításán, és a tanúsítványhoz tartozó tanúsítványok vagy kulcsok létrehozásán. Függetlenül attól, hogy melyik tanúsítványmechanizmust választja, a létesítési információk hozzáadódnak az IoT Edge konfigurációs fájlhoz az IoT Edge-eszközön.

Nyissa meg a konfigurációs fájlt.

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg a fájl létesítési konfigurációit, és ne fűzzön megjegyzést az igazolási mechanizmusnak megfelelő szakaszhoz. Győződjön meg arról, hogy minden más kiépítési szakaszok megjegyzésekkel. A **kiépítési:** sor nak nem lehet korábbi szóköz, és a beágyazott elemeket két szóközzel kell behúzni. Frissítse a **scope_id** értékét az IoT Hub-eszközkiépítési szolgáltatás példányának értékével, és adja meg a megfelelő értékeket a tanúsítványmezőkhöz.

TPM-igazolás:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

X.509 tanúsítvány:

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Szimmetrikus kulcsigazolás:

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

Vágólap tartalmának a `Shift+Right Click` Nano `Shift+Insert`programba történő beillesztéséhez vagy a billentyű t.

Mentse és zárja be a fájlt. `CTRL + X`, `Y`, `Enter`

Miután megadta a létesítési adatokat a konfigurációs fájlban, indítsa újra a démont:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha az előző szakaszban a **manuális konfigurációs** lépéseket használta, az IoT Edge futtatótime-ot sikeresen ki kell építeni, és az eszközön kell futtatni. Ha az **automatikus konfigurációs** lépéseket használta, majd néhány további lépést kell végrehajtania, hogy a futásidejű regisztrálhassa az eszközt az IoT hubon az Ön nevében. A következő lépésekért [lásd: Szimulált TPM IoT Edge-eszköz létrehozása és kiépítése Linux os virtuális gépen.](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)

Ellenőrizheti az IoT Edge démon állapotát:

```bash
systemctl status iotedge
```

Vizsgálja meg a démonnaplókat:

```bash
journalctl -u iotedge --no-pager --no-full
```

Futtassa a leggyakoribb konfigurációs és hálózati hibák automatikus ellenőrzését:

```bash
sudo iotedge check
```

Amíg az első modult az IoT Edge-re nem telepíti az eszközön, a **$edgeHub** rendszermodul nem lesz telepítve az eszközön. Ennek eredményeképpen az automatikus ellenőrzés hibát `Edge Hub can bind to ports on host` ad vissza a kapcsolat ellenőrzéséhez. Ez a hiba figyelmen kívül hagyható, kivéve, ha egy modul üzembe helyezése után az eszközre.

Végül, lista futó modulok:

```bash
sudo iotedge list
```

Az IoT Edge telepítése után az eszközre, az egyetlen modul, amit látnia kell, az **edgeAgent.** Az első üzembe helyezés létrehozása után a másik rendszermodul **$edgeHub** is elindul az eszközön. További információ: [IoT Edge-modulok üzembe helyezése.](how-to-deploy-modules-portal.md)

## <a name="tips-and-troubleshooting"></a>Tippek és hibaelhárítás

Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. A futásidejű telepítése után jelentkezzen ki a számítógépről, és jelentkezzen be újra az engedélyek automatikus frissítéséhez. Addig is, használja **sudo** `iotedge` előtt minden a parancsokat.

Erőforrás-korlátozott eszközökön erősen ajánlott az *OptimizeForPerformance* környezeti változót *hamisra* állítani a [hibaelhárítási útmutató](troubleshoot.md)utasításainak megfelelően.

Ha a hálózat, amely rendelkezik a proxy szerver, kövesse az [IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikáció](how-to-configure-proxy-support.md)lépéseit.

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>A Linux kernel ellenőrzése a Moby-kompatibilitás szempontjából

Számos beágyazott eszközgyártó szállítja az egyéni Linux kerneleket tartalmazó eszközképeket a tárolófutásidejű kompatibilitáshoz szükséges funkciók nélkül. Ha problémákba ütközik az ajánlott Moby-tároló futásidejének telepítése során, akkor a hivatalos [Moby GitHub-tárházból](https://github.com/moby/moby)származó [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) parancsfájl segítségével elháríthatja a Linux kernel konfigurációját. Futtassa a következő parancsokat az eszközön a kernel konfigurációjának ellenőrzéséhez:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Ez a parancs részletes kimenetet tartalmaz, amely a Moby-futásidő által használt kernelszolgáltatások állapotát tartalmazza. Győződjön meg arról, `Generally Necessary` hogy `Network Drivers` az összes elem alatt, és engedélyezve van annak érdekében, hogy a kernel teljes mértékben kompatibilis a Moby runtime.  Ha azonosította a hiányzó szolgáltatásokat, engedélyezze a rendszermag forrásból történő újraépítését és a kapcsolódó modulok kiválasztását a megfelelő kernel .config-ba való felvételhez.  Hasonlóképpen, ha a kernel konfigurációs `defconfig` `menuconfig`generátor, mint vagy , keresse meg és engedélyezze a megfelelő funkciókat, és újjáépíteni a kernel ennek megfelelően.  Az újonnan módosított rendszermag telepítése után futtassa újra a check-config parancsfájlt, és ellenőrizze, hogy az összes szükséges szolgáltatás sikeresen engedélyezve van-e.

## <a name="uninstall-iot-edge"></a>IoT Edge eltávolítása

Ha el szeretné távolítani az IoT Edge-telepítést a Linux-eszközről, használja a következő parancsokat a parancssorból.

Távolítsa el az IoT Edge-futtatókörnyezetet.

```bash
sudo apt-get remove --purge iotedge
```

Az IoT Edge-futásidő eltávolításakor a létrehozott tárolók leállnak, de továbbra is léteznek az eszközön. Tekintse meg az összes tárolót, hogy lássa, melyek maradnak.

```bash
sudo docker ps -a
```

Törölje a tárolókat az eszközről, beleértve a két futásidejű tárolók.

```bash
sudo docker rm -f <container name>
```

Végül távolítsa el a tároló futási idejét az eszközről.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>További lépések

Most, hogy egy IoT Edge-eszközt létesített a futásidejű telepítve, telepítheti az [IoT Edge-modulokat.](how-to-deploy-modules-portal.md)

Ha problémái vannak az IoT Edge futásidejű megfelelő telepítésével, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

Ha egy meglévő telepítést az IoT Edge legújabb verziójára szeretne frissíteni, olvassa [el az IoT Edge biztonsági démonának és futásidejű](how-to-update-iot-edge.md)verziójának frissítése című témakört.

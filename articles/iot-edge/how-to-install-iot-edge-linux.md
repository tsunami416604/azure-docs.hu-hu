---
title: A Azure IoT Edge telepítése Linux rendszeren | Microsoft Docs
description: Azure IoT Edge telepítési utasítások Ubuntu vagy Raspbian rendszerű Linux-eszközökön
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: aca417ebbc6f9af80058ddece32842f38918ce60
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964761"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>A Azure IoT Edge Runtime telepítése Debian-alapú Linux rendszereken

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet az eszközökön kisméretű, málna PI-ként vagy ipari kiszolgálóként is telepíthető. Ha egy eszköz konfigurálva van a IoT Edge futtatókörnyezettel, megkezdheti az üzleti logika telepítését a felhőből. További információ: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

Ez a cikk a Azure IoT Edge futtatókörnyezet x64-, ARM32-vagy ARM64-alapú Linux-eszközre történő telepítésének lépéseit sorolja fel. A telepítési csomagok az Ubuntu Server 16,04, az Ubuntu Server 18,04 és a Raspbian stretch számára érhetők el. A támogatott Linux operációs rendszerek és architektúrák listáját a [Azure IoT Edge támogatott rendszerek](support.md#operating-systems) című rész tartalmazza.

>[!NOTE]
>A ARM64-eszközök támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

> [!NOTE]
> A linuxos szoftverek tárházában lévő csomagokat az egyes csomagokban található licencfeltételek (/usr/share/doc/ *-csomag neve*) alapján kell megvizsgálni. A csomag használata előtt olvassa el a licencfeltételeket. A csomag telepítése és használata jelenti a jelen feltételek elfogadását. Ha nem fogadja el a licencfeltételeket, ne használja a csomagot.

## <a name="install-the-latest-runtime-version"></a>A legújabb futtatókörnyezet-verzió telepítése

Az alábbi részekben a Azure IoT Edge Runtime legújabb verzióját telepítheti az eszközre. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>A Microsoft-kulcs és a szoftver tárház-hírcsatorna regisztrálása

Készítse elő az eszközt az IoT Edge Runtime telepítéséhez.

Telepítse az adattár konfigurációját. Válassza ki a **16,04** vagy **18,04** parancsot, amely megfelel az eszköz operációs rendszerének:

* **Ubuntu Server 16,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian stretch**:
   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Másolja a generált listát.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

A Microsoft GPG nyilvános kulcsának telepítése

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>A tároló futtatókörnyezetének telepítése

Azure IoT Edge egy [OCI-kompatibilis tároló-](https://www.opencontainers.org/) futtatókörnyezetre támaszkodik. Az éles környezetekben ajánlott az alább ismertetett [Moby-alapú](https://mobyproject.org/) motort használni. Ez az egyetlen tároló motor, amelyet hivatalosan támogat a Azure IoT Edge. A Docker CE/EE tároló lemezképei kompatibilisek a Moby Runtime szolgáltatással.

Az apt-frissítés végrehajtása.

   ```bash
   sudo apt-get update
   ```

Telepítse a Moby Engine-t.

   ```bash
   sudo apt-get install moby-engine
   ```

Telepítse a Moby parancssori felületet (CLI). A CLI hasznos a fejlesztéshez, de nem kötelező az éles környezetekben való üzembe helyezéshez.

   ```bash
   sudo apt-get install moby-cli
   ```

Ha hiba lép fel a Moby Container Runtime telepítésekor, kövesse a jelen cikk későbbi részében ismertetett, a [Linux-kernel a Moby Compatibility szolgáltatással való ellenőrzésének](#verify-your-linux-kernel-for-moby-compatibility)lépéseit. 

### <a name="install-the-azure-iot-edge-security-daemon"></a>A Azure IoT Edge biztonsági démon telepítése

A **IoT Edge biztonsági démon** a IoT Edge eszközön biztosít és tart fenn biztonsági szabványokat. A démon minden rendszerindításkor elindul, és elindítja az eszközt a IoT Edge futtatókörnyezet további részének elindításával.

A telepítési parancs a **libiothsm** standard verzióját is telepíti, ha még nem létezik.

Az apt-frissítés végrehajtása.

   ```bash
   sudo apt-get update
   ```

Telepítse a biztonsági démont. A csomag telepítése `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

A IoT Edge sikeres telepítése után a kimenet rákérdez a konfigurációs fájl frissítésére. Az eszköz kiépítés befejezéséhez kövesse a [Azure IoT Edge biztonsági démon konfigurálása](#configure-the-security-daemon) című szakasz lépéseit. 

## <a name="install-a-specific-runtime-version"></a>Egy adott futtatókörnyezet-verzió telepítése

Ha a Azure IoT Edge futtatókörnyezet egy adott verzióját szeretné telepíteni, közvetlenül a IoT Edge GitHub-tárházból is megcélozhatja az összetevők fájljait. A következő lépésekkel szerezheti be az összes IoT Edge-összetevőt az eszközre: a Moby Engine és a CLI, a libiothsm, végül pedig a IoT Edge biztonsági démon.

1. Navigáljon a [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)kiadásokhoz, és keresse meg a célként használni kívánt kiadási verziót. 

2. Bontsa ki az **eszközök** szakaszt az adott verzióhoz.

3. Előfordulhat, hogy az adott kiadásban nem lehet frissíteni a Moby motort. Ha olyan fájlokat lát, amelyek a **Moby-Engine** és a **Moby-CLI**használatával kezdődnek, az alábbi parancsokkal frissítheti ezeket az összetevőket. Ha nem lát Moby-fájlokat, lépjen vissza a régebbi kiadási eszközökre, amíg meg nem találja a legújabb verziót. 

   1. Keresse meg a IoT Edge eszköz architektúrájának megfelelő **Moby-Engine-** fájlt. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe.

   2. A következő parancsban található másolt hivatkozás használatával telepítse a Moby Engine adott verzióját: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Keresse meg a IoT Edge eszköz architektúrájának megfelelő **Moby-CLI-** fájlt. A Moby CLI egy választható összetevő, de a fejlesztés során hasznos lehet. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe. 

   4. A következő parancsban található másolt hivatkozás használatával telepítse a Moby CLI adott verzióját: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Minden kiadásnak új fájlokkal kell rendelkeznie a IoT Edge biztonsági démonhoz és a hsmlib. Ezen összetevők frissítéséhez használja az alábbi parancsokat. 

   1. Keresse meg a IoT Edge eszköz architektúrájának megfelelő **libiothsm-STD-** fájlt. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe. 

   2. A következő parancsban található másolt hivatkozásra kattintva telepítheti a hsmlib adott verzióját:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. A IoT Edge eszköz architektúrájának megfelelő **iotedge** -fájl megkeresése. Kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe. 

   4. A következő parancsban található másolt hivatkozásra kattintva telepítse a IoT Edge biztonsági démon adott verzióját. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

A IoT Edge sikeres telepítése után a kimenet rákérdez a konfigurációs fájl frissítésére. Kövesse a következő szakaszban leírt lépéseket az eszköz kiépítés befejezéséhez. 

## <a name="configure-the-security-daemon"></a>A biztonsági démon konfigurálása

Konfigurálja a IoT Edge futtatókörnyezetet úgy, hogy az Azure IoT hub-ban található eszköz identitásával összekapcsolja a fizikai eszközt.

A démon a `/etc/iotedge/config.yaml` konfigurációs fájljának használatával konfigurálható. A fájl alapértelmezés szerint írásvédett, ezért a szerkesztéshez emelt szintű engedélyekre lehet szükség.

Egy IoT Edge eszköz manuálisan is kiépíthető a IoT Hub által biztosított eszköz-kapcsolati karakterlánc használatával. Vagy az eszköz kiépítési szolgáltatásával automatikusan kiépítheti az eszközöket, ami hasznos lehet, ha sok eszközt kell kiépíteni. A kiépítési döntéstől függően válassza ki a megfelelő telepítési parancsfájlt.

### <a name="option-1-manual-provisioning"></a>1\. lehetőség: manuális kiépítés

Az eszköz manuális kiépítéséhez meg kell adnia azt egy eszköz- [kapcsolódási karakterlánctal](how-to-register-device.md#register-in-the-azure-portal) , amelyet létrehozhat egy új eszköz regisztrálásával az IoT hub-ban.

Nyissa meg a konfigurációs fájlt.

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg a fájl üzembe helyezési konfigurációit, és tegye meg a **manuális kiépítési konfiguráció** szakasz megjegyzését. Frissítse a **device_connection_string** értékét a IoT Edge eszközről származó kapcsolatok karakterláncával. Ügyeljen rá, hogy a többi kiépítési szakaszt is jegyezze fel.

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
A vágólap tartalmának Nano `Shift+Right Click`ba való beillesztéséhez vagy a `Shift+Insert` megnyomásához.

Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

A konfigurációs fájlban szereplő kiépítési információk megadása után indítsa újra a démont:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>2\. lehetőség: automatikus kiépítés

Egy eszköz automatikus [kiépítéséhez állítsa be a Device kiépítési szolgáltatást, és kérje le az eszköz regisztrációs azonosítóját](how-to-auto-provision-simulated-device-linux.md). A IoT Edge az automatikus kiépítés használatakor számos igazolási mechanizmust támogat, de a hardverre vonatkozó követelmények is hatással vannak a választásokra. A málna PI-eszközök például nem rendelkeznek alapértelmezett platformmegbízhatósági modul (TPM) csiptel.

Nyissa meg a konfigurációs fájlt.

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

A vágólap tartalmának Nano `Shift+Right Click`ba való beillesztéséhez vagy a `Shift+Insert` megnyomásához.

Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

A konfigurációs fájlban szereplő kiépítési információk megadása után indítsa újra a démont:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha az előző szakaszban a **manuális konfigurációs** lépéseket használta, az IoT Edge futtatókörnyezetet sikeresen üzembe kell helyeznie és futnia kell az eszközön. Ha az **automatikus konfigurációs** lépéseket használta, néhány további lépést is végre kell hajtania, hogy a futtatókörnyezet regisztrálja az eszközt az IoT hub-ban az Ön nevében. A következő lépésekért lásd: [szimulált TPM-IoT Edge eszköz létrehozása és kiépítése Linux rendszerű virtuális gépen](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

A IoT Edge démon állapotát a következőket tekintheti meg:

```bash
systemctl status iotedge
```

Daemon-naplók vizsgálata:

```bash
journalctl -u iotedge --no-pager --no-full
```

Futtasson automatizált vizsgálatot a leggyakoribb konfigurációs és hálózati hibákhoz: 

```bash
sudo iotedge check
```

És, a futó modulok listázása:

```bash
sudo iotedge list
```

A IoT Edge telepítését követően az eszközön az egyetlen modulnak kell **edgeAgent**. Miután létrehozta az első telepítést, a másik rendszermodul **$edgeHub** is elindul az eszközön. További információ: IoT Edge- [modulok üzembe helyezése](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Tippek és hibaelhárítás

Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. A futtatókörnyezet telepítése után jelentkezzen ki a gépről, és jelentkezzen be ismét az engedélyek automatikus frissítéséhez. Addig használja a **sudo** -t a parancsok minden `iotedge` elé.

Az erőforrás által korlátozott eszközök esetében erősen ajánlott a *OptimizeForPerformance* környezeti változót *hamis* értékre állítani a [hibaelhárítási útmutató](troubleshoot.md)utasításai szerint.

Ha a hálózatban proxykiszolgáló található, kövesse az [IoT Edge eszköz konfigurálása a proxykiszolgálón keresztül történő kommunikációhoz](how-to-configure-proxy-support.md)című témakör lépéseit.

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>A Linux-kernel ellenőrzése a Moby kompatibilitás érdekében

Számos beágyazott eszköz gyártója olyan eszközöket biztosít, amelyek egyéni linuxos kerneleket tartalmaznak a tároló futásidejű kompatibilitásához szükséges szolgáltatások nélkül. Ha problémák merülnek fel a javasolt Moby Container Runtime telepítésekor, lehetséges, hogy a Linux-kernel konfigurációját a hivatalos [Moby GitHub-tárházból](https://github.com/moby/moby)származó [ellenőrzés-konfiguráció](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) parancsfájl használatával tudja elhárítani. Futtassa a következő parancsokat az eszközön a rendszermag konfigurációjának ellenõrzéséhez:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Ez egy részletes kimenetet ad meg, amely a Moby Runtime által használt kernel-funkciók állapotát tartalmazza. Győződjön meg arról, hogy a `Generally Necessary` és `Network Drivers` alatti összes elem engedélyezve van, hogy a rendszermag teljes mértékben kompatibilis legyen a Moby Runtime szolgáltatással.  Ha azonosította a hiányzó funkciókat, engedélyezze őket a kernel forrásból való újraépítésével, majd a megfelelő kernel. config fájlban való felvételhez kapcsolódó modulok kiválasztásával.  Hasonlóképpen, ha olyan kernel-konfigurációs generátort használ, mint például a defconfig vagy a menuconfig, keresse meg és engedélyezze a megfelelő szolgáltatásokat, és ennek megfelelően hozza létre újra a kernelt.  Miután telepítette az újonnan módosított kernelt, futtassa újra a bejelentkezést ellenőrző parancsfájlt annak ellenőrzéséhez, hogy az összes szükséges funkció engedélyezve lett-e.


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

## <a name="next-steps"></a>Következő lépések

Most, hogy már telepített egy IoT Edge eszközt a futtatott futtatókörnyezettel, telepítheti [IoT Edge modulokat](how-to-deploy-modules-portal.md).

Ha problémákat tapasztal a IoT Edge futtatókörnyezet megfelelő telepítésével kapcsolatban, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést szeretne frissíteni a IoT Edge legújabb verziójára, tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

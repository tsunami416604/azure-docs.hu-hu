---
title: A Azure IoT Edge telepítése Linux rendszeren | Microsoft Docs
description: Azure IoT Edge telepítési utasítások Ubuntu vagy Raspbian rendszerű Linux-eszközökön
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: kgremban
ms.openlocfilehash: 4078d7e6c20571db2387cfd138ecb325fc3469e7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022088"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Az Azure IoT Edge-futtatókörnyezet telepítése Debian-alapú Linux rendszereken

Az Azure IoT Edge futtatókörnyezet az eszköz IoT Edge eszközre való bekapcsolása. A futtatókörnyezet az eszközökön kisméretű, málna PI-ként vagy ipari kiszolgálóként is telepíthető. Miután konfigurált egy eszközt az IoT Edge-futtatókörnyezettel, üzembe helyezhet rajta üzleti logikát a felhőből. További információ: [a Azure IoT Edge futtatókörnyezet és az architektúrájának megismerése](iot-edge-runtime.md).

Ez a cikk a Azure IoT Edge futtatókörnyezet x64-, ARM32-vagy ARM64-alapú Linux-eszközre történő telepítésének lépéseit sorolja fel. Telepítési csomagokat biztosítunk Ubuntu Server 16,04, Ubuntu Server 18,04 és Raspbian stretch számára. A támogatott Linux operációs rendszerek és architektúrák listáját a [Azure IoT Edge támogatott rendszerek](support.md#operating-systems) című rész tartalmazza.

> [!NOTE]
> A linuxos szoftverek tárházában lévő csomagokat az egyes csomagokban található licencfeltételek (/usr/share/doc/*-csomag neve*) alapján kell megvizsgálni. A csomag használata előtt olvassa el a licencfeltételeket. A csomag telepítése és használata jelenti a jelen feltételek elfogadását. Ha nem fogadja el a licencfeltételeket, ne használja a csomagot.

## <a name="install-iot-edge-and-container-runtimes"></a>IoT Edge-és tároló-futtatókörnyezetek telepítése

Az alábbi részekben a Azure IoT Edge Runtime legújabb verzióját telepítheti az eszközre.

>[!NOTE]
>A ARM64-eszközök támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

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

Telepítse a Microsoft GPG nyilvános kulcsot.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Tároló-futtatókörnyezet telepítése

Azure IoT Edge egy [OCI-kompatibilis tároló-](https://www.opencontainers.org/) futtatókörnyezetre támaszkodik. Az éles környezetekben ajánlott az alább ismertetett [Moby-alapú](https://mobyproject.org/) motort használni. A Moby motor az egyetlen tároló motor, amelyet hivatalosan támogat a Azure IoT Edge. A Docker CE/EE tároló lemezképei kompatibilisek a Moby Runtime szolgáltatással.

Frissítési csomagok listája az eszközön.

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

Ha hibaüzenet jelenik meg a Moby Container Runtime telepítésekor, kövesse a jelen cikk későbbi részében ismertetett, a [Linux-kernel a Moby Compatibility szolgáltatással való ellenőrzésének](#verify-your-linux-kernel-for-moby-compatibility)lépéseit.

### <a name="install-the-azure-iot-edge-security-daemon"></a>A Azure IoT Edge biztonsági démon telepítése

A **IoT Edge biztonsági démon** a IoT Edge eszközön biztosít és tart fenn biztonsági szabványokat. A démon minden rendszerindításkor elindul, és elindítja az eszközt a IoT Edge futtatókörnyezet további részének elindításával.

Frissítési csomagok listája az eszközön.

   ```bash
   sudo apt-get update
   ```

Ellenőrizze, hogy a IoT Edge mely verziói érhetők el.

   ```bash
   apt list -a iotedge
   ```

Ha a biztonsági démon legújabb verzióját szeretné telepíteni, használja a következő parancsot, amely a **libiothsm-STD** csomag legújabb verzióját is telepíti:

   ```bash
   sudo apt-get install iotedge
   ```

Ha a biztonsági démon egy adott verzióját szeretné telepíteni, adja meg a verziót az apt List kimenetből. Ugyanezt a verziót is meg kell adni a **libiothsm-STD** csomaghoz, amely egyébként a legújabb verzióját telepíti. A következő parancs például a 1.0.8 kiadás legújabb verzióját telepíti:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Ha a telepíteni kívánt verzió nem szerepel a listáján, kövesse a [futtatókörnyezet telepítése a kiadási eszközök használatával](#install-runtime-using-release-assets)című témakör lépéseit. Ez a szakasz bemutatja, hogyan célozhatja meg a IoT Edge biztonsági démon vagy a Release Candidate verziójának korábbi verzióját.

IoT Edge sikeres telepítése után `/etc/iotedge/` a kimenet a konfigurációs fájl frissítését kéri. Folytassa a következő szakasszal az eszközök üzembe helyezésének befejezéséhez.

## <a name="configure-the-security-daemon"></a>A biztonsági démon konfigurálása

Konfigurálja a IoT Edge futtatókörnyezetet úgy, hogy az Azure IoT hub-ban található eszköz identitásával összekapcsolja a fizikai eszközt.

A démon a következő konfigurációs fájllal konfigurálható: `/etc/iotedge/config.yaml` . A fájl alapértelmezés szerint írásvédett, ezért a szerkesztéshez emelt szintű engedélyekre lehet szükség.

Egy IoT Edge eszköz manuálisan is kiépíthető a IoT Hub által biztosított eszköz-kapcsolati karakterlánc használatával. Vagy az eszköz kiépítési szolgáltatásával automatikusan kiépítheti az eszközöket, ami hasznos lehet, ha sok eszközt kell kiépíteni. A kiépítési döntéstől függően válassza ki a megfelelő telepítési parancsfájlt.

### <a name="option-1-manual-provisioning"></a>1. lehetőség: manuális kiépítés

Az eszköz manuális kiépítéséhez meg kell adnia azt egy eszköz- [kapcsolódási karakterlánctal](how-to-register-device.md#register-in-the-azure-portal) , amelyet létrehozhat egy új eszköz regisztrálásával az IoT hub-ban.

Nyissa meg a konfigurációs fájlt.

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg a fájl üzembe helyezési konfigurációit, és tegye meg a **manuális kiépítési konfiguráció** szakasz megjegyzését. **Device_connection_string** értékének frissítése a IoT Edge eszközhöz tartozó kapcsolatok karakterláncával. Ügyeljen rá, hogy a többi kiépítési szakaszt is jegyezze fel. Győződjön meg arról, hogy a **kiépítés:** sor nem tartalmaz korábbi szóközt, és a beágyazott elemek két szóközzel vannak behúzva.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

A vágólap tartalmának a Nanoba vagy a sajtóba való beillesztéséhez `Shift+Right Click` `Shift+Insert` .

Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

A konfigurációs fájlban szereplő kiépítési információk megadása után indítsa újra a démont:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>2. lehetőség: automatikus kiépítés

IoT Edge eszközök automatikusan üzembe helyezhetők az [Azure IoT hub Device Provisioning Service (DPS)](../iot-dps/index.yml)használatával. A IoT Edge jelenleg három igazolási mechanizmust támogat az automatikus kiépítés használatakor, de a hardverre vonatkozó követelmények befolyásolhatják a választásait. A málna PI-eszközök például nem rendelkeznek alapértelmezett platformmegbízhatósági modul (TPM) csiptel. További információkért tekintse át a következő cikkeket:

* [Virtuális TPM-sel rendelkező IoT Edge-eszköz létrehozása és kiépítése Linux rendszerű virtuális GÉPEN](how-to-auto-provision-simulated-device-linux.md)
* [IoT Edge-eszköz létrehozása és kiépítése X. 509 tanúsítványok használatával](how-to-auto-provision-x509-certs.md)
* [IoT Edge-eszköz létrehozása és kiépítése a szimmetrikus kulcs igazolásával](how-to-auto-provision-symmetric-keys.md)

Ezek a cikkek végigvezetik a regisztrációk a DPS-ben való beállításán, valamint a megfelelő tanúsítványok vagy kulcsok létrehozásán az igazoláshoz. Függetlenül attól, hogy melyik igazolási mechanizmust választja, a kiépítési információk bekerülnek a IoT Edge eszköz IoT Edge konfigurációs fájljába.

Nyissa meg a konfigurációs fájlt.

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg a fájl kiépítési konfigurációit, és adja meg az igazolási mechanizmusnak megfelelő szakaszt. Ügyeljen rá, hogy a többi kiépítési szakaszt is jegyezze fel. A **kiépítés:** sor nem rendelkezhet korábbi szóközzel, és a beágyazott elemeket két szóközzel kell behúzni. Frissítse a **scope_id** értékét a IoT hub Device Provisioning Service-példány értékével, és adja meg az igazolási mezők megfelelő értékeit.

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

X. 509 tanúsítvány:

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

Szimmetrikus kulcs igazolása:

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

A vágólap tartalmának a Nanoba vagy a sajtóba való beillesztéséhez `Shift+Right Click` `Shift+Insert` .

Mentse és zárja be a fájlt. `CTRL + X`, `Y`, `Enter`

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

A [hibaelhárítási eszköz](troubleshoot.md#run-the-check-command) futtatásával keresse meg a leggyakoribb konfigurációs és hálózati hibákat:

```bash
sudo iotedge check
```

Amíg nem telepíti az első modult az eszközön való IoT Edgere, a rendszer nem telepíti a **$edgeHub** rendszermodult az eszközre. Ennek eredményeképpen az automatikus ellenőrzés hibaüzenetet ad vissza a `Edge Hub can bind to ports on host` kapcsolat ellenőrzésekor. Ez a hiba figyelmen kívül hagyható, kivéve, ha egy modulnak az eszközre történő telepítése után következik be.

Végül a futó modulok listázása:

```bash
sudo iotedge list
```

A IoT Edge telepítését követően az eszközön az egyetlen modulnak kell **edgeAgent**. Miután létrehozta az első telepítést, a másik rendszermodul **$edgeHub** is elindul az eszközön. További információ: IoT Edge- [modulok üzembe helyezése](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Tippek és hibaelhárítás

Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. A futtatókörnyezet telepítése után jelentkezzen ki a gépről, és jelentkezzen be ismét az engedélyek automatikus frissítéséhez. Addig is használja a **sudo** -t a `iotedge` parancsok előtt.

Az erőforrás által korlátozott eszközök esetében erősen ajánlott a *OptimizeForPerformance* környezeti változót *hamis* értékre állítani a [hibaelhárítási útmutató](troubleshoot.md)utasításai szerint.

Ha a hálózatban proxykiszolgáló található, kövesse az [IoT Edge eszköz konfigurálása a proxykiszolgálón keresztül történő kommunikációhoz](how-to-configure-proxy-support.md)című témakör lépéseit.

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>A Linux-kernel ellenőrzése a Moby kompatibilitás érdekében

Számos beágyazott eszköz gyártója olyan eszközöket biztosít, amelyek egyéni linuxos kerneleket tartalmaznak a tároló futásidejű kompatibilitásához szükséges szolgáltatások nélkül. Ha problémák merülnek fel a javasolt Moby Container Runtime telepítésekor, lehetséges, hogy a Linux-kernel konfigurációját a hivatalos [Moby GitHub-tárházból](https://github.com/moby/moby)származó [ellenőrzés-konfiguráció](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) parancsfájl használatával tudja elhárítani. Futtassa a következő parancsokat az eszközön a rendszermag konfigurációjának ellenõrzéséhez:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Ez a parancs egy részletes kimenetet tartalmaz, amely a Moby Runtime által használt kernel-funkciók állapotát tartalmazza. Győződjön meg arról, hogy a és a alá tartozó összes elem engedélyezve van, hogy `Generally Necessary`  `Network Drivers` a kernel teljes mértékben kompatibilis legyen a Moby Runtime szolgáltatással.  Ha azonosította a hiányzó funkciókat, engedélyezze őket a kernel forrásból való újraépítésével, majd a megfelelő kernel. config fájlban való felvételhez kapcsolódó modulok kiválasztásával.  Hasonlóképpen, ha olyan kernel-konfigurációs generátort használ `defconfig` , mint a vagy a `menuconfig` , megkeresheti és engedélyezheti a megfelelő szolgáltatásokat, és ennek megfelelően újjáépítheti a kernelt.  Miután telepítette az újonnan módosított kernelt, futtassa újra a bejelentkezést ellenőrző parancsfájlt annak ellenőrzéséhez, hogy az összes szükséges funkció engedélyezve lett-e.

## <a name="install-runtime-using-release-assets"></a>Futtatókörnyezet telepítése a kiadási eszközök használatával

Az ebben a szakaszban ismertetett lépéseket követve telepítheti a Azure IoT Edge futtatókörnyezet adott verzióját, amely nem érhető el a rendszeren `apt-get install` . A Microsoft-csomagok listája csak korlátozott számú közelmúltbeli verziót és azok alverzióit tartalmazza, ezért ezek a lépések mindazok számára szükségesek, akik egy régebbi verziót vagy egy kiadásra jelölt verziót kívánnak telepíteni.

A curl-parancsok használatával közvetlenül a IoT Edge GitHub-tárházból is megcélozhatja az összetevők fájljait. A következő lépésekkel telepítheti a libiothsm és a IoT Edge biztonsági démont. Telepítse a Moby Engine-t és a CLI-t a [Container Runtime telepítése](#install-a-container-runtime) szakasz lépéseit követve.

1. Navigáljon a [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)kiadásokhoz, és keresse meg a célként használni kívánt kiadási verziót.

2. Bontsa ki az **eszközök** szakaszt az adott verzióhoz.

3. Minden kiadásnak új fájlokkal kell rendelkeznie a IoT Edge biztonsági démonhoz és a hsmlib. Ezen összetevők frissítéséhez használja az alábbi parancsokat.

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

IoT Edge sikeres telepítése után `/etc/iotedge` a kimenet a konfigurációs fájl frissítését kéri. A Device kiépítés befejezéséhez kövesse a [biztonsági démon konfigurálása](#configure-the-security-daemon) című szakaszban ismertetett lépéseket.

## <a name="uninstall-iot-edge"></a>IoT Edge eltávolítása

Ha el szeretné távolítani a IoT Edge telepítését a Linux-eszközről, használja a következő parancsokat a parancssorból.

Távolítsa el az IoT Edge-futtatókörnyezetet.

```bash
sudo apt-get remove --purge iotedge
```

A IoT Edge futtatókörnyezet eltávolításakor a létrehozott tárolók leállnak, de továbbra is léteznek az eszközön. Megtekintheti az összes tárolót, hogy azok megmaradjanak-e.

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

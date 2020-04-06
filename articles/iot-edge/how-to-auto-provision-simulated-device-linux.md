---
title: Eszköz kiépítése virtuális TPM-mel Linux os virtuális gépen – Azure IoT Edge
description: Szimulált TPM használata Linuxos virtuális számítógépen az Azure-eszközkiépítési szolgáltatás Azure IoT Edge-hez történő teszteléséhez
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/2/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b62f551e2532e0205159358b3618695524ae85c8
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666691"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>IoT Edge-eszköz létrehozása és kiépítése virtuális TPM-mel linuxos virtuális gépen

Az Azure IoT Edge-eszközök automatikusan kiépíthetők az [eszközkiépítési szolgáltatás](../iot-dps/index.yml)használatával. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át az [automatikus kiépítés iményét.](../iot-dps/concepts-auto-provisioning.md)

Ez a cikk bemutatja, hogyan tesztelheti az automatikus kiépítést egy szimulált IoT Edge-eszközön a következő lépésekkel:

* Hozzon létre egy Linux virtuális gépet (VM) a Hyper-V-ben egy szimulált platformmegbízhatósági modullal (TPM) a hardverbiztonság érdekében.
* Hozzon létre egy példányt az IoT Hub-eszközlétesítési szolgáltatás (DPS) példánya.
* Egyéni regisztráció létrehozása az eszközhöz
* Telepítse az IoT Edge futásidejű, és csatlakoztassa az eszközt az IoT Hub

> [!TIP]
> Ez a cikk ismerteti, hogyan tesztelhében DPS kiépítése egy TPM-szimulátor, de nagy része vonatkozik a fizikai TPM-hardver, például az [Infineon&trade; OPTIGA TPM,](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)egy Azure Certified IoT-eszköz.
>
> Ha fizikai eszközt használ, ugorjon előre a [kiépítési adatok lekérése a fizikai eszközről](#retrieve-provisioning-information-from-a-physical-device) szakaszebben a cikkben.

## <a name="prerequisites"></a>Előfeltételek

* Windows fejlesztőgép, amelyen [a Hyper-V engedélyezve van.](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Ez a cikk az Ubuntu Server virtuális gép futtatásával működő Windows 10-et használja.
* Egy aktív IoT Hub.
* Szimulált TPM használata esetén a [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015-ös vagy újabb verzióban engedélyezve van az ["Asztalfejlesztés C++"-os](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) munkaterheléssel.

> [!NOTE]
> A TPM 2.0 szükséges, ha A TPM tanúsítványt dps-szel használ, és csak egyéni, csoport- és regisztrációk létrehozására használható.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Linuxos virtuális gép létrehozása virtuális TPM-mel

Ebben a szakaszban hozzon létre egy új Linux virtuális gépet a Hyper-V-n. Ezt a virtuális gépet egy szimulált TPM-mel konfigurálta, hogy az Automatikus kiépítés ioT Edge-el való működésének teszteléséhez használhassa.

### <a name="create-a-virtual-switch"></a>Virtuális kapcsoló létrehozása

A virtuális kapcsoló lehetővé teszi, hogy a virtuális gép fizikai hálózathoz csatlakozzon.

1. Nyissa meg a Hyper-V Managert windowsos számítógépén.

2. A **Műveletek menüben** válassza a **Virtual Switch Manager parancsot.**

3. Válasszon egy **külső** virtuális kapcsolót, majd válassza **a Virtuális kapcsoló létrehozása**lehetőséget.

4. Adjon nevet az új virtuális kapcsolónak, például **EdgeSwitch**. Győződjön meg arról, hogy a kapcsolat típusa **Külső hálózat,** majd válassza az **Ok**gombot.

5. Egy előugró ablak figyelmeztet, hogy a hálózati kapcsolat megszakadhat. A folytatáshoz válassza az **Igen** lehetőséget.

Ha az új virtuális kapcsoló létrehozásakor hibákat lát, győződjön meg arról, hogy más kapcsolók nem használják az ethernet adaptert, és hogy más kapcsolók nem használják ugyanazt a nevet.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Töltsön le egy lemezképfájlt a virtuális géphez való használatra, és mentse helyileg. Például, [Ubuntu szerver](https://www.ubuntu.com/download/server).

2. A Hyper-V Manager ismét válassza az **Új** > **virtuális gép** a **Műveletek** menüben.

3. Töltse ki az **Új virtuális gép varázslót** a következő konfigurációkkal:

   1. **Létrehozás megadása**: **Válassza a 2.** A 2.
   2. **Hálózat konfigurálása**: Állítsa be a **Kapcsolat** értékét az előző szakaszban létrehozott virtuális kapcsolóval.
   3. **Telepítési beállítások:** Válassza **az Operációs rendszer telepítése rendszerindításra alkalmas lemezképfájlból lehetőséget,** és keresse meg a helyben mentett lemezképfájlt.

4. A virtuális gép létrehozásához válassza a varázsló **Befejezés** lehetőséget.

Az új virtuális gép létrehozása eltarthat néhány percig.

### <a name="enable-virtual-tpm"></a>Virtuális TPM engedélyezése

A virtuális gép létrehozása után nyissa meg a beállításokat a virtuális platformmodul (TPM) engedélyezéséhez, amely lehetővé teszi az eszköz automatikus kiépítését.

1. Jelölje ki a virtuális gépet, majd nyissa meg a **Beállítások lehetőséget.**

2. Keresse meg a **Biztonság**.

3. Törölje a jelet **a Biztonságos rendszerindítás engedélyezése**jelölőnégyzetből.

4. Jelölje be **a Platformmegbízhatósági modul engedélyezése jelölőnégyzetet.**

5. Kattintson az **OK** gombra.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>A virtuális gép indítása és A TPM-adatok gyűjtése

A virtuális gépben hozzon létre egy eszközt, amellyel lekérheti az eszköz **regisztrációs azonosítóját** és **ellenőrzőkulcsát.**

1. Indítsa el a virtuális gépet, és csatlakozzon hozzá.

1. Kövesse a virtuális gépen belüli utasításokat a telepítési folyamat befejezéséhez és a számítógép újraindításához.

1. Jelentkezzen be a virtuális gépre, majd kövesse a [Linux-fejlesztői környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) az Azure IoT-eszköz C-hez sdk telepítéséhez és létrehozásához című témakör lépéseit.

   >[!TIP]
   >A cikk során a virtuális gépre másolhat és illeszthet be, ami nem könnyű a Hyper-V Manager kapcsolatalkalmazáson keresztül. Előfordulhat, hogy egyszer a Hyper-V Manager segítségével szeretne csatlakozni `ifconfig`a virtuális géphez az IP-cím lekéréséhez: . Ezután az IP-címet használhatja az `ssh <username>@<ipaddress>`SSH-n keresztüli csatlakozáshoz: .

1. Futtassa a következő parancsokat az SDK-eszköz létrehozásához, amely lekéri az eszköz létesítési adatait a TPM-szimulátorból.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Egy parancsablakból keresse `azure-iot-sdk-c` meg a könyvtárat, és futtassa a TPM-szimulátort. A 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Ne zárja be ezt a parancsablakot; meg kell tartani ezt a szimulátort fut.

   A `azure-iot-sdk-c` könyvtárból futtassa a következő parancsot a szimulátor elindításához:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. A Visual Studio használatával nyissa meg `cmake` a `azure_iot_sdks.sln`könyvtárban létrehozott megoldást, és hozza létre a **Build** menü **Megoldás összeállítása** parancsával.

1. A Visual Studio **Solution Explorer** (Megoldáskezelő) panelén lépjen a **Provision\_Tools** mappára. Kattintson a jobb gombbal a **tpm_device_provision** projektre, és válassza a **Set as Startup Project** (Beállítás kezdőprojektként) lehetőséget.

1. Futtassa a megoldást a **Debug** menü **Start** parancsaival. A kimeneti ablakban megjelenik a TPM-szimulátor **regisztrációs azonosítója** és az **Ellenőrző kulcs**, amelyet később kell másolnia, amikor egyéni regisztrációt hoz létre az eszközhöz: Bezárhatja ezt az ablakot (regisztrációs azonosítóval és ellenőrző kulccsal), de hagyja futni a TPM-szimulátor ablakát.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Kiépítési adatok lekérése fizikai eszközről

Az eszközén hozzon létre egy eszközt, amellyel lekérheti az eszköz létesítési adatait.

1. Kövesse a [Linux-fejlesztői környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) az Azure IoT-eszköz C-hez való telepítéséhez és létrehozásához című témakör lépéseit.

1. Futtassa a következő parancsokat az SDK-eszköz létrehozásához, amely lekéri az eszköz létesítési adatait a TPM-eszközről.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Másolja a **regisztrációs azonosító** és az **ellenőrzőkulcs értékeit.** Ezekkel az értékekkel egyéni regisztrációt hozhat létre az eszközdps-ben.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Az IoT Hub-eszközkiépítési szolgáltatás beállítása

Hozzon létre egy új példányt az IoT Hub-eszközkiépítési szolgáltatás az Azure-ban, és kapcsolja össze az IoT hub. Az [IoT Hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című útmutató utasításait követheti.

Miután futtatta az eszközkiépítési szolgáltatást, másolja az **ID Scope** értékét az áttekintő lapról. Ezt az értéket használja az IoT Edge futásidejű konfigurálásakor.

## <a name="create-a-dps-enrollment"></a>DPS-regisztráció létrehozása

A kiépítési adatok lekérése a virtuális gépről, és használja, hogy hozzon létre egy egyéni regisztráció eszközkiépítési szolgáltatás.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van egy **kezdeti ikereszköz-állapot**deklarálni. Az ikereszközben beállíthatja az eszközök csoportosítását a megoldásban szükséges bármely mutató, például a régió, a környezet, a hely vagy az eszköz típusa szerint. Ezek a címkék az automatikus központi telepítések létrehozásához [használatosak.](how-to-deploy-monitor.md)

> [!TIP]
> Az Azure CLI-ben létrehozhat egy [regisztrációt](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) vagy egy [regisztrációs csoportot,](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) és a **peremhálózati jelző** használatával megadhatja, hogy egy eszköz vagy eszközcsoport egy IoT Edge-eszköz.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az IoT Hub-eszközkiépítési szolgáltatás példányát.

2. A **Beállítások csoportban**válassza **a Regisztrációk kezelése**lehetőséget.

3. Válassza **az Egyéni regisztráció hozzáadása lehetőséget,** majd hajtsa végre a következő lépéseket a regisztráció konfigurálásához:  

   1. A **Mechanizmus**csoportban válassza a **TPM**lehetőséget.

   2. Adja meg a virtuális gépről másolt **ellenőrzőkulcsot** és **regisztrációs azonosítót.**

      > [!TIP]
      > Ha fizikai TPM-eszközt használ, meg kell **határoznia**az Ellenőrző kulcsot , amely minden Egyes TPM-chipre egyedi, és amelyet a hozzá társított TPM-chipgyártó tól kell beszereznie. A TPM-eszköz egyedi **regisztrációs azonosítóját** származtathatja, például létrehozhatja az ellenőrzőkulcs SHA-256 kivonatát.

   3. **Válassza a True** lehetőséget, ha deklarálni szeretné, hogy ez a virtuális gép egy IoT Edge-eszköz.

   4. Válassza ki azt a csatolt **IoT-központot,** amelyhez csatlakoztatni szeretné az eszközt. Több elosztót is kiválaszthat, és az eszköz a kiválasztott foglalási házirendnek megfelelően lesz hozzárendelve az egyikhez.

   5. Adja meg az eszköz azonosítóját, ha szeretné. Az eszközazonosítók segítségével megcélozhat egy adott eszközt a modul telepítéséhez. Ha nem adja meg az eszközazonosítót, a regisztrációs azonosítót használja a készülék.

   6. Ha szeretné, adjon hozzá egy címkeértéket a **kezdeti ikereszköz-állapothoz.** A címkék segítségével a modul üzembe helyezéséhez eszközcsoportokat célozhat meg. Példa:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Kattintson a **Mentés** gombra.

Most, hogy egy regisztráció létezik ehhez az eszközhöz, az IoT Edge futásidejű automatikusan kiépítheti az eszközt a telepítés során.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge futásidejű telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközre, így a kód a peremhálózaton futtatható. Telepítse az IoT Edge futásidejű a virtuális gépen.

Ismerje meg a **DPS-azonosító hatókörét** és az eszköz **regisztrációs azonosítóját,** mielőtt elkezdene az eszköztípusnak megfelelő cikket. Ha telepítette a példa Ubuntu szerver, használja az **x64** utasításokat. Győződjön meg arról, hogy konfigurálja az IoT Edge futásidejű automatikus, nem manuális, kiépítés.

[Az Azure IoT Edge futásidejű telepítésélinuxra](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge-hozzáférés a TPM-hez

Annak érdekében, hogy az IoT Edge futásidejű automatikusan kiépíti az eszközt, hozzá kell férnie a TPM-hez.

A TPM-hozzáférést az IoT Edge futásidejű felülbírálásával a rendszerezött beállításokat, hogy a **iotedge** szolgáltatás root jogosultságokkal rendelkezik. Ha nem szeretné magasabbra emelni a szolgáltatási jogosultságokat, az alábbi lépésekkel manuálisan is biztosíthatja a TPM-hozzáférést.

1. Keresse meg az eszközön a TPM-hardvermodul fájlelérési útját, és mentse helyi változóként.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Hozzon létre egy új szabályt, amely hozzáférést biztosít az IoT Edge futásidejű hozzáférést a tpm0.Create a new rule that will give the IoT Edge runtime access to tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Nyissa meg a szabályfájlt.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Másolja a következő hozzáférési adatokat a szabályfájlba.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Mentse és lépjen ki a fájlból.

6. Indítsa el az udev rendszert az új szabály kiértékeléséhez.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ellenőrizze, hogy a szabály alkalmazása sikeresen megtörtént-e.

   ```bash
   ls -l /dev/tpm0
   ```

   A sikeres kimenet a következőképpen néz ki:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Ha nem látja, hogy a megfelelő engedélyeket alkalmazta, próbálja meg újraindítani a számítógépet az udev frissítéséhez.

## <a name="restart-the-iot-edge-runtime"></a>Indítsa újra az IoT Edge futásidejét

Indítsa újra az IoT Edge futásidejű, hogy felveszi az eszközön végrehajtott összes konfigurációs módosítást.

   ```bash
   sudo systemctl restart iotedge
   ```

Ellenőrizze, hogy az IoT Edge futtatótér fut-e.

   ```bash
   sudo systemctl status iotedge
   ```

Ha kiépítési hibákat lát, előfordulhat, hogy a konfigurációs módosítások még nem léptek érvénybe. Próbálja meg újraindítani az IoT Edge démont.

   ```bash
   sudo systemctl daemon-reload
   ```

Vagy próbálja meg újraindítani a virtuális gépet, hogy lássa, hogy a módosítások új kezdet esetén lépnek-e életbe.

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futásidő sikeresen elindult, az IoT Hubba léphet, és láthatja, hogy az új eszköz automatikusan ki lett építve. Most az eszköz készen áll az IoT Edge-modulok futtatására.

Ellenőrizze az IoT Edge démon állapotát.

```cmd/sh
systemctl status iotedge
```

Vizsgálja meg a démonnaplókat.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Futó modulok listája.

```cmd/sh
iotedge list
```

Ellenőrizheti, hogy az eszközkiépítési szolgáltatásban létrehozott egyéni regisztrációt használta-e. Keresse meg az Eszközkiépítési szolgáltatás példányát az Azure Portalon. Nyissa meg a létrehozott egyéni beléptetési adatokat. Figyelje meg, hogy a regisztráció állapota hozzá van **rendelve,** és az eszközazonosító szerepel a listában.

## <a name="next-steps"></a>További lépések

Az eszközkiépítési szolgáltatás regisztrációs folyamat lehetővé teszi, hogy az eszközazonosítóés az ikercímkék beállítása az új eszköz kiépítésével egy időben. Ezeket az értékeket használhatja az egyes eszközök vagy eszközcsoportok automatikus eszközkezelés használatával történő célzásához. Ismerje meg, hogyan [telepítheti és figyelheti az IoT Edge-modulokat az Azure Portalon](how-to-deploy-monitor.md) vagy az [Azure CLI használatával.](how-to-deploy-monitor-cli.md)

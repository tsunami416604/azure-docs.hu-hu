---
title: Eszköz kiépítése virtuális TPM-sel Linux rendszerű virtuális GÉPEN – Azure IoT Edge
description: Szimulált TPM használata Linux rendszerű virtuális gépen az Azure-eszközök kiépítési szolgáltatásának teszteléséhez Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511059"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Virtuális TPM-sel rendelkező IoT Edge-eszköz létrehozása és kiépítése Linux rendszerű virtuális gépen

Azure IoT Edge eszközök automatikusan üzembe helyezhetők az [eszköz kiépítési szolgáltatásával](../iot-dps/index.yml). Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át az [automatikus kiépítési fogalmakat](../iot-dps/concepts-auto-provisioning.md) .

Ez a cikk bemutatja, hogyan tesztelheti az automatikus kiépítést egy szimulált IoT Edge eszközön a következő lépésekkel:

* Hozzon létre egy Linux rendszerű virtuális gépet (VM) a Hyper-V-ben egy szimulált platformmegbízhatósági modul (TPM) a hardveres biztonság érdekében.
* Hozzon létre egy IoT Hub Device Provisioning Service (DPS) egy példányát.
* Egyéni regisztráció létrehozása az eszközhöz
* A IoT Edge futtatókörnyezet telepítése és az eszköz csatlakoztatása IoT Hub

> [!TIP]
> Ez a cikk bemutatja, hogyan tesztelheti a DPS-kiépítés TPM-szimulátor használatával történő tesztelését, de a fizikai TPM-hardverek nagy része, például az [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), egy Azure Certified for IoT-eszköz.
>
> Ha fizikai eszközt használ, ugorjon a jelen cikk [fizikai eszközről származó kiépítési információinak lekérése](#retrieve-provisioning-information-from-a-physical-device) szakaszára.

## <a name="prerequisites"></a>Előfeltételek

* [Hyper-V-t használó](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)Windows-fejlesztői gép. Ez a cikk az Ubuntu Server rendszerű virtuális gépet futtató Windows 10-es verzióját használja.
* Aktív IoT Hub.
* Ha szimulált TPM-t, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015-es vagy újabb verziót használ az [" C++asztali fejlesztés](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) a munkaterheléssel" beállítással.

> [!NOTE]
> A TPM 2,0-es kiadására akkor van szükség, ha a TPM-igazolást a DPS használatával használja, és csak egyéni, nem csoportos, regisztrációkat hozhat létre.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Linuxos virtuális gép létrehozása virtuális TPM-sel

Ebben a szakaszban egy új linuxos virtuális gépet hoz létre a Hyper-V-n. Ezt a virtuális gépet egy szimulált TPM-sel konfigurálta, így azt tesztelheti, hogy az automatikus kiépítés hogyan működik a IoT Edgeokkal.

### <a name="create-a-virtual-switch"></a>Virtuális kapcsoló létrehozása

A virtuális kapcsolók lehetővé teszik a virtuális gép számára a fizikai hálózathoz való kapcsolódást.

1. Nyissa meg a Hyper-V kezelőjét a Windows rendszerű gépen.

2. A **műveletek** menüben válassza a **Virtual switch Manager**lehetőséget.

3. Válasszon ki egy **külső** virtuális kapcsolót, majd kattintson a **virtuális kapcsoló létrehozása**elemre.

4. Adja meg az új virtuális kapcsoló nevét, például **EdgeSwitch**. Győződjön meg arról, hogy a kapcsolattípus **külső hálózatra**van beállítva, majd válassza **az OK gombot**.

5. Egy előugró ablak figyelmezteti, hogy a hálózati kapcsolat megszakadhat. A folytatáshoz válassza az **Igen** lehetőséget.

Ha az új virtuális kapcsoló létrehozásakor hibák jelennek meg, győződjön meg arról, hogy egyetlen másik kapcsoló sem használja az Ethernet-adaptert, és hogy más kapcsolók nem használják ugyanazt a nevet.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Töltse le a virtuális géphez használni kívánt lemezképfájlt, és mentse helyileg. Például: [Ubuntu Server](https://www.ubuntu.com/download/server).

2. A Hyper-V kezelőjében ismét válassza a **műveletek** menü **új** > **virtuális gép** elemét.

3. Fejezze be az **új virtuális gép varázslót** a következő beállításokkal:

   1. **Generáció megadása**: válassza a **2. generáció**lehetőséget. A 2. generációs virtuális gépeken engedélyezve van a beágyazott virtualizálás, amely a IoT Edge virtuális gépen való futtatásához szükséges.
   2. **Hálózatkezelés beállítása**: állítsa be az előző szakaszban létrehozott virtuális kapcsolóhoz való **Kapcsolódás** értékét.
   3. **Telepítési beállítások**: válassza az **operációs rendszer telepítése rendszerindító lemezképfájlból** lehetőséget, és keresse meg a helyileg mentett lemezképfájlt.

4. A virtuális gép létrehozásához válassza a **Befejezés** lehetőséget a varázslóban.

Az új virtuális gép létrehozása több percet is igénybe vehet.

### <a name="enable-virtual-tpm"></a>Virtuális TPM engedélyezése

A virtuális gép létrehozása után nyissa meg a beállításait a Virtual platformmegbízhatósági modul (TPM) engedélyezéséhez, amely lehetővé teszi az eszköz automatikus kiépítését.

1. Válassza ki a virtuális gépet, majd nyissa meg a **beállításait**.

2. Navigáljon a **Biztonság**elemre.

3. Törölje a **biztonságos rendszerindítás engedélyezése**jelölőnégyzet jelölését.

4. Jelölje be az **engedélyezés platformmegbízhatósági modul**.

5. Kattintson az **OK** gombra.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>A virtuális gép elindítása és a TPM-adatok összegyűjtése

A virtuális gépen hozzon létre egy eszközt, amely segítségével lekérheti az eszköz **regisztrációs azonosítóját** és a **hátirat kulcsát**.

1. Indítsa el a virtuális gépet, és kapcsolódjon hozzá.

1. Kövesse a virtuális gépen megjelenő utasításokat a telepítési folyamat befejezéséhez és a számítógép újraindításához.

1. Jelentkezzen be a virtuális gépre, majd kövesse a [Linux fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) az Azure IOT Device SDK for C telepítéséhez és felépítéséhez című témakör lépéseit.

   >[!TIP]
   >Ebben a cikkben a virtuális gépről másolja a vágólapra, és illessze be a vágólapra, amely nem könnyű a Hyper-V Manager-alapú kapcsolódási alkalmazáson keresztül. A Hyper-V kezelőjével a virtuális géphez való kapcsolódáshoz a következő IP-cím lekérése után lehet szükség: `ifconfig`. Ezután az IP-címet használhatja az SSH-n keresztül történő kapcsolódáshoz: `ssh <username>@<ipaddress>`.

1. A következő parancsok futtatásával hozza létre azt az SDK-eszközt, amely az eszköz kiépítési adatait a TPM-szimulátorból kéri le.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. A parancssorablakban navigáljon a `azure-iot-sdk-c` könyvtárba, és futtassa a TPM-szimulátort. A 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Ne zárjuk be a parancssorablakot; a szimulátort továbbra is futtatnia kell.

   A `azure-iot-sdk-c` könyvtárból futtassa a következő parancsot a szimulátor elindításához:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. A Visual Studióban nyissa meg a `azure_iot_sdks.sln`nevű `cmake` könyvtárban létrehozott megoldást, és hozza **létre a Build menü Build** **Solution** parancsának használatával.

1. A Visual Studio **Solution Explorer** (Megoldáskezelő) panelén lépjen a **Provision\_Tools** mappára. Kattintson a jobb gombbal a **tpm_device_provision** projektre, és válassza a **Set as Startup Project** (Beállítás kezdőprojektként) lehetőséget.

1. Futtassa a megoldást a **hibakeresés** menü egyik **indítási** parancsával. A kimeneti ablak megjeleníti a TPM-szimulátor **regisztrációs azonosítóját** és a **záradék kulcsát**, amelyet később kell másolni, amikor az eszközhöz egyéni regisztrációt hoz létre, akkor lezárhatja ezt az ABLAKOT (a regisztrációs azonosítóval és a jóváhagyás kulcsával), de a TPM-szimulátor ablakát ne futtassa.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Kiépítési információk beolvasása fizikai eszközről

Az eszközön hozzon létre egy eszközt, amely segítségével lekérheti az eszköz kiépítési információit.

1. Kövesse a [Linux fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) az Azure IOT Device SDK for C telepítéséhez és felépítéséhez című témakör lépéseit.

1. A következő parancsok futtatásával hozza létre azt az SDK-eszközt, amely lekéri az eszköz kiépítési információit a TPM-eszközről.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Másolja a **regisztrációs azonosító** és a **jóváhagyó kulcs**értékeit. Ezeknek az értékeknek a használatával egyéni regisztrációt hozhat létre az eszközhöz a DPS-ben.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>A IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányt a IoT Hub Device Provisioning Service az Azure-ban, és kapcsolja össze az IoT hubhoz. A [IOT hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című témakör útmutatását követve végezheti el.

Miután futtatta az eszköz kiépítési szolgáltatását, másolja az **azonosító hatókör** értékét az Áttekintés lapról. Ezt az értéket használja a IoT Edge futtatókörnyezet konfigurálásakor.

## <a name="create-a-dps-enrollment"></a>DPS-regisztráció létrehozása

Kérje le a kiépítési információkat a virtuális gépről, és ezt használva hozzon létre egy egyéni regisztrációt a Device kiépítési szolgáltatásban.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az eszköz Twin-ben címkéket állíthat be az eszközök csoportosításához a megoldásban szükséges mérőszámok, például a régió, a környezet, a hely vagy az eszköz típusa alapján. Ezek a címkék [automatikus központi telepítések](how-to-deploy-monitor.md)létrehozásához használhatók.

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához.

2. A **Beállítások**területen válassza a **regisztrációk kezelése**lehetőséget.

3. Válassza az **Egyéni regisztráció hozzáadása** lehetőséget, majd végezze el a következő lépéseket a beléptetés konfigurálásához:  

   1. A **mechanizmus**beállításnál válassza a **TPM**lehetőséget.

   2. Adja meg a virtuális gépről másolt **hátirat-kulcsot** és **regisztrációs azonosítót** .

      > [!TIP]
      > Ha fizikai TPM-eszközt használ, meg kell határoznia a **jóváhagyás kulcsát**, amely minden TPM-lapka esetében egyedi, és a hozzá társított TPM-lapka gyártójától származik. Létrehozhat egy egyedi **regisztrációs azonosítót** a TPM-eszközhöz, például a HÁTIRAT kulcs SHA-256 kivonatának létrehozásával.

   3. A **true (igaz** ) érték kiválasztásával deklarálhatja, hogy a virtuális gép IoT Edge eszköz.

   4. Válassza ki azt a csatolt **IoT hub** , amelyhez csatlakoztatni szeretné az eszközt. Több hub is kiválasztható, és az eszköz a kiválasztott foglalási szabályzatnak megfelelően lesz hozzárendelve az egyikhez.

   5. Ha szeretné, adja meg az eszköz AZONOSÍTÓját. Az eszközök azonosítói segítségével megcélozhat egy különálló eszközt a modulok telepítéséhez. Ha nem adja meg az eszköz AZONOSÍTÓját, a rendszer a regisztrációs azonosítót használja.

   6. Ha szeretné, adjon hozzá egy címke értéket a **kezdeti eszköz iker állapotához** . A címkék használatával megcélozhatja az eszközök csoportjait a modulok üzembe helyezéséhez. Példa:

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

Most, hogy az eszközhöz regisztrálva van, a IoT Edge futtatókörnyezet automatikusan kiépítheti az eszközt a telepítés során.

## <a name="install-the-iot-edge-runtime"></a>A IoT Edge futtatókörnyezet telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközön, így a kód a peremhálózat szélén is futtatható. Telepítse a IoT Edge futtatókörnyezetet a virtuális gépre.

Az eszköz típusának megfelelő cikk megkezdése előtt Ismerje meg a DPS- **azonosító hatókörét** és az eszköz **regisztrációs azonosítóját** . Ha telepítette az Ubuntu-kiszolgálót, használja az **x64** -es utasításokat. Győződjön meg arról, hogy a IoT Edge futtatókörnyezet automatikus, nem manuális, kiépítés esetén van konfigurálva.

[A Azure IoT Edge Runtime telepítése Linux rendszeren](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge hozzáférés biztosítása a TPM-hez

Ahhoz, hogy a IoT Edge futtatókörnyezet automatikusan kiépítse az eszközt, hozzá kell férnie a TPM-hez.

A rendszerszintű beállítások felülbírálása érdekében engedélyezheti a TPM-hez való hozzáférést a IoT Edge futtatókörnyezet számára, hogy a **iotedge** szolgáltatás gyökérszintű jogosultságokkal rendelkezzen. Ha nem szeretné megemelni a szolgáltatási jogosultságokat, az alábbi lépéseket követve manuálisan is megadhatja a TPM-hozzáférést.

1. Keresse meg az eszközön a TPM hardveres modul elérési útját, és mentse helyi változóként.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Hozzon létre egy új szabályt, amely megadja a IoT Edge Runtime hozzáférést a tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Nyissa meg a szabályok fájlt.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Másolja a következő hozzáférési adatokat a szabályok fájlba.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Mentse és zárja be a fájlt.

6. Aktiválja a udev rendszerét az új szabály kiértékeléséhez.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Ellenőrizze, hogy a szabály alkalmazása sikeresen megtörtént-e.

   ```bash
   ls -l /dev/tpm0
   ```

   A sikeres kimenet a következőhöz hasonlóan néz ki:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Ha nem látja, hogy a megfelelő engedélyek lettek alkalmazva, próbálja meg újraindítani a gépet a udev frissítéséhez.

## <a name="restart-the-iot-edge-runtime"></a>A IoT Edge futtatókörnyezet újraindítása

Indítsa újra a IoT Edge futtatókörnyezetet, hogy az az eszközön végrehajtott összes konfigurációs módosítást felveszi.

   ```bash
   sudo systemctl restart iotedge
   ```

Ellenőrizze, hogy fut-e a IoT Edge futtatókörnyezet.

   ```bash
   sudo systemctl status iotedge
   ```

Ha a kiépítési hibák jelennek meg, előfordulhat, hogy a konfiguráció módosítása még nem lépett érvénybe. Próbálkozzon újra a IoT Edge démon újraindításával.

   ```bash
   sudo systemctl daemon-reload
   ```

Vagy próbálja meg újraindítani a virtuális gépet, és ellenőrizze, hogy a módosítások érvénybe lépnek-e egy új indításkor.

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futtatókörnyezet sikeresen elindult, beléphet a IoT Hubba, és láthatja, hogy az új eszköz automatikusan lett kiépítve. Az eszköz most már készen áll IoT Edge modulok futtatására.

A IoT Edge démon állapotának bejelölése.

```cmd/sh
systemctl status iotedge
```

Daemon-naplók vizsgálata.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Futó modulok listázása.

```cmd/sh
iotedge list
```

Ellenőrizheti, hogy a rendszer használta-e a Device kiépítési szolgáltatásban létrehozott egyéni regisztrációt. Navigáljon az eszköz kiépítési szolgáltatási példányához a Azure Portal. Nyissa meg a regisztráció részleteit a létrehozott egyéni regisztrációhoz. Figyelje meg, hogy a regisztráció állapota **hozzá van rendelve** , és az eszköz azonosítója megjelenik.

## <a name="next-steps"></a>Következő lépések

Az eszköz kiépítési szolgáltatásának beléptetési folyamata lehetővé teszi, hogy az eszköz AZONOSÍTÓját és az eszköz Twin címkéit az új eszköz kiépítésekor egy időben állítsa be. Ezeket az értékeket használhatja az egyes eszközök vagy eszközök automatikus eszközkezelés használatával történő megcélzásához. Megtudhatja, hogyan [helyezheti üzembe és figyelheti IoT Edge-modulok méretezését a Azure Portal vagy az](how-to-deploy-monitor.md) [Azure CLI használatával](how-to-deploy-monitor-cli.md).

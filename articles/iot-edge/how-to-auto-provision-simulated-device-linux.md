---
title: Linux-eszközök automatikus kiépítése a DPS-Azure IoT Edgerel | Microsoft Docs
description: Linux rendszerű virtuális gép egy szimulált TPM-eszköz segítségével tesztelheti az Azure Device Provisioning Service az Azure IoT Edge-hez
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 228851a0d528bfb222e5aa19880f856424e95ad1
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828129"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Virtuális TPM-sel rendelkező IoT Edge-eszköz létrehozása és kiépítése Linux rendszerű virtuális gépen

Azure IoT Edge eszközök automatikusan üzembe helyezhetők az [eszköz kiépítési szolgáltatásával](../iot-dps/index.yml). Ha még nem ismeri az Automatikus kiépítés folyamatát, tekintse át a [automatikus kiépítés alapfogalmait](../iot-dps/concepts-auto-provisioning.md) a folytatás előtt.

Ez a cikk bemutatja, hogyan tesztelheti az automatikus kiépítést egy szimulált IoT Edge eszközön a következő lépésekkel:

* Hozzon létre egy Linux rendszerű virtuális gép (VM) a Hyper-V és a egy szimulált platformmegbízhatósági modul (TPM) a hardveres biztonsági.
* Hozzon létre egy példányt, IoT Hub Device Provisioning szolgáltatás (DPS).
* Hozzon létre egyéni regisztrációt az eszköz
* Telepítse az IoT Edge-futtatókörnyezet és az eszköz csatlakoztatása az IoT hubhoz

> [!NOTE]
> A TPM 2,0-es kiadására akkor van szükség, ha a TPM-igazolást a DPS használatával használja, és csak egyéni, nem csoportos, regisztrációkat hozhat létre.

> [!TIP]
> Ez a cikk bemutatja, hogyan tesztelheti a DPS-kiépítés TPM-szimulátor használatával történő tesztelését, de a fizikai TPM-hardverek nagy része, például az [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), egy Azure Certified for IoT-eszköz.
>
> Ha fizikai eszközt használ, ugorjon a jelen cikk [fizikai eszközről származó kiépítési információinak lekérése](#retrieve-provisioning-information-from-a-physical-device) szakaszára.

## <a name="prerequisites"></a>Előfeltételek

* A Windows fejlesztői gépen való [engedélyezve van a Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Ebben a cikkben egy Ubuntu rendszerű virtuális Gépen futó Windows 10-es.
* Az aktív IoT hubra.
* Ha szimulált TPM-t, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015-es vagy újabb verziót használ az [" C++asztali fejlesztés](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) a munkaterheléssel" beállítással.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Hozzon létre egy Linux rendszerű virtuális gép virtuális TPM-mel

Ebben a szakaszban egy új linuxos virtuális gépet hoz létre a Hyper-V-n. Ezt a virtuális gépet egy szimulált TPM-sel konfigurálta, így azt tesztelheti, hogy az automatikus kiépítés hogyan működik a IoT Edgeokkal. 

### <a name="create-a-virtual-switch"></a>Virtuális kapcsoló létrehozása

Virtuális kapcsoló lehetővé teszi, hogy a virtuális gép egy fizikai hálózathoz való csatlakozáshoz.

1. Nyissa meg a Hyper-V kezelőjét a Windows rendszerű gépen. 

2. Az a **műveletek** menüjében válassza **Virtuáliskapcsoló-kezelő**. 

3. Válasszon egy **külső** virtuális kapcsoló, majd válassza a **virtuális kapcsoló létrehozása**. 

4. Adja meg az új virtuális kapcsoló nevét, például **EdgeSwitch**. Győződjön meg arról, hogy a kapcsolat típusa értéke **külső hálózati**, majd **Ok**.

5. Egy előugró ablak figyelmeztetést jelenít meg, hogy a hálózati kapcsolat előfordulhat, hogy tartománnyá. Válassza ki **Igen** folytatásához. 

Ha hibába ütközik, az új virtuális kapcsoló létrehozása során, győződjön meg arról, hogy nincs más kapcsolók sem használja az ethernet-adapter és, más kapcsolók nélkül használja ugyanazt a nevet. 

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Töltse le a virtuális gép használja, és mentse helyileg a lemezképfájllal. Ha például [Ubuntu server](https://www.ubuntu.com/download/server). 

2. A Hyper-V kezelőjében ismét válassza a **műveletek** menü **új** > **virtuális gép** elemét.

3. Végezze el a **új virtuális gép varázsló** az alábbi konfigurációkkal:

   1. **Adja meg a generáció**: válasszon **2. generációs**. A 2. generációs virtuális gépeken engedélyezve van a beágyazott virtualizálás, amely a IoT Edge virtuális gépen való futtatásához szükséges.
   2. **A hálózatkezelés konfigurálását**: állítsa **kapcsolat** a virtuális kapcsolóhoz, amely az előző szakaszban létrehozott. 
   3. **Telepítési beállítások**: válasszon **operációs rendszer telepítése rendszerindító lemezkép-fájlból** és keresse meg a helyileg mentett lemezképfájllal.

4. A virtuális gép létrehozásához válassza a **Befejezés** lehetőséget a varázslóban.

Az új virtuális gép létrehozása néhány percig is eltarthat. 

### <a name="enable-virtual-tpm"></a>Virtuális TPM engedélyezése

A virtuális gép létrehozása után nyissa meg a beállításait a Virtual platformmegbízhatósági modul (TPM) engedélyezéséhez, amely lehetővé teszi az eszköz automatikus kiépítését.

1. Válassza ki a virtuális gépet, majd nyissa meg a **beállításait**.

2. Navigáljon a **biztonsági**. 

3. Törölje a jelet **biztonságos rendszerindítás engedélyezése**.

4. Ellenőrizze **engedélyezése platformmegbízhatósági modul**. 

5. Kattintson az **OK** gombra.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Indítsa el a virtuális gépet, és a TPM-adatok gyűjtése

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

1. Másolja a **regisztrációs azonosító** és a **jóváhagyó kulcs**értékeit. Ezek az értékek használatával hozzon létre egyéni regisztrációt a DPS Szolgáltatásban az eszközhöz.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Az IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányát az IoT Hub Device Provisioning Service az Azure-ban, és az IoT hub-ra hivatkozik. Az utasítások a [állítsa be az IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

Miután a Device Provisioning Service futó, másolja az értékét **azonosító hatóköre** az Áttekintés lapon. Amikor az IoT Edge-futtatókörnyezet konfigurálja ezt az értéket használja. 

## <a name="create-a-dps-enrollment"></a>A DPS regisztrációjának létrehozásához

A kiépítési adatokat beolvasni a virtuális gép számára, és annak létre egyéni regisztrációt a Device Provisioning Service használatával. 

Amikor létrehoz egy regisztrációs a DPS Szolgáltatásban, lehetősége van deklarálnia egy **Ikereszköz kezdeti állapota**. Az ikereszközön, állíthat be címkéket csoportosítani az eszközöket a megoldás, mint például a régiót, környezetet, hely vagy eszköz típusa kell tetszőleges metrika szerint. Ezekkel a címkékkel hozhatók létre [automatikus központi telepítések](how-to-deploy-monitor.md). 

1. A [Azure Portal](https://portal.azure.com)navigáljon a IoT hub Device Provisioning Service-példányához. 

2. A **beállítások**válassza **beléptetések kezelése**. 

3. Válassza ki **egyéni regisztráció hozzáadása** végezze el a regisztrációt konfigurálhatja az alábbi lépéseket:  

   1. A **mechanizmus**válassza **TPM**. 

   2. Adja meg a virtuális gépről másolt **hátirat-kulcsot** és **regisztrációs azonosítót** .

      > [!TIP]
      > Ha fizikai TPM-eszközt használ, meg kell határoznia a **jóváhagyás kulcsát**, amely minden TPM-lapka esetében egyedi, és a hozzá társított TPM-lapka gyártójától származik. Létrehozhat egy egyedi **regisztrációs azonosítót** a TPM-eszközhöz, például a HÁTIRAT kulcs SHA-256 kivonatának létrehozásával.

   3. A **true (igaz** ) érték kiválasztásával deklarálhatja, hogy a virtuális gép IoT Edge eszköz. 

   4. Válassza ki a csatolt **az IoT Hub** , hogy szeretné-e az eszközt csatlakoztatni. Több hub is kiválasztható, és az eszköz a kiválasztott foglalási szabályzatnak megfelelően lesz hozzárendelve az egyikhez. 

   5. Ha szeretné, adja meg az azonosító az eszközt. Az eszközazonosítókat, amelyekre egy adott eszköz számára üzembe helyezett házirendmodul használhatja. Ha nem adja meg az eszköz AZONOSÍTÓját, a rendszer a regisztrációs azonosítót használja.

   6. Címke Érték hozzáadása a **Ikereszköz kezdeti állapota** Ha szeretné. Használhatja a címkéket, hogy az eszközök célcsoportok az adatraktármodul központi telepítése. Például: 

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

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-modul telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Annak összetevői tárolókban futtassa, és lehetővé teszi, hogy a kódot futtathatja a peremhálózaton további tárolókat üzembe az eszközön. Az IoT Edge-futtatókörnyezet telepítse a virtuális gépen. 

A DPS tudja **azonosító hatóköre** és az eszköz **regisztrációs azonosító** a cikk, amely az eszköz típusának megfelelő megkezdése előtt. Ha a például Ubuntu-kiszolgálót telepítette, használja a **x64** utasításokat. Ellenőrizze, hogy az IoT Edge-futtatókörnyezet, az automatikus, nem manuális üzembe helyezést. 

[A Azure IoT Edge Runtime telepítése Linux rendszeren](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge-hozzáférést biztosít a TPM-eszköz

Ahhoz, hogy automatikusan építheti ki az eszközt az IoT Edge-futtatókörnyezet, az azt kell tudnia érnie a TPM-be. 

TPM hozzáférést biztosíthat, az IoT Edge-futtatókörnyezet letiltásával a systemd beállításait, hogy a **iotedge** szolgáltatás legfelső szintű jogosultságokkal rendelkezik. Jogosultságainak a szolgáltatás nem szeretné, ha TPM-hozzáférést biztosítania a manuálisan is használhatja az alábbi lépéseket. 

1. A fájl elérési útját a TPM-eszköz hardveres modulra keresése az eszközön, és mentse egy helyi változóhoz. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Hozzon létre egy új szabályt, amely az IoT Edge futásidejű hozzáférést biztosít tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Nyissa meg a szabályok fájlt. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Másolja az alábbi hozzáférési szabályok fájlba. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Mentse, és lépjen ki a fájlt. 

6. Az új szabály kiértékelheti, hogy az udev rendszer aktiválása. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Győződjön meg arról, hogy a szabály sikeresen alkalmazva lett.

   ```bash
   ls -l /dev/tpm0
   ```

   A sikeres kimenet az alábbihoz hasonló:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Ha nem látja, hogy megtörtént-e megfelelő engedélyekkel, próbálja meg a gép frissítése udev újraindítása. 

## <a name="restart-the-iot-edge-runtime"></a>Indítsa újra az IoT Edge-futtatókörnyezet

Indítsa újra az IoT Edge-futtatókörnyezet, úgy, hogy az eszközön végrehajtott összes konfigurációs módosítást buildkonfigurációtól. 

   ```bash
   sudo systemctl restart iotedge
   ```

Ellenőrizze, hogy fut-e az IoT Edge-futtatókörnyezet. 

   ```bash
   sudo systemctl status iotedge
   ```

Ha látja, hibák, lehet, hogy a konfigurációs módosítások még nem még életbe lépjenek. Próbálja meg újra újraindítani az IoT Edge-démon. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Másik lehetőségként újraindítja a virtuális gépet, tekintse meg, ha a módosítások életbe a friss start. 

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ha a modul sikeresen elindult, megkezdheti az IoT Hub, és tekintse meg, hogy az új eszköz automatikusan lett-e kiépítve. Most már az eszköz IoT Edge-modulok futtatása készen áll. 

Az IoT Edge-démon állapotának ellenőrzéséhez.

```cmd/sh
systemctl status iotedge
```

Vizsgálja meg a démon naplóit.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Futó modulok listája.

```cmd/sh
iotedge list
```

Ellenőrizheti, hogy a rendszer használta-e a Device kiépítési szolgáltatásban létrehozott egyéni regisztrációt. Navigáljon az eszköz kiépítési szolgáltatási példányához a Azure Portal. Nyissa meg a regisztráció részleteit a létrehozott egyéni regisztrációhoz. Figyelje meg, hogy a regisztráció állapota **hozzá van rendelve** , és az eszköz azonosítója megjelenik. 

## <a name="next-steps"></a>Következő lépések

A Device Provisioning Service-regisztrációs folyamat állítsa be az Eszközazonosítót és a device twin címkék egyszerre, az új eszköz kiépítése teszi lehetővé. Használhatja ezeket az értékeket az egyes eszközök, illetve eszközfelügyeleti automatikus eszközök csoportjait célozza. Ismerje meg, hogyan [üzembe helyezése és figyelése az IoT Edge-modulok méretezése az Azure portal használatával](how-to-deploy-monitor.md) vagy [Azure CLI-vel](how-to-deploy-monitor-cli.md).

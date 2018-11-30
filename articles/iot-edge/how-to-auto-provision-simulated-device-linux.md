---
title: Automatikus üzembe Azure IoT Edge-eszköz-DPS – Linux |} A Microsoft Docs
description: Linux rendszerű virtuális gép egy szimulált TPM-eszköz segítségével tesztelheti az eszköz kiépítése Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 61da3b8e139cf5091aec4c1ab835c23fe319ea46
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446242"
---
# <a name="create-and-provision-an-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>A Linux rendszerű virtuális gép virtuális TPM-mel az Edge-eszköz létrehozása és kiépítése

Az Azure IoT Edge-eszközökön lehet autoprovisioned használatával a [Device Provisioning Service](../iot-dps/index.yml) ugyanúgy, mint az eszközök, amelyek az edge-kompatibilis nem. Ha még nem ismeri a autoprovisioning folyamatán, tekintse át a [autoprovisioning fogalmak](../iot-dps/concepts-auto-provisioning.md) a folytatás előtt. 

Ez a cikk bemutatja, hogyan teszteléséhez autoprovisioning szimulált peremhálózati eszközön az alábbi lépéseket követve: 

* Hozzon létre egy Linux rendszerű virtuális gép (VM) a Hyper-V és a egy szimulált platformmegbízhatósági modul (TPM) a hardveres biztonsági.
* Hozzon létre egy példányt, IoT Hub Device Provisioning szolgáltatás (DPS).
* Hozzon létre egyéni regisztrációt az eszköz
* Telepítse az IoT Edge-futtatókörnyezet és az eszköz csatlakoztatása az IoT hubhoz

A jelen cikkben ismertetett lépések tesztelési célokra szolgálnak.

## <a name="prerequisites"></a>Előfeltételek

* A Windows fejlesztői gépen való [engedélyezve van a Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Ebben a cikkben egy Ubuntu rendszerű virtuális Gépen futó Windows 10-es. 
* Az aktív IoT hubra. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Hozzon létre egy Linux rendszerű virtuális gép virtuális TPM-mel

Ebben a szakaszban, hogy a tesztelési autoprovisioning működése az IoT Edge szolgáltatással is használhatja azt egy szimulált TPM-eszköz rendelkező Hyper-v hoz létre egy új Linux rendszerű virtuális gépet. 

### <a name="create-a-virtual-switch"></a>Virtuális kapcsoló létrehozása

Virtuális kapcsoló lehetővé teszi, hogy a virtuális gép egy fizikai hálózathoz való csatlakozáshoz.

1. Nyissa meg a Hyper-V a Windows rendszerű gépén. 

2. Az a **műveletek** menüjében válassza **Virtuáliskapcsoló-kezelő**. 

3. Válasszon egy **külső** virtuális kapcsoló, majd válassza a **virtuális kapcsoló létrehozása**. 

4. Adja meg az új virtuális kapcsoló nevét, például **EdgeSwitch**. Győződjön meg arról, hogy a kapcsolat típusa értéke **külső hálózati**, majd **Ok**.

5. Egy előugró ablak figyelmeztetést jelenít meg, hogy a hálózati kapcsolat előfordulhat, hogy tartománnyá. Válassza ki **Igen** folytatásához. 

Ha hibába ütközik, az új virtuális kapcsoló létrehozása során, győződjön meg arról, hogy nincs más kapcsolók sem használja az ethernet-adapter és, más kapcsolók nélkül használja ugyanazt a nevet. 

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Töltse le a virtuális gép használja, és mentse helyileg a lemezképfájllal. Ha például [Ubuntu server](https://www.ubuntu.com/download/server). 

2. Nyissa meg újra a Hyper-V. Az a **műveletek** menüjében válassza **új** > **virtuális gép**.

3. Végezze el a **új virtuális gép varázsló** az alábbi konfigurációkkal:

   1. **Adja meg a generáció**: válasszon **2. generációs**.
   2. **A hálózatkezelés konfigurálását**: állítsa **kapcsolat** a virtuális kapcsolóhoz, amely az előző szakaszban létrehozott. 
   3. **Telepítési beállítások**: válasszon **operációs rendszer telepítése rendszerindító lemezkép-fájlból** és keresse meg a helyileg mentett lemezképfájllal.

Az új virtuális gép létrehozása néhány percig is eltarthat. 

### <a name="enable-virtual-tpm"></a>Virtuális TPM engedélyezése

1. A virtuális gép létrehozása után nyissa meg a beállításokat. 
2. Navigáljon a **biztonsági**. 
3. Törölje a jelet **biztonságos rendszerindítás engedélyezése**.
4. Ellenőrizze **engedélyezése platformmegbízhatósági modul**. 
5. Kattintson az **OK** gombra.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Indítsa el a virtuális gépet, és a TPM-adatok gyűjtése

A virtuális gép létrehozása a C SDK eszköz, amellyel az eszköz beolvasása **regisztrációs azonosító** és **ellenőrzőkulcsot**. 

1. Indítsa el a virtuális Gépet, és csatlakozhat hozzá a telepítési folyamat befejezéséhez. 

2. A virtuális gépen, kövesse a [Linux fejlesztési környezet beállítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) telepítéséhez, és hozhat létre az Azure IoT eszközoldali SDK-t c-hez 

3. Futtassa az alábbi parancsokat hozhat létre egy C SDK-eszköz, amely az eszköz kiépítési adatait kérdezi le. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

3. Másolja le az értékeket a **regisztrációs azonosító** és **ellenőrzőkulcsot**. Ezek az értékek használatával hozzon létre egyéni regisztrációt a DPS Szolgáltatásban az eszközhöz. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Az IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányát az IoT Hub Device Provisioning Service az Azure-ban, és az IoT hub-ra hivatkozik. Az utasítások a [állítsa be az IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

Miután a Device Provisioning Service futó, másolja az értékét **azonosító hatóköre** az Áttekintés lapon. Amikor az IoT Edge-futtatókörnyezet konfigurálja ezt az értéket használja. 

## <a name="create-a-dps-enrollment"></a>A DPS regisztrációjának létrehozásához

A kiépítési adatokat beolvasni a virtuális gép számára, és annak létre egyéni regisztrációt a Device Provisioning Service használatával. 

Amikor létrehoz egy regisztrációs a DPS Szolgáltatásban, lehetősége van deklarálnia egy **Ikereszköz kezdeti állapota**. Az ikereszközön, állíthat be címkéket csoportosítani az eszközöket a megoldás, mint például a régiót, környezetet, hely vagy eszköz típusa kell tetszőleges metrika szerint. Ezekkel a címkékkel hozhatók létre [automatikus központi telepítések](how-to-deploy-monitor.md). 


1. Az a [az Azure portal](https://portal.azure.com), és keresse meg az IoT Hub Device Provisioning Service-példányhoz. 

2. A **beállítások**válassza **beléptetések kezelése**. 

3. Válassza ki **egyéni regisztráció hozzáadása** végezze el a regisztrációt konfigurálhatja az alábbi lépéseket:  

   1. A **mechanizmus**válassza **TPM**. 
   2. Helyezze be a **ellenőrzőkulcsot** és **regisztrációs azonosító** másolt virtuális gépről.
   3. Válassza ki **engedélyezése** bejelenteni, hogy a virtuális gép-e az IoT Edge-eszköz. 
   4. Válassza ki a csatolt **az IoT Hub** , hogy szeretné-e az eszközt csatlakoztatni. 
   5. Ha szeretné, adja meg az azonosító az eszközt. Az eszközazonosítókat, amelyekre egy adott eszköz számára üzembe helyezett házirendmodul használhatja. 
   6. Címke Érték hozzáadása a **Ikereszköz kezdeti állapota** Ha szeretné. Használhatja a címkéket, hogy az eszközök célcsoportok az adatraktármodul központi telepítése. 
   7. Kattintson a **Mentés** gombra. 


## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-modul telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Annak összetevői tárolókban futtassa, és lehetővé teszi, hogy a kódot futtathatja a peremhálózaton további tárolókat üzembe az eszközön. Az IoT Edge-futtatókörnyezet telepítse a virtuális gépen. 

A DPS tudja **azonosító hatóköre** és az eszköz **regisztrációs azonosító** a cikk, amely az eszköz típusának megfelelő megkezdése előtt. Ha a például Ubuntu-kiszolgálót telepítette, használja a **x64** utasításokat. Ellenőrizze, hogy az IoT Edge-futtatókörnyezet, az automatikus, nem manuális üzembe helyezést. 

* [Linux (x64)](how-to-install-iot-edge-linux.md)
* [Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge-hozzáférést biztosít a TPM-eszköz

Ahhoz, hogy automatikusan építheti ki az eszközt az IoT Edge-futtatókörnyezet, az azt kell tudnia érnie a TPM-be. 

TPM hozzáférést biztosíthat, az IoT Edge-futtatókörnyezet letiltásával a systemd beállításait, hogy a *iotedge* szolgáltatás legfelső szintű jogosultságokkal rendelkezik. Jogosultságainak a szolgáltatás nem szeretné, ha TPM-hozzáférést biztosítania a manuálisan is használhatja az alábbi lépéseket. 

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

8. Nyissa meg az IoT Edge-futtatókörnyezet felülbírálja a fájlt. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Adja hozzá a következő kódot egy TPM környezeti változó létrehozásához.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

9. Győződjön meg arról, hogy sikeres volt-e a felülbírálást.

   ```bash
   sudo systemctl cat iotedge.service
   ```

   Sikeres kimenet megjeleníti a **iotedge** alapértelmezett szolgáltatás változókat, és állítsa be majd megjeleníti a környezeti változó **override.conf**. 

12. Töltse be újra a beállításokat.

   ```bash
   sudo systemctl daemon-reload
   ```

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


## <a name="next-steps"></a>További lépések

A Device Provisioning Service-regisztrációs folyamat állítsa be az Eszközazonosítót és a device twin címkék egyszerre, az új eszköz kiépítése teszi lehetővé. Használhatja ezeket az értékeket az egyes eszközök, illetve eszközfelügyeleti automatikus eszközök csoportjait célozza. Ismerje meg, hogyan [üzembe helyezése és figyelése az IoT Edge-modulok méretezése az Azure portal használatával](how-to-deploy-monitor.md) vagy [Azure CLI-vel](how-to-deploy-monitor-cli.md).

---
title: Automatikus üzembe Azure IoT Edge-eszköz-DPS – Windows |} A Microsoft Docs
description: Szimulált eszköz használata a Windows-gépen teszteléséhez automatikus eszközkiépítés az Azure IoT Edge Device Provisioning Service szolgáltatással
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e558f44f9271009b92fbf4ece9aa706801e4176c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576202"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>A Windows egy szimulált TPM Edge-eszköz létrehozása és kiépítése

Az Azure IoT Edge-eszközök automatikus – helyezhetők a [Device Provisioning Service](../iot-dps/index.yml) ugyanúgy, mint az eszközök, amelyek az edge-kompatibilis nem. Ha még nem ismeri az Automatikus kiépítés folyamatát, tekintse át a [automatikus kiépítés alapfogalmait](../iot-dps/concepts-auto-provisioning.md) a folytatás előtt. 

Ez a cikk bemutatja, hogyan tesztelheti automatikus kiépítés szimulált peremhálózati eszközön az alábbi lépéseket követve: 

* Hozzon létre egy példányt, IoT Hub Device Provisioning szolgáltatás (DPS).
* A gépen Windows-egy szimulált platformmegbízhatósági modul (TPM) a hardveres biztonsági egy szimulált eszköz létrehozása.
* Hozzon létre egyéni regisztrációt az eszközön.
* Telepítse az IoT Edge-futtatókörnyezet és az eszköz csatlakoztatása az IoT hubhoz.

## <a name="prerequisites"></a>Előfeltételek

* A Windows fejlesztői gépen. Ez a cikk a Windows 10-es használja. 
* Az aktív IoT hubra. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Az IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányát az IoT Hub Device Provisioning Service az Azure-ban, és az IoT hub-ra hivatkozik. Az utasítások a [állítsa be az IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

Miután a Device Provisioning Service futó, másolja az értékét **azonosító hatóköre** az Áttekintés lapon. Amikor az IoT Edge-futtatókörnyezet konfigurálja ezt az értéket használja. 

## <a name="simulate-a-tpm-device"></a>TPM-eszköz szimulálása

Szimulált TPM-eszköz létrehozása a Windows fejlesztői gépen. Lekérése a **regisztrációs azonosító** és **ellenőrzőkulcsot** az eszközhöz, és ezek segítségével egyéni regisztrációs bejegyzés létrehozása a DPS Szolgáltatásban. 

Amikor létrehoz egy regisztrációs a DPS Szolgáltatásban, lehetősége van deklarálnia egy **Ikereszköz kezdeti állapota**. Az ikereszköz címkék beállíthat csoportosítani az eszközöket a megoldás, mint például a régiót, környezetet, hely vagy eszköz típusa kell tetszőleges metrika szerint. Ezekkel a címkékkel hozhatók létre [automatikus központi telepítések](how-to-deploy-monitor.md). 

Válassza ki az SDK nyelvet, amelyet a szimulált eszköz létrehozásához használni szeretne, és kövesse a lépéseket, amíg nem hoz létre az egyéni regisztráció. 

Az egyéni regisztráció létrehozásakor válassza **engedélyezése** deklarálja, hogy van-e a virtuális gép, egy **IoT Edge-eszköz**.

Szimulált eszköz és egyéni regisztrációs útmutatók: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Miután létrehozta az egyéni regisztráció, mentse az értékét a **regisztrációs azonosító**. Amikor az IoT Edge-futtatókörnyezet konfigurálja ezt az értéket használja. 

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-modul telepítése

Ha befejezte az előző szakaszban, az új eszközt az IoT Hub IoT Edge-eszköz állapottal jelenik meg. Most telepítenie kell az IoT Edge-futtatókörnyezet az eszközön. 

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Annak összetevői tárolókban futtassa, és lehetővé teszi, hogy a kódot futtathatja a peremhálózaton további tárolókat üzembe az eszközön. A Windows rendszerű eszközökhöz választhat, vagy a tárolókat Windows vagy Linux-tárolók használata. Válassza ki a használni kívánt tárolók típusát, és kövesse a lépéseket. Ellenőrizze, hogy az IoT Edge-futtatókörnyezet, az automatikus, nem manuális üzembe helyezést. 

Kövesse az utasításokat követve telepítse az IoT Edge-futtatókörnyezet, amely az előző szakaszban a szimulált TPM-eszköz fut az eszközön. 

A DPS tudja **azonosító hatóköre** és az eszköz **regisztrációs azonosító** ezekben a cikkekben megkezdése előtt. 

* [Windows-tárolók](how-to-install-iot-edge-windows-with-windows.md)
* [Linux-tárolók](how-to-install-iot-edge-windows-with-linux.md)

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ha a modul sikeresen elindult, az IoT Hub lép, és elkezdeni az eszköz IoT Edge-modulok telepítését. Az eszközön az alábbi parancsok használatával ellenőrizze, hogy a futtatókörnyezet-sikeresen elindult.  

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```powershell
Get-Service iotedge
```

Vizsgálja meg a szolgáltatási naplók az elmúlt 5 percben.

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Futó modulok listája.

```powershell
iotedge list
```

## <a name="next-steps"></a>További lépések

A Device Provisioning Service-regisztrációs folyamat állítsa be az Eszközazonosítót és a device twin címkék egyszerre, az új eszköz kiépítése teszi lehetővé. Használhatja ezeket az értékeket az egyes eszközök, illetve eszközfelügyeleti automatikus eszközök csoportjait célozza. Ismerje meg, hogyan [üzembe helyezése és figyelése az IoT Edge-modulok méretezése az Azure portal használatával](how-to-deploy-monitor.md) vagy [Azure CLI használatával](how-to-deploy-monitor-cli.md)

---
title: Automatikus üzembe Windows-eszközök Azure IoT Edge - a DPS |} A Microsoft Docs
description: Szimulált eszköz használata a Windows-gépen teszteléséhez automatikus eszközkiépítés az Azure IoT Edge Device Provisioning Service szolgáltatással
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0236491f9ebc8e3ecf7df8b74db4fd5ff441c7f8
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677440"
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

Az egyéni regisztráció létrehozásakor a True ( **igaz** ) érték kiválasztásával deklarálhatja, hogy a SZIMULÁLt TPM-eszköz a Windows fejlesztői gépen **IoT Edge eszköz**.

Szimulált eszköz és egyéni regisztrációs útmutatók:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Miután létrehozta az egyéni regisztráció, mentse az értékét a **regisztrációs azonosító**. Amikor az IoT Edge-futtatókörnyezet konfigurálja ezt az értéket használja.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-modul telepítése

Ha befejezte az előző szakaszban, az új eszközt az IoT Hub IoT Edge-eszköz állapottal jelenik meg. Most telepítenie kell az IoT Edge-futtatókörnyezet az eszközön.

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Annak összetevői tárolókban futtassa, és lehetővé teszi, hogy a kódot futtathatja a peremhálózaton további tárolókat üzembe az eszközön.  

Kövesse az utasításokat követve telepítse az IoT Edge-futtatókörnyezet, amely az előző szakaszban a szimulált TPM-eszköz fut az eszközön. Ellenőrizze, hogy az IoT Edge-futtatókörnyezet, az automatikus, nem manuális üzembe helyezést.

Az eszközön a IoT Edge telepítése előtt Ismerje meg a DPS- **azonosító hatókörét** és az eszköz **regisztrációs azonosítóját** .

[IoT Edge telepítése és automatikus kiépítése](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ha a modul sikeresen elindult, az IoT Hub lép, és elkezdeni az eszköz IoT Edge-modulok telepítését. Az eszközön az alábbi parancsok használatával ellenőrizze, hogy a futtatókörnyezet-sikeresen elindult.  

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```powershell
Get-Service iotedge
```

Vizsgálja meg a szolgáltatási naplók az elmúlt 5 percben.


```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futó modulok listája.

```powershell
iotedge list
```

## <a name="next-steps"></a>További lépések

A Device Provisioning Service-regisztrációs folyamat állítsa be az Eszközazonosítót és a device twin címkék egyszerre, az új eszköz kiépítése teszi lehetővé. Használhatja ezeket az értékeket az egyes eszközök, illetve eszközfelügyeleti automatikus eszközök csoportjait célozza. Ismerje meg, hogyan [üzembe helyezése és figyelése az IoT Edge-modulok méretezése az Azure portal használatával](how-to-deploy-monitor.md) vagy [Azure CLI használatával](how-to-deploy-monitor-cli.md)

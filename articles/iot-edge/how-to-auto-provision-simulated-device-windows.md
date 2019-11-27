---
title: Windows-eszközök automatikus kiépítése a DPS-Azure IoT Edgeokkal | Microsoft Docs
description: Szimulált eszköz használata a Windows-gépen teszteléséhez automatikus eszközkiépítés az Azure IoT Edge Device Provisioning Service szolgáltatással
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad92d4cf0d5b61c778b87114d4be6c23557f8e26
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457138"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Szimulált IoT Edge eszköz létrehozása és kiépítése virtuális TPM-sel Windows rendszeren

Azure IoT Edge eszközök automatikusan kiállíthatók az [eszköz kiépítési szolgáltatásával](../iot-dps/index.yml) , ugyanúgy, mint az Edge-t nem használó eszközök. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át az [automatikus kiépítési fogalmakat](../iot-dps/concepts-auto-provisioning.md) .

A DPS támogatja a szimmetrikus kulcsos igazolást IoT Edge eszközökhöz az egyéni regisztráció és a csoportos regisztráció során. Csoportos regisztráció esetén, ha a "IoT Edge eszköz" beállítást szeretné megadni a szimmetrikus kulcsos igazolásban, akkor a beléptetési csoportban regisztrált összes eszköz IoT Edge eszközként lesz megjelölve. 

Ez a cikk bemutatja, hogyan tesztelheti az automatikus kiépítést egy szimulált IoT Edge eszközön a következő lépésekkel:

* Hozzon létre egy példányt, IoT Hub Device Provisioning szolgáltatás (DPS).
* A gépen Windows-egy szimulált platformmegbízhatósági modul (TPM) a hardveres biztonsági egy szimulált eszköz létrehozása.
* Hozzon létre egyéni regisztrációt az eszközön.
* Telepítse az IoT Edge-futtatókörnyezet és az eszköz csatlakoztatása az IoT hubhoz.

> [!NOTE]
> A TPM 2,0-es kiadására akkor van szükség, ha a TPM-igazolást a DPS használatával használja, és csak egyéni, nem csoportos, regisztrációkat hozhat létre.

> [!TIP]
> Ez a cikk azt ismerteti, hogyan lehet az automatikus kiépítés tesztelését a TPM-igazolás használatával virtuális eszközökön, de a fizikai TPM-hardverek használata esetén ez nagy részét is alkalmazza.

## <a name="prerequisites"></a>Előfeltételek

* A Windows fejlesztői gépen. Ez a cikk a Windows 10-es használja.
* Az aktív IoT hubra.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Az IoT Hub Device Provisioning Service beállítása

Hozzon létre egy új példányát az IoT Hub Device Provisioning Service az Azure-ban, és az IoT hub-ra hivatkozik. A [IOT hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című témakör útmutatását követve végezheti el.

Miután futtatta az eszköz kiépítési szolgáltatását, másolja az **azonosító hatókör** értékét az Áttekintés lapról. Amikor az IoT Edge-futtatókörnyezet konfigurálja ezt az értéket használja.

> [!TIP]
> Ha fizikai TPM-eszközt használ, meg kell határoznia a **jóváhagyás kulcsát**, amely minden TPM-lapka esetében egyedi, és a hozzá társított TPM-lapka gyártójától származik. Létrehozhat egy egyedi **regisztrációs azonosítót** a TPM-eszközhöz, például a HÁTIRAT kulcs SHA-256 kivonatának létrehozásával.
>
> Kövesse a cikk utasításait az [eszközök regisztrálásának az Azure Portalon való kezeléséhez](../iot-dps/how-to-manage-enrollments.md) a DPS-ben való regisztráció létrehozásához, majd folytassa a jelen cikk [IoT Edge futtatókörnyezet telepítése](#install-the-iot-edge-runtime) című szakaszával a folytatáshoz.

## <a name="simulate-a-tpm-device"></a>TPM-eszköz szimulálása

Szimulált TPM-eszköz létrehozása a Windows fejlesztői gépen. Kérje le az eszköz **regisztrációs azonosítóját** és **záradékának kulcsát** , és hozzon létre egy egyéni beléptetési bejegyzést a DPS-ben.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van bejelenteni a **kezdeti eszköz kettős állapotát**. Az ikereszköz címkék beállíthat csoportosítani az eszközöket a megoldás, mint például a régiót, környezetet, hely vagy eszköz típusa kell tetszőleges metrika szerint. Ezek a címkék [automatikus központi telepítések](how-to-deploy-monitor.md)létrehozásához használhatók.

Válassza ki az SDK nyelvet, amelyet a szimulált eszköz létrehozásához használni szeretne, és kövesse a lépéseket, amíg nem hoz létre az egyéni regisztráció.

Az egyéni regisztráció létrehozásakor a True ( **igaz** ) érték kiválasztásával deklarálhatja, hogy a SZIMULÁLt TPM-eszköz a Windows fejlesztői gépen **IoT Edge eszköz**.

Szimulált eszköz és egyéni regisztrációs útmutatók:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Az egyéni regisztráció létrehozása után mentse a **regisztrációs azonosító**értékét. Amikor az IoT Edge-futtatókörnyezet konfigurálja ezt az értéket használja.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge-modul telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Annak összetevői tárolókban futtassa, és lehetővé teszi, hogy a kódot futtathatja a peremhálózaton további tárolókat üzembe az eszközön.

Az eszköz kiépítés során a következő információkra lesz szüksége:

* A DPS- **azonosító hatókörének** értéke
* A létrehozott eszköz **regisztrációs azonosítója**

Telepítse a IoT Edge futtatókörnyezetet a szimulált TPM-t futtató eszközre. A IoT Edge futtatókörnyezetet automatikus, nem manuális, kiépítés esetén kell konfigurálnia.

> [!TIP]
> Tartsa meg az ablak, amely a TPM-szimulátor fut, nyissa meg a telepítés során, és a tesztelés.

A IoT Edge Windows rendszeren való telepítésével kapcsolatos további információkért, beleértve a tárolók kezeléséhez és a IoT Edge frissítéséhez szükséges feladatok előfeltételeit és utasításait lásd: [a Azure IoT Edge futtatókörnyezet telepítése Windows](how-to-install-iot-edge-windows.md)rendszeren.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. Ügyeljen arra, hogy az IoT Edge telepítésekor a PowerShell AMD64-munkamenetét használja, nem a PowerShell (x86) rendszerre.

1. Az **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows rendszerű számítógép támogatott verziójú-e, bekapcsolja a tárolók szolgáltatást, majd letölti a Moby Runtime és a IoT Edge futtatókörnyezetet. A parancs alapértelmezés szerint Windows-tárolókat használ.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Ezen a ponton a IoT Core-eszközök automatikusan újraindulnak. Előfordulhat, hogy a Windows 10 vagy Windows Server rendszerű eszközök újraindítását kérik. Ha igen, indítsa újra az eszközt. Ha az eszköz elkészült, futtassa újra a PowerShellt rendszergazdaként.

1. Az **inicializálás-IoTEdge** parancs konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a Windows-tárolók manuális kiépítés. A manuális kiépítés helyett használja a `-Dps` jelzőt a Device kiépítési szolgáltatás használatára.

   Cserélje le `{scope_id}` és `{registration_id}` helyőrző értékeit a korábban összegyűjtött adatokra.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

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

A Device Provisioning Service-regisztrációs folyamat állítsa be az Eszközazonosítót és a device twin címkék egyszerre, az új eszköz kiépítése teszi lehetővé. Használhatja ezeket az értékeket az egyes eszközök, illetve eszközfelügyeleti automatikus eszközök csoportjait célozza. Megtudhatja, hogyan [helyezhet üzembe és figyelheti IoT Edge-modulok méretezését a Azure Portal vagy az](how-to-deploy-monitor.md) [Azure CLI használatával](how-to-deploy-monitor-cli.md)

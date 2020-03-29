---
title: Windows-eszközök automatikus kiépítése a DPS használatával – Azure IoT Edge | Microsoft dokumentumok
description: Szimulált eszköz használata a Windows-gépen az Azure IoT Edge automatikus eszközkiépítésének teszteléséhez az eszközkiépítési szolgáltatással
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee4f01c3ec57b0cf9e3ecf47254b57be95ea051a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510940"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Szimulált IoT Edge-eszköz létrehozása és kiépítése virtuális TPM-mel Windows rendszeren

Az Azure IoT Edge-eszközök is automatikusan kiépíthető az [eszközkiépítési szolgáltatás,](../iot-dps/index.yml) csakúgy, mint az eszközök, amelyek nem él-kompatibilis. Ha nem ismeri az automatikus kiépítés folyamatát, a folytatás előtt tekintse át az [automatikus kiépítés iményét.](../iot-dps/concepts-auto-provisioning.md)

A DPS támogatja az IoT Edge-eszközök szimmetrikus kulcsigazolását mind az egyéni regisztrációban, mind a csoportregisztrációban. A csoport regisztráció, ha bejelöli az "IoT Edge-eszköz" beállítás, hogy igaz legyen a szimmetrikus kulcs tanúsítvány, az adott regisztrációs csoport ban regisztrált összes eszköz lesz jelölve IoT Edge-eszközök.

Ez a cikk bemutatja, hogyan tesztelheti az automatikus kiépítést egy szimulált IoT Edge-eszközön a következő lépésekkel:

* Hozzon létre egy példányt az IoT Hub-eszközlétesítési szolgáltatás (DPS) példánya.
* A hardverbiztonság érdekében szimulált platformmegbízhatósági modullal (TPM) szimulált eszközt hozhat létre windowsos számítógépén.
* Hozzon létre egyéni regisztrációt az eszközhöz.
* Telepítse az IoT Edge futásidejű, és csatlakoztassa az eszközt az IoT Hubhoz.

> [!TIP]
> Ez a cikk az automatikus kiépítés tesztelését ismerteti a TPM-tanúsítvány virtuális eszközökön való használatával, de nagy része a fizikai TPM-hardver ek használata esetén is érvényes.

## <a name="prerequisites"></a>Előfeltételek

* Egy Windows fejlesztőgép. Ez a cikk a Windows 10-et használja.
* Egy aktív IoT Hub.

> [!NOTE]
> A TPM 2.0 szükséges, ha A TPM tanúsítványt dps-szel használ, és csak egyéni, csoport- és regisztrációk létrehozására használható.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Az IoT Hub-eszközkiépítési szolgáltatás beállítása

Hozzon létre egy új példányt az IoT Hub-eszközkiépítési szolgáltatás az Azure-ban, és kapcsolja össze az IoT hub. Az [IoT Hub DPS beállítása](../iot-dps/quick-setup-auto-provision.md)című útmutató utasításait követheti.

Miután futtatta az eszközkiépítési szolgáltatást, másolja az **ID Scope** értékét az áttekintő lapról. Ezt az értéket használja az IoT Edge futásidejű konfigurálásakor.

> [!TIP]
> Ha fizikai TPM-eszközt használ, meg kell **határoznia**az Ellenőrző kulcsot , amely minden Egyes TPM-chipre egyedi, és amelyet a hozzá társított TPM-chipgyártó tól kell beszereznie. A TPM-eszköz egyedi **regisztrációs azonosítóját** származtathatja, például létrehozhatja az ellenőrzőkulcs SHA-256 kivonatát.
>
> Kövesse a cikkben található utasításokat: Hogyan kezelheti az [eszközregisztrációkat](../iot-dps/how-to-manage-enrollments.md) az Azure Portalon a DPS-ben való regisztráció létrehozásához, majd folytassa [az IoT Edge futásidejű szakaszának telepítésével](#install-the-iot-edge-runtime) ebben a cikkben a folytatáshoz.

## <a name="simulate-a-tpm-device"></a>TPM-eszköz szimulálása

Szimulált TPM-eszköz létrehozása a Windows fejlesztői gépen. Olvassa be az eszköz **regisztrációs azonosítóját** és **ellenőrzőkulcsát,** és használja őket egyéni regisztrációs bejegyzés létrehozására a DPS-ben.

Amikor létrehoz egy regisztrációt a DPS-ben, lehetősége van egy **kezdeti ikereszköz-állapot**deklarálni. Az ikereszközben beállíthatja a címkéket az eszközök csoportosításához a megoldásban szükséges bármely mutató szerint, például a régió, a környezet, a hely vagy az eszköz típusa szerint. Ezek a címkék az automatikus központi telepítések létrehozásához [használatosak.](how-to-deploy-monitor.md)

Válassza ki a szimulált eszköz létrehozásához használni kívánt SDK-nyelvet, és kövesse a lépéseket, amíg létre nem hozza az egyes regisztrációt.

Az egyéni regisztráció létrehozásakor válassza az **Igaz** lehetőséget, ha deklarálni szeretné, hogy a Windows fejlesztői gépen lévő szimulált TPM-eszköz **Egy IoT Edge-eszköz.**

Szimulált eszköz- és egyéni beléptetési útmutatók:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C #](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Az egyéni regisztráció létrehozása után mentse a **regisztrációs azonosító**értékét. Ezt az értéket használja az IoT Edge futásidejű konfigurálásakor.

## <a name="install-the-iot-edge-runtime"></a>Az IoT Edge futásidejű telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Összetevői tárolókban futnak, és lehetővé teszik további tárolók üzembe helyezését az eszközre, így a kód a peremhálózaton futtatható.

Az eszköz kiépítésekor a következő adatokra lesz szüksége:

* A **DPS-azonosító hatókörének** értéke
* A létrehozott eszköz **regisztrációs azonosítója**

Telepítse az IoT Edge futtató eszközre a szimulált TPM.Install the IoT Edge runtime on the device that is running the simulated TPM. Az IoT Edge-futásidejű automatikus, nem manuális, kiépítés konfigurálása.

> [!TIP]
> Tartsa nyitva a TPM-szimulátort futtató ablakot a telepítés és a tesztelés során.

Az IoT Edge Windows rendszeren történő telepítéséről, beleértve az olyan feladatok előfeltételeit és utasításait, mint a tárolók kezelése és az IoT Edge frissítése, [az Azure IoT Edge futásidejének telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)című témakörben talál további információt.

1. Nyisson meg egy PowerShell-ablakot rendszergazdai módban. Győződjön meg arról, hogy az IoT Edge telepítésekor a PowerShell AMD64-munkamenetét használja, ne a PowerShellt (x86).

1. A **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows-gép egy támogatott verzió, bekapcsolja a tárolók funkciót, majd letölti a moby futásidejű és az IoT Edge futásidejű. A parancs alapértelmezés szerint Windows-tárolókat használ.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Ezen a ponton az IoT Core-eszközök automatikusan újraindulhatnak. Más Windows 10- vagy Windows Server-eszközök újraindítást kérhetnek. Ha igen, indítsa újra az eszközt most. Ha az eszköz készen áll, futtassa újra a PowerShellt rendszergazdaként.

1. Az **Initialize-IoTEdge** parancs konfigurálja az IoT Edge futásidejét a számítógépen. A parancs alapértelmezés szerint manuálisan létesít és windowsos tárolókat. Használja `-Dps` a jelzőt az eszközkiépítési szolgáltatás kézi kiépítés helyett.

   Cserélje le a `{scope_id}` korábban gyűjtött adatok helyőrző értékeit. `{registration_id}`

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ha a futásidő sikeresen elindult, az IoT Hubba léphet, és elkezdheti az IoT Edge-modulok üzembe helyezését az eszközre. Az alábbi parancsokkal ellenőrizheti, hogy a futásidő sikeresen telepítve van-e, és sikeresen elindult-e.  

Ellenőrizze az IoT Edge-szolgáltatás állapotát.

```powershell
Get-Service iotedge
```

Vizsgálja meg az elmúlt 5 perc szolgáltatásnaplóit.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futó modulok listája.

```powershell
iotedge list
```

## <a name="next-steps"></a>További lépések

Az eszközkiépítési szolgáltatás regisztrációs folyamat lehetővé teszi, hogy az eszközazonosítóés az ikercímkék beállítása az új eszköz kiépítésével egy időben. Ezeket az értékeket használhatja az egyes eszközök vagy eszközcsoportok automatikus eszközkezelés használatával történő célzásához. Ismerje meg, hogyan [telepítheti és figyelheti az IoT Edge-modulokat az Azure Portalon](how-to-deploy-monitor.md) vagy az [Azure CLI használatával.](how-to-deploy-monitor-cli.md)

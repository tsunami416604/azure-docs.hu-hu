---
title: Automatikus-kiépítési Azure IoT peremhálózati eszköz a terjesztési pontok – Windows |} Microsoft Docs
description: A Windows-számítógépen a szimulált eszköz segítségével ellenőrizze, hogy Azure IoT peremhálózati eszköz kiépítése szolgáltatáshoz kiépítés automatikus eszköz
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a973b248022cf3c0497f72bc2fcdd45a6527e65
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116122"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Hozzon létre, és helyezze üzembe a Windows egy szimulált TPM peremhálózati eszköz

Az Azure IoT peremeszközök automatikus-létesíthetők használatával a [eszköz kiépítése szolgáltatáshoz](../iot-dps/index.yml) ugyanúgy, mint az eszközök, amelyek nincsenek edge-kompatibilis. Ha nem ismeri az Automatikus kiépítés folyamatát, tekintse át a [automatikus kiépítés fogalmak](../iot-dps/concepts-auto-provisioning.md) folytatása előtt. 

Ez a cikk bemutatja, hogyan tesztelése automatikus kiépítés egy szimulált peremhálózati eszközön az alábbi lépéseket: 

* Hozzon létre egy példányt az IoT Hub eszköz kiépítése szolgáltatáshoz (terjesztési pontok).
* A számítógépen a Windows, a szimulált platformmegbízhatósági modul (TPM) a hardveres biztonsági a szimulált eszköz létrehozásához.
* Hozzon létre egy egyéni regisztrációt az adott eszköz.
* Az IoT-Edge futásidejű telepítse, és az eszköz csatlakoztatása az IoT-központ.

## <a name="prerequisites"></a>Előfeltételek

* A Windows fejlesztői gépen. Ez a cikk a Windows 10 használja. 
* Az aktív IoT-központ. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Az IoT Hub eszköz kiépítése szolgáltatás beállítása

Az IoT Hub-kiépítés szolgáltatást egy új példányának létrehozása az Azure-ban, és az IoT hub csatolható. Az utasítások a [az IoT Hub terjesztési pontok beállítása](../iot-dps/quick-setup-auto-provision.md).

Értékének másolása után az eszköz kiépítése szolgáltatás fut, **azonosító hatókör** a áttekintése oldalon. Az IoT-Edge futásidejű konfigurálásakor használja ezt az értéket. 

## <a name="simulate-a-tpm-device"></a>TPM-eszköz szimulálása

A szimulált TPM-eszköz létrehozásához a Windows fejlesztői számítógépén. Beolvasni a **regisztrációs Azonosítót** és **ellenőrzőkulccsal** az eszközhöz, és használhatják azokat a terjesztési pontok egyes regisztrációs bejegyzés létrehozása. 

Terjesztési pontok, a beléptetési létrehozásakor lehetősége van deklarálnia egy **eszköz iker Alapállapot**. Az eszköz iker a is megadhat címkék csoportosíthatja az eszközöket a metrika a megoldásban, például a régió, környezet, hely vagy eszköz típusa van szüksége. Ezek a címkék létrehozásához használt [automatikus telepítések](how-to-deploy-monitor.md). 

Válassza ki a szimulált eszköz létrehozásához használni kívánt SDK nyelvet, és kövesse a lépéseket, amíg nem hoz létre az egyes beléptetési. 

Az egyes beléptetési létrehozásakor válassza ki a **engedélyezése** segítségével deklarálja azt, hogy van-e a virtuális gép egy **IoT peremhálózati eszköz**.

Szimulált eszköz és egyéni regisztrálási útmutatók: 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Miután létrehozta az egyes beléptetési, mentse a értékének a **regisztrációs Azonosítót**. Az IoT-Edge futásidejű konfigurálásakor használja ezt az értéket. 

## <a name="install-the-iot-edge-runtime"></a>Az IoT-Edge futásidejű telepítése

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Az összetevők tárolók futnak, és lehetővé teszi, hogy a kódot futtathatja a peremhálózaton további tárolókat telepítheti az eszközön. A Windows rendszert futtató eszközökre választhat, vagy a Windows-tárolók és a Linux-tárolók használatára. Válassza ki a használni kívánt tárolókat, és kövesse a lépéseket. Ügyeljen arra, hogy konfigurálja az IoT-Edge futásidejű nem manuális, automatikus üzembe helyezéséhez. 

Kövesse az utasításokat az IoT-Edge runtime telepítését az eszközre, amelyet a szimulált TPM fut az előző szakaszban. 

A terjesztési pontok tudja **azonosító hatókör** és az eszköz **regisztrációs Azonosítót** ezek a cikkek megkezdése előtt. 

* [Windows-tárolók](how-to-install-iot-edge-windows-with-windows.md)
* [Linux-tárolók](how-to-install-iot-edge-windows-with-linux.md)

## <a name="create-a-tpm-environment-variable"></a>A TPM környezeti változó létrehozása

Azon a számítógépen, amelyen a szimulált eszköz, módosítsa a **iotedge** szolgáltatás beállításjegyzék egy környezeti változó beállítása.

1. Az a **Start** menü megnyitása **regedit**. 
2. Navigáljon a **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\iotedge**. 
3. Válassza ki **szerkesztése** > **új** > **karakterláncsoros értéket**. 
4. Adja meg a nevét **környezet**. 
5. Kattintson duplán az új változó, és állítsa be a **IOTEDGE_USE_TPM_DEVICE = ON**. 
6. Kattintson az **OK** gombra a módosítások mentéséhez. 

## <a name="restart-the-iot-edge-runtime"></a>Indítsa újra az IoT-Edge futásidejű

Indítsa újra az IoT-Edge futásidejű, úgy, hogy azt szerzi be az eszközön végrehajtott összes konfigurációs módosítások. 

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

Ellenőrizze, hogy fut-e az IoT-Edge futásidejű. 

   ```bash
   sudo systemctl status iotedge
   ```

## <a name="verify-successful-installation"></a>Sikeres telepítésének ellenőrzése

A futtatókörnyezet sikeresen elindult, ha azokat az IoT Hub, és hogy az új eszköz automatikusan lett kiépítve, és készen áll a futtatásra IoT peremhálózati modulok. 

Az IoT-Edge szolgáltatás állapotának ellenőrzése.

```powershell
Get-Service iotedge
```

Ellenőrizze a szolgáltatás eseménynaplóit az utolsó 5 perc.

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Futó modul felsorolása.

```powershell
iotedge list
```

## <a name="next-steps"></a>További lépések

Az eszköz kiépítése szolgáltatáshoz a regisztrációs folyamat beállítani az Eszközazonosító és az eszköz iker címkék egyszerre az új eszköz kiépítése lehetővé teszi. Egyes eszközöket, vagy az automatikus eszközkezeléssel eszközcsoportok használhatja ezeket az értékeket. Megtudhatja, hogyan [telepítés és figyelés modulok skálázása az Azure portál használatával IoT peremhálózati](how-to-deploy-monitor.md) vagy [Azure parancssori felület használatával](how-to-deploy-monitor-cli.md)
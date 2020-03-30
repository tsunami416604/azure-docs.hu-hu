---
title: Az Azure IoT Edge for Linux telepítése Windows rendszeren | Microsoft dokumentumok
description: Az Azure IoT Edge telepítési utasításai Linux-tárolókhoz Windows 10, Windows Server és Windows IoT Core rendszeren
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133164"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Linux-tárolók futtatása az IoT Edge segítségével Windows rendszeren

Tesztelje az IoT Edge-modulokat Linux-eszközökhöz egy Windows-gép használatával.

Éles környezetben a Windows-eszközöknek csak Windows-tárolókat kell futtatniuk. Azonban egy gyakori fejlesztési forgatókönyv, hogy egy Windows-számítógép segítségével IoT Edge-modulok Linux-eszközökhöz. Az IoT Edge Windows futásideje lehetővé teszi, hogy Linux-tárolókat **tesztelési és fejlesztési** célokra futtasson.

Ez a cikk az Azure IoT Edge futásidejű Linux-tárolók használatával a Windows x64 (AMD/Intel) rendszer telepítésének lépéseit sorolja fel. Ha többet szeretne megtudni az IoT Edge futásidejű telepítőjéről, beleértve az összes telepítési paraméter részleteit, [olvassa el az Azure IoT Edge futásidő telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)című témakört.

Az IoT Edge legújabb verziójában található információkról az [Azure IoT Edge-kiadások című témakörben](https://github.com/Azure/azure-iotedge/releases)talál.

## <a name="prerequisites"></a>Előfeltételek

Ebben a szakaszban áttekintheti, hogy a Windows-eszköz támogatja-e az IoT Edge-et, és előkészítheti azt egy tárolómotorra a telepítés előtt.

### <a name="supported-windows-versions"></a>Támogatott Windows-verziók

Az Azure IoT Edge Linux-tárolókkal a Windows bármely olyan verzióján futtatható, amely megfelel a [Docker Desktop követelményeinek](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Ha az IoT Edge-et virtuális gépen szeretné telepíteni, engedélyezze a beágyazott virtualizációt, és legalább 2 GB memóriát foglaljon le. A beágyazott virtualizáció engedélyezése a használt hipervizortól függően eltérő. A Hyper-V esetében a 2. A VMWare esetében van egy kapcsoló, amely engedélyezi a funkciót a virtuális gépen.

### <a name="prepare-the-container-engine"></a>Készítse elő a konténermotort

Az Azure IoT Edge egy [OCI-kompatibilis](https://www.opencontainers.org/) tárolómotorra támaszkodik. A Windows és Linux-tárolók Windows-gépen való futtatása közötti legnagyobb konfigurációs különbség az, hogy az IoT Edge-telepítés tartalmaz egy Windows-tároló futtatókörnyezetet, de az IoT Edge telepítése előtt meg kell adnia a saját futtatókörnyezetét a Linux-tárolókszámára.

Ha be szeretne állítani egy Windows-gépet linuxos eszközök tárolóinak fejlesztéséhez és teszteléséhez, használhatja a [Docker Desktop-ot](https://www.docker.com/docker-windows) tárolómotorként. Telepítenie kell a Dockert, és konfigurálnia kell [linuxos tárolók használatára](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) az IoT Edge telepítése előtt.  

Ha az IoT Edge-eszköz egy Windows-számítógép, ellenőrizze, hogy megfelel-e a Hyper-V [rendszerkövetelményeinek.](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)

## <a name="install-iot-edge-on-a-new-device"></a>Az IoT Edge telepítése új eszközre

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagokra a csomagokban (a LICENC címtárban) található licencfeltételek vonatkoznak. Kérjük, olvassa el a licencfeltételeket a csomag használata előtt. A csomag telepítése és használata azt jelenti, hogy ön elfogadja ezeket a feltételeket. Ha nem ért egyet a licencfeltételekkel, ne használja a csomagot.

Egy PowerShell-parancsfájl letölti és telepíti az Azure IoT Edge biztonsági démon. A biztonsági démon ezután elindítja az első két futásidejű modulok, az IoT Edge-ügynök, amely lehetővé teszi a távoli üzembe helyezések más modulok.

Amikor telepíti az IoT Edge futásidejű első alkalommal egy eszközön, ki kell építenie az eszközt egy IoT hub identitással. Egyetlen IoT Edge-eszköz manuálisan kiépíthető az IoT hub által biztosított eszközkapcsolati karakterlánc használatával. Vagy használhatja az eszközkiépítési szolgáltatás automatikusan eszközök kiépítése, ami akkor hasznos, ha sok eszközt kell beállítani.

A különböző telepítési lehetőségekről és paraméterekről az [Azure IoT Edge futásidő telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)című témakörben olvashat bővebben. Miután telepítette és konfigurálta a Docker Desktopot a Linux-tárolókhoz, a fő telepítési különbség a Linux deklarálása a **-ContainerOs** paraméterrel. Példa:

1. Ha még nem tette meg, regisztráljon egy új IoT Edge-eszközt, és olvassa be az eszköz kapcsolati karakterláncát. Másolja a szakasz későbbi részében használni hozandó kapcsolati karakterláncot. Ezt a lépést a következő eszközökkel hajthatja végre:

   * [Azure-portál](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio kód](how-to-register-device.md#register-with-visual-studio-code)

2. Futtassa a PowerShellt rendszergazdaként.

   >[!NOTE]
   >Az IoT Edge telepítéséhez használja a PowerShell AMD64-munkamenetét, ne a PowerShellt (x86). Ha nem biztos abban, hogy melyik munkamenettípust használja, futtassa a következő parancsot:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. A **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows-gép egy támogatott verzió, bekapcsolja a tárolók funkciót, majd letölti a moby runtime (amely nem linuxos tárolók) és az IoT Edge futásidejű. A parancs alapértelmezés szerint a Windows tárolók, így deklarálni Linux, mint a kívánt tároló operációs rendszer.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Ezen a ponton az IoT Core-eszközök automatikusan újraindulhatnak. Más Windows 10- vagy Windows Server-eszközök újraindítást kérhetnek. Ha igen, indítsa újra az eszközt most. Ha az eszköz készen áll, futtassa újra a PowerShellt rendszergazdaként.

5. Az **Initialize-IoTEdge** parancs konfigurálja az IoT Edge futásidejét a számítógépen. A parancs alapértelmezés szerint manuálisan létesít egy eszközkapcsolati karakterlánccal. Deklarálja a Linuxot a kívánt tárolóoperációs rendszerként.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Amikor a rendszer kéri, adja meg az 1. Az eszköz kapcsolati karakterlánc társítja a fizikai eszköz egy eszközazonosítót az IoT Hubban.

   Az eszköz kapcsolati karakterlánca a következő formátumot veszi fel, és nem tartalmazhat idézőjeleket:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

Ellenőrizze az IoT Edge szolgáltatás állapotát:

```powershell
Get-Service iotedge
```

Vizsgálja meg az elmúlt 5 perc szolgáltatásnaplóit:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futtassa a leggyakoribb konfigurációs és hálózati hibák automatikus ellenőrzését:

```powershell
iotedge check
```

Futó modulok listája. Egy új telepítés után az egyetlen modul, amelyet látnia kell, az **edgeAgent**. Miután először telepítette az [IoT Edge-modulokat,](how-to-deploy-modules-portal.md) a másik rendszermodul, az **edgeHub**is elindul az eszközön.

```powershell
iotedge list
```

## <a name="next-steps"></a>További lépések

Most, hogy egy IoT Edge-eszközt létesített a futásidejű telepítve, telepítheti az [IoT Edge-modulokat.](how-to-deploy-modules-portal.md)

Ha problémái vannak az IoT Edge megfelelő telepítésével, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést az IoT Edge legújabb verziójára szeretne frissíteni, olvassa [el az IoT Edge biztonsági démonának és futásidejű](how-to-update-iot-edge.md)verziójának frissítése című témakört.

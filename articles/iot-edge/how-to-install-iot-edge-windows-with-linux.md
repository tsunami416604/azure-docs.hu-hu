---
title: A Linux rendszerhez készült Azure IoT Edge telepítése Windows rendszeren | Microsoft Docs
description: A Linux-tárolók telepítési útmutatója a Windows 10, a Windows Server és a Windows IoT Core rendszeren Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 40e6f850a00a8f77e846d9574caedc345aa94cef
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509988"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>A Windows IoT Edge használata Linux-tárolók futtatásához

Windows rendszerű számítógépeken tesztelheti a Linux-eszközökhöz készült IoT Edge modulokat.

Éles környezetben a Windows-eszközök csak Windows-tárolókat futtathatnak. Egy gyakori fejlesztési forgatókönyv azonban egy Windows-számítógép használata IoT Edge modulok Linux-eszközökhöz való létrehozásához. A Windows IoT Edge Runtime lehetővé teszi, hogy Linux-tárolókat futtasson **tesztelési és fejlesztési** célokra.

Ez a cikk azokat a lépéseket ismerteti, amelyekkel telepítheti a Azure IoT Edge futtatókörnyezetet Linux-tárolók használatával a Windows x64 (AMD/Intel) rendszeren. Ha többet szeretne megtudni a IoT Edge Runtime telepítőről, beleértve az összes telepítési paraméter részleteit, tekintse meg [a Azure IoT Edge futtatókörnyezet telepítése Windows](how-to-install-iot-edge-windows.md)rendszeren című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ebből a szakaszból megtekintheti, hogy a Windows-eszköz támogatja-e a IoT Edget, és hogy a telepítés előtt előkészítse-e a tároló motorját.

### <a name="supported-windows-versions"></a>Támogatott Windows-verziók

A Linux-tárolókkal rendelkező Azure IoT Edge a Windows bármely olyan verzióját futtathatja, amely megfelel a [Docker Desktop követelményeinek](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) .

A IoT Edge legújabb verziójában található további tudnivalókat lásd: [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases).

Ha virtuális gépen szeretné telepíteni a IoT Edget, engedélyezze a beágyazott virtualizációt, és foglaljon le legalább 2 GB memóriát. A beágyazott virtualizálás engedélyezése a használt hypervisortól függően eltérő. A Hyper-V esetében a 2. generációs virtuális gépekhez alapértelmezés szerint engedélyezve van a beágyazott virtualizálás. A VMWare esetében van egy váltógomb, amely engedélyezi a szolgáltatást a virtuális gépen.

### <a name="prepare-the-container-engine"></a>A tároló motorjának előkészítése

A Azure IoT Edge egy [OCI-kompatibilis](https://www.opencontainers.org/) tároló motorra támaszkodik. A Windows-és Linux-tárolók Windows-gépen való futtatása közötti legnagyobb különbség az, hogy a IoT Edge telepítés tartalmaz egy Windows Container Runtime-t, de a IoT Edge telepítése előtt meg kell adnia a Linux-tárolók saját futtatókörnyezetét.

Egy Windows rendszerű gép létrehozásához és a Linux-eszközökhöz készült tárolók teszteléséhez használhatja a [Docker Desktopot](https://www.docker.com/docker-windows) a tároló motorként. A IoT Edge telepítése előtt telepítenie kell a Docker-t, és konfigurálnia kell a [Linux-tárolók használatát](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) .  

Ha a IoT Edge eszköz egy Windows rendszerű számítógép, ellenőrizze, hogy az megfelel-e a Hyper-V [rendszerkövetelményeinek](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) .

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge telepítése új eszközre

>[!NOTE]
>Azure IoT Edge szoftvercsomagok a csomagokban (a LICENCek könyvtárában) található licencfeltételek hatálya alá esnek. A csomag használata előtt olvassa el a licencfeltételeket. A csomag telepítése és használata jelenti a jelen feltételek elfogadását. Ha nem fogadja el a licencfeltételeket, ne használja a csomagot.

A PowerShell-parancsfájlok letöltik és telepítik a Azure IoT Edge biztonsági démont. A biztonsági démon ezután elindítja az első két futásidejű modult, a IoT Edge ügynököt, amely lehetővé teszi más modulok távoli központi telepítését.

Amikor első alkalommal telepíti a IoT Edge futtatókörnyezetet az eszközön, az eszközt egy IoT hub identitásával kell kiépíteni. Egy IoT Edge eszköz manuálisan is kiépíthető az IoT hub által biztosított eszköz-kapcsolati karakterlánc használatával. Vagy az eszközök kiépítési szolgáltatásával automatikusan kiépítheti az eszközöket, ami hasznos lehet, ha több eszköz is be van állítva.

A különböző telepítési lehetőségekről és paraméterekről további információt a [Azure IoT Edge futtatókörnyezet telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)című cikkben talál. Miután telepítette és konfigurálta a Docker Desktopot a Linux-tárolók számára, a fő telepítési különbség a Linux és a **-ContainerOs** paraméter deklarálása. Példa:

1. Ha még nem tette meg, regisztráljon egy új IoT Edge eszközt, és kérje le az eszköz csatlakoztatási karakterláncát. Másolja a kapcsolódási karakterláncot a szakasz későbbi részében való használatra. Ezt a lépést a következő eszközök használatával végezheti el:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Futtassa a PowerShellt rendszergazdaként.

   >[!NOTE]
   >A PowerShell AMD64-munkamenetének használatával telepítse a IoT Edge, nem a PowerShellt (x86). Ha nem biztos abban, hogy melyik munkamenet-típust használja, futtassa a következő parancsot:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Az **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows rendszerű számítógép támogatott verzióban van-e, bekapcsolja a tárolók szolgáltatást, majd letölti a Moby Runtime (amely nem a Linux-tárolók esetében használatos) és a IoT Edge futtatókörnyezet. A parancs alapértelmezés szerint a Windows-tárolókat állítja be, ezért deklarálja a Linux-t a kívánt tároló operációs rendszerként.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Ezen a ponton a IoT Core-eszközök automatikusan újraindulnak. Előfordulhat, hogy a Windows 10 vagy Windows Server rendszerű eszközök újraindítását kérik. Ha igen, indítsa újra az eszközt. Ha az eszköz elkészült, futtassa újra a PowerShellt rendszergazdaként.

5. Az **inicializálás-IoTEdge** parancs konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a manuális kiépítés egy eszköz-kapcsolódási karakterlánccal. A Linux-t a kívánt tároló operációs rendszer megadásával deklaráljuk.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Ha a rendszer kéri, adja meg az 1. lépésben lekért eszköz-kapcsolódási karakterláncot. Az eszköz-csatlakoztatási karakterlánc a fizikai eszközt a IoT Hub eszköz-azonosítójával társítja.

   Az eszköz-kapcsolatok karakterlánca a következő formátumot veszi figyelembe, és nem tartalmazhat idézőjeleket: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Sikeres telepítés ellenőrzése

A IoT Edge szolgáltatás állapotának ellenõrzése:

```powershell
Get-Service iotedge
```

A szolgáltatási naplók vizsgálata az elmúlt 5 percben:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futtasson automatizált vizsgálatot a leggyakoribb konfigurációs és hálózati hibákhoz:

```powershell
iotedge check
```

Futó modulok listázása. Új telepítés után az egyetlen modulnak kell megjelennie a **edgeAgent**. A [IoT Edge-modulok első üzembe helyezése](how-to-deploy-modules-portal.md) után a **edgeHub**másik rendszermodulja is elindul az eszközön.

```powershell
iotedge list
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már telepített egy IoT Edge eszközt a futtatott futtatókörnyezettel, telepítheti [IoT Edge modulokat](how-to-deploy-modules-portal.md).

Ha nem sikerül a IoT Edge megfelelően telepíteni, tekintse meg a [hibaelhárítási](troubleshoot.md) oldalt.

Ha egy meglévő telepítést szeretne frissíteni a IoT Edge legújabb verziójára, tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

---
title: Az Azure IoT Edge a Linux rendszerre telepíthető Windows |} A Microsoft Docs
description: A Windows 10-es, a Windows Server és a Windows IoT Core Linux-tárolókhoz az Azure IoT Edge telepítési utasításokat
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: b7386cbbe18d7e05c2fbffb96f6214b468956192
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151700"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Windows, Linux-tárolókat futtathat IoT Edge használata

Tesztelje IoT Edge-modulok Windows gépen a Linux rendszerű eszközök számára. 

Éles forgatókönyvekben Windows-eszközök csak Windows-tárolók futtatni. Viszont fejlesztési gyakran előfordul, hogy egy Windows-számítógép segítségével hozhat létre az IoT Edge-modulokat Linux rendszerű eszközök. Az IoT Edge-futtatókörnyezet, a Windows lehetővé teszi, hogy a Linux-tárolókat futtathat **teszteléshez és fejlesztéshez** céljából. 

Ez a cikk a Linux-tárolók használata a Windows x64 (Intel vagy AMD) az Azure IoT Edge-futtatókörnyezet telepítésének lépéseit sorolja fel rendszer. További információ az IoT Edge telepítője, beleértve a telepítési paraméterek részleteit: [telepítse az Azure IoT Edge-futtatókörnyezet Windows](how-to-install-iot-edge-windows.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben a szakaszban használja, tekintse át, hogy a Windows-eszköz IoT Edge is támogatják-e, és előkészítéséhez, telepítés előtt egy tároló-motor. 

### <a name="supported-windows-versions"></a>Támogatott Windows-verziók

Az Azure IoT Edge a Linux-tárolók futtassa az alábbi Windows-verziók: 
* A Windows 10 Évfordulós frissítés (build 14393) vagy újabb
* Windows Server 2016 vagy újabb

Mit tartalmaz az IoT Edge a legújabb verzióra további információ: [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases).

Ha szeretné telepíteni az IoT Edge egy virtuális gépen, beágyazott virtualizálás engedélyezése, és legalább 2 GB memóriát lefoglalni. Beágyazott virtualizálás engedélyezése hogyan eltér attól függően, a hipervizor használatát. A Hyper-V a 2. generációs virtuális gépek beágyazott virtualizálás alapértelmezés szerint engedélyezve. A VMWare van egy ki-/ bekapcsolása a virtuális gépen a funkció engedélyezéséhez. 

### <a name="prepare-the-container-engine"></a>A tároló motor előkészítése 

Az Azure IoT Edge támaszkodik egy [OCI-kompatibilis](https://www.opencontainers.org/) container-motor. A legnagyobb konfiguráció közötti különbség Windows és Linux-tárolókat futtat egy Windows-gép, hogy az IoT Edge telepítése magában foglalja egy Windows-tároló modult, de meg kell adnia a saját futásidejű Linux-tárolók esetén az IoT Edge telepítése előtt. 

Egy Windows-gép fejlesztéséhez és teszteléséhez a tárolók a Linux rendszerű eszközök beállításához használhatja [Docker asztali](https://www.docker.com/docker-windows) a tároló motorként. Telepítheti a Dockert, és konfigurálja úgy, hogy kell [Linux-tárolók használata](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) IoT Edge telepítése előtt.  

Ha az IoT Edge-eszközt egy Windows-számítógépen, ellenőrizze, hogy megfelel-e a [rendszerkövetelmények](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge egy új eszköz telepítése

>[!NOTE]
>Az Azure IoT Edge szoftvercsomagok feltételei vonatkoznak rá a licencet (a címtárban licenccel) a csomagokban található. Kérjük, olvassa el a licencfeltételeket, a csomag használata előtt. Az üzembe helyezése és használata a csomag jelent a feltételek elfogadása. Ha nem fogadja el a licencfeltételeket, ne használja a csomag.

Egy PowerShell-szkript letölti és telepíti az Azure IoT Edge biztonsági démon. A biztonsági démon ezután elindítja az első két futásidejű modulok, az IoT Edge ügynök, amely lehetővé teszi az egyéb modulok távoli telepítések. 

Ha egy eszköz először telepíti az IoT Edge-futtatókörnyezet, kell biztosítsa az eszköz IoT hubról identitást. Egy adott IoT Edge-eszköz kiépítése az IoT hub által biztosított eszközök kapcsolatok karakterlánc segítségével manuálisan. Másik lehetőségként használhatja a Device Provisioning Service-eszközök automatikus kiépítésére, amely akkor hasznos, ha sok eszköz beállításához van. 

További információ a különböző telepítési lehetőségei és a paraméterek a cikk [telepítse az Azure IoT Edge-futtatókörnyezet Windows](how-to-install-iot-edge-windows.md). Miután telepítette és konfigurálta a Linux-tárolók Docker Desktop, a telepítés fő különbség a Linux-nyilatkozik a **- ContainerOs** paraméter. Példa: 

1. Ha még nem tette, egy új IoT Edge-eszköz regisztrálása, és az eszköz kapcsolati karakterláncának lekérése. Másolja a kapcsolati karakterláncot, ez a rész későbbi használat céljából. Ezt a lépést a következő eszközök használatával is elvégezheti:

   * [Azure Portal](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Futtassa a Powershellt rendszergazdaként.

   >[!NOTE]
   >PowerShell AMD64 munkamenet használata IoT Edge segítségével, nem (x86) PowerShell telepítéséhez. Ha nem biztos abban, hogy melyik munkamenet típusa használ, futtassa a következő parancsot:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. A **üzembe helyezés – IoTEdge** parancs ellenőrzi, hogy a Windows-gépen valamelyik támogatott verzióra, bekapcsolja a tárolók funkciót, és ezután letölti az moby modul (amely nem használható Linux-tárolók) és az IoT Edge-futtatókörnyezet. Windows-tárolók, a parancs alapértelmezés szerint Linux így deklarálható mint a kívánt operációs rendszer. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. Ezen a ponton IoT Core-eszközök automatikus újraindítását. Egyéb Windows 10-es vagy Windows Server-eszközök késztethetik, újra kell indítani. Ha igen, most indítsa újra az eszközt. Ha az eszköz készen áll, PowerShell futtatása rendszergazdaként újra.

5. A **Initialize-IoTEdge** parancsot az IoT Edge-futtatókörnyezet konfigurálása a gépen. A parancs alapértelmezés szerint az eszköz kapcsolati karakterlánc manuális kiépítési. Deklarálja Linux, a kívánt operációs rendszer újra. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. Amikor a rendszer kéri, adja meg az eszköz kapcsolati karakterláncát, amely az 1. lépésben lekért. Az eszköz kapcsolati karakterláncának társítja a fizikai eszköz az IoT Hub-Eszközazonosító. 

   Az eszköz kapcsolati karakterláncának formátuma a következő vesz igénybe, és nem tartalmazhat idézőjelek közé: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>A sikeres telepítésének ellenőrzése

Ellenőrizze az IoT Edge-szolgáltatás állapotát. Futtatásával kell szerepelnie.  

```powershell
Get-Service iotedge
```

Vizsgálja meg a szolgáltatási naplók az elmúlt 5 percben. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Futó modulok listája. Egy új telepítést követően a futó van megjelennie csak modul **edgeAgent**. Miután [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md), mások láthatja. 

```powershell
iotedge list
```

## <a name="next-steps"></a>További lépések

Most, hogy az IoT Edge-eszköz kiosztva a modul telepítve van, [üzembe helyezése IoT Edge-modulok](how-to-deploy-modules-portal.md).

Ha az IoT Edge megfelelően telepítése problémák merülnek fel, tekintse meg a [hibaelhárítási](troubleshoot.md) lapot.

Egy meglévő telepítéshez az IoT Edge a legújabb verzióra frissítéséhez lásd [az IoT Edge biztonsági démon és a futtatókörnyezet frissítése](how-to-update-iot-edge.md).

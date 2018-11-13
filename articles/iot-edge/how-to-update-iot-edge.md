---
title: Eszközök frissítése a legújabb verzióra, az Azure IoT Edge |} A Microsoft Docs
description: A biztonság-démont és az IoT Edge-futtatókörnyezet legfrissebb verzióit futtatni az IoT Edge-eszközök frissítése
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1522d9c9bc4fda178d8571fb57cb9c94ed1044ae
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567791"
---
# <a name="update-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet frissítése

Amint az IoT Edge szolgáltatás kiadja az új verziók, érdemes frissíteni szeretné, hogy a legújabb funkciókat és biztonsági fejlesztések az IoT Edge-eszközökön. Ez a cikk ismerteti az IoT Edge-eszközök frissítése, ha új verzió érhető el. 

IoT Edge-eszköz két összetevőből, ha újabb verzióra szeretne frissíteni kell. Az első az a biztonsági démont, amely futtatja az eszközön, és a futtatókörnyezet kezdődik, amikor az eszköz elindul. Jelenleg a biztonsági démon csak frissíthetők magáról az eszközről. A második összetevő nem a futtatókörnyezet, az Edge hub és az Edge agent-modulok. Az üzembe helyezés felépítésében függően a futtatókörnyezet frissíthetők az eszközről, vagy távolról. 

Az Azure IoT Edge legújabb verzióját, lásd: [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>A biztonsági démon frissítése

Az IoT Edge biztonsági démon a natív összetevője, amely az IoT Edge-eszközön a package manager használatával frissíteni kell. 

Ellenőrizze a verziót a biztonsági démon a paranccsal az eszközön futó `iotedge version`. 

### <a name="linux-devices"></a>Linux rendszerű eszközök

A Linux rendszerű eszközökön használja a apt-get paranccsal vagy a megfelelő Csomagkezelő frissíteni a biztonsági démon. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Windows-eszközök

Windows-eszközön használja a PowerShell-parancsfájlt, távolítsa el, majd telepítse újra a biztonsági démon. A telepítési parancsfájl automatikusan lekéri a biztonsági démon legújabb verzióját. Adja meg a kapcsolati karakterláncot az eszköz a telepítési folyamat során újra kell. 

Távolítsa el a biztonsági démon rendszergazdai PowerShell-munkamenetben. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
UnInstall-SecurityDaemon
```

Telepítse újra a biztonsági démon attól függően, hogy az IoT Edge-eszköz Windows-tárolók vagy a Linux-tárolók használja. Cserélje le a kifejezés **\<Windows vagy Linux-alapú\>** a tárolók operációs rendszerek egyikével. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOS <Windows or Linux>
```

## <a name="update-the-runtime-containers"></a>A futtatókörnyezet tárolóiból frissítése

Az Edge agentet és az Edge hub-tárolók frissítése módja attól függ, hogy használhat működés közbeni címkéket (például az 1.0-s) vagy adott címkék (például 1.0.2-es) a központi telepítésben. 

Az IoT Edge-ügynök és az Edge hub-modulok jelenleg az eszközön a parancsokkal verziójának ellenőrzése `iotedge logs edgeAgent` vagy `iotedge logs edgeHub`. 

  ![Tároló verzió megtekintése](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge-címkék ismertetése

Az Edge agentet és az Edge hub lemezképek címkével vannak ellátva, amely a velük társított IoT Edge-verzióval. Címkék használata a futtatókörnyezet képekkel két különböző módja van: 

* **A működés közbeni címkék** – csak az első két értékeket a verziószám használatával a legújabb rendszerképet, amely megfelel ezen számjegy. Például 1.0 frissül, amikor nincs az új kiadás, mutasson a legújabb 1.0.x verziót. Ha a tároló-futtatókörnyezet az IoT Edge-eszközön újra lekéri a rendszerképet, a futásidejű modulok a legújabb verzióra frissíti. Ez a módszer javasolt fejlesztési célokra. A működés közbeni címkéket az Azure portal alapértelmezett telepítések. 
* **Adott címkék** -explicit módon állítsa be a rendszerkép verziószámát a verziószám mindhárom értéket használja. Ha például 1.0.2-es eredeti kiadás után nem fognak változni. Ha készen áll frissíteni eszközhöz adhat meg egy új verziószámot a manifest nasazení. Ez a megközelítés termelési célra ajánlott.

### <a name="update-a-rolling-tag-image"></a>A működés közbeni címke-lemezkép frissítése

Ha a központi telepítésben használja működés közbeni címkék (például mcr.microsoft.com/azureiotedge-hub:**1.0-s**) a tároló-futtatókörnyezet kényszerítése az eszközön, hogy a rendszerkép legújabb verzióját kell majd. 

Törölje a rendszerkép helyi verzióját az IoT Edge-eszköz. 

```cmd/sh
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Előfordulhat, hogy kell használnia a kényszerített `-f` jelző eltávolítja a képeket. 

Az IoT Edge szolgáltatás kérje le a legújabb verzióra a futtatókörnyezet képek és automatikusan indítsa el az eszközön újra azokat. 

### <a name="update-a-specific-tag-image"></a>Egy adott címkével lemezkép frissítése

Ha adott címkék a központi telepítésben használja (például mcr.microsoft.com/azureiotedge-hub:**1.0.2-es**) akkor meg kell frissíteni a címkét az üzembe helyezés jegyzékfájlban és a módosításokat alkalmazza az eszközre. 

Az Azure Portalon, a modul telepítési lemezképeket a deklarált a **speciális Edge-futtatókörnyezet-beállítások konfigurálása** szakaszban. 

[Speciális edge-futtatókörnyezet beállítások konfigurálása](./media/how-to-update-iot-edge/configure-runtime.png)

A JSON manifest nasazení, frissítse a modulban lévő rendszerképek az **systemModules** szakaszban. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>További lépések

Megtekintheti a legfrissebb [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases).

Maradjon naprakész a legújabb frissítések és a közlemény a [IOT-blog](https://azure.microsoft.com/blog/topics/internet-of-things/) 
---
title: Frissítés az IoT Edge verziója az Azure IoT Edge-eszközök – |} A Microsoft Docs
description: A biztonság-démont és az IoT Edge-futtatókörnyezet legfrissebb verzióit futtatni az IoT Edge-eszközök frissítése
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a3dd7f78362b5f5c99dc4a74fe0a32c4d26be5b7
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311917"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Az IoT Edge biztonsági démon és a futtatókörnyezet frissítése

Amint az IoT Edge szolgáltatás kiadja az új verziók, érdemes frissíteni az IoT Edge-eszközökön a legújabb funkciókról és a biztonsági fejlesztések. Ez a cikk ismerteti az IoT Edge-eszközök frissítése, ha új verzió érhető el. 

IoT Edge-eszköz két összetevőből, ha újabb verzióra szeretne frissíteni kell. Az első az a biztonsági démont, amely futtatja az eszközön, és a futásidejű modulok kezdődik, amikor az eszköz elindul. Jelenleg a biztonsági démon csak frissíthetők magáról az eszközről. A másodperc összetevőt a futtatókörnyezet, az IoT Edge hub és az IoT Edge-ügynök modulok. Az üzembe helyezés felépítésében függően a futtatókörnyezet frissíthetők az eszközről, vagy távolról. 

Az Azure IoT Edge legújabb verzióját, lásd: [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases).

>[!IMPORTANT]
>Az Azure IoT Edge futtat egy Windows-eszközön, ha nem frissítik verzióra 1.0.5 Ha az eszköz vonatkozik a következők egyikét: 
>* Az eszköz nem frissített a Windows build 17763. 1.0.5 nem támogatja a Windows IoT Edge-verzió régebbi, mint 17763 épít fel.
>* A Java vagy Node.js-modulok futtatása Windows-eszközén. Hagyja ki 1.0.5 verzió, még akkor is, ha a Windows-eszköz frissítése a legújabb buildre. 
>
>IoT Edge verzió 1.0.5 kapcsolatos további információkért lásd: [1.0.5 kibocsátási megjegyzések](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Megakadályozza, hogy a fejlesztői eszközöket a legújabb verzióra frissítésével kapcsolatos további információkért lásd: [az IoT-fejlesztői blog](https://devblogs.microsoft.com/iotdev/).


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

Windows-eszközön használja a PowerShell-parancsfájlt, távolítsa el, majd telepítse újra a biztonsági démon. A telepítési parancsfájl automatikusan lekéri a biztonsági démon legújabb verzióját. 

Távolítsa el a biztonsági démon rendszergazdai PowerShell-munkamenetben. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon
```

Fut a `Uninstall-SecurityDaemon` parancs paraméterek nélkül csak eltávolítja a biztonsági démon az eszközről, a modul két tárolórendszerképeket együtt. A config.yaml fájlt az eszközön, valamint a a Moby tároló motor adatai másolatok. A konfigurációs információk azt jelenti, hogy adja meg a kapcsolati karakterlánc vagy a telepítési folyamat során újra az eszköz Device Provisioning Service-információk nem kell tartani. 

Telepítse újra a biztonsági démon attól függően, hogy az IoT Edge-eszköz Windows-tárolók vagy a Linux-tárolók használja. Cserélje le a kifejezés **\<Windows vagy Linux-alapú\>** a megfelelő tárolót operációs rendszerekkel. Használja a **- ExistingConfig** jelzőt az eszközön a meglévő config.yaml fájlra mutasson. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOS <Windows or Linux>
```

Ha szeretné a biztonsági démon egy adott verzióját telepítse, töltse le a megfelelő iotedged-windows.zip fájlt a [IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases). Ezután használja a `-OfflineInstallationPath` paraméter, a fájl helyére mutasson. További információkért lásd: [kapcsolat nélküli telepítés](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>A futtatókörnyezet tárolóiból frissítése

Frissítse az IoT Edge-ügynök és az IoT Edge hubot tárolók módja attól függ, hogy használhat működés közbeni címkéket (például az 1.0-s) vagy adott címkék (például 1.0.2-es) a központi telepítésben. 

Az IoT Edge-ügynök és az IoT Edge hub-modulok jelenleg az eszközön a parancsokkal verziójának ellenőrzése `iotedge logs edgeAgent` vagy `iotedge logs edgeHub`. 

  ![A naplókat tároló verzió azonosításához](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge-címkék ismertetése

Az IoT Edge-ügynök és az IoT Edge hubot lemezképek címkével vannak ellátva, amely a velük társított IoT Edge-verzióval. Címkék használata a futtatókörnyezet képekkel két különböző módja van: 

* **A működés közbeni címkék** – csak az első két értékeket a verziószám használatával a legújabb rendszerképet, amely megfelel ezen számjegy. Például 1.0 frissül, amikor nincs az új kiadás, mutasson a legújabb 1.0.x verziót. Ha a tároló-futtatókörnyezet az IoT Edge-eszközön újra lekéri a rendszerképet, a futásidejű modulok a legújabb verzióra frissíti. Ez a módszer javasolt fejlesztési célokra. A működés közbeni címkéket az Azure portal alapértelmezett telepítések. 
* **Adott címkék** -explicit módon állítsa be a rendszerkép verziószámát a verziószám mindhárom értéket használja. Ha például 1.0.2-es eredeti kiadás után nem fognak változni. Ha készen áll frissíteni eszközhöz adhat meg egy új verziószámot a manifest nasazení. Ez a megközelítés termelési célra ajánlott.

### <a name="update-a-rolling-tag-image"></a>A működés közbeni címke-lemezkép frissítése

Ha a központi telepítésben használja működés közbeni címkék (például mcr.microsoft.com/azureiotedge-hub:**1.0-s**) a tároló-futtatókörnyezet kényszerítése az eszközön, hogy a rendszerkép legújabb verzióját kell majd. 

Törölje a rendszerkép helyi verzióját az IoT Edge-eszköz. Windows-gépeken a biztonsági démon is eltávolítása a futtatókörnyezet képek, így nem kell újra figyelembe ezt a lépést. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Előfordulhat, hogy kell használnia a kényszerített `-f` jelző eltávolítja a képeket. 

Az IoT Edge szolgáltatás kérje le a legújabb verzióra a futtatókörnyezet képek és automatikusan indítsa el az eszközön újra azokat. 

### <a name="update-a-specific-tag-image"></a>Egy adott címkével lemezkép frissítése

Ha adott címkék a központi telepítésben használja (például mcr.microsoft.com/azureiotedge-hub:**1.0.2-es**) akkor meg kell frissíteni a címkét az üzembe helyezés jegyzékfájlban és a módosításokat alkalmazza az eszközre. 

Az Azure Portalon, a modul telepítési lemezképeket a deklarált a **speciális Edge-futtatókörnyezet-beállítások konfigurálása** szakaszban. 

![Speciális edge-futtatókörnyezet beállítások konfigurálása](./media/how-to-update-iot-edge/configure-runtime.png)

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

A legújabb frissítések és a bejelentés naprakész a [IOT-blog](https://azure.microsoft.com/blog/topics/internet-of-things/) 
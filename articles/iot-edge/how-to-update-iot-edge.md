---
title: Frissítés az IoT Edge verziója az Azure IoT Edge-eszközök – |} A Microsoft Docs
description: A biztonság-démont és az IoT Edge-futtatókörnyezet legfrissebb verzióit futtatni az IoT Edge-eszközök frissítése
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58171d2fc8e7e1563bd83689d1cd91c55fea239a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456543"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Az IoT Edge biztonsági démon és a futtatókörnyezet frissítése

Mivel a IoT Edge szolgáltatás új verziókat szabadít fel, frissítenie kell a IoT Edge eszközöket a legújabb funkciókkal és biztonsági frissítésekkel. Ez a cikk ismerteti az IoT Edge-eszközök frissítése, ha új verzió érhető el. 

IoT Edge-eszköz két összetevőből, ha újabb verzióra szeretne frissíteni kell. Az első a biztonsági démon, amely az eszközön fut, és elindítja a futásidejű modulokat az eszköz indításakor. Jelenleg a biztonsági démon csak frissíthetők magáról az eszközről. A második összetevő a futtatókörnyezet, amely az IoT Edge hub és a IoT Edge Agent moduljaiból tevődik fel. Az üzembe helyezés felépítésében függően a futtatókörnyezet frissíthetők az eszközről, vagy távolról. 

A Azure IoT Edge legújabb verziójának megkereséséhez tekintse meg [Azure IoT Edge kiadásait](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>A biztonsági démon frissítése

Az IoT Edge biztonsági démon a natív összetevője, amely az IoT Edge-eszközön a package manager használatával frissíteni kell. 

Az eszközön futó Security Daemon verziójának ellenőrzéséhez használja a következő parancsot: `iotedge version`. 

### <a name="linux-devices"></a>Linux rendszerű eszközök

Linux x64 rendszerű eszközökön használja az apt-get vagy a megfelelő csomagkezelő eszközt a biztonsági démon frissítéséhez. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

Linux ARM32-eszközökön a biztonsági démon legújabb verziójának telepítéséhez hajtsa végre a következő témakört: [install Azure IoT Edge Runtime for Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) . 

### <a name="windows-devices"></a>Windows-eszközök

Windows-eszközökön a PowerShell-parancsfájl használatával frissítse a biztonsági démont. A parancsfájl automatikusan lekéri a biztonsági démon legújabb verzióját. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Az Update-IoTEdge parancs futtatásával távolítsa el a biztonsági démont az eszközről, a két futásidejű tároló lemezképével együtt. A config. YAML fájlt a rendszer az eszközön tárolja, valamint a Moby Container Engine-ből származó adatokkal (ha Windows-tárolókat használ). A konfigurációs adatok megőrzése azt jelenti, hogy a frissítési folyamat során nem kell megadnia a kapcsolódási karakterlánc vagy az eszköz kiépítési szolgáltatásának adatait az eszközhöz. 

Ha a biztonsági démon egy adott verzióját szeretné telepíteni, töltse le a megfelelő Microsoft-Azure-IoTEdge. cab-fájlt [IoT Edge kiadásokból](https://github.com/Azure/azure-iotedge/releases). Ezután a `-OfflineInstallationPath` paraméter használatával mutasson a fájl helyére. További információ: [Offline telepítés](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>A futtatókörnyezet tárolóiból frissítése

A IoT Edge-ügynök és a IoT Edge hub-tárolók frissítésének módja attól függ, hogy a működés közbeni címkéket (például 1,0) vagy az adott címkéket (például 1.0.7) használja-e a telepítésben. 

A IoT Edge-ügynök és IoT Edge hub-modulok verziószámát az eszközön, az `iotedge logs edgeAgent` vagy a `iotedge logs edgeHub`parancs használatával vizsgálja meg. 

  ![A naplókat tároló verzió azonosításához](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge-címkék ismertetése

A IoT Edge-ügynök és a IoT Edge hub-lemezképek a IoT Edge azon verziójával vannak címkézve, amelyhez társítva vannak. Címkék használata a futtatókörnyezet képekkel két különböző módja van: 

* **Jelölő címkék** – csak a verziószám első két értékének beolvasásával kérheti le az adott számjegyeknek megfelelő legújabb képet. Például 1.0 frissül, amikor nincs az új kiadás, mutasson a legújabb 1.0.x verziót. Ha a tároló-futtatókörnyezet az IoT Edge-eszközön újra lekéri a rendszerképet, a futásidejű modulok a legújabb verzióra frissíti. Ez a módszer javasolt fejlesztési célokra. A működés közbeni címkéket az Azure portal alapértelmezett telepítések. 
* **Megadott címkék** – a verziószám mindhárom értékének használatával explicit módon állíthatja be a rendszerkép verzióját. A 1.0.7 például nem változik a kezdeti kiadás után. Ha készen áll frissíteni eszközhöz adhat meg egy új verziószámot a manifest nasazení. Ez a megközelítés termelési célra ajánlott.

### <a name="update-a-rolling-tag-image"></a>A működés közbeni címke-lemezkép frissítése

Ha gördülő címkéket használ a központi telepítésben (például mcr.microsoft.com/azureiotedge-hub:**1,0**), akkor a lemezkép legújabb verziójának lekéréséhez kényszeríteni kell a tároló futtatókörnyezetét az eszközön. 

Törölje a rendszerkép helyi verzióját az IoT Edge-eszköz. Windows rendszerű gépeken a biztonsági démon eltávolítása eltávolítja a futtatókörnyezet lemezképeit is, így nem kell újból végrehajtania ezt a lépést. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Előfordulhat, hogy a rendszerképek eltávolításához a Force `-f` jelzőt kell használnia. 

Az IoT Edge szolgáltatás kérje le a legújabb verzióra a futtatókörnyezet képek és automatikusan indítsa el az eszközön újra azokat. 

### <a name="update-a-specific-tag-image"></a>Egy adott címkével lemezkép frissítése

Ha adott címkéket használ a központi telepítésben (például mcr.microsoft.com/azureiotedge-hub:**1.0.7**), akkor mindössze annyit kell tennie, hogy frissíti a címkét a telepítési jegyzékben, és alkalmazza a módosításokat az eszközön. 

A Azure Portal a futásidejű telepítési lemezképeket a **speciális Edge-futtatókörnyezet beállításainak konfigurálása** című szakaszban deklaráljuk. 

![Speciális Edge-futtatókörnyezet beállításainak konfigurálása](./media/how-to-update-iot-edge/configure-runtime.png)

A JSON-telepítési jegyzékben frissítse a modul lemezképeit a **systemModules** szakaszban. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>Frissítés a Release Candidate verzióra

Azure IoT Edge rendszeresen felszabadítja a IoT Edge szolgáltatás új verzióit. Az egyes stabil kiadások előtt egy vagy több Release Candidate (RC) verzió található. Az RC-verziók tartalmazzák a kiadás tervezett funkcióit, de továbbra is a stabil kiadáshoz szükséges tesztelési és érvényesítési folyamatokon mennek keresztül. Ha korábban új funkciót szeretne tesztelni, telepítheti az RC verziót, és visszajelzést küldhet a GitHubon keresztül. 

A kiadásra jelölt verziók ugyanazt a számozási konvenciót követik, mint a Releases, de a **-RC** és egy növekményes szám is a végéhez fűzve. A kiadási jelölteket megtekintheti a stabil verzióként megjelenő [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases) listájában. Keresse meg például a **1.0.7-RC1** és a **1.0.7-RC2**, a **1.0.7**előtti két kiadási jelöltet. Azt is láthatja, hogy az RC-verziók **előzetes** címkékkel vannak megjelölve. 

Előzetes verzióként a Release Candidate verziók nem szerepelnek a normál telepítők által megcélzott legújabb verzióban. Ehelyett manuálisan kell megcéloznia a tesztelni kívánt RC-verzióhoz tartozó eszközöket. A IoT Edge eszköz operációs rendszertől függően a következő fejezetek segítségével frissítheti a IoT Edget egy adott verzióra:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Következő lépések

Tekintse meg a legújabb [Azure IoT Edge kiadásokat](https://github.com/Azure/azure-iotedge/releases).

Naprakészen tarthatja a legújabb frissítéseket és bejelentéseket a [eszközök internetes hálózata blogban](https://azure.microsoft.com/blog/topics/internet-of-things/) 
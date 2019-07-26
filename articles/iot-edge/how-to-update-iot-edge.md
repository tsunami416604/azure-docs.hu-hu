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
ms.custom: seodec18
ms.openlocfilehash: b6b4cd38ce5f591d43f27f735a48993cc1a1ab63
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414449"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Az IoT Edge biztonsági démon és a futtatókörnyezet frissítése

Mivel a IoT Edge szolgáltatás új verziókat szabadít fel, frissítenie kell a IoT Edge eszközöket a legújabb funkciókkal és biztonsági frissítésekkel. Ez a cikk ismerteti az IoT Edge-eszközök frissítése, ha új verzió érhető el. 

IoT Edge-eszköz két összetevőből, ha újabb verzióra szeretne frissíteni kell. Az első a biztonsági démon, amely az eszközön fut, és elindítja a futásidejű modulokat az eszköz indításakor. Jelenleg a biztonsági démon csak frissíthetők magáról az eszközről. A második összetevő a futtatókörnyezet, amely az IoT Edge hub és a IoT Edge Agent moduljaiból tevődik fel. Az üzembe helyezés felépítésében függően a futtatókörnyezet frissíthetők az eszközről, vagy távolról. 

Az Azure IoT Edge legújabb verzióját, lásd: [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases).

>[!IMPORTANT]
>Ha Windows rendszerű eszközön futtat Azure IoT Edge, ne frissítsen a verzió 1.0.5, ha a következők egyike az eszközre vonatkozik: 
>* Nem frissítette az eszközt a Windows Build 17763-re. IoT Edge 1.0.5-verzió nem támogatja a 17763-nál régebbi Windows-buildek használatát.
>* Java-vagy Node. js-modulokat futtat a Windows-eszközön. A verzió 1.0.5 akkor is kihagyhatja, ha frissítette a Windows-eszközt a legújabb buildre. 
>
>További információ a IoT Edge 1.0.5: [1.0.5 kibocsátási megjegyzések](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). A fejlesztői eszközök legújabb verzióra történő frissítésével kapcsolatos további információkért tekintse meg [a IoT fejlesztői blogját](https://devblogs.microsoft.com/iotdev/).


## <a name="update-the-security-daemon"></a>A biztonsági démon frissítése

Az IoT Edge biztonsági démon a natív összetevője, amely az IoT Edge-eszközön a package manager használatával frissíteni kell. 

Ellenőrizze a verziót a biztonsági démon a paranccsal az eszközön futó `iotedge version`. 

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

A parancsok `iotedge logs edgeAgent` vagy `iotedge logs edgeHub`a segítségével keresse meg az eszközön a IoT Edge-ügynök és IoT Edge hub-modulok verzióját. 

  ![A naplókat tároló verzió azonosításához](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge-címkék ismertetése

A IoT Edge-ügynök és a IoT Edge hub-lemezképek a IoT Edge azon verziójával vannak címkézve, amelyhez társítva vannak. Címkék használata a futtatókörnyezet képekkel két különböző módja van: 

* **A működés közbeni címkék** – csak az első két értékeket a verziószám használatával a legújabb rendszerképet, amely megfelel ezen számjegy. Például 1.0 frissül, amikor nincs az új kiadás, mutasson a legújabb 1.0.x verziót. Ha a tároló-futtatókörnyezet az IoT Edge-eszközön újra lekéri a rendszerképet, a futásidejű modulok a legújabb verzióra frissíti. Ez a módszer javasolt fejlesztési célokra. A működés közbeni címkéket az Azure portal alapértelmezett telepítések. 
* **Adott címkék** -explicit módon állítsa be a rendszerkép verziószámát a verziószám mindhárom értéket használja. A 1.0.7 például nem változik a kezdeti kiadás után. Ha készen áll frissíteni eszközhöz adhat meg egy új verziószámot a manifest nasazení. Ez a megközelítés termelési célra ajánlott.

### <a name="update-a-rolling-tag-image"></a>A működés közbeni címke-lemezkép frissítése

Ha a központi telepítésben használja működés közbeni címkék (például mcr.microsoft.com/azureiotedge-hub:**1.0-s**) a tároló-futtatókörnyezet kényszerítése az eszközön, hogy a rendszerkép legújabb verzióját kell majd. 

Törölje a rendszerkép helyi verzióját az IoT Edge-eszköz. Windows rendszerű gépeken a biztonsági démon eltávolítása eltávolítja a futtatókörnyezet lemezképeit is, így nem kell újból végrehajtania ezt a lépést. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Előfordulhat, hogy kell használnia a kényszerített `-f` jelző eltávolítja a képeket. 

Az IoT Edge szolgáltatás kérje le a legújabb verzióra a futtatókörnyezet képek és automatikusan indítsa el az eszközön újra azokat. 

### <a name="update-a-specific-tag-image"></a>Egy adott címkével lemezkép frissítése

Ha adott címkéket használ a központi telepítésben (például mcr.microsoft.com/azureiotedge-hub:**1.0.7**), akkor mindössze annyit kell tennie, hogy frissíti a címkét a telepítési jegyzékben, és alkalmazza a módosításokat az eszközön. 

Az Azure Portalon, a modul telepítési lemezképeket a deklarált a **speciális Edge-futtatókörnyezet-beállítások konfigurálása** szakaszban. 

![Speciális Edge-futtatókörnyezet beállításainak konfigurálása](./media/how-to-update-iot-edge/configure-runtime.png)

A JSON manifest nasazení, frissítse a modulban lévő rendszerképek az **systemModules** szakaszban. 

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

## <a name="next-steps"></a>További lépések

Megtekintheti a legfrissebb [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases).

Naprakészen tarthatja a legújabb frissítéseket és bejelentéseket a [eszközök internetes hálózata blogban](https://azure.microsoft.com/blog/topics/internet-of-things/) 
---
title: IoT Edge verziójának frissítése az eszközökön – Azure IoT Edge | Microsoft Docs
description: IoT Edge eszközök frissítése a biztonsági démon és a IoT Edge futtatókörnyezet legújabb verzióinak futtatásához
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1e29420b8e029a7e1723f969753eb2081381a8e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434168"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>A IoT Edge biztonsági démon és futtatókörnyezet frissítése

Mivel a IoT Edge szolgáltatás új verziókat szabadít fel, frissítenie kell a IoT Edge eszközöket a legújabb funkciókkal és biztonsági frissítésekkel. Ez a cikk tájékoztatást nyújt arról, hogyan frissítheti IoT Edge eszközeit, amikor új verzió érhető el.

Egy IoT Edge eszköz két összetevőjét frissíteni kell, ha újabb verzióra szeretne áttérni. Az első a biztonsági démon, amely az eszközön fut, és elindítja a futásidejű modulokat az eszköz indításakor. A biztonsági démont jelenleg csak az eszközről lehet frissíteni. A második összetevő a futtatókörnyezet, amely az IoT Edge hub és a IoT Edge Agent moduljaiból tevődik fel. Attól függően, hogy miként strukturálja az üzemelő példányt, a futtatókörnyezet az eszközről vagy távolról is frissíthető.

A Azure IoT Edge legújabb verziójának megkereséséhez tekintse meg [Azure IoT Edge kiadásait](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>A biztonsági démon frissítése

A IoT Edge Security Daemon egy natív összetevő, amelyet a IoT Edge eszköz csomagkezelő funkciójával kell frissíteni.

Az eszközön futó Security Daemon verziójának ellenőrzéséhez használja a következő parancsot: `iotedge version`.

### <a name="linux-devices"></a>Linuxos eszközök

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

Az Update-IoTEdge parancs futtatása eltávolítja és frissíti a biztonsági démont az eszközről, valamint a két futásidejű tároló lemezképét. A config. YAML fájlt a rendszer az eszközön tárolja, valamint a Moby Container Engine-ből származó adatokkal (ha Windows-tárolókat használ). A konfigurációs adatok megőrzése azt jelenti, hogy a frissítési folyamat során nem kell megadnia a kapcsolódási karakterlánc vagy az eszköz kiépítési szolgáltatásának adatait az eszközhöz.

Ha a biztonsági démon egy adott verzióját szeretné telepíteni, töltse le a megfelelő Microsoft-Azure-IoTEdge. cab-fájlt [IoT Edge kiadásokból](https://github.com/Azure/azure-iotedge/releases). Ezután a `-OfflineInstallationPath` paraméter használatával mutasson a fájl helyére. További információ: [Offline telepítés](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>A futásidejű tárolók frissítése

A IoT Edge-ügynök és a IoT Edge hub-tárolók frissítésének módja attól függ, hogy a működés közbeni címkéket (például 1,0) vagy az adott címkéket (például 1.0.7) használja-e a telepítésben.

A IoT Edge-ügynök és IoT Edge hub-modulok verziószámát az eszközön, az `iotedge logs edgeAgent` vagy a `iotedge logs edgeHub`parancs használatával vizsgálja meg.

  ![Tároló verziójának keresése a naplókban](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge címkék ismertetése

A IoT Edge-ügynök és a IoT Edge hub-lemezképek a IoT Edge azon verziójával vannak címkézve, amelyhez társítva vannak. A következő két különböző módon használhatja a címkéket a futásidejű lemezképekkel:

* **Jelölő címkék** – csak a verziószám első két értékének beolvasásával kérheti le az adott számjegyeknek megfelelő legújabb képet. Például a 1,0 frissül, amikor új kiadás jelenik meg, amely a legújabb 1.0. x verzióra mutat. Ha a IoT Edge eszközön lévő tároló futtatókörnyezete újra lekéri a lemezképet, a futásidejű modulok a legújabb verzióra frissülnek. Ez a megközelítés fejlesztési célokra javasolt. Központi telepítések a Azure Portal alapértelmezettről a címkékre.

* **Megadott címkék** – a verziószám mindhárom értékének használatával explicit módon állíthatja be a rendszerkép verzióját. A 1.0.7 például nem változik a kezdeti kiadás után. Ha készen áll a frissítésre, deklarálhat egy új verziószámot az üzembe helyezési jegyzékben. Ezt a megközelítést éles célokra javasoljuk.

### <a name="update-a-rolling-tag-image"></a>Egy gördülő címke rendszerképének frissítése

Ha gördülő címkéket használ a központi telepítésben (például mcr.microsoft.com/azureiotedge-hub:**1,0**), akkor a lemezkép legújabb verziójának lekéréséhez kényszeríteni kell a tároló futtatókörnyezetét az eszközön.

Törölje a rendszerkép helyi verzióját a IoT Edge eszközről. Windows rendszerű gépeken a biztonsági démon eltávolítása eltávolítja a futtatókörnyezet lemezképeit is, így nem kell újból végrehajtania ezt a lépést.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Előfordulhat, hogy a rendszerképek eltávolításához a Force `-f` jelzőt kell használnia.

A IoT Edge szolgáltatás lekéri a futásidejű lemezképek legújabb verzióit, és automatikusan elindítja őket az eszközön.

### <a name="update-a-specific-tag-image"></a>Egy adott címke rendszerképének frissítése

Ha adott címkéket használ a központi telepítésben (például mcr.microsoft.com/azureiotedge-hub:**1.0.8**), akkor mindössze annyit kell tennie, hogy frissíti a címkét a telepítési jegyzékben, és alkalmazza a módosításokat az eszközön.

1. A Azure Portal IoT Hub válassza ki a IoT Edge eszközt, és válassza a **modulok beállítása**lehetőséget.

1. A **IoT Edge-modulok** szakaszban válassza a **futtatókörnyezet beállításai**lehetőséget.

   ![Futtatókörnyezet beállításainak konfigurálása](./media/how-to-update-iot-edge/configure-runtime.png)

1. A **Futásidejű beállítások**területen frissítse a **rendszerkép** értékét az **Edge hub** számára a kívánt verzióra. Ne válassza a **Mentés** csak még lehetőséget.

   ![Edge hub-rendszerkép verziójának frissítése](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Csukja össze az **Edge hub** beállításait, vagy görgessen lefelé, és frissítse az **Edge-ügynök** **rendszerképének** értékét ugyanazzal a kívánt verzióval.

   ![Edge hub-ügynök verziójának frissítése](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Kattintson a **Mentés** gombra.

1. Válassza a **felülvizsgálat + létrehozás**lehetőséget, tekintse át a telepítést, és válassza a **Létrehozás**lehetőséget.

## <a name="update-to-a-release-candidate-version"></a>Frissítés a Release Candidate verzióra

Azure IoT Edge rendszeresen felszabadítja a IoT Edge szolgáltatás új verzióit. Az egyes stabil kiadások előtt egy vagy több Release Candidate (RC) verzió található. Az RC-verziók tartalmazzák a kiadás tervezett funkcióit, de továbbra is a stabil kiadáshoz szükséges tesztelési és érvényesítési folyamatokon mennek keresztül. Ha korábban új funkciót szeretne tesztelni, telepítheti az RC verziót, és visszajelzést küldhet a GitHubon keresztül.

A kiadásra jelölt verziók ugyanazt a számozási konvenciót követik, mint a Releases, de a **-RC** és egy növekményes szám is a végéhez fűzve. A kiadási jelölteket megtekintheti a stabil verzióként megjelenő [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases) listájában. Keresse meg például a **1.0.7-RC1** és a **1.0.7-RC2**, a **1.0.7**előtti két kiadási jelöltet. Azt is láthatja, hogy az RC-verziók **előzetes** címkékkel vannak megjelölve.

Előzetes verzióként a Release Candidate verziók nem szerepelnek a normál telepítők által megcélzott legújabb verzióban. Ehelyett manuálisan kell megcéloznia a tesztelni kívánt RC-verzióhoz tartozó eszközöket. A IoT Edge eszköz operációs rendszertől függően a következő fejezetek segítségével frissítheti a IoT Edget egy adott verzióra:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Következő lépések

Tekintse meg a legújabb [Azure IoT Edge kiadásokat](https://github.com/Azure/azure-iotedge/releases).

Naprakészen tarthatja a legújabb frissítéseket és bejelentéseket a [eszközök internetes hálózata blogban](https://azure.microsoft.com/blog/topics/internet-of-things/)
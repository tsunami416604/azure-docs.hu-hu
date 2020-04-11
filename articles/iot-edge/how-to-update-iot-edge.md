---
title: IoT Edge-verzió frissítése az eszközökön - Azure IoT Edge | Microsoft dokumentumok
description: Az IoT Edge-eszközök frissítése a biztonsági démon és az IoT Edge futásidejű legújabb verzióinak futtatásához
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ce69593c1df0039d64f89e79124af1150409eff7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113304"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Az IoT Edge biztonsági démon és futtatókörnyezet frissítése

Mivel az IoT Edge szolgáltatás új verziókat ad ki, érdemes frissíteni az IoT Edge-eszközöket a legújabb funkciók és biztonsági fejlesztések érdekében. Ez a cikk az IoT Edge-eszközök frissítéséről nyújt tájékoztatást, ha új verzió érhető el.

Az IoT Edge-eszközök két összetevőjét frissíteni kell, ha egy újabb verzióra szeretne áthelyezni. Az első a biztonsági démon, amely fut az eszközön, és elindítja a futásidejű modulokat, amikor az eszköz elindul. Jelenleg a biztonsági démon csak magáról az eszközről frissíthető. A második összetevő az IoT Edge hub és az IoT Edge-ügynökmodulok futásideje. Attól függően, hogy hogyan strukturálja a központi telepítést, a futásidejű frissíthető az eszközről vagy távolról.

Az Azure IoT Edge legújabb verziójának megkereséséhez tekintse meg az [Azure IoT Edge-kiadások című témakört.](https://github.com/Azure/azure-iotedge/releases)

## <a name="update-the-security-daemon"></a>A biztonsági démon frissítése

Az IoT Edge biztonsági démon egy natív összetevő, amelyet frissíteni kell a csomagkezelő az IoT Edge-eszközön.

Ellenőrizze az eszközön futó biztonsági démon verzióját a parancs `iotedge version`segítségével.

### <a name="linux-devices"></a>Linux-eszközök

Linux x64-es eszközökön az apt-get vagy a megfelelő csomagkezelő segítségével frissítse a biztonsági démont a legújabb verzióra.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Ha a biztonsági démon egy adott verziójára szeretne frissíteni, keresse meg az [IoT Edge-kiadásokból](https://github.com/Azure/azure-iotedge/releases)a megcélozni kívánt verziót. Ebben a verzióban keresse meg a készülékhez megfelelő **libiothsm-std** és **iotedge** fájlokat. Minden fájlesetében kattintson a jobb gombbal a fájlhivatkozásra, és másolja a hivatkozás címét. A hivatkozáscím segítségével telepítse az összetevők adott verzióit:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows rendszerű eszközök

Windows-eszközökön a PowerShell-parancsfájl segítségével frissítse a biztonsági démont. A parancsfájl automatikusan lekéri a biztonsági démon legújabb verzióját.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Az Update-IoTEdge parancs futtatása eltávolítja és frissíti a biztonsági démont az eszközről, valamint a két futásidejű tárolórendszerképet. A config.yaml fájl az eszközön, valamint a Moby tárolómotorból származó adatok (windowsos tárolók használata esetén) tárolják. A konfigurációs adatok megtartása azt jelenti, hogy a frissítési folyamat során nem kell újra megadnia az eszköz kapcsolati karakterláncának vagy eszközkiépítési szolgáltatásának adatait.

Ha a biztonsági démon egy adott verziójára szeretne frissíteni, keresse meg az [IoT Edge-kiadásokból](https://github.com/Azure/azure-iotedge/releases)a megcélozni kívánt verziót. Ebben a verzióban töltse le a **Microsoft-Azure-IoTEdge.cab** fájlt. Ezután a `-OfflineInstallationPath` paraméter segítségével mutasson a helyi fájl helyére. Például:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>A `-OfflineInstallationPath` paraméter egy **Microsoft-Azure-IoTEdge.cab** nevű fájlt keres a megadott könyvtárban. Az IoT Edge 1.0.9-rc4-es verziójától kezdve két .cab fájl áll rendelkezésre, egy az AMD64 eszközökhöz és egy az ARM32-hez. Töltse le az eszköznek megfelelő fájlt, majd nevezze át a fájlt az architektúra-utótag eltávolításához.

A frissítési beállításokkal kapcsolatos `Get-Help Update-IoTEdge -full` további tudnivalókért használja a parancsot, vagy olvassa el az [összes telepítési paramétert.](how-to-install-iot-edge-windows.md#all-installation-parameters)

## <a name="update-the-runtime-containers"></a>A futásidejű tárolók frissítése

Az IoT Edge-ügynök és az IoT Edge hub-tárolók frissítésének módja attól függ, hogy a központi telepítésben futó címkéket (például 1.0) vagy adott címkéket (például 1.0.7) használ-e.

Ellenőrizze az IoT Edge-ügynök és az IoT Edge hub-modulok `iotedge logs edgeAgent` `iotedge logs edgeHub`verzióját az eszközön a parancsokkal vagy.

  ![Tárolóverzió keresése a naplókban](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Az IoT Edge-címkék ismertetése

Az IoT Edge-ügynök és az IoT Edge hub-lemezképek vannak címkézve az IoT Edge-verzió, amely azok társítva vannak. A futóidejű lemezképekcímkéinek két különböző módja van:

* **Gördülő címkék** - Csak a verziószám első két értékét használja a számjegyeknek megfelelő legújabb kép lefelvételéhez. Például az 1.0 frissül, ha új kiadás jelenik meg, amely a legújabb 1.0.x verzióra mutat. Ha a tároló futásidejű az IoT Edge-eszközön ismét lekéri a lemezképet, a futásidejű modulok frissülnek a legújabb verzióra. Ez a megközelítés fejlesztési célokra javasolt. Az Azure Portalról történő központi telepítések alapértelmezés szerint működés közbeni címkékké történnek.

* **Adott címkék** – A verziószám mindhárom értékének használatával explicit módon állíthatja be a lemezkép-verziót. Az 1.0.7 például nem változik a kezdeti kiadás után. Ha készen áll a frissítésre, deklarálhatja az új verziószámot a központi telepítési jegyzékben. Ez a megközelítés termelési célokra javasolt.

### <a name="update-a-rolling-tag-image"></a>Gördülő címke képének frissítése

Ha a központi telepítésben (például mcr.microsoft.com/azureiotedge-hub:**1.0)** gördülő címkéket használ, akkor a tároló futásidejét az eszközön kell kényszerítenie a lemezkép legújabb verziójának lekérése érdekében.

Törölje a kép helyi verzióját az IoT Edge-eszközről. Windows-gépeken a biztonsági démon eltávolítása a futásidejű lemezképeket is eltávolítja, így nem kell újra megtennie ezt a lépést.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Előfordulhat, hogy a `-f` képek eltávolításához az erőjelzőt kell használnia.

Az IoT Edge szolgáltatás lekéri a futásidejű lemezképek legújabb verzióit, és automatikusan újraindítja őket az eszközön.

### <a name="update-a-specific-tag-image"></a>Adott címkekép frissítése

Ha a központi telepítésben meghatározott címkéket használ (például mcr.microsoft.com/azureiotedge-hub:**1.0.8),** akkor mindössze annyit kell tennie, hogy frissíti a címkét a központi telepítési jegyzékben, és alkalmazza a módosításokat az eszközre.

1. Az Azure Portalon az IoT Hubban válassza ki az IoT Edge-eszközt, és válassza **a Modulok beállítása lehetőséget.**

1. Az **IoT Edge-modulok csoportban** válassza a **Futásidejű beállítások lehetőséget.**

   ![Futásidejű beállítások konfigurálása](./media/how-to-update-iot-edge/configure-runtime.png)

1. A **Futásidejű beállítások ban**frissítse az Edge **Hub** **képértékét** a kívánt verzióval. Még ne válassza a **Mentés** lehetőséget.

   ![Az Edge Hub lemezképverziójának frissítése](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Csukja össze az **Edge Hub** beállításait, vagy görgessen lefelé, és frissítse az **Edge Agent** **képértékét** ugyanazzal a kívánt verzióval.

   ![Edge Hub Agent verziójának frissítése](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Kattintson a **Mentés** gombra.

1. Válassza **a Véleményezés + létrehozás**lehetőséget, tekintse át a központi telepítést, és válassza a **Létrehozás lehetőséget.**

## <a name="update-offline-or-to-a-specific-version"></a>Frissítés kapcsolat nélküli módban vagy egy adott verzióra

Ha egy eszközt kapcsolat nélküli módban szeretne frissíteni, vagy az IoT Edge egy adott verziójára `-OfflineInstallationPath` szeretne frissíteni a legújabb verzió helyett, megteheti a paraméterrel.

Az IoT Edge-eszközök két összetevőjének frissítése:

* Egy PowerShell-parancsfájl, amely a telepítési utasításokat tartalmazza
* Microsoft Azure IoT Edge fülke, amely az IoT Edge biztonsági démont (iotedged), a Moby tárolómotort és a Moby CLI-t tartalmazza

1. A legújabb IoT Edge telepítőfájlokat a korábbi verziókkal együtt az [Azure IoT Edge-kiadások ban.](https://github.com/Azure/azure-iotedge/releases)

2. Keresse meg a telepíteni kívánt verziót, és töltse le a következő fájlokat a kibocsátási megjegyzések **Eszközök** szakaszából az IoT-eszközre:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab az 1.0.9-es vagy újabb kiadásokból, vagy a Microsoft-Azure-IoTEdge.cab az 1.0.8-as és újabb kiadásokból.

   A Microsoft-Azure-IotEdge-arm32.cab is elérhető 1.0.9-től kezdve csak tesztelési célokra. Az IoT Edge jelenleg nem támogatott Windows ARM32-eszközökön.

   Fontos, hogy a PowerShell-parancsfájlt ugyanabból a kiadásból használja, mint a .cab fájlt, mert a funkciók változnak, hogy támogassa a funkciókat az egyes kiadásokban.

3. Ha a letöltött .cab fájlon architektúra-utótag található, nevezze át a fájlt csak **Microsoft-Azure-IoTEdge.cab fájlra.**

4. A frissítés offline összetevők, [dot forrás](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) a helyi példányát a PowerShell-parancsfájl. Ezután használja `-OfflineInstallationPath` a paramétert `Update-IoTEdge` a parancs részeként, és adja meg a fájlkönyvtár abszolút elérési útját. Például:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Frissítés a kiadásra jelölt verzióra

Az Azure IoT Edge rendszeresen kiadja az IoT Edge-szolgáltatás új verzióit. Minden stabil kiadás előtt van egy vagy több kiadás jelölt (RC) verzió. RC verziók tartalmazzák az összes tervezett funkciók a kiadás, de még mindig megy keresztül tesztelés és érvényesítés. Ha korábban szeretne tesztelni egy új funkciót, telepíthet egy RC-verziót, és visszajelzést adhat a GitHubon keresztül.

Release candidate verziók követik az azonos számozási konvenciókiadások, de **-rc** plusz egy növekményes számot csatolták a végén. A kiadási jelöltek et az [Azure IoT Edge-kiadások](https://github.com/Azure/azure-iotedge/releases) ugyanazon listájában láthatja, mint a stabil verziókat. Például keresse meg **az 1.0.7-rc1** és **az 1.0.7-rc2-** t , a két kiadási jelöltet, amelyek **az 1.0.7**előtt jöttek . Azt is láthatja, hogy az RC verziók **kiadás előtti** címkékkel vannak megjelölve.

Az IoT Edge-ügynök és a hub modulok RC-verziók, amelyek ugyanazzal a konvencióval vannak címkézve. Például **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2**.

Az előzetes verziók, release candidate verziók nem szerepelnek a legújabb verzió, hogy a rendszeres telepítők cél. Ehelyett manuálisan kell megcéloznia a tesztelni kívánt RC-verzió eszközeit. Az rc-verziótelepítése vagy frissítése többe több, mint az IoT Edge bármely más speciális verziójának célzása.

Ebben a cikkben található szakaszok ból megtudhatja, hogyan frissítheti az IoT Edge-eszközt a biztonsági démon vagy a futásidejű modulok egy adott verziójára.

Ha az IoT Edge-et új számítógépre telepíti, az alábbi hivatkozásoksegítségével megtudhatja, hogyan telepíthet egy adott verziót az eszköz operációs rendszerétől függően:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>További lépések

Tekintse meg a legújabb [Azure IoT Edge-kiadásokat.](https://github.com/Azure/azure-iotedge/releases)

Legyen up-to-date a legújabb frissítéseket és bejelentést az [Internet of Things blog](https://azure.microsoft.com/blog/topics/internet-of-things/)

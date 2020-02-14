---
title: Frissítés az IoT Edge verziója az Azure IoT Edge-eszközök – |} A Microsoft Docs
description: A biztonság-démont és az IoT Edge-futtatókörnyezet legfrissebb verzióit futtatni az IoT Edge-eszközök frissítése
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186516"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Az IoT Edge biztonsági démon és a futtatókörnyezet frissítése

Mivel a IoT Edge szolgáltatás új verziókat szabadít fel, frissítenie kell a IoT Edge eszközöket a legújabb funkciókkal és biztonsági frissítésekkel. Ez a cikk ismerteti az IoT Edge-eszközök frissítése, ha új verzió érhető el.

IoT Edge-eszköz két összetevőből, ha újabb verzióra szeretne frissíteni kell. Az első a biztonsági démon, amely az eszközön fut, és elindítja a futásidejű modulokat az eszköz indításakor. Jelenleg a biztonsági démon csak frissíthetők magáról az eszközről. A második összetevő a futtatókörnyezet, amely az IoT Edge hub és a IoT Edge Agent moduljaiból tevődik fel. Az üzembe helyezés felépítésében függően a futtatókörnyezet frissíthetők az eszközről, vagy távolról.

A Azure IoT Edge legújabb verziójának megkereséséhez tekintse meg [Azure IoT Edge kiadásait](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>A biztonsági démon frissítése

Az IoT Edge biztonsági démon a natív összetevője, amely az IoT Edge-eszközön a package manager használatával frissíteni kell.

Az eszközön futó Security Daemon verziójának ellenőrzéséhez használja a következő parancsot: `iotedge version`.

### <a name="linux-devices"></a>Linux rendszerű eszközök

Linux x64 rendszerű eszközökön használja az apt-get vagy a megfelelő csomagkezelő eszközt a biztonsági démon legújabb verzióra való frissítéséhez.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Ha a biztonsági démon egy adott verziójára szeretne frissíteni, keresse meg a célként használni kívánt verziót [IoT Edge kiadásokból](https://github.com/Azure/azure-iotedge/releases). Ebben a verzióban keresse meg a megfelelő **libiothsm-STD** és **iotedge** fájlokat az eszközhöz. Minden fájlhoz kattintson a jobb gombbal a fájl hivatkozásra, és másolja a hivatkozás címe. A hivatkozás címe segítségével telepítse az összetevők adott verzióit:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows-eszközök

Windows-eszközökön a PowerShell-parancsfájl használatával frissítse a biztonsági démont. A parancsfájl automatikusan lekéri a biztonsági démon legújabb verzióját.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Az Update-IoTEdge parancs futtatása eltávolítja és frissíti a biztonsági démont az eszközről, valamint a két futásidejű tároló lemezképét. A config. YAML fájlt a rendszer az eszközön tárolja, valamint a Moby Container Engine-ből származó adatokkal (ha Windows-tárolókat használ). A konfigurációs adatok megőrzése azt jelenti, hogy a frissítési folyamat során nem kell megadnia a kapcsolódási karakterlánc vagy az eszköz kiépítési szolgáltatásának adatait az eszközhöz.

Ha a biztonsági démon egy adott verziójára szeretne frissíteni, keresse meg a célként használni kívánt verziót [IoT Edge kiadásokból](https://github.com/Azure/azure-iotedge/releases). Ebben a verzióban töltse le a **Microsoft-Azure-IoTEdge. cab** fájlt. Ezután használja a `-OfflineInstallationPath` paramétert, hogy a helyi fájl helyére mutasson. Például:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>A `-OfflineInstallationPath` paraméter egy **Microsoft-Azure-IoTEdge. cab** nevű fájlt keres a megadott könyvtárban. A IoT Edge 1.0.9-rc4-es verziótól kezdődően két. cab fájl használható, egyet az AMD64-eszközökhöz, egyet pedig a ARM32. Töltse le az eszközének megfelelő fájlt, majd nevezze át a fájlt az architektúra utótagjának eltávolításához.

A frissítési lehetőségekkel kapcsolatos további információkért használja az parancsot `Get-Help Update-IoTEdge -full` vagy tekintse át az [összes telepítési paramétert](how-to-install-iot-edge-windows.md#all-installation-parameters).

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

Azure IoT Edge rendszeresen felszabadítja a IoT Edge szolgáltatás új verzióit. Az egyes stabil kiadások előtt egy vagy több Release Candidate (RC) verzió található. Az RC-verziók tartalmazzák a kiadás tervezett funkcióit, de továbbra is tesztelésen és ellenőrzésen mennek keresztül. Ha korábban egy új szolgáltatást szeretne tesztelni, telepítheti az RC-verziót, és visszajelzést küldhet a GitHubon keresztül.

A kiadásra jelölt verziók ugyanazt a számozási konvenciót követik, mint a Releases, de a **-RC** és egy növekményes szám is a végéhez fűzve. A kiadási jelölteket megtekintheti a stabil verzióként megjelenő [Azure IoT Edge kiadások](https://github.com/Azure/azure-iotedge/releases) listájában. Keresse meg például a **1.0.7-RC1** és a **1.0.7-RC2**, a **1.0.7**előtti két kiadási jelöltet. Azt is láthatja, hogy az RC-verziók **előzetes** címkékkel vannak megjelölve.

A IoT Edge-ügynök és a hub-modulok olyan RC-verziókkal rendelkeznek, amelyek ugyanazon egyezménnyel vannak megjelölve. Például: **MCR.microsoft.com/azureiotedge-hub:1.0.7-RC2**.

Előzetes verzióként a Release Candidate verziók nem szerepelnek a normál telepítők által megcélzott legújabb verzióban. Ehelyett manuálisan kell megcéloznia a tesztelni kívánt RC-verzióhoz tartozó eszközöket. A legtöbb esetben az RC verzióra történő telepítés vagy frissítés ugyanaz, mint a Windows rendszerű eszközökre vonatkozó egyik különbség, kivéve a IoT Edge bármely más verzióját. 

A Release Candidate verzióban a PowerShell-parancsfájl, amely lehetővé teszi, hogy a Windows-eszközökön a IoT Edge biztonsági démont telepítse és felügyelje, különböző funkciókkal rendelkezhet, mint a legújabb általánosan elérhető verziók. Az RC IoT Edge. cab fájljának letöltése mellett töltse le a **IotEdgeSecurityDaemon. ps1** parancsfájlt is. A lefuttatott szkript az aktuális forrásban való futtatásához használja a [pont forrását](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) . Például: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

A cikk fejezeteiből megtudhatja, hogyan frissíthet egy IoT Edge eszközt a biztonsági démon vagy a futásidejű modulok egy adott verziójára.

Ha új gépre telepíti a IoT Edget, a következő hivatkozások segítségével megtudhatja, hogyan telepíthet egy adott verziót az eszköz operációs rendszerének megfelelően:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Következő lépések

Tekintse meg a legújabb [Azure IoT Edge kiadásokat](https://github.com/Azure/azure-iotedge/releases).

Naprakészen tarthatja a legújabb frissítéseket és bejelentéseket a [eszközök internetes hálózata blogban](https://azure.microsoft.com/blog/topics/internet-of-things/)

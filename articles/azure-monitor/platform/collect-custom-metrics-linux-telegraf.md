---
title: Egyéni metrikák gyűjtése a Linux virtuális géphez az InfluxData Telegraf ügynökkel
description: Útmutató az InfluxData Telegraf-ügynök üzembe helyezéséhez linuxos virtuális gépen az Azure-ban, és konfigurálja az ügynök metrikák közzététele az Azure Monitorban.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 0ed9144116c1d716124025ef0aae39e7783c5934
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655463"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Egyéni metrikák gyűjtése egy Linux virtuális géphez az InfluxData Telegraf ügynökkel

Az Azure Monitor használatával egyéni metrikákat gyűjthet az alkalmazás telemetriai adatokon keresztül, az Azure-erőforrásokon futó ügynök, vagy akár külső figyelési rendszerek segítségével. Ezután elküldheti őket közvetlenül az Azure Monitornak. Ez a cikk útmutatást nyújt az [InfluxData](https://www.influxdata.com/) Telegraf-ügynök üzembe helyezéséhez egy Linux virtuális gépen az Azure-ban, és konfigurálja az ügynököt metrikák közzétételére az Azure Monitorban. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf ügynök 

[A Telegraf](https://docs.influxdata.com/telegraf/) egy plug-in-driven ügynök, amely lehetővé teszi a metrikák gyűjtését több mint 150 különböző forrásból. Attól függően, hogy milyen számítási feladatok futnak a virtuális gépen, konfigurálhatja az ügynököt, hogy speciális bemeneti beépülő modulokat használjon a metrikák gyűjtéséhez. Ilyenpéldául a MySQL, az NGINX és az Apache. A kimeneti beépülő modulok használatával az ügynök ezután írhat a kiválasztott célhelyekre. A Telegraf-ügynök közvetlenül integrálva van az Azure Monitor egyéni metrikák REST API-val. Támogatja az Azure Monitor kimeneti beépülő modult. Ezzel a beépülő modul használatával az ügynök gyűjtheti a számítási feladatok-specifikus metrikák a Linux virtuális gép, és küldje el őket egyéni metrikák ként az Azure Monitor. 

 ![Távíró ügynök – áttekintés](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Egyéni mutatók küldése 

Ebben az oktatóanyagban egy Linux virtuális gép, amely az Ubuntu 16.04 LTS operációs rendszert futtatja. A Telegraf ügynök a legtöbb Linux operációs rendszer esetében támogatott. Mind a Debian, mind az RPM csomagok elérhetők a csomagolatlan Linux binárisokkal együtt az [InfluxData letöltési portálon.](https://portal.influxdata.com/downloads) További telepítési útmutatót és beállításokat ebben a [Telegraf telepítési útmutatóban](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) talál. 

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

Hozzon létre egy új Linux virtuális gép: 

1. Válassza az **Erőforrás létrehozása** lehetőséget a bal oldali navigációs ablakban. 
1. Keressen **meg virtuális gépet**.  
1. Válassza az **Ubuntu 16.04 LTS lehetőséget,** majd a **Létrehozás**lehetőséget. 
1. Adjon meg egy virtuális gép nevét, például **A MyTelegrafVM.**  
1. Hagyja a lemez típusát **SSD-ként.** Ezután adjon meg egy **felhasználónevet**, például **az azureuser**. 
1. A **Hitelesítés típusmezőben**válassza a **Jelszó**lehetőséget. Ezután adjon meg egy jelszót, amelyet később az SSH-hoz fog használni a virtuális gépbe. 
1. Válassza az **új erőforráscsoport létrehozását.** Ezután adjon meg egy nevet, például **a myResourceGroup**. Válassza ki a **tartózkodási helyét.** Ezután kattintson az **OK** gombra. 

    ![Ubuntu rendszerű virtuális gép létrehozása](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Válasszon méretet a virtuális gép számára. Szűrhet például **számítási típus** vagy **lemeztípus** alapján. 

    ![Virtuális gép mérete Távíró ügynök – áttekintés](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. A **Hálózati** > **hálózati biztonsági csoport** >  **Beállítások** lapján Válassza ki a nyilvános**bejövő portokat,** válassza a **HTTP** és **SSH (22)** lehetőséget. Tartsa meg az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra. 

1. Az Összefoglalás lapon válassza a **Létrehozás** lehetőséget a virtuális gép üzembe helyezésének megkezdéséhez. 

1. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. A központi telepítés befejezése után a virtuális gép összegzése automatikusan megnyílik. 

1. A virtuális gép ablaktáblán keresse meg az **Identitás** lapot. Győződjön meg arról, hogy a virtuális gép rendelkezik a rendszer által hozzárendelt identitás beállítása **Be.** 
 
    ![Telegraf VM identitás előnézete](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez 

Hozzon léte egy SSH-kapcsolatot a virtuális géppel. A virtuális gép áttekintő oldalán kattintson a **Csatlakozás** gombra. 

![Telegraf VM áttekintő lap](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

A **Csatlakozás virtuális géphez** oldalon tartsa meg az alapértelmezett beállításokat a DNS-név alapján a 22-es porton keresztül való csatlakozáshoz. A **Virtuális gép helyi fiókjával történő bejelentkezésben**egy kapcsolatparancs jelenik meg. A parancs másolásához válassza a gombot. Az SSH-kapcsolat parancsa az alábbi példához hasonlóan néz ki: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Illessze be az SSH-kapcsolat parancsot egy rendszerhéjba, például az Azure Cloud Shell vagy a Bash ubuntuon Windows rendszeren, vagy használjon egy Ön által választott SSH-ügyfélalkalmazást a kapcsolat létrehozásához. 

## <a name="install-and-configure-telegraf"></a>A Telegraf telepítése és konfigurálása 

A Telegraf Debian csomag virtuális gépre való telepítéséhez futtassa az SSH-munkamenet következő parancsait: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
A Telegraf konfigurációs fájlja határozza meg a Telegraf működését. Alapértelmezés szerint egy példa konfigurációs fájl van telepítve az **/etc/telegraf/telegraf.conf**elérési útra. A példa konfigurációs fájl felsorolja az összes lehetséges bemeneti és kimeneti beépülő modult. Azonban létrehozunk egy egyéni konfigurációs fájlt, és az ügynök használja a következő parancsok futtatásával: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Az előző kód lehetővé teszi, hogy csak két bemeneti plug-inek: **cpu** és **mem**. A számítógépen futó munkaterheléstől függően további bemeneti beépülő modulokat adhat hozzá. Ilyenpéldául a Docker, a MySQL és az NGINX. A bemeneti beépülő modulok teljes listáját a **További konfiguráció** szakaszban található. 

Végül, ha azt szeretné, hogy az ügynök elkezdi használni az új konfigurációt, kényszerítjük az ügynököt, hogy állítsa le, és indítsa el a következő parancsok futtatásával: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Most az ügynök összegyűjti a metrikákat az egyes megadott bemeneti beépülő modulokból, és kibocsátja azokat az Azure Monitorba. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>A Telegraf-mérőszámok nyomtatása az Azure Portalon 

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). 

1. Nyissa meg az új **Monitor** lapot. Ezután válassza **a Metrikák**lehetőséget.  

     ![Figyelő - Metrikák (előzetes verzió)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Válassza ki a virtuális gép az erőforrás-választóban.

     ![Metrikus diagram](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Jelölje ki a **Telegraf/CPU** névteret, és válassza ki a **usage_system** metrikát. Választhat, hogy a mérőszám dimenziói szerint szűr, vagy feloszthatja őket.  

     ![Névtér és metrika kiválasztása](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>További konfigurálás 

Az előző forgatókönyv arról nyújt tájékoztatást, hogyan konfigurálhatja a Telegraf-ügynököt, hogy néhány alapvető bemeneti beépülő modul metrikákat gyűjtsön. A Telegraf ügynök több mint 150 bemeneti beépülő modult támogat, néhány további beállítási lehetőséggel. InfluxData közzétett egy [listát a támogatott beépülő modulok](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) és [utasításokat, hogyan kell beállítani őket](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Emellett ebben a forgatókönyvben a Telegraf-ügynök segítségével metrikákat bocsáthat ki az ügynök üzembe helyezett virtuális gépről. A Telegraf-ügynök más erőforrások metrikák gyűjtőjeként és továbbítójaként is használható. Ha meg szeretné tudni, hogyan konfigurálhatja az ügynököt más Azure-erőforrások metrikai kibocsátására, olvassa el az [Azure Monitor egyéni metrikakimenete a Telegrafhoz című témakört.](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md)  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez jelölje ki a virtuális gép erőforráscsoportját, és válassza a **Törlés**lehetőséget. Ezután erősítse meg a törölni kívánt erőforráscsoport nevét. 

## <a name="next-steps"></a>További lépések
- További információ az [egyéni mutatókról.](metrics-custom-overview.md)




---
title: Egyéni metrikák gyűjthetők InfluxData Telegraf-ügynökkel rendelkező Linux virtuális géphez
description: Egyéni metrikák gyűjthetők InfluxData Telegraf-ügynökkel rendelkező Linux virtuális géphez
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 14415b88cd6036642442ef9ae23e8dee301bb908
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775611"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Egyéni metrikák gyűjthetők InfluxData Telegraf-ügynökkel rendelkező Linux virtuális géphez

Az Azure Monitor használatával egyéni metrikákat gyűjthet keresztül a telemetriai, az Azure-erőforrások, vagy akár külső-a monitorozási rendszerek futó ügynök. Ezután elküldheti őket közvetlenül az Azure Monitor. Ez a cikk útmutatást nyújt üzembe helyezése a [InfluxData](https://www.influxdata.com/) Telegraf ügynököt egy Linux rendszerű virtuális gépen az Azure-ban és a metrikák közzététele az Azure Monitor-ügynök konfigurálása. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf ügynök 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) plug az adatvezérelt ügynök, amely lehetővé teszi, hogy a gyűjtemény mérőszámok összefoglaló 150 különböző forrásokból. Milyen számítási feladatok futtatása a virtuális Gépen, függően konfigurálhat a az ügynök kihasználhatja a speciális bemeneti modulok kíván gyűjteni. Példák az Apache, MySQL és az NGINX. Kimeneti modulok használatával az ügynök majd írhat Ön által választott helyre. A Telegraf ügynök van integrálva közvetlenül az Azure Monitor REST API-val egyéni metrikákat. Támogatja a beépülő modul az Azure Monitor kimenetnek. Ez a beépülő modul, az ügynök is munkaterhelés-specifikus metrikák gyűjthetők a Linux rendszerű virtuális gépen, és egyéni mérőszámok az Azure Monitor használatával küldheti el ezeket. 

 ![Távíró agent áttekintése](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Egyéni mérőszámok küldése 

Ebben az oktatóanyagban azt az Ubuntu 16.04 LTS operációs rendszert futtató Linux virtuális gép üzembe helyezése. A Telegraf ügynök Linux operációs rendszerek többsége esetén támogatott. Debian és az RPM csomagok érhetők el a csomagolatlan Linux bináris együtt a [InfluxData letöltési portálra](https://portal.influxdata.com/downloads). Ez [Telegraf telepítési útmutató](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) a további telepítési utasításokat és beállítások. 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

Hozzon létre egy új Linux rendszerű virtuális Gépet: 

1. Válassza ki a **erőforrás létrehozása** lehetőséget a bal oldali navigációs ablaktáblán. 
1. Keresse meg **virtuális gép**.  
1. Válassza ki **Ubuntu 16.04 LTS** válassza **létrehozás**. 
1. Adja meg például a virtuális gép nevét **MyTelegrafVM**.  
1. A lemez típusát, hagyja **SSD**. Adja meg egy **felhasználónév**, mint például **azureuser**. 
1. A **hitelesítési típus**válassza **jelszó**. Majd adja meg a jelszót fogja használni később ssh be ezt a virtuális Gépet. 
1. Válassza ki a **hozzon létre új erőforráscsoportot**. Majd adjon meg egy nevet, például: **myResourceGroup**. Válassza ki a **hely**. Válassza ki **OK**. 

    ![Ubuntu rendszerű virtuális gép létrehozása](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Válasszon méretet a virtuális gép számára. Szűrhet **számítási típus** vagy **lemeztípus**, például. 

    ![Virtuális gép mérete telegráf ügynök – áttekintés](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Az a **beállítások** lap **hálózati** > **hálózati biztonsági csoport**   >  ** Nyilvános bejövő portok kiválasztása**válassza **HTTP** és **SSH (22)**. Hagyja meg az alapértelmezett beállításokat, és válassza ki a többi **OK**. 

1. Az összefoglalás lapon válassza ki a **létrehozás** a virtuális gép üzembe helyezésének megkezdéséhez. 

1. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejezését követően a virtuális gép összefoglalás panelje automatikusan megnyílik. 

1. A virtuális gép ablaktáblán keresse meg a **identitás** fülre. Győződjön meg arról, hogy a virtuális gép rendelkezik-e a rendszer által hozzárendelt identitás beállítása **a**. 
 
    ![Telegraf virtuális gép identitása előzetes verzió](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez 

Hozzon léte egy SSH-kapcsolatot a virtuális géppel. Válassza ki a **Connect** gombra a virtuális gép az Áttekintés oldalon. 

![Telegraf virtuális gépek – áttekintés oldalra](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Az a **csatlakozhat a virtuális gép** párbeszédpanelen tartsa meg az alapértelmezett beállításokat a 22-es porton keresztül csatlakozni a DNS-név. A **virtuális gép helyi fiókjával bejelentkezési**, megjelenik egy connection parancsot. Válassza ki a gombra kattintva másolja a parancsot. Az SSH-kapcsolat parancsa az alábbi példához hasonlóan néz ki: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Illessze be az SSH-kapcsolat parancsot egy rendszerhéjból, például az Azure Cloud Shell vagy a Bash on Ubuntu on Windows, vagy a kapcsolat létrehozásához használja az SSH-ügyfelet, a választott. 

## <a name="install-and-configure-telegraf"></a>Telepítse és konfigurálja a Telegraf 

A Telegraf Debian csomag be a virtuális Gépre telepíti, a következő parancsokat az SSH-munkamenetből: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegraf a konfigurációs fájl Telegraf a műveletek határozza meg. Alapértelmezés szerint egy példa konfigurációs fájl elérési útja a telepített **/etc/telegraf/telegraf.conf**. A példa konfigurációs fájl felsorolja az összes lehetséges bemeneti, és a kimeneti beépülő modulokat. Azonban azt hozhat létre egyéni konfigurációs fájlt, és az ügynök vele a következő parancsok futtatásával: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> A fenti kód lehetővé teszi, hogy csak két bemeneti modulok: **cpu** és **memóriára**. Több bemeneti beépülő modulok, attól függően, hogy fut a gépen a munkaterhelés adhat hozzá. Példák a Docker, a MySQL és az nginx-et. A bemeneti modulok teljes listáját lásd: a **további konfigurációs** szakaszban. 

Végül úgy, hogy az ügynököt az új konfigurációt használja, hogy kényszeríti az ügynököt, hogy állítsa le és indítsa el a következő parancsok futtatásával: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Most már az ügynök a megadott bemeneti beépülő modulok mindegyikének adatainak begyűjtése és gridre bocsáthatja ki őket az Azure monitornak. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Az Azure Portalon a Telegraf metrikákat jeleníti meg 

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). 

1. Lépjen az új **figyelő** fülre. Válassza ki **metrikák**.  

     ![Monitor - metrikák (előnézet)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Válassza ki a virtuális gép az erőforrás-választó.

     ![Metrikadiagram](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Válassza ki a **Telegraf/CPU** névteret, és válassza ki a **usage_system** metrikát. Ez a metrika vagy felosztás bekapcsolva őket a dimenziók szerinti szűrést, választhat.  

     ![Válassza ki a névteret és a metrika](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>További konfigurációs 

A fenti forgatókönyv információkat nyújt a mérőszámok gyűjtését néhány alapvető bemeneti modulok Telegraf ügynök konfigurálása. A Telegraf ügynök több mint 150 bemeneti beépülő modulok, néhány kiegészítő további konfigurációs lehetőségekkel támogatással rendelkezik. InfluxData tett közzé egy [támogatott beépülő modulok listájában](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) , és tájékoztatni [konfigurálásukról](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Ezenkívül ez az útmutató, amellyel a Telegraf ügynök gridre bocsáthatja ki a virtuális gép üzemel, az ügynök metrikákat. A Telegraf ügynök a gyűjtő és metrikák továbbító is használható a források. Ismerje meg, hogyan kell konfigurálni az ügynököt gridre bocsáthatja ki az egyéb Azure-erőforrások mérőszámait, lásd: [Azure Monitor egyéni metrika kimeneti Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Amikor szükség van rájuk már nem, törölheti az erőforráscsoportot, virtuális gép és minden kapcsolódó erőforrás. Ehhez válassza ki a virtuális gép erőforráscsoportját, és válassza ki **törlése**. Erősítse meg a nevet az erőforráscsoport törléséhez. 

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).




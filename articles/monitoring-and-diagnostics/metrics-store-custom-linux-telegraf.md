---
title: Egyéni metrikák gyűjthetők InfluxData Telegraf-ügynökkel rendelkező Linux virtuális géphez
description: Egyéni metrikák gyűjthetők InfluxData Telegraf-ügynökkel rendelkező Linux virtuális géphez
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990705"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Egyéni metrikák gyűjthetők InfluxData Telegraf-ügynökkel rendelkező Linux virtuális géphez

Az Azure Monitor lehetővé teszi egyéni metrikák gyűjthetők a telemetriai, az Azure-erőforrások, vagy akár külső-a monitorozási rendszerek futó ügynök-n keresztül, és küldje el őket közvetlenül az Azure Monitor. Ez a cikk útmutatást ad üzembe helyezése elsősorban a [InfluxData](https://www.influxdata.com/) Telegraf ügynököt egy Linux rendszerű virtuális gépen az Azure-ban és a metrikák közzététele az Azure Monitor-ügynök konfigurálása. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf ügynök 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) beépülő modul által készített ügynök, amely lehetővé teszi, hogy a gyűjtemény mérőszámok összefoglaló 150 különböző forrásokból. Attól függően, milyen számítási feladatok futnak a virtuális Gépen (például. MySQL, az NGINX, Apache, stb.) Beállíthatja, hogy az ügynök kihasználhatja a speciális bemeneti beépülő modulok kíván gyűjteni. Kimeneti beépülő modulok, majd engedélyezze a ügynököt tetszőleges célhelyre írására. A Telegraf ügynök van integrálva közvetlenül az Azure Monitor REST API-val egyéni metrikákat, és a egy "az Azure Monitor kimeneti beépülő modul" támogatja. Ez lehetővé teszi a számítási feladatok adott metrikák gyűjthetők a Linux rendszerű virtuális gépen, és küldje el őket az Azure monitornak egyéni mérőszámokként-ügynök. 

 ![Távíró Agent áttekintése](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Egyéni mérőszámok küldése 

Jelen oktatóanyag esetében azt Linux virtuális gép üzembe helyezése az Ubuntu 16.04 LTS operációs rendszert. A Telegraf ügynök Linux operációs rendszerek többsége esetén támogatott. Debian és az RPM-csomagot a InfluxData letöltési portálra a csomagolatlan Linux bináris együtt érhetők el. Ez a telepítési útmutató a további Telegraf telepítési utasításokat és beállítások megtekintéséhez. 

Jelentkezzen be a [Azure Portalon](https://portal.azure.com)

Új Linux rendszerű virtuális gép létrehozása: 

1. Kattintson a **erőforrás létrehozása** lehetőséget a bal oldali navigációs ablaktáblán. 
1. Keresse meg *virtuális gép*  
1. Válassza ki *Ubuntu 16.04 LTS* kattintson **létrehozása** 
1. Adja meg például a virtuális gép nevét *MyTelegrafVM*.  
1. A lemez típusát, hagyja **SSD**, majd adja meg egy **felhasználónév**, mint például *azureuser*. 
1. A *hitelesítési típus*válassza **jelszó**, majd írja be a jelszót később ezt fogja használni az SSH a virtuális gépen. 
1. Válassza ki a **hozzon létre új erőforráscsoportot**, majd adjon meg egy nevet, például: *myResourceGroup*.  Válassza ki a kívánt helyen, majd válassza ki **OK**. 

     ![Ubuntu rendszerű virtuális gép létrehozása](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. Válasszon méretet a virtuális gép számára. Szűrheti a számítási típus vagy a lemez típusát, például. 

     ![Virtuális gép mérete távíró Agent áttekintése](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. Az a **beállítások lapon**, a **hálózati** > **hálózati biztonsági csoport** > ** Nyilvános bejövő portok kiválasztása**válassza *HTTP* és *SSH (22)*. Hagyja meg az alapértelmezett beállításokat, és válassza ki a többi **OK**. 

1. Az összefoglalás lapon válassza ki a virtuális gép üzembe helyezésének megkezdéséhez hozzon létre. 

1. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejeztével a virtuális gép összefoglalás panelje automatikusan megnyílik. 

1. A virtuális gép paneljén, lépjen a **identitás** lapra, és ellenőrizze a virtuális gép rendelkezik-e a rendszer által hozzárendelt identitással *a*. 
 
![FILLIN](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez 

Hozzon léte egy SSH-kapcsolatot a virtuális géppel. Válassza ki a csatlakozás gomb az Áttekintés lap a virtuális géphez. 

![FILLIN](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

A virtuális gép kapcsolódás tartsa meg az alapértelmezett beállításokat a 22-es porton keresztül csatlakozni a DNS-név. A virtuális gép használatával jelentkezzen jelenik meg helyi fiók egy connection parancsot. Kattintson a gombra a parancs kimásolásához. Az SSH-kapcsolat parancsa az alábbi példához hasonlóan néz ki: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Illessze be az SSH-kapcsolat parancs-rendszerhéjat, például az Azure Cloud Shell Bash on Ubuntu on Windows, vagy egy SSH-ügyfél szabadon segítségével hozza létre a kapcsolatot. 

## <a name="install-and-configure-telegraf"></a>Telepítse és konfigurálja a Telegraf 

A Telegraf Debian csomag be a virtuális Gépre telepíti, a következő parancsokat az SSH-munkamenetből: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegraf a konfigurációs fájl Telegraf a műveletek határozza meg. Alapértelmezés szerint egy példa konfigurációs fájl elérési útja a telepítve "/ etc/telegraf/telegraf.conf". A példa konfigurációs fájl felsorolja az összes lehetséges bemeneti és kimeneti beépülő modulokat. Azonban fogjuk hozzon létre egy egyéni konfigurációs fájlt, és rendelkezik ezzel a következő parancsok futtatásával ügynökkel 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> A fenti csak lehetővé teszi két bemeneti beépülő modulok "processzor" és "memóriára", nyugodtan adjon hozzá további bemeneti beépülő modulok (Docker, MySQL, az NGINX, stb.) a gépen futó függően. A bemeneti beépülő modulok teljes listája itt található. 

Végül úgy, hogy az ügynököt az új konfigurációt használja azt kényszeríti az ügynököt, hogy állítsa le és indítsa el a következő parancsok futtatásával 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Most már az ügynök a megadott bemeneti beépülő modulok mindegyikének adatainak begyűjtése és gridre bocsáthatja ki őket az Azure monitornak. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Az Azure Portalon a Telegraf metrikákat jeleníti meg 

1. Nyissa meg a [Azure Portalon](https://portal.azure.com) 

1. Keresse meg az új figyelő lapot, és válassza **metrikák**.  
     ![FILLIN](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. Válassza ki a virtuális gép az erőforrás-választó

     ![FILLIN](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. Válassza ki a *Telegraf/CPU* névteret, és válassza ki a *usage_system* metrikát. Ez a mérőszám a dimenziók és szűrő válassza ki, vagy őket osztani.  

     ![FILLIN](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>További konfigurációs 

A fenti forgatókönyv információt nyújt a mérőszámok gyűjtését néhány alapvető bemeneti beépülő modulok Telegraf ügynök konfigurálása. A Telegraf ügynök több mint 150 bemeneti beépülő modulok, néhány kiegészítő további konfigurációs lehetőségekkel támogatással rendelkezik. InfluxData tett közzé egy [támogatott beépülő modulok listájában](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) , és tájékoztatni [konfigurálásukról](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Ez a forgatókönyv emellett engedélyezett a Telegraf ügynök használatával gridre bocsáthatja ki a virtuális gép üzemel, az ügynök metrikákat. A Telegraf ügynök a gyűjtő és metrikák továbbító is használható a források. Ismerje meg, hogyan kell konfigurálni az ügynököt gridre bocsáthatja ki az egyéb Azure-erőforrások mérőszámait, lásd: [Azure Monitor egyéni metrika kimeneti Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, válassza a törlés, majd erősítse meg a törölni kívánt erőforráscsoport nevét. 

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).



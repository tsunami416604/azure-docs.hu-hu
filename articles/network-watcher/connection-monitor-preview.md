---
title: Csatlakozáskezelő (előzetes verzió) | Microsoft Docs
description: Ismerje meg, hogyan figyelheti a hálózati kommunikációt egy elosztott környezetben a kapcsolatkezelő (előzetes verzió) használatával.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 8f3a6f002fbebe215699c9b97a6dce63177c446f
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599326"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Hálózati kapcsolat figyelése a kapcsolat figyelője (előzetes verzió)

A kapcsolódási figyelő (előzetes verzió) egységes, végpontok közötti kapcsolatok figyelését teszi lehetővé az Azure Network Watcherban. A kapcsolódási figyelő (előzetes verzió) funkció támogatja a hibrid és az Azure-beli Felhőbeli üzembe helyezést. A Network Watcher eszközöket biztosít az Azure-környezetek kapcsolattal kapcsolatos metrikáinak figyelésére, diagnosztizálására és megtekintésére.

Íme néhány használati eset a Csatlakozáskezelő (előzetes verzió) szolgáltatáshoz:

- Az előtér-webkiszolgáló virtuális gépe egy adatbázis-kiszolgáló virtuális géppel kommunikál egy többrétegű alkalmazásban. Szeretné megtekinteni a két virtuális gép közötti hálózati kapcsolatot.
- Azt szeretné, hogy az USA keleti régiójában lévő virtuális gépek Pingelje a virtuális gépeket az USA középső régiójában, és szeretné összehasonlítani a régiók közötti hálózati késéseket.
- Több helyszíni Office-telephelye is van Seattle-ben, Washingtonban és Ashburn-ben, Virginia államban. Az Office-helyek az Office 365 URL-címekhez csatlakoznak. Az Office 365 URL-címeinek felhasználói számára hasonlítsa össze a Seattle és a Ashburn közötti késéseket.
- A hibrid alkalmazásnak kapcsolódnia kell egy Azure Storage-végponthoz. A helyszíni hely és az Azure-alkalmazás ugyanahhoz az Azure Storage-végponthoz csatlakozik. Össze szeretné hasonlítani a helyszíni hely késéseit az Azure-alkalmazás késésével.
- Szeretné megtekinteni a helyszíni telepítések és a felhőalapú alkalmazást futtató Azure-beli virtuális gépek közötti kapcsolatot.

Az előzetes verzióban a kapcsolati figyelő a két funkció közül a legjobbat ötvözi: a Network Watcher a [kapcsolat figyelője](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) funkciót és a Network Performance monitor (NPM) [szolgáltatás-kapcsolat figyelője](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) funkciót.

Íme néhány előny a Csatlakozáskezelő (előzetes verzió) szolgáltatáshoz:

* Egységes, intuitív élmény az Azure-hoz és a hibrid monitorozási igényekhez
* Régiók közötti, több-munkaterület közötti kapcsolat figyelése
* Nagyobb Szondázási gyakoriságok és a hálózati teljesítmény jobb láthatósága
* Gyorsabb riasztás a hibrid üzembe helyezésekhez
* HTTP-, TCP-és ICMP-alapú kapcsolatok ellenőrzésének támogatása 
* Metrikák és Log Analytics támogatás az Azure és a nem Azure-beli tesztelési beállításokhoz

![Ábra, amely bemutatja, hogyan kommunikál a kapcsolatkezelő az Azure-beli virtuális gépekkel, a nem Azure-beli gazdagépekkel,-végpontokkal és az adattárolási hellyel](./media/connection-monitor-2-preview/hero-graphic.png)

A Csatlakozáskezelő (előzetes verzió) figyeléshez való használatának megkezdéséhez kövesse az alábbi lépéseket: 

1. Figyelési ügynökök telepítése.
1. Network Watcher engedélyezése az előfizetésben.
1. Hozzon létre egy összekötő-figyelőt.
1. Az adatelemzés és a riasztások beállítása.
1. A hálózatban felmerülő problémák diagnosztizálása.

A következő szakaszokban részletesen ismertetjük ezeket a lépéseket.

## <a name="install-monitoring-agents"></a>Figyelési ügynökök telepítése

A kapcsolati figyelő egyszerűsített végrehajtható fájlokra támaszkodik a kapcsolati ellenőrzések futtatásához.  Az Azure-környezetek és a helyszíni környezetek közötti kapcsolati ellenőrzéseket is támogatja. A használt végrehajtható fájl attól függ, hogy a virtuális gép az Azure-ban vagy a helyszínen van-e tárolva.

### <a name="agents-for-azure-virtual-machines"></a>Az Azure Virtual Machines-ügynökök

Ahhoz, hogy a Csatlakozáskezelő felismerje az Azure-beli virtuális gépeket megfigyelési forrásként, telepítse a Network Watcher-ügynök virtuálisgép-bővítményét. Ezt a bővítményt *Network Watcher bővítménynek*is nevezzük. Az Azure Virtual Machines használatához a bővítménynek teljes körű monitorozást és egyéb speciális funkciókat kell elindítania. 

A [virtuális gép létrehozásakor](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)a Network Watcher bővítményt is telepítheti. A [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) és a [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)Network Watcher bővítményét külön is telepítheti, konfigurálhatja és elháríthatja.

A hálózati biztonsági csoport (NSG) vagy a tűzfal szabályai letilthatják a forrás és a cél közötti kommunikációt. A Csatlakozáskezelő észleli ezt a problémát, és diagnosztikai üzenetként jeleníti meg a topológiában. A kapcsolat figyelésének engedélyezéséhez győződjön meg arról, hogy a NSG és a tűzfalszabályok engedélyezik a TCP-vagy ICMP-csomagok használatát a forrás és a cél között.

### <a name="agents-for-on-premises-machines"></a>Helyszíni gépek ügynökei

Ahhoz, hogy a Csatlakozáskezelő felismerje a helyszíni gépeket a figyeléshez, telepítse a Log Analytics ügynököt a gépekre. Ezután engedélyezze a Network Performance Monitor megoldást. Ezek az ügynökök Log Analytics munkaterületekhez vannak társítva, ezért be kell állítania a munkaterület-azonosítót és az elsődleges kulcsot, mielőtt az ügynökök el tudják indítani a figyelést.

A Windows rendszerű gépek Log Analytics ügynökének telepítéséhez lásd: [Azure monitor virtuálisgép-bővítmény a Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)rendszerhez.

Ha az elérési út tartalmazza a tűzfalakat vagy a hálózati virtuális berendezéseket (NVA), akkor győződjön meg arról, hogy a célhely elérhető.

## <a name="enable-network-watcher-on-your-subscription"></a>Network Watcher engedélyezése az előfizetésen

A virtuális hálózattal rendelkező összes előfizetés engedélyezve van a Network Watcher. Amikor létrehoz egy virtuális hálózatot az előfizetésében, Network Watcher automatikusan engedélyezve lesz a virtuális hálózat régiójában és előfizetésében. Ez az automatikus engedélyezés nem befolyásolja az erőforrásokat, vagy díjköteles. Győződjön meg arról, hogy a Network Watcher nincs explicit módon letiltva az előfizetésen. 

További információ: [Network Watcher engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Kapcsolatfigyelő létrehozása 

A kapcsolati figyelő rendszeres időközönként figyeli a kommunikációt. Tájékoztatja Önt a Elérhetőség és a késés változásairól. A forrás-és a cél végpontok közötti aktuális és korábbi hálózati topológiát is megtekintheti.

A források lehetnek olyan Azure-beli virtuális gépek vagy helyszíni számítógépek, amelyeken telepítve van egy felügyeleti ügynök. A célként megadott végpontok lehetnek Office 365 URL-címek, Dynamics 365 URL-címek, egyéni URL-címek, Azure-beli virtuális gépek erőforrás-azonosítói, IPv4, IPv6, FQDN vagy bármely tartománynév.

### <a name="access-connection-monitor-preview"></a>Elérési kapcsolat figyelője (előzetes verzió)

1. A Azure Portal kezdőlapján válassza a **Network Watcher**lehetőséget.
1. A bal oldalon a **figyelés** szakaszban válassza a **Csatlakozáskezelő (előzetes verzió)** lehetőséget.
1. Megjelenik az összes, a Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott figyelő. A kapcsolódási figyelő klasszikus felhasználói felületén létrehozott kapcsolódási figyelők megtekintéséhez lépjen a **Csatlakozáskezelő** lapra.

    ![A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott kapcsolatok figyelőit bemutató képernyőfelvétel](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Kapcsolatfigyelő létrehozása

A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott kapcsolódó figyelőkben a helyszíni és az Azure-beli virtuális gépeket is felveheti forrásként. Ezek a kapcsolati figyelők a végpontokhoz való kapcsolódás figyelésére is használhatók. A végpontok lehetnek az Azure-ban vagy bármely más URL-címen vagy IP-címen.

A Csatlakozáskezelő (előzetes verzió) a következő entitásokat tartalmazza:

* **Csatlakozáskezelő erőforrás** – régióra jellemző Azure-erőforrás. A következő entitások mindegyike egy Csatlakozáskezelő erőforrás tulajdonságait képezi.
* **Endpoint (végpont** ) – a kapcsolati ellenőrzésekben részt vevő forrás vagy cél. Ilyen végpontok például az Azure-beli virtuális gépek, a helyszíni ügynökök, az URL-címek és az IP-címek.
* **Teszt konfiguráció** – egy adott protokollra jellemző konfiguráció. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztelési gyakoriságot és az egyéb paramétereket.
* **Test Group (tesztelési csoport** ) – a forrás-végpontokat, a célként megadott végpontokat és a tesztelési konfigurációkat tartalmazó csoport. Egy Csatlakozáskezelő több tesztelési csoportot is tartalmazhat.
* **Test (teszt** ) – a forrás-végpont, a cél végpont és a tesztelési konfiguráció kombinációja. A teszt a legrészletesebben használt szint, amelyen a figyelési adatokat elérhetővé kell tennie. A figyelési adatmennyiség magában foglalja a sikertelen ellenőrzések százalékos arányát és az oda-és visszaút idejét (RTT).

 ![A kapcsolati figyelőt bemutató diagram, a tesztelési csoportok és tesztek közötti kapcsolat meghatározása](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Figyelő létrehozása a Azure Portalból

A Azure Portal a következő lépésekkel hozhat létre egy kapcsolódási figyelőt:

1. A **Csatlakozáskezelő (előzetes verzió)** irányítópultján, a bal felső sarokban kattintson a **Létrehozás**elemre.
1. Az **alapvető beállítások** lapon adja meg a Csatlakozáskezelő adatait:
   * **Csatlakozáskezelő neve** – adja hozzá a Csatlakozáskezelő nevét. Használja az Azure-erőforrások szabványos elnevezési szabályait.
   * **Előfizetés** – válasszon egy előfizetést a kapcsolatok figyelője számára.
   * **Régió** – válasszon régiót a kapcsolatok figyelője számára. Csak az ebben a régióban létrehozott forrás virtuális gépeket lehet kiválasztani.
   * **Munkaterület konfigurációja** – a munkaterület tárolja a figyelési adatait. Használhat egyéni munkaterületet vagy az alapértelmezett munkaterületet is. 
       * Az alapértelmezett munkaterület használatához jelölje be a jelölőnégyzetet. 
       * Egyéni munkaterület kiválasztásához törölje a jelet a jelölőnégyzetből. Ezután válassza ki az előfizetést és a régiót az egyéni munkaterülethez. 
1. A lap alján kattintson a Next ( **Tovább) gombra: csoportok tesztelése**.

   ![A Csatlakozáskezelő alapjai lapját ábrázoló képernyőfelvétel](./media/connection-monitor-2-preview/create-cm-basics.png)

1. A **test groups (tesztelési csoportok** ) lapon válassza a **+ teszt csoport**elemet. A tesztelési csoportok beállításával kapcsolatban lásd: [tesztkörnyezet létrehozása a kapcsolódási figyelőben](#create-test-groups-in-a-connection-monitor). 
1. A lap alján válassza a **Tovább: Áttekintés + létrehozás** elemet a kapcsolódási figyelő áttekintéséhez.

   ![A test groups (tesztelési csoportok) lapot és a tesztelési csoport részleteit tartalmazó panelt ábrázoló képernyőfelvétel](./media/connection-monitor-2-preview/create-tg.png)

1. A **felülvizsgálat + létrehozás** lapon tekintse át az alapszintű információkat és a tesztelési csoportokat a Csatlakozáskezelő létrehozása előtt. Ha módosítania kell a kapcsolódási figyelőt:
   * Az alapszintű részletek szerkesztéséhez válassza a ceruza ikont.
   * Egy tesztelési csoport szerkesztéséhez válassza ki azt.

   > [!NOTE] 
   > A **felülvizsgálat + létrehozás** lapon a Csatlakozáskezelő előzetes verziójának havi díja látható. Jelenleg az **aktuális költség** oszlop nem számít fel díjat. Ha a kapcsolódási figyelő általánosan elérhetővé válik, ebben az oszlopban a havi díj jelenik meg. 
   > 
   > Még a Csatlakozáskezelő előzetes verziójának szakasza is érvényes, Log Analytics betöltési díjat számítunk fel.

1. Amikor készen áll a kapcsolódási figyelő létrehozására, a **felülvizsgálat + létrehozás** lap alján válassza a **Létrehozás**lehetőséget.

   ![Képernyőkép a Csatlakozáskezelőról, amely a felülvizsgálat + létrehozás lapot mutatja](./media/connection-monitor-2-preview/review-create-cm.png)

A Csatlakozáskezelő (előzetes verzió) létrehozza a figyelő erőforrást a háttérben.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>ARMClient használatával létesített figyelő létrehozása

A következő kód használatával hozzon létre egy ARMClient a kapcsolódási figyelő használatával.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premises agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Az üzembe helyezési parancs a következő:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Tesztelési csoportok létrehozása egy figyelőben

A-figyelő minden egyes tesztelési csoportja olyan forrásokat és célhelyeket tartalmaz, amelyek a hálózati paramétereken keresztül teszteltek. A rendszer a sikertelen ellenőrzések százalékos arányát és a RTT.

A Azure Portal a következő mezők értékeinek megadásával hozzon létre egy tesztelési csoportot a kapcsolódási figyelőben:

* **Tesztelési csoport letiltása** – ezt a mezőt kiválasztva letilthatja az összes olyan forrás és cél figyelését, amelyet a tesztelési csoport meghatároz. Ez a beállítás alapértelmezés szerint nincs bejelölve.
* **Név** – a tesztelési csoport neve.
* **Források** – az Azure-beli virtuális gépeket és a helyszíni gépeket forrásként is megadhatja, ha az ügynökök telepítve vannak rajtuk. A forráshoz tartozó ügynök telepítéséhez tekintse meg a [figyelési ügynökök telepítése](#install-monitoring-agents)című témakört.
   * Az Azure-ügynökök kiválasztásához válassza az **Azure-ügynökök** fület. Itt csak a kapcsolódási figyelő létrehozásakor megadott régióhoz kötött virtuális gépek jelennek meg. Alapértelmezés szerint a virtuális gépek a hozzájuk tartozó előfizetésbe vannak csoportosítva. Ezek a csoportok össze vannak csukva. 
   
       Az előfizetések szintjéről a hierarchiában lévő többi szintre is lebonthatja a részletezést:

      Az **előfizetés** > **erőforráscsoportok** > **virtuális hálózatok** > **alhálózatok** > **virtuális gépek ügynökkel**

      A **Group By** mező értékét úgy is megváltoztathatja, hogy bármely más szintről elindítsa a fát. Ha például a virtuális hálózat alapján csoportosítja a csoportot, akkor a hierarchiában lévő ügynökökkel rendelkező virtuális gépeket a **virtuális hálózatok** > **alhálózatokat** > **ügynökökkel rendelkező virtuális**gépeket.

      ![Képernyőfelvétel a kapcsolatkezelő szolgáltatásról, amely a források hozzáadása panelt és az Azure Agents fület jeleníti meg](./media/connection-monitor-2-preview/add-azure-sources.png)

   * A helyszíni ügynökök kiválasztásához válassza a **nem – Azure ügynökök** lapot. Alapértelmezés szerint az ügynökök régiónként vannak csoportosítva. Mindegyik munkaterülethez konfigurálva van a Network Performance Monitor-megoldás. 
   
       Ha Network Performance Monitort kell hozzáadnia a munkaterülethez, szerezze be azt az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)-ről. A Network Performance Monitor hozzáadásával kapcsolatos további információkért lásd: [monitorozási megoldások a Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       A **Csatlakozáskezelő létrehozása** nézetben az **alapok** lapon az alapértelmezett régió van kiválasztva. Ha megváltoztatja a régiót, az ügynököket az új régióban lévő munkaterületekről is kiválaszthatja. A **Group By** mező értékét is módosíthatja alhálózatok szerint csoportosítva.

      ![Képernyőfelvétel a kapcsolatkezelő szolgáltatásról, amely a források hozzáadása panelt és a nem Azure-ügynökök lapot mutatja](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * A kiválasztott Azure-és nem Azure-ügynökök áttekintéséhez lépjen a **felülvizsgálat** lapra.

      ![Képernyőfelvétel a kapcsolatok Figyelőről, a források hozzáadása panel és a felülvizsgálat lap](./media/connection-monitor-2-preview/review-sources.png)

   * Amikor befejezte a források beállítását, a **források hozzáadása** panel alján válassza a **kész**lehetőséget.

* **Célhelyek** – az Azure-beli virtuális gépekkel vagy bármely végponttal (nyilvános IP-címmel, URL-lel vagy FQDN-vel) való kapcsolat figyeléséhez megadhatja őket célhelyként. Egyetlen tesztelési csoportban Azure-beli virtuális gépeket, Office 365 URL-címeket, Dynamics 365 URL-címeket és egyéni végpontokat adhat hozzá.

    * Az Azure-beli virtuális gépek célhelyként való kiválasztásához válassza az Azure-beli **virtuális gépek** fület. Alapértelmezés szerint az Azure-beli virtuális gépek olyan előfizetési hierarchiába vannak csoportosítva, amely ugyanabban a régióban található, mint amelyet a **kapcsolatkezelő létrehozása** nézetben kiválasztott, az **alapok** lapon. Módosíthatja a régiót, és az újonnan kiválasztott régióból is kiválaszthatja az Azure-beli virtuális gépeket. Ezután részletezheti az előfizetések szintjét a hierarchia más szintjeire, például az Azure-ügynökök szintjére.

       ![Képernyőkép a célhelyek hozzáadása panelről, amely az Azure-beli virtuális gépek lapot mutatja](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Képernyőkép a célhelyek hozzáadása panelről, amely az előfizetés szintjét mutatja](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * A végpontok célhelyként való kiválasztásához válassza a **végpontok** fület. A végpontok listája az Office 365 test URL-címeit és a Dynamics 365 teszt URL-címeket tartalmazza, név szerint csoportosítva. Ezen végpontok mellett olyan végpontot is kiválaszthat, amelyet más tesztelési csoportokban hoztak létre ugyanabban a kapcsolódási figyelőben. 
    
        Új végpont hozzáadásához kattintson a jobb felső sarokban található **+ végpontok**elemre. Ezután adja meg a végpont nevét és URL-címét, az IP-címet vagy a teljes tartománynevet.

       ![Képernyőfelvétel: a végpontok célhelyként való hozzáadásának helye a kapcsolódási figyelőben](./media/connection-monitor-2-preview/add-endpoints.png)

    * A kiválasztott Azure-beli virtuális gépek és végpontok áttekintéséhez válassza a **felülvizsgálat** lapot.
    * Amikor befejezte a célhelyek kiválasztását, válassza a **kész**lehetőséget.

* **Tesztelési konfigurációk** – tesztelési konfigurációkat rendelhet a tesztelési csoportokhoz. A Azure Portal tesztelési csoportonként csak egy tesztelési konfigurációt engedélyez, de a ARMClient használatával további műveleteket is felvehet.

    * **Név** – a teszt konfigurációjának neve.
    * **Protokoll** – válassza a TCP, az ICMP vagy a http lehetőséget. A HTTP HTTPS-re való módosításához válassza a **http** protokollt, és válassza a **443** portot.
        * **Hálózati teszt konfigurációjának létrehozása** – ez a jelölőnégyzet csak akkor jelenik meg, ha a **protokoll** mezőben a **http** lehetőséget választja. Jelölje be ezt a jelölőnégyzetet, ha olyan tesztelési konfigurációt szeretne létrehozni, amely ugyanazokat a forrásokat és célhelyeket használja, amelyeket a konfigurációban máshol adott meg. Az újonnan létrehozott teszt-konfiguráció neve `<the name of your test configuration>_networkTestConfig`.
        * **Traceroute letiltása** – ez a mező olyan tesztelési csoportokra vonatkozik, amelyek TCP vagy ICMP protokollal rendelkeznek. Jelölje be ezt a jelölőnégyzetet, ha le szeretné állítani, hogy a források felfedjék a topológiát és a hop-by-hop RTT.
    * **Célport** – a mező testreszabható a választott célport használatával.
    * **Tesztelési gyakoriság** – ezzel a mezővel kiválaszthatja, hogy a források milyen gyakran fognak pingelni a protokollon és a megadott porton. 30 másodpercet, 1 percet, 5 percet, 15 percet vagy 30 percet is kiválaszthat. A források a választott érték alapján próbálják meg a célokhoz való kapcsolódást.  Ha például 30 másodpercet választ, a források legalább egyszer ellenőrizhetik a kapcsolatot a célhoz, 30 másodpercen belül.
    * **Sikerességi küszöb** – a következő hálózati paraméterek küszöbértékeit állíthatja be:
       * Az **ellenőrzések sikertelenek** – megadhatja a sikertelen ellenőrzések százalékos arányát, ha a források a megadott feltételek használatával ellenőrzik a csatlakozást a célhelyekhez. A TCP vagy az ICMP protokoll esetében a sikertelen ellenőrzések százalékaránya a csomagok elvesztésének százalékában is egyenlő lehet. HTTP protokoll esetén ez a mező a nem válaszoló HTTP-kérelmek százalékos arányát jelöli.
       * **Oda-** és bekapcsolási idő – állítsa be a RTT ezredmásodpercben, hogy mennyi ideig tart a források kapcsolódása a célhoz a tesztelési konfiguráción keresztül.
    
       ![Képernyőkép a tesztelési konfiguráció beállításáról a kapcsolódási figyelőben](./media/connection-monitor-2-preview/add-test-config.png)

A tesztelési csoportba felvett összes forrás, cél és tesztelési konfiguráció az egyes tesztekre bontva kerül. Íme egy példa arra, hogyan oszlanak meg a források és a célhelyek:

* Tesztelési Csoport: TG1
* Források: 3 (A, B, C)
* Célhelyek: 2 (D, E)
* Tesztelési konfigurációk: 2 (config 1, 2. konfiguráció)
* Létrehozott tesztek összesen: 12

| Teszt száma | Forrás | Cél | Konfiguráció tesztelése |
| --- | --- | --- | --- |
| 1 | A | D | 1\. konfiguráció |
| 2 | A | D | 2\. konfiguráció |
| 3 | A | E | 1\. konfiguráció |
| 4 | A | E | 2\. konfiguráció |
| 5 | B | D | 1\. konfiguráció |
| 6 | B | D | 2\. konfiguráció |
| 7 | B | E | 1\. konfiguráció |
| 8 | B | E | 2\. konfiguráció |
| 9 | C | D | 1\. konfiguráció |
| 10 | C | D | 2\. konfiguráció |
| 11 | C | E | 1\. konfiguráció |
| 12 | C | E | 2\. konfiguráció |

### <a name="scale-limits"></a>Skálázási korlátok

A hálózati figyelők a következő skálázási korlátokkal rendelkeznek:

* Az előfizetések maximális száma régiónként: 100
* Tesztelési csoportok maximális száma kapcsolatonként: 20
* Maximális források és célhelyek kapcsolatonként: 100
* A tesztelési konfigurációk maximális száma kapcsolatonként: 
    * 20 ARMClient keresztül
    * 2 a Azure Portal keresztül

## <a name="analyze-monitoring-data-and-set-alerts"></a>Figyelési adatelemzés és riasztások beállítása

A kapcsolati figyelő létrehozása után a források a tesztelési konfiguráció alapján ellenőrizhetik a helyekhez való kapcsolódást.

### <a name="checks-in-a-test"></a>Tesztek ellenőrzése

A tesztelési konfigurációban kiválasztott protokoll alapján a kapcsolat figyelője (előzetes verzió) a forrás-cél párokhoz tartozó ellenőrzések sorozatát futtatja. Az ellenőrzések a választott tesztelési gyakoriságnak megfelelően futnak.

Ha HTTP-t használ, a szolgáltatás kiszámítja a válasz kódját visszaadó HTTP-válaszok számát. Az eredmény meghatározza a sikertelen ellenőrzések százalékos arányát. A RTT kiszámításához a szolgáltatás a HTTP-hívás és a válasz közötti időt méri.

Ha a TCP vagy az ICMP protokollt használja, a szolgáltatás kiszámítja a csomagok elvesztésének százalékos arányát a sikertelen ellenőrzések százalékos arányának megállapításához. A RTT kiszámításához a szolgáltatás megméri az elküldött csomagok nyugtájának (ACK) fogadására tett időt. Ha engedélyezte a traceroute-adatelemzést a hálózati tesztek számára, a helyszíni hálózatra vonatkozó ugrások közötti adatvesztést és késést láthatja.

### <a name="states-of-a-test"></a>Teszt állapota

A csekkek által visszaadott adathalmazok alapján a tesztek a következő állapotokkal rendelkezhetnek:

* **Pass** – a sikertelen ellenőrzések és RTT százalékos arányának tényleges értékei a megadott küszöbértékeken belül vannak.
* **Fail (sikertelen** ) – a sikertelen ellenőrzések százalékos arányának tényleges értékei, vagy a RTT túllépte a megadott küszöbértéket. Ha nem ad meg küszöbértéket, akkor a teszt meghiúsul, ha a sikertelen ellenőrzések százalékaránya 100.
* **Figyelmeztetés** – a sikertelen ellenőrzések százalékarányához nem adtak meg feltételeket. A megadott feltételek hiányában a Csatlakozáskezelő (előzetes verzió) automatikusan hozzárendel egy küszöbértéket. Ha túllépi a küszöbértéket, a teszt állapota figyelmeztetésre változik.

### <a name="data-collection-analysis-and-alerts"></a>Adatgyűjtés, elemzés és riasztások

A Csatlakozáskezelő (előzetes verzió) által gyűjtött adatokat a Log Analytics munkaterületen tárolja a rendszer. Ezt a munkaterületet a kapcsolódási figyelő létrehozásakor állíthatja be. 

A figyelési adatok Azure Monitor Metrikákban is elérhetők. A figyelési adatait a kívánt ideig a Log Analytics használatával is megőrizheti. Alapértelmezés szerint a Azure Monitor csak 30 napig tárolja a metrikákat. 

[Metrikus riasztásokat is beállíthat az adatokra vonatkozóan](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Irányítópultok figyelése

A figyelési irányítópultokon megtekintheti az előfizetések, a régiók, az időbélyegek, a források és a célhelyek számára elérhető kapcsolati figyelők listáját.

Ha Network Watcherról nyit meg a kapcsolódási figyelőt (előzetes verzió), az adatok az alábbiak szerint jeleníthetők meg:

* **Csatlakozáskezelő** – az előfizetésekhez, a régiókhoz, az időbélyegekhez, a forrásokhoz és a célhelyekhez létrehozott összes összekötő-figyelő listája. Ez a nézet az alapértelmezett.
* **Tesztelési csoportok** – az előfizetésekhez, a régiókhoz, az időbélyegekhez, a forrásokhoz és a célhelyekhez létrehozott összes tesztelési csoport listája. Ezeket a tesztelési csoportokat nem a kapcsolatok figyelői szűrik.
* **Test** – az előfizetésekhez, a régiókhoz, az időbélyegekhez, a forrásokhoz és a célhelyekhez futtatott tesztek listája. Ezek a tesztek nem a kapcsolatok figyelője vagy a tesztelési csoportok szerint vannak szűrve.

Az alábbi ábrán a három adatnézetet az 1. nyíl jelzi.

Az irányítópulton kibonthatja az egyes kapcsolati figyelőket a tesztelési csoportok megjelenítéséhez. Ezután az egyes tesztelési csoportok kibontásával megtekintheti a rajta futó teszteket. 

A listát a következő alapján szűrheti:

* **Legfelső szintű szűrők** – válassza az előfizetések, a régiók, az időbélyegző-források és a célhelyek lehetőséget. Lásd a 2. négyzetet az alábbi képen.
* **Állapot-alapú szűrők** – a Csatlakozáskezelő, a test Group vagy a test állapota alapján szűrhet. Lásd az alábbi képen látható 3. nyilat.
* **Egyéni szűrők** – válassza az **összes kijelölése** lehetőséget egy általános keresés végrehajtásához. Egy adott entitás általi kereséshez válassza ki a elemet a legördülő listából. Lásd az alábbi képen látható 4. nyilat.

![A kapcsolódási figyelők, a tesztelési csoportok és a tesztek nézeteinek szűrését bemutató képernyőkép (előzetes verzió)](./media/connection-monitor-2-preview/cm-view.png)

Ha például meg szeretné tekinteni az összes tesztet a 10.192.64.56 (előzetes verzió), ahol a forrás IP-címe a következő:
1. Módosítsa a nézetet a **tesztre**.
1. A Keresés mezőbe írja be a következőt: *10.192.64.56*
1. A legördülő listában válassza a **források**elemet.

Csak a sikertelen tesztek megjelenítése a 10.192.64.56 (előzetes verzió), ahol a forrás IP-címe a következő:
1. Módosítsa a nézetet a **tesztre**.
1. Az állapot-alapú szűrő esetében válassza a **sikertelen**lehetőséget.
1. A Keresés mezőbe írja be a következőt: *10.192.64.56*
1. A legördülő listában válassza a **források**elemet.

Csak a sikertelen tesztek megjelenítése a outlook.office365.com (előzetes verzió), ahol a cél a következő:
1. Váltás a nézetre a **teszteléshez**.
1. Az állapot-alapú szűrő esetében válassza a **sikertelen**lehetőséget.
1. A Keresés mezőbe írja be a *Outlook.office365.com*
1. A legördülő listában válassza a **Célhelyek**lehetőséget.

   ![Képernyőfelvétel: a szűrt nézetet megjelenítő nézet, amely csak a Outlook.Office365.com célhelyének sikertelen teszteit jeleníti meg](./media/connection-monitor-2-preview/tests-view.png)

A RTT trendjeinek és a sikertelen ellenőrzések százalékos arányának megtekintéséhez:
1. Válassza ki a vizsgálni kívánt kapcsolódási figyelőt. Alapértelmezés szerint a figyelési adatcsoportot a rendszer a test Group alapján rendezi.

   ![A Csatlakozáskezelő metrikáit bemutató képernyőkép, amelyet a test Group megjelenít](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Válassza ki a vizsgálni kívánt tesztelési csoportot.

   ![A tesztelési csoport kiválasztásának helyét bemutató képernyőkép](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    A tesztelési csoport első öt sikertelen tesztjét láthatja a sikertelen ellenőrzések RTT vagy százaléka alapján. Minden tesztnél megjelenik a RTT és a trend sorok a sikertelen ellenőrzések százalékos arányához.
1. Válasszon ki egy tesztet a listából, vagy válasszon egy másik tesztet a vizsgálathoz. Az időintervallum és a sikertelen ellenőrzések százalékos aránya a küszöbértéket és a tényleges értékeket jeleníti meg. A RTT a küszöbérték, az átlag, a minimum és a maximum értékek láthatók.

   ![A teszt eredményeit bemutató képernyőkép a RTT és a sikertelen ellenőrzések százalékos arányáról](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Módosítsa az időintervallumot a további adatértékek megtekintéséhez.
1. A nézet módosításával megtekintheti a forrásokat, a célhelyeket és a tesztelési konfigurációkat. 
1. Válasszon egy forrást a sikertelen tesztek alapján, és vizsgálja meg az első öt sikertelen tesztet. Válassza például a **megtekintés** > **források** alapján lehetőséget, és **tekintse meg** > **célhely** alapján, hogy vizsgálja meg a kapcsolódó teszteket a kapcsolódási figyelőben.

   ![Képernyőfelvétel: az első öt sikertelen teszt teljesítmény-mérőszámai](./media/connection-monitor-2-preview/cm-drill-select-source.png)

A RTT trendjeinek és a tesztelési csoport sikertelen ellenőrzésének százalékos arányának megtekintéséhez:

1. Válassza ki a vizsgálni kívánt tesztelési csoportot. 

    Alapértelmezés szerint a figyelési adatforrások, a célhelyek és a tesztelési konfigurációk (tesztek) szerint vannak rendezve. Később megváltoztathatja a nézetet a tesztelési csoportokból a forrásokra, a célhelyekre vagy a tesztelési konfigurációkra. Ezután válasszon ki egy entitást az első öt sikertelen teszt kivizsgálásához. Például módosítsa a nézetet a forrásokra és a célhelyekre, hogy kivizsgálja a megfelelő teszteket a kiválasztott figyelőben.
1. Válassza ki a vizsgálni kívánt tesztet.

   ![A teszt kiválasztásának helyét bemutató képernyőkép](./media/connection-monitor-2-preview/tg-drill.png)

    Az adott időintervallumban és a sikertelen ellenőrzések százalékos arányában a küszöbértékek és a tényleges értékek láthatók. A RTT esetében a küszöbérték, az átlag, a minimum és a maximum értékek láthatók. A kiválasztott teszthez kilőtt riasztásokat is láthat.
1. Módosítsa az időintervallumot a további adatértékek megtekintéséhez.

A RTT trendjeinek és a tesztek sikertelen ellenőrzésének százalékos arányának megtekintéséhez:
1. Válassza ki a vizsgálni kívánt forrás-, cél-és tesztelési konfigurációt.

    Az adott időintervallumban és a sikertelen ellenőrzések százalékos arányában a küszöbértékek és a tényleges értékek láthatók. A RTT esetében a küszöbérték, az átlag, a minimum és a maximum értékek láthatók. A kiválasztott teszthez kilőtt riasztásokat is láthat.

   ![A teszt mérőszámait bemutató képernyőkép](./media/connection-monitor-2-preview/test-drill.png)

1. A hálózati topológia megtekintéséhez válassza a **topológia**lehetőséget.

   ![A hálózati topológia lapot ábrázoló képernyőfelvétel](./media/connection-monitor-2-preview/test-topo.png)

1. Az azonosított problémák megjelenítéséhez a topológiában válassza ki az ugrást az elérési úton. (Ezek a komló Azure-erőforrások.) Ez a funkció jelenleg nem érhető el a helyszíni hálózatokhoz.

   ![A topológia lapon a kiválasztott ugrási hivatkozásra mutató képernyőkép](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Lekérdezések naplózása Log Analytics

A Log Analytics használatával egyéni nézeteket hozhat létre a figyelési adatairól. A felhasználói felület által megjelenített összes adatok forrása Log Analytics. Interaktív módon elemezheti az adattárat. A Agent Health vagy más, Log Analyticson alapuló megoldások adatainak összekapcsolása. Exportálja az Excelbe vagy Power BIba, vagy hozzon létre egy megosztható hivatkozást.

#### <a name="metrics-in-azure-monitor"></a>Metrikák az Azure Monitorban

A Csatlakozáskezelő (előzetes verzió) felhasználói felülete előtt létrehozott AverageRoundtripMs a következő négy metrika érhető el:% Probes sikertelen,, ChecksFailedPercent (előzetes) és RoundTripTimeMs (előzetes verzió). A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott hálózati figyelők esetében az adatok csak a *(előzetes)* címkével megjelölt mérőszámokhoz érhetők el.

![A Csatlakozáskezelő metrikáit bemutató képernyőkép (előzetes verzió)](./media/connection-monitor-2-preview/monitor-metrics.png)

Metrikák használata esetén állítsa be az erőforrástípust a Microsoft. Network/networkWatchers/connectionMonitors

| Metrika | Megjelenített név | Unit (Egység) | Összesítés típusa | Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | %-Os mintavétel sikertelen | Százalék | Átlag | A kapcsolat figyelési mintavételének százalékos aránya meghiúsult. | Nincsenek méretek |
| AverageRoundtripMs | Átlagos menetidő (MS) | Ezredmásodperc | Átlag | A forrás és a cél között eljuttatott kapcsolat-figyelési mintavételek átlagos hálózati RTT. |             Nincsenek méretek |
| ChecksFailedPercent (előzetes verzió) | %-Os ellenőrzés sikertelen (előzetes verzió) | Százalék | Átlag | A teszt sikertelen ellenőrzésének százalékos aránya. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Sourceresourceid azonosítónak <br>forrás típusa <br>Protokoll <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Régió |
| RoundTripTimeMs (előzetes verzió) | Oda-és visszaút időpontja (MS) (előzetes verzió) | Ezredmásodperc | Átlag | A forrás és a cél között továbbított ellenőrzések RTT. Ez az érték nem átlag. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Sourceresourceid azonosítónak <br>forrás típusa <br>Protokoll <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Régió |

#### <a name="metric-alerts-in-azure-monitor"></a>Metrikus riasztások Azure Monitor

Riasztás létrehozása Azure Monitorban:

1. Válassza ki a Csatlakozáskezelő (előzetes verzió) eszközben létrehozott Csatlakozáskezelő-erőforrást.
1. Győződjön meg arról, hogy a **metrika** a Csatlakozáskezelő jel típusaként jelenik meg.
1. A **feltétel hozzáadása**területen a **jel neveként**válassza a **ChecksFailedPercent (előzetes verzió)** vagy a **RoundTripTimeMs (előzetes verzió)** lehetőséget.
1. A **jel típusa**beállításnál válassza a **metrikák**lehetőséget. Válassza például a **ChecksFailedPercent (előzetes verzió)** lehetőséget.
1. Megjelenik a metrika összes dimenziója. Válassza ki a dimenzió nevét és a dimenzió értékét. Válassza például a **forrás címe** lehetőséget, majd adja meg a Csatlakozáskezelő bármelyik FORRÁSÁNAK IP-címét.
1. A **riasztási logikában**adja meg a következő adatokat:
   * **Feltétel típusa**: **statikus**.
   * **Feltétel** és **küszöbérték**.
   * **Összesítés részletessége és a kiértékelés gyakorisága: a**Csatlakozáskezelő (előzetes verzió) minden percben frissíti az adatokat.
1. A **műveletek**területen válassza ki a műveleti csoportot.
1. Adja meg a riasztás részleteit.
1. Hozzon létre egy riasztási szabályt.

   ![Képernyőfelvétel a szabály létrehozása területén Azure Monitor; A "forrás címe" és a "forrás végpont neve" ki van emelve](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>A hálózatban felmerülő problémák diagnosztizálása

A Csatlakozáskezelő (előzetes verzió) segítségével diagnosztizálhatja a kapcsolatok figyelője és a hálózat hibáit. A hibrid hálózattal kapcsolatos problémákat a korábban telepített Log Analytics ügynökök észlelik. Az Azure-beli problémákat a Network Watcher bővítmény észleli. 

A hálózati topológiában megtekintheti az Azure-hálózatban felmerülő problémákat.

Azokon a hálózatokon, amelyek forrásai a helyszíni virtuális gépek, a következő problémák észlelhetők:

* A kérelem időkorlátja lejárt.
* A végpontot nem oldja fel a DNS – ideiglenes vagy állandó. Érvénytelen URL-cím.
* Nem található gazdagép.
* A forrás nem tud kapcsolódni a célhelyhez. A cél nem érhető el az ICMP protokollon keresztül.
* Tanúsítványokkal kapcsolatos problémák: 
    * Az ügynök hitelesítéséhez szükséges ügyféltanúsítvány. 
    * A tanúsítvány-áthelyezési lista nem érhető el. 
    * A végpont állomásneve nem felel meg a tanúsítvány tulajdonosának vagy a tulajdonos alternatív nevének. 
    * A főtanúsítvány hiányzik a forrás helyi számítógépének megbízható hitelesítésszolgáltatók tárolójából. 
    * Az SSL-tanúsítvány lejárt, érvénytelen, visszavont vagy nem kompatibilis.

Az Azure-beli virtuális gépeket forrásként szolgáló hálózatok esetében a következő problémák észlelhetők:

* Ügynökkel kapcsolatos problémák:
    * Az ügynök leállt.
    * Sikertelen DNS-feloldás.
    * Nincs figyelő alkalmazás vagy figyelő a célport számára.
    * A szoftvercsatorna nem nyitható meg.
* VIRTUÁLIS gépek állapotával kapcsolatos problémák: 
    * Indítás
    * Leállítás
    * Leállítva
    * Felszabadítás
    * Felszabadítva
    * Újraindítás
    * Nincs lefoglalva
* Hiányzik az ARP-tábla bejegyzése.
* A forgalmat a helyi tűzfal problémái vagy a NSG-szabályok miatt blokkolta a rendszer.
* Virtuális hálózati átjáróval kapcsolatos problémák: 
    * Hiányzó útvonalak.
    * A két átjáró közötti alagút le van választva vagy hiányzik.
    * A második átjáró nem található az alagútban.
    * Nem található egyenrangú információ.
* Az útvonal hiányzik a Microsoft Edge-ben.
* A forgalom a rendszerútvonalak vagy a UDR miatt leállt.
* A BGP nincs engedélyezve az átjáró-kapcsolatban.
* A DIP-mintavétel le van töltve a terheléselosztó számára.

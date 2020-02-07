---
title: Csatlakozáskezelő (előzetes verzió) | Microsoft Docs
description: Ismerje meg, hogyan figyelheti a hálózati kommunikációt egy elosztott környezetben a Csatlakozáskezelő (előzetes verzió) használatával
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 5dc705fbd17a12ee001e1e8de15b49e841f08b81
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049282"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>Egyesített kapcsolat figyelése a kapcsolat figyelője (előzetes verzió)

A kapcsolódási figyelő (előzetes verzió) egységes, végpontok közötti kapcsolat-figyelési képességeket biztosít az Azure Network Watcher a hibrid és az Azure-beli Felhőbeli üzembe helyezéshez. Az Azure Network Watcher eszközeivel figyelheti, diagnosztizálhatja és tekintheti meg az Azure-beli üzemelő példányok kapcsolattal kapcsolatos metrikáit.

Legfontosabb használati esetek:

- Rendelkezik egy előtér-webkiszolgáló virtuális géppel, amely egy többrétegű alkalmazásban található adatbázis-kiszolgáló virtuális géppel kommunikál. Szeretné megtekinteni a két virtuális gép közötti hálózati kapcsolatot.
- Azt szeretné, hogy az USA keleti régiójában lévő virtuális gépek Pingelje a virtuális gépeket az USA középső régiójában, és hasonlítsa össze a régiók közötti
- Több helyi Office-hellyel rendelkezik, mint például Seattle. Csatlakozás az Office 365 URL-címekhez. Össze szeretné hasonlítani a felhasználók által tapasztalt késéseket a Seattle és a Ashburn Office 365 URL-címeivel.
- Egy olyan hibrid alkalmazásra van beállítva, amelyhez kapcsolódnia kell egy Azure Storage-végponthoz. Szeretné összehasonlítani a késést egy helyszíni hely és az Azure-alkalmazás között ugyanahhoz az Azure Storage-végponthoz való csatlakozással.
- Szeretné megtekinteni a Felhőbeli alkalmazást üzemeltető Azure-beli virtuális gépek kapcsolódását a helyszíni telepítésekhez.

Ebben az előzetes verzióban a megoldás két fő funkció közül a legjobbat egyesíti: Network Watcher a [kapcsolat figyelője](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) és a Network Performance monitor (NPM) [szolgáltatás kapcsolódási figyelője](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity).

Kiemeli

* Egységes, intuitív élmény az Azure-hoz és a hibrid monitorozási igényekhez
* Régiók közötti kapcsolat, több munkaterület közötti kapcsolatok figyelése
* Nagyobb Szondázási gyakoriságok és a hálózati teljesítmény jobb láthatósága
* Gyorsabb riasztás a hibrid üzembe helyezésekhez
* HTTP-, TCP-és ICMP-alapú kapcsolat-ellenőrzések támogatása
* Metrikák és Log Analytics támogatás az Azure és a nem Azure-beli tesztelési beállításokhoz

![Kapcsolatfigyelő](./media/connection-monitor-2-preview/hero-graphic.png)

Kövesse az alábbi lépéseket a figyelés megkezdéséhez a Csatlakozáskezelő használatával (előzetes verzió)

## <a name="step-1-install-monitoring-agents"></a>1\. lépés: figyelési ügynökök telepítése

A kapcsolati figyelő egyszerűsített végrehajtható fájlok használatával futtatja a kapcsolati ellenőrzéseket.  Az Azure és a helyszíni környezetek közötti kapcsolati ellenőrzéseket is támogatjuk. A használni kívánt végrehajtható fájl attól függ, hogy a virtuális gép az Azure-ban vagy a helyszínen van-e tárolva.

### <a name="agents-for-azure-virtual-machines"></a>Az Azure Virtual Machines-ügynökök

Ahhoz, hogy a Csatlakozáskezelő felismerje az Azure-beli virtuális gépeket a monitorozás forrásaként, telepítenie kell a Network Watcher Agent virtuálisgép-bővítményt (más néven Network Watcher bővítményt). A Network Watcher ügynök bővítménye az Azure-beli virtuális gépek végpontok közötti figyelésének és egyéb speciális funkcióinak indítására vonatkozó követelmény. [Létrehozhat egy virtuális gépet, és telepítheti az Network Watcher-bővítményt](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[is.](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)  A [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) és a [Windows rendszerhez](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) készült Network Watcher-bővítményt külön is telepítheti, konfigurálhatja és elháríthatja.

Ha a NSG vagy a tűzfalszabályok blokkolja a forrás és a cél közötti kommunikációt, a kapcsolati figyelő felismeri a problémát, és diagnosztikai üzenetként jeleníti meg a topológiában. A kapcsolat figyelésének engedélyezéséhez győződjön meg arról, hogy a NSG és a tűzfalszabályok engedélyezik a TCP-vagy ICMP-csomagokat a forrás és a cél között.

### <a name="agents-for-on-premise-machines"></a>Helyszíni gépek ügynökei

Ahhoz, hogy a kapcsolatkezelő felismerje a helyszíni gépeket a figyeléshez, telepítenie kell a Log Analytics-ügynököt a gépekre, és engedélyeznie kell a hálózati teljesítmény figyelésére szolgáló megoldást. Ezek az ügynökök Log Analytics munkaterületekhez kapcsolódnak, és a figyelés megkezdése előtt be kell állítani a munkaterület-azonosítót és az elsődleges kulcsot.

A Windows rendszerű gépek Log Analytics ügynökének telepítéséhez kövesse a [hivatkozásban](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows) említett utasításokat

Győződjön meg arról, hogy a célhely elérhető, ha vannak tűzfalak vagy virtuális hálózati berendezések (NVA) az elérési útban.

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>2\. lépés: az előfizetéshez tartozó Network Watcher engedélyezése

A VNET összes előfizetése engedélyezve van a Network Watcher. Amikor létrehoz egy virtuális hálózatot az előfizetésében, Network Watcher automatikusan engedélyezve lesz az adott Virtual Network régiójában és előfizetésében. A Network Watcher automatikus engedélyezéséhez nincs hatással az erőforrásokra vagy a kapcsolódó díjakra. Győződjön meg arról, hogy a Network Watcher nincs explicit módon letiltva az előfizetésen. További információ: [Network Watcher engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="step-3-create-connection-monitor"></a>3\. lépés: a kapcsolatkezelő létrehozása 

A _kapcsolati figyelő_ rendszeres időközönként figyeli a kommunikációt, és tájékoztatja a forrás-és a cél végpontok közötti elérhetőségi, késési és hálózati topológiai változásokról. A források lehetnek olyan Azure-beli virtuális gépek vagy helyszíni számítógépek, amelyeken telepítve van egy figyelési ügynök. A célként megadott végpontok lehetnek Office 365 URL-címek, Dynamics 365 URL-címek, egyéni URL-címek, Azure-beli virtuális gépek erőforrás-azonosítói, IPv4, IPv6, FQDN vagy bármely tartománynév.

### <a name="accessing-connection-monitor-preview"></a>A Csatlakozáskezelő elérése (előzetes verzió)

1. A Azure Portal kezdőlapján keresse fel a Network Watcher
2. A Network Watcher bal oldali paneljének figyelés szakaszában kattintson a "kapcsolatbeállítások (előzetes verzió)" fülre.
3. A Csatlakozáskezelő (előzetes verzió) használatával létrehozott összes figyelőt láthatja. A rendszer a Csatlakozáskezelő lap klasszikus felhasználói felületének használatával létrehozott összes figyelőt a Csatlakozáskezelő lapon fogja látni.

    ![Figyelő létrehozása](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Csatlakozáskezelő létrehozása

A kapcsolat figyelője (előzetes verzió) használatával létrehozott csatlakozási figyelők lehetővé teszik a helyszíni és az Azure-beli virtuális gépek forrásként történő hozzáadását és a végpontokhoz való kapcsolódás figyelését, amely az Azure-t vagy bármely más URL-címet vagy IP-címet képes biztosítani.

A következő entitások szerepelnek a Csatlakozáskezelőben:

* Csatlakozáskezelő erőforrás – régió-specifikus Azure-erőforrás. Az alább említett entitások a kapcsolatkezelő erőforrásának tulajdonságai.
* Végpontok – a kapcsolati ellenőrzésekben részt vevő összes forrást és célhelyet végpontoknak nevezzük. Példa végpontra – Azure-beli virtuális gépek, helyszíni ügynökök, URL-címek és IP-címek
* Konfiguráció tesztelése – minden teszt-konfiguráció protokoll-specifikus. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztelés gyakoriságát és az egyéb paramétereket.
* Test Group (tesztelési csoport) – minden tesztelési csoport forrás-és cél-végpontokat, valamint tesztelési konfigurációkat tartalmaz. Minden egyes Csatlakozáskezelő több tesztelési csoportot is tartalmazhat
* Test – a forrás-végpont, a cél végpont és a tesztelési konfiguráció kombinációja egy tesztet hajt végre. A teszt az a legalacsonyabb szint, amelyen a figyelési adatellenőrzés (sikertelen volt a (z)% és a RTT)

 ![Figyelő létrehozása](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>A portálról

A kapcsolódási figyelő létrehozásához kövesse az alábbi lépéseket:

1. A Csatlakozáskezelő (előzetes verzió) Irányítópultján kattintson a "létrehozás" gombra a bal felső sarokban.
2. Az alapszintű lapon adja meg a kapcsolatok figyelője adatait
   1. Csatlakozáskezelő neve – a figyelő neve. Az Azure-erőforrások szabványos elnevezési szabályai itt érvényesek.
   2. Előfizetés – válasszon egy előfizetést a kapcsolatok figyelője számára.
   3. Régió – válasszon egy régiót a Csatlakozáskezelő-erőforráshoz. Csak az ebben a régióban létrehozott forrás virtuális gépeket lehet kiválasztani.
   4. Munkaterület konfigurációja – az alapértelmezett jelölőnégyzetre kattintva a Csatlakozáskezelő által létrehozott alapértelmezett munkaterületet használhatja a figyelési adatai tárolásához. Egyéni munkaterület kiválasztásához törölje a jelet a jelölőnégyzetből. Válassza ki az előfizetést és régiót a munkaterület kiválasztásához, amely a figyelési adatait fogja tárolni.
   5. Tesztelési csoportok hozzáadásához kattintson a "Next: test groups" elemre.

      ![Figyelő létrehozása](./media/connection-monitor-2-preview/create-cm-basics.png)

3. A test groups (tesztelési csoportok) lapon kattintson a "+ test Group" elemre egy tesztelési csoport hozzáadásához. Tesztelési csoportok hozzáadásához használja _a kapcsolódási figyelőben a tesztkörnyezet létrehozását_ . A kapcsolódási figyelő áttekintéséhez kattintson a "felülvizsgálat + létrehozás" gombra.

   ![Figyelő létrehozása](./media/connection-monitor-2-preview/create-tg.png)

4. A "felülvizsgálat + létrehozás" lapon tekintse át az alapszintű információkat és a tesztelési csoportokat a Csatlakozáskezelő létrehozása előtt. A kapcsolódási figyelő szerkesztése a "felülvizsgálat + létrehozás" nézetből:
   1. Az alapszintű adatok szerkesztéséhez használja a ceruza ikont a 2. képen szereplő 1. mezőben megadott módon.
   2. Az egyes tesztelési csoportok szerkesztéséhez kattintson arra a tesztelési csoportra, amelyet szerkeszteni szeretne, és nyissa meg a tesztelési csoportot szerkesztési módban.
   3. Az aktuális ár/hó az előzetes verzió során felmerülő költségeket jelzi. Jelenleg nem számítunk fel díjat a Csatlakozáskezelő használatához, így ez az oszlop nullát fog mutatni. Tényleges költség/hónap jelezte, hogy az általános elérhetőség után fizetendő díj. Ne feledje, hogy a log Analytics betöltési díjait a rendszer még az előzetes verzióban is alkalmazza.

5. A "felülvizsgálat + létrehozás" lapon kattintson a "létrehozás" gombra a kapcsolódási figyelő létrehozásához.

   ![Figyelő létrehozása](./media/connection-monitor-2-preview/review-create-cm.png)

6.  A Csatlakozáskezelő (előzetes verzió) létrehozza a figyelő erőforrást a háttérben.

#### <a name="from-armclient"></a>Armclient

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

address: '\&lt;FQDN of your on-premise agent'

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

Üzembe helyezési parancs:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Tesztelési csoportok létrehozása a Csatlakozáskezelőben

A kapcsolati figyelő mindegyik tesztelési csoportja olyan forrásokat és célhelyeket tartalmaz, amelyek az ellenőrzések hálózati paramétereinek tesztelését végezik, és RTT a tesztelési konfigurációkon.

#### <a name="from-portal"></a>A portálról

Ha egy figyelőhöz szeretne létrehozni egy tesztelési csoportot, akkor a lent említett mezők értékét kell megadnia:

1. Tesztelési csoport letiltása – a mező ellenőrzése letiltja a tesztelési csoportban megadott összes forrás és cél figyelését. Ez a beállítás alapértelmezés szerint nincs bejelölve.
2. Név – a tesztelési csoport neve
3. Források – az Azure-beli és a helyszíni gépeket is megadhatja forrásként, ha az ügynökök telepítve vannak bennük. A forrásra vonatkozó ügynök telepítéséhez tekintse meg az 1. lépést.
   1. Az Azure-ügynökök kiválasztásához kattintson az "Azure-ügynökök" fülre. Csak azok a virtuális gépek jelennek meg, amelyek a kapcsolódási figyelő létrehozásakor megadott régióhoz vannak kötve. A virtuális gépek alapértelmezés szerint a hozzájuk tartozó előfizetésbe vannak csoportosítva, és a csoportok össze vannak csukva. Az előfizetések szintjéről a hierarchiában lévő többi szintre is lebonthatja a részletezést:

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      A "Group By" mező értékét is megváltoztathatja a fa bármely más szintről való elindításához. Például: Group By – VNET megjeleníti a virtuális gépeket a virtuális hálózatok-\&gt; hierarchiában lévő ügynökökkel. Alhálózatok –\&gt; Ügynökökkel rendelkező virtuális gépek.

      ![Források hozzáadása](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Kattintson a "nem – Azure-ügynökök" fülre a helyi ügynökök kiválasztásához. Alapértelmezés szerint az ügynökök az adott régióban munkaterületekre csoportosítva jelennek meg. Csak azok a munkaterületek jelennek meg, amelyeken Network Performance Monitor megoldás van konfigurálva. Adja hozzá a Network Performance Monitor megoldást a munkaterülethez az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Használhatja a [Solutions Gallery Azure monitor megoldások hozzáadása](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) című témakörben ismertetett eljárást is. Alapértelmezés szerint a Csatlakozáskezelő létrehozása nézet alapinformációk lapján kiválasztott régió jelenik meg. Módosíthatja a régiót, és az újonnan kiválasztott régióból származó ügynököket is választhat. Az alhálózatok csoportosításához a "Group By" mező értékét is módosíthatja.

      ![Nem Azure-források](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Kattintson a "felülvizsgálat" gombra a kiválasztott Azure-és nem Azure-ügynökök áttekintéséhez.

      ![Források áttekintése](./media/connection-monitor-2-preview/review-sources.png)

   4. Kattintson a kész gombra a források kiválasztásának befejezése után.

4. Célhelyek – az Azure-beli virtuális gépekhez vagy bármely végponthoz (nyilvános IP-címhez, URL-címekhez, FQDN) való csatlakozást megfigyelheti célhelyként megadva. Egyetlen tesztelési csoportban Azure-beli virtuális gépeket, O365 URL-címeket, D365 URL-címeket vagy egyéni végpontokat adhat hozzá.

   1. Kattintson az "Azure-beli virtuális gépek" fülre az Azure-beli virtuális gépek célhelyként való kiválasztásához. Alapértelmezés szerint az előfizetési hierarchiába csoportosított Azure-beli virtuális gépek ugyanabban a régióban találhatók, mint a Csatlakozáskezelő létrehozása nézet alapinformációk lapján. Módosíthatja a régiót, és az újonnan kiválasztott régióból is kiválaszthatja az Azure-beli virtuális gépeket. Az előfizetések szintjéről a hierarchiában lévő más szintekre, például az Azure-ügynökökre is lebonthatja a részletezést.

      ![Célhelyek hozzáadása](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![2\. célhely hozzáadása](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Kattintson a "végpontok" fülre az Azure-beli virtuális gépek célhelyként való kiválasztásához. A végpontok listája O365 és D365-teszt URL-címekkel lesz feltöltve, név szerint csoportosítva.  Egy másik tesztelési csoportban létrehozott végpontot is kiválaszthat ugyanabban a figyelőben. Új végpont hozzáadásához kattintson a képernyő jobb felső sarkában található "+ végpont" elemre, és adja meg a végpont URL-címét/IP-címét/FQDN-jét és nevét.

      ![Végpontok hozzáadása](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Kattintson a "felülvizsgálat" gombra a kiválasztott Azure-és nem Azure-ügynökök áttekintéséhez.
   4. Kattintson a kész gombra a források kiválasztásának befejezése után.

5. Tesztelési konfiguráció – tetszőleges számú tesztelési konfigurációt rendelhet egy adott tesztelési csoportban. A portál egy tesztelési csoportra korlátozza a tesztelési konfigurációt, de a Armclient használatával további műveleteket is felvehet.
   1. Név – a teszt konfigurációjának neve
   2. Protokoll – a TCP, az ICMP vagy a HTTP beállítás közül választhat. A HTTP HTTPS-re való módosításához válassza a HTTP as Protocol és a 443 as port lehetőséget.
   3. Hálózati teszt konfigurációjának létrehozása – Ez a jelölőnégyzet csak akkor jelenik meg, ha a protokoll mezőben a HTTP lehetőséget választja. Ha engedélyezi ezt a mezőt, a 3. és a 4. lépésben megadott forrás-és célhelyek használatával hozzon létre egy másik tesztelési konfigurációt a TCP/ICMP protokollal. Az újonnan létrehozott tesztelési konfiguráció neve "\&lt; név van megadva 5. a\&gt;\_networkTestConfig "
   4. Traceroute letiltása – ez a mező a TCP vagy ICMP protokollt használó tesztelési csoportok esetében alkalmazható lesz.  Ennek a mezőnek a bejelölésével leállíthatja a forrásokat a topológia és a hop-ugrások közötti időpontra.
   5. Célport – testreszabhatja ezt a mezőt úgy, hogy az Ön által választott cél portot helyezzen el.
   6. Tesztelési gyakoriság – ebben a mezőben határozható meg, hogy a források milyen gyakran fognak pingelni a protokollon és a fent megadott porton. 30 másodperc, 1 perc, 5 perc, 15 perc és 30 perc között lehet választani. A források a választott érték alapján próbálják meg a célokhoz való kapcsolódást.  Ha például 30 másodpercet választ, a források legalább egyszer, 30 másodperc alatt ellenőrizhetik a kapcsolatot a célhelykel.
   7. Állapot küszöbértékei – az alább említett hálózati paraméterek küszöbértékeit beállíthatja
      1. Az ellenőrzés sikertelen volt, mert a (z)% – az ellenőrzések százalékos aránya meghiúsult, amikor a források megkeresik a fent megadott feltételek alapján a célhelyet A TCP/ICMP protokoll esetében a (z)%-os sikertelen ellenőrzések a (z)%-os csomag elvesztésével egyenértékűek lehetnek. HTTP protokoll esetén ez a mező azoknak a http-kérelmeknek a számát jelöli, amelyek nem kaptak választ.
      2. RTT ezredmásodpercben – oda-és visszaúti idő ezredmásodpercben, amikor a források a fent megadott tesztelési konfiguráción keresztül csatlakoznak a célhoz.

      ![TG hozzáadása](./media/connection-monitor-2-preview/add-test-config.png)

Az egyes tesztelési csoportokhoz hozzáadott összes forrást és célhelyet az egyes tesztekre bontva lehet megadni. Például:

* Tesztelési Csoport: TG1
* Források: 3 (A, B, C)
* Célhelyek: 2 (D, E)
* Tesztelési konfiguráció: 2 (config 1, 2. konfiguráció)
* Létrehozott tesztek: összesen = 12

| **Teszt száma** | **Forrás** | **Cél** | **Teszt konfigurációjának neve** |
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

* Az előfizetések maximális száma/régiónként – 100
* A tesztelési csoportok maximális száma kapcsolatonként – 20
* A # forrás-és célhelyek maximális száma kapcsolatonként – 100
* A Armclient-n keresztüli kapcsolatonként a tesztelési konfigurációk maximális száma. 2 a portálon keresztül.

## <a name="step-4--data-analysis-and-alerts"></a>4\. lépés: az adatelemzés és a riasztások

A kapcsolati figyelő létrehozása után a források a megadott tesztelési konfiguráció alapján ellenőrizhetik a helyekhez való kapcsolódást.

### <a name="checks-in-a-test"></a>Tesztek ellenőrzése

A tesztelési konfigurációban a felhasználó által kiválasztott protokoll alapján a kapcsolat figyelője (előzetes verzió) egy sor ellenőrzést futtat a forrás célhelyen a kiválasztott tesztelési gyakorisággal.

Ha a HTTP van kiválasztva, a szolgáltatás kiszámítja, hogy a rendszer hány HTTP-választ adott vissza, és nem sikerült meghatározni az ellenőrzéseket (%).  A RTT kiszámításához a HTTP-hívás válaszának fogadására tett időt mérjük.

Ha a TCP vagy az ICMP lehetőséget választotta, a szolgáltatás kiszámítja a (z)%-os csomagot az ellenőrzések megállapításához. A RTT kiszámításához mérjük meg az elküldött csomagok NYUGTÁjának fogadásához szükséges időt. Ha engedélyezte a traceroute-adatelemzést a hálózati tesztek számára, a helyszíni hálózatra vonatkozóan megtekintheti az ugrások közötti adatvesztést és a késést.

### <a name="states-of-a-test"></a>Teszt állapota

A tesztek ellenőrzései által visszaadott adathalmazok alapján minden teszt után a következő állapotok szerepelhetnek:

* Pass = ha az ellenőrzések tényleges értékei sikertelenek (%), és a RTT a megadott küszöbértékeken belül vannak
* Fail = ha a csekkek tényleges értékei nem sikerültek% vagy a RTT több megadott küszöbértéket adott meg. Ha nem ad meg küszöbértéket, akkor a teszt sikertelen lesz, ha az ellenőrzések meghiúsultak:% = 100%
* Figyelmeztetés – ha a sikertelen ellenőrzések feltételei% nincs megadva. Ebben az esetben a kapcsolatkezelő (előzetes verzió) egy automatikus beállított feltételt használ küszöbértékként, és ha a teszt megszegése megszegi az állapotot, a "figyelmeztetés" értékre van állítva.

### <a name="data-collection-analysis-and-alerts"></a>Adatgyűjtés, elemzés és riasztások

A rendszer a Csatlakozáskezelő (előzetes verzió) által összegyűjtött összes adatokat a Log Analytics munkaterületen tárolja, amelyet a rendszer a Csatlakozáskezelő létrehozásakor konfigurált. A figyelési adatok Azure Monitor Metrikákban is elérhetők. A Log Analytics használatával a figyelési adatokat akár úgy is megtarthatja, hogy az adott állapotban maradjon, de a metrikák alapértelmezés szerint 30 napig Azure Monitor **.** Ezután mérőszám- [alapú riasztásokat állíthat be az adatokon](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Irányítópultok figyelése a Csatlakozáskezelő megoldásban

Megjelenik a kapcsolati figyelő azon listája, amelyhez hozzáférése van az előfizetések, a régiók, az időbélyeg, a forrás és a célhelyek adott kiválasztásához.

Ha Network Watcher szolgáltatásból navigál a kapcsolódási figyelőhöz (előzetes verzió), **megtekintheti**a következőt:

* Csatlakozáskezelő (alapértelmezett) – a kiválasztott előfizetésekhez, régióokhoz, időbélyegekhez, forrásokhoz és célhelyekhez létrehozott kapcsolatok összes figyelő listája
* Tesztelési csoportok – a kiválasztott előfizetésekhez, régióokhoz, időbélyeg-, forrás-és célhelyekhez létrehozott összes tesztelési csoport listája. Ezek a tesztelési csoportok nem szűrve vannak a kapcsolatok figyelője szolgáltatásban
* Tesztek – a kiválasztott előfizetésekhez, régiókhoz, időbélyegekhez, forrásokhoz és célhelyekhez futtatott tesztek listája. Ezeket a teszteket a rendszer nem szűri a kapcsolatok figyelője vagy a tesztelési csoportok között.

Az egyes kapcsolatok figyelőit kiterjesztheti a tesztelési csoportokba és az egyes tesztelési csoportba az irányítópulton futó különböző tesztekre. Az alábbi képen [1] van megjelölve.

A listát a következő alapján szűrheti:

* Legfelső szintű szűrők – előfizetések, régiók, időbélyeg-forrás és rendeltetési típusok. Az alábbi képen [2] jelöléssel van megjelölve.
* Állapot-alapú szűrők – második szintű szűrő a Csatlakozáskezelő/tesztelési csoport/tesztelés állapota alapján. Az alábbi képen [3] van megjelölve.
* Keresőmező – válassza az "összes" lehetőséget egy általános keresés végrehajtásához. Egy adott entitáson való kereséshez használja a legördülő listát a keresési eredmények szűkítéséhez. Az alábbi képen [4] jelöléssel van megjelölve.

![Tesztek szűrése](./media/connection-monitor-2-preview/cm-view.png)

Például:

1. Az összes olyan teszt megtekintése az összes (előzetes verzióban), ahol a Source IP = 10.192.64.56:
   1. Nézet módosítása a "tesztek" értékre
   2. Keresés iktatott = 10.192.64.56
   3. A "források" kiválasztásához használja az érték melletti legördülő menüt.
2. Ha csak a sikertelen teszteket szeretné kiszűrni az összes kapcsolódási figyelőben (előzetes verzió), ahol a forrás IP-címe = 10.192.64.56
   1. Nézet módosítása a "tesztek" értékre
   2. Az állapot-alapú szűrők közül válassza a "sikertelen" lehetőséget.
   3. Keresési mező = 10.192.64.56
   4. A "források" kiválasztásához használja az érték melletti legördülő menüt.
3. Csak a sikertelen tesztek szűrése az összes outlook.office365.com (előzetes verzió), ahol a cél
   1. Nézet módosítása a "tesztek" értékre
   2. Az állapot-alapú szűrők közül válassza a "sikertelen" lehetőséget.
   3. Keresési mező = outlook.office365.com
   4. Az érték melletti legördülő menüben válassza a "célhelyek" lehetőséget.

   ![Sikertelen tesztek](./media/connection-monitor-2-preview/tests-view.png)

A (z)% és a RTT sikertelen ellenőrzések trendjeinek megtekintéséhez:

1. Kapcsolatfigyelő
   1. Kattintson a vizsgálni kívánt Csatlakozáskezelő részletes adataira
   2. Alapértelmezés szerint a figyelési értékeket "tesztelési csoportok" nézetben tekintheti meg

      ![Metrikák megtekintése](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Válassza ki a vizsgálni kívánt tesztelési csoportot.

      ![Mérőszámok TG szerint](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Az ellenőrzések során az első 5 sikertelen teszt sikertelen volt% vagy RTT ezredmásodperc az előző lépésben kiválasztott tesztelési csoportra vonatkozóan. Minden tesztnél látni fogja a (z)% és a RTT MS
   5. Válasszon ki egy tesztet a fenti listából, vagy válasszon egy másik tesztet a részletek kivizsgálásához.
   6. A kiválasztott időintervallum esetében a (z)%-os ellenőrzésekhez nem sikerült a küszöbértéket és a tényleges értékeket látni. A RTT MS a küszöbértéket, az átlagot, a minimumot és a maximális értéket fogja látni.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Az időintervallum módosítása további adatértékek megtekintéséhez
  8. A b lépésben megváltoztathatja a nézetet, és megtekintheti forrás, célhely vagy tesztelési konfiguráció alapján. Ezután válasszon ki egy forrást a sikertelen tesztek alapján, és vizsgálja meg az 5 leggyakoribb sikertelen tesztet.  Például: válassza a nézet: források és célhelyek lehetőséget, hogy vizsgálja meg az adott kombináció között futó összes tesztet a kiválasztott kapcsolati figyelőben.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Tesztelési csoport
   1. Kattintson a vizsgálni kívánt tesztelési csoportra részletesen
   2. Alapértelmezés szerint a "forrás + cél + tesztelési konfiguráció" (teszt) alapján megtekintheti a figyelési adatpontokat.

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Válassza ki a vizsgálni kívánt tesztet részletesen
   4. A kiválasztott időintervallum esetében a (z)%-os ellenőrzésekhez nem sikerült a küszöbértéket és a tényleges értékeket látni. A RTT MS a küszöbértéket, az átlagot, a minimumot és a maximális értéket fogja látni. A kiválasztott teszthez kapcsolódó felgyújtott riasztásokat is láthat.
   5. Az időintervallum módosítása további adatértékek megtekintéséhez
   6. A b lépésben megváltoztathatja a nézetet, és megtekintheti forrás, célhely vagy tesztelési konfiguráció alapján. Ezután válasszon ki egy entitást az 5 leggyakoribb sikertelen teszt kivizsgálásához.  Például: válassza a nézet: források és célhelyek lehetőséget, hogy vizsgálja meg az adott kombináció között futó összes tesztet a kiválasztott kapcsolati figyelőben.

3. Tesztelés
   1. Kattintson a vizsgálni kívánt forrás + cél + tesztelési konfigurációra.
   2. A kiválasztott időintervallum esetében a (z)%-os ellenőrzésekhez nem sikerült a küszöbértéket és a tényleges értékeket látni. A RTT MS a küszöbértéket, az átlagot, a minimumot és a maximális értéket fogja látni. A kiválasztott teszthez kapcsolódó felgyújtott riasztásokat is láthat.

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. A topológia lehetőségre kattintva bármikor megtekintheti a hálózati topológiát.

      ![Teszt2](./media/connection-monitor-2-preview/test-topo.png)

   4. A kapcsolódási figyelő által azonosított problémák megtekintéséhez kattintson bármelyik ugrásra az Azure-hálózatra mutató hivatkozásra. Ez a funkció jelenleg nem érhető el helyszíni hálózatokhoz.

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Lekérdezések naplózása Azure Monitor Log Analytics

A Log Analytics használatával egyéni nézeteket hozhat létre a figyelési adatairól. A felhasználói felületen megjelenő összes adatot Log Analytics-ból tölti ki a rendszer. A tárházban lévő adatok interaktív elemzését elvégezheti, és különböző forrásokból (például az ügynökök állapotáról és más Log Analytics-alapú alkalmazásokból) származó adatokkal is korrelálhat. Az Excelbe, Power BIba vagy egy megosztható hivatkozásba is exportálhatja az adatfájlokat.

#### <a name="metrics-in-azure-monitor"></a>Metrikák az Azure Monitorban

A Csatlakozáskezelő (előzetes verzió) felhasználói felülete előtt létrehozott figyelőhöz mind a 4 metrika elérhetővé válik. A Csatlakozáskezelő (előzetes verzió) használatával létrehozott kapcsolatok figyelője esetében az adatok csak az "(előzetes)" címkével ellátott Mérőszámokhoz lesznek elérhetők.

Erőforrás típusa – Microsoft. Network/networkWatchers/connectionMonitors

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | %-Os mintavétel sikertelen | Százalék | Átlag | a kapcsolat figyelési mintavételének%-a meghiúsult | Nincs dimenzió |
| AverageRoundtripMs | Átlagos menetidő (MS) | Ezredmásodpercben | Átlag | A forrás és a cél között eljuttatott kapcsolati figyelési mintavételek átlagos hálózati időkorlátja (MS) |             Nincs dimenzió |
| ChecksFailedPercent (előzetes verzió) | %-Os ellenőrzés sikertelen (előzetes verzió) | Százalék | Átlag | teszt esetén sikertelen volt az ellenőrzések százaléka | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * Sourceresourceid azonosítónak <br> * Forrás típusa <br> * Protokoll <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * Régió |
| RoundTripTimeMs (előzetes verzió) | Oda-és visszaút időpontja (MS) (előzetes verzió) | Ezredmásodperc | Átlag | A forrás és a cél között eljuttatott ellenőrzések időkorlátja (MS). Ez az érték nem átlag | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * Sourceresourceid azonosítónak <br> * Forrás típusa <br> * Protokoll <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * Régió |

 ![Metrikák figyelése](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Metrikus riasztások Azure Monitor

Riasztás létrehozása:

1. Válassza ki a Csatlakozáskezelő használatával létrehozott Csatlakozáskezelő-erőforrást (előzetes verzió)
2. Győződjön meg arról, hogy a "metrika" megjelenik az előző lépésben kiválasztott erőforráshoz tartozó jel típusaként
3. A feltétel hozzáadása területen válassza a jel neve ChecksFailedPercent (előzetes verzió) vagy a RoundTripTimeMs (előzetes verzió) és a jel típusa Mérőszámként lehetőséget. Például: Choose ChecksFailedPercent (előzetes verzió)
4. A rendszer a metrikák szerint alkalmazandó összes dimenziót listázza.  Válassza ki a dimenzió nevét és a dimenzió értékét. Például: válassza a forrás címe lehetőséget, és adja meg az 1. lépésben kiválasztott, a Csatlakozáskezelő erőforrásban érintett bármely forrás IP-címét.
5. A riasztási logika területen válassza a következőket:
   1. Feltétel típusa – statikus
   2. Feltétel és küszöbérték
   3. Az Összesítés részletessége és a kiértékelés gyakorisága – a Csatlakozáskezelő (előzetes verzió) 1 percenként frissíti az adatokat.
6.  A műveletek területen válassza ki a műveleti csoportot
7. Riasztás részleteinek megadása
8. Riasztási szabály létrehozása

   ![Riasztások](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>5\. lépés: a hálózatban felmerülő problémák diagnosztizálása

A kapcsolódási figyelő segít a kapcsolódási figyelő erőforrásának és a hálózatának megfelelő problémák diagnosztizálásában. A hibrid hálózattal kapcsolatos problémákat az 1. lépésben telepített Log Analytics ügynökök fogják észlelni, és az Azure-ban az Network Watcher-bővítmény észleli a problémákat.  A hibrid hálózatban felmerülő problémák a diagnosztika lapon jelennek meg, és az Azure-hálózatban felmerülő problémák a hálózati topológiában lesznek láthatók.

A helyszíni virtuális gépekkel rendelkező hálózatok forrásaként a rendszer az alábbiakat vizsgálja:

* A kérelem időkorlátja lejárt
* A végpontot nem oldja fel a DNS – ideiglenes vagy állandó. Érvénytelen URL-cím.
* Nem található gazdagép.
* A forrás nem tud kapcsolódni a célhelyhez. A cél nem érhető el az ICMP protokollon keresztül.
* Tanúsítványhoz kapcsolódó probléma – az ügynök hitelesítéséhez szükséges ügyféltanúsítvány, a tanúsítvány-áthelyezési lista nem érhető el, a végpont állomásneve nem felel meg a tanúsítvány tulajdonosának vagy tulajdonosának alternatív nevének, a forrás helyi számítógépének megbízható hitelesítésszolgáltatók tárolójában hiányzó főtanúsítvány, az SSL-tanúsítvány lejárt/érvénytelen/visszavont, nem kompatibilis

Az Azure-beli virtuális gépekkel rendelkező hálózatok forrásai a következők:

* Ügynökkel kapcsolatos problémák – az ügynök leállt, sikertelen DNS-feloldás, nincs alkalmazás/figyelő a célport figyelésére, a szoftvercsatorna nem nyitható meg.
* VIRTUÁLIS gépek állapotával kapcsolatos problémák – indítás, Leállítás, leállítva, felszabadítás, felszabadítva, újraindítás, nincs lefoglalva
* Hiányzik az ARP-tábla bejegyzése
* A helyi tűzfal problémái miatt letiltott forgalom, NSG-szabályok
* VNET-átjáró – hiányzó útvonalak, a két átjáró közötti alagút le van választva, vagy hiányzik, vagy a másik átjáró nem található meg az alagútban, nem találhatók peering információ
* Hiányzó útvonal az MS Edge-ben.
* A forgalom a rendszerútvonalak vagy a UDR miatt leállt
* A BGP nincs engedélyezve az átjárón keresztüli kapcsolatban
* DIP-mintavétel a Load Balancer

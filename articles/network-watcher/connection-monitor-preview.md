---
title: Kapcsolatfigyelő (előzetes verzió) | Microsoft dokumentumok
description: Megtudhatja, hogy miként figyelheti a hálózati kommunikációt elosztott környezetben a Kapcsolatfigyelő (előzetes verzió) segítségével.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599326"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Hálózati kapcsolat figyelése kapcsolatfigyelővel (előzetes verzió)

A Kapcsolatfigyelő (előzetes verzió) egységes, teljes körű kapcsolatfigyelést biztosít az Azure Network Watcher ben. A Kapcsolatfigyelő (Előzetes verzió) szolgáltatás támogatja a hibrid és az Azure felhőalapú telepítések. A Network Watcher eszközöket biztosít az Azure-telepítésekhez tartozó, kapcsolatokkal kapcsolatos metrikák figyeléséhez, diagnosztizálásához és megtekintéséhez.

Íme néhány használati eset a Kapcsolatfigyelőhöz (előzetes verzió):

- Az előtér-webkiszolgáló virtuális gépe kommunikál egy adatbázis-kiszolgáló virtuális gép egy többrétegű alkalmazás. Szeretné ellenőrizni a hálózati kapcsolatot a két virtuális gép között.
- Azt szeretné, hogy az USA keleti régiójában lévő virtuális gépek pingeljék a virtuális gépeket az USA középső régiójában, és össze szeretné hasonlítani a régiók közötti hálózati késéseket.
- Több helyszíni irodája van Seattle-ben, Washingtonban és Ashburnben, Virginiában. Az irodai webhelyek office 365-ös URL-címekhez csatlakoznak. Az Office 365 URL-címeinek felhasználói számára hasonlítsa össze a Seattle és Ashburn közötti késéseket.
- A hibrid alkalmazásnak szüksége van egy Azure Storage-végpontra való kapcsolatra. A helyszíni hely és az Azure-alkalmazás ugyanahhoz az Azure Storage-végponthoz csatlakozik. Szeretné összehasonlítani a helyszíni hely késéseit az Azure-alkalmazás késései.
- Szeretné ellenőrizni a helyszíni beállítások és a felhőalapú alkalmazást üzemeltető Azure-beli virtuális gépek közötti kapcsolatot.

Az előzetes verzióban a Kapcsolatfigyelő két funkció közül a legjobbat egyesíti: a Hálózatfigyelő [kapcsolatfigyelő](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) szolgáltatását és a Hálózati teljesítményfigyelő (NPM) [szolgáltatás kapcsolatfigyelő](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) szolgáltatását.

Íme néhány előnye a Kapcsolatfigyelő (Előzetes verzió):

* Egységes, intuitív élmény az Azure-hoz és a hibrid monitorozási igényekhez
* Régiók közötti, munkaterületközi kapcsolat figyelése
* Nagyobb szondázási frekvenciák és jobb láthatóság a hálózati teljesítményben
* Gyorsabb riasztás a hibrid környezetekben
* Http, TCP és ICMP alapú kapcsolatellenőrzések támogatása 
* Metrikák és log analytics-támogatás az Azure és a nem Azure tesztbeállításokhoz

![Az azure-beli virtuális gépekkel, nem Azure-beli gazdagépekkel, végpontokkal és adattárolási helyekkel való kapcsolatfigyelő együttműködését bemutató diagram](./media/connection-monitor-2-preview/hero-graphic.png)

A Kapcsolatfigyelő (Előzetes verzió) figyelési szolgáltatásának használatának megkezdéséhez kövesse az alábbi lépéseket: 

1. Felügyeleti ügynökök telepítése.
1. Engedélyezze a Network Watcher előfizetését.
1. Hozzon létre egy kapcsolatfigyelőt.
1. Adatelemzés és riasztások beállítása.
1. Diagnosztizálja a hálózati problémákat.

A következő szakaszok ezeket a lépéseket ismertetik.

## <a name="install-monitoring-agents"></a>Figyelőügynökök telepítése

A Kapcsolatfigyelő a könnyű végrehajtható fájlokra támaszkodik a kapcsolódási ellenőrzések futtatásához.  Támogatja a kapcsolatellenőrzéset az Azure-környezetekből és a helyszíni környezetekből is. A futtatható fájl használata attól függ, hogy a virtuális gép az Azure-ban vagy a helyszíni üzemelteti.

### <a name="agents-for-azure-virtual-machines"></a>Ügynökök az Azure virtuális gépekhez

Ha azt szeretné, hogy a Connection Monitor felismerje az Azure-beli virtuális gépeket figyelési forrásként, telepítse rájuk a Network Watcher Agent virtuálisgép-bővítményt. Ezt a bővítményt *Network Watcher bővítménynek is nevezik.* Az Azure virtuális gépek megkövetelik a bővítményt a végpontok között figyelés és más speciális funkciók aktiválásához. 

Virtuális gép létrehozásakor telepítheti a Network Watcher [bővítményt.](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm) A Network Watcher bővítményt külön is telepítheti, konfigurálhatja és elháríthatja [Linuxra](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) és [Windowsra.](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)

A hálózati biztonsági csoportra (NSG) vagy tűzfalra vonatkozó szabályok blokkolhatják a forrás és a cél közötti kommunikációt. A Kapcsolatfigyelő észleli a problémát, és diagnosztikai üzenetként jeleníti meg a topológiában. A kapcsolatfigyelés engedélyezéséhez győződjön meg arról, hogy az NSG- és tűzfalszabályok engedélyezik a TCP-n vagy ICMP-n keresztüli csomagokat a forrás és a cél között.

### <a name="agents-for-on-premises-machines"></a>Ügynökök helyszíni gépekhez

Ha azt szeretné, hogy a Kapcsolatfigyelő a helyszíni gépeket a figyelés forrásaként ismerje fel, telepítse a Log Analytics-ügynököt a gépekre. Ezután engedélyezze a Hálózati teljesítményfigyelő megoldást. Ezek az ügynökök a Log Analytics-munkaterületekhez kapcsolódnak, ezért be kell állítania a munkaterület-azonosítót és az elsődleges kulcsot, mielőtt az ügynökök megkezdhetnék a figyelést.

A Windows-gépekhez készült Log Analytics-ügynök telepítéséről az [Azure Monitor Windows-alapú virtuálisgép-bővítménycímű](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)témakörben található.

Ha az elérési út tűzfalakat vagy hálózati virtuális készülékeket (NVA-kat) tartalmaz, győződjön meg arról, hogy a cél elérhető.

## <a name="enable-network-watcher-on-your-subscription"></a>A Hálózatfigyelő engedélyezése az előfizetésen

Minden virtuális hálózattal rendelkező előfizetés engedélyezve van a Network Watcher szolgáltatással. Amikor virtuális hálózatot hoz létre az előfizetésében, a Network Watcher automatikusan engedélyezve lesz a virtuális hálózat régiójában és előfizetésében. Ez az automatikus engedélyezés nincs hatással az erőforrásokra, és nem számít fel díjat. Győződjön meg arról, hogy a Network Watcher nincs kifejezetten letiltva az előfizetésben. 

További információt a [Hálózatfigyelő engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)című témakörben talál.

## <a name="create-a-connection-monitor"></a>Kapcsolatfigyelő létrehozása 

A Kapcsolatfigyelő rendszeres időközönként figyeli a kommunikációt. Tájékoztatja Önt az elérhetőség és a késés változásairól. Ellenőrizheti a forrásügynökök és a célvégpontok közötti aktuális és előzményhálózati topológiát is.

A források lehetnek Azure-beli virtuális gépek vagy helyszíni gépek, amelyek telepített figyelési ügynökkel rendelkeznek. A célvégpontok lehetnek Office 365 URL-címek, Dynamics 365 URL-ek, egyéni URL-ek, Azure VM-erőforrásazonosítók, IPv4, IPv6, Teljes tartománynév vagy bármely tartománynév.

### <a name="access-connection-monitor-preview"></a>Hozzáférés-kapcsolatfigyelő (előzetes verzió)

1. Az Azure Portal kezdőlapján nyissa meg a **Network Watcher**című lapot.
1. A bal oldalon, a **Figyelés** csoportban válassza a **Kapcsolatfigyelő (előzetes verzió)** lehetőséget.
1. A Kapcsolatfigyelőben (előzetes verzió) létrehozott összes kapcsolatfigyelő megjelenik. A Kapcsolatfigyelő klasszikus felületén létrehozott kapcsolatfigyelők megtekintéséhez lépjen a **Kapcsolatfigyelő** lapra.

    ![Képernyőkép a Kapcsolatfigyelőben létrehozott kapcsolatfigyelőkről (előzetes verzió)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Kapcsolatfigyelő létrehozása

A kapcsolatfigyelőben (előzetes verzió) létrehozott kapcsolatfigyelőkben a helyszíni gépeket és az Azure-beli virtuális gépeket is hozzáadhatja forrásként. Ezek a kapcsolatfigyelők a végpontokhoz való kapcsolódást is figyelhetik. A végpontok lehetnek az Azure-ban, vagy bármely más URL-cím vagy IP.

A Kapcsolatfigyelő (előzetes verzió) a következő entitásokat tartalmazza:

* **Kapcsolatfigyelő erőforrás** – régióspecifikus Azure-erőforrás. Az alábbi entitások mindegyike egy kapcsolatfigyelő erőforrás tulajdonságai.
* **Végpont** – olyan forrás vagy cél, amely részt vesz a kapcsolat ellenőrzésében. Végpontok közé tartozik az Azure virtuális gépek, a helyszíni ügynökök, URL-címek és IP-címek.
* **Tesztkonfiguráció** – Protokoll-specifikus konfiguráció egy teszthez. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztgyakoriságot és egyéb paramétereket.
* **Tesztcsoport** – A forrásvégpontokat, a célvégpontokat és a tesztkonfigurációkat tartalmazó csoport. A kapcsolatfigyelő több tesztcsoportot is tartalmazhat.
* **Teszt** – A forrásvégpont, a célvégpont és a tesztkonfiguráció kombinációja. A vizsgálat a legrészletesebb szint, amelyen a monitorozási adatok rendelkezésre állnak. A figyelési adatok tartalmazzák a sikertelen ellenőrzések százalékos arányát és az oda-vissza idő (RTT) arányát.

 ![Kapcsolatfigyelőt bemutató diagram, amely meghatározza a tesztcsoportok és tesztek közötti kapcsolatot](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Kapcsolatfigyelő létrehozása az Azure Portalon

Ha kapcsolatfigyelőt szeretne létrehozni az Azure Portalról, kövesse az alábbi lépéseket:

1. A **Kapcsolatfigyelő (előzetes verzió)** irányítópultján, a bal felső sarokban válassza a **Létrehozás gombot.**
1. Az **Alapok** lapon adja meg a kapcsolatfigyelő adatait:
   * **Kapcsolatfigyelő neve** – Adja hozzá a kapcsolatfigyelő nevét. Használja az Azure-erőforrások szabványos elnevezési szabályait.
   * **Előfizetés** – Válasszon előfizetést a kapcsolatfigyelőhöz.
   * **Régió** – Válasszon régiót a kapcsolatfigyelőhöz. Csak az ebben a régióban létrehozott forrásvirtuális gépeket választhatja ki.
   * **Munkaterület-konfiguráció** – A munkaterület tartalmazza a figyelési adatokat. Egyéni munkaterületet vagy az alapértelmezett munkaterületet is használhat. 
       * Az alapértelmezett munkaterület használatához jelölje be a jelölőnégyzetet. 
       * Egyéni munkaterület kiválasztásához törölje a jelet a jelölőnégyzetből. Ezután válassza ki az egyéni munkaterület előfizetését és régióját. 
1. A lap alján válassza a **Tovább: Tesztcsoportok**lehetőséget.

   ![Képernyőkép az Alapok lapról a Kapcsolatfigyelőben](./media/connection-monitor-2-preview/create-cm-basics.png)

1. A **Tesztcsoportok** lapon válassza a **+ Tesztcsoport lehetőséget.** A tesztcsoportok beállításához olvassa el a [Tesztcsoportok létrehozása a Kapcsolatfigyelőben című témakört.](#create-test-groups-in-a-connection-monitor) 
1. A lap alján válassza a **Tovább: Véleményezés + létrehozás** lehetőséget a kapcsolatfigyelő áttekintéséhez.

   ![Képernyőkép a Tesztcsoportok lapról és a tesztcsoport részleteinek hozzáadásáról készült ablaktábláról](./media/connection-monitor-2-preview/create-tg.png)

1. A **Véleményezés + létrehozás** lapon tekintse át az alapvető információkat és a tesztcsoportokat a kapcsolatfigyelő létrehozása előtt. Ha a kapcsolatfigyelőszerkesztésre van szükség:
   * Az alapvető részletek szerkesztéséhez kattintson a ceruza ikonra.
   * Tesztcsoport szerkesztéséhez jelölje ki azt.

   > [!NOTE] 
   > A **Véleményezés + létrehozás** lapon a Kapcsolatfigyelő előnézeti szakaszában a havonta számított költség látható. Jelenleg a **CURRENT COST** oszlop ban nem jelenik meg díjmentes. Amikor a Kapcsolatfigyelő általánosan elérhetővé válik, ez az oszlop havi díjat jelenít meg. 
   > 
   > A Log Analytics betöltési díjai még a Kapcsolatfigyelő előzetes szakaszában is érvényesek.

1. Ha készen áll a kapcsolatfigyelő létrehozására, a **Véleményezés + létrehozás** lap alján válassza a **Létrehozás gombot.**

   ![Képernyőkép a Kapcsolatfigyelőről, amelyen a Véleményezés + létrehozás lap látható](./media/connection-monitor-2-preview/review-create-cm.png)

A Kapcsolatfigyelő (előzetes verzió) a háttérben hozza létre a kapcsolatfigyelő erőforrást.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Kapcsolatfigyelő létrehozása az ARMClient használatával

A következő kód segítségével hozzon létre egy kapcsolatfigyelőt az ARMClient használatával.

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

Itt a telepítési parancs:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Tesztcsoportok létrehozása kapcsolatfigyelőben

A kapcsolatfigyelő minden tesztcsoportja olyan forrásokat és célállomásokat tartalmaz, amelyeket a hálózati paraméterek tesztelnek. A rendszer teszteli a sikertelen ellenőrzések százalékos arányát és az RTT-t a tesztkonfigurációk on keresztül.

Az Azure Portalon egy tesztcsoport létrehozásához a kapcsolatfigyelőben adja meg a következő mezők értékeit:

* **Tesztcsoport letiltása** – Ezt a mezőt a tesztcsoport által megadott összes forrás és cél figyelésének letiltásához választhatja ki. A kijelölés alapértelmezés szerint törlődik.
* **Név** – A tesztcsoport elnevezése.
* **Források** – Megadhatja az Azure virtuális gépek és a helyszíni gépek forrásként, ha ügynökök vannak telepítve rájuk. A forrásügynök telepítéséről a [Figyelési ügynökök telepítése](#install-monitoring-agents)című témakörben van.
   * Az Azure-ügynökök kiválasztásához válassza az **Azure-ügynökök** lapot. Itt csak azokat a virtuális gépeket láthatja, amelyek a kapcsolatfigyelő létrehozásakor megadott régióhoz vannak kötve. Alapértelmezés szerint a virtuális gépek vannak csoportosítva az előfizetés, amelyhez tartoznak. Ezek a csoportok összevannak esve. 
   
       Az Előfizetés szinttől a hierarchia más szintjeiig is leáshat:

      **Előfizetési** > **erőforráscsoportok** > **VNET-k** > **alhálózatok** > **virtuális hálózatok ügynökökkel**

      A Csoport mező **szerinti** értékét is módosíthatja, ha a fát bármely más szintről indítja el. Ha például virtuális hálózat szerint csoportosít, akkor a **vnets-ek** > **ügynökei** > ügynökökkel rendelkező virtuális**gépeket láthatja ügynökökkel.**

      ![Képernyőkép a Kapcsolatfigyelőről, amelyen a Források hozzáadása panel és az Azure-ügynökök lap látható](./media/connection-monitor-2-preview/add-azure-sources.png)

   * A helyszíni ügynökök kiválasztásához válassza a **Nem Azure-ügynökök** lapot. Alapértelmezés szerint az ügynökök régiók szerint vannak csoportosítva a munkaterületekre. Az összes munkaterület konfigurálva van a Hálózati teljesítményfigyelő megoldással. 
   
       Ha hálózati teljesítményfigyelőt kell hozzáadnia a munkaterülethez, az [Azure Marketplace-ről](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)szerezheti be. A Hálózati teljesítményfigyelő hozzáadásáról az [Azure Monitor figyelési megoldásai](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)című témakörben talál további információt. 
   
       A **Kapcsolatfigyelő létrehozása** nézetben az **Alapok** lapon az alapértelmezett terület van kiválasztva. Ha módosítja a régiót, az új régió munkaterületei közül választhat ügynököket. A Csoport értéke mező **szerint** is módosítható alhálózatok szerinti csoportosításra.

      ![Képernyőkép a Kapcsolatfigyelőről, amely en látható a Források hozzáadása panel és a Nem Azure-ügynökök lap](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * A kiválasztott Azure- és nem Azure-ügynökök áttekintéséhez lépjen a **Véleményezés** lapra.

      ![Képernyőkép a Kapcsolatfigyelőről, amelyen a Források hozzáadása panel és a Véleményezés lap látható](./media/connection-monitor-2-preview/review-sources.png)

   * Amikor befejezte a források beállítását, a **Források hozzáadása** panel alján válassza a **Kész**lehetőséget.

* **Úti célok** – Figyelheti az Azure virtuális gépekhez vagy bármely végponthoz (nyilvános IP-cím, URL vagy teljes tartományn) való kapcsolódást, ha úti célként adja meg őket. Egyetlen tesztcsoportban hozzáadhat Azure-beli virtuális gépeket, Office 365-ös URL-eket, Dynamics 365 URL-címeket és egyéni végpontokat.

    * Az Azure-beli virtuális gépek úti célként való kiválasztásához válassza az **Azure virtuális gépek** lapot. Alapértelmezés szerint az Azure-beli virtuális gépek egy olyan előfizetési hierarchiába vannak csoportosítva, amely ugyanabban a régióban található, amelyet a **Kapcsolatfigyelő létrehozása** nézetben, az **Alapok lapon** választott ki. Módosíthatja a régiót, és kiválaszthatja az Azure-beli virtuális gépeket az újonnan kiválasztott régióból. Ezután leáshat az előfizetési szintről a hierarchia más szintjeire, például az Azure-ügynökök szintjére.

       ![Képernyőkép a Célhozzáadása ablaktábláról, amely en látható az Azure virtuális gépek lap](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Képernyőkép a Célok hozzáadása ablaktábláról az Előfizetési szint látható](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Végpontok célként való kiválasztásához válassza a **Végpontok** lapot. A végpontok listája tartalmazza az Office 365 teszt URL-címeit és a Dynamics 365 teszt URL-címeit, név szerint csoportosítva. Ezeken a végpontokon kívül kiválaszthat egy végpontot, amely et ugyanazon kapcsolatfigyelő más tesztcsoportjaiban hoztak létre. 
    
        Új végpont hozzáadásához a jobb felső sarokban válassza a **+ Végpontok**lehetőséget. Ezután adja meg a végpont nevét és URL-címét, IP-címét vagy teljes tartománynevét.

       ![Képernyőkép avégpontok célként való hozzáadásának helyének a connection monitorban](./media/connection-monitor-2-preview/add-endpoints.png)

    * Az Azure virtuális gépek és a kiválasztott végpontok áttekintéséhez válassza a **Véleményezés** lapot.
    * Ha befejezte az úti célok kiválasztását, válassza a **Kész**lehetőséget.

* **Konfigurációk tesztelése** – Tesztkonfigurációk at társítható egy tesztcsoporthoz. Az Azure Portal tesztcsoportonként csak egy tesztkonfigurációt engedélyez, de az ARMClient használatával továbbiakat adhat hozzá.

    * **Név** – A tesztkonfiguráció elnevezése.
    * **Protokoll** – Válassza a TCP, ICMP vagy HTTP protokollt. A HTTP HTTPS-re való módosításához válassza a **HTTP** protokollt, és válassza a **443-as** portot.
        * **Hálózati tesztkonfiguráció létrehozása** – Ez a jelölőnégyzet csak akkor jelenik meg, ha a Protokoll mezőben a **HTTP** lehetőséget **választja.** Jelölje be ezt a jelölőnégyzetet, ha egy másik tesztkonfigurációt szeretne létrehozni, amely ugyanazokat a forrásokat és célokat használja, mint amelyeket a konfiguráció ban máshol megadott. Az újonnan létrehozott `<the name of your test configuration>_networkTestConfig`tesztkonfiguráció neve .
        * **Traceroute letiltása** – Ez a mező azokra a tesztcsoportokra vonatkozik, amelyek protokollja TCP vagy ICMP. Jelölje be ezt a jelölőnégyzetet, ha meg szeretné akadályozni, hogy a források felfedjék a topológiát és az ugrásos RTT-t.
    * **Célport** – Ezt a mezőt az Ön által választott célporttal szabhatja testre.
    * **Tesztgyakoriság** – Ebben a mezőben adhatja meg, hogy a források milyen gyakran pingeljék a célállomásokat a megadott protokollon és porton. Választhat 30 másodperc, 1 perc, 5 perc, 15 perc vagy 30 perc. A források a választott érték alapján tesztelik a célhelyekhez való kapcsolódást.  Ha például 30 másodpercet választ ki, a források 30 másodperces időszakban legalább egyszer ellenőrzik a célhoz való kapcsolódást.
    * **Sikerességi küszöbérték** – A következő hálózati paraméterekre vonatkozó küszöbértékeket állíthatja be:
       * **Az ellenőrzések sikertelenek** – Beállíthatja, hogy a megadott feltételek alapján a források a célhoz való kapcsolódás ellenőrzése esetén sikertelen ellenőrzések százalékos aránya.Checks failed – Set the percentage of checks that can fail when sources check connectivity to destinations by using the criteria you d. A TCP vagy ICMP protokoll esetében a sikertelen ellenőrzések százalékos aránya a csomagvesztés százalékának felelmeg. A HTTP protokoll esetében ez a mező azoknak a HTTP-kérelmeknek a százalékos arányát jelöli, amelyekre nem érkezett válasz.
       * **Oda-vissza út –** Állítsa be az RTT-t ezredmásodpercben arra vonatkozóan, hogy a források mennyi időt vehetnek igénybe a célhoz való csatlakozáshoz a tesztkonfiguráción keresztül.
    
       ![Képernyőkép atesztkonfiguráció beállításának helyről a Kapcsolatfigyelőben](./media/connection-monitor-2-preview/add-test-config.png)

A tesztcsoporthoz hozzáadott összes forrás, cél- és tesztkonfiguráció az egyes tesztekre bontásban történik. Íme egy példa a források és az úti célok szerinti bontásra:

* Vizsgálati csoport: TG1
* Források: 3 (A, B, C)
* Úti célok: 2 (D, E)
* Tesztkonfigurációk: 2 (Config 1, Config 2)
* Összes teszt létrehozva: 12

| Vizsgálati szám | Forrás | Cél | Teszt konfigurációja |
| --- | --- | --- | --- |
| 1 | A | D | 1. konfiguráció |
| 2 | A | D | Config 2 (Konfiguráció 2) |
| 3 | A | E | 1. konfiguráció |
| 4 | A | E | Config 2 (Konfiguráció 2) |
| 5 | B | D | 1. konfiguráció |
| 6 | B | D | Config 2 (Konfiguráció 2) |
| 7 | B | E | 1. konfiguráció |
| 8 | B | E | Config 2 (Konfiguráció 2) |
| 9 | C# | D | 1. konfiguráció |
| 10 | C# | D | Config 2 (Konfiguráció 2) |
| 11 | C# | E | 1. konfiguráció |
| 12 | C# | E | Config 2 (Konfiguráció 2) |

### <a name="scale-limits"></a>Méretezési határértékek

A kapcsolatfigyelők méretezési határértékei a következők:

* Maximális kapcsolatfigyelők előfizetésenként régiónként: 100
* Maximális tesztcsoportok kapcsolatfigyelőnként: 20
* Maximális források és úti célok kapcsolatfigyelőnként: 100
* Maximális tesztkonfigurációk kapcsolatfigyelőnként: 
    * 20 keresztül ARMClient
    * 2 az Azure portálon keresztül

## <a name="analyze-monitoring-data-and-set-alerts"></a>Figyelési adatok elemzése és riasztások beállítása

Miután létrehozott egy kapcsolatfigyelőt, a források a tesztkonfiguráció alapján ellenőrzik a célhelyekhez való kapcsolódást.

### <a name="checks-in-a-test"></a>Ellenőrzések egy vizsgálatban

A tesztkonfigurációban kiválasztott protokoll alapján a Kapcsolatfigyelő (Előzetes verzió) egy sor ellenőrzést futtat a forrás-cél párhoz. Az ellenőrzések a kiválasztott tesztgyakoriság nak megfelelően futnak.

Http használata esetén a szolgáltatás kiszámítja a válaszkódot visszaadó HTTP-válaszok számát. Az eredmény a sikertelen ellenőrzések százalékos arányát határozza meg. Az RTT kiszámításához a szolgáltatás méri a HTTP-hívás és a válasz közötti időt.

Ha TCP vagy ICMP protokollt használ, a szolgáltatás kiszámítja a csomagvesztési százalékot a sikertelen ellenőrzések százalékos arányának meghatározásához. Az RTT kiszámításához a szolgáltatás méri az elküldött csomagok nyugtázásának (ACK) fogadásához szükséges időt. Ha engedélyezte a nyomkövetési útvonal adatait a hálózati tesztekhez, láthatja az ugrásról ugrásra való veszteséget és a késést a helyszíni hálózaton.

### <a name="states-of-a-test"></a>A vizsgálat állapotai

Az ellenőrzések által visszaadott adatok alapján a tesztek a következő állapotokat kaphatják:

* **Áthaladás** – a sikertelen ellenőrzések és RTT százalékának tényleges értékei a megadott küszöbértékeken belül vannak.
* **Sikertelen** – a sikertelen ellenőrzések vagy RTT-k százalékos arányának tényleges értékei meghaladták a megadott küszöbértékeket. Ha nincs megadva küszöbérték, akkor a teszt eléri a Fail állapotot, ha a sikertelen ellenőrzések százalékos aránya 100.
* **Figyelmeztetés** – Nem adott meg feltételt a sikertelen ellenőrzések százalékos arányára vonatkozóan. Meghatározott feltételek hiányában a Kapcsolatfigyelő (Előzetes verzió) automatikusan hozzárendel egy küszöbértéket. A küszöbérték túllépése esetén a teszt állapota Figyelmeztetés értékre változik.

### <a name="data-collection-analysis-and-alerts"></a>Adatgyűjtés, elemzés és riasztások

A Kapcsolatfigyelő (Előzetes verzió) által gyűjtött adatok a Log Analytics-munkaterületen tárolódnak. Ezt a munkaterületet a kapcsolatfigyelő létrehozásakor állította be. 

Figyelési adatok is rendelkezésre állnak az Azure Monitor metrikák. A Log Analytics segítségével a figyelési adatokat, ameddig csak szeretné. Az Azure Monitor alapértelmezés szerint csak 30 napig tárolja a metrikákat. 

Az [adatokon metrikaalapú riasztásokat állíthat be.](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)

#### <a name="monitoring-dashboards"></a>Irányítópultok figyelése

A figyelési irányítópultokon láthatja az előfizetések, régiók, időbélyegzők, források és céltípusok kapcsolatfigyelőinek listáját.

Amikor a Hálózatfigyelő kapcsolatfigyelőjének (előzetes verzió) elemre lép, az adatokat a következő konkra tekintheti meg:

* **Kapcsolatfigyelő** – Az előfizetésekhez, régiókhoz, időbélyegzőkhöz, forrásokhoz és céltípusokhoz létrehozott összes kapcsolatfigyelő listája. Ez a nézet az alapértelmezett.
* **Tesztcsoportok** – Az előfizetésekhez, régiókhoz, időbélyegzőkhöz, forrásokhoz és céltípusokhoz létrehozott összes tesztcsoport listája. Ezeket a tesztcsoportokat nem szűrik a kapcsolatfigyelők.
* **Teszt** – Az előfizetésekhez, régiókhoz, időbélyegzőkhöz, forrásokhoz és céltípusokhoz futó összes teszt listája. Ezeket a teszteket nem szűrik a kapcsolatfigyelők vagy a tesztcsoportok.

Az alábbi képen a három adatnézetet az 1.nyíl jelzi.

Az irányítópulton kibonthatja az egyes kapcsolatfigyelők tesztcsoportjait. Ezután kibonthatja az egyes tesztcsoportokat, hogy láthassa a benne futó teszteket. 

A listák at a következők alapján szűrheti:

* **Legfelső szintű szűrők** – Válasszon előfizetéseket, régiókat, időbélyegző-forrásokat és céltípusokat. Lásd az alábbi képen látható 2.
* **Állapotalapú szűrők** – Szűrés a kapcsolatfigyelő, tesztcsoport vagy teszt állapota szerint. Lásd a 3-as nyilat az alábbi képen.
* **Egyéni szűrők** – Az általános kereséshez válassza **az Összes kijelölése** lehetőséget. Ha egy adott entitás szerint szeretne keresni, válasszon a legördülő listából. Lásd a 4-es nyilat az alábbi képen.

![Képernyőkép a kapcsolatfigyelők, tesztcsoportok és tesztek nézeteinek szűréséről a Kapcsolatfigyelőben (előzetes verzió)](./media/connection-monitor-2-preview/cm-view.png)

Ha például a Kapcsolatfigyelő (Előzetes verzió) összes tesztjét meg szeretné tekinteni, ahol a forrás IP-címe 10.192.64.56:
1. Módosítsa a nézetet **tesztelésre.**
1. A keresőmezőbe írja be a *10.192.64.56*
1. A legördülő listában válassza a **Források**lehetőséget.

Csak a sikertelen tesztek megjelenítése a Kapcsolatfigyelőben (előzetes verzió), ahol a forrás IP-címe 10.192.64.56:
1. Módosítsa a nézetet **tesztelésre.**
1. Az állapotalapú szűrőhöz válassza a **Fail (Sikertelen)** lehetőséget.
1. A keresőmezőbe írja be a *10.192.64.56*
1. A legördülő listában válassza a **Források**lehetőséget.

Csak a sikertelen tesztek megjelenítése a Kapcsolatfigyelőben (előzetes verzió), ahol a cél outlook.office365.com:
1. Nézet módosítása **tesztre.**
1. Az állapotalapú szűrőhöz válassza a **Fail (Sikertelen)** lehetőséget.
1. A keresőmezőbe írja be *a outlook.office365.com*
1. A legördülő listában válassza a **Célok**lehetőséget.

   ![Képernyőkép, amely en csak a Outlook.Office365.com célhoz nem felelt meg teszteket megjelenítő nézet látható](./media/connection-monitor-2-preview/tests-view.png)

Az RTT trendjeinek és a kapcsolatfigyelő sikertelen csekkjeinek százalékos arányának megtekintése:
1. Jelölje ki a vizsgálni kívánt kapcsolatfigyelőt. Alapértelmezés szerint a figyelési adatok tesztcsoport szerint vannak rendezve.

   ![Egy kapcsolatfigyelő mutatóit bemutató képernyőkép, tesztcsoport szerint](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Válassza ki a vizsgálni kívánt tesztcsoportot.

   ![Képernyőkép atesztcsoport kiválasztásának helyén](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    A tesztcsoport öt sikertelen tesztje az RTT vagy a sikertelen ellenőrzések százalékos aránya alapján jelenik meg. Minden tesztnél a sikertelen csekkek százalékos arányának RTT- és trendsorai láthatók.
1. Válasszon ki egy tesztet a listából, vagy válasszon egy másik tesztet a vizsgálathoz. Az időintervallumés a sikertelen ellenőrzések százalékos aránya a küszöbértékés a tényleges értékek látható. Az RTT esetében a küszöbérték, az átlag, a minimum és a maximum értékeit láthatja.

   ![Képernyőkép az RTT-re vonatkozó teszt eredményeiről és a sikertelen ellenőrzések százalékos arányáról](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. További adatok megtekintéséhez módosítsa az időintervallumot.
1. Módosítsa a nézetet a források, a célok vagy a tesztkonfigurációk megtekintéséhez. 
1. Válasszon egy forrást a sikertelen tesztek alapján, és vizsgálja meg az első öt sikertelen tesztet. Válassza**Destinations** például a Megtekintés források **szerint** > **Sources** lehetőséget, a Cél **szerint** > megtekintése lehetőséget, és a kapcsolatfigyelőben a megfelelő teszteket vizsgálhatja meg.

   ![Képernyőkép az első öt sikertelen teszt teljesítménymutatóiról](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Az RTT-ben bekövetkező trendek és a sikertelen ellenőrzések százalékos arányának megtekintése egy tesztcsoportban:

1. Válassza ki a vizsgálni kívánt tesztcsoportot. 

    Alapértelmezés szerint a figyelési adatok források, célok és tesztkonfigurációk (tesztek) szerint vannak elrendezve. Később módosíthatja a nézetet a tesztcsoportokról a forrásokra, a célhelyekre vagy a tesztkonfigurációkra. Ezután válasszon egy entitást az első öt sikertelen teszt vizsgálatához. Módosítsa például a nézetet forrásokra és célhelyekre a kiválasztott kapcsolatfigyelő megfelelő tesztjeinek vizsgálatához.
1. Válassza ki a vizsgálni kívánt tesztet.

   ![Képernyőkép ateszt kiválasztásának helyén](./media/connection-monitor-2-preview/tg-drill.png)

    Az időintervallumban és a sikertelen ellenőrzések százalékos arányában megjelenik a küszöbértékek és a tényleges értékek. Az RTT esetében a küszöbérték, az átlag, a minimum és a maximum értékei jelennek meg. A kiválasztott teszthez is megjelennek a kirúgott riasztások.
1. További adatok megtekintéséhez módosítsa az időintervallumot.

Az RTT-ben bekövetkező trendek és a sikertelen ellenőrzések százalékos arányának megtekintése:
1. Válassza ki a vizsgálni kívánt forrás-, cél- és tesztkonfigurációt.

    Az időintervallumban és a sikertelen ellenőrzések százalékos arányában megjelenik a küszöbértékek és a tényleges értékek. Az RTT esetében a küszöbérték, az átlag, a minimum és a maximum értékei jelennek meg. A kiválasztott teszthez is megjelennek a kirúgott riasztások.

   ![Képernyőkép egy teszt mutatóit mutatja](./media/connection-monitor-2-preview/test-drill.png)

1. A hálózati topológiát a Topológia lehetőséget válassza a **Topológia**lehetőséget.

   ![A hálózati topológia lapról képernyőkép](./media/connection-monitor-2-preview/test-topo.png)

1. Az azonosított problémák megtekintéséhez a topológiában jelöljön ki egy ugrást az elérési úton. (Ezek az ugrások Azure-erőforrások.) Ez a funkció jelenleg nem érhető el a helyszíni hálózatokon.

   ![Képernyőkép a Topológia lapon kijelölt ugrási hivatkozásról](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Lekérdezések naplózása a Log Analytics szolgáltatásban

A Log Analytics segítségével egyéni nézeteket hozhat létre a figyelési adatokból. A felhasználói felület által megjelenített összes adat a Log Analytics szolgáltatásból származik. Interaktívan elemezheti a tárházban lévő adatokat. Az Ügynökállapotból származó adatok vagy a Log Analytics-en alapuló más megoldások korrelációja. Exportálja az adatokat az Excelbe vagy a Power BI-ba, vagy hozzon létre egy megosztható hivatkozást.

#### <a name="metrics-in-azure-monitor"></a>Metrikák az Azure Monitorban

A kapcsolatfigyelő (előzetes verzió) előtt létrehozott kapcsolatfigyelőkben mind a négy mérőszám elérhető: % Mintavételek sikertelen, Átlagoskör-tripmok, ChecksFailedPercent (Előzetes verzió) és Körúttal (előzetes verzió). A Kapcsolatfigyelő (előzetes verzió) felületén létrehozott kapcsolatfigyelőkben csak a *(Előzetes verzió)* címkével ellátott mérőszámokkal rendelkező adatkészletek adatai érhetők el.

![Képmutató a Kapcsolatfigyelő ben (előzetes verzió)](./media/connection-monitor-2-preview/monitor-metrics.png)

Ha metrikákat használ, állítsa be az erőforrás típusát Microsoft.Network/networkWatchers/connectionMonitors néven.

| Metrika | Megjelenített név | Unit (Egység) | Összesítés típusa | Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| MintavételekFailedPercent | Sikertelen % mintavételek | Százalék | Átlag | A kapcsolatfigyelő mintavételek százalékos aránya nem sikerült. | Nincsenek dimenziók |
| Átlagosfordulók | Átlagos oda-vissza idő (ms) | Ezredmásodperc | Átlag | Átlagos hálózati RTT a forrás és a cél között küldött kapcsolatfigyelő szondákhoz. |             Nincsenek dimenziók |
| ChecksFailedPercent (előzetes verzió) | Sikertelen %checks (előzetes verzió) | Százalék | Átlag | A sikertelen ellenőrzések százalékos aránya a teszthez. | ConnectionMonitorResourceId azonosító <br>Forráscím <br>Forrásnév <br>SourceResourceId azonosító <br>SourceType <br>Protocol (Protokoll) <br>DestinationAddress cím <br>Célnév <br>DestinationResourceId azonosító <br>Céltípusa <br>Célport <br>TestGroupName (TestGroupName) <br>TestConfigurationName <br>Régió |
| Körúttalmunkaidőm (előzetes verzió) | Oda-vissza út ideje (ms) (előzetes verzió) | Ezredmásodperc | Átlag | RTT a forrás és a cél között küldött ellenőrzésekre. Ez az érték nem átlagos. | ConnectionMonitorResourceId azonosító <br>Forráscím <br>Forrásnév <br>SourceResourceId azonosító <br>SourceType <br>Protocol (Protokoll) <br>DestinationAddress cím <br>Célnév <br>DestinationResourceId azonosító <br>Céltípusa <br>Célport <br>TestGroupName (TestGroupName) <br>TestConfigurationName <br>Régió |

#### <a name="metric-alerts-in-azure-monitor"></a>Metrikariasztások az Azure Monitorban

Riasztás létrehozása az Azure Monitorban:

1. Válassza ki a Kapcsolatfigyelőben (előzetes verzió) létrehozott kapcsolatfigyelő erőforrást.
1. Győződjön meg arról, hogy **a Metrika** jeltípusként jelenik meg a kapcsolatfigyelőhöz.
1. A **Feltétel hozzáadása (Add Condition) (Feltétel hozzáadása) csoportban**válassza a **ChecksFailedPercent(Preview)** vagy **a RoundTripTimeMs(Preview) lehetőséget.** **Signal Name**
1. A **Jeltípus mezőben**válassza **a Mérőszámok**lehetőséget. Válassza például **a ChecksFailedPercent(Preview) lehetőséget.**
1. A metrika összes dimenziója fel van sorolva. Válassza ki a dimenzió nevét és a dimenzió értékét. Válassza például a **Forráscím** lehetőséget, majd adja meg a kapcsolatfigyelő bármely forrásának IP-címét.
1. Az **Alert Logic (Riasztási logika) című**területen adja meg a következő adatokat:
   * **Feltétel típusa**: **Statikus**.
   * **Feltétel** és **küszöb**.
   * **Aggregációs részletesség és a kiértékelés gyakorisága:** A kapcsolatfigyelő (előzetes verzió) percenként frissíti az adatokat.
1. A **Műveletek csoportban**válassza ki az akciócsoportot.
1. Adja meg a riasztás részleteit.
1. Hozza létre a riasztási szabályt.

   ![Képernyőkép a Szabály létrehozása területről az Azure Monitorban; A "Forráscím" és a "Forrásvégpont neve" ki emelve](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>A hálózati problémák diagnosztizálása

A Kapcsolatfigyelő (Előzetes verzió) segítségével diagnosztizálhatja a kapcsolatfigyelőés a hálózat problémáit. A hibrid hálózat problémáit a korábban telepített Log Analytics-ügynökök észlelik. Az Azure-ban a Hálózatfigyelő bővítmény problémákat észlel. 

Az Azure-hálózat problémáit a hálózati topológiában tekintheti meg.

Olyan hálózatok esetében, amelyek nek a forrásai helyszíni virtuális gépek, a következő problémák észlelhetők:

* A kérés időkérés.
* A végpontot a DNS nem oldotta fel – ideiglenes vagy állandó. Az URL érvénytelen.
* Nem található gazdagép.
* A forrás nem tud csatlakozni a célhoz. A célpont nem érhető el az ICMP-n keresztül.
* A tanúsítvánnyal kapcsolatos problémák: 
    * Az ügynök hitelesítéséhez ügyféltanúsítvány szükséges. 
    * A tanúsítványok áthelyezési listája nem érhető el. 
    * A végpont állomásneve nem egyezik meg a tanúsítvány tulajdonosának vagy tulajdonosának alternatív nevével. 
    * A főtanúsítvány hiányzik a forrás helyi megbízható hitelesítésszolgáltatók tárolójában. 
    * Az SSL-tanúsítvány lejárt, érvénytelen, visszavont vagy nem kompatibilis.

Olyan hálózatok esetében, amelyek forrásai Azure virtuális gépek, a következő problémák észlelhetők:

* Ügynöki problémák:
    * Az ügynök megállt.
    * Sikertelen DNS-feloldás.
    * Nincs alkalmazás vagy figyelő figyela célporton.
    * A szoftvercsatorna nem nyitható meg.
* Virtuális gép állapota problémák: 
    * Indítás
    * Leállítás
    * Leállítva
    * Felszabadítás
    * Felszabadítva
    * Újraindít
    * Nincs lefoglalva
* Hiányzik az ARP táblabejegyzés.
* A forgalmat helyi tűzfalproblémák vagy NSG-szabályok miatt blokkolták.
* Virtuális hálózati átjáróval kapcsolatos problémák: 
    * Hiányzó útvonalak.
    * A két átjáró közötti alagút levan választva vagy hiányzik.
    * A második átjárót nem az alagúttalálta meg.
    * Nem található társviszony-létesítési információ.
* Az útvonal hiányzott a Microsoft Edge-ből.
* A forgalom leállt a rendszerútvonalak vagy az UDR miatt.
* A BGP nincs engedélyezve az átjárókapcsolaton.
* A DIP szonda a terheléselosztónál van.

---
title: Csatlakozáskezelő előzetes verziójának létrehozása – ARMClient
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan hozhat létre a ARMClient használatával a kapcsolódási figyelőt (előzetes verzió).
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 7d35799cd73ff4d065cb58189f2325dc4dac6840
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87567937"
---
# <a name="create-a-connection-monitor-preview-using-the-armclient"></a>Csatlakozáskezelő (előzetes verzió) létrehozása a ARMClient

Megtudhatja, hogyan hozhat létre kapcsolati figyelőt (előzetes verzió) az erőforrások közötti kommunikáció figyeléséhez a ARMClient használatával. Támogatja a hibrid és az Azure-beli Felhőbeli üzembe helyezést.

## <a name="before-you-begin"></a>Előkészületek 

A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott kapcsolódó figyelőkben a helyszíni és az Azure-beli virtuális gépeket is felveheti forrásként. Ezek a kapcsolati figyelők a végpontokhoz való kapcsolódás figyelésére is használhatók. A végpontok lehetnek az Azure-ban vagy bármely más URL-címen vagy IP-címen.

A Csatlakozáskezelő (előzetes verzió) a következő entitásokat tartalmazza:

* **Csatlakozáskezelő erőforrás** – régióra jellemző Azure-erőforrás. A következő entitások mindegyike egy Csatlakozáskezelő erőforrás tulajdonságait képezi.
* **Endpoint (végpont** ) – a kapcsolati ellenőrzésekben részt vevő forrás vagy cél. Ilyen végpontok például az Azure-beli virtuális gépek, a helyszíni ügynökök, az URL-címek és az IP-címek.
* **Teszt konfiguráció** – egy adott protokollra jellemző konfiguráció. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztelési gyakoriságot és az egyéb paramétereket.
* **Test Group (tesztelési csoport** ) – a forrás-végpontokat, a célként megadott végpontokat és a tesztelési konfigurációkat tartalmazó csoport. Egy Csatlakozáskezelő több tesztelési csoportot is tartalmazhat.
* **Test (teszt** ) – a forrás-végpont, a cél végpont és a tesztelési konfiguráció kombinációja. A teszt a legrészletesebben használt szint, amelyen a figyelési adatokat elérhetővé kell tennie. A figyelési adatmennyiség magában foglalja a sikertelen ellenőrzések százalékos arányát és az oda-és visszaút idejét (RTT).

    ![A kapcsolati figyelőt bemutató diagram, a tesztelési csoportok és tesztek közötti kapcsolat meghatározása](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>A minta ARM-sablonnal létrehozandó lépések

A következő kód használatával hozzon létre egy ARMClient a kapcsolódási figyelő használatával.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

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

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

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

## <a name="description-of-properties"></a>Tulajdonságok leírása

* connectionMonitorName – a Csatlakozáskezelő erőforrás neve

* Annak az előfizetésnek az előfizetési azonosítója, amelyben a kapcsolódási figyelőt létre szeretné hozni

* NW – Network Watcher erőforrás-azonosító, amelyben a rendszer létrehozza a CM-t 

* hely – régió, amelyben a rendszer létrehozza a figyelőt

* Végpontok
    * név – az egyes végpontok egyedi neve
    * resourceId – az Azure-végpontok esetében az erőforrás-azonosító a virtuális gépekhez tartozó Azure Resource Manager (ARM) erőforrás-AZONOSÍTÓra hivatkozik. Nem Azure-végpontok esetén az erőforrás-azonosító a nem Azure-ügynökökhöz kapcsolódó Log Analytics munkaterület ARM-erőforrás-AZONOSÍTÓját jelöli.
    * címe – csak akkor alkalmazható, ha nincs megadva erőforrás-azonosító, vagy ha az erőforrás-azonosító Log Analytics munkaterület. Ha Log Analytics erőforrás-AZONOSÍTÓval használja, akkor a figyeléshez használható ügynök teljes tartománynevére utal. Ha erőforrás-azonosító nélkül használja, akkor ez lehet bármely nyilvános végpont URL-címe vagy IP-címe.
    * szűrő – nem Azure-végpontok esetén a szűrő használatával válassza ki az ügynököket Log Analytics munkaterületről, amelyet a rendszer a Csatlakozáskezelő erőforrás figyeléséhez fog használni. Ha a szűrők nincsenek beállítva, akkor a Log Analytics munkaterülethez tartozó összes ügynök használható figyelésre.
        * típus – típus megadása "ügynök címe"
        * címterület – a helyszíni ügynök teljes tartománynevének megadása

* Tesztelési csoportok
    * név – a tesztelési csoport neve.
    * testConfigurations – tesztelési konfigurációk, amelyek alapján a forrás-végpontok a cél végpontokhoz csatlakoznak
    * források – válasszon a fent létrehozott végpontok közül. Az Azure-alapú forrás-végpontoknak telepíteniük kell az Azure Network Watcher bővítményt, és a nem Azure-alapú forrás-végpontoknak telepíteniük kell a haveAzure Log Analytics-ügynököt. A forráshoz tartozó ügynök telepítéséhez tekintse meg a [figyelési ügynökök telepítése](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)című témakört.
    * célhelyek – válasszon a fent létrehozott végpontok közül. Az Azure-beli virtuális gépekhez vagy bármely végponthoz (nyilvános IP-címhez, URL-címhez vagy FQDN-hez) való csatlakozást célhelyként megadható módon figyelheti. Egyetlen tesztelési csoportban Azure-beli virtuális gépeket, Office 365 URL-címeket, Dynamics 365 URL-címeket és egyéni végpontokat adhat hozzá.
    * letiltás – ezzel a mezővel letilthatja az összes olyan forrás és cél figyelését, amelyet a tesztelési csoport meghatároz.

* Tesztelési konfigurációk
    * név – a teszt konfigurációjának neve.
    * testFrequencySec – azt határozza meg, hogy a források milyen gyakran fognak pingelni a protokollon és a megadott porton. 30 másodpercet, 1 percet, 5 percet, 15 percet vagy 30 percet is kiválaszthat. A források a választott érték alapján próbálják meg a célokhoz való kapcsolódást. Ha például 30 másodpercet választ, a források legalább egyszer ellenőrizhetik a kapcsolatot a célhoz, 30 másodpercen belül.
    * protokoll – a TCP, az ICMP, a HTTP vagy a HTTPS lehetőség közül választhat. A protokolltól függően egyes protokoll-specifikus konfigurációk is megadhatók
        * preferHTTPS – adja meg, hogy HTTPS protokollt használ-e HTTP-n keresztül
        * Port – adja meg az Ön által választott cél portot.
        * disableTraceRoute – ez olyan tesztelési csoportokra vonatkozik, amelyek TCP vagy ICMP protokollal rendelkeznek. Leállítja a forrásokat a topológia és a hop-by-hop RTT felfedéséhez.
    * successThreshold – a következő hálózati paraméterek küszöbértékeit állíthatja be:
        * checksFailedPercent – az ellenőrzések százalékos arányának beállítása, ha a forrás a megadott feltételek alapján ellenőrzi a csatlakozást a célhelyekhez. A TCP vagy az ICMP protokoll esetében a sikertelen ellenőrzések százalékaránya a csomagok elvesztésének százalékában is egyenlő lehet. HTTP protokoll esetén ez a mező a nem válaszoló HTTP-kérelmek százalékos arányát jelöli.
        * roundTripTimeMs – állítsa be a RTT ezredmásodpercben, hogy mennyi ideig tart a források kapcsolódása a célhoz a tesztelési konfiguráción keresztül.

## <a name="scale-limits"></a>Skálázási korlátok

A hálózati figyelők a következő skálázási korlátokkal rendelkeznek:

* Az előfizetések maximális száma régiónként: 100
* Tesztelési csoportok maximális száma kapcsolatonként: 20
* Maximális források és célhelyek kapcsolatonként: 100
* A tesztelési konfigurációk maximális száma kapcsolatonként: 20 ARMClient-n keresztül

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan elemezheti a figyelési és riasztási információkat](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Útmutató a [hálózatban felmerülő problémák diagnosztizálásához](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)

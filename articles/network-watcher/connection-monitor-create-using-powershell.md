---
title: Csatlakozáskezelő létrehozása – PowerShell
titleSuffix: Azure Network Watcher
description: Ismerje meg, hogyan hozhatja létre a kapcsolódási figyelőt a PowerShell használatával.
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
ms.openlocfilehash: fa8b2d967a336343d23c5f6aa4477ebcf2396407
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949037"
---
# <a name="create-a-connection-monitor-using-powershell"></a>Figyelő létrehozása a PowerShell használatával

Ismerje meg, hogyan hozhat létre kapcsolati figyelőt az erőforrások közötti kommunikáció figyeléséhez a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek 

A Csatlakozáskezelőben létrehozott kapcsolódó figyelők esetében a helyszíni és az Azure-beli virtuális gépeket is felveheti forrásként. Ezek a kapcsolati figyelők a végpontokhoz való kapcsolódás figyelésére is használhatók. A végpontok lehetnek az Azure-ban vagy bármely más URL-címen vagy IP-címen.

A Csatlakozáskezelő a következő entitásokat tartalmazza:

* **Csatlakozáskezelő erőforrás** – régióra jellemző Azure-erőforrás. A következő entitások mindegyike egy Csatlakozáskezelő erőforrás tulajdonságait képezi.
* **Endpoint (végpont** ) – a kapcsolati ellenőrzésekben részt vevő forrás vagy cél. Ilyen végpontok például az Azure-beli virtuális gépek, a helyszíni ügynökök, az URL-címek és az IP-címek.
* **Teszt konfiguráció** – egy adott protokollra jellemző konfiguráció. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztelési gyakoriságot és az egyéb paramétereket.
* **Test Group (tesztelési csoport** ) – a forrás-végpontokat, a célként megadott végpontokat és a tesztelési konfigurációkat tartalmazó csoport. Egy Csatlakozáskezelő több tesztelési csoportot is tartalmazhat.
* **Test (teszt** ) – a forrás-végpont, a cél végpont és a tesztelési konfiguráció kombinációja. A teszt a legrészletesebben használt szint, amelyen a figyelési adatokat elérhetővé kell tennie. A figyelési adatmennyiség magában foglalja a sikertelen ellenőrzések százalékos arányát és az oda-és visszaút idejét (RTT).

    ![A kapcsolati figyelőt bemutató diagram, a tesztelési csoportok és tesztek közötti kapcsolat meghatározása](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>A létrehozás lépései a PowerShell használatával

A következő parancsokkal hozhat létre egy kapcsolódási figyelőt a PowerShell használatával.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Tulajdonságok leírása

* connectionMonitorName – a Csatlakozáskezelő erőforrás neve

* Annak az előfizetésnek az előfizetési azonosítója, amelyben a kapcsolódási figyelőt létre szeretné hozni

* NW – Network Watcher erőforrás-azonosító, amelyben a rendszer létrehozza a CM-t 

* hely – régió, amelyben a rendszer létrehozza a figyelőt

* Végpontok
    * név – az egyes végpontok egyedi neve
    * resourceId – az Azure-végpontok esetében az erőforrás-azonosító a virtuális gépek Azure Resource Manager erőforrás-AZONOSÍTÓJÁRA hivatkozik. Nem Azure-végpontok esetén az erőforrás-azonosító az Azure Resource Manager erőforrás-AZONOSÍTÓját jelöli a nem Azure-ügynökökhöz társított Log Analytics munkaterülethez.
    * címe – csak akkor alkalmazható, ha nincs megadva erőforrás-azonosító, vagy ha az erőforrás-azonosító Log Analytics munkaterület. Ha Log Analytics erőforrás-AZONOSÍTÓval használja, akkor a figyeléshez használható ügynök teljes tartománynevére utal. Ha erőforrás-azonosító nélkül használja, akkor ez lehet bármely nyilvános végpont URL-címe vagy IP-címe.
    * szűrő – nem Azure-végpontok esetén a szűrő használatával válassza ki az ügynököket Log Analytics munkaterületről, amelyet a rendszer a Csatlakozáskezelő erőforrás figyeléséhez fog használni. Ha a szűrők nincsenek beállítva, akkor a Log Analytics munkaterülethez tartozó összes ügynök használható figyelésre.
        * típus – típus megadása "ügynök címe"
        * címterület – a helyszíni ügynök teljes tartománynevének megadása

* Tesztelési csoportok
    * név – a tesztelési csoport neve.
    * testConfigurations – tesztelési konfigurációk, amelyek alapján a forrás-végpontok a cél végpontokhoz csatlakoznak
    * források – válasszon a fent létrehozott végpontok közül. Az Azure-alapú forrás-végpontoknak telepíteniük kell az Azure Network Watcher bővítményt, és a nem Azure-alapú forrás-végpontoknak telepíteniük kell a haveAzure Log Analytics-ügynököt. A forráshoz tartozó ügynök telepítéséhez tekintse meg a [figyelési ügynökök telepítése](./connection-monitor-overview.md#install-monitoring-agents)című témakört.
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
* A tesztelési konfigurációk maximális száma kapcsolatonként: 20

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan elemezheti a figyelési és riasztási információkat](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Útmutató a [hálózatban felmerülő problémák diagnosztizálásához](./connection-monitor-overview.md#diagnose-issues-in-your-network)
---
title: Az Azure Data Explorer-fürt hozzáférésének, betöltésének és működésének a virtuális hálózatban való hibaelhárítása
description: Az Azure Data Explorer-fürt kapcsolatának, betöltésének, fürtlétrehozásának és működésének a virtuális hálózatban való hibaelhárítása
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241659"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Az Azure Data Explorer-fürt hozzáférésének, betöltésének és működésének a virtuális hálózatban való hibaelhárítása

Ebben a szakaszban megtudhatja, hogyan háríthatja el a [virtuális hálózatra](/azure/virtual-network/virtual-networks-overview)telepített fürt kapcsolódási, üzemeltetési és fürtlétrehozási problémáit.

## <a name="access-issues"></a>Hozzáférési problémák

Ha a nyilvános (cluster.region.kusto.windows.net) vagy a privát (private-cluster.region.kusto.windows.net) végpont használatával történő fürt elérésekor probléma merül fel, és azt gyanítja, hogy az a virtuális hálózat beállításához kapcsolódik, hajtsa végre a következő lépéseket a elháríthatja a problémát.

### <a name="check-tcp-connectivity"></a>TCP-kapcsolat ellenőrzése

Az első lépés a TCP-kapcsolat ellenőrzése Windows vagy Linux operációs rendszer használatával.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Töltse le a [TCping-et](https://www.elifulkerson.com/projects/tcping.php) a fürthöz csatlakozó gépre.
   2. Pingelje a célt a forrásgépről a következő paranccsal:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. *Netcat* telepítése a fürthöz csatlakozó gépbe

    ```bash
    $ apt-get install netcat
     ```

   2. Pingelje a célt a forrásgépről a következő paranccsal:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Ha a teszt sikertelen, folytassa az alábbi lépésekkel. Ha a teszt sikeres, a problémát nem tcp-kapcsolati probléma miatt kell el. Lépjen rá a [működési problémákra](#cluster-creation-and-operations-issues) a további hibaelhárításhoz.

### <a name="check-the-network-security-group-nsg"></a>A hálózati biztonsági csoport (NSG) ellenőrzése

   Ellenőrizze, hogy a fürt alhálózatához csatlakoztatott [hálózati biztonsági csoport](/azure/virtual-network/security-overview) (NSG) rendelkezik-e olyan bejövő szabállyal, amely lehetővé teszi a hozzáférést az ügyfélgép IP-címéről a 443-as porthoz.

### <a name="check-route-table"></a>Útvonaltábla ellenőrzése

   Ha a fürt alhálózata kényszeríti a bújtatási beállításokat a tűzfalhoz (alhálózat, amely a '0.0.0/0' alapértelmezett [útvonalat](/azure/virtual-network/virtual-networks-udr-overview) tartalmazó útvonaltáblát tartalmazza), győződjön meg arról, hogy a gép IP-címe rendelkezik a VirtualNetwork/Internet [következő ugrástípussal](/azure/virtual-network/virtual-networks-udr-overview) rendelkező útvonallal. Ez az útvonal szükséges az aszimmetrikus útvonalproblémák megelőzéséhez.

## <a name="ingestion-issues"></a>Lenyelési problémák

Ha betöltési problémákat tapasztal, és azt gyanítja, hogy az a virtuális hálózat beállításához kapcsolódik, hajtsa végre a következő lépéseket.

### <a name="check-ingestion-health"></a>A betöltés állapotának ellenőrzése

Ellenőrizze, hogy a [fürt betöltési metrikák](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) kifogástalan állapotot jeleznek-e.

### <a name="check-security-rules-on-data-source-resources"></a>Adatforrás-erőforrások biztonsági szabályainak ellenőrzése

Ha a metrikák azt jelzik, hogy az adatforrásból *(Esemény/IoT* hubok feldolgozott események metrikája) nem történt meg, győződjön meg arról, hogy az adatforrás-erőforrások (Event Hub vagy Storage) engedélyezik a hozzáférést a fürt alhálózatából a tűzfalszabályokban vagy a szolgáltatásvégpontokban.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>A fürt alhálózatán konfigurált biztonsági szabályok ellenőrzése

Győződjön meg arról, hogy a fürt alhálózata nsg-, UDR- és tűzfalszabályokkal rendelkezik. Ezenkívül tesztelje a hálózati kapcsolatot az összes függő végponthoz. 

## <a name="cluster-creation-and-operations-issues"></a>Fürtlétrehozási és műveleti problémák

Ha fürtlétrehozási vagy -üzemeltetési problémákat tapasztal, és azt gyanítja, hogy az a virtuális hálózat beállításához kapcsolódik, kövesse az alábbi lépéseket a probléma elhárításához.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>A virtuális hálózat diagnosztizálása a REST API-val

Az [ARMClient](https://chocolatey.org/packages/ARMClient) a REST API-t a PowerShell használatával hívja meg. 

1. Bejelentkezés armclient nal

   ```powerShell
   armclient login
   ```

1. Diagnosztizálási művelet meghívása

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Ellenőrizze a választ

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Várakozás a művelet befejezésére

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Várjon, amíg az *állapottulajdonság* *befejeződött*, majd a *tulajdonságok* mezőben a következők jelennek meg:

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Ha a *Megállapítások* tulajdonság üres eredményt jelenít meg, az azt jelenti, hogy az összes hálózati teszt megfelelt, és nem megszakadt a kapcsolat. Ha a következő hiba jelenik meg, előfordulhat, hogy a *kimenő függőségi szolgáltatás "{dependencyName}:{port}" nem teljesül (Kimenő),* és a fürt nem tudja elérni a függő szolgáltatásvégpontokat. Folytassa a következő lépésekkel.

### <a name="check-network-security-group-nsg"></a>Hálózati biztonsági csoport ellenőrzése (NSG)

Győződjön meg arról, hogy a [hálózati biztonsági csoport](/azure/virtual-network/security-overview) megfelelően van konfigurálva a Virtuális hálózat telepítéséhez szükséges [függőségek című](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment) részben található utasítások szerint

### <a name="check-route-table"></a>Útvonaltábla ellenőrzése

Ha a fürt alhálózata tűzfalhoz van beállítva a kényszerbújtatás (a "0.0.0.0/0" alapértelmezett [útvonalat](/azure/virtual-network/virtual-networks-udr-overview) tartalmazó útvonallal rendelkező alhálózat) győződjön meg arról, hogy a [felügyeleti IP-címek](vnet-deployment.md#azure-data-explorer-management-ip-addresses)) és az [állapotfigyelő IP-címek](vnet-deployment.md#health-monitoring-addresses) útvonala [a következő ugrástípusú](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *internettel,* valamint a *"management-ip/32"* és az *"health-monitoring-ip/32"* [forráscímelőtaggal](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) rendelkezik. Ez az útvonal szükséges az aszimmetrikus útvonalproblémák megelőzéséhez.

### <a name="check-firewall-rules"></a>Tűzfalszabályok ellenőrzése

Ha a tűzfalhoz kényszeríti az alagút alhálózati kimenő forgalmát, győződjön meg arról, hogy az összes függőség fqdn (például *.blob.core.windows.net)* engedélyezett a tűzfal konfigurációjában a [tűzfallal való biztonságossá tétele](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)című részen leírtak szerint.

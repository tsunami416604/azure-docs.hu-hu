---
title: Az Azure Load Balancer elosztási módjának konfigurálása
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Load Balancer terjesztési módját a forrás IP-affinitás támogatásához.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: ddccd02e7157792d942309ae4f74933322f246f9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225376"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Az Azure Load Balancer az elosztási módjának konfigurálása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Kivonat-alapú terjesztési mód

A Azure Load Balancer alapértelmezett terjesztési módja egy öt rekordos kivonat. 

A rekord a következőkből áll:
* **Forrás IP-címe**
* **Forrásport**
* **Cél IP-címe**
* **Célport**
* **Protokoll típusa**

A kivonat használatával képezhető le a forgalom az elérhető kiszolgálókra. Az algoritmus csak a szállítási munkameneten belül nyújt stickiet. Az azonos munkamenetben lévő csomagokat ugyanahhoz az adatközponti IP-címhez irányítja a rendszer, amely a terheléselosztási végpont mögött található. Amikor az ügyfél egy új munkamenetet indít el ugyanabból a forrás IP-címről, a forrásport megváltozik, és a forgalom egy másik adatközpont-végpontra lép.

![Öt rekordos kivonatoló terjesztési mód](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Forrás IP-affinitás módban

A terheléselosztó a forrás IP-affinitás elosztási módjának használatával is konfigurálható. Ez a terjesztési mód munkamenet-affinitást, vagy az ügyfél IP-affinitás is nevezik. A mód egy kétrekordos (forrás IP-cím és cél IP-cím) vagy három rekordos (forrás IP-cím, cél IP-cím és protokolltípus) kivonatot használ a forgalom az elérhető kiszolgálókra történő leképezéséhez. A forrás IP-affinitás használatával az azonos ügyfélszámítógépről indított kapcsolatok ugyanarra az adatközpont-végpontra kerülnek.

Az alábbi ábrán két rekordos konfiguráció látható. Figyelje meg, hogyan futnak a két rekord a terheléselosztó használatával az 1. virtuális gépen (VM1). A VM1 majd biztonsági másolatot készít a VM2 és vm3 virtuális gép.

![Kétrekordos munkamenet-affinitás terjesztési módja](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Forrás IP-affinitás módban oldja meg az Azure Load Balancer és a távoli asztali átjáró (RD átjáró) között inkompatibilitás. Ez a mód használatával hozhat létre egy távoli asztali átjáró farm egyetlen felhőszolgáltatásban.

Egy másik használatieset-forgatókönyvek feltöltés media. Az adatfeltöltés UDP keresztül történik, de a vezérlősík TCP keresztül valósul meg:

* Az ügyfél TCP-munkamenetet indít el a terheléselosztási nyilvános címről, és egy adott DIP-re irányítja. A csatorna marad aktív, a kapcsolati állapotának figyeléséhez.
* Az azonos ügyfélszámítógépről származó új UDP-munkamenet ugyanarra a terheléselosztásos nyilvános végpontra van elindítva. A kapcsolat a azonos DIP-végpont, az előző TCP-kapcsolat van átirányítva. A media feltöltés a vezérlőcsatorna keresztül TCP fenntartása mellett nagy adatátviteli kapacitással hajthatók végre.

> [!NOTE]
> Ha egy elosztott terhelésű készlet eltávolításával vagy egy virtuális gép hozzáadása, ügyfél-kérések recomputed van. Új kapcsolatokat a meglévő ügyfeleket, hogy a kiszolgálón, végül nem függhet. Ezenkívül forrás IP-cím használatával kapcsolat elosztási módjának okozhat egy eltérő forgalom elosztását. Proxy mögötti futtató ügyfelek akkor látható, egy egyedi ügyfél-alkalmazásként.

## <a name="configure-source-ip-affinity-settings"></a>Forrás IP-affinitás beállítások konfigurálása

### <a name="azure-portal"></a>Azure Portal

A terjesztési mód konfigurációját módosíthatja a portál terheléselosztási szabályának módosításával.

1. Jelentkezzen be a Azure Portalba, és keresse meg a módosítani kívánt terheléselosztó tartalmazó erőforráscsoportot az **erőforráscsoportok**lehetőségre kattintva.
2. A terheléselosztó áttekintése képernyőn kattintson a **Beállítások**területen található **terheléselosztási szabályok** elemre.
3. A terheléselosztási szabályok képernyőn kattintson arra a terheléselosztási szabályra, amelyre módosítani szeretné a terjesztési módot.
4. A szabály alatt a terjesztési mód módosul a **munkamenet-megőrzés** legördülő lista módosításával.  A következő lehetőségek érhetők el:
    
    * **Nincs (kivonat-alapú)** – azt határozza meg, hogy egy adott ügyfél egymást követő kérelmeit bármely virtuális gép kezelheti.
    * **Ügyfél IP-címe (forrás IP-affinitás 2-rekord)** – azt határozza meg, hogy ugyanazon ügyfél IP-címéről érkező egymást követő kérelmeket ugyanaz a virtuális gép fogja kezelni.
    * **Ügyfél IP-címe és protokollja (forrás IP-affinitás 3 – rekord)** – azt határozza meg, hogy az azonos ügyfél IP-címe és a protokoll kombinációja egymást követő kérelmeket ugyanazzal a virtuális géppel fogja kezelni.

5. Válassza ki a terjesztési módot, majd kattintson a **Mentés**gombra.

### <a name="azure-powershell"></a>Azure PowerShell

A Resource Managerrel üzembe helyezett virtuális gépek esetében a PowerShell használatával módosítsa a terheléselosztó terjesztési beállításait egy meglévő terheléselosztási szabályon. A következő parancs frissíti a terjesztési módot: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Klasszikus virtuális gépek esetében az Azure PowerShell használatával módosíthatja a telepítési beállításokat. Egy Azure-végpont hozzáadása egy virtuális gépet, és a terheléselosztó elosztási módjának konfigurálása:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Állítsa be a `LoadBalancerDistribution` elem értékét a szükséges terheléselosztási mennyiséghez. SourceIP megadása két rekordos (forrás IP-cím és cél IP-cím) terheléselosztáshoz. Adja meg a sourceIPProtocol a három rekordos (forrás IP-cím, cél IP-cím és protokoll típusa) terheléselosztáshoz. Az öt rekordos terheléselosztás alapértelmezett működéséhez válassza a nincs értéket.

Ezek a beállítások használatával kérje le egy végpont terheléselosztói terjesztési mód konfigurációban:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Ha a `LoadBalancerDistribution` elem nincs jelen, Azure Load Balancer az alapértelmezett öt rekordos algoritmust használja.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Az elosztott terhelésű végpont készletének elosztási módjának konfigurálása

Ha a végpontok egy elosztott terhelésű végpont készletének része, a terjesztési mód az elosztott terhelésű végpont készletének kell konfigurálni:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>A Cloud Services végpontjainak elosztási módjának konfigurálása

Az Azure SDK for .NET 2.5 segítségével a felhőszolgáltatása frissítését. A Cloud Services végpontbeállításokat a .csdef fájlban menjenek végbe. Ha frissíteni szeretné a terheléselosztó elosztási módjának Cloud Services üzembe helyezéséhez, szükség egy üzemelő példány frissítése.

Íme egy példa a végpont beállításainak módosítása a .csdef:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API-példa

Az alábbi példa bemutatja, hogyan konfigurálja újra a terheléselosztó elosztási módjának a központi telepítésben megadott elosztott terhelésű készlet. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Elosztott terhelésű készlet telepített terjesztési mód váltása

A klasszikus Azure üzemi modell segítségével módosíthatja egy meglévő központi telepítési konfigurációt. Adja hozzá a `x-ms-version` fejlécet, és állítsa az értéket a 2014-09-01-es vagy újabb verzióra.

#### <a name="request"></a>Kérés

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Az előzőekben leírtak szerint állítsa be a `LoadBalancerDistribution` elemet a kétrekordos affinitás sourceIP, a sourceIPProtocol a három rekordos affinitáshoz, vagy egyiket sem, ha nincs affinitás (öt rekordos affinitás).

#### <a name="response"></a>Válasz

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Következő lépések

* [Az Azure belső Load Balancer áttekintése](load-balancer-internal-overview.md)
* [Ismerkedés az internetre irányuló terheléselosztó konfigurálásával](load-balancer-get-started-internet-arm-ps.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)

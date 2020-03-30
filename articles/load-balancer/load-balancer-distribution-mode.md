---
title: Az Azure Load Balancer terjesztési módjának konfigurálása
titleSuffix: Azure Load Balancer
description: Ebben a cikkben az Azure Load Balancer telepítési módjának konfigurálása a forrás IP-affinitásának támogatásához.
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
ms.openlocfilehash: 5c50186692438be5d0922cd329c28e665310e5c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023531"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Az Azure Load Balancer terjesztési módjának konfigurálása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Kivonatalapú terjesztési mód

Az Azure Load Balancer alapértelmezett eloszlási módja az ötrekordos kivonat. 

A tuple a következőkből áll:
* **Forrás IP-címe**
* **Forrásport**
* **Cél IP-címe**
* **Célport**
* **Protokoll típusa**

A kivonat a forgalom leképezésére szolgál a rendelkezésre álló kiszolgálókhoz. Az algoritmus csak az átviteli munkameneten belül biztosítja a ragacsosságot. Az ugyanabban a munkamenetben lévő csomagok a terheléselosztási végpont mögött ugyanarra az adatközpontI IP-re vannak irányítva. Amikor az ügyfél új munkamenetet indít ugyanabból a forrásIP-címből, a forrásport megváltozik, és a forgalom egy másik adatközpont-végpontra kerül.

![Öthangú kivonatalapú terjesztési mód](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Forrás IP-affinitási módja

A terheléselosztó a forrás IP-affinitás elosztási mód használatával is konfigurálható. Ezt az elosztási módot munkamenet-affinitásnak vagy ügyfél-IP-cím affinitásnak is nevezik. A mód két-törzsből (forrás IP és cél IP) vagy három-tuple (forrás IP, cél IP és protokoll típus) kivonatot használ a forgalom leképezéséhez az elérhető kiszolgálókhoz. A forrás IP-affinitásának használatával az ugyanarról az ügyfélszámítógépről indított kapcsolatok ugyanarra az adatközpont-végpontra kerülnek.

Az alábbi ábra egy kétsávos konfigurációt mutat be. Figyelje meg, hogy a két-tuple fut át a terheléselosztó a virtuális gép 1 (VM1). VM1 ezután a VM2 és a VM3 biztonsági másolatot.

![Kéthangú munkamenet affinitáselosztási mód](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

A forrás IP-affinitási mód megoldja az Azure Load Balancer és a Távoli asztali átjáró (Távoli asztali átjáró) közötti inkompatibilitást. Ezzel a móddal egyetlen felhőszolgáltatásban hozhat létre egy Távoli asztali átjárófarmot.

Egy másik használati eset a médiafeltöltés. Az adatok feltöltése UDP-n keresztül történik, de a vezérlősík a TCP-n keresztül érhető el:

* Az ügyfél tcp-munkamenetet indít a terheléselosztásos nyilvános címre, és egy adott DIP-re irányítja. A csatorna aktív marad a kapcsolat állapotának figyeléséhez.
* Ugyanattól az ügyfélszámítógépről egy új UDP-munkamenet indul ugyanarra a terheléselosztásos nyilvános végpontra. A kapcsolat ugyanarra a DIP-végpontra irányul, mint az előző TCP-kapcsolat. Az adathordozó-feltöltés nagy átviteli sebességgel hajtható végre, miközben a TCP-n keresztül is fenntart egy vezérlőcsatornát.

> [!NOTE]
> Ha egy terhelés-kiegyensúlyozott készlet virtuális gép eltávolításával vagy hozzáadásával változik, az ügyfélkérelmek elosztása újraszámításra kerül. Nem függhet a meglévő ügyfelek új kapcsolataitól, hogy ugyanazon a kiszolgálón végezhet. Emellett a forrás IP-affinitás terjesztési mód használata egyenlőtlen forgalmat okozhat. A proxyk mögött futó ügyfelek egy egyedi ügyfélalkalmazásnak tekinthetők.

## <a name="configure-source-ip-affinity-settings"></a>Forrás IP-affinitási beállításainak konfigurálása

### <a name="azure-portal"></a>Azure portál

A terjesztési mód konfigurációját a portál terheléselosztási szabályának módosításával módosíthatja.

1. Jelentkezzen be az Azure Portalra, és keresse meg a módosítani kívánt terheléselosztót tartalmazó erőforráscsoportot az **Erőforráscsoportok**elemre kattintva.
2. A terheléselosztó áttekintő képernyőjén kattintson a **Terheléselosztási szabályok** elemre a **Beállítások területen.**
3. A terheléselosztási szabályok képernyőn kattintson a terjesztési módot módosítani kívánt terheléselosztási szabályra.
4. A szabály szerint a terjesztési mód a **Munkamenet-megőrzés legördülő** lista módosításával változik.  A következő beállítások érhetők el:
    
    * **Nincs (kivonat-alapú)** – Itt adhatja meg, hogy az ugyanazon ügyféltől érkező egymást követő kéréseket bármely virtuális gép kezelheti.
    * **Ügyfél IP-címe (forrás IP-affinitás 2-tüz)** – Itt adhatja meg, hogy az azonos ügyfél IP-címéről érkező egymást követő kérelmeket ugyanaz a virtuális gép kezelje.
    * **Ügyfél IP-címe és protokollja (forrás IP-affinitás 3-tuple)** – Itt adhatja meg, hogy az azonos ügyfél IP-címéről és protokollkombinációjáról érkező egymást követő kérelmeket ugyanaz a virtuális gép kezelje.

5. Válassza a terjesztési módot, majd kattintson a **Mentés gombra.**

### <a name="azure-powershell"></a>Azure PowerShell

Az Erőforrás-kezelővel telepített virtuális gépek esetén a PowerShell segítségével módosítsa a terheléselosztó terjesztési beállításait egy meglévő terheléselosztási szabályon. A következő parancs frissíti a terjesztési módot: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Klasszikus virtuális gépek esetén az Azure PowerShell segítségével módosíthatja a terjesztési beállításokat. Adjon hozzá egy Azure-végpontot egy virtuális géphez, és konfigurálja a terheléselosztó terjesztési módját:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Állítsa be az `LoadBalancerDistribution` elem értékét a szükséges terheléselosztás mennyiségéhez. Adja meg a forrásIP-t a kéthangú (forrás IP és a cél IP) terheléselosztáshoz. Adja meg a sourceIPProtocol protokollt a háromhangú (forrás IP, cél IP és protokolltípus) terheléselosztáshoz. Adjon meg egyet az öthangú terheléselosztás alapértelmezett viselkedéséhez.

Végpontterhelés-elosztó terjesztési mód konfigurációjának lekérése az alábbi beállításokkal:

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

Ha `LoadBalancerDistribution` az elem nincs jelen, az Azure Load Balancer az alapértelmezett öthangú algoritmust használja.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Elosztási mód konfigurálása a terheléselosztási végpontkészleten

Ha a végpontok egy terheléselosztással egyensúlyozott végpontkészlet részei, az elosztási módot a terheléselosztási végpontkészleten kell konfigurálni:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Terjesztési mód konfigurálása a Felhőszolgáltatások végpontjaihoz

A .NET 2.5-höz tartozó Azure SDK használatával frissítheti a felhőszolgáltatást. A Felhőszolgáltatások végpontbeállításai a .csdef fájlban találhatók. A felhőszolgáltatások központi telepítéséhez a terheléselosztó terjesztési módjának frissítéséhez központi telepítés szükséges.

Íme egy példa a végpontbeállítások .csdef változásaira:

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

## <a name="api-example"></a>PÉLDA API-ra

A következő példa bemutatja, hogyan konfigurálhatja újra a terheléselosztó terjesztési módját egy adott terheléselosztási készlethez egy központi telepítésben. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Az üzembe helyezett terheléselosztási halmaz elosztási módjának megváltoztatása

Az Azure klasszikus üzembe helyezési modell egy meglévő központi telepítési konfiguráció módosításához használja. Adja `x-ms-version` hozzá a fejlécet, és állítsa az értéket a 2014-09-01-es vagy újabb verzióra.

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

Ahogy korábban `LoadBalancerDistribution` leírtuk, állítsa be az elemet a sourceIP értékre a kéttónusú affinitás, a sourceIPProtocol háromtónusú affinitás, vagy nincs affinitás (öt-tuple affinitás) esetén.

#### <a name="response"></a>Válasz

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>További lépések

* [Az Azure belső terheléselosztó – áttekintés](load-balancer-internal-overview.md)
* [Ismerkedés az internetre néző terheléselosztó konfigurálásával](quickstart-create-standard-load-balancer-powershell.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)

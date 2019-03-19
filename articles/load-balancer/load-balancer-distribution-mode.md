---
title: Az Azure Load Balancer elosztási módjának konfigurálása
titlesuffix: Azure Load Balancer
description: Hogyan konfigurálható az Azure Load Balancerhez forrás IP-affinitás támogatásához a terjesztési mód.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: afa840bd0b48cc9df1e9711caa035b85e8ec3855
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883661"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Az Azure Load Balancer az elosztási módjának konfigurálása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Kivonat-alapú terjesztési mód

Az alapértelmezett telepítési mód az Azure Load Balancerhez 5 rekordos kivonatot. A rekord a forrás IP-címe, forrásport, cél IP-cím, céloldali port és protokoll típusa tevődik össze. A kivonatoló képezni a forgalmat az elérhető kiszolgálókhoz használható, és az algoritmus biztosít a tartós használat csak egy átviteli munkamenet belül. Csomagok, amelyek ugyanabban a munkamenetben adatközpont IP (DIP) ugyanazon az elosztott terhelésű végpont mögött irányítja. Amikor az ügyfél új munkamenet indítása az azonos forrás IP-címe, Forrásport módosítja, és a forgalmat, nyissa meg egy másik DIP-végpont.

![5 rekordos kivonat-alapú terjesztési mód](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Forrás IP-affinitás módban

Load Balancer is konfigurálható úgy, hogy a forrás IP-affinitás elosztási mód használatával. Ez a terjesztési mód munkamenet-affinitást, vagy az ügyfél IP-affinitás is nevezik. A módot használ egy 2 rekordos (forrás és cél IP-Címek) vagy 3 rekordos (forrás IP-címe, cél IP-címe és protokoll típusa) ujjlenyomat-forgalom leképezése a rendelkezésre álló kiszolgálók. Forrás IP-affinitás révén az ugyanarra az ügyfélszámítógépre felé indított kapcsolatok nyissa meg az azonos DIP-végpont.

Az alábbi ábra egy 2 rekordos konfiguráció. Figyelje meg, hogyan 2 rekordos fut a terheléselosztón keresztül a virtuális gép 1 (VM1 –). A VM1 majd biztonsági másolatot készít a VM2 és vm3 virtuális gép.

![2 rekordos munkamenet-affinitás elosztási mód](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Forrás IP-affinitás módban oldja meg az Azure Load Balancer és a távoli asztali átjáró (RD átjáró) között inkompatibilitás. Ez a mód használatával hozhat létre egy távoli asztali átjáró farm egyetlen felhőszolgáltatásban.

Egy másik használatieset-forgatókönyvek feltöltés media. Az adatfeltöltés UDP keresztül történik, de a vezérlősík TCP keresztül valósul meg:

* Ügyfél kezdeményezi egy TCP-munkamenet, nyilvános kiegyenlített terhelésű címre, és a egy adott dedikált IP-CÍMMEL van irányítva. A csatorna marad aktív, a kapcsolati állapotának figyeléséhez.
* Az azonos ügyfélszámítógépről egy új UDP új munkamenet indul azonos kiegyenlített terhelésű nyilvános végpontjára mutató. A kapcsolat a azonos DIP-végpont, az előző TCP-kapcsolat van átirányítva. A media feltöltés a vezérlőcsatorna keresztül TCP fenntartása mellett nagy adatátviteli kapacitással hajthatók végre.

> [!NOTE]
> Ha egy elosztott terhelésű készlet eltávolításával vagy egy virtuális gép hozzáadása, ügyfél-kérések recomputed van. Új kapcsolatokat a meglévő ügyfeleket, hogy a kiszolgálón, végül nem függhet. Ezenkívül forrás IP-cím használatával kapcsolat elosztási módjának okozhat egy eltérő forgalom elosztását. Proxy mögötti futtató ügyfelek akkor látható, egy egyedi ügyfél-alkalmazásként.

## <a name="configure-source-ip-affinity-settings"></a>Forrás IP-affinitás beállítások konfigurálása

A Resource Managerrel üzembe helyezett virtuális gépekhez a PowerShell használatával egy meglévő terheléselosztási szabály load balancer terjesztési beállításainak módosításához. Ez frissíti a terjesztési mód: 

```powershell
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Klasszikus virtuális gépek esetében az Azure PowerShell használatával módosíthatja a telepítési beállításokat. Egy Azure-végpont hozzáadása egy virtuális gépet, és a terheléselosztó elosztási módjának konfigurálása:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Az értékét állítsa be a `LoadBalancerDistribution` kívánt mennyisége terheléselosztást elem. Adja meg a 2 rekordos (forrás és cél IP-Címek) terheléselosztás sourceIP. 3 rekordos (forrás IP-címe, cél IP-címe és protokoll típusa) terheléselosztás sourceIPProtocol adja meg. Adja meg, nincs az 5-ször több terheléselosztási funkció alapértelmezett viselkedését.

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

Ha a `LoadBalancerDistribution` elem nem található, az Azure Load Balancer az alapértelmezett 5 rekordos algoritmust használ.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Az elosztott terhelésű végpont készletének elosztási módjának konfigurálása

Ha a végpontok egy elosztott terhelésű végpont készletének része, a terjesztési mód az elosztott terhelésű végpont készletének kell konfigurálni:

```azurepowershell
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

A klasszikus Azure üzemi modell segítségével módosíthatja egy meglévő központi telepítési konfigurációt. Adja hozzá a `x-ms-version` fejlécére, és állítsa be az 2014-09-01-es verzió vagy újabb.

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

Állítsa be az előzőleg leírtak szerint a `LoadBalancerDistribution` sourceIP 2 rekordos affinitás, 3 rekordos affinitáshoz sourceIPProtocol vagy nincs az affinitás nélküli (5-ször több kapcsolat) elemet.

#### <a name="response"></a>Válasz

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>További lépések

* [Az Azure belső Load Balancer áttekintése](load-balancer-internal-overview.md)
* [Ismerkedés az internetkapcsolattal rendelkező load balancer konfigurálása](load-balancer-get-started-internet-arm-ps.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)

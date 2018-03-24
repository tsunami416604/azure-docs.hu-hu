---
title: Azure Load Balancer terjesztési mód konfigurálása |} Microsoft Docs
description: Megtudhatja, hogyan állíthatja be a telepítési módot, a Azure Load Balancer forrás IP-kapcsolat támogatásához.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ae793bad9cef86158418eb87e0c38ee0370a6bd2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>A telepítési mód konfigurálása az Azure Load Balancer

## <a name="hash-based-distribution-mode"></a>Kivonat-alapú terjesztési mód

Az alapértelmezett telepítési Azure Load Balancer módja 5 rekordos kivonatát. A rekord a forrás IP-cím, a forrásport, a cél IP-cím, a célport és a protokolltípus tevődik össze. A kivonatoló szolgál a forgalom hozzárendelése az elérhető kiszolgálókhoz, és az algoritmus biztosít tölcsérútvonalak csak egy átviteli munkamenet belül. Csomagok, amelyek ugyanabban a munkamenetben van irányítva a datacenter IP-címet (DIP) példányt az elosztott terhelésű végpont mögött. Amikor az ügyfél egy új munkamenet indítása azonos a forrás IP-cím, a forrásport módosításai, és a forgalmat egy másik DIP végpont gomba.

![5 rekordos kivonat-alapú terjesztési mód](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Forrás IP-affinitási módja

Terheléselosztó is konfigurálható úgy, hogy a forrás IP-kapcsolat terjesztési módban. Ez a telepítési mód munkamenet affinitás vagy az ügyfél IP-kapcsolatot is nevezik. A módot használja (a forrás IP-cím és a cél IP-cím) 2-rekordot, vagy a 3-rekordot (forrás IP-címe, cél IP-címe és protokoll típusa) kivonat-forgalmat az elérhető kiszolgálókhoz hozzárendelni. Forrás IP-kapcsolat használatával az ugyanarra az ügyfélszámítógépre indított kapcsolatok nyissa meg az azonos DIP-végponthoz.

A következő ábra azt mutatja be, a 2-rekordot konfiguráció. Figyelje meg, hogyan a 2-rekordot futtatja keresztül a terheléselosztó virtuális géphez 1 (VM1). VM1 majd a biztonsági vm2 virtuális gépnek és VM3.

![2-rekordot munkamenet affinitás terjesztési mód](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Forrás IP-affinitási módja megoldja inkompatibilitás Azure Load Balancer és a távoli asztali átjáró (RD átjáró) között. Ezt a módot használ, egyetlen felhőszolgáltatásban egy távoli asztali átjáró-farmot hozhat létre.

Egy másik felhasználási forgatókönyve az adathordozó feltöltése. Az adatfeltöltés UDP keresztül történik, de a vezérlő vezérlősík sorrendekben TCP:

* Ügyfél indít el egy TCP-munkamenet az elosztott terhelésű nyilvános cím, és egy adott DIP van átirányítva. A csatorna marad aktív, a kapcsolat állapotának figyeléséhez.
* Egy új UDP-munkamenetet ugyanarra az ügyfélszámítógépre a indítható ugyanazon terhelésű nyilvános végponthoz. A kapcsolat a azonos DIP végpont, az előző TCP-kapcsolat van átirányítva. A media feltöltés magas teljesítmény: a vezérlőcsatorna keresztül TCP megőrzésével hajtható végre.

> [!NOTE]
> Ha egy elosztott terhelésű készlet vált, a virtuális gépek hozzáadásával, az ügyfélkérelmek terjesztését van recomputed. Új kapcsolatokat a meglévő ügyfelek jussanak ugyanazon a kiszolgálón nem függ. Emellett forrás IP-cím használatával terjesztési affinitású okozhat az egyenlőtlen terjesztési forgalom. Proxy mögött rendszerű ügyfelek akkor látható, egy egyedi ügyfél-alkalmazásként.

## <a name="configure-source-ip-affinity-settings"></a>Forrás IP-kapcsolat konfigurálása

A virtuális gépek Azure PowerShell módosításához használja az időtúllépés beállítása. Egy Azure-végpont hozzáadása a virtuális gép, és adja meg a terheléselosztó terheléselosztási mód:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Állítsa a `LoadBalancerDistribution` a kívánt memóriamennyiséget terheléselosztás eleme. Adja meg a 2-rekordot (forrás IP-cím és a cél IP-cím) terheléselosztás sourceIP. 3-rekordot (forrás IP-címe, cél IP-címe és protokoll típusa) terheléselosztás adjon sourceIPProtocol. Adja meg, nincs az alapértelmezett viselkedése 5 rekordos terheléselosztás.

Lekérni egy végpont terjesztési mód terheléselosztó ezekkel a beállításokkal:

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

Ha a `LoadBalancerDistribution` elem nincs jelen, Azure Load Balancer az alapértelmezett 5 rekordos algoritmust használ.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Az elosztott terhelésű végpont készletének terjesztési mód konfigurálása

Amikor a végpont egy elosztott terhelésű végpont készletének részét képezik, a telepítési mód az elosztott terhelésű végpont készletének kell konfigurálni:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Cloud Services végpontok terjesztési mód konfigurálása

Az Azure SDK for .NET 2.5 használatával a felhőalapú szolgáltatás frissítése. Cloud Services végpont beállításait a .csdef fájlban történik. A terheléselosztási mód terheléselosztó Felhőszolgáltatások központi telepítés frissítéséhez a központi telepítés frissítésének megadása kötelező.

Itt látható egy példa .csdef változásokat a végpont beállításait:

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

A következő példa bemutatja, hogyan konfigurálja újra a terheléselosztási mód terheléselosztó egy központi telepítésben megadott elosztott terhelésű készlethez. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Elosztott terhelésű készlet telepített terjesztési módjának módosítása

Az Azure klasszikus telepítési modell segítségével módosíthatja egy meglévő központi telepítés konfigurálása. Adja hozzá a `x-ms-version` fejlécet, és állítsa be az verzióra 2014-09-01 vagy újabb.

#### <a name="request"></a>Kérés

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

A korábban ismertetett, beállíthatja a `LoadBalancerDistribution` sourceIP 2-rekordot kapcsolat, a 3-rekordot kapcsolatára sourceIPProtocol vagy nincs affinitás nélküli (5 rekordos affinitás) elemet.

#### <a name="response"></a>Válasz

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>További lépések

* [Az Azure belső terheléselosztó áttekintése](load-balancer-internal-overview.md)
* [Első lépések egy internetre irányuló terheléselosztót konfigurálása](load-balancer-get-started-internet-arm-ps.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)

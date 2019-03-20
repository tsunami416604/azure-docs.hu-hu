---
title: Load Balancer TCP üresjárati időkorlát konfigurálása az Azure-ban
titlesuffix: Azure Load Balancer
description: Load Balancer TCP üresjárati időkorlát konfigurálása
services: load-balancer
documentationcenter: na
author: kumudd
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0c57eec4d739da13d98099a6b2f01fbf0ad0051c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857538"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>TCP üresjárati időkorlátjának az Azure Load Balancer konfigurálása

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Az alapértelmezett konfigurációban az Azure Load Balancerhez egy 4 perces üresjárati időkorlátot beállítás tartozik. Ha egy tétlen időszak hosszabb, mint az időtúllépési érték, nincs garancia arra, amely a TCP- vagy HTTP-munkamenetben megmarad az ügyfél és a felhőszolgáltatás között.

Ha a kapcsolat megszakad, az ügyfélalkalmazás a következő hibaüzenet jelenhet meg: "A kapcsolat lezárva: Egy kapcsolatot, amely a várt tartandó bezárta a kiszolgálóhoz."

Általános gyakorlat, hogy a keep-alive TCP. Ez az eljárás biztosítja, hogy a kapcsolat aktív hosszabb ideig. További információkért lásd: ezek [.NET-példáit](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Életben tartási engedélyezve van, és csomagok küldése a kapcsolat inaktív időszakai alatt. Ezek a életben tartási csomagok győződjön meg arról, hogy az üresjárati időkorlátja soha nem elérni, és a egy hosszú ideig változatlan marad a kapcsolatot.

Ez a beállítás csak a bejövő kapcsolatok esetében működik. A kapcsolat elvesztésének elkerüléséhez életben tartási TCP időközt kevesebb, mint az üresjárati időkorlát konfigurálása, vagy növelje az üresjárati időkorlát értékét. Az ilyen forgatókönyvek támogatása érdekében hozzáadtunk egy konfigurálható üresjárati időkorlát támogatása. Most már beállíthatja a 4 – 30 perc alatt az időtartam.

Életben tartási TCP jól működik olyan forgatókönyvekben, ahol akkumulátor-élettartamát nem egy korlátozás. Nem ajánlott a mobilalkalmazásokhoz. A TCP életben tartási mobilalkalmazás használatával a telep eszköz gyorsabban.

![A TCP időtúllépési](./media/load-balancer-tcp-idle-timeout/image1.png)

Az alábbi szakaszok ismertetik a virtuális gépek üresjárati időkorlátjának módosítása és a felhőszolgáltatások.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>A TCP-időtúllépést állítsa a példányszintű nyilvános IP-címek és 15 perc

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

A(z) `IdleTimeoutInMinutes` nem kötelező. Ha nincs beállítva, az alapértelmezett időtúllépési érték 4 perc. Az elfogadható időtúllépési tartománya 4 – 30 perc.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Az időtúllépést beállítani, ha egy Azure-végpont létrehozása egy virtuális gépen

A végpont a timeout beállítás módosításához használja a következőt:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Az üresjárati időkorlát konfigurálása lekéréséhez használja a következő parancsot:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>A TCP időtúllépési egy elosztott terhelésű végpont készletének beállítása

Ha a végpont egy elosztott terhelésű végpont készletének részét képezik, a TCP időtúllépési az elosztott terhelésű végpont készletének be kell állítani. Példa:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>A cloud services-időtúllépési beállításainak módosítása

Az Azure SDK segítségével a felhőszolgáltatása frissítését. A cloud services végpontbeállítások választja ki a .csdef fájl. Egy üzemelő példány frissítése a TCP időtúllépési számára egy felhőszolgáltatás üzemelő példányának frissítése szükséges. Egy kivétel az, ha a TCP időtúllépési csak a nyilvános IP-cím van megadva. Nyilvános IP-beállításokat a .cscfg fájlban, és frissítheti azokat a központi telepítési update és a frissítés.

A végpont beállításait a .csdef változások a következők:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

A nyilvános IP-címek a timeout beállítás a .cscfg változások a következők:

```xml
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

## <a name="rest-api-example"></a>Példa REST API-val

A TCP üresjárati időkorlát konfigurálhatja a service management API használatával. Győződjön meg arról, hogy a `x-ms-version` fejléc értéke verzió `2014-06-01` vagy újabb. A megadott kiegyenlített terhelésű vstupní koncové body egy központi telepítésben lévő összes virtuális gép konfigurációjának frissítése.

### <a name="request"></a>Kérés

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Válasz

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>További lépések

[Belső load balancer áttekintése](load-balancer-internal-overview.md)

[Ismerkedés az internetkapcsolattal rendelkező terheléselosztó konfigurálása](load-balancer-get-started-internet-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

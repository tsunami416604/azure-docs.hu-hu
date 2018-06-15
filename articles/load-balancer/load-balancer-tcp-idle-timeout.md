---
title: Konfigurálja a Load Balancer TCP időtúllépést |} Microsoft Docs
description: Konfigurálja a Load Balancer TCP üresjárati időtúllépés
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 4625c6a8-5725-47ce-81db-4fa3bd055891
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: f19ac77f7c7f7d4ab8909d628f9dcce08c07c928
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855228"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Azure Load Balancer TCP üresjárati időtúllépés beállításainak konfigurálása

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Az alapértelmezett konfiguráció esetén Azure Load Balancer rendelkezik egy üresjárati időtúllépés beállítás 4 perces. Ha adott ideig tartó tétlenség hosszabb, mint az időtúllépési értéket, nincs nem biztos, hogy a TCP- vagy HTTP-munkamenetben megmarad az ügyfél és a felhőalapú szolgáltatás között.

Ha a kapcsolat megszakad, az ügyfélalkalmazás jelenhet meg a következő hibaüzenet: "a kapcsolat lezárva: egy nyitott állapotban tartandó kapcsolatot bezárt a kiszolgáló."

Általános gyakorlat, hogy egy életben tartási TCP használja. Ez az eljárás tartja a kapcsolat aktív hosszabb ideig. További információkért lásd: ezek [.NET példák](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Életben tartási engedélyezve van, a rendszer küld csomagokat, a kapcsolat tétlen időszak. Ezek a életben tartási csomagok győződjön meg arról, hogy az üresjárati időkorlátot a rendszer soha nem éri el a kapcsolat megmarad, hosszú ideig.

Ez a beállítás csak a bejövő kapcsolatok működik. A kapcsolat elvesztésének elkerülése, konfigurálja a TCP életben tartási időközzel kisebb, mint az üresjárati időkorlát, vagy növelje az üresjárati időkorlát értékét. Az ilyen helyzetek feltételeinek megteremtésére, vettünk konfigurálható üresjárati időtúllépés támogatása. Beállíthatja azt az a 4-30 perces időtartamot.

TCP életben tartási forgatókönyvekben, ahol eszközakkumulátor élettartamának nem korlátozás esetén működik. Nem ajánlott, az alkalmazásokat. A TCP életben mobilalkalmazás használatával a telep eszköz gyorsabb.

![TCP-időtúllépés](./media/load-balancer-tcp-idle-timeout/image1.png)

Az alábbi szakaszok azt ismertetik, hogyan időtúllépést módosítása a virtuális gépek és a felhőalapú szolgáltatások.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurálja a TCP-időtúllépés a példányszintű nyilvános IP-cím és 15 perc

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

A(z) `IdleTimeoutInMinutes` nem kötelező. Ha nincs megadva, az alapértelmezett időtúllépési érték 4 perces. Elfogadható időtúllépés tartománya 4 – 30 perc.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Az üresjárati időkorlát be, ha egy virtuális gépen egy Azure-végpont létrehozása

Az időkorlát a végpont módosításához használja a következő:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Az üresjárati időkorlát konfigurációs lekéréséhez használja a következő parancsot:

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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>A TCP időtúllépési be egy elosztott terhelésű végpont készletének

Ha végpont egy elosztott terhelésű végpont készletének részét képezik, az TCP időtúllépési az elosztott terhelésű végpont készletének be kell állítani. Példa:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Cloud services időtúllépés beállításainak módosítása

Az Azure SDK használatával a felhőalapú szolgáltatás frissítése. A felhőszolgáltatások végpontbeállításokat elvégezte a .csdef fájlban. A központi telepítés frissítésének a felhőszolgáltatás üzemelő példányának TCP időtúllépés frissítése szükséges. Kivétel, ha a TCP időtúllépési csak a nyilvános IP-cím van megadva. Nyilvános IP-beállításokat a .cscfg fájlban, és frissítheti azokat a központi telepítés update és a frissítés.

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

## <a name="rest-api-example"></a>REST API – példa

A szolgáltatáskezelő API használatával konfigurálhatja a TCP üresjárati időkorlátját. Győződjön meg arról, hogy a `x-ms-version` fejléc értéke verzió `2014-06-01` vagy újabb. A megadott terhelésű bemeneti végpontja egy központi telepítésben lévő összes virtuális gép konfigurációjának frissítése.

### <a name="request"></a>Kérés

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Válasz

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

## <a name="next-steps"></a>Következő lépések

[Belső terheléselosztó áttekintése](load-balancer-internal-overview.md)

[Első lépések egy internetre irányuló terheléselosztót konfigurálása](load-balancer-get-started-internet-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

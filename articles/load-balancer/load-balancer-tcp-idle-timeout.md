---
title: Load Balancer TCP Üresjárati időkorlát konfigurálása az Azure-ban
titlesuffix: Azure Load Balancer
description: Load Balancer TCP Üresjárati időkorlát konfigurálása
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: b3df1ead7a3164ffd9a4b4acf8820d0f5b82cee3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274171"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>A TCP Üresjárati időkorlát beállításainak konfigurálása Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Az alapértelmezett konfigurációjában a Azure Load Balancer 4 perc üresjárati időtúllépési beállítással rendelkezik. Ha egy inaktivitási időtartam hosszabb az időtúllépési értéknél, nem garantálható, hogy a TCP-vagy HTTP-munkamenet az ügyfél és a felhőalapú szolgáltatás között marad.

Ha a csatlakozás be van zárva, az ügyfélalkalmazás a következő hibaüzenetet kaphatja: "A mögöttes kapcsolatok bezárultak: A kiszolgáló lezárta az életben tartani várt kapcsolatokat. "

Gyakori eljárás a TCP Keep-Alive használata. Ez a gyakorlat hosszabb ideig tart a kapcsolatok aktív állapotban. További információkért tekintse meg ezeket a [.net](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)-példákat. Ha a Keep-Alive engedélyezve van, a rendszer a csatlakozáskor inaktivitási időszakokban küld csomagokat. Ezek a életben tartási csomagok biztosítják, hogy az Üresjárati időkorlát értéke soha ne legyen elérhető, és a kapcsolat hosszabb ideig marad.

Ez a beállítás csak a bejövő kapcsolatok esetében működik. A kapcsolat elvesztésének elkerülése érdekében a TCP Keep-Alive értéket a tétlen időtúllépési beállításnál kisebb intervallummal kell konfigurálni, vagy növelje az Üresjárati időkorlát értékét. Ilyen helyzetekben a konfigurálható Üresjárati időkorlát támogatása is támogatott. Most már 4 és 30 perc közötti időtartamra is beállíthatja.

A TCP Keep-Alive jól működik olyan forgatókönyvek esetén, ahol az akkumulátorok élettartama nem korlátozás. Mobile-alkalmazásokhoz nem ajánlott. Ha egy TCP Keep-Alive protokollt használ a mobil alkalmazásban, az eszköz akkumulátora gyorsabban kiüríthető.

![TCP-időtúllépés](./media/load-balancer-tcp-idle-timeout/image1.png)

A következő szakaszok azt ismertetik, hogyan változtathatók meg a virtuális gépek és a Cloud Services üresjárati időtúllépési beállításai.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>A példány-szintű nyilvános IP-cím TCP-időtúllépésének beállítása 15 percre

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

A(z) `IdleTimeoutInMinutes` nem kötelező. Ha nincs beállítva, az alapértelmezett időtúllépés 4 perc. Az elfogadható időtúllépési tartomány 4 – 30 percet vesz igénybe.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Üresjárati időkorlát beállítása Azure-végpont virtuális gépen való létrehozásakor

Egy végpont időtúllépési beállításának módosításához használja a következőt:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Az Üresjárati időkorlát konfigurációjának lekéréséhez használja a következő parancsot:

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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Egy elosztott terhelésű végponton beállított TCP-Időtúllépés beállítása

Ha a végpontok egy elosztott terhelésű végpont készletének részét képezik, a TCP-időtúllépést be kell állítani a terheléselosztásos végpont-készletre. Példa:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Cloud Services időtúllépési beállításainak módosítása

Használhatja az Azure SDK-t a Cloud Service frissítéséhez. A. csdef fájlban a Cloud Services végpont-beállításait kell megtennie. A felhőalapú szolgáltatások telepítésének TCP-időtúllépését a központi telepítés frissítéséhez kell frissíteni. Kivételt képez, ha a TCP-időtúllépés csak egy nyilvános IP-címhez van megadva. A nyilvános IP-címek a. cscfg fájlban találhatók, és az üzembe helyezés frissítésével és frissítésével frissíthetik azokat.

A végponti beállítások. csdef változásai a következők:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

A nyilvános IP-címek időtúllépési beállításának. cscfg változásai a következők:

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

## <a name="rest-api-example"></a>REST API példa

A TCP üresjárati időkorlátot a Service Management API használatával konfigurálhatja. Győződjön meg arról, `x-ms-version` hogy a fejléc verziója `2014-06-01` vagy újabb. A megadott elosztott terhelésű bemeneti végpontok konfigurációjának frissítése a központi telepítésben lévő összes virtuális gépen.

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

[A belső Load Balancer áttekintése](load-balancer-internal-overview.md)

[Az internetre irányuló terheléselosztó konfigurálásának első lépései](load-balancer-get-started-internet-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

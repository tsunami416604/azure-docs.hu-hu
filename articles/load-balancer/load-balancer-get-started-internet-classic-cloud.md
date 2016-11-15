---
title: "Bevezetés az internetkapcsolattal rendelkező terheléselosztó felhőszolgáltatásokhoz történő létrehozásába klasszikus üzembe helyezési modellben | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó felhőszolgáltatásokhoz klasszikus üzembe helyezési modellben"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 0bb16f96-56a6-429f-88f5-0de2d0136756
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/17/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 171d5cd41d900b83c22e1db4bc514471a3d4b556

---

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Hozzon létre egy internetkapcsolattal rendelkező terheléselosztót felhőszolgáltatásokhoz

[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [azt is megismerheti, hogyan lehet internetkapcsolattal rendelkező terheléselosztót létrehozni az Azure Resource Manager használatával](load-balancer-get-started-internet-arm-cli.md).

A felhőszolgáltatások automatikusan terheléselosztóval vannak konfigurálva, és a szolgáltatásmodell segítségével szabhatók testre.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Terheléselosztó létrehozása a szolgáltatásdefiníciós fájl használatával

Az Azure SDK for .NET 2.5 segítségével végezheti a felhőszolgáltatása frissítését. A felhőszolgáltatások végpontbeállításait a [service definition](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef fájlban kell elvégezni.

Az alábbi példa bemutatja, hogyan van konfigurálva egy servicedefinition.csdef fájl egy felhőtelepítéshez:

A felhőbeli üzembe helyezés által generált .csdef-fájl kódrészletének ellenőrzésével megtekintheti a 10000, 10001 és 10002 portokon való HTTP-használathoz konfigurált külső végpontot.

```xml
    <ServiceDefinition name=“Tenant“>
       <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
      </WorkerRole>
    </ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Felhőszolgáltatások állapotinformációinak ellenőrzése a terheléselosztóban

Az alábbiakban egy állapotmintára látható példa:

```xml
    <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
    </LoadBalancerProbes>
```

A terheléselosztó egyesíti a végpontra és a mintavételre vonatkozó információkat, amelyek alapján létrehozza a szolgáltatás állapotának lekérdezéséhez használható URL-címet a következő formában: http://{virtuális gép dedikált IP-címe}:80/Probe.aspx.

A szolgáltatás rendszeres időközönként észleli a mintavételeket ugyanarról az IP-címről. Ez az állapotminta iránti kérelem annak a csomópontnak a gazdagépétől érkezik, amelyen a virtuális gép fut. A szolgáltatásnak HTTP 200 állapotkóddal kell válaszolnia ahhoz, hogy a terheléselosztó azt feltételezze, hogy a szolgáltatás kifogástalan állapotban van. Bármilyen más HTTP-állapotkód (például 503) közvetlenül kiveszi a virtuális gépet a rotációból.

A mintavétel definíciója a mintavétel gyakoriságát is szabályozza. A fenti esetben a terheléselosztó 5 másodpercenként vesz mintát a végpontról. Ha 10 másodpercig (két mintavételi időköz) nem érkezik pozitív válasz, a terheléselosztó feltételezi, hogy a mintavétel leállt, és a virtuális gépet kiveszi a rotációból. Hasonlóképpen, ha a terheléselosztó a szolgáltatást kiveszi a rotációból, és pozitív válasz érkezik, a szolgáltatást azonnal visszahelyezi a rotációba. Ha a szolgáltatás állapota a kifogástalan és a nem megfelelő között ingadozik, a terheléselosztó eldöntheti, hogy késleltesse-e a szolgáltatás újbóli visszahelyezését a rotációba addig, amíg több mintavétel esetében kifogástalannak nem minősül az állapota.

További információkért ellenőrizze az [állapotmintát](https://msdn.microsoft.com/library/azure/jj151530.aspx) a szolgáltatásdefiníciós sémában.

## <a name="next-steps"></a>Következő lépések

[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->



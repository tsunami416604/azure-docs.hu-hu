---
title: "Belső terheléselosztó létrehozása az Azure Cloud Serviceshez | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre belső terheléselosztó a PowerShell használatával a klasszikus üzembehelyezési modellben"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-service-management
ms.assetid: 57966056-0f46-4f95-a295-483ca1ad135d
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: 6616c26ede13919b94a098dc38bdd6e2f0fc0b5b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Bevezetés a belső terheléselosztó (klasszikus) felhőszolgáltatásokhoz történő létrehozásába

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Felhőszolgáltatások](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, [hogyan hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával](load-balancer-get-started-ilb-arm-ps.md).

## <a name="configure-internal-load-balancer-for-cloud-services"></a>Belső terheléselosztó konfigurálása a felhőszolgáltatásokhoz

A belső terheléselosztó használata virtuális gépek és felhőszolgáltatások esetén egyaránt támogatott. Egy regionális virtuális hálózaton kívül eső felhőszolgáltatásban létrehozott belső terheléselosztói végpont csak az adott felhőszolgáltatásban érhető el.

A belső terheléselosztó konfigurációját be kell állítania az alábbi mintában látható módon, amikor az első telepítést létrehozza a felhőszolgáltatásban.

> [!IMPORTANT]
> Az alábbi lépések futtatásának előfeltétele, hogy a felhőtelepítéshez már létre legyen hozva egy virtuális hálózat. A belső terheléselosztás létrehozásához szüksége lesz a virtuális hálózat és az alhálózat nevére.

### <a name="step-1"></a>1. lépés

Nyissa meg a felhőtelepítéshez szükséges szolgáltatáskonfigurációs fájlt (.cscfg) a Visual Studióban, és a hálózat konfigurálásához adja hozzá a következő szakaszt az utolsó „`</Role>`” elem alatt, hogy létrehozhassa a belső terheléselosztást.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="name of the load balancer">
        <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Adja meg a hálózat konfigurációs fájljához szükséges értékeket, hogy lássa, hogyan fog kinézni. A példában feltételeztük, létrehozott egy „test_vnet” nevű virtuális hálózatot, amely egy test_subnet nevű 10.0.0.0/24 alhálózatot tartalmaz, és a statikus IP-címe 10.0.0.4. A terheléselosztó elnevezése testLB lesz.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="testLB">
        <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

A terheléselosztó sémájával kapcsolatos további információkért lásd: [Add load balancer](https://msdn.microsoft.com/library/azure/dn722411.aspx) (Terheléselosztó hozzáadása).

### <a name="step-2"></a>2. lépés

Ha végpontokat szeretne hozzáadni a belső terheléselosztáshoz, módosítsa a szolgáltatásdefiníciós fájlt (.csdef). Szerepkörpéldány létrehozásakor a szolgáltatásdefiníciós fájl hozzáadja a szerepkörpéldányokat a belső terheléselosztáshoz.

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
    </Endpoints>
</WorkerRole>
```

A fenti példa értékeit felhasználva adja hozzá az értékeket a szolgáltatásdefiníciós fájlhoz.

```xml
<WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
    </Endpoints>
</WorkerRole>
```

A hálózati forgalom terhelésének elosztása a testLB terheléselosztóval végezhető el, ehhez a 80-as portot kell használni a bejövő kérelmek fogadásához, valamint a feldolgozói szerepkörpéldányokra való küldést szintén a 80-as porton keresztül kell végezni.

## <a name="next-steps"></a>Következő lépések

[A terheléselosztó elosztási módjának konfigurálása forrás IP-affinitás használatával](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)


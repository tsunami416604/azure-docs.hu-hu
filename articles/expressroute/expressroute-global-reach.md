---
title: Globális elérhetőség az Azure ExpressRoute |} A Microsoft Docs
description: Ez a cikk az ExpressRoute-Kapcsolatcsoportok privát hálózati a helyszíni hálózat között, és engedélyezze a globális elérhetőségű teszik hivatkozásra.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966815"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>Hivatkozás az ExpressRoute-Kapcsolatcsoportok ExpressRoute globális elérhetőségű (előzetes verzió) engedélyezése

Az ExpressRoute egy magán- és rugalmas módja a helyszíni hálózatok csatlakoztatása a Microsoft Cloud. Számos Microsoft felhőszolgáltatások, például Azure, Office 365 és Dynamics 365 elérését a saját adatközpontban vagy a vállalati hálózathoz. Például előfordulhat, hogy rendelkezik egy fiókiroda az ExpressRoute-kapcsolatcsoport szilícium-völgy és a egy másik fiókiroda a Baltimore az ExpressRoute-kapcsolatcsoport, Washington, D.C. a San franciscóban. 

Mindkét fiókirodák nagy sebességű kapcsolat az Azure-erőforrások USA keleti RÉGIÓJA és USA nyugati Régiójában is rendelkezhet. Azonban a fiókirodák közvetlenül egymással adatok nem váltanak. Más szóval 10.0.1.0/24 10.0.3.0/24 és 10.0.4.0/24, de nem 10.0.2.0/24 adatokat tudjon cserélni.

![nélkül][1]

A **ExpressRoute globális elérhetőségű**, ExpressRoute-Kapcsolatcsoportok teszik egy titkos adatok hálózatot a helyszíni hálózat közötti kapcsolat. A fenti példában, ExpressRoute globális elérhetőségű, azonban kiegészül a San Franciscóban office (10.0.1.0/24) közvetlenül adatcserét a Baltimore Office (10.0.2.0/24) keresztül a meglévő ExpressRoute-Kapcsolatcsoportok és a Microsoft globális hálózaton keresztül. 

![a][2]

Az ExpressRoute globális elérhetőségű jelenleg támogatott a következő országokban:

* Egyesült Államok
* Egyesült Királyság 
* Japán

Az ExpressRoute-Kapcsolatcsoportok létre kell hozni a [ExpressRoute-társviszony-létesítési helyszínek](expressroute-locations.md) a fenti országok. Ahhoz, hogy az ExpressRoute globális elérhetőségű közötti [geopolitikai régiókon](expressroute-locations.md), a Kapcsolatcsoportok prémium Termékváltozatot kell lennie.


## <a name="before-you-begin"></a>Előkészületek

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Mielőtt elkezdené a konfigurációt, ellenőrizze az alábbi követelményeknek kell.

* Telepítse az Azure PowerShell legújabb verzióját. Lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/install-azurerm-ps).
* Megismerheti az ExpressRoute kapcsolatcsoport-kiépítési [munkafolyamatok](expressroute-workflows.md).
* Ellenőrizze, hogy az ExpressRoute-Kapcsolatcsoportok kiépített állapotban van.
* Ellenőrizze, hogy az ExpressRoute-Kapcsolatcsoportok az Azure privát társviszony-létesítés van konfigurálva.  

### <a name="log-into-your-azure-account"></a>Jelentkezzen be az Azure-fiókkal
A konfiguráció indításához az Azure-fiókjával be kell jelentkeznie. 

Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A parancs kérni fogja az Azure-fiók bejelentkezési hitelesítő adatait.  

```powershell
Connect-AzureRmAccount
```

Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

```powershell
Get-AzureRmSubscription
```

Válassza ki a használni kívánt előfizetést.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Az ExpressRoute-kapcsolatcsoportokat konfigurációs azonosítása
Engedélyezheti a globális elérhetőségű ExpressRoute bármely két ExpressRoute-Kapcsolatcsoportok között, amennyiben azok még található a támogatott országok, és másik társviszony-létesítési helyszínek során jönnek. Ha az előfizetés tulajdonosa mindkét Kapcsolatcsoportok, vagy futtassa a konfigurációt az alábbi szakaszok a kapcsolatcsoport választhat. Ha a két kapcsolatcsoporttal az Azure-előfizetések, egy Azure-előfizetésből engedélyezési kell, és a többi Azure-előfizetésben a konfigurációs parancs futtatásakor adja át a hitelesítési kulcsot.

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózat közötti kapcsolat

Kapcsolatcsoport 1 és 2 kapcsolatcsoport beolvasásához használja a következő parancsokat. A két Kapcsolatcsoportok találhatók ugyanabban az előfizetésben.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Kapcsolatcsoport 1 az alábbi parancsot futtassa, és adja át a kapcsolatcsoport 2 a privát társviszony-létesítés tartozó azonosító.

Vegye figyelembe, hogy azonosítója a privát társviszony-létesítés a következőhöz hasonló:

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

A *- AddressPrefix* kell lennie egy/29 IPv4 alhálózati, például a "10.0.0.0/29". IP-címek használjuk ezt az alhálózatot a kapcsolatot a két ExpressRoute-Kapcsolatcsoportok között. Nem használjon cím az alhálózat az Azure Vnetekhez vagy a helyszíni hálózatokon. 


A kapcsolatcsoport 1 a konfiguráció mentéséhez.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Az előző művelet befejeződése után kell kapcsolat segítségével a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Az ExpressRoute-Kapcsolatcsoportok az Azure-előfizetések

Ha a két Kapcsolatcsoportok nem az Azure-előfizetéshez, engedélyt kell. A következő konfiguráció engedélyezése a kapcsolatcsoport 2 előfizetés létrehozása és a hitelesítési kulcs átadott kapcsolatcsoport 1.

Hitelesítési kulcs létrehozásához. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Jegyezze fel a kapcsolatcsoport 2 a privát társviszony-létesítést az azonosítója, valamint a hitelesítési kulcsot.

Futtassa a következő parancsot a kapcsolatcsoport 1 ellen. Adja át a kapcsolatcsoport 2 a privát társviszony-létesítést az ID és a hitelesítési kulcs

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

A kapcsolatcsoport 1 a konfiguráció mentéséhez.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

A korábbi műveletek befejeződése után kell kapcsolat segítségével a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

## <a name="to-get-and-verify-the-configuration"></a>És a konfiguráció ellenőrzése

Használja a következő parancsot a kapcsolatcsoport a konfiguráció ellenőrzése, amelyen a konfigurációs történt. A példa az előző példában 1 kapcsolatcsoport.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Ha egyszerűen futtassa *$ckt1* láthatja a PowerShellben *CircuitConnectionStatus* a kimenetben. Azt jelzi, hogy a kapcsolat létrejött, "Csatlakoztatva,"-e, illetve "Csatlakoztatva". 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózat közötti kapcsolat letiltása

Letiltásához futtassa a parancsokat a kapcsolatcsoport ellen amelyen a konfigurációs történt. Ebben a példában a kapcsolatcsoport 1 előző példákból használ.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

A Get-művelet a állapotának ellenőrzése futtathatja. 

A fenti művelet befejezése után már nem elérhetőséget az ExpressRoute-Kapcsolatcsoportok keresztül a helyszíni hálózat között. 

## <a name="next-steps"></a>További lépések
1. [További információ az ExpressRoute globális elérhetőségű](expressroute-faqs.md#globalreach)
2. [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
3. [Azure virtuális hálózati kapcsolat ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "globális jelenlét nélkül diagramja"
[2]: ./media/expressroute-global-reach/2.png "globális elérhetőségű diagramja"
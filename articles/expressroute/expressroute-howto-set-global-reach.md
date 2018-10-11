---
title: Globális elérhetőség az Azure ExpressRoute konfigurálása |} A Microsoft Docs
description: Ez a cikk az ExpressRoute-Kapcsolatcsoportok privát hálózati a helyszíni hálózat között, és engedélyezze a globális elérhetőségű teszik hivatkozásra.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: cherylmc
ms.openlocfilehash: 4006626f9768289e75ccd61a1ef0bad5389f0a7a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071232"
---
# <a name="configure-expressroute-global-reach-preview"></a>Globális elérhetőség az ExpressRoute (előzetes verzió) konfigurálása
Ez a cikk segít az ExpressRoute globális elérhetőségű PowerShell-lel konfigurálja. További információkért lásd: [ExpressRouteRoute globális elérhetőségű](expressroute-global-reach.md).
 
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
Engedélyezheti a globális elérhetőségű ExpressRoute bármely két ExpressRoute-Kapcsolatcsoportok között, amennyiben azok még található a támogatott országok, és másik társviszony-létesítési helyszínek során jönnek. Ha az előfizetés tulajdonosa mindkét Kapcsolatcsoportok vagy futtassa a konfigurációt az alábbi szakaszok a kapcsolatcsoport választhatja meg. Ha a két kapcsolatcsoporttal az Azure-előfizetések, egy Azure-előfizetésből engedélyezési kell, és a többi Azure-előfizetésben a konfigurációs parancs futtatásakor adja át a hitelesítési kulcsot.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózat közötti kapcsolat

Kapcsolatcsoport 1 és 2 kapcsolatcsoport beolvasásához használja a következő parancsokat. A két Kapcsolatcsoportok találhatók ugyanabban az előfizetésben.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Kapcsolatcsoport 1 az alábbi parancsot futtassa, és adja át a kapcsolatcsoport 2 a privát társviszony-létesítés tartozó azonosító.

> [!NOTE]
> A privát társviszony-létesítés azonosító úgy tűnik, a */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering*
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> *-AddressPrefix* kell lennie egy/29 IPv4 alhálózati, például: "10.0.0.0/29". IP-címek használjuk ezt az alhálózatot a kapcsolatot a két ExpressRoute-Kapcsolatcsoportok között. Nem-címeket kell használnia az alhálózat az Azure Vnetekhez vagy a helyszíni hálózatokon.
> 



A konfiguráció mentéséhez, a kapcsolatcsoport 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

A fenti művelet befejeződése után kell kapcsolat segítségével a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Az ExpressRoute-Kapcsolatcsoportok az Azure-előfizetések

Ha a két Kapcsolatcsoportok nem az Azure-előfizetéshez, engedélyt kell. A következő konfiguráció engedélyezése a kapcsolatcsoport 2 előfizetés létrehozása és a hitelesítési kulcs átadott kapcsolatcsoport 1.

Hitelesítési kulcs létrehozásához. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Jegyezze fel a kapcsolatcsoport 2 a privát társviszony-létesítést az azonosítója, valamint a hitelesítési kulcs.

Futtassa a következő parancsot a kapcsolatcsoport 1 ellen. Adja át a kapcsolatcsoport 2 a privát társviszony-létesítést az Id és a hitelesítési kulcs 
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

A konfiguráció mentéséhez, a kapcsolatcsoport 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

A fenti művelet befejeződése után kell kapcsolat segítségével a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

## <a name="get-and-verify-the-configuration"></a>Első és a konfiguráció ellenőrzése

Használja a következő parancsot a kapcsolatcsoport a konfiguráció ellenőrzése, amelyen a konfigurációs történt, azaz 1 kapcsolatcsoport a fenti példában.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Ha egyszerűen futtassa *$ckt1* PowerShell látni fogja a *CircuitConnectionStatus* a kimenetben. Azt jelzi, hogy a kapcsolat létrejött, "Csatlakoztatva,"-e, illetve "Csatlakoztatva". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Tiltsa le a helyszíni hálózat közötti kapcsolat

Tiltsa le, futtassa a parancsokat a kapcsolatcsoport elleni, a konfiguráció készült, azaz 1 kapcsolatcsoport a fenti példában.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

A Get-művelet a állapotának ellenőrzése futtathatja. 

A fenti művelet befejezése után már nem elérhetőséget az ExpressRoute-Kapcsolatcsoportok keresztül a helyszíni hálózat között. 


## <a name="next-steps"></a>További lépések
1. [További információ az ExpressRoute globális elérhetőségű](expressroute-global-reach.md)
2. [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
3. [Azure virtuális hálózati kapcsolat ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)



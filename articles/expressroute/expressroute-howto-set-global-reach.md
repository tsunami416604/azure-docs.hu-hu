---
title: 'Globális elérhetőség – az ExpressRoute konfigurálása: Azure |} A Microsoft Docs'
description: Ez a cikk az ExpressRoute-Kapcsolatcsoportok privát hálózati a helyszíni hálózat között, és engedélyezze a globális elérhetőségű teszik hivatkozásra.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: eafb37f9bd54e0928e2f6c7615fc7fe365897620
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250603"
---
# <a name="configure-expressroute-global-reach-preview"></a>Konfigurálja az ExpressRoute globális elérhetőségű (előzetes verzió)
Ez a cikk segít az ExpressRoute globális elérhetőségű PowerShell-lel konfigurálja. További információkért lásd: [ExpressRouteRoute globális elérhetőségű](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Előkészületek
> [!IMPORTANT]
> A nyilvános előzetes verziója egy szolgáltatásiszint-megállapodás nélkül, és nem használható éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Mielőtt elkezdené a konfigurációt, ellenőrizze az alábbiakat:

* Hogy telepítette az Azure PowerShell legújabb verzióját. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps).
* Az ExpressRoute kapcsolatcsoport-kiépítési megértette [munkafolyamatok](expressroute-workflows.md).
* Hogy az ExpressRoute-Kapcsolatcsoportok kiépített állapotban van.
* Az ExpressRoute-Kapcsolatcsoportok, hogy az Azure privát társviszony-létesítés van konfigurálva.  

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába
A konfiguráció indításához jelentkezzen be az Azure-fiókjával. 

Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal, és csatlakoztassa a fiókját. A parancs az Azure-fiók bejelentkezési hitelesítő adatokat kér.  

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
Engedélyezheti a globális elérhetőségű ExpressRoute bármely két ExpressRoute-Kapcsolatcsoportok között mindaddig, amíg Ön található a támogatott országok és a létrehozásuk másik társviszony-létesítési helyszínek. Ha az előfizetés tulajdonosa mindkét Kapcsolatcsoportok, vagy futtassa a konfigurációt az alábbi szakaszok a kapcsolatcsoport választhat. 

Ha a két kapcsolatcsoporttal az Azure-előfizetések, szüksége van egy Azure-előfizetésből engedélyezési. Ezután adja át az engedélyezési kulcsot a többi Azure-előfizetésben a konfigurációs parancs futtatásakor.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózat közötti kapcsolat

Kapcsolatcsoport 1 és 2 kapcsolatcsoport beolvasásához használja a következő parancsokat. A két Kapcsolatcsoportok találhatók ugyanabban az előfizetésben.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Futtassa a következő parancsot a kapcsolatcsoport 1 szemben, és a privát társviszony-létesítési kör 2 Azonosítóját adja át.

> [!NOTE]
> A privát társviszony-létesítési azonosítója a következőhöz hasonló: */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/ Adhatja*
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> *-AddressPrefix* kell lennie egy/29 IPv4 alhálózati, például a "10.0.0.0/29". IP-cím az alhálózat, a két ExpressRoute-Kapcsolatcsoportok közötti kapcsolatot létesíteni használjuk. Ez az alhálózat, ne használja a címeket, az Azure virtuális hálózataihoz, illetve a helyszíni hálózat.
> 



A konfiguráció mentéséhez kapcsolatcsoport 1 a következőképpen:
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Az előző művelet befejeződése után kell kapcsolat segítségével a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Az ExpressRoute-Kapcsolatcsoportok az Azure-előfizetések

Ha a két Kapcsolatcsoportok nem az Azure-előfizetéshez, engedélyt kell. A következő konfigurációt, az engedélyezési jön létre a kapcsolatcsoport 2 előfizetés, és a hitelesítési kulcs átadott kapcsolatcsoport 1.

Hitelesítési kulcs létrehozásához. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Jegyezze fel a kapcsolatcsoport 2, valamint a hitelesítési kulcs privát társviszony-létesítési azonosítója.

Futtassa a következő parancsot a kapcsolatcsoport 1 ellen. 2. és a hitelesítési kulcs adja át a privát társviszony-létesítési kör Azonosítóját.
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

A kapcsolatcsoport 1 a konfiguráció mentéséhez.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Az előző művelet befejeződése után kell kapcsolat segítségével a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

## <a name="get-and-verify-the-configuration"></a>Első és a konfiguráció ellenőrzése

Használja a következő parancsot a kapcsolatcsoport a konfiguráció ellenőrzése, amelyen a konfigurációs (például az előző példában 1 kapcsolatcsoport) történt.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Ha egyszerűen futtassa *$ckt1* láthatja a PowerShellben *CircuitConnectionStatus* a kimenetben. Megadja, hogy e a kapcsolat létrejött, "Csatlakoztatva" vagy "Csatlakoztatva". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Tiltsa le a helyszíni hálózat közötti kapcsolat

Kapcsolat letiltásához futtassa a parancsokat a kapcsolatcsoport elleni ahol a configuration (például az előző példában 1 kapcsolatcsoport) történt.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

A Get-művelet a állapotának ellenőrzése futtathatja. 

Az előző művelet befejezése után, hogy már nem rendelkezik az ExpressRoute-Kapcsolatcsoportok keresztül a helyszíni hálózat között. 


## <a name="next-steps"></a>További lépések
1. [További információ az ExpressRoute globális elérhetőségű](expressroute-global-reach.md)
2. [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
3. [Az Azure virtuális hálózat ExpressRoute-kapcsolatcsoport összekapcsolása](expressroute-howto-linkvnet-arm.md)



---
title: 'Új kapcsolatcsoport – társviszony-létesítés ExpressRoute: Azure |} A Microsoft Docs'
description: Hogyan letiltása és engedélyezése az ExpressRoute-kapcsolatcsoport társviszony.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: ad050e11c98139af00ad752f8960d55a58ca2f34
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132591"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute-kapcsolatcsoport társviszony alaphelyzetbe állítása

Ez a cikk bemutatja, hogyan letiltása és engedélyezése a PowerShell használatával egy ExpressRoute-kapcsolatcsoport társviszony-létesítéseket. Ha letilt egy társviszony-létesítés, a BGP-munkamenetben az elsődleges kapcsolat és az ExpressRoute-kapcsolatcsoport másodlagos kapcsolati is leáll. Megszakad a kapcsolat a Microsoft-társviszony segítségével. Ha engedélyezi a társviszonyt, a BGP-munkamenetben az elsődleges kapcsolat és az ExpressRoute-kapcsolatcsoport másodlagos kapcsolati kerül. Akkor lesz között visszaáll a kapcsolat a Microsoft-társviszony segítségével. Engedélyezze, és tiltsa le a Microsoft-Peering és az Azure privát társviszony-létesítés az ExpressRoute-kapcsolatcsoport egymástól függetlenül. Amikor először konfigurálja a az ExpressRoute-kapcsolatcsoport társviszony-létesítések, a társviszony-létesítések alapértelmezés szerint engedélyezve vannak.

Van néhány olyan forgatókönyvek, ahol, hasznos lehet az ExpressRoute-társviszonyok alaphelyzetbe állítása.
* Tesztelje a katasztrófa utáni helyreállítás megtervezéséhez és implementációjához. Ha például két ExpressRoute-kapcsolatcsoporttal rendelkezik. Egy kapcsolatcsoport társviszony-létesítések letilthatja, és az egyéb kapcsolatcsoporthoz feladatátvételét a hálózati forgalom kényszerített.
* Kétirányú továbbítás észlelése (BFD) az Azure privát társviszony-létesítés, az ExpressRoute-kapcsolatcsoport engedélyezése. BFD alapértelmezés szerint engedélyezve van, ha az ExpressRoute-kapcsolatcsoport 2018. augusztus 1. után készült. Ha a kapcsolatcsoport, amely előtt lett létrehozva, BFD nem volt engedélyezve. A társviszony-létesítés letiltása és újbóli engedélyezésére, BFD engedélyezheti. Megjegyzendő, hogy BFD támogatott az Azure privát társviszony-létesítés csak.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Visszaállítása egy társviszony-létesítés

1. Ha Ön helyileg futtatja a Powershellt, nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

  ```azurepowershell
  Connect-AzureRmAccount
  ```
2. Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
3. Válassza ki a használni kívánt előfizetést.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Futtassa az alábbi parancsokat az ExpressRoute-kapcsolatcsoport lekéréséhez.

  ```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```
5. Azonosítsa a társviszonyt szeretne engedélyezése vagy letiltása. *Társviszony-Létesítéseket* egy tömb. A következő példában a [0] Társviszonyok Azure privát társviszony-létesítés és a Microsoft-társviszony-Létesítéseket [1] Peering.

  ```azurepowershell-interactive
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
ServiceKey                       : ########-####-####-####-############
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : []
AllowClassicOperations           : False
GatewayManagerEtag               :
  ```
6. Futtassa az alábbi parancsokat a társviszony állapotának módosítása.

  ```azurepowershell-interactive
  $ckt.Peerings[0].State = "Disabled"
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```
A társviszony-létesítés beállított állapotban kell lennie. 

## <a name="next-steps"></a>További lépések
Ha egy ExpressRoute-probléma megoldásához segítségre van szüksége, tekintse meg a következő cikkeket:
* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [A hálózati teljesítmény hibaelhárítása](expressroute-troubleshooting-network-performance.md)

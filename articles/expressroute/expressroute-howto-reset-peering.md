---
title: 'Azure ExpressRoute: Alaphelyzetbe állítási kapcsolatlétesítés'
description: Az ExpressRoute-kapcsolatlétesítések letiltása és engedélyezése.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941743"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute-kapcsolatlétesítések alaphelyzetbe állítása

Ez a cikk azt ismerteti, hogyan tilthatja le és engedélyezheti az ExpressRoute-kapcsolatlétesítéseket a PowerShell használatával. Ha letilt egy társviszony-létesítést, a BGP-munkamenet mind az elsődleges, mind az ExpressRoute-kapcsolat másodlagos kapcsolatán leáll. Ezzel a Microsoft-társviszony-létesítéssel megszakad a kapcsolat. Ha engedélyezi a társviszony-létesítést, a BGP-munkamenet az elsődleges és az ExpressRoute-kapcsolat másodlagos kapcsolatán is megjelenik. Ezzel a Microsofttal való társviszony-létesítés révén visszanyeri a kapcsolatot. A Microsoft Társviszony-létesítés és az Azure privát társviszony-létesítése egy ExpressRoute-kapcsolati kapcsolat függetlenül engedélyezheti és letilthatja. Amikor először konfigurálja a társviszony-létesítéseket az ExpressRoute-kapcsolaton, a társviszony-létesítések alapértelmezés szerint engedélyezve vannak.

Van néhány forgatókönyv, ahol hasznosnak találhatja az ExpressRoute-társviszony-létesítések alaphelyzetbe állítása.
* Tesztelje a vész-helyreállítási tervezés és megvalósítás. Például két ExpressRoute-kapcsolaton van. Letilthatja az egyik kapcsolatlétesítést, és kényszerítheti a hálózati forgalmat, hogy a másik áramkörre adja át a feladatát.
* Engedélyezze a kétirányú továbbítás észlelését (BFD) az Azure privát társviszony-létesítési vagy az ExpressRoute-kapcsolatlétesítés Microsoft társviszony-létesítésén. A BFD alapértelmezés szerint engedélyezve van az Azure Privát társviszony-létesítéskor, ha az ExpressRoute-kapcsolati kapcsolat 2018. Ha a kapcsolatcsoport korábban jött létre, a BFD nem volt engedélyezve. A BFD engedélyezéséhez tiltsa le a társviszony-létesítést, és engedélyezze azt. 

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Társviszony-létesítés alaphelyzetbe állítása

1. Ha helyileg futtatja a PowerShellt, nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Válassza ki a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Futtassa a következő parancsokat az ExpressRoute-kapcsolat beolvasásához.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Azonosítsa a letiltani vagy engedélyezni kívánt társviszony-létesítést. *A társviszony-létesítés* egy tömb. A következő példában a társviszony-létesítés[0] az Azure privát társviszonylétesítési és társviszony-létesítési[1] Microsoft társviszonylétesítés.

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
6. A társviszony-létesítés állapotának módosításához futtassa a következő parancsokat.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   A társviszony-létesítésnek a beállított állapotban kell lennie. 

## <a name="next-steps"></a>További lépések
Ha segítségre van szüksége egy ExpressRoute-probléma elhárításához, olvassa el az alábbi cikkeket:
* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [A hálózati teljesítmény hibaelhárítása](expressroute-troubleshooting-network-performance.md)

---
title: 'Azure ExpressRoute: áramkör-társítások alaphelyzetbe állítása'
description: Megtudhatja, hogyan tilthatja le és engedélyezheti az Azure ExpressRoute áramköri szolgáltatásait Azure PowerShell használatával. A társítások konfigurálásakor a rendszer alapértelmezés szerint engedélyezi őket.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: e576fe2f08d54d2642603e3b7974e00feec8ce93
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192186"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute-áramköri társítások alaphelyzetbe állítása

Ez a cikk bemutatja, hogyan tilthatja le és engedélyezheti egy ExpressRoute-áramkör társítását a PowerShell használatával. Ha letilt egy társítást, akkor a BGP-munkamenet mind az elsődleges, mind a ExpressRoute áramkör másodlagos kapcsolata le lesz állítva. Ezzel a kapcsolattal elveszti a kapcsolatot a Microsofttal. Ha engedélyezi a társítást, akkor a BGP-munkamenet az elsődleges kapcsolaton és a ExpressRoute áramkör másodlagos kapcsolatán is megjelenik. Ezzel a kapcsolattal visszanyerheti a kapcsolatot a Microsofttal. A Microsoft-és az Azure-beli privát ExpressRoute egymástól függetlenül engedélyezheti és tilthatja le. Amikor először konfigurálja a társításokat a ExpressRoute-áramkörön, a rendszer alapértelmezés szerint engedélyezi a társításokat.

Van néhány olyan forgatókönyv, ahol hasznos lehet a ExpressRoute-társítások alaphelyzetbe állítása.
* Tesztelje a vész-helyreállítási tervét és megvalósítását. Tegyük fel, hogy két ExpressRoute-áramkörrel rendelkezik. Letilthatja egy áramkör társításait, és kényszerítheti a hálózati forgalmat, hogy átadja a feladatátvételt a másik áramkörnek.
* Engedélyezze a kétirányú továbbítási észlelést (BFD) a ExpressRoute-áramkör Azure-beli privát vagy Microsoft-hálózatán. A BFD alapértelmezés szerint engedélyezve van az Azure-beli privát kapcsolatok esetében, ha a ExpressRoute-áramkört a 1 2018-es és a Microsoft-partneri kapcsolat alapján hozza létre, ha a ExpressRoute-áramkört január 10 2020 után hozták létre. Ha az áramkört korábban már létrehozták, a BFD nem volt engedélyezve. A BFD engedélyezéséhez tiltsa le a társítást, és engedélyezze újra. 

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Társítás alaphelyzetbe állítása

1. Ha helyileg futtatja a PowerShellt, nyissa meg emelt szintű jogosultságokkal a PowerShell-konzolt, és kapcsolódjon a fiókjához. A következő példa segít a kapcsolódásban:

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
4. Futtassa az alábbi parancsokat a ExpressRoute áramkör beolvasásához.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Azonosítsa a letiltani vagy engedélyezni kívánt társítást. A *társak* tömb. A következő példában a [0] társak az Azure privát társ-és társai [1] Microsoft-társak.

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
6. Futtassa a következő parancsokat a társítás állapotának módosításához.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   A társításnak beállított állapotban kell lennie. 

## <a name="next-steps"></a>Következő lépések
Ha segítségre van szüksége egy ExpressRoute-probléma elhárításához, tekintse meg a következő cikkeket:
* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [A hálózati teljesítmény hibaelhárítása](expressroute-troubleshooting-network-performance.md)

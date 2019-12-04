---
title: 'Azure ExpressRoute: áramkör-társítások alaphelyzetbe állítása'
description: ExpressRoute-áramköri kapcsolatok letiltása és engedélyezése.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.openlocfilehash: b14b8a9a2bc43c33095bf07e7fb7ebcc2d6c1ffa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769523"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute-áramköri társítások alaphelyzetbe állítása

Ez a cikk bemutatja, hogyan tilthatja le és engedélyezheti egy ExpressRoute-áramkör társítását a PowerShell használatával. Ha letilt egy társítást, akkor a BGP-munkamenet mind az elsődleges, mind a ExpressRoute áramkör másodlagos kapcsolata le lesz állítva. Ezzel a kapcsolattal elveszti a kapcsolatot a Microsofttal. Ha engedélyezi a társítást, akkor a BGP-munkamenet az elsődleges kapcsolaton és a ExpressRoute áramkör másodlagos kapcsolatán is megjelenik. Ezzel a kapcsolattal visszanyerheti a kapcsolatot a Microsofttal. A Microsoft-és az Azure-beli privát ExpressRoute egymástól függetlenül engedélyezheti és tilthatja le. Amikor először konfigurálja a társításokat a ExpressRoute-áramkörön, a rendszer alapértelmezés szerint engedélyezi a társításokat.

Van néhány olyan forgatókönyv, ahol hasznos lehet a ExpressRoute-társítások alaphelyzetbe állítása.
* Tesztelje a vész-helyreállítási tervét és megvalósítását. Tegyük fel, hogy két ExpressRoute-áramkörrel rendelkezik. Letilthatja egy áramkör társításait, és kényszerítheti a hálózati forgalmat, hogy átadja a feladatátvételt a másik áramkörnek.
* Engedélyezze a kétirányú továbbítási észlelést (BFD) az ExpressRoute-áramkör Azure-beli privát hálózatán. A BFD alapértelmezés szerint engedélyezve van, ha a ExpressRoute-áramkör létrehozása 2018. augusztus 1-től történik. Ha az áramkört korábban már létrehozták, a BFD nem volt engedélyezve. A BFD engedélyezéséhez tiltsa le a társítást, és engedélyezze újra. Érdemes megjegyezni, hogy a BFD csak az Azure-beli privát kiszolgálókon támogatott.

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

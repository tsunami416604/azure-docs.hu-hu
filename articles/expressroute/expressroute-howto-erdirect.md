---
title: Konfigurálja az ExpressRoute közvetlen – Azure |} A Microsoft Docs
description: Ezen a lapon konfigurálja az ExpressRoute közvetlen segítségével.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: c5bae17008e2d664a09999daf3244213dfa2364f
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406745"
---
# <a name="how-to-configure-expressroute-direct"></a>Az ExpressRoute közvetlen konfigurálása

Az ExpressRoute közvetlen lehetőséget nyújt a közvetlenül a Microsoft társviszony-létesítési helyszínek stratégiai a világ különböző pontjain található globális hálózatának csatlakoztathatnak. További információkért lásd: [kapcsolatos az ExpressRoute közvetlen csatlakozás](expressroute-erdirect-about.md).

## <a name="resources"></a>Az erőforrás létrehozása

1. Jelentkezzen be az Azure-ba, és válassza ki az előfizetést. Az ExpressRoute közvetlen erőforrások és az ExpressRoute-Kapcsolatcsoportok ugyanabban az előfizetésben kell lennie.

  ```powershell
  Connect-AzAccount 

  Select-AzSubscription -Subscription “<SubscriptionID or SubscriptionName>”
  ```
2. Minden hely, ahol az ExpressRoute közvetlen támogatott listája.
  
  ```powershell
  Get-AzExpressRoutePortsLocation
  ```

  **Példa a kimenetre**
  
  ```powershell
  Name                : Equinix-Ashburn-DC2
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
  ProvisioningState   : Succeeded
  Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-Dallas-DA3
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
  ProvisioningState   : Succeeded
  Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : []
  ```
3. Határozza meg, hogy rendelkezik-e egy olyan helyre, fent felsorolt rendelkezésre álló sávszélesség

  ```powershell
  Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
  ```

  **Példa a kimenetre**

  ```powershell
  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
  ```
4. Hozzon létre egy ExpressRoute közvetlen erőforrást a fent kiválasztott hely alapján

  Az ExpressRoute közvetlen QinQ- és Dot1Q beágyazását támogatja. Ha QinQ van kijelölve, mindegyik ExpressRoute-kapcsolatcsoport dinamikusan rendeli egy S-címke, és az ExpressRoute közvetlen erőforrás teljes egyedi lesz. Minden C-címke a kapcsolatcsoport egyedinek kell lennie. a kapcsolatcsoport, de nem az ExpressRoute közvetlen között.  

  Ha Dot1Q beágyazás be van jelölve, a teljes az ExpressRoute közvetlen erőforrás között egyedi-e a C-Tag (VLAN) kell kezelni.  

  > [!IMPORTANT]
  > Az ExpressRoute közvetlen csak egy beágyazás típusa lehet. A beágyazás ExpressRoute közvetlen létrehozása után nem módosítható.
  > 
 
  ```powershell 
  $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
  ```

  > [!NOTE]
  > A beágyazás attribútum Dot1Q is beállítható. 
  >

  **Példa a kimenetre:**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
  Circuits                   : []
  ```

## <a name="state"></a>A hivatkozások felügyeleti állapot módosítása

  Ez a folyamat egy 1. rétegét tesztet, amely biztosítja, hogy minden egyes közötti kapcsolat megfelelően javított minden útválasztón elsődleges és másodlagos elvégzéséhez használható.
1. Az ExpressRoute közvetlen beolvasása – részletek.

  ```powershell
  $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  ```
2. Állítsa hivatkozás engedélyezett. Ismételje meg ezt a lépést minden hivatkozás engedélyezve van beállítva.

  Hivatkozások [0] az az elsődleges port, a hivatkozások [1] pedig a másodlagos portot.

  ```powershell
  $ERDirect.Links[0].AdminState = “Enabled”
  Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
  $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  $ERDirect.Links[1].AdminState = “Enabled”
  Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
  ```
  **Példa a kimenetre:**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
  Circuits                   : []
  ```

  Ugyanezzel az eljárással lehetséges az `AdminState = “Disabled”` kapcsolja le a portokat.

## <a name="circuit"></a>Kapcsolatcsoport létrehozása

Alapértelmezés szerint az előfizetés, amelyben az ExpressRoute közvetlen erőforrás van a 10 Kapcsolatcsoportok hozhat létre. Ez a támogatás által növelhető. Ön felelős nyomon követheti kiépített és a felhasznált sávszélesség. Kiosztott sávszélességre sávszélesség az ExpressRoute közvetlen erőforrás minden kapcsolatcsoportra összegét, és a mögöttes fizikai adapterek fizikai használatát a magas kihasználtságú sávszélesség.

Nincsenek további kapcsolatcsoport sávszélessége, amely az ExpressRoute közvetlen csak a fent vázolt forgatókönyvek támogatásához, amellyel. Ezek a következők: 40Gbps és 100Gbps.

Standard vagy prémium szintű kapcsolatok hozhatók létre. Standard Kapcsolatcsoportok megtalálhatók a költség, míg a prémium szintű Kapcsolatcsoportok költsége a kiválasztott sávszélesség alapján. Kapcsolatcsoportok csak hozhatók létre, a forgalmi díjas, korlátlan, nem támogatott az ExpressRoute közvetlen.

Kapcsolatcsoport létrehozása az ExpressRoute közvetlen erőforráson.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Más sávszélességeket a következők: 5.0-s, 10.0 és 40.0

  **Példa a kimenetre:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```

## <a name="next-steps"></a>További lépések

Az ExpressRoute közvetlen kapcsolatos további információkért lásd: a [áttekintése](expressroute-erdirect-about.md).

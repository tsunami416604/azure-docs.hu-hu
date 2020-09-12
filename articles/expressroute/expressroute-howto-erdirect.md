---
title: 'Azure ExpressRoute: a ExpressRoute közvetlen konfigurálása'
description: Megtudhatja, hogyan konfigurálhatja a Azure PowerShellt az Azure ExpressRoute Direct használatára, hogy közvetlenül kapcsolódjon a Microsoft globális hálózatához a világ különböző pontjain.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/22/2020
ms.author: duau
ms.openlocfilehash: c4ce764f50f85ef9979d5a14235759c16228f6b7
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396029"
---
# <a name="how-to-configure-expressroute-direct"></a>A ExpressRoute Direct konfigurálása

A ExpressRoute Direct lehetővé teszi a közvetlen kapcsolódást a Microsoft globális hálózatához a világ bármely pontján elérhető, stratégiai módon elosztott helyen. További információ: [Az ExpressRoute Direct ismertetése](expressroute-erdirect-about.md).

## <a name="create-the-resource"></a><a name="resources"></a>Az erőforrás létrehozása

1. Jelentkezzen be az Azure-ba, és válassza ki az előfizetést. A ExpressRoute Direct erőforrás-és ExpressRoute-áramköröknek ugyanahhoz az előfizetéshez kell tartoznia.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Regisztrálja újra az előfizetést a Microsoft. Network-be a expressrouteportslocation és a expressrouteport API-k eléréséhez.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. Az összes olyan hely listázása, ahol a ExpressRoute Direct támogatott.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **Példa kimenetre**
  
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
4. Annak megállapítása, hogy a fent felsorolt hely rendelkezik-e elérhető sávszélességgel

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **Példa kimenetre**

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
5. ExpressRoute közvetlen erőforrás létrehozása a fent kiválasztott hely alapján

   A ExpressRoute Direct a QinQ és a Dot1Q beágyazást is támogatja. Ha a QinQ van kiválasztva, az egyes ExpressRoute-körök dinamikusan hozzá lesznek rendelve egy S-címkéhez, és egyediek lesznek a ExpressRoute közvetlen erőforrásaiban. Az áramkörön minden C-címkének egyedinek kell lennie az áramkörön, a közvetlen ExpressRoute azonban nem.  

   Ha a Dot1Q beágyazás van kiválasztva, akkor a C-tag (VLAN) egyediségét kell kezelnie a teljes ExpressRoute közvetlen erőforrásban.  

   > [!IMPORTANT]
   > A ExpressRoute Direct csak egy beágyazási típus lehet. A beágyazás nem módosítható a közvetlen ExpressRoute létrehozása után.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > A beágyazási attribútum a Dot1Q értékre is állítható. 
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

## <a name="change-admin-state-of-links"></a><a name="state"></a>Hivatkozások rendszergazdai állapotának módosítása

  Ezt a folyamatot kell használni az 1. rétegbeli tesztek elvégzéséhez, hogy az egyes kapcsolatok megfelelően legyenek kijavítani az egyes útválasztók számára az elsődleges és a másodlagos számára.
1. ExpressRoute közvetlen részletek beolvasása.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Állítsa be a hivatkozást engedélyezve értékre. Ismételje meg ezt a lépést az egyes hivatkozások engedélyezésre való beállításához.

   A hivatkozások [0] az elsődleges port és a hivatkozások [1] a másodlagos port.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
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

   A portok kikapcsolásához használja ugyanezt az eljárást `AdminState = "Disabled"` .

## <a name="create-a-circuit"></a><a name="circuit"></a>Kapcsolatcsoport létrehozása

Alapértelmezés szerint 10 áramkört hozhat létre az előfizetésben, ahol a ExpressRoute Direct erőforrás. Ezt a támogatás növelheti. A kiosztott és a felhasznált sávszélesség nyomon követése felelős. A kiépített sávszélesség a ExpressRoute közvetlen erőforrásban található összes áramkör sávszélességének összege, a felhasznált sávszélesség pedig az alapul szolgáló fizikai felületek fizikai használata.

A fentiekben ismertetett forgatókönyvek támogatásához további áramköri sávszélességek is használhatók, amelyek csak a ExpressRoute közvetlen használatával használhatók. Ezek a következők: 40Gbps és 100Gbps.

A **SkuTier** lehet helyi, standard vagy prémium.

A **SkuFamily** csak MeteredData lehet, mert a ExpressRoute Direct nem támogatja.

Hozzon létre egy áramkört a ExpressRoute Direct erőforráson.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Egyéb sávszélességek: 5,0, 10,0 és 40,0

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

## <a name="next-steps"></a>Következő lépések

A közvetlen ExpressRoute kapcsolatos további információkért tekintse meg az [áttekintést](expressroute-erdirect-about.md).

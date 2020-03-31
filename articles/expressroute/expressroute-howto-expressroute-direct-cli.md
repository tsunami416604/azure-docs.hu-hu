---
title: 'Azure ExpressRoute: ExpressRoute Direct konfigurálása: CLI'
description: Ez a cikk segít az ExpressRoute Direct konfigurálása az Azure CLI használatával
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: cherylmc
ms.openlocfilehash: dcca1417aec52fb4bf99d5c480d81995154a68b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481976"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>ExpressRoute Direct konfigurálása az Azure CLI használatával

Az Azure ExpressRoute Direct segítségével közvetlenül csatlakozhat a Microsoft globális hálózatához a világszerte stratégiailag elosztott társviszony-létesítési helyeken. További információ: [About ExpressRoute Direct Connect](expressroute-erdirect-about.md).

## <a name="create-the-resource"></a><a name="resources"></a>Az erőforrás létrehozása

1. Jelentkezzen be az Azure-ba, és válassza ki az ExpressRoute-ot tartalmazó előfizetést. Az ExpressRoute Direct erőforrásnak és az ExpressRoute-áramköröknek ugyanabban az előfizetésben kell lenniük. Az Azure CLI-ben futtassa a következő parancsokat:

   ```azurecli
   az login
   ```

   Ellenőrizze a fiók előfizetéseit: 

   ```azurecli
   az account list 
   ```

   Válassza ki azt az előfizetést, amelyhez ExpressRoute-áramkört szeretne létrehozni:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Regisztrálja újra a Microsoft.Network előfizetését az expressrouteportslocation és az expressrouteport API-k eléréséhez

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. Az összes olyan hely listázása, ahol az ExpressRoute Direct támogatott:
    
   ```azurecli
   az network express-route port location list
   ```

   **Példa kimenetre**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. Határozza meg, hogy az előző lépésben felsorolt helyek egyike rendelkezik-e rendelkezésre álló sávszélességgel:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Példa kimenetre**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. Hozzon létre egy ExpressRoute Direct-erőforrást, amely az előző lépésekben kiválasztott helyen alapul.

   Az ExpressRoute Direct támogatja a QinQ és a Dot1Q beágyazást is. Ha a QinQ lehetőséget választja, minden ExpressRoute-kapcsolatcsoport dinamikusan s-címke van hozzárendelve, és egyedi az ExpressRoute Direct erőforrásban. Az áramkör minden C-címkének egyedinek kell lennie a kapcsolatcsoporton, de nem az ExpressRoute Direct erőforráson keresztül.  

   Ha a Dot1Q beágyazás lehetőséget választja, a C-tag (VLAN) egyediségét a teljes ExpressRoute Direct erőforrásban kell kezelnie.  

   > [!IMPORTANT]
   > Az ExpressRoute Direct csak egy beágyazási típus lehet. Az ExpressRoute Direct erőforrás létrehozása után nem módosíthatja a beágyazás típusát.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > A **Encapsulation** attribútumot **dot1Q-ra is állíthatja.** 
   >

   **Példa kimenetre**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="change-adminstate-for-links"></a><a name="state"></a>Hivatkozások adminállapotának módosítása

Ezzel a folyamattal végezzen 1. Győződjön meg arról, hogy minden keresztkapcsolat megfelelően van-e javítva az elsődleges és másodlagos portok minden útválasztójához.

1. Állítsa a hivatkozásokat **Engedélyezve értékre.** Ismételje meg ezt a lépést, ha minden hivatkozást **Engedélyezve értékre állíthat.**

   A Links[0] az elsődleges port, a Links[1] pedig a másodlagos port.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Példa kimenetre**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   Ugyanezzel az eljárással le `AdminState = "Disabled"`a portok segítségével.

## <a name="create-a-circuit"></a><a name="circuit"></a>Kapcsolatcsoport létrehozása

Alapértelmezés szerint 10 áramkört hozhat létre az ExpressRoute Direct erőforrást tartalmazó előfizetésben. A Microsoft támogatási szolgálata növelheti az alapértelmezett korlátot. Ön felelős a kiosztott és a felhasznált sávszélesség nyomon követéséért. A kiépített sávszélesség az ExpressRoute Direct erőforrás összes áramköre sávszélességének összege. A felhasznált sávszélesség az alapul szolgáló fizikai felületek fizikai használata.

Az ExpressRoute Directen csak az itt ismertetett forgatókönyvek támogatásához használhat további áramköri sávszélességeket. A sávszélesség 40 Gbps és 100 Gbps.

**A SkuTier** lehet helyi, standard vagy prémium.

**A SkuFamily** csak MeteredData csak akkor legyen MeteredData, mivel az ExpressRoute Direct nem támogatja a korlátlan t.
Hozzon létre egy áramkört az ExpressRoute Direct erőforráson:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Egyéb sávszélességek közé tartozik az 5 Gbps, 10 Gbps és 40 Gbps.

  **Példa kimenetre**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>További lépések

Az ExpressRoute Directről további információt az [áttekintésben talál.](expressroute-erdirect-about.md)

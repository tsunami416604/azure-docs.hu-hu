---
title: Konfigurálja az ExpressRoute közvetlen – az Azure CLI |} A Microsoft Docs
description: Ez a cikk segítséget nyújt az ExpressRoute közvetlen konfigurálása az Azure CLI (előzetes verzió) használatával
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 285b429f565f8a2c7f8c20756f076e631223b10f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076719"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli-preview"></a>Az ExpressRoute közvetlen konfigurálása az Azure CLI (előzetes verzió) használatával

Az Azure ExpressRoute közvetlen használatával közvetlenül csatlakozik a Microsoft társviszony-létesítési helyszínek stratégiai a világ különböző pontjain található globális hálózatán. További információkért lásd: [kapcsolatos az ExpressRoute közvetlen csatlakozás](expressroute-erdirect-about.md).

> [!IMPORTANT]
> Az ExpressRoute közvetlen jelenleg előzetes verzióban érhető el.
>
> Az ExpressRoute közvetlen nyilvános előzetes nélkül egy szolgáltatásiszint-szerződést biztosítunk. Az ExpressRoute közvetlen kép ne használja a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, előfordulhat, hogy egyes funkciók korlátozott képességekkel és egyes funkciói esetleg nem érhetők el az összes Azure-helyen. További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="resources"></a>Az erőforrás létrehozása

1. Jelentkezzen be az Azure-ba, és válassza ki az előfizetést, amely tartalmazza az ExpressRoute. Az ExpressRoute közvetlen erőforrás és az ExpressRoute-Kapcsolatcsoportok ugyanabban az előfizetésben kell lennie. Az Azure CLI-ben futtassa a következő parancsokat:

  ```azurecli
  az login
  ```

  Keresse meg a fiókot az előfizetésekben: 

  ```azurecli
  az account list 
  ```

  Válassza ki az előfizetést, amelynek meg szeretné ExpressRoute-kapcsolatcsoport létrehozása:

  ```azurecli
  az account set --subscription "<subscription ID>"
  ```

2. Minden hely, ahol támogatott az ExpressRoute közvetlen listázza:
    
  ```azurecli
  az network express-route port location list
  ```

  **Példa a kimenetre**
  
  ```azurecli
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
3. Határozza meg, hogy az az előző lépésben felsorolt helyek valamelyikén van-e rendelkezésre álló sávszélességet:

  ```azurecli
  az network express-route port location show -l "Equinix-Ashburn-DC2"
  ```

  **Példa a kimenetre**

  ```azurecli
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
4. Hozzon létre egy ExpressRoute közvetlen erőforrást, amely az előző lépésekben a kiválasztott hely alapján.

  Az ExpressRoute közvetlen QinQ- és Dot1Q beágyazását támogatja. Ha QinQ, mindegyik ExpressRoute-kapcsolatcsoport rendszer dinamikusan rendeli hozzá az S-címke, és egyedi az ExpressRoute közvetlen erőforrás teljes. Minden C-címke a kapcsolatcsoport egyedinek kell lennie. a kapcsolatcsoport, de nem az ExpressRoute közvetlen erőforrás között.  

  Ha Dot1Q beágyazást, egyedi-e a C-Tag (VLAN) között a teljes az ExpressRoute közvetlen erőforrás kell kezelni.  

  > [!IMPORTANT]
  > Az ExpressRoute közvetlen csak egy beágyazás típusa lehet. Az ExpressRoute közvetlen erőforrás létrehozása után a Beágyazás típusa nem módosítható.
  > 
 
  ```azurecli
  az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
  ```

  > [!NOTE]
  > Beállíthatja a **beágyazás** attribútumot **Dot1Q**. 
  >

  **Példa a kimenetre**

  ```azurecli
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

## <a name="state"></a>Változás AdminState hivatkozások

Ez a folyamat használatához egy 1. réteg teszt elvégzéséhez. Győződjön meg arról, hogy minden egyes közötti kapcsolat megfelelően tudjon fókuszálni az elsődleges és másodlagos portok minden egyes útválasztón.

1. Hivatkozások beállítása **engedélyezve**. Ismételje meg ezt a lépést minden hivatkozás beállítása **engedélyezve**.

  Hivatkozások [0] az az elsődleges port, a hivatkozások [1] pedig a másodlagos portot.

  ```azurecli
  az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
  ```
  ```azurecli
  az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
  ```
  **Példa a kimenetre**

  ```azurecli
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

  A fenti eljárással le a portok használatával `AdminState = “Disabled”`.

## <a name="circuit"></a>Kapcsolatcsoport létrehozása

Alapértelmezés szerint 10 Kapcsolatcsoportok hozhat létre, amely tartalmazza az ExpressRoute közvetlen erőforrást az előfizetésben. A Microsoft Support növelheti az alapértelmezett korlát. Ön felelős kiépített és magas kihasználtságú sávszélesség nyomon követése. Kiosztott sávszélességre a sávszélesség, az ExpressRoute közvetlen erőforráson a Kapcsolatcsoportok összege. A mögöttes fizikai adapterek fizikai használatát a magas kihasználtságú sávszélesség.

Segítségével további kapcsolatcsoport sávszélessége az ExpressRoute közvetlen csak az itt leírt forgatókönyveket támogatja. A sávszélességek olyan, 40 GB/s és 100 GB/s.

Standard vagy prémium szintű Kapcsolatcsoportok hozhat létre. A szolgáltatás díja magában foglalja a standard Kapcsolatcsoportok vannak. A prémium szintű Kapcsolatcsoportok költségét, válassza ki a sávszélesség alapján történik. Kapcsolatcsoportok hozhat létre, csak a forgalmi díjas szerint. Korlátlan számú Kapcsolatcsoportok az ExpressRoute közvetlen nem támogatottak.

Kapcsolatcsoport létrehozása az ExpressRoute közvetlen erőforráson:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Más sávszélességeket 5 GB/s sebességű, 10 GB/s és 40 GB/s közé tartozik.

  **Példa a kimenetre**

  ```azurecli
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

Az ExpressRoute közvetlen kapcsolatos további információkért lásd: a [áttekintése](expressroute-erdirect-about.md).

---
title: Azure ExpressRoute nyilvános társának létrehozása és kezelése
description: A nyilvános Azure-alapú társ-kezelés ismertetése és kezelése
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: bae44f67a485546ba29148a114d88df198f7c3e6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280923"
---
# <a name="create-and-manage-expressroute-public-peering"></a>ExpressRoute nyilvános társának létrehozása és kezelése

> [!div class="op_single_selector"]
> * [Cikk – nyilvános társítás](about-public-peering.md)
> * [Videó – nyilvános peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Cikk – Microsoft-társ](expressroute-circuit-peerings.md#microsoftpeering)
>

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet nyilvános társ-útválasztási konfigurációt egy ExpressRoute-áramkörhöz. A társítások állapotát, frissítését vagy törlését és megszüntetését is megtekintheti. Ez a cikk olyan Resource Manager-áramkörökre vonatkozik, amelyeket a rendszer a nyilvános társítás érvénytelenítése előtt hozott létre. Ha már rendelkezik egy korábban már meglévő, a nyilvános társítások levonása előtt létrehozott munkaterülettel, akkor a [Azure PowerShell](#powershell), az [Azure CLI](#cli)és a [Azure Portal](#portal)használatával felügyelheti/konfigurálhatja a nyilvános társításokat.

>[!NOTE]
>A nyilvános társítás elavult. Az új ExpressRoute-áramkörökben nem hozhatók létre nyilvános társak. Ha egy új ExpressRoute-áramkört használ, használja a [Microsoft-partnert](expressroute-circuit-peerings.md#microsoftpeering) az Azure-szolgáltatásokhoz.
>

## <a name="connectivity"></a>Kapcsolatok

Kapcsolat WAN hálózatból mindig kezdeményezett a Microsoft Azure-szolgáltatásokhoz. Microsoft Azure-szolgáltatások nem lesz képes kezdeményeznek kapcsolatokat a hálózatban az útválasztási tartomány segítségével. Ha a ExpressRoute-áramkör engedélyezve van az Azure-beli nyilvános kapcsolatok esetében, az [Azure-ban használt nyilvános IP-tartományokat](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) az áramkörön keresztül érheti el.

A nyilvános társítás engedélyezése után a legtöbb Azure-szolgáltatáshoz csatlakozhat. Azt teszi lehetővé külön-külön válassza ki a szolgáltatásokat, amelyhez a Microsoft felé haladó útvonalak meghirdetéséhez.

* Az Azure Storage, az SQL Database és a websites szolgáltatás nyilvános IP-címeken elérhető.
* A nyilvános partneri útválasztási tartományon keresztül privát módon csatlakozhat a nyilvános IP-címeken üzemeltetett szolgáltatásokhoz, beleértve a Cloud Services VIP-címét is.
* A nyilvános társviszony-létesítési tartományhoz csatlakozni a DMZ-t, és az összes Azure-szolgáltatások a saját nyilvános IP-címek kapcsolat WAN hálózatból nélkül az interneten keresztül.

## <a name="services"></a>Services

Ez a szakasz a nyilvános társításon keresztül elérhető szolgáltatásokat mutatja be. Mivel a nyilvános társítás elavult, nem lehet új vagy további szolgáltatásokat hozzáadni a nyilvános társak számára. Ha nyilvános társítást használ, és a használni kívánt szolgáltatás csak Microsoft-partneri kapcsolaton keresztül támogatott, akkor a Microsoft-társra kell váltania. A támogatott szolgáltatások listáját lásd: [Microsoft-partnerek](expressroute-faqs.md#microsoft-peering) .

**Támogatott**

* Power BI
* Az Azure-szolgáltatások többsége támogatottak. Jelölje be a közvetlenül a támogatás ellenőrzéséhez használni kívánt szolgáltatást.

**Nem támogatott:**
  * Tartalomkézbesítési hálózat (CDN)
  * Azure Front Door
  * Multi-Factor Authentication kiszolgáló (örökölt)
  * Traffic Manager

Egy adott szolgáltatás rendelkezésre állásának ellenőrzéséhez ellenőrizheti a szolgáltatás dokumentációját, és ellenőrizheti, hogy van-e fenntartott tartomány közzétéve az adott szolgáltatás számára. Ezután megkeresheti a cél szolgáltatás IP-tartományait, és összehasonlíthatja az [Azure IP-tartományok és szolgáltatások címkék – nyilvános felhő XML-fájljában](https://www.microsoft.com/download/details.aspx?id=56519)felsorolt tartományokkal. Azt is megteheti, hogy megnyit egy támogatási jegyet a kérdéses szolgáltatáshoz a tisztázás érdekében.

## <a name="compare"></a>Egyenrangú összehasonlítás

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Az Azure nyilvános társítása 1 NAT IP-címmel rendelkezik, amely az egyes BGP-munkamenetekhez van társítva. Ha több mint 2 NAT IP-címet szeretne, lépjen a Microsoft-partneri hálózatra. A Microsoft-partnerek lehetővé teszik saját NAT-foglalások konfigurálását, valamint az útválasztási szűrők használatát a szelektív előtaggal kapcsolatos hirdetményekhez. További információ: [áttérés a Microsoft-partneri](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)hálózatra.
>

## <a name="custom-route-filters"></a>Egyéni útválasztási szűrők

Egyéni útvonalszűrőket meghatározhatja csak a szükséges útvonalakat használhat a hálózaton belül. Az útválasztási konfigurációval kapcsolatos részletes információkért tekintse meg az [Útválasztás](expressroute-routing.md) lapot.

## <a name="powershell"></a>Azure PowerShell lépések


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Mivel a nyilvános társítás elavult, nem lehet új ExpressRoute-áramkörön konfigurálni a nyilvános társítást.

1. Győződjön meg arról, hogy rendelkezik egy kiépített és engedélyezett ExpressRoute-áramkörrel. Használja a következő példát:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A válasz a következő példához hasonló:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. Győződjön meg arról, hogy rendelkezik a következő adatokat, mielőtt folytatná.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Nem kötelező:
   * Egy MD5-kivonat, ha használni kívánja.

   Futtassa az alábbi példa konfigurálása az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Ha MD5-kivonatot használja, használja a következő példát:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
   > 
   >

### <a name="getpublic"></a>Az Azure-beli nyilvános partneri kapcsolat részleteinek beszerzése

Konfiguráció részleteit az alábbi parancsmaggal kérheti le:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Az Azure nyilvános társ-összevonási konfigurációjának frissítése

Az alábbi példa a konfiguráció bármelyik részét frissítheti. Ebben a példában a kör VLAN-azonosító frissítése folyamatban van 200-ról 600-ra.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Az Azure nyilvános társának törlése

A társviszony-létesítés konfigurációját a következő példa futtatásával távolíthatja el:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="cli"></a>Azure CLI-lépések


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Ellenőrizze a ExpressRoute-kapcsolatcsoport győződjön meg arról, hogy kiosztott és engedélyezett. Használja a következő példát:

   ```azurecli-interactive
   az network express-route list
   ```

   A válasz a következő példához hasonló:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. Győződjön meg arról, hogy rendelkezik a következő adatokat, mielőtt folytatná.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Nem **kötelező –** Egy MD5-kivonat, ha úgy dönt, hogy használ egyet.

   Futtassa az alábbi példa az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz konfigurálása:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Ha MD5-kivonatot használja, használja a következő példát:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.

### <a name="getpublic"></a>Az Azure nyilvános peering részleteinek megtekintése

Konfiguráció részleteit az alábbi példa használatával kérheti le:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

A kimenet a következő példához hasonló:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatepublic"></a>Az Azure nyilvános társ-összevonási konfigurációjának frissítése

Az alábbi példa a konfiguráció bármelyik részét frissítheti. Ebben a példában a kör VLAN-azonosító frissítése folyamatban van 200-ról 600-ra.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="deletepublic"></a>Az Azure nyilvános társának törlése

A társviszony-létesítés konfigurációját a következő példa futtatásával távolíthatja el:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="portal"></a>Azure Portal lépések

A társítás konfigurálásához használja a cikkben található PowerShell vagy parancssori felület lépéseit. A társítások kezeléséhez az alábbi szakaszt használhatja. Ezek a lépések a [Microsoft-partnerek a portálon](expressroute-howto-routing-portal-resource-manager.md#msft)való kezeléséhez hasonló módon jelennek meg.

### <a name="get"></a>Az Azure nyilvános peering részleteinek megtekintése

Tekintse meg az Azure-beli nyilvános társak tulajdonságait a portálon való társítás kiválasztásával.

### <a name="update"></a>Az Azure nyilvános társ-összevonási konfigurációjának frissítése

Válassza ki a társításhoz tartozó sort, majd módosítsa a társítási tulajdonságokat.

### <a name="delete"></a>Az Azure nyilvános társának törlése

A Törlés ikonra kattintva távolítsa el a társ-konfigurációt.

## <a name="next-steps"></a>Következő lépések

A következő lépés [a virtuális hálózat összekapcsolása egy ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md).

* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
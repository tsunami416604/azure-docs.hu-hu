---
title: Azure ExpressRoute nyilvános társviszony-létesítés létrehozása és kezelése
description: Az Azure nyilvános társviszony-létesítéséről és kezeléséről
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481133"
---
# <a name="create-and-manage-expressroute-public-peering"></a>ExpressRoute nyilvános társviszony-létesítéslétrehozása és kezelése

> [!div class="op_single_selector"]
> * [Cikk - Nyilvános társviszony-létesítés](about-public-peering.md)
> * [Videó - Nyilvános társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Cikk – Microsoft társviszony-létesítés](expressroute-circuit-peerings.md#microsoftpeering)
>

Ez a cikk segít a nyilvános társviszony-létesítési útválasztási konfiguráció létrehozása és kezelése egy ExpressRoute-kapcsolati kapcsolati kapcsolati kapcsolati kapcsolati műveletterv konfigurációját. Ellenőrizheti az állapotát, frissítését vagy törlését és megszüntetését is. Ez a cikk azokra az Erőforrás-kezelő-áramkörökre vonatkozik, amelyeket a nyilvános társviszony-létesítés elavultak előtt hoztak létre. Ha korábban már meglévő kapcsolatkapcsolattal rendelkezik (a nyilvános társviszony-létesítés elavultsága előtt hozták létre), kezelheti/konfigurálhatja a nyilvános társviszony-létesítést az [Azure PowerShell,](#powershell)az [Azure CLI](#cli)és az [Azure Portal](#portal)használatával.

>[!NOTE]
>A nyilvános társviszony-létesítés elavult. Nem hozhat létre nyilvános társviszony-létesítést az új ExpressRoute-kapcsolati körökön. Ha új ExpressRoute-kapcsolattal rendelkezik, használja a [Microsoft-társviszony-létesítést](expressroute-circuit-peerings.md#microsoftpeering) az Azure-szolgáltatásokhoz.
>

## <a name="connectivity"></a>Kapcsolatok

A kapcsolat mindig a WAN-ról a Microsoft Azure-szolgáltatásokkal történik. A Microsoft Azure-szolgáltatások nem tudnak kapcsolatot kezdeményezni a hálózatba ezen az útválasztási tartományon keresztül. Ha az ExpressRoute-kapcsolati kapcsolat engedélyezve van az Azure nyilvános társviszony-létesítés, elérheti az [Azure-ban használt nyilvános IP-tartományok](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) a kapcsolatcsoporton keresztül.

Ha a nyilvános társviszony-létesítés engedélyezve van, csatlakozhat a legtöbb Azure-szolgáltatáshoz. Nem engedélyezzük, hogy szelektíven válasszon olyan szolgáltatásokat, amelyekre útvonalakat hirdetünk.

* Szolgáltatások, például az Azure Storage, AZ SQL-adatbázisok és a webhelyek nyilvános IP-címeken érhetők el.
* A nyilvános társviszony-létesítési útválasztási tartományon keresztül privát módon csatlakozhat a nyilvános IP-címeken tárolt szolgáltatásokhoz, beleértve a felhőszolgáltatások VIP-címeit is.
* A nyilvános társviszony-létesítési tartományt csatlakoztathatja a DMZ-hez, és csatlakozhat az összes Azure-szolgáltatáshoz a nyilvános IP-címükön a WAN-ról anélkül, hogy az interneten keresztül kellene csatlakoznia.

## <a name="services"></a><a name="services"></a>Szolgáltatások

Ez a szakasz a nyilvános társviszony-létesítéssorán elérhető szolgáltatásokat mutatja be. Mivel a nyilvános társviszony-létesítés elavult, nincs terv új vagy további szolgáltatások hozzáadása a nyilvános társviszony-létesítéshez. Ha nyilvános társviszony-létesítést használ, és a használni kívánt szolgáltatás csak a Microsoft társviszony-létesítésén keresztül támogatott, át kell váltania a Microsoft társviszony-létesítésre. A támogatott szolgáltatások listáját a [Microsoft társviszony-létesítése](expressroute-faqs.md#microsoft-peering) című témakörben található.

**Támogatott:**

* Power BI
* A legtöbb Azure-szolgáltatások támogatottak. Ellenőrizze közvetlenül a használni kívánt szolgáltatást a támogatás ellenőrzéséhez.

**Nem támogatott:**
  * Tartalomkézbesítési hálózat (CDN)
  * Azure Front Door
  * Többtényezős hitelesítési kiszolgáló (örökölt)
  * Traffic Manager

Egy adott szolgáltatás rendelkezésre állásának ellenőrzéséhez ellenőrizze a szolgáltatás dokumentációjában, hogy van-e az adott szolgáltatáshoz fenntartott tartomány. Ezután megkeresheti a célszolgáltatás IP-tartományait, és összehasonlíthatja az [Azure IP-tartományokban és a szolgáltatáscímkék – nyilvános felhőXML-fájlban](https://www.microsoft.com/download/details.aspx?id=56519)felsorolt tartományokkal. Másik lehetőségként megnyithat egy támogatási jegyet a kérdéses szolgáltatáshoz a pontosítás érdekében.

## <a name="peering-comparison"></a><a name="compare"></a>Társviszony-létesítés összehasonlítása

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Az Azure nyilvános társviszony-létesítés1 NAT IP-címet társított minden BGP-munkamenethez. 2 NAT IP-címnél nagyobb esetén lépjen a Microsoft társviszony-létesítési lehetőségre. A Microsoft társviszony-létesítéslehetővé teszi a saját NAT-foglalások konfigurálását, valamint a szelektív előtaghirdetések hez használható útvonalszűrők használatát. További információt az [Áthelyezés a Microsoft-társviszony-létesítésre](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)című témakörben talál.
>

## <a name="custom-route-filters"></a>Egyéni útvonalszűrők

A hálózaton belül egyéni útvonalszűrőket határozhat meg, hogy csak a szükséges útvonalakat használja fel. Az útválasztási konfigurációval kapcsolatos részletes információkat az [Útválasztás](expressroute-routing.md) lapon találja.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Az Azure PowerShell lépései


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Mivel a nyilvános társviszony-létesítés elavult, nem konfigurálható nyilvános társviszony-létesítés egy új ExpressRoute-kapcsolati kapcsolaton.

1. Ellenőrizze, hogy van-e olyan ExpressRoute-kapcsolat, amely ki van építve és egyben engedélyezve is van.Verify that you have an ExpressRoute circuited that is enabled. Használja a következő példát:

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
2. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik az alábbi információkkal.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Nem kötelező:
   * Egy MD5-kivonat, ha használni kívánja.

   Futtassa a következő példát az Azure nyilvános társviszony-létesítésének konfigurálásához a hálózathoz

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Ha MD5-kivonatot szeretne használni, használja a következő példát:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Az Azure nyilvános társviszony-létesítési részleteinek beszereznie

A konfiguráció részleteit a következő parancsmag segítségével kaphatja meg:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure nyilvános társviszony-létesítés konfigurációjának frissítése

A következő példával a konfiguráció bármely részét frissítheti. Ebben a példában az áramkör VLAN-azonosítója 200-ról 600-ra frissül.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure nyilvános társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a következő példa futtatásával távolíthatja el:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Az Azure CLI lépései


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Ellenőrizze az ExpressRoute-áramkört, hogy az ki van-e építve és engedélyezve van-e. Használja a következő példát:

   ```azurecli-interactive
   az network express-route list
   ```

   A válasz a következő példához hasonló:

   ```output
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

2. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik az alábbi információkkal.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * **Nem kötelező -** MD5-kivonat, ha úgy dönt, hogy használ egyet.

   Futtassa a következő példát az Azure nyilvános társviszony-létesítésének konfigurálásához a kapcsolatcsoporthoz:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Ha MD5-kivonatot szeretne használni, használja a következő példát:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Azure nyilvános társviszony-létesítés részleteinek megtekintése

A konfiguráció részleteit a következő példával kaphatja meg:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

A kimenet a következő példához hasonló:

```output
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

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure nyilvános társviszony-létesítés konfigurációjának frissítése

A következő példával a konfiguráció bármely részét frissítheti. Ebben a példában az áramkör VLAN-azonosítója 200-ról 600-ra frissül.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure nyilvános társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a következő példa futtatásával távolíthatja el:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Az Azure Portal lépései

A társviszony-létesítés konfigurálásához használja a jelen cikkben ismertetett PowerShell- vagy CLI-lépéseket. Társviszony-létesítés kezeléséhez használhatja az alábbi szakaszokat. Ezek a lépések a [microsoftos társviszony-létesítés nek a portálon történő](expressroute-howto-routing-portal-resource-manager.md#msft)kezeléséhez hasonlóan néznek ki.

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Azure nyilvános társviszony-létesítés részleteinek megtekintése

Tekintse meg az Azure nyilvános társviszony-létesítési tulajdonságait a társviszony-létesítés kiválasztásával a portálon.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Azure nyilvános társviszony-létesítés konfigurációjának frissítése

Jelölje ki a társviszony-létesítési sort, majd módosítsa a társviszony-létesítési tulajdonságokat.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Azure nyilvános társviszony-létesítés törlése

A törlésikon kiválasztásával távolítsa el a társviszony-létesítési konfigurációt.

## <a name="next-steps"></a>További lépések

Következő lépés: [Virtuális hálózat csatolása egy ExpressRoute-kapcsolattal.](expressroute-howto-linkvnet-arm.md)

* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
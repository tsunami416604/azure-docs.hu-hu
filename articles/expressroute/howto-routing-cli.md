---
title: 'Azure ExpressRoute: Társviszony-létesítés konfigurálása: CLI'
description: Ez a cikk segítséget nyújt az ExpressRoute-kapcsolat privát, nyilvános és Microsoft-társviszony-létesítésének létrehozásában és kiépítésében. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 91a1b6cc877b31fbcef638e34d3147d3377ce85c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476117"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Peering létrehozása és módosítása ExpressRoute-kapcsolati kapcsolatbanlatban cli használatával

Ez a cikk segítséget nyújt az ExpressRoute-kapcsolati/-társviszony-létesítési konfiguráció létrehozásához és kezeléséhez az Erőforrás-kezelő telepítési modelljében a CLI használatával. Egy ExpressRoute-kapcsolatkapcsolat állapotát, frissítését vagy törlését és megszüntetését is ellenőrizheti. Ha más módszert szeretne használni a kapcsolatcsoport használatához, válasszon ki egy cikket az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Nyilvános társviszony-létesítés](about-public-peering.md)
> * [Videó - Privát társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft-társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).
* A konfiguráció megkezdése előtt ellenőrizze, hogy áttekintette-e az [előfeltételeket,](expressroute-prerequisites.md) [az útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamat-lapokat.](expressroute-workflows.md)
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](howto-circuit-cli.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoportnak kiépített és engedélyezett állapotban kell lennie ahhoz, hogy futtatható legyenek a cikkben szereplő parancsok.

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt Layer 3 szolgáltatásokat (általában IPVPN-t, például MPLS-t) kínál, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást.

Konfigurálhatja a privát társviszony-létesítésés és a Microsoft-társviszony-létesítés egy ExpressRoute-kapcsolati kapcsolat (Az Azure nyilvános társviszony-létesítés elavult az új áramkörök). A társviszony-létesítés tetszőleges sorrendben konfigurálható. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. A tartományok és társviszony-létesítések útválasztási tartományairól további információt az [ExpressRoute útválasztási tartományok című témakörben talál.](expressroute-circuit-peerings.md) A nyilvános társviszony-létesítésről az [ExpressRoute nyilvános társviszony-létesítéscímű témakörben](about-public-peering.md)talál.

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft társviszony-létesítés

Ez a szakasz segítséget nyújt az ExpressRoute-kapcsolati kapcsolatbeállításainak létrehozásában, behírlése, frissítésében és törlésében.

> [!IMPORTANT]
> 2017. augusztus 1-je előtt konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése a Microsoft társviszony-létesítésével a Microsoft társviszony-létesítésén keresztül lesz meghirdetve, még akkor is, ha nincsenek megadva útvonalszűrők. 2017. augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése nem rendelkezik előtagokkal, amíg egy útvonalszűrő nem csatlakozik az áramkörhöz. További információt a [Microsoft-társviszony-létesítés útvonalszűrőjének konfigurálása](how-to-routefilter-powershell.md)című témakörben talál.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Telepítse az Azure CLI legújabb verzióját. Használja az Azure parancssori felület (CLI) legújabb verzióját. A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és [a munkafolyamatokat.](expressroute-workflows.md)

   ```azurecli
   az login
   ```

   Válassza ki azt az előfizetést, amelyhez ExpressRoute-áramkört szeretne létrehozni.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot. Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](howto-circuit-cli.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha a kapcsolódási szolgáltató felügyelt Layer 3 szolgáltatásokat kínál, megkérheti a kapcsolódási szolgáltatót, hogy engedélyezze a Microsoft-társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató nem kezeli az útválasztást, a kapcsolatcsoport létrehozása után folytassa a konfigurációt a következő lépésekkel. 

3. Ellenőrizze az ExpressRoute-kapcsolatcsoportban, hogy ki van-e építve és engedélyezve van-e. Használja a következő példát:

   ```azurecli
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

4. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagok készletét tervezi elküldeni, vesszővel tagolt listát küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * **Nem kötelező -** Ügyfél ASN: Ha olyan előtagokat hirdet, amelyek nincsenek regisztrálva a társviszony-létesítő AS-számhoz, megadhatja azt az AS-számot, amelyre regisztrálva vannak.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * **Nem kötelező -** MD5-kivonat, ha úgy dönt, hogy használ egyet.

   Futtassa a következő példát a Microsoft-társviszony-létesítés konfigurálásához a kapcsolatcsoporthoz:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft társviszony-létesítés részleteinek megtekintése

A konfiguráció részleteit a következő példával kaphatja meg:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> A Microsoft ellenőrzi, hogy a megadott "Hirdetett nyilvános előtagok" és a "Társ ASN" (vagy "Customer ASN") hozzá vannak-e rendelve az Internetes útválasztási beállításjegyzékben. Ha a nyilvános előtagokat egy másik entitástól kapja, és a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, az automatikus érvényesítés nem fejeződik be, és manuális érvényesítést igényel. Ha az automatikus érvényesítés sikertelen, a fenti parancs kimenetén a "HirdetettPublicPrefixesState" "Érvényesítés szükséges" lesz. 
> 
> Ha az "Érvényesítés szükséges" üzenet jelenik meg, gyűjtse össze azokat a dokumentum(okait), amelyek(ek)et az útválasztási jegyzékben az előtagok tulajdonosaként felsorolt entitás a szervezethez rendeli,(/a) gyűjtse össze azokat a dokumentum(okat), amelyek (2) bekezdéssel megegyezésre vannak. támogatási jegy megnyitását az alábbiak szerint. 
> 
>

A kimenet a következő példához hasonló:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
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

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármely részét frissítheti. Az áramkör hirdetett előtagai a következő példában 123.1.0.0/24-ről 124.1.0.0/24-re frissülnek:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>IPv6-microsoftos társviszony-létesítési beállítások hozzáadása meglévő IPv4-konfigurációhoz

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a következő példa futtatásával távolíthatja el:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="azure-private-peering"></a><a name="private"></a>Azure privát társviszony-létesítés

Ez a szakasz segít létrehozni, beszerezni, frissíteni és törölni az Azure privát társviszony-létesítési konfiguráció egy ExpressRoute-kapcsolati kapcsolat.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Telepítse az Azure CLI legújabb verzióját. A konfiguráció megkezdése előtt az Azure parancssori felület (CLI) legújabb verzióját kell használnia.* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és [a munkafolyamatokat.](expressroute-workflows.md)

   ```azurecli
   az login
   ```

   Válassza ki az előfizetést, amelyben az ExpressRoute-kapcsolatcsoportot létre kívánja hozni.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot. Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](howto-circuit-cli.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha a kapcsolódási szolgáltató felügyelt Layer 3 szolgáltatásokat kínál, kérheti meg a kapcsolatszolgáltatótól, hogy engedélyezze az Azure privát társviszony-létesítését. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató nem kezeli az útválasztást, a kapcsolatcsoport létrehozása után folytassa a konfigurációt a következő lépésekkel.

3. Ellenőrizze az ExpressRoute-kapcsolatcsoportban, hogy ki van-e építve és engedélyezve van-e. Használja a következő példát:

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
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

4. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem lehet része a virtuális hálózatok számára fenntartott címtérnek.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem lehet része a virtuális hálózatok számára fenntartott címtérnek.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ne használja a 65515 számot.
   * **Nem kötelező -** MD5-kivonat, ha úgy dönt, hogy használ egyet.

   Az alábbi példában konfigurálhatja az Azure privát társviszony-létesítését a kapcsolatcsoporthoz:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Ha MD5-kivonatot szeretne használni, használja a következő példát:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Azure privát társviszony-létesítés részleteinek megtekintése

A konfiguráció részleteit a következő példával kaphatja meg:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

A kimenet a következő példához hasonló:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
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

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A következő példával a konfiguráció bármely részét frissítheti. Ebben a példában az áramkör VLAN-azonosítója 100-ról 500-ra frissül.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure privát társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a következő példa futtatásával távolíthatja el:

> [!WARNING]
> A példa futtatása előtt gondoskodnia kell arról, hogy az összes virtuális hálózat és az ExpressRoute globális elérési kapcsolat törlődjön. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>További lépések

A következő lépés egy [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](howto-linkvnet-cli.md).

* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).

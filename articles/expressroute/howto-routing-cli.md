---
title: 'Azure ExpressRoute: társítás konfigurálása: parancssori felület'
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és helyezhet üzembe egy ExpressRoute-áramkör privát, nyilvános és Microsoft-társait. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: cbde41bd79409255e1ebf5145548ea260ac8581d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84727041"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>ExpressRoute-áramkör társításának létrehozása és módosítása a parancssori felület használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet útválasztási konfigurációt/társítást egy ExpressRoute-áramkörhöz a Resource Manager-alapú üzemi modellben a CLI használatával. A ExpressRoute-áramkör állapota, frissítése vagy törlése és megszüntetése is ellenőrizhető. Ha más módszert szeretne használni az áramkörrel való munkavégzéshez, válasszon egy cikket a következő listából:

> [!div class="op_single_selector"]
> * [Azure Portalra](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Nyilvános társak](about-public-peering.md)
> * [Videó – privát peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).
* A konfigurálás megkezdése előtt ellenőrizze, hogy áttekintette-e az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamat](expressroute-workflows.md) -lapokat.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](howto-circuit-cli.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie ahhoz, hogy futtatni tudja a cikkben szereplő parancsokat.

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegbeli szolgáltatásokat kínál (jellemzően IPVPN, például MPLS), akkor a kapcsolati szolgáltató konfigurálja és kezeli az útválasztást.

A ExpressRoute-áramkörhöz privát és Microsoft-társítást is beállíthat (az Azure nyilvános társítása elavult az új áramkörök esetében). A társításokat tetszőleges sorrendben lehet konfigurálni. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. További információ az útválasztási tartományokról és a társításokról: [ExpressRoute-útválasztási tartományok](expressroute-circuit-peerings.md). A nyilvános hálózattal kapcsolatos további információkért lásd: [nyilvános ExpressRoute](about-public-peering.md).

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft társviszony-létesítés

Ez a szakasz segítséget nyújt egy ExpressRoute-áramkör Microsoft-társi konfigurációjának létrehozásához, lekéréséhez, frissítéséhez és törléséhez.

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás-előtagokkal rendelkezik, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő. További információ: [útválasztási szűrő beállítása a Microsoft-társak](how-to-routefilter-powershell.md)számára.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Telepítse az Azure CLI legújabb verzióját. Használja az Azure parancssori felület (CLI) legújabb verzióját. A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .

   ```azurecli
   az login
   ```

   Válassza ki azt az előfizetést, amelyhez ExpressRoute-áramkört kíván létrehozni.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot. Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](howto-circuit-cli.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha a kapcsolat szolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a kapcsolat szolgáltatóját, hogy engedélyezze a Microsoft-partneri kapcsolatot. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa a konfigurációt a következő lépésekkel. 

3. Ellenőrizze a ExpressRoute áramkört, és győződjön meg arról, hogy az üzembe helyezése és engedélyezése is megtörténik. Használja a következő példát:

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
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagokat szeretne elküldeni, vesszővel tagolt listát is küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * Nem **kötelező –** Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem szerepelnek a társítás AS számában, megadhatja az AS-számot, amelyre regisztrálva vannak.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Nem **kötelező –** Egy MD5-kivonat, ha úgy dönt, hogy használ egyet.

   A következő példa futtatásával konfigurálja a Microsoft-társat az áramkörhöz:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft társviszony-létesítés részleteinek megtekintése

A konfigurációs adatokat a következő példa használatával érheti el:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> A Microsoft ellenőrzi, hogy a megadott "meghirdetett nyilvános előtagok" és "peer ASN" (vagy "Customer ASN") hozzá van-e rendelve az Internet Routing beállításjegyzékben. Ha egy másik entitásból kéri le a nyilvános előtagokat, és ha a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, akkor az automatikus ellenőrzés nem fejeződik be, és manuális ellenőrzésre van szükség. Ha az automatikus ellenőrzés sikertelen, a fenti parancs kimenetén megjelenik a "AdvertisedPublicPrefixesState" érvényesítési művelet. 
> 
> Ha az "érvényesítés szükséges" üzenet jelenik meg, Gyűjtse össze azokat a dokumentumokat, amelyek megjelenítik a nyilvános előtagokat a szervezethez az útválasztási beállításjegyzékben szereplő előtagok tulajdonosának listáján, majd a dokumentumok manuális ellenőrzéshez való beküldéséhez az alább látható módon nyisson meg egy támogatási jegyet. 
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

A konfiguráció bármely részét frissítheti. Az áramkör meghirdetett előtagjai a következő példában szereplő 123.1.0.0/24-ről 124.1.0.0/24-re frissülnek:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>IPv6 Microsoft-partneri beállítások hozzáadása meglévő IPv4-konfigurációhoz

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft társviszony-létesítés törlése

A következő példa futtatásával távolíthatja el a társ-konfigurációt:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="azure-private-peering"></a><a name="private"></a>Azure privát társviszony-létesítés

Ez a szakasz segítséget nyújt egy ExpressRoute-áramkör Azure-beli privát társ-összevonási konfigurációjának létrehozásához, lekéréséhez, frissítéséhez és törléséhez.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Telepítse az Azure CLI legújabb verzióját. Az Azure parancssori felület (CLI) legújabb verzióját kell használnia. * a konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .

   ```azurecli
   az login
   ```

   Válassza ki az előfizetést, amelyben az ExpressRoute-kapcsolatcsoportot létre kívánja hozni.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
2. Hozzon létre egy ExpressRoute-kapcsolatcsoportot. Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](howto-circuit-cli.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha a kapcsolat szolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a kapcsolati szolgáltatót, hogy engedélyezze az Azure-beli privát kapcsolatot. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa a konfigurációt a következő lépésekkel.

3. Ellenőrizze a ExpressRoute áramkört, és győződjön meg arról, hogy az üzembe helyezése és engedélyezése is megtörténik. Használja a következő példát:

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

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem lehet a virtuális hálózatok számára fenntartott címterület része.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem lehet a virtuális hálózatok számára fenntartott címterület része.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ne használja a 65515 számot.
   * Nem **kötelező –** Egy MD5-kivonat, ha úgy dönt, hogy használ egyet.

   Az alábbi példát követve konfigurálhatja az Azure-beli privát kapcsolatot az áramkörhöz:

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

A konfigurációs adatokat a következő példa használatával érheti el:

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

A konfiguráció bármely részét frissítheti a következő példán keresztül. Ebben a példában az áramkör VLAN-azonosítója 100 és 500 között frissül.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure privát társviszony-létesítés törlése

A következő példa futtatásával távolíthatja el a társ-konfigurációt:

> [!WARNING]
> A példa futtatása előtt győződjön meg arról, hogy a virtuális hálózatok és a ExpressRoute összes Global Reach kapcsolata el lesz távolítva. 
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

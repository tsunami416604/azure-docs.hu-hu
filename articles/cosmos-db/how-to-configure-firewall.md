---
title: IP-tűzfal konfigurálása az Azure Cosmos DB-fiókjához
description: Megtudhatja, hogyan konfigurálhatja az IP-hozzáférés-vezérlési szabályzatokat az Azure Cosmos-fiókok tűzfaltámogatásához.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 1c24782285ac9b06d5499351eebe1693ade07297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78162944"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>IP-tűzfal konfigurálása az Azure Cosmos DB-ben

Az Azure Cosmos DB-fiókjában tárolt adatokat IP-tűzfalak használatával biztosíthatja. Az Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlésa a bejövő tűzfal támogatása. Az Alábbi módokon állíthatja be az IP-tűzfalat az Azure Cosmos DB-fiókban:

* Az Azure Portalról
* Deklaratívan az Azure Resource Manager-sablon használatával
* Az Azure CLI vagy az Azure PowerShell programozott módon az **ipRangeFilter** tulajdonság frissítésével

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a>IP-tűzfal konfigurálása az Azure Portal használatával

Az IP-hozzáférés-vezérlési szabályzat beállítása az Azure Portalon, lépjen az Azure Cosmos DB-fiók lapra, és válassza a tűzfal és a **virtuális hálózatok** a navigációs menüben. Módosítsa a **Hozzáférés engedélyezése** a **kijelölt hálózatokra**értéket, majd kattintson a **Mentés gombra.**

![Képernyőkép, amely bemutatja, hogyan nyitható meg a Tűzfal lap az Azure Portalon](./media/how-to-configure-firewall/azure-portal-firewall.png)

Ha az IP-hozzáférés-vezérlés be van kapcsolva, az Azure Portal lehetővé teszi az IP-címek, IP-címtartományok és kapcsolók megadását. A kapcsolók lehetővé teszik a hozzáférést más Azure-szolgáltatásokhoz és az Azure Portalhoz. A következő szakaszok részletesen ismertetik ezeket a kapcsolókat.

> [!NOTE]
> Miután engedélyezte az Azure Cosmos DB-fiókjához az IP-hozzáférés-vezérlési szabályzatot, az Azure Cosmos DB-fiókjához az IP-címtartományok megengedett listáján kívül álló gépekről érkező összes kérést elutasítja a rendszer. Az Azure Cosmos DB-erőforrások a portálról is le van tiltva a hozzáférés-vezérlés integritásának biztosítása érdekében.

### <a name="allow-requests-from-the-azure-portal"></a>Kérelmek engedélyezése az Azure Portalról

Ha programozott módon engedélyezi az IP-hozzáférés-vezérlési házirendet, a hozzáférés fenntartásához hozzá kell adnia az Azure Portal IP-címét az **ipRangeFilter** tulajdonsághoz. A portál IP-címei a következők:

|Régió|IP-cím|
|------|----------|
|Németország|51.4.229.218|
|Kína|139.217.8.252|
|US Gov|52.244.48.71|
|Minden más régió|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Az Azure Portal elérésére irányuló kérelmeket az **Azure Portalon való hozzáférés engedélyezése** lehetőség kiválasztásával engedélyezheti, ahogy az a következő képernyőképen látható:

![Képernyőkép az Azure Portal-hozzáférés engedélyezéséről](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Globális Azure-adatközpontokból vagy egyéb Azure-beli forrásokból érkező kérések engedélyezése

Ha az Azure Cosmos DB-fiókját olyan szolgáltatásokból éri el, amelyek nem biztosítanak statikus IP-címet (például Az Azure Stream Analytics és az Azure Functions), továbbra is használhatja az IP-tűzfalat a hozzáférés korlátozására. Az Azure-on belül más forrásokból is engedélyezheti a hozzáférést az **Azure-adatközpontokon belüli kapcsolatok elfogadása** lehetőség kiválasztásával, ahogy az az alábbi képernyőképen látható:

![Képernyőkép, amely bemutatja, hogyan nyitható meg a Tűzfal lap az Azure Portalon](./media/how-to-configure-firewall/enable-azure-services.png)

Ha engedélyezi ezt a beállítást, az IP-cím `0.0.0.0` hozzáadódik az engedélyezett IP-címek listájához. Az `0.0.0.0` IP-cím korlátozza a kérelmeket az Azure Cosmos DB-fiók az Azure datacenter IP-tartományból. A beállítás semmilyen más IP-tartomány számára nem engedélyezi a hozzáférést az Azure Cosmos DB-fiókhoz.

> [!NOTE]
> Ez a beállítás úgy konfigurálja a tűzfalat, hogy az Azure-ból érkező összes kérést engedélyezze, beleértve az Azure-ban telepített más ügyfelek előfizetéseitől érkező kérelmeket is. Az IP-k e beállítás által engedélyezett listája széles, így korlátozza a tűzfalházirendek hatékonyságát. Csak akkor használja ezt a beállítást, ha a kérések nem statikus IP-kből vagy virtuális hálózatok alhálózataiból származnak. Ez a beállítás automatikusan engedélyezi a hozzáférést az Azure Portalról, mert az Azure Portal az Azure-ban van telepítve.

### <a name="requests-from-your-current-ip"></a>Kérések az aktuális IP-címről

A fejlesztés egyszerűsítése érdekében az Azure Portal segít azonosítani és hozzáadni az ügyfélgép IP-címét az engedélyezett listához. A gépet futtató alkalmazások ezután hozzáférhetnek az Azure Cosmos DB-fiókjához.

A portál automatikusan észleli az ügyfél IP-címét. Lehet, hogy a számítógép ügyfél IP-címe, vagy a hálózati átjáró IP-címe. Győződjön meg róla, hogy távolítsa el ezt az IP-címet, mielőtt a számítási feladatok éles környezetben.

Ha hozzá szeretné adni az aktuális IP-címet az IP-k listájához, válassza **az Aktuális IP hozzáadása**lehetőséget. Ezután válassza a **Save** (Mentés) lehetőséget.

![Képernyőkép az aktuális IP tűzfalbeállításainak konfigurálásáról](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Kérelmek a felhőszolgáltatásoktól

Az Azure-ban a felhőszolgáltatások gyakori módjai a középső rétegbeli szolgáltatási logika üzemeltetésének az Azure Cosmos DB használatával. Az Azure Cosmos DB-fiókjához való hozzáférés engedélyezéséhez egy felhőszolgáltatásból, hozzá kell adnia a felhőszolgáltatás nyilvános IP-címét az Azure Cosmos DB-fiókjához társított IP-címek engedélyezett listájához [az IP-hozzáférés-vezérlési szabályzat konfigurálásával.](#configure-ip-policy) Ez biztosítja, hogy a felhőszolgáltatások minden szerepkörpéldánya hozzáférhessen az Azure Cosmos DB-fiókjához.

A felhőszolgáltatások IP-címeit az Azure Portalon szerezheti be, ahogy az a következő képernyőképen látható:

![Képernyőkép az Azure Portalon megjelenő felhőszolgáltatás nyilvános IP-címén](./media/how-to-configure-firewall/public-ip-addresses.png)

Amikor szerepkörpéldányok hozzáadásával horizontális felskálázhatja a felhőszolgáltatást, ezek az új példányok automatikusan hozzáférhetnek az Azure Cosmos DB-fiókhoz, mert ugyanannak a felhőszolgáltatásnak a részét képezik.

### <a name="requests-from-virtual-machines"></a>Virtuális gépekről érkező kérelmek

[Virtuális gépek](https://azure.microsoft.com/services/virtual-machines/) vagy [virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) is használhatja a középső rétegbeli szolgáltatások üzemeltetéséhez az Azure Cosmos DB használatával. A Cosmos DB-fiók olyan konfigurálásához, hogy az lehetővé tegye a virtuális gépekről való hozzáférést, az [IP-hozzáférés-vezérlési szabályzat konfigurálásával konfigurálnia](#configure-ip-policy)kell a virtuális gép és/vagy a virtuálisgép-méretezési csoport nyilvános IP-címét az Azure Cosmos DB-fiók egyik engedélyezett IP-címén.

A virtuális gépek IP-címeit az Azure Portalon is lekérheti, ahogy az a következő képernyőképen látható:

![Az Azure Portalon megjelenő virtuális gépek nyilvános IP-címét bemutató képernyőkép](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Amikor virtuálisgép-példányokat ad hozzá a csoporthoz, azok automatikusan hozzáférést kapnak az Azure Cosmos DB-fiókjához.

### <a name="requests-from-the-internet"></a>Kérések az internetről

Amikor az Azure Cosmos DB-fiókját egy internetes számítógépről éri el, a számítógép ügyfél IP-címét vagy IP-címtartományát hozzá kell adni a fiókjához engedélyezett IP-címek listájához.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>IP-tűzfal konfigurálása Erőforrás-kezelő sablon használatával

Az Azure Cosmos DB-fiók hozzáférés-vezérlésének konfigurálásához győződjön meg arról, hogy az Erőforrás-kezelő sablon megadja az **ipRangeFilter** attribútumot az engedélyezett IP-tartományok listájával. Ha az IP-tűzfal konfigurálása egy már `locations` telepített Cosmos-fiókhoz, győződjön meg arról, hogy a tömb megegyezik a jelenleg telepítettvel. A tömb és `locations` más tulajdonságok egyidejűleg nem módosíthatók. További információ és minták az Azure Resource Manager-sablonok az Azure Cosmos DB-hoz lásd: [Azure Resource Manager-sablonok az Azure Cosmos DB-hez](resource-manager-samples.md)

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>IP-hozzáférés-vezérlési házirend konfigurálása az Azure CLI használatával

A következő parancs bemutatja, hogyan hozhat létre egy Azure Cosmos DB-fiókot IP-hozzáférés-vezérléssel:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>IP-hozzáférés-vezérlési házirend konfigurálása a PowerShell használatával

Az alábbi parancsfájl bemutatja, hogyan hozhat létre egy Azure Cosmos DB-fiókot AZ IP-hozzáférés-vezérléssel:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRangeFilter = "192.168.221.17,183.240.196.255,40.76.54.131"

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRangeFilter"=$ipRangeFilter
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Ip-hozzáférés-vezérlési házirenddel kapcsolatos problémák elhárítása

Az IP-hozzáférés-vezérlési házirendekkel kapcsolatos problémákat az alábbi beállításokkal háríthatja el:

### <a name="azure-portal"></a>Azure portál

Az Azure Cosmos DB-fiókjához egy IP-hozzáférés-vezérlési szabályzat engedélyezésével letilthatja a fiókjához intézett összes kérelmet az IP-címtartományok megengedett listáján kívül álló gépekről. A portáladat-sík műveletek, például a tárolók böngészése és a dokumentumok lekérdezése engedélyezéséhez explicit módon engedélyeznie kell az Azure Portal-hozzáférést a **portál tűzfal** ablaktáblájának használatával.

### <a name="sdks"></a>SDK-k

Amikor az Azure Cosmos DB-erőforrásokat az engedélyezett listában nem szereplő gépekről származó SDK-k használatával éri el, a rendszer további részletek nélkül egy általános **403-as tiltott** választ ad vissza. Ellenőrizze a fiók engedélyezett IP-listáját, és győződjön meg arról, hogy a megfelelő házirend-konfiguráció van alkalmazva az Azure Cosmos DB-fiókjára.

### <a name="source-ips-in-blocked-requests"></a>Forrás IP-k a letiltott kérelmekben

Engedélyezze a diagnosztikai naplózást az Azure Cosmos DB-fiókjában. Ezek a naplók minden kérést és választ jelenítmeg. A tűzfallal kapcsolatos üzeneteket a rendszer 403-as visszatérési kóddal naplózza. Ezeknek az üzeneteknek a szűrésével láthatja a letiltott kérelmek forrás IP-jeit. Lásd: [Azure Cosmos DB diagnosztikai naplózás.](logging.md)

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Az Azure Cosmos DB szolgáltatásvégpontjával rendelkező alhálózatból érkező kérelmek

Az Azure Cosmos DB szolgáltatásvégpontot tartalmazó virtuális hálózat alhálózatából érkező kérelmek a virtuális hálózatot és az alhálózati identitást az Azure Cosmos DB-fiókokba küldik. Ezek a kérelmek nem rendelkeznek a forrás nyilvános IP-címével, ezért az IP-szűrők elutasítják őket. Ha engedélyezni szeretné a hozzáférést a virtuális hálózatok adott alhálózataiból, adjon hozzá egy hozzáférés-vezérlési listát az [Azure Cosmos DB-fiókjához a virtuális hálózat és az alhálózati hozzáférés konfigurálása](how-to-configure-vnet-service-endpoint.md)című részében ismertetett módon. A tűzfalszabályok alkalmazása akár 15 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

Virtuális hálózati szolgáltatásvégpont konfigurálásához az Azure Cosmos DB-fiókjához az alábbi cikkekben:

* [Virtuális hálózat- és alhálózati hozzáférés-vezérlés az Azure Cosmos DB-fiókjához](vnet-service-endpoint.md)
* [Virtuális hálózat és alhálózati hozzáférés konfigurálása az Azure Cosmos DB-fiókjához](how-to-configure-vnet-service-endpoint.md)

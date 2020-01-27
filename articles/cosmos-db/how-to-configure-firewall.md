---
title: IP-tűzfal konfigurálása a Azure Cosmos DB-fiókhoz
description: Ismerje meg, hogyan konfigurálhatja az IP-hozzáférés-vezérlési házirendeket az Azure Cosmos-fiókok tűzfal általi támogatásához.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 8522a537301c1d35da2a2eb46b4374fa4daf6a27
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580684"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>IP-tűzfal konfigurálása Azure Cosmos DB

Az Azure Cosmos DB-fiókban tárolt adatait IP-tűzfalak használatával is biztonságossá teheti. A Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. A következő módszerek egyikével állíthatja be az IP-tűzfalat a Azure Cosmos DB fiókban:

* a Azure Portal
* Deklaratív Azure Resource Manager sablon használatával
* Programozott módon az Azure CLI-n vagy Azure PowerShellon keresztül, a **ipRangeFilter** tulajdonság frissítésével

## <a id="configure-ip-policy"></a>IP-tűzfal konfigurálása a Azure Portal használatával

A Azure Portal IP-hozzáférés-vezérlési szabályzatának beállításához nyissa meg a Azure Cosmos DB fiók lapot, és válassza a **tűzfal és virtuális hálózatok** lehetőséget a navigációs menüben. Módosítsa az értékről a **kiválasztott hálózatokra** **való hozzáférés engedélyezése lehetőséget** , majd válassza a **Mentés**lehetőséget.

![Képernyőfelvétel: a tűzfal oldalának megnyitása a Azure Portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Ha az IP-hozzáférés-vezérlés be van kapcsolva, az Azure Portal lehetővé teszi az IP-címek, IP-címtartományok és kapcsolók megadását. A kapcsolók lehetővé teszik a hozzáférést más Azure-szolgáltatásokhoz és a Azure Portalhoz. A kapcsolókról a következő fejezetekben olvashat bővebben.

> [!NOTE]
> Miután engedélyezte a Azure Cosmos DB-fiókjához tartozó IP-hozzáférés-vezérlési házirendet, a rendszer elutasítja a Azure Cosmos DB fiókjának az IP-címtartományok engedélyezett listáját nem tartalmazó összes kérését. A hozzáférés-vezérlés integritásának biztosítása érdekében a portálon található Azure Cosmos DB-erőforrások böngészése is le van tiltva.

### <a name="allow-requests-from-the-azure-portal"></a>Kérések engedélyezése a Azure Portal

Ha programozott módon engedélyezi az IP-hozzáférés-vezérlési házirendet, a hozzáférés fenntartásához hozzá kell adnia a Azure Portal IP-címét a **ipRangeFilter** tulajdonsághoz. A portál IP-címei a következők:

|Régió|IP-cím|
|------|----------|
|Németország|51.4.229.218|
|Kína|139.217.8.252|
|US Gov|52.244.48.71|
|Minden más régió|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

A Azure Portal elérését engedélyezheti a hozzáférés **engedélyezése Azure Portal** lehetőség kiválasztásával, ahogy az alábbi képernyőfelvételen is látható:

![A Azure Portal-hozzáférés engedélyezését bemutató képernyőkép](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>A globális Azure-adatközpontok vagy más források kéréseinek engedélyezése az Azure-ban

Ha a Azure Cosmos DB fiókját olyan szolgáltatásokból éri el, amelyek nem biztosítanak statikus IP-címet (például Azure Stream Analytics és Azure Functions), akkor továbbra is használhatja az IP-tűzfalat a hozzáférés korlátozására. Az ilyen szolgáltatások Azure Cosmos DB fiók elérésének engedélyezéséhez adja hozzá a 0.0.0.0 IP-címet az engedélyezett IP-címek listájához. A 0.0.0.0-cím korlátozza a kéréseket az Azure Datacenter IP-címtartomány Azure Cosmos DB-fiókjára. Ez a beállítás nem teszi lehetővé más IP-címtartományok elérését Azure Cosmos DB-fiókjához.

> [!NOTE]
> Ez a beállítás úgy konfigurálja a tűzfalat, hogy engedélyezze az Azure-ból érkező összes kérést, beleértve az Azure-ban üzembe helyezett más ügyfelek előfizetésének kéréseit is. A beállítás által megengedett IP-címek listája széles körű, ezért korlátozza a tűzfalszabályok hatékonyságát. Ezt a lehetőséget csak akkor használja, ha a kérések nem a virtuális hálózatok statikus IP-címeiről vagy alhálózatáról származnak. Ha ezt a beállítást választja, a rendszer automatikusan engedélyezi a hozzáférést a Azure Portal, mert az Azure Portal üzembe helyezése az Azure-ban történik.

Az alábbi képernyőképen látható módon engedélyezheti a hozzáférést a Azure Portalhoz: válassza a **kapcsolatok elfogadása az Azure-adatközpontok között** lehetőséget.

![Képernyőfelvétel: a tűzfal oldalának megnyitása a Azure Portal](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Az aktuális IP-címről érkező kérések

A fejlesztés egyszerűsítése érdekében a Azure Portal segíti az ügyfélszámítógép IP-címének azonosítását és hozzáadását az engedélyezett listához. A gépet futtató alkalmazások hozzáférhetnek a Azure Cosmos DB-fiókjához.

A portál automatikusan észleli az ügyfél IP-címét. Lehet, hogy a gép ügyfél IP-címe vagy a hálózati átjáró IP-címe. Mielőtt éles környezetben üzembe helyezi a számítási feladatokat, ügyeljen rá, hogy távolítsa el ezt az IP-címet.

Ha a jelenlegi IP-címet az IP-címek listájához szeretné felvenni, válassza a **jelenlegi IP-cím hozzáadása**lehetőséget. Ezután válassza a **Save** (Mentés) lehetőséget.

![Képernyőfelvétel: a tűzfalbeállítások konfigurálása az aktuális IP-címhez](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>A Cloud servicestől érkező kérések

Az Azure-ban a Cloud Services gyakori módszer a közepes szintű szolgáltatás-logika Azure Cosmos DB használatával történő üzemeltetésére. Ha engedélyezni szeretné a Azure Cosmos DB fiókjához való hozzáférést egy felhőalapú szolgáltatásból, hozzá kell adnia a felhőalapú szolgáltatás nyilvános IP-címét a Azure Cosmos DB fiókjához társított IP-címek engedélyezett listájához az [IP-hozzáférés-vezérlési házirend konfigurálásával](#configure-ip-policy). Ez biztosítja, hogy a Cloud Services összes szerepkör-példánya hozzáférhessen a Azure Cosmos DB-fiókjához.

A felhőalapú szolgáltatások IP-címeit a Azure Portal az alábbi képernyőképen látható módon kérheti le:

![Képernyőfelvétel: a Azure Portalban megjelenített felhőalapú szolgáltatás nyilvános IP-címének megjelenítése](./media/how-to-configure-firewall/public-ip-addresses.png)

Ha szerepkör-példányok hozzáadásával bővíti a felhőalapú szolgáltatást, akkor ezek az új példányok automatikusan hozzáférhetnek a Azure Cosmos DB fiókhoz, mert ugyanahhoz a felhőalapú szolgáltatáshoz tartoznak.

### <a name="requests-from-virtual-machines"></a>Virtuális gépek kérései

A [virtuális gépeket](https://azure.microsoft.com/services/virtual-machines/) vagy virtuálisgép- [méretezési csoportokat](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) is használhat a közepes szintű szolgáltatások üzemeltetéséhez Azure Cosmos db használatával. Ha úgy szeretné konfigurálni a Cosmos DB fiókot, hogy az lehetővé tegye a virtuális gépekről való hozzáférést, konfigurálja a virtuális gép és/vagy virtuálisgép-méretezési csoport nyilvános IP-címét úgy, hogy az a Azure Cosmos DB-fiók számára engedélyezett IP-címet adja meg a következő [konfigurálásával: az IP-hozzáférés-vezérlési házirend](#configure-ip-policy).

A Azure Portal lévő virtuális gépek IP-címeit az alábbi képernyőképen látható módon kérheti le:

![A Azure Portalban megjelenített virtuális gép nyilvános IP-címét bemutató képernyőfelvétel](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Amikor virtuálisgép-példányokat vesz fel a csoportba, a automatikusan hozzáférést kap a Azure Cosmos DB fiókjához.

### <a name="requests-from-the-internet"></a>Az internetről érkező kérések

Amikor az interneten található számítógépről fér hozzá a Azure Cosmos DB-fiókjához, a számítógép ügyfél IP-címét vagy IP-címét hozzá kell adni a fiókja IP-címeinek engedélyezett listájához.

## <a id="configure-ip-firewall-arm"></a>IP-tűzfal konfigurálása Resource Manager-sablon használatával

A Azure Cosmos DB-fiók hozzáférés-vezérlésének konfigurálásához győződjön meg arról, hogy a Resource Manager-sablon megadja a **ipRangeFilter** attribútumot az engedélyezett IP-címtartományok listájával. Ha az IP-tűzfalat már telepített Cosmos-fiókra konfigurálja, győződjön meg arról, hogy a `locations` tömb megfelel a jelenleg telepített rendszernek. A `locations` tömb és egyéb tulajdonságok nem módosíthatók egyszerre. További információ és példák a Azure Resource Manager-sablonokra Azure Cosmos DB megtekintéséhez: [Azure Resource Manager sablonok Azure Cosmos db](resource-manager-samples.md)

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

## <a id="configure-ip-firewall-cli"></a>IP-hozzáférés-vezérlési szabályzat konfigurálása az Azure CLI használatával

Az alábbi parancs bemutatja, hogyan hozhat létre egy olyan Azure Cosmos DB fiókot, amely IP-hozzáférés-vezérléssel rendelkezik:

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

## <a id="configure-ip-firewall-ps"></a>IP-hozzáférés-vezérlési házirend konfigurálása a PowerShell használatával

Az alábbi szkript bemutatja, hogyan hozhat létre egy Azure Cosmos DB-fiókot IP-hozzáférés-vezérléssel:

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

## <a id="troubleshoot-ip-firewall"></a>IP-hozzáférés-vezérlési házirenddel kapcsolatos problémák elhárítása

Az IP-hozzáférés-vezérlési házirenddel kapcsolatos problémákat a következő beállításokkal lehet elhárítani:

### <a name="azure-portal"></a>Azure Portal

Ha engedélyezi a Azure Cosmos DB-fiókjához tartozó IP-hozzáférés-vezérlési házirendet, a fiókra irányuló összes kérést letiltja az IP-címtartományok engedélyezési listáján kívüli gépekről. A portál adatsík műveleteinek, például a tárolók és a dokumentumok lekérdezésének engedélyezéséhez explicit módon engedélyeznie kell Azure Portal hozzáférését a portál **tűzfal** paneljének használatával.

### <a name="sdks"></a>SDK-k

Ha olyan gépekről próbál meg hozzáférni Azure Cosmos DB erőforrásokhoz, amelyek nem szerepelnek az engedélyezési listán, a rendszer általános **403 Tiltott** választ ad vissza további részletek nélkül. Ellenőrizze az engedélyezett IP-listát a fiókjához, és győződjön meg arról, hogy a megfelelő házirend-konfigurációt alkalmazza a rendszer a Azure Cosmos DB-fiókjára.

### <a name="source-ips-in-blocked-requests"></a>A letiltott kérelmekben szereplő forrás IP-címek

Engedélyezze a diagnosztikai naplózást a Azure Cosmos DB-fiókjában. Ezek a naplók minden kérelmet és választ jelenítenek meg. A tűzfalral kapcsolatos üzenetek naplózása 403 visszatérési kóddal történik. Az üzenetek szűrésével megtekintheti a letiltott kérelmek forrás IP-címeit. Lásd: [Azure Cosmos db diagnosztikai naplózás](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Az alhálózaton lévő, Azure Cosmos DB engedélyezett szolgáltatás-végponttal rendelkező alhálózat kérései

A virtuális hálózat azon alhálózatai által küldött kérelmek, amelyek szolgáltatás-végpontja Azure Cosmos DB engedélyezve, a virtuális hálózatot és az alhálózati identitást Azure Cosmos DB fiókoknak küldi el. Ezek a kérések nem rendelkeznek a forrás nyilvános IP-címével, ezért az IP-szűrők elutasítja őket. Ha engedélyezni szeretné a hozzáférést a virtuális hálózatokban lévő adott alhálózatokról, vegyen fel egy hozzáférés-vezérlési listát a [virtuális hálózat és az alhálózat-alapú hozzáférés konfigurálása a Azure Cosmos db-fiókhoz című témakörben](how-to-configure-vnet-service-endpoint.md)leírtak szerint. A tűzfalszabályok alkalmazása akár 15 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések

A Azure Cosmos DB-fiókhoz tartozó virtuális hálózati szolgáltatás végpontjának konfigurálásához tekintse meg a következő cikkeket:

* [Virtuális hálózati és alhálózat-hozzáférés-vezérlés a Azure Cosmos DB-fiókhoz](vnet-service-endpoint.md)
* [A virtuális hálózat és az alhálózat-alapú hozzáférés konfigurálása a Azure Cosmos DB-fiókhoz](how-to-configure-vnet-service-endpoint.md)

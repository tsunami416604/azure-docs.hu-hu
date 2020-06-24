---
title: Azure Private Endpoint DNS-konfiguráció
description: Az Azure Private Endpoint DNS konfigurációjának megismerése
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: d6417b3632e1aad0b942844a1470772e8f0197e2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081094"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure Private Endpoint DNS-konfiguráció


Ha a kapcsolati karakterlánc részeként egy teljes tartománynevet (FQDN) használó privát kapcsolati erőforráshoz csatlakozik, fontos, hogy a DNS-beállításokat helyesen konfigurálja a lefoglalt magánhálózati IP-címhez való feloldáshoz. Előfordulhat, hogy a meglévő Microsoft Azure szolgáltatások már rendelkeznek DNS-konfigurációval, amikor nyilvános végponton keresztül csatlakoznak. Ezt a konfigurációt felül kell bírálni a privát végpont használatával történő kapcsolódáshoz. 
 
A magánhálózati végponthoz társított hálózati adapter tartalmazza a DNS konfigurálásához szükséges összes információt, beleértve az FQDN-t és egy adott privát kapcsolati erőforráshoz lefoglalt magánhálózati IP-címeket. 
 
A következő beállításokkal konfigurálhatja a magánhálózati végpontok DNS-beállításait: 
- **A gazda fájl használata (csak teszteléshez ajánlott)**. A virtuális gépen lévő gazdagépen a DNS felülbírálására is használható.  
- **Használjon privát DNS-zónát**. [Saját DNS-zónák](../dns/private-dns-privatednszone.md) használatával felülbírálhatja egy adott privát végpont DNS-feloldását. A saját DNS-zónák összekapcsolhatók a virtuális hálózattal bizonyos tartományok feloldásához.
- **Használja a DNS-továbbítót (nem kötelező)**. A DNS-továbbító használatával felülbírálhatja egy adott privát kapcsolati erőforrás DNS-feloldását. Ha a [DNS-kiszolgáló](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) egy virtuális hálózaton található, létrehozhat egy DNS-továbbítási szabályt, amely egy magánhálózati DNS-zónát használ, hogy leegyszerűsítse az összes privát kapcsolati erőforrás konfigurációját.
 
> [!IMPORTANT]
> Nem ajánlott felülbírálni egy olyan zónát, amely aktívan használatban van a nyilvános végpontok feloldásához. Az erőforrásokhoz való csatlakozások nem lesznek képesek megfelelően feloldani a nyilvános DNS-re való DNS-továbbítás nélkül. A problémák elkerülése érdekében hozzon létre egy másik tartománynevet, vagy kövesse a javasolt nevet az alábbi szolgáltatásokhoz. 

## <a name="azure-services-dns-zone-configuration"></a>Azure-szolgáltatások DNS-zóna konfigurációja
Az Azure-szolgáltatások egy kanonikus DNS-rekordot (CNAME) hoznak létre a nyilvános DNS-szolgáltatásban, hogy átirányítsák a felbontást a javasolt privát tartomány nevére. Felülbírálhatja a felbontást a privát végpontok magánhálózati IP-címével. 
 
Az alkalmazásoknak nem kell módosítaniuk a kapcsolódási URL-címet. Ha nyilvános DNS-szolgáltatás használatával próbál feloldani, a DNS-kiszolgáló most feloldja a magánhálózati végpontokat. A folyamat nem érinti a meglévő alkalmazásokat. 

Az Azure-szolgáltatások esetében használja az ajánlott zónák nevét az alábbi táblázatban leírtak szerint:

| Privát kapcsolat erőforrástípus/alerőforrása |saját DNS zóna neve | Nyilvános DNS-zónák továbbítói |
|---|---|---|---|
| Azure Automation/(Microsoft. Automation/automationAccounts)/webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (Microsoft. SQL/Servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure szinapszis Analytics (Microsoft. SQL/Servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/tábla (tábla, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/üzenetsor (Üzenetsor, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/fájl (fájl, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake fájlrendszer Gen2 (Microsoft. Storage/storageAccounts)/Data Lake fájlrendszer Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/tábla | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL – egyetlen kiszolgáló (Microsoft. DBforPostgreSQL/Servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft. DBforMySQL/Servers)/portra beállított mysqlserver | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/Servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft. kulcstartó/tárolók)/tároló | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service – Kubernetes API (Microsoft. Tárolószolgáltatás/managedClusters)/felügyelet | privatelink. {Region}. azmk8s. IO | {Region}. azmk8s. IO |
| Azure Search (Microsoft. Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft. ContainerRegistry/nyilvántartók)/beállításjegyzék | privatelink.azurecr.io | azurecr.io |
| Azure-alkalmazás konfigurációja (Microsoft. AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft. Recoveryservices szolgáltatónál/Vaults)/tároló | privatelink. {Region}. backup. windowsazure. com | {Region}. backup. windowsazure. com |
| Azure Event Hubs (Microsoft. EventHub/névterek)/névtér | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft. ServiceBus/névterek)/névtér | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net | azure-devices.net |
| Azure Relay (Microsoft. Relay/névterek)/névtér | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft. EventGrid/témák)/témakör | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft. EventGrid/Domains)/tartomány | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft. Web/Sites)/helyek | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/munkaterületek)/munkaterület | privatelink.api.azureml.ms | api.azureml.ms |
| IoT Hub (Microsoft. Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| Jelző (Microsoft. SignalRService/szignáló)/jelző | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft. bepillantások/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com | monitor.azure.com |
| Cognitive Services (Microsoft. CognitiveServices/accounts)/fiók | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |

 
## <a name="dns-configuration-scenarios"></a>DNS-konfigurációs forgatókönyvek

A szolgáltatások teljes tartományneve automatikusan feloldja a nyilvános IP-címet. A magánhálózati végpont magánhálózati IP-címére való feloldáshoz ennek megfelelően módosítania kell a DNS-konfigurációt.

A DNS egy kritikus összetevő, amellyel az alkalmazás megfelelően működik a magánhálózati végpont IP-címének feloldásával.

A beállítások alapján a következő forgatókönyvek érhetők el az integrált DNS-feloldással:

- [Virtuális hálózati munkaterhelések egyéni DNS-kiszolgáló nélkül](#virtual-network-workloads-without-custom-dns-server)
- [DNS-továbbítót használó helyszíni számítási feladatok](#on-premises-workloads-using-a-dns-forwarder)
- [Virtuális hálózat és helyszíni számítási feladatok DNS-továbbító használatával](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Virtuális hálózati munkaterhelések egyéni DNS-kiszolgáló nélkül

Ez a konfiguráció az egyéni DNS-kiszolgáló nélküli virtuális hálózati munkaterhelésekhez megfelelő. Ebben az esetben az ügyfél az Azure által biztosított DNS-szolgáltatás [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)kérdezi le a MAGÁNHÁLÓZATI végpont IP-címét. Azure DNS a magánhálózati DNS-zónák DNS-feloldására lesz felelős.

> [!NOTE]
> Ez a forgatókönyv a Azure SQL Database javasolt magánhálózati DNS-zónát használja. Más szolgáltatások esetében a modellt a következő hivatkozással módosíthatja: [Azure-szolgáltatások DNS-zóna konfigurációja](#azure-services-dns-zone-configuration).

A megfelelő konfigurálásához a következő erőforrásokra van szükség:

- Ügyfél virtuális hálózata

- Saját DNS zóna [Privatelink.database.Windows.net](../dns/private-dns-privatednszone.md) [egy rekord beírásával](../dns/dns-zones-records.md#record-types)

- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

Az alábbi képernyőfelvételen a virtuális hálózati munkaterhelések DNS-feloldási folyamata látható a magánhálózati DNS-zónával:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Egyetlen virtuális hálózat és az Azure által biztosított DNS":::

Ez a modell bővíthető több, ugyanahhoz a privát végponthoz társított virtuális hálózatra. Ezt úgy teheti meg, hogy [új virtuális hálózati hivatkozásokat ad hozzá](../dns/private-dns-virtual-network-links.md) a magánhálózati DNS-zónához az összes társ virtuális hálózat számára.

> [!IMPORTANT]
> Ehhez a konfigurációhoz egyetlen privát DNS-zónára van szükség. A különböző virtuális hálózatok azonos nevű zónáinak létrehozásához kézi műveletekre van szükség a DNS-rekordok egyesítéséhez.

> [!IMPORTANT]
> Ha egy másik előfizetésből származó, sugaras modellben lévő magánhálózati végpontot használ, akkor ugyanazt a saját DNS-zónát használja a központban.

Ebben a forgatókönyvben egy központilag [küllős](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) hálózati topológia van, és a küllős hálózatok közös privát végpontot használnak, és az összes küllős virtuális hálózat ugyanahhoz a magánhálózati DNS-zónához van csatolva. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub és küllő az Azure által biztosított DNS-sel":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>DNS-továbbítót használó helyszíni számítási feladatok

Ha a helyszíni számítási feladatokhoz egy privát végpont teljes tartománynevét szeretné feloldani a magánhálózati IP-címhez, DNS-továbbítót kell használnia az Azure szolgáltatás [nyilvános DNS-zónájának](#azure-services-dns-zone-configuration) feloldásához az Azure-ban.

Az alábbi forgatókönyv olyan helyszíni hálózat számára megfelelő, amely az Azure-ban DNS-továbbítót tartalmaz, amely az összes DNS-lekérdezés feloldását végzi a kiszolgálói szintű továbbítóval az Azure által biztosított DNS- [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). 

> [!NOTE]
> Ez a forgatókönyv a Azure SQL Database javasolt magánhálózati DNS-zónát használja.Más szolgáltatások esetében a modellt a következő hivatkozással módosíthatja: [Azure-szolgáltatások DNS-zóna konfigurációja](#azure-services-dns-zone-configuration).

A megfelelő konfigurálásához a következő erőforrásokra van szükség:

- Helyszíni hálózat
- A helyszíni környezethez [csatlakoztatott](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) virtuális hálózat
- DNS-továbbító üzembe helyezése az Azure-ban 
- Saját DNS zónák [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)    [type A record](../dns/dns-zones-records.md#record-types)
- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

Az alábbi ábrán egy olyan helyszíni hálózat DNS-feloldási sorozata látható, amely az Azure-ban üzembe helyezett DNS-továbbítót használja, ahol a felbontást egy [virtuális hálózathoz kapcsolódó](../dns/private-dns-virtual-network-links.md)privát DNS-zóna végzi el:

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Helyszíni Azure DNS használatával":::

Ez a konfiguráció bővíthető olyan helyszíni hálózatra, amely már rendelkezik DNS-megoldással. 
A helyszíni DNS-megoldást úgy kell konfigurálni, hogy az Azure-ban üzembe helyezett DNS-továbbítóra hivatkozó [feltételes továbbító](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) használatával továbbítsa a DNS-forgalmat Azure DNS.

> [!NOTE]
> Ez a forgatókönyv a Azure SQL Database javasolt magánhálózati DNS-zónát használja. Más szolgáltatások esetében a modellt a következő hivatkozással módosíthatja: [Azure-szolgáltatások DNS-zóna konfigurációja](#azure-services-dns-zone-configuration)

A megfelelő konfigurálásához a következő erőforrásokra van szükség:

- Helyi hálózat egyéni DNS-megoldással 
- A helyszíni környezethez [csatlakoztatott](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) virtuális hálózat
- DNS-továbbító üzembe helyezése az Azure-ban
- Saját DNS zónák [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)     [type A record](../dns/dns-zones-records.md#record-types)
- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

A következő ábra a DNS-feloldási sorozatot mutatja be egy helyszíni hálózatról, amely feltételesen továbbítja a DNS-forgalmat az Azure-ba, ahol a megoldás egy [virtuális hálózathoz kapcsolódó](../dns/private-dns-virtual-network-links.md)magánhálózati DNS-zónából áll.

> [!IMPORTANT]
> A feltételes továbbítást az ajánlott [nyilvános DNS-zónák továbbítójának](#azure-services-dns-zone-configuration)kell végrehajtania.Például:  `database.windows.net`   a **privatelink**. database.Windows.net helyett.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Helyszíni továbbítás Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Virtuális hálózat és helyszíni számítási feladatok DNS-továbbító használatával

A virtuális és a helyszíni hálózatokból származó privát végpontokhoz hozzáférést igénylő munkaterhelésekhez megfelelő általános megközelítéshez megosztott DNS-továbbítót kell használnia az Azure-ban üzembe helyezett Azure [-szolgáltatás nyilvános DNS-zónájának](#azure-services-dns-zone-configuration) feloldásához.

A következő forgatókönyv olyan helyszíni hálózat számára megfelelő, amely az Azure-ban DNS-továbbítót tartalmaz, valamint olyan virtuális hálózatokat, amelyeknek hozzáférést kell biztosítani a megosztott hub-hálózatban található privát végponthoz.  

A DNS-továbbító feladata az összes DNS-lekérdezés feloldása egy kiszolgálói szintű továbbítón keresztül az Azure által biztosított DNS-szolgáltatás [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Ehhez a konfigurációhoz egyetlen privát DNS-zónára van szükség. A helyszíni és a [társ virtuális hálózatokból](../virtual-network/virtual-network-peering-overview.md) végrehajtott összes ügyfélkapcsolatnak ugyanazt a magánhálózati DNS-zónát kell használnia.

> [!NOTE]
> Ez a forgatókönyv a Azure SQL Database javasolt magánhálózati DNS-zónát használja. Más szolgáltatások esetében a modellt a következő hivatkozással módosíthatja: [Azure-szolgáltatások DNS-zóna konfigurációja](#azure-services-dns-zone-configuration).

A megfelelő konfigurálásához a következő erőforrásokra van szükség:

- Helyszíni hálózat
- A helyszíni környezethez [csatlakoztatott](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) virtuális hálózat
- [Egyenrangú virtuális hálózat](../virtual-network/virtual-network-peering-overview.md) 
- DNS-továbbító üzembe helyezése az Azure-ban
- Saját DNS zónák [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)     [type A record](../dns/dns-zones-records.md#record-types)
- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

A következő ábra a DNS-feloldási sorozatot mutatja be egy olyan helyszíni és virtuális hálózatról, amely az Azure-ban üzembe helyezett DNS-továbbítót használja, ahol a felbontást egy [virtuális hálózathoz kapcsolódó](../dns/private-dns-virtual-network-links.md)privát DNS-zóna végzi el:

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Hibrid forgatókönyv":::

## <a name="next-steps"></a>További lépések
- [További tudnivalók a privát végpontokról](private-endpoint-overview.md)

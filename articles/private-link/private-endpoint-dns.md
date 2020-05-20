---
title: Azure Private Endpoint DNS-konfiguráció
description: Az Azure Private Endpoint DNS konfigurációjának megismerése
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: af7d47c98e4716df3a6cbd222c7d3c8def48e5fc
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701640"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure Private Endpoint DNS-konfiguráció


Ha a kapcsolati karakterlánc részeként egy teljes tartománynevet (FQDN) használó privát kapcsolati erőforráshoz csatlakozik, fontos, hogy a DNS-beállításokat helyesen konfigurálja a lefoglalt magánhálózati IP-címhez való feloldáshoz. Előfordulhat, hogy a meglévő Azure-szolgáltatások már rendelkeznek DNS-konfigurációval, ha nyilvános végponton keresztül csatlakoznak. Ezt a konfigurációt felül kell bírálni a privát végpont használatával történő kapcsolódáshoz. 
 
A magánhálózati végponthoz társított hálózati adapter tartalmazza a DNS konfigurálásához szükséges összes információt, beleértve a teljes TARTOMÁNYNEVEt és az adott privát kapcsolati erőforrás számára lefoglalt magánhálózati IP-címeket is. 
 
A következő beállításokkal konfigurálhatja a magánhálózati végpontok DNS-beállításait: 
- **A gazda fájl használata (csak teszteléshez ajánlott)**. A virtuális gépen lévő gazdagépen a DNS felülbírálására is használható.  
- **Használjon privát DNS-zónát**. A [privát DNS-zónák](../dns/private-dns-privatednszone.md) használatával felülbírálhatja egy adott privát végpont DNS-feloldását. A saját DNS-zónák összekapcsolhatók a virtuális hálózattal bizonyos tartományok feloldásához.
- **Használja a DNS-továbbítót (nem kötelező)**. A DNS-továbbító használatával felülbírálhatja egy adott privát kapcsolati erőforrás DNS-feloldását. Ha a [DNS-kiszolgáló](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) egy virtuális hálózaton található, létrehozhat egy DNS-továbbítási szabályt, amely egy magánhálózati DNS-zónát használ, hogy leegyszerűsítse az összes privát kapcsolati erőforrás konfigurációját.
 
> [!IMPORTANT]
> Nem ajánlott felülbírálni egy olyan zónát, amely aktívan használatban van a nyilvános végpontok feloldásához. Az erőforrásokhoz való csatlakozások nem lesznek képesek megfelelően feloldani a nyilvános DNS-re való DNS-továbbítás nélkül. A problémák elkerülése érdekében hozzon létre egy másik tartománynevet, vagy kövesse a javasolt nevet az alábbi szolgáltatásokhoz. 

## <a name="azure-services-dns-zone-configuration"></a>Azure-szolgáltatások DNS-zóna konfigurációja
Az Azure-szolgáltatások egy kanonikus DNS-rekordot (CNAME) hoznak létre a nyilvános DNS-ben, hogy átirányítsák a felbontást a javasolt privát tartomány nevére. Felülbírálhatja a felbontást a privát végpontok magánhálózati IP-címével. 
 
Az alkalmazásoknak nem kell módosítaniuk a kapcsolódási URL-címet. Ha nyilvános DNS-sel próbál feloldani, a DNS-kiszolgáló most feloldja a saját végpontokat. A folyamat nem érinti a meglévő alkalmazásokat. 

Az Azure-szolgáltatások esetében használja az ajánlott zónák nevét az alábbi táblázatban leírtak szerint:

| Privát kapcsolat erőforrástípus/alerőforrása |saját DNS zóna neve | Nyilvános DNS-zónák továbbítói |
|---|---|---|---|
| SQL DB (Microsoft. SQL/Servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure szinapszis Analytics (Microsoft. SQL/Servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/tábla (tábla, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/üzenetsor (Üzenetsor, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/fájl (fájl, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Storage-fiók (Microsoft. Storage/storageAccounts)/Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake fájlrendszer Gen2 (Microsoft. Storage/storageAccounts)/Data Lake fájlrendszer Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/tábla | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL – egyetlen kiszolgáló (Microsoft. DBforPostgreSQL/Servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft. DBforMySQL/Servers)/portra beállított mysqlserver | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/Servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft. kulcstartó/tárolók)/tároló | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service – Kubernetes API (Microsoft. Tárolószolgáltatás/managedClusters)/managedCluster | privatelink. {Region}. azmk8s. IO | {Region}. azmk8s. IO |
| Azure Search (Microsoft. Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft. ContainerRegistry/nyilvántartók)/beállításjegyzék | privatelink.azurecr.io | azurecr.io |
| Azure-alkalmazás konfigurációja (Microsoft. AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft. Recoveryservices szolgáltatónál/Vaults)/tároló | privatelink. {Region}. backup. windowsazure. com | {Region}. backup. windowsazure. com |
| Azure Event hub (Microsoft. EventHub/névterek)/névtér | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft. ServiceBus/névterek)/névtér | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Relay (Microsoft. Relay/névterek)/névtér | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft. EventGrid/témák)/témakör | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft. EventGrid/Domains)/tartomány | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure WebApps (Microsoft. Web/Sites)/webhely | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/munkaterületek)/munkaterület | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>DNS-konfigurációs forgatókönyvek

A szolgáltatások teljes tartományneve automatikusan feloldásra kerül egy nyilvános IP-címhez, így a magánhálózati végpont magánhálózati IP-címére való feloldás érdekében a DNS-konfigurációt ennek megfelelően kell módosítania.

A DNS egy kritikus összetevő, amellyel az alkalmazás megfelelően működik úgy, hogy a saját végpont IP-címének megfelelő módon oldja meg a megoldást.

A beállítások alapján az alábbi forgatókönyvek érhetők el az integrált DNS-feloldáshoz:

- [Virtuális hálózati munkaterhelések egyéni DNS-kiszolgáló nélkül](#virtual-network-workloads-without-custom-dns-server)
- [DNS-továbbítót használó helyszíni számítási feladatok](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Virtuális hálózati munkaterhelések egyéni DNS-kiszolgáló nélkül

Ez a konfiguráció az egyéni DNS-kiszolgáló nélküli virtuális hálózati munkaterhelésekhez megfelelő. Ebben az esetben az ügyfél lekérdezi a magánhálózati végpont IP-címét az Azure által megadott DNS- [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS a magánhálózati DNS-zónák DNS-feloldására lesz felelős.


> [!NOTE]
> Ez a forgatókönyv az Azure SQL Database ajánlott saját DNS zónáját használja. Más szolgáltatások esetében a modellt a következő [Azure-szolgáltatások DNS-zóna konfigurációjának](#azure-services-dns-zone-configuration)használatával állíthatja be.

A megfelelő konfigurálásához a következő erőforrásokra lesz szüksége:

- Ügyfél virtuális hálózata

- Saját DNS zóna [Privatelink.database.Windows.net](../dns/private-dns-privatednszone.md) [egy rekord beírásával](../dns/dns-zones-records.md#record-types)

- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

Az alábbi ábrán a virtuális hálózati munkaterhelések DNS-alapú, a saját DNS-zónáját használó számítási feladatait ábrázolják

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Egyetlen virtuális hálózat és az Azure által biztosított DNS":::

Ez a modell bővíthető több, ugyanahhoz a privát végponthoz társított virtuális hálózatra. Ezt úgy teheti meg, hogy [új virtuális hálózati hivatkozásokat ad hozzá](../dns/private-dns-virtual-network-links.md) a magánhálózati DNS-zónához az összes társ virtuális hálózat számára.

> [!IMPORTANT]
> Ehhez a konfigurációhoz egyetlen privát DNS-zónára van szükség, így a különböző virtuális hálózatok azonos nevű zónáinak létrehozásához kézi műveletekre van szükség a DNS-rekordok egyesítéséhez.

Ebben a forgatókönyvben egy központilag [& küllős](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) hálózati topológia a közös privát végpontot megosztó küllős hálózatokkal, és az összes küllős virtuális hálózat ugyanahhoz a magánhálózati DNS-zónához van csatolva. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub és küllő az Azure által biztosított DNS-sel":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>DNS-továbbítót használó helyszíni számítási feladatok

Ahhoz, hogy a helyszíni munkaterhelések fel tudják oldani a privát végpontok teljes tartománynevét a magánhálózati IP-címekre, DNS-továbbítót kell használnia az Azure-ban üzembe helyezett Azure [-szolgáltatás nyilvános DNS-zónájának](#azure-services-dns-zone-configuration) feloldásához.


A következő forgatókönyv olyan helyszíni hálózat számára megfelelő, amely az Azure-ban DNS-továbbítót tartalmaz, amely az összes DNS-lekérdezést az Azure által biztosított DNS- [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) való kiszolgálói szintű továbbítón keresztül oldja fel. 

> [!NOTE]
> Ez a forgatókönyv az Azure SQL Database ajánlott saját DNS zónáját használja.Más szolgáltatások esetében a modellt a következő [Azure-szolgáltatások DNS-zóna konfigurációjának](#azure-services-dns-zone-configuration)használatával állíthatja be.

A megfelelő konfigurálásához a következő erőforrásokra lesz szüksége:

- Helyszíni hálózat
- A helyszíni környezethez [csatlakoztatott](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) virtuális hálózat
- DNS-továbbító üzembe helyezése az Azure-ban 
- Saját DNS zónák [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)    [type A Record](../dns/dns-zones-records.md#record-types)
- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

Az alábbi ábrán egy olyan helyszíni hálózat DNS-feloldási sorozata látható, amely az Azure-ban üzembe helyezett DNS-továbbítót használja, ahol a felbontást egy [virtuális hálózathoz kapcsolódó](../dns/private-dns-virtual-network-links.md) privát DNS-zóna végzi.

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Helyszíni Azure DNS használatával":::

Ez a konfiguráció bővíthető olyan helyszíni hálózatra, amely már rendelkezik DNS-megoldással. 
A helyszíni DNS-megoldást úgy kell konfigurálni, hogy az Azure-ban üzembe helyezett DNS-továbbítóra hivatkozó [feltételes továbbító](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) használatával továbbítsa a DNS-forgalmat a Azure DNS.

> [!NOTE]
> Ez a forgatókönyv az Azure SQL Database ajánlott saját DNS zónáját használja.Más szolgáltatások esetében a modellt a következő [Azure-szolgáltatások DNS-zóna konfigurációjának](#azure-services-dns-zone-configuration)használatával állíthatja be.

A megfelelő konfigurálásához a következő erőforrásokra lesz szüksége:

- Helyi hálózat egyéni DNS-megoldással 
- A helyszíni környezethez [csatlakoztatott](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) virtuális hálózat
- DNS-továbbító üzembe helyezése az Azure-ban
- Saját DNS zónák [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)     [type A Record](../dns/dns-zones-records.md#record-types)
- Magánhálózati végpont adatai (FQDN-rekord neve és magánhálózati IP-cím)

A következő ábra a DNS-feloldási sorozatot mutatja be egy helyszíni hálózatról, amely feltételesen továbbítja a DNS-forgalmat az Azure-ba, ahol a megoldás egy [virtuális hálózathoz kapcsolódó](../dns/private-dns-virtual-network-links.md) magánhálózati DNS-zónából áll.

> [!IMPORTANT]
> A feltételes továbbítást az ajánlott [**nyilvános DNS-zónák továbbítása**](#azure-services-dns-zone-configuration)(   pl.:) alapján kell elvégezni a  `database.windows.net`    **privatelink**. database.Windows.net helyett.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Helyszíni továbbítás Azure DNS":::


## <a name="next-steps"></a>További lépések
- [További tudnivalók a privát végpontokról](private-endpoint-overview.md)

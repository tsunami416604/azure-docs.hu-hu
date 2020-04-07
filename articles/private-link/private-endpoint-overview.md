---
title: Mi az Azure Private Endpoint?
description: További információ az Azure Private Endpointról
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: b8f55b0693b7ffdf10516cfb19b67ccc5a94dc5a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742908"
---
# <a name="what-is-azure-private-endpoint"></a>Mi az Azure Private Endpoint?

Az Azure privát végpont egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz. A privát végpont egy privát IP-címet használ a virtuális hálózatból, így a szolgáltatás hatékonyan hozza a virtuális hálózatba. A szolgáltatás lehet egy Azure-szolgáltatás, például az Azure Storage, az Azure Cosmos DB, az SQL [stb.](private-link-service-overview.md)
  
## <a name="private-endpoint-properties"></a>Privát végpont tulajdonságai 
 A privát végpont a következő tulajdonságokat határozza meg: 


|Tulajdonság  |Leírás |
|---------|---------|
|Név    |    Egyedi név az erőforráscsoporton belül.      |
|Alhálózat    |  A privát IP-címek virtuális hálózatból történő üzembe helyezéséhez és lefoglalásához használt alhálózat. Az alhálózati követelményekről a cikk Korlátozások című szakaszában olvashat.         |
|Magánhivatkozás erőforrás    |   Az erőforrás-azonosító vagy alias használatával kapcsolatba lépő magánhivatkozás-erőforrás az elérhető típusok listájából. A rendszer egyedi hálózati azonosítót hoz létre az erőforrásnak küldött összes forgalomhoz.       |
|Cél alerőforrás   |      A csatlakoztatni a csatlakozáshoz használt alerőforrás. Minden magánkapcsolati erőforrástípus nak különböző beállításai vannak a preferencia alapján kiválasztva.    |
|Kapcsolat jóváhagyási módja    |  Automatikus vagy kézi. A szerepköralapú hozzáférés-vezérlési (RBAC) engedélyek alapján a privát végpont automatikusan jóváhagyható. Ha rBAC nélküli magánkapcsolati erőforráshoz próbál csatlakozni, a manuális módszerrel engedélyezheti az erőforrás tulajdonosának a kapcsolat jóváhagyását.        |
|Üzenet kérése     |  Megadhat egy üzenetet a kért kapcsolatok manuálisan jóvá kell hagyni. Ez az üzenet egy adott kérelem azonosítására használható.        |
|A kapcsolat állapota   |   Írásvédett tulajdonság, amely megadja, hogy a privát végpont aktív-e. Csak a privát végpontok jóváhagyott állapotban lehet használni a forgalom küldéséhez. További elérhető állapotok: <br>-**Jóváhagyva:** A kapcsolat automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra.</br><br>-**Függőben**: A kapcsolat manuálisan jött létre, és a magánkapcsolati erőforrás tulajdonosának jóváhagyása vár.</br><br>-**Elutasítva:** A kapcsolatot a magánkapcsolati erőforrás tulajdonosa elutasította.</br><br>-**Kapcsolat nélkül:** A kapcsolatot a magánkapcsolati erőforrás tulajdonosa eltávolította. A privát végpont informatívsá válik, és törlésre kell törölni. </br>|

Íme néhány fontos részlet a privát végpontokról: 
- A privát végpont lehetővé teszi az ugyanazon virtuális hálózatból származó, regionális társviszonyba vett virtuális hálózatokból származó, globálisan társviszonyba vett virtuális hálózatok és a [VPN-](https://azure.microsoft.com/services/vpn-gateway/) vagy [express-útvonal](https://azure.microsoft.com/services/expressroute/) és a privát kapcsolat által működtetett szolgáltatások használatával a fogyasztók közötti kapcsolatot.
 
- Privát végpont létrehozásakor egy hálózati adapter is létrejön az erőforrás életciklusához. A kapcsolat hoz egy privát IP-címet az alhálózat, amely leképezi a Private Link szolgáltatás.
 
- A privát végpontot ugyanabban a régióban kell telepíteni, mint a virtuális hálózatot. 
 
- A magánkapcsolati erőforrás a virtuális hálózattól és a privát végponttól eltérő régióban is telepíthető.
 
- Több magánvégpont is létrehozható ugyanazzal a privát kapcsolaterőforrással. A dns-kiszolgáló közös konfigurációját használó egyetlen hálózat esetében ajánlott egyetlen magánhálózati végpont használata egy adott magánkapcsolati erőforráshoz az ismétlődő bejegyzések vagy a DNS-feloldás ütközésének elkerülése érdekében. 
 
- Több magánvégpont is létrehozható ugyanazon vagy különböző alhálózatokon ugyanazon a virtuális hálózaton belül. Az előfizetésben létrehozható privát végpontok száma korlátozott. További információt az [Azure-korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)ban talál.


 
## <a name="private-link-resource"></a>Magánkapcsolati erőforrás 
A magánkapcsolati erőforrás egy adott privát végpont célcélja. Az alábbi lista az elérhető magánkapcsolati erőforrástípusokat tartalmazza: 
 
|Magánkapcsolati erőforrás neve  |Erőforrás típusa   |Alforrások  |
|---------|---------|---------|
|**Private Link Szolgáltatás** (Saját szolgáltatás)   |  Microsoft.Network/privateLinkServices       | üres |
|**Azure SQL Database** | Microsoft.Sql/kiszolgálók    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/kiszolgálók    |  Sql Server (sqlServer)        | 
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Táblázat (táblázat, table_secondary)<BR> Várólista (várólista, queue_secondary)<BR> Fájl (fájl, file_secondary)<BR> Web (web, web_secondary)        |
|**2. generációs Azure Data Lake Storage**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Data Lake fájlrendszer Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Asztal|
|**Azure Database for PostgreSQL -Single server (Egyetlen kiszolgáló) azure-adatbázis** | Microsoft.DBforPostgreSQL/szerverek   | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/kiszolgálók    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/kiszolgálók    | mariadbServer |
|**Azure Key Vault** | Microsoft.KeyVault/vaults    | tár |
|**Azure Kubernetes szolgáltatás - Kubernetes API** | Microsoft.ContainerService/managedClusters | managedCluster (felügyelt fürt) |
|**Azure Search** | Microsoft.Search/searchService| searchService (keresés)|  
|**Azure container-beállításjegyzék** | Microsoft.ContainerRegistry/regisztika  | registry |
|**Azure App Configuration** | Microsoft.Appconfiguration/configurationStores   | configurationStore (konfigurációStore) |
|**Azure Backup** | Microsoft.RecoveryServices/vaults   | tár |
|**Azure Event Hub** | Microsoft.EventHub/névterek    | névtér |
|**Azure Service Bus** | Microsoft.ServiceBus/névterek | névtér |
|**Azure Relay** | Microsoft.Relay/névterek | névtér |
|**Azure Event Grid** | Microsoft.EventGrid/témakörök  | témakör |
|**Azure Event Grid** | Microsoft.EventGrid/tartományok | domain |
|**Azure WebApps** | Microsoft.Web/webhelyek    | Oldalon |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/munkaterületek  | munkaterület |
  
 
## <a name="network-security-of-private-endpoints"></a>A magánvégpontok hálózati biztonsága 
Ha az Azure-szolgáltatások hoz privát végpontokat használ, a forgalom egy adott magánkapcsolati erőforráshoz van biztosítva. A platform hozzáférés-vezérlést hajt végre, hogy ellenőrizze, hogy a hálózati kapcsolatok csak a megadott magánkapcsolati erőforrást érjék el. Az ugyanazon Azure-szolgáltatáson belüli további erőforrások eléréséhez további privát végpontokra van szükség. 
 
Teljesen zárolhatja a számítási feladatokat a nyilvános végpontok eléréséből, hogy csatlakozzon egy támogatott Azure-szolgáltatáshoz. Ez a vezérlő egy további hálózati biztonsági réteget biztosít az erőforrások számára azáltal, hogy egy beépített kiszivárgási védelmet biztosít, amely megakadályozza az ugyanazon Azure-szolgáltatásban üzemeltetett más erőforrásokhoz való hozzáférést. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Hozzáférés egy magánkapcsolati erőforráshoz jóváhagyási munkafolyamat használatával 
A magánkapcsolati erőforrásokhoz a következő kapcsolat-jóváhagyási módszerekkel csatlakozhat:
- **Automatikusan** jóváhagyva, ha rendelkezik az adott magánkapcsolati erőforrás sal, vagy rendelkezik engedéllyel. A szükséges engedély a magánkapcsolat erőforrás típusától függ, a következő formátumban: Microsoft. \<Szolgáltató>/<resource_type>/privateEndpointConnectionApproval/action szolgáltató
- **Kézi** kérelem, ha nem rendelkezik a szükséges engedéllyel, és hozzáférést szeretne kérni. A jóváhagyási munkafolyamat elindul. A privát végpont és a következő privátvégpont-kapcsolat „Függőben” állapotban jön létre. A privát hivatkozás erőforrásának tulajdonosa felelős a kapcsolat jóváhagyásáért. Jóváhagyása után a privát végpont engedélyezve van a forgalom normál küldésére, ahogy az a következő jóváhagyási munkafolyamat-diagramban látható.  

![munkafolyamat jóváhagyása](media/private-endpoint-overview/private-link-paas-workflow.png)
 
A magánkapcsolati erőforrás tulajdonosa a következő műveleteket hajthatja végre egy privát végponti kapcsolaton keresztül: 
- Tekintse át az összes privát végpontkapcsolatok adatait. 
- Privát végpontkapcsolat jóváhagyása. A megfelelő privát végpont engedélyezve lesz a forgalom küldése a magánkapcsolati erőforrás. 
- Privát végpontkapcsolat elutasítása. A megfelelő privát végpont frissül, hogy tükrözze az állapotot.
- Privát végpontkapcsolat törlése bármely állapotban. A megfelelő privát végpont frissül egy leválasztott állapotban, hogy tükrözze a műveletet, a privát végpont tulajdonosa csak törölni az erőforrást ezen a ponton. 
 
> [!NOTE]
> Csak egy jóváhagyott állapotú privát végpont küldhet forgalmat egy adott magánkapcsolati erőforrásnak. 

### <a name="connecting-using-alias"></a>Csatlakozás alias használatával
Az alias egy egyedi becenév, amely akkor jön létre, amikor a szolgáltatás tulajdonosa létrehozza a privát kapcsolat szolgáltatást egy szabványos terheléselosztó mögött. A szolgáltatás tulajdonosa megoszthatja ezt az aliast a fogyasztóival kapcsolat nélkül. A fogyasztók kérhetik a kapcsolatot a magánkapcsolati szolgáltatással az erőforrás URI-jával vagy az aliashasználatával. Ha alias használatával szeretne csatlakozni, manuális kapcsolat-jóváhagyási módszerrel kell létrehoznia a saját végpontot. A kézi kapcsolat jóváhagyási módszer használatával állítsa manuális kérelem paraméter igaz a magán-végpont létrehozása folyamat. Tekintse meg a [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) és [az az hálózati magán-végpont létrehozása](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) a részleteket. 

## <a name="dns-configuration"></a>DNS-konfiguráció 
Ha a kapcsolati karakterlánc részeként egy teljesen minősített tartománynév (FQDN) használatával csatlakozik egy privát kapcsolati erőforráshoz, fontos, hogy a DNS-beállításokat helyesen konfigurálja a lefoglalt privát IP-címre való feloldáshoz. Előfordulhat, hogy a meglévő Azure-szolgáltatások már rendelkeznek egy NYILVÁNOS végponton való csatlakozáshoz használható DNS-konfigurációval. Ezt felül kell bírnia a privát végponthasználatával történő csatlakozáshoz. 
 
A magánvégponthoz társított hálózati adapter tartalmazza a DNS konfigurálásához szükséges teljes információkészletet, beleértve az FQDN-t és az adott magánhálózati erőforráshoz lefoglalt privát IP-címeket. 
 
Az alábbi beállításokkal konfigurálhatja a DNS-beállításokat a saját végpontokhoz: 
- **Használja a Gazdafájlt (csak tesztelésre ajánlott).** A virtuális gépen lévő gazdafájl segítségével felülbírálhatja a DNS-t.  
- **Használjon privát DNS-zónát**. A magánDNS-zónák segítségével felülbírálhatja egy adott privát végpont DNS-feloldását. Adott tartományok feloldása érdekében a virtuális hálózathoz privát DNS-zóna is csatolható.
- **Használja az egyéni DNS-kiszolgálót**. A saját DNS-kiszolgálójával felülbírálhatja egy adott magánkapcsolati erőforrás DNS-feloldását. Ha a [DNS-kiszolgáló](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) egy virtuális hálózaton van, létrehozhat egy DNS-továbbítási szabályt, amely egy privát DNS-zóna használatával egyszerűsíti az összes magánkapcsolati erőforrás konfigurációját.
 
> [!IMPORTANT]
> Nem ajánlott felülbírálni egy olyan zónát, amely aktívan használatban van a nyilvános végpontok feloldásához. Az erőforrásokkal létesített kapcsolatok nem oldhatók fel megfelelően a DNS-továbbítás nélkül a nyilvános DNS-re. A problémák elkerülése érdekében hozzon létre egy másik tartománynevet, vagy kövesse az egyes szolgáltatások javasolt nevét. 
 
Az Azure-szolgáltatások esetében használja az ajánlott zónaneveket az alábbi táblázatban leírtak szerint:

|Magánkapcsolat erőforrástípusa   |Alerőforrás  |Zóna neve  |
|---------|---------|---------|
|SQL DB (Microsoft.Sql/kiszolgálók)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft.Sql/kiszolgálók)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|Tárfiók (Microsoft.Storage/storageAccounts)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Tárfiók (Microsoft.Storage/storageAccounts)    |    Táblázat (táblázat, table_secondary)      |   privatelink.table.core.windows.net       |
|Tárfiók (Microsoft.Storage/storageAccounts)    |    Várólista (várólista, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Tárfiók (Microsoft.Storage/storageAccounts)   |    Fájl (fájl, file_secondary)      |    privatelink.file.core.windows.net      |
|Tárfiók (Microsoft.Storage/storageAccounts)     |  Web (web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake fájlrendszer Gen2 (Microsoft.Storage/storageAccounts)  |  Data Lake fájlrendszer Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Tábla|privatelink.table.cosmos.azure.com|
|Azure Database for PostgreSQL - Egykiszolgálós (Microsoft.DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database for MySQL (Microsoft.DBforMySQL/kiszolgálók)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure-adatbázis a MariaDB-hez (Microsoft.DBforMariaDB/kiszolgálók)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft.KeyVault/vaults)|tár|privatelink.vaultcore.azure.net|
|Azure Kubernetes-szolgáltatás – Kubernetes API (Microsoft.ContainerService/managedClusters) | managedCluster (felügyelt fürt) | {guid}.privatelink. <region>azmk8s.io|
|Azure Search (Microsoft.Search/searchServices)|searchService (keresés)|privatelink.search.windows.net|   
|Azure Container Registry (Microsoft.ContainerRegistry/regisztika) | registry | privatelink.azurecr.io |
|Azure App konfigurációja (Microsoft.Appconfiguration/configurationStores)| configurationStore (konfigurációStore) | privatelink.azconfig.io|
|Azure Backup (Microsoft.RecoveryServices/vaults)| tár |privát link. {region}.backup.windowsazure.com|
|Azure Event Hub (Microsoft.EventHub/namespaces)| névtér |privatelink.servicebus.windows.net|
|Azure Service Bus (Microsoft.ServiceBus/névterek) | névtér |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft.Relay/névterek) | névtér |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft.EventGrid/topics)   | témakör | Téma. {region}.privatelink.eventgrid.azure.net|
|Azure Event Grid (Microsoft.EventGrid/tartományok) | domain | Tartomány. {region}.privatelink.eventgrid.azure.net |
|Azure WebApps (Microsoft.Web/sites)    | Oldalon | privatelink.azurewebsites.net |
|Azure Machine Learning(Microsoft.MachineLearningServices/munkaterületek)   | munkaterület | privatelink.api.azureml.ms |
 
Az Azure létrehoz egy gyűjtőnév DNS-rekordot (CNAME) a nyilvános DNS-en, hogy a felbontást a javasolt tartománynevekre irányítsa át. A feloldást felülírhatja a privát végpontok privát IP-címével. 
 
Az alkalmazásoknak nem kell módosítaniuk a kapcsolat URL-címét. Nyilvános DNS-sel történő feloldáskor a DNS-kiszolgáló feloldódik a saját végpontjaira. A folyamat nincs hatással az alkalmazásokra. 
 
## <a name="limitations"></a>Korlátozások
 
Az alábbi táblázat a magánvégfelhasználói végpontok használata során ismert korlátozásokat tartalmazza: 


|Korlátozás |Leírás |Kezelés  |
|---------|---------|---------|
|A hálózati biztonsági csoport (NSG) szabályai és a felhasználó által definiált útvonalak nem vonatkoznak a privát végpontra    |Az NSG nem támogatott a privát végpontokon. Míg a magánvégpontot tartalmazó alhálózatoknn, nsg-t társíthatnak hozzá, a szabályok nem lesznek hatékonyak a privát végpont által feldolgozott forgalomban. A [hálózati házirendek kényszerítésének le van tiltva](disable-private-endpoint-network-policy.md) ahhoz, hogy a magánvégpontokat alhálózatban telepítsék. Az NSG továbbra is érvényben van az ugyanazon az alhálózaton üzemeltetett más számítási feladatokon. Bármely ügyfélalhálózat útvonalai /32 előtagot fognak használni, az alapértelmezett útválasztási viselkedés módosítása hasonló UDR-t igényel  | A forgalmat a forrásügyfelek kimenő forgalmat nsg-szabályok használatával szabályozhatja. A /32 előtaggal rendelkező egyes útvonalak üzembe helyezése a magánvégpont-útvonalak felülbírálásához. Az NSG-folyamatnaplók és a kimenő kapcsolatok figyelési információi továbbra is támogatottak, és használhatók        |


## <a name="next-steps"></a>További lépések
- [Saját végpont létrehozása az SQL Database Server számára a Portál használatával](create-private-endpoint-portal.md)
- [Saját végpont létrehozása az SQL Database Server számára a PowerShell használatával](create-private-endpoint-powershell.md)
- [Saját végpont létrehozása az SQL Database Server számára a CLI használatával](create-private-endpoint-cli.md)
- [Privát végpont létrehozása a storage-fiókhoz a Portál használatával](create-private-endpoint-storage-portal.md)
- [Privát végpont létrehozása az Azure Cosmos-fiókhoz a Portal használatával](../cosmos-db/how-to-configure-private-endpoints.md)
- [Saját Privát kapcsolat szolgáltatás létrehozása az Azure PowerShell használatával](create-private-link-service-powershell.md)
- [Saját privát kapcsolat létrehozása az Azure-adatbázishoz a PostgreSQL számára - Egyetlen kiszolgáló a Portál használatával](../postgresql/howto-configure-privatelink-portal.md)
- [Saját privát kapcsolat létrehozása az Azure-adatbázishoz a PostgreSQL-hez - Egyetlen kiszolgáló cli használatával](../postgresql/howto-configure-privatelink-cli.md)
- [Saját privát kapcsolat létrehozása a MySQL azure-adatbázisához a Portál használatával](../mysql/howto-configure-privatelink-portal.md)
- [Saját privát kapcsolat létrehozása a MySQL Azure-adatbázisához a CLI használatával](../mysql/howto-configure-privatelink-cli.md)
- [Saját privát kapcsolat létrehozása a MariaDB Azure-adatbázisához a Portál használatával](../mariadb/howto-configure-privatelink-portal.md)
- [Saját privát kapcsolat létrehozása az Azure-adatbázishoz a MariaDB számára a CLI használatával](../mariadb/howto-configure-privatelink-cli.md)

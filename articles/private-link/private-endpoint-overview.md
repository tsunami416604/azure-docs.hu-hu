---
title: Mi az Azure Private-végpont?
description: További tudnivalók az Azure Private-végpontról
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: cd06d4cbf62078c2c7a5def4a0032ddce97d67f0
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842452"
---
# <a name="what-is-azure-private-endpoint"></a>Mi az az Azure Private Endpoint?

Az Azure privát végpont egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás olyan Azure-szolgáltatás lehet, mint például az Azure Storage, az Azure Cosmos DB, az SQL vagy a saját [privát kapcsolati szolgáltatása](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Magánhálózati végpont tulajdonságai 
 A privát végpontok a következő tulajdonságokat határozzák meg: 


|Tulajdonság  |Leírás |
|---------|---------|
|Név    |    Az erőforráscsoporthoz tartozó egyedi név.      |
|Alhálózat    |  A magánhálózati IP-címek virtuális hálózatról való üzembe helyezésére és lefoglalására szolgáló alhálózat. Az alhálózatra vonatkozó követelményekért tekintse meg a jelen cikk korlátozások című szakaszát.         |
|Magánhálózati kapcsolat erőforrása    |   A privát kapcsolati erőforrás az erőforrás-AZONOSÍTÓval vagy az aliassal való kapcsolódáshoz az elérhető típusok listájából. Az erőforrásnak elküldett összes forgalomhoz egyedi hálózati azonosító jön létre.       |
|Cél alerőforrás   |      A kapcsolódáshoz használandó alerőforrás. Minden egyes privát kapcsolati erőforrástípus különböző beállításokkal választható ki a megfelelő beállítás alapján.    |
|A kapcsolatok jóváhagyási módszere    |  Automatikus vagy manuális. A szerepköralapú hozzáférés-vezérlési (RBAC) engedélyek alapján a magánhálózati végpontok automatikusan is jóváhagyva lesznek. Ha RBAC nélküli privát kapcsolati erőforráshoz próbál csatlakozni, a manuális módszerrel engedélyezheti, hogy az erőforrás tulajdonosa jóváhagyja a kapcsolatot.        |
|Kérelem üzenete     |  Megadhat egy üzenetet, hogy a kért kapcsolatok manuálisan legyenek jóváhagyva. Ez az üzenet egy adott kérelem azonosítására szolgál.        |
|A kapcsolat állapota   |   Írásvédett tulajdonság, amely megadja, hogy a magánhálózati végpont aktív-e. A forgalom csak a jóváhagyott állapotú privát végpontok használatával küldhető el. További elérhető állapotok: <br>-**jóváhagyva**: a rendszer automatikusan vagy manuálisan jóváhagyta a kapcsolódást, és készen áll a használatra.</br><br>-**függőben**: a kapcsolat manuálisan lett létrehozva, és a privát kapcsolat erőforrás-tulajdonosa jóváhagyásra vár.</br><br>-**visszautasítva**: a kapcsolati erőforrás tulajdonosa elutasította a kapcsolatot.</br><br>-**leválasztva**: a kapcsolatot a privát kapcsolat erőforrásának tulajdonosa törölte. A privát végpont informatív lesz, és törölni kell a tisztításhoz. </br>|

Íme néhány fontos adat a privát végpontokról: 
- A privát végpont lehetővé teszi, hogy az azonos VNet, regionálisan egymással virtuális hálózatok, globálisan összekapcsolt virtuális hálózatok és helyszíni kapcsolaton keresztüli kapcsolatot létesítsen a [VPN](https://azure.microsoft.com/services/vpn-gateway/) -t vagy [expressz útvonalat](https://azure.microsoft.com/services/expressroute/) és szolgáltatásokat használó privát kapcsolaton keresztül.
 
- Privát végpont létrehozásakor a rendszer az erőforrás életciklusához is létrehoz egy hálózati adaptert. Az illesztőhöz a magánhálózati kapcsolat szolgáltatáshoz hozzárendelt privát IP-cím tartozik.
 
- A magánhálózati végpontot a virtuális hálózattal megegyező régióban kell telepíteni. 
 
- A magánhálózati kapcsolati erőforrás a virtuális hálózat és a magánhálózati végpontok egy másik régiójában is üzembe helyezhető.
 
- Több privát végpont is létrehozható ugyanazzal a privát kapcsolati erőforrással. Egy közös DNS-kiszolgáló konfigurációját használó egyetlen hálózat esetében az ajánlott eljárás egyetlen magánhálózati végpont használata egy adott privát kapcsolati erőforráshoz a duplikált bejegyzések vagy a DNS-feloldási ütközések elkerülése érdekében. 
 
- Több privát végpont is létrehozható ugyanazon a virtuális hálózaton belül ugyanazon vagy különböző alhálózatokon. Az előfizetésben létrehozható privát végpontok száma korlátozott. Részletekért lásd: az [Azure korlátai](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Magánhálózati kapcsolat erőforrása 
A privát kapcsolati erőforrás egy adott privát végpont célját célozza meg. Az alábbi lista a rendelkezésre álló privát kapcsolati erőforrástípusok listáját tartalmazza: 
 
|Privát hivatkozás erőforrásának neve  |Erőforrás típusa   |Alerőforrások  |
|---------|---------|---------|
|**Privát kapcsolati szolgáltatás** (saját szolgáltatás)   |  Microsoft. Network/privateLinkServices       | üres |
|**Azure SQL Database** | Microsoft. SQL/kiszolgálók    |  SQL Server (sqlServer)        |
|**Azure SQL Data Warehouse** | Microsoft. SQL/kiszolgálók    |  SQL Server (sqlServer)        |
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  BLOB (blob, blob_secondary)<BR> Tábla (tábla, table_secondary)<BR> Üzenetsor (Üzenetsor, queue_secondary)<BR> Fájl (fájl, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  BLOB (blob, blob_secondary)<BR> Data Lake fájlrendszer Gen2 (DFS, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft. AzureCosmosDB/databaseAccounts | SQL, MongoDB, Cassandra, Gremlin, Table|
|**Azure Database for PostgreSQL – egyetlen kiszolgáló** | Microsoft. DBforPostgreSQL/kiszolgálók   | postgresqlServer |
|**Azure Database for MySQL** | Microsoft. DBforMySQL/kiszolgálók    | Portra beállított mysqlserver |
|**Azure Database for MariaDB** | Microsoft. DBforMariaDB/kiszolgálók    | mariadbServer |
 
## <a name="network-security-of-private-endpoints"></a>Privát végpontok hálózati biztonsága 
Ha privát végpontokat használ az Azure-szolgáltatásokhoz, a forgalom egy adott privát kapcsolati erőforráshoz van védve. A platform egy hozzáférés-vezérléssel ellenőrzi, hogy a hálózati kapcsolatok csak a megadott privát kapcsolati erőforrást érik-e el. Ha további erőforrásokat szeretne elérni ugyanazon az Azure-szolgáltatáson belül, további privát végpontokra van szükség. 
 
A munkaterhelések teljes mértékben lezárhatók a nyilvános végpontokhoz való hozzáféréstől egy támogatott Azure-szolgáltatáshoz való kapcsolódáshoz. Ez a vezérlő egy további hálózati biztonsági réteget biztosít az erőforrásokhoz egy beépített kiszűrése-védelem biztosításával, amely megakadályozza az ugyanazon az Azure-szolgáltatáson üzemeltetett egyéb erőforrásokhoz való hozzáférést. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Hozzáférés egy privát kapcsolati erőforráshoz jóváhagyási munkafolyamat használatával 
A következő kapcsolat-jóváhagyási módszerekkel csatlakozhat egy privát kapcsolati erőforráshoz:
- A **rendszer automatikusan** jóváhagyja, ha a saját vagy engedéllyel rendelkezik az adott privát kapcsolati erőforráshoz. A szükséges engedély a privát kapcsolat erőforrástípus alapján, a következő formátumban: Microsoft.\<Provider >/< resource_type >/privateEndpointConnectionApproval/action
- **Manuális** kérés, ha nem rendelkezik a szükséges engedélyekkel, és hozzáférést szeretne kérni. A rendszer elindít egy jóváhagyási munkafolyamatot. A privát végpont és a következő privátvégpont-kapcsolat „Függőben” állapotban jön létre. A privát hivatkozás erőforrásának tulajdonosa felelős a kapcsolat jóváhagyásáért. A jóváhagyást követően a magánhálózati végpont engedélyezve van a forgalom normál módon történő elküldéséhez, ahogy az a következő jóváhagyási munkafolyamat-diagramon is látható.  

![munkafolyamat-jóváhagyás](media/private-endpoint-overview/private-link-paas-workflow.png)
 
A magánhálózati kapcsolat erőforrás-tulajdonosa a következő műveleteket hajthatja végre egy privát végponti kapcsolaton keresztül: 
- Tekintse át az összes privát végponti kapcsolat részleteit. 
- Privát végponti kapcsolatok jóváhagyása. A megfelelő privát végpont engedélyezve lesz a privát kapcsolati erőforrás felé irányuló forgalom elküldéséhez. 
- Privát végponti kapcsolatok elutasítása. A rendszer frissíti a megfelelő privát végpontot, hogy tükrözze az állapotot.
- Privát végponti kapcsolatok törlése bármilyen állapotban. A rendszer a megfelelő privát végpontot leválasztott állapottal frissíti, hogy tükrözze a műveletet, a privát végpont tulajdonosa csak ezen a ponton tudja törölni az erőforrást. 
 
> [!NOTE]
> Csak a jóváhagyott állapotú privát végpont küldhet forgalmat egy adott privát kapcsolati erőforrásba. 

### <a name="connecting-using-alias"></a>Csatlakozás alias használatával
Az alias egy egyedi moniker, amely akkor jön létre, amikor a szolgáltatás tulajdonosa létrehoz egy standard Load Balancer mögötti privát kapcsolati szolgáltatást. A szolgáltatás tulajdonosai megoszthatják ezt az aliast a felhasználókkal kapcsolat nélkül. A felhasználók az erőforrás URI-ja vagy az alias használatával igényelhetnek kapcsolatot a privát kapcsolati szolgáltatással. Ha az alias használatával szeretne csatlakozni, létre kell hoznia egy privát végpontot a manuális kapcsolat jóváhagyási módszerével. A manuális kapcsolódási jóváhagyási módszer használatához állítsa a manuális kérelem paraméterét True értékre a magánhálózati végpont létrehozása folyamat során. A részletekért tekintse meg a [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) és [az az Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) parancsot. 

## <a name="dns-configuration"></a>DNS-konfiguráció 
Ha a kapcsolati karakterlánc részeként egy teljes tartománynevet (FQDN) használó privát kapcsolati erőforráshoz csatlakozik, fontos, hogy a DNS-beállításokat helyesen konfigurálja a lefoglalt magánhálózati IP-címhez való feloldáshoz. Előfordulhat, hogy a meglévő Azure-szolgáltatások már rendelkeznek DNS-konfigurációval, ha nyilvános végponton keresztül csatlakoznak. Ezt felül kell bírálni a privát végpont használatával történő kapcsolódáshoz. 
 
A magánhálózati végponthoz társított hálózati adapter tartalmazza a DNS konfigurálásához szükséges összes információt, beleértve a teljes TARTOMÁNYNEVEt és az adott privát kapcsolati erőforrás számára lefoglalt magánhálózati IP-címeket is. 
 
A következő beállításokkal konfigurálhatja a magánhálózati végpontok DNS-beállításait: 
- **A gazda fájl használata (csak teszteléshez ajánlott)** . A virtuális gépen lévő gazdagépen a DNS felülbírálására is használható.  
- **Használjon privát DNS-zónát**. A privát DNS-zónák használatával felülbírálhatja egy adott privát végpont DNS-feloldását. A saját DNS-zónák összekapcsolhatók a virtuális hálózattal bizonyos tartományok feloldásához.
- **Használja az egyéni DNS-kiszolgálót**. A saját DNS-kiszolgáló használatával felülbírálhatja egy adott privát kapcsolati erőforrás DNS-feloldását. Ha a [DNS-kiszolgáló](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) egy virtuális hálózaton található, létrehozhat egy DNS-továbbítási szabályt, amely egy magánhálózati DNS-zónát használ, hogy leegyszerűsítse az összes privát kapcsolati erőforrás konfigurációját.
 
> [!IMPORTANT]
> Nem ajánlott felülbírálni egy olyan zónát, amely aktívan használatban van a nyilvános végpontok feloldásához. Az erőforrásokhoz való csatlakozások nem lesznek képesek megfelelően feloldani a nyilvános DNS-re való DNS-továbbítás nélkül. A problémák elkerülése érdekében hozzon létre egy másik tartománynevet, vagy kövesse a javasolt nevet az alábbi szolgáltatásokhoz. 
 
Az Azure-szolgáltatások esetében használja az ajánlott zónák nevét az alábbi táblázatban leírtak szerint:

|Privát kapcsolat erőforrástípus   |Alerőforrás  |Zóna neve  |
|---------|---------|---------|
|SQL DB/DW (Microsoft. SQL/Servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Storage-fiók (Microsoft. Storage/storageAccounts)    |  BLOB (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Storage-fiók (Microsoft. Storage/storageAccounts)    |    Tábla (tábla, table_secondary)      |   privatelink.table.core.windows.net       |
|Storage-fiók (Microsoft. Storage/storageAccounts)    |    Üzenetsor (Üzenetsor, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Storage-fiók (Microsoft. Storage/storageAccounts)   |    Fájl (fájl, file_secondary)      |    privatelink.file.core.windows.net      |
|Storage-fiók (Microsoft. Storage/storageAccounts)     |  Web (web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake fájlrendszer Gen2 (Microsoft. Storage/storageAccounts)  |  Data Lake fájlrendszer Gen2 (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Table|privatelink.table.cosmos.azure.com|
|Azure Database for PostgreSQL – egyetlen kiszolgáló (Microsoft. DBforPostgreSQL/Servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database for MySQL (Microsoft. DBforMySQL/Servers)|Portra beállított mysqlserver|privatelink.mysql.database.azure.com|
|Azure Database for MariaDB (Microsoft. DBforMariaDB/Servers)|mariadbServer|privatelink.mariadb.database.azure.com|
 
Az Azure egy kanonikus DNS-rekordot (CNAME) hoz létre a nyilvános DNS-ben, hogy átirányítsa a felbontást a javasolt tartomány nevére. A felbontás felülbírálható a privát végpontok magánhálózati IP-címével. 
 
Az alkalmazásoknak nem kell módosítaniuk a kapcsolódási URL-címet. Ha nyilvános DNS-sel próbál feloldani, a DNS-kiszolgáló most feloldja a saját végpontokat. A folyamat nem befolyásolja az alkalmazásait. 
 
## <a name="limitations"></a>Korlátozások
 
A következő táblázat tartalmazza a privát végpontok használatakor felismert korlátozások listáját: 


|Korlátozás |Leírás |Kezelés  |
|---------|---------|---------|
|A hálózati biztonsági csoport (NSG) szabályai és a felhasználó által megadott útvonalak nem vonatkoznak a privát végpontra    |A NSG nem támogatott a privát végpontokon. Míg a privát végpontot tartalmazó alhálózatokhoz NSG társítható, a szabályok nem lesznek érvényesek a privát végpont által feldolgozott forgalomra. A privát végpontok alhálózaton való üzembe helyezéséhez [le kell tiltani a hálózati házirendek kényszerítését](disable-private-endpoint-network-policy.md) . A NSG továbbra is érvényben van az ugyanazon alhálózaton futó egyéb munkaterheléseken. Az összes ügyfél-alhálózat útvonala egy/32 előtagot használ, és az alapértelmezett útválasztási viselkedés megváltoztatásához hasonló UDR van szükség.  | A forgalmat a forrás-ügyfeleken a kimenő forgalomra vonatkozó NSG szabályok használatával szabályozhatja. Egyéni útvonalak üzembe helyezése a/32 előtaggal a privát végponti útvonalak felülbírálásához        |
|  A privát végpontokkal rendelkező Virtual Network csak nem támogatottak   |   Ha a privát végpontokhoz való csatlakozás a Virtual Network más számítási feladatok nélkül nem támogatott.       | Egyetlen virtuális gép üzembe helyezése a Virtual Network a kapcsolat engedélyezéséhez |
|A speciális számítási feladatok nem férnek hozzá privát végpontokhoz    |   A virtuális hálózatba központilag telepített következő szolgáltatások nem férhetnek hozzá privát végpontokat használó privát kapcsolati erőforrásokhoz:<br>App Service-csomag</br>Azure Container Instance</br>Azure NetApp Files</br>Azure-beli dedikált HSM<br>       |   Az előzetes verzióban nem érhető el mérséklés.       |


## <a name="next-steps"></a>Következő lépések
- [Privát végpont létrehozása SQL Database kiszolgálóhoz a portál használatával](create-private-endpoint-portal.md)
- [Magánhálózati végpont létrehozása SQL Database kiszolgálóhoz a PowerShell használatával](create-private-endpoint-powershell.md)
- [Magánhálózati végpont létrehozása SQL Database kiszolgálóhoz a parancssori felület használatával](create-private-endpoint-cli.md)
- [Privát végpont létrehozása a Storage-fiókhoz a portál használatával](create-private-endpoint-storage-portal.md)
- [Privát végpont létrehozása az Azure Cosmos-fiókhoz a portál használatával](../cosmos-db/how-to-configure-private-endpoints.md)
- [Saját privát kapcsolati szolgáltatás létrehozása Azure PowerShell használatával](create-private-link-service-powershell.md)
- [Saját privát hivatkozás létrehozása Azure Database for PostgreSQL-egyetlen kiszolgálóhoz a portál használatával](../postgresql/howto-configure-privatelink-portal.md)
- [Saját privát hivatkozás létrehozása Azure Database for PostgreSQL-egyetlen kiszolgálóhoz a parancssori felület használatával](../postgresql/howto-configure-privatelink-cli.md)
- [Saját privát hivatkozás létrehozása Azure Database for MySQLhoz a portál használatával](../mysql/howto-configure-privatelink-portal.md)
- [Saját privát hivatkozás létrehozása Azure Database for MySQLhoz a CLI használatával](../mysql/howto-configure-privatelink-cli.md)
- [Saját privát hivatkozás létrehozása Azure Database for MariaDBhoz a portál használatával](../mariadb/howto-configure-privatelink-portal.md)
- [Saját privát hivatkozás létrehozása Azure Database for MariaDBhoz a CLI használatával](../mariadb/howto-configure-privatelink-cli.md)

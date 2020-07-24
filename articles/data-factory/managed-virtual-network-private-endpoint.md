---
title: Felügyelt virtuális hálózat & felügyelt magánhálózati végpontok
description: Ismerkedjen meg a felügyelt virtuális hálózattal és a felügyelt privát végpontokkal Azure Data Factoryban.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: f6868e3a77cbc4b262180b47ed3b387840062969
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096671"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory felügyelt Virtual Network (előzetes verzió)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk ismerteti a felügyelt Virtual Network és a felügyelt privát végpontokat a Azure Data Factoryban.


## <a name="managed-virtual-network"></a>Felügyelt virtuális hálózat

Ha Azure Data Factory felügyelt Virtual Networkon (VNET) belül hoz létre Azure Integration Runtime (IR), az integrációs modult a felügyelt Virtual Network fogja kiépíteni, és a magánhálózati végpontokat a támogatott adattárakhoz való biztonságos csatlakozásra fogja használni. 

A felügyelt Virtual Networkon belüli Azure IR létrehozása biztosítja az adatintegrációs folyamat elkülönítését és védelmét. 

A felügyelt Virtual Network használatának előnyei:

- Felügyelt Virtual Network esetén kiszervezheti a Virtual Network Azure Data Factoryre való felügyeletének terhét. Nem kell létrehoznia olyan alhálózatot Azure Integration Runtimehoz, amely végül sok magánhálózati IP-címet használhat a Virtual Network, és a hálózati infrastruktúra korábbi megtervezését igényli. 
- Az adatintegrációk biztonságos elvégzéséhez nincs szükség az Azure hálózatkezelési szolgáltatásának alapos ismeretére. Ehelyett a biztonságos ETL-vel való Ismerkedés sokkal egyszerűbbé teszi az adatmérnökök számára. 
- A felügyelt Virtual Network és a felügyelt privát végpontok is megvédik az kiszűrése. 

> [!IMPORTANT]
>A felügyelt VNet jelenleg csak a Azure Data Factory régióval azonos régióban támogatott.
 

![Az ADF felügyelt Virtual Network architektúrája](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Felügyelt privát végpontok

A felügyelt magánhálózati végpontok a Azure Data Factory felügyelt Virtual Network létrehozott privát végpontok, amelyek az Azure-erőforrásokra mutató privát hivatkozást hoznak létre. Azure Data Factory kezeli ezeket a privát végpontokat az Ön nevében. 

![Új felügyelt magánhálózati végpont](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory támogatja a privát hivatkozásokat. A privát hivatkozás lehetővé teszi az Azure-(Pásti-) szolgáltatások (például az Azure Storage, a Azure Cosmos DB, a Azure SQL Data Warehouse) elérését.

Privát kapcsolat használatakor az adattárak és a felügyelt Virtual Network közötti adatforgalom teljes egészében a Microsoft gerinc hálózatán halad át. A privát hivatkozás védi az adatkiszűrése kockázatait. Privát végpont létrehozásával létrehozhat egy erőforrásra mutató privát hivatkozást.

A magánhálózati végpont egy magánhálózati IP-címet használ a felügyelt Virtual Networkban, hogy hatékonyan hozza a szolgáltatást. A magánhálózati végpontok egy adott erőforrásra vannak leképezve az Azure-ban, és nem a teljes szolgáltatás. Az ügyfelek korlátozhatják a szervezete által jóváhagyott adott erőforráshoz való kapcsolódást. További információ a [privát kapcsolatokról és a privát végpontokról](https://docs.microsoft.com/azure/private-link/).

> [!NOTE]
> Javasoljuk, hogy felügyelt privát végpontokat hozzon létre az összes Azure-adatforráshoz való kapcsolódáshoz. 
 
> [!WARNING]
> Ha egy Pásti-adattár (blob, ADLS Gen2, SQL DW) már létre van hozva egy privát végpontot, és akkor is, ha az összes hálózatról engedélyezi a hozzáférést, az ADF csak felügyelt privát végponton keresztül férhet hozzá. Győződjön meg róla, hogy ilyen helyzetekben Hozzon létre egy privát végpontot. 

A privát végponti kapcsolatok "függő" állapotban jönnek létre, amikor felügyelt privát végpontot hoz létre Azure Data Factoryban. A rendszer elindít egy jóváhagyási munkafolyamatot. A magánhálózati kapcsolat erőforrásának tulajdonosa a kapcsolat jóváhagyása vagy elutasítása.

![Privát végpont kezelése](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Ha a tulajdonos jóváhagyja a kapcsolatot, a magánhálózati kapcsolat létrejött. Ellenkező esetben a magánhálózati kapcsolat nem lesz létrehozva. Mindkét esetben a felügyelt magánhálózati végpont a kapcsolatok állapotával lesz frissítve.

![Felügyelt privát végpont jóváhagyása](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Csak egy felügyelt magánhálózati végpont engedélyezett állapotban küldhet forgalmat egy adott privát kapcsolati erőforrásnak.

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák
### <a name="supported-data-sources"></a>Támogatott adatforrások
Az alábbi adatforrások az ADF által felügyelt Virtual Networkon keresztüli privát kapcsolaton keresztül csatlakozhatnak.
- Azure Blob-tároló
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (nem tartalmazza az Azure SQL felügyelt példányát)
- Azure SQL Data Warehouse
- Azure CosmosDB SQL
- Azure Key Vault

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Kimenő kommunikáció nyilvános végponton keresztül, felügyelt Virtual Network
- A kimenő kommunikációhoz csak az 443-es port van megnyitva.
- Az Azure Storage és a Azure Data Lake Gen2 nem támogatottak nyilvános végponton keresztüli csatlakoztatáshoz az ADF által felügyelt Virtual Network.

### <a name="other-known-issues"></a>Egyéb ismert problémák
A CosmosDB-kapcsolat hibakeresési futtatása nem működik, beleértve a adatfolyam hibakeresését és a folyamat hibakeresését is.


## <a name="next-steps"></a>További lépések

- Oktatóanyag: [másolási folyamat létrehozása felügyelt Virtual Network és privát végpontok használatával](tutorial-copy-data-portal-private.md) 
- Oktatóanyag: [leképezési adatfolyam folyamat létrehozása felügyelt Virtual Network és privát végpontok használatával](tutorial-data-flow-private.md)

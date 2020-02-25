---
title: Mi az az Azure privát kapcsolat?
description: Ismerje meg, hogyan használhatja az Azure Private-hivatkozást az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérésére a virtuális hálózat privát végpontján keresztül.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/09/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: aea424d4e74f0744f5891a0d7b3b08008fa227b5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562037"
---
# <a name="what-is-azure-private-link"></a>Mi az az Azure privát kapcsolat? 
Az Azure Private link lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage, a Azure Cosmos DB és a SQL Database) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérését a virtuális hálózat [privát végpontján](private-endpoint-overview.md) keresztül. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Létrehozhatja saját [privát kapcsolati szolgáltatását](private-link-service-overview.md) is a virtuális hálózaton (VNet), és saját maga is elvégezheti az ügyfelek számára. Az Azure Private link használatával történő telepítési és használati élmény konzisztens az Azure Pásti, az ügyfél és a megosztott partneri szolgáltatások között.

> [!IMPORTANT]
> Az Azure Private link mostantól általánosan elérhető képességgel rendelkezik. A privát végpont és a magánhálózati kapcsolat szolgáltatás (a standard Load Balancer mögötti szolgáltatás) általánosan elérhető. A különböző Azure-beli Pásti különböző időpontokban fog bejelentkezni az Azure Private-hivatkozásba. A [rendelkezésre állási](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) szakaszt alább tekintheti meg, ha a privát hivatkozáson az Azure Péter pontos állapotát mutatja. Az ismert korlátozásokért lásd: [privát végpont](private-endpoint-overview.md#limitations) és [privát kapcsolat szolgáltatás](private-link-service-overview.md#limitations). 

![Privát végpont áttekintése](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Főbb előnyök
Az Azure Private link a következő előnyöket biztosítja:  
- **Saját hozzáférésű szolgáltatások az Azure platformon**: a virtuális hálózatokat a saját Azure-ban futó szolgáltatásokhoz kapcsolhatja anélkül, hogy a forráson vagy a célhelyen nyilvános IP-címet kellene használnia. A szolgáltatók saját virtuális hálózatban tehetik saját szolgáltatásaikat, és a felhasználók a helyi virtuális hálózatban is hozzáférhetnek a szolgáltatásokhoz. A privát kapcsolati platform a fogyasztó és a szolgáltatások közötti kapcsolatot fogja kezelni az Azure-beli gerinc hálózaton. 
 
- Helyszíni és egymással **összekapcsolt hálózatok**: az Azure-ban futó, a helyszíni és a privát végpontokat használó, ExpressRoute-alapú privát és VPN-alagutakon üzemelő, illetve a virtuális hálózatokat futtató szolgáltatások eléréséhez. A szolgáltatás eléréséhez nincs szükség nyilvános vagy az Internet bejárására. Ez a funkció biztonságos módszert biztosít a számítási feladatok Azure-ba való átirányításához.
 
- Az **kiszűrése elleni védelem**: az Azure Private-hivatkozással a VNet privát végpontja az ügyfél Pásti-erőforrásának egy adott példányára van leképezve, szemben a teljes szolgáltatással. A privát végpontok használatával a felhasználók csak az adott erőforráshoz csatlakozhatnak, és nem a szolgáltatásban lévő más erőforrásokhoz. Ez az épített mechanizmus védelmet nyújt az adatkiszűrései kockázatokkal szemben. 
 
- **Globális elérhetőség**: kapcsolódjon a más régiókban futó szolgáltatásokhoz. Ez azt jelenti, hogy a fogyasztó virtuális hálózata az A régióban lehet, és a B régióban található privát kapcsolaton keresztül kapcsolódhat a szolgáltatásokhoz.  
 
- **Kiterjesztheti saját szolgáltatásait**: ugyanazokat a szolgáltatásokat és funkciókat használhatja, mint a saját szolgáltatásait az Azure-ban saját ügyfelei számára. Ha a szolgáltatást egy standard Load Balancer mögé helyezi, akkor engedélyezheti azt a privát hivatkozáshoz. A fogyasztó ezután közvetlenül kapcsolódhat a szolgáltatáshoz a saját VNet lévő privát végpont használatával. Ezeket a kapcsolatkérelmeket egyszerű jóváhagyási hívási folyamattal kezelheti. Az Azure Private link a különböző Active Directory bérlők által használt felhasználók és szolgáltatások számára is működik. 

## <a name="availability"></a>Rendelkezésre állás 
 A következő táblázat felsorolja a privát kapcsolati szolgáltatásokat, valamint azokat a régiókat, ahol elérhetők. 

|Forgatókönyv  |Támogatott szolgáltatások  |Elérhető régiók | Állapot  |
|:---------|:-------------------|:-----------------|:--------|
|Privát hivatkozás az ügyfél tulajdonában lévő szolgáltatásokhoz|standard Load Balancer mögötti privát kapcsolati szolgáltatások | Összes nyilvános régió  | FE <br/> [További információ](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Privát hivatkozás az Azure Pásti-szolgáltatásokhoz   | Azure Storage        |  Összes nyilvános régió      | Előzetes verzió <br/> [További információ](/azure/storage/common/storage-private-endpoints)  |
|  | 2\. generációs Azure Data Lake Storage        |  Összes nyilvános régió      | Előzetes verzió <br/> [További információ](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Összes nyilvános régió      |   Előzetes verzió <br/> [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
||Azure SQL Data Warehouse| Összes nyilvános régió |Előzetes verzió <br/> [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
||Azure Cosmos DB| USA nyugati középső régiója, WestUS, USA északi középső régiója |Előzetes verzió <br/> [További információ](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Azure Database for PostgreSQL – egyetlen kiszolgáló         | Összes nyilvános régió      |   Előzetes verzió <br/> [További információ](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Azure Database for MySQL         | Összes nyilvános régió      |   Előzetes verzió <br/> [További információ](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure Database for MariaDB         | Összes nyilvános régió      |   Előzetes verzió <br/> [További információ](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  Azure Key Vault         | Összes nyilvános régió      |   Előzetes verzió   <br/> [További információ](https://docs.microsoft.com/azure/key-vault/private-link-service)   |

A legfrissebb értesítésekért keresse fel az [Azure Virtual Network Updates oldalt](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Az Azure Private link integrálva van Azure Monitor, amely lehetővé teszi a naplók archiválását egy Storage-fiókba, az események továbbítását az Event hub-ba, vagy elküldheti őket Azure Monitor naplókba. Azure Monitor a következő információkat érheti el: 
- **Privát végpont**: a privát végpont által feldolgozott adatértékek (be/ki)
 
- **Magánhálózati kapcsolati szolgáltatás**:
    - A privát kapcsolati szolgáltatás által feldolgozott adatértékek (be/ki)
    - NAT-port elérhetősége  
 
## <a name="pricing"></a>Díjszabás   
A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Gyakori kérdések  
Gyakori kérdések: [Azure Private link – gyakori kérdések](private-link-faq.md).
 
## <a name="limits"></a>Korlátok  
A korlátokat lásd: [Azure Private link Limits](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>szolgáltatói szerződés
SLA esetén lásd: [SLA az Azure Private linkhez](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>További lépések
- [Privát végpont létrehozása SQL Database kiszolgálóhoz a portál használatával](create-private-endpoint-portal.md)
- [Magánhálózati végpont létrehozása SQL Database kiszolgálóhoz a PowerShell használatával](create-private-endpoint-powershell.md)
- [Magánhálózati végpont létrehozása SQL Database kiszolgálóhoz a parancssori felület használatával](create-private-endpoint-cli.md)
- [Privát végpont létrehozása a Storage-fiókhoz a portál használatával](create-private-endpoint-storage-portal.md)
- [Privát végpont létrehozása az Azure Cosmos-fiókhoz a portál használatával](../cosmos-db/how-to-configure-private-endpoints.md)
- [Saját privát kapcsolati szolgáltatás létrehozása Azure PowerShell használatával](create-private-link-service-powershell.md)


 

---
title: Mi az az Azure privát kapcsolat?
description: Az Azure Private link funkcióinak, architektúrájának és megvalósításának áttekintése. Ismerje meg, hogyan működik az Azure Private-végpontok és az Azure Private link Service, és hogyan használhatja őket.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 1bef4e5f4129ddc8300d61d609392ce0b07b74b8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80656253"
---
# <a name="what-is-azure-private-link"></a>Mi az az Azure privát kapcsolat? 
Az Azure Private link lehetővé teszi az Azure Pásti-szolgáltatások (például az Azure Storage és a SQL Database) és az Azure által üzemeltetett felhasználói/partneri szolgáltatások elérését a virtuális hálózat [privát végpontján](private-endpoint-overview.md) keresztül.

A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át. A szolgáltatás nyilvános internetre való kimutatása már nem szükséges. Létrehozhatja saját [privát kapcsolati szolgáltatását](private-link-service-overview.md) a virtuális hálózatban, és továbbíthatja az ügyfeleknek. Az Azure Private link használatával történő beállítás és felhasználás konzisztens az Azure Pásti, az ügyfél és a megosztott partneri szolgáltatások között.

> [!IMPORTANT]
> Az Azure Private link már általánosan elérhető. A privát végpont és a magánhálózati kapcsolat szolgáltatás (a standard Load Balancer mögötti szolgáltatás) általánosan elérhető. A különböző Azure-beli Pásti különböző időpontokban fog bejelentkezni az Azure Private-hivatkozásba. A [rendelkezésre állási](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) szakaszt alább tekintheti meg, ha a privát hivatkozáson az Azure Péter pontos állapotát mutatja. Az ismert korlátozásokért lásd: [privát végpont](private-endpoint-overview.md#limitations) és [privát kapcsolat szolgáltatás](private-link-service-overview.md#limitations). 

![Privát végpont áttekintése](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Főbb előnyök
Az Azure Private link a következő előnyöket biztosítja:  
- **Saját hozzáférésű szolgáltatások az Azure platformon**: virtuális hálózatának összekapcsolása az Azure-ban, nyilvános IP-cím nélkül a forráson vagy a célhelyen. A szolgáltatók saját virtuális hálózatban tehetik a szolgáltatásaikat, és a felhasználók a helyi virtuális hálózatban érhetik el a szolgáltatásokat. A privát kapcsolati platform a fogyasztó és a szolgáltatások közötti kapcsolatot fogja kezelni az Azure-beli gerinc hálózaton. 
 
- Helyszíni és egymással **összekapcsolt hálózatok**: az Azure-ban futó, privát végpontokat használó EXPRESSROUTE, VPN-alagutakra és a virtuális hálózatokra épülő, az Azure-ban futtatott hozzáférési szolgáltatások. A szolgáltatás eléréséhez nincs szükség nyilvános vagy az Internet bejárására. A privát hivatkozás biztonságos módszert biztosít a számítási feladatok Azure-ba történő átirányításához.
 
- **Védelem az adatszivárgás ellen**: a rendszer a teljes szolgáltatás helyett egy privát végpontot rendel egy Pásti erőforrás egy példányához. A felhasználók csak az adott erőforráshoz tudnak csatlakozni. A szolgáltatás bármely más erőforrásához való hozzáférés le van tiltva. Ez a mechanizmus védelmet nyújt az adatszivárgási kockázatokkal szemben. 
 
- **Globális elérhetőség**: kapcsolódjon a más régiókban futó szolgáltatásokhoz. A fogyasztó virtuális hálózata az A régióba kerülhet, és a B régióban található privát hivatkozás mögött található szolgáltatásokhoz is csatlakozhat.  
 
- **Kiterjesztheti saját szolgáltatásait**: az Azure-beli felhasználók számára is lehetővé teszi, hogy a szolgáltatás magántulajdonban legyen. Ha a szolgáltatást egy szabványos Azure Load Balancer mögött helyezi el, akkor azt privát hivatkozásként is engedélyezheti. A fogyasztó ezután közvetlenül kapcsolódhat a szolgáltatáshoz a saját virtuális hálózatában található privát végpont használatával. A kapcsolatkérelmeket a jóváhagyási hívási folyamat használatával kezelheti. Az Azure Private link a különböző Azure Active Directory bérlők számára tartozó felhasználók és szolgáltatások számára is működik. 

## <a name="availability"></a>Rendelkezésre állás 
 A következő táblázat felsorolja a privát kapcsolati szolgáltatásokat, valamint azokat a régiókat, ahol elérhetők. 

|Forgatókönyv  |Támogatott szolgáltatások  |Elérhető régiók | status  |
|:---------|:-------------------|:-----------------|:--------|
|Privát hivatkozás az ügyfél tulajdonában lévő szolgáltatásokhoz |A standard Azure Load Balancer mögötti privát kapcsolati szolgáltatások | Összes nyilvános régió  | FE <br/> [További információ](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Privát hivatkozás az Azure Pásti-szolgáltatásokhoz   | Azure Storage        |  Összes nyilvános régió      | FE <br/> [További információ](/azure/storage/common/storage-private-endpoints)  |
|  | 2. generációs Azure Data Lake Storage        |  Összes nyilvános régió      | FE <br/> [További információ](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Összes nyilvános régió      |   FE <br/> [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|  |Azure szinapszis Analytics (SQL Data Warehouse)| Összes nyilvános régió |FE <br/> [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|  |Azure Cosmos DB|  Összes nyilvános régió |FE <br/> [További információ](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Azure Database for PostgreSQL – egyetlen kiszolgáló         | Összes nyilvános régió      |   FE <br/> [További információ](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Azure Database for MySQL         | Összes nyilvános régió      |   FE <br/> [További információ](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure Database for MariaDB         | Összes nyilvános régió      |   FE <br/> [További információ](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  Azure Key Vault         | Összes nyilvános régió      |   FE   <br/> [További információ](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|  |Azure Kubernetes Service – Kubernetes API | Összes nyilvános régió      |   FE   <br/> [További információ](https://docs.microsoft.com/azure/aks/private-clusters)   |
|  |Azure Search | USA KELETI RÉGIÓJA, USA 2. NYUGATI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA |   Előzetes verzió    |
|  |Azure Container Registry | Összes nyilvános régió      |   Előzetes verzió   |
|  |Azure App Configuration | Összes nyilvános régió      |   Előzetes verzió   |
|  |Azure Backup | USA KELETI RÉGIÓJA, USA 2. NYUGATI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA     |   Előzetes verzió   |
|  |Azure Event Hub | Összes nyilvános régió      |   Előzetes verzió    |
|  |Azure Service Bus | Összes nyilvános régió      |   Előzetes verzió   |
|  |Azure Relay | Összes nyilvános régió      |   Előzetes verzió   |
|  |Azure Event Grid| USA KELETI RÉGIÓJA, USA 2. NYUGATI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA      |   Előzetes verzió   <br/> [További információ](https://docs.microsoft.com/azure/event-grid/network-security)   |
|  |Azure Web Apps | USA KELETI RÉGIÓJA, USA 2. NYUGATI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA      |   Előzetes verzió   <br/> [További információ](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|  |Azure Machine Learning | USA KELETI RÉGIÓJA, USA 2. NYUGATI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA      |   Előzetes verzió   <br/> [További információ](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |

A legfrissebb értesítésekért keresse fel az [Azure Virtual Network Updates oldalt](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Az Azure Private-hivatkozás Azure Monitorokkal való integrációt tartalmaz. Ez a kombináció lehetővé teszi a következőket:

 - Naplók archiválása egy Storage-fiókba.
 - Események továbbítása az Event hub-ba.
 - Azure Monitor naplózás.

Azure Monitor a következő információkat érheti el: 
- **Privát végpont**: 
    - A magánhálózati végpont által feldolgozott adatértékek (be/ki)
 
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

- [Rövid útmutató: privát végpont létrehozása Azure Portal használatával](create-private-endpoint-portal.md)
- [Rövid útmutató: privát link szolgáltatás létrehozása a Azure Portal használatával](create-private-link-service-portal.md)


 

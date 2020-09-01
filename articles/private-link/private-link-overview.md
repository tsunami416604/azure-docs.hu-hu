---
title: Mi az az Azure privát kapcsolat?
description: Az Azure Private link funkcióinak, architektúrájának és megvalósításának áttekintése. Ismerje meg, hogyan működik az Azure Private-végpontok és az Azure Private link Service, és hogyan használhatja őket.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 06/18/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 908d9386f08c5de863d1d435c54d65cb85feef7a
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181663"
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

|Támogatott szolgáltatások  |Elérhető régiók | Néhány fontos megjegyzés | status  |
|:-------------------|:-----------------|:----------------|:--------|
|A standard Azure Load Balancer mögötti privát kapcsolati szolgáltatások | Összes nyilvános régió<br/> Minden kormányzati régió  | standard Load Balancer támogatott | FE <br/> [További információ](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
| Azure Storage       |  Összes nyilvános régió       |  A fiók típusa általános célú v2-ben támogatott | FE <br/> [További információ](/azure/storage/common/storage-private-endpoints)  |
| 2. generációs Azure Data Lake Storage        |  Összes nyilvános régió      |  A fiók típusa általános célú v2-ben támogatott | FE <br/> [További információ](/azure/storage/common/storage-private-endpoints)  |
|  Azure SQL Database         | Összes nyilvános régió <br/> Minden kormányzati régió      |  Proxy- [kapcsolatok házirendjében](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policyhttps://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) támogatott | FE <br/> [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|Azure szinapszis Analytics (SQL Data Warehouse)| Összes nyilvános régió <br/> Minden kormányzati régió |  Proxy- [kapcsolatok házirendjében](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policyhttps://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) támogatott |FE <br/> [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|Azure Cosmos DB|  Összes nyilvános régió<br/> Minden kormányzati régió | |FE <br/> [További információ](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  Azure Database for PostgreSQL – egyetlen kiszolgáló         | Összes nyilvános régió <br/> Minden kormányzati régió     | A általános célú és a memória optimalizált díjszabási szintjein támogatott | FE <br/> [További információ](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  Azure Database for MySQL         | Összes nyilvános régió<br/> Minden kormányzati régió      |  | FE <br/> [További információ](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  Azure Database for MariaDB         | Összes nyilvános régió<br/> Minden kormányzati régió     |  | FE <br/> [További információ](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  Azure Key Vault         | Összes nyilvános régió<br/> Minden kormányzati régió      |  | FE   <br/> [További információ](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|Azure Kubernetes Service – Kubernetes API | Összes nyilvános régió      |  | FE   <br/> [További információ](https://docs.microsoft.com/azure/aks/private-clusters)   |
|Azure Search | Összes nyilvános régió <br/> Minden kormányzati régió | A szolgáltatás privát módban támogatott | FE   <br/> [További információ](https://docs.microsoft.com/azure/search/search-security-overview#endpoint-access)    |
|Azure Container Registry | Összes nyilvános régió<br/> Minden kormányzati régió    | A Container Registry prémium szintű csomagja támogatja. [Kattintson a rétegek](https://docs.microsoft.com/azure/container-registry/container-registry-skus)| FE   <br/> [További információ](https://docs.microsoft.com/azure/container-registry/container-registry-private-link)   |
|Azure App Configuration | Összes nyilvános régió      |  | Előnézet   |
|Azure Backup | Összes nyilvános régió<br/> Minden kormányzati régió   |  | FE   <br/> [További információ](https://docs.microsoft.com/azure/backup/private-endpoints)   |
|Azure Event Hub | Összes nyilvános régió<br/>Minden kormányzati régió      |   | FE   <br/> [További információ](https://docs.microsoft.com/azure/event-hubs/private-link-service)  |
|Azure Service Bus | Minden nyilvános régió<br/>Minden kormányzati régió  | A prémium szintű Azure Service Bus támogatott. [Kattintson a rétegek](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-premium-messaging) | FE   <br/> [További információ](https://docs.microsoft.com/azure/service-bus-messaging/private-link-service)    |
|Azure Relay | Összes nyilvános régió      |  | Előnézet <br/> [További információ](https://docs.microsoft.com/azure/service-bus-relay/private-link-service)  |
|Azure Event Grid| Összes nyilvános régió<br/> Minden kormányzati régió       |  | FE   <br/> [További információ](https://docs.microsoft.com/azure/event-grid/network-security) |
|Azure-webalkalmazások | Összes nyilvános régió      | PremiumV2 Windows-és Linux-és rugalmas prémium szintű funkciókkal támogatott  | Előnézet   <br/> [További információ](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|Azure Machine Learning | USA KELETI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA, USA 2. NYUGATI RÉGIÓJA      |  | Előnézet   <br/> [További információ](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |
| Azure Automation  | Összes nyilvános régió |  | Előnézet | |
| Azure IoT Hub | Összes nyilvános régió    |  | FE   <br/> [További információ](https://docs.microsoft.com/azure/iot-hub/virtual-network-support ) |
| Azure SignalR | USA KELETI RÉGIÓJA, USA 2. NYUGATI RÉGIÓJA, USA DÉLI KÖZÉPSŐ RÉGIÓJA      |  | Előnézet   <br/> [További információ](https://aka.ms/asrs/privatelink)   |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Összes nyilvános régió      |  | FE   <br/> [További információ](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)   | 
| Azure File Sync | Összes nyilvános régió      | |   FE   <br/> [További információ](https://docs.microsoft.com/azure/storage/files/storage-sync-files-networking-endpoints?tabs=azure-portal)   |
| Azure Batch | USA KELETI RÉGIÓJA, USA 2. NYUGATI RÉGIÓJA, AZ USA DÉLI KÖZÉPSŐ RÉGIÓJA, AZ USA NYUGATI KÖZÉPSŐ RÉGIÓJA, KÖZÉP-NÉMETORSZÁG, KELET-ÁZSIA, EGYESÜLT KIRÁLYSÁG DÉLI RÉGIÓJA, USA  | | FE <br/> [További információ](https://docs.microsoft.com/azure/batch/private-connectivity) |

A legfrissebb értesítésekért keresse fel az [Azure Private link frissítései lapot](https://azure.microsoft.com/updates/?product=private-link).

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


 

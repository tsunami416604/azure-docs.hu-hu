---
title: Mi az az Azure privát kapcsolat?
description: Az Azure Private Link funkcióinak, architektúrájának és megvalósításának áttekintése. Ismerje meg, hogyan működik az Azure Private Endpoints és az Azure Private Link szolgáltatás, és hogyan használhatja őket.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 1bef4e5f4129ddc8300d61d609392ce0b07b74b8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656253"
---
# <a name="what-is-azure-private-link"></a>Mi az az Azure privát kapcsolat? 
Az Azure Private Link lehetővé teszi az Azure PaaS-szolgáltatások (például az Azure Storage és az SQL Database) és az Azure által üzemeltetett ügyféltulajdonú/partnerszolgáltatások elérését a virtuális hálózat [privát végpontján](private-endpoint-overview.md) keresztül.

A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatát járja be. A szolgáltatás nyilvános internetnek való kitettsége már nem szükséges. Létrehozhatja saját [privát kapcsolati szolgáltatását](private-link-service-overview.md) a virtuális hálózatban, és eljuttathatja az ügyfelekhez. Az Azure Private Link használatával történő beállítás és felhasználás konzisztens az Azure PaaS, az ügyfél tulajdonában lévő és a megosztott partnerszolgáltatások között.

> [!IMPORTANT]
> Az Azure Private Link mostantól általánosan elérhető. Mind a Private Endpoint, mind a Private Link szolgáltatás (standard terheléselosztó mögötti szolgáltatás) általában elérhető. Különböző Azure PaaS lesz a fedélzeten az Azure Private Link különböző ütemezések szerint. Ellenőrizze az alábbi [elérhetőségi](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) szakaszt az Azure PaaS privát kapcsolaton állapotának pontos állapotáról. Ismert korlátozásokat a [Privát végpont](private-endpoint-overview.md#limitations) és a Privát kapcsolat [szolgáltatás ban.](private-link-service-overview.md#limitations) 

![Privát végpont – áttekintés](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Főbb előnyök
Az Azure Private Link a következő előnyöket nyújtja:  
- **Szolgáltatások privát elérése az Azure platformon:** Csatlakoztassa a virtuális hálózatot az Azure-beli szolgáltatásokhoz anélkül, hogy a forráson vagy a célhelyen nyilvános IP-címet biztosítana. A szolgáltatók saját virtuális hálózatukban is biztosíthatják szolgáltatásaikat, és a fogyasztók a helyi virtuális hálózatukban érhetik el ezeket a szolgáltatásokat. A Private Link platform kezeli a kapcsolatot a fogyasztó és a szolgáltatások az Azure gerinchálózaton keresztül. 
 
- **Helyszíni és társviszony-létesített hálózatok:** Az Azure-ban futó szolgáltatások a helyszíni ExpressRoute privát társviszony-létesítés, VPN-alagutak és társviszony-létesített virtuális hálózatok magánvégpontok használatával. Nincs szükség nyilvános társviszony-létesítés beállítására vagy az interneten való bejárásra a szolgáltatás eléréséhez. A Private Link biztonságos módot biztosít a számítási feladatok Azure-ba való áttelepítéséhez.
 
- **Az adatszivárgás elleni védelem:** A privát végpont egy PaaS-erőforrás egy példányához van rendelve a teljes szolgáltatás helyett. A fogyasztók csak az adott erőforráshoz csatlakozhatnak. A szolgáltatás bármely más erőforrásához való hozzáférés le van tiltva. Ez a mechanizmus védelmet nyújt az adatszivárgási kockázatokkal szemben. 
 
- **Globális elérés**: Privát módon csatlakozhat más régiókban futó szolgáltatásokhoz. A fogyasztó virtuális hálózata az A régióban lehet, és csatlakozhat a Privát kapcsolat mögötti szolgáltatásokhoz a B régióban.  
 
- **Bővítse ki a saját szolgáltatásaira**is: Ugyanazt a felhasználói élményt és funkcionalitást engedélyezheti, hogy a szolgáltatás privát módon jelenjen meg az Azure-beli fogyasztók számára. Azáltal, hogy a szolgáltatást egy szabványos Azure Load Balancer mögé helyezi, engedélyezheti azt a Private Link hez. Az fogyasztó ezután közvetlenül csatlakozhat a szolgáltatáshoz egy privát végpont használatával a saját virtuális hálózatában. A csatlakozási kérelmeket jóváhagyási hívási folyamat tal kezelheti. Az Azure Private Link különböző Azure Active Directory-bérlőkhöz tartozó fogyasztók és szolgáltatások számára működik. 

## <a name="availability"></a>Rendelkezésre állás 
 Az alábbi táblázat a Private Link szolgáltatásokat és azokat a régiókat sorolja fel, ahol elérhetők. 

|Forgatókönyv  |Támogatott szolgáltatások  |Elérhető régiók | status  |
|:---------|:-------------------|:-----------------|:--------|
|Privát hivatkozás az ügyfelek tulajdonában lévő szolgáltatásokhoz |Privát kapcsolati szolgáltatások a szabványos Azure Load Balancer mögött | Minden nyilvános régió  | FE <br/> [További információ](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Privát hivatkozás az Azure PaaS-szolgáltatásokhoz   | Azure Storage        |  Minden nyilvános régió      | FE <br/> [További információ](/azure/storage/common/storage-private-endpoints)  |
|  | 2. generációs Azure Data Lake Storage        |  Minden nyilvános régió      | FE <br/> [További információ](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Minden nyilvános régió      |   FE <br/> [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|  |Azure Synapse Analytics (SQL Adattár)| Minden nyilvános régió |FE <br/> [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|  |Azure Cosmos DB|  Minden nyilvános régió |FE <br/> [További információ](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Azure Database for PostgreSQL – Egyetlen kiszolgáló         | Minden nyilvános régió      |   FE <br/> [További információ](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Azure Database for MySQL         | Minden nyilvános régió      |   FE <br/> [További információ](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure Database for MariaDB         | Minden nyilvános régió      |   FE <br/> [További információ](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  Azure Key Vault         | Minden nyilvános régió      |   FE   <br/> [További információ](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|  |Azure Kubernetes szolgáltatás - Kubernetes API | Minden nyilvános régió      |   FE   <br/> [További információ](https://docs.microsoft.com/azure/aks/private-clusters)   |
|  |Azure Search | USA KELETI RÉGIÓJA, USA NYUGATI RÉGIÓja 2, USA DÉLI KÖZÉPSŐ RÉGIÓJA |   Előzetes verzió    |
|  |Azure Container Registry | Minden nyilvános régió      |   Előzetes verzió   |
|  |Azure App Configuration | Minden nyilvános régió      |   Előzetes verzió   |
|  |Azure Backup | USA KELETI RÉGIÓJA, USA NYUGATI RÉGIÓja 2, USA DÉLI KÖZÉPSŐ RÉGIÓJA     |   Előzetes verzió   |
|  |Azure Event Hub | Minden nyilvános régió      |   Előzetes verzió    |
|  |Azure Service Bus | Minden nyilvános régió      |   Előzetes verzió   |
|  |Azure Relay | Minden nyilvános régió      |   Előzetes verzió   |
|  |Azure Event Grid| USA KELETI RÉGIÓJA, USA NYUGATI RÉGIÓja 2, USA DÉLI KÖZÉPSŐ RÉGIÓJA      |   Előzetes verzió   <br/> [További információ](https://docs.microsoft.com/azure/event-grid/network-security)   |
|  |Azure Web Apps | USA KELETI RÉGIÓJA, USA NYUGATI RÉGIÓja 2, USA DÉLI KÖZÉPSŐ RÉGIÓJA      |   Előzetes verzió   <br/> [További információ](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|  |Azure Machine Learning | USA KELETI RÉGIÓJA, USA NYUGATI RÉGIÓja 2, USA DÉLI KÖZÉPSŐ RÉGIÓJA      |   Előzetes verzió   <br/> [További információ](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |

A legfrissebb értesítésekért tekintse meg az [Azure virtuális hálózat frissítéseit tartalmazó lapot.](https://azure.microsoft.com/updates/?product=virtual-network)

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

Az Azure Private Link integrációt folytat az Azure Monitorral. Ez a kombináció lehetővé teszi:

 - Naplók archiválása egy tárfiókba.
 - Események streamelése az Eseményközpontba.
 - Az Azure Monitor naplózása.

Az Azure Monitoron a következő információkérhetők el: 
- **Privát végpont:** 
    - A privát végpont által feldolgozott adatok (IN/OUT)
 
- **Privát link szolgáltatás:**
    - A Private Link szolgáltatás által feldolgozott adatok (IN/OUT)
    - A NAT-port elérhetősége  
 
## <a name="pricing"></a>Díjszabás   
A díjszabással kapcsolatos részletekről az [Azure Private Link díjszabása](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Gyakori kérdések  
Gyakori kérdések az [Azure Private Link gyakori kérdések .](private-link-faq.md)
 
## <a name="limits"></a>Korlátok  
A korlátokat az [Azure Private Link limitek (Azure Private Link) korlátja i.](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)

## <a name="service-level-agreement"></a>Szolgáltatásiszint-szerződés
Az SLA-t lásd: [SLA for Azure Private Link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Privát végpont létrehozása az Azure Portal használatával](create-private-endpoint-portal.md)
- [Rövid útmutató: Privát kapcsolatszolgáltatás létrehozása az Azure Portal használatával](create-private-link-service-portal.md)


 

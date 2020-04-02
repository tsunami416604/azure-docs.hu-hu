---
title: Az Azure előfizetési korlátai és kvótái
description: A gyakori Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások listáját tartalmazza. Ez a cikk a limitek növelésének és a maximális értékeknek a növelésével kapcsolatos információkat tartalmazza.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 397d6a719f64a49208e651469fab9cf841ccd4ad
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521349"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások

Ez a dokumentum felsorolja a leggyakoribb Microsoft Azure-korlátokat, amelyeket kvótáknak is neveznek.

Ha többet szeretne megtudni az Azure-díjszabásról, olvassa el [az Azure díjszabási áttekintését.](https://azure.microsoft.com/pricing/) Itt megbecsülheti a költségeket az [árképzési kalkulátor](https://azure.microsoft.com/pricing/calculator/)segítségével. Egy adott szolgáltatás, például a [Windows virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)díjszabási részleteit tartalmazó lapra is léphet. A költségek kezeléséhez a Váratlan költségek megelőzése az [Azure számlázásával és költségkezelésével című](../../billing/billing-getting-started.md)témakörben olvashat.

## <a name="managing-limits"></a>Korlátok kezelése

> [!NOTE]
> Egyes szolgáltatások állítható korlátokkal rendelkeznek.
>
> Ha egy szolgáltatás nem rendelkezik állítható korlátokkal, a következő táblázatok a **Korlát**fejlécet használják. Ezekben az esetekben az alapértelmezett és a maximális korlátok megegyeznek.
>
> Ha a korlát módosítható, a táblák tartalmazzák az **Alapértelmezett korlát** és a **Maximális korlát** fejléceket. A korlát az alapértelmezett korlát fölé emelhető, de nem haladja meg a maximális korlátot.
>
> Ha a korlátot vagy kvótát az alapértelmezett korlát fölé szeretné emelni, [nyisson meg egy online ügyfélszolgálati kérelmet díjmentesen.](../templates/error-resource-quota.md)

[Az ingyenes próbaverziós előfizetések](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak a korlát vagy a kvótanövelésére. Ha ingyenes [próbaverziós előfizetéssel](https://azure.microsoft.com/offers/ms-azr-0044p)rendelkezik, frissíthet a [csak annyi-ki(ti)](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetésre, hogy az ingyenesen használható előfizetéssel rendelkezik. További információ: [Ingyenes Azure-próba-előfizetés frissítése használatra fizetéses előfizetésre](../../billing/billing-upgrade-azure-subscription.md) és az [ingyenes próbaverziós előfizetéssel kapcsolatos gyakori kérdések című témakörben.](https://azure.microsoft.com/free/free-account-faq)

Egyes korlátokat regionális szinten kezelnek.

Vegyük példaként a vCPU-kvótákat. Ha kvótanövelést szeretne kérni a vCPU-k támogatásával, el kell döntenie, hogy hány vCPU-t kíván használni, mely régiókban. Ezután egy adott kérelmet az Azure erőforráscsoport vCPU-kvóták a kívánt összegeket és régiókat. Ha 30 vCPU-t kell használnia Nyugat-Európában az alkalmazás futtatásához, akkor kifejezetten 30 vCPU-t kér Nyugat-Európában. A vCPU-kvóta nem nőtt más régiókban – csak Nyugat-Európa rendelkezik a 30-vCPU kvótával.

Ennek eredményeképpen döntse el, hogy az Azure erőforráscsoport-kvótáknak milyennek kell lenniük a számítási feladatokhoz bármely régióban. Ezután kérje ezt az összeget minden olyan régióban, amelybe telepíteni szeretné. Ha segítségre van szüksége az egyes régiók aktuális kvótáinak meghatározásához, olvassa [el az Erőforráskvóták hibáinak megoldása (Megoldása) témakört.](../templates/error-resource-quota.md)

## <a name="general-limits"></a>Általános határértékek

Az erőforrások nevére vonatkozó korlátozásokat az [Azure-erőforrások elnevezési szabályai és korlátozásai](resource-name-rules.md).

A Resource Manager API olvasási és írási korlátairól az [Erőforrás-kezelő kérések szabályozása című témakörben olvashat.](request-limits-and-throttling.md)

### <a name="management-group-limits"></a>Felügyeleti csoport korlátai

A következő korlátozások vonatkoznak a [felügyeleti csoportokra](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Előfizetés korlátai

A következő korlátozások az Azure Resource Manager és az Azure erőforráscsoportok használatakor érvényesek.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Erőforráscsoport korlátai

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Az Active Directory korlátai

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API-kezelési korlátok

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>App-szolgáltatás korlátai

Az App Service következő korlátozásai közé tartoznak a webalkalmazásokra, a mobilalkalmazásokra és az API-alkalmazásokra vonatkozó korlátozások.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Automatizálási korlátok

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure-gyorsítótár a Redis-korlátozásokhoz

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Az Azure Cloud Services korlátai

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Az Azure Cognitive Search korlátai

A díjszabási szintek határozzák meg a keresési szolgáltatás kapacitását és korlátait. A szintek a következők:

* **A** más Azure-előfizetőkkel megosztott ingyenes több-bérlős szolgáltatás kiértékelésre és kisebb fejlesztési projektekre szolgál.
* **Az alapszintű** dedikált számítási erőforrásokat biztosít az éles számítási feladatokhoz kisebb léptékben, legfeljebb három replikával a magas rendelkezésre állású lekérdezési számítási feladatokhoz.
* **Standard**, amely magában foglalja az S1, S2, S3 és S3 nagy sűrűségű, a nagyobb termelési számítási feladatok. A standard szinten belül több szint is létezik, így olyan erőforrás-konfigurációt választhat, amely a legjobban megfelel a számítási feladatok profiljának.

**Előfizetésenkénti korlátok**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Korlátozások keresési szolgáltatásonként**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Ha többet szeretne megtudni a részletesebb szintű korlátozásokról, például a dokumentumméretről, a másodpercenkénti lekérdezésekről, a kulcsokról, a kérésekről és a válaszokról, olvassa el [a Szolgáltatáskorlátok az Azure Cognitive Search szolgáltatáskorlátai](../../search/search-limits-quotas-capacity.md)ban.

## <a name="azure-cognitive-services-limits"></a>Az Azure Cognitive Services korlátai

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Az Azure Cosmos DB korlátai

Az Azure Cosmos DB-korlátokról az [Azure Cosmos DB korlátai.](../../cosmos-db/concepts-limits.md)

## <a name="azure-data-explorer-limits"></a>Az Azure Data Explorer korlátai

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Az Azure Database for MySQL-korlátokról az [Azure Database for MySQL](../../mysql/concepts-limits.md)című témakörben található.

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

A PostgreSQL-korlátozásokhoz készült Azure Database for Azure Database [(Azure Database](../../postgresql/concepts-limits.md)for PostgreSQL) című témakörben található.

## <a name="azure-functions-limits"></a>Az Azure Functions korlátai

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Az Azure Kubernetes szolgáltatás korlátai

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Az Azure Machine Learning korlátai

Az Azure Machine Learning számítási kvótáinak legújabb értékei az [Azure Machine Learning kvótalapján](../../machine-learning/how-to-manage-quotas.md) találhatók.

## <a name="azure-maps-limits"></a>Az Azure Maps korlátai

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Az Azure Monitor korlátai

### <a name="alerts"></a>Riasztások

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Műveletcsoportok

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>Naplózási lekérdezések és nyelv

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics-munkaterületek

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Az Azure szabályzatkorlátai

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Az Azure SignalR szolgáltatás korlátai

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Biztonsági mentési korlátok

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Köteghatárok

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Klasszikus üzembe helyezési modell korlátai

Ha az Azure Resource Manager üzembe helyezési modellje helyett klasszikus üzembe helyezési modellt használ, a következő korlátok érvényesek.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Tárolópéldányok korlátai

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Tároló-beállításjegyzék-korlátok

Az alábbi táblázat az alapszintű, standard és prémium [szintű szolgáltatási szintek funkcióit](../../container-registry/container-registry-skus.md)és korlátait ismerteti.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Tartalomkézbesítési hálózat korlátai

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Adatgyári korlátok

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>A Data Lake Analytics korlátai

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Data Lake Áruház korlátai

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Adatmegosztási korlátok

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Adatbázis-áttelepítési szolgáltatás korlátai

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>Eseményrács korlátai

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Az eseményközpontok korlátai

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Identitáskezelő korlátai

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>IoT központi határértékek
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Az IoT Hub korlátai

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub-eszközkiépítési szolgáltatás korlátai

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>A Key Vault korlátai

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>A Media Services korlátai

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2 (örökölt)

A Media Services 2-es (örökölt) verzióra vonatkozó korlátait lásd: [Media Services v2 (örökölt)](https://docs.microsoft.com/azure/media-services/previous/media-services-quotas-and-limitations)

## <a name="mobile-services-limits"></a>Mobilszolgáltatások korlátai

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Többtényezős hitelesítési korlátok

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Hálózatkezelési korlátok

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>ExpressRoute-korlátozások

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>Virtuális WAN-korlátok

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Alkalmazásátjáró-korlátozások

A következő táblázat a v1, v2, Standard és WAF skus-okra vonatkozik, hacsak másként nem jelezzük.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>A Hálózatfigyelő korlátai

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>A privát kapcsolat korlátai

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Forgalomkezelő korlátai

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Az Azure Bástya korlátai

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Az Azure DNS-korlátai

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Az Azure tűzfal korlátai

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Az Azure Bejárati ajtó szolgáltatás korlátai

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Értesítési központok korlátai

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>Szerepköralapú hozzáférés-vezérlési korlátok

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>A Szolgáltatásbusz korlátai

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>A Site Recovery korlátai

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL-adatbázis korlátai

Az SQL Database-korlátokról az [SQL Database egyes adatbázisokra vonatkozó erőforráskorlátait,](../../sql-database/sql-database-vcore-resource-limits-single-databases.md) [a rugalmas készletek és a készletes adatbázisok SQL Database erőforráskorlátait,](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md) [valamint a felügyelt példányok SQL Database erőforráskorlátait](../../sql-database/sql-database-managed-instance-resource-limits.md)című témakörben található.

## <a name="sql-data-warehouse-limits"></a>SQL Adattárház korlátai

Az SQL Data Warehouse korlátairól az SQL Data Warehouse erőforráskorlátai című [témakörben található.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)

## <a name="storage-limits"></a>Tárolási korlátok

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

A standard tárfiókok ra vonatkozó korlátozásokról további információt a [szabványos tárfiókok méretezhetőségi céljai című](../../storage/common/scalability-targets-standard-account.md)témakörben talál.

### <a name="storage-resource-provider-limits"></a>A tárolási erőforrások szolgáltatóinak korlátai

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Az Azure Blob tárolási korlátai

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Az Azure Files korlátai

Az Azure Files korlátairól az [Azure Files méretezhetőségi és teljesítménycélok című](../../storage/files/storage-files-scale-targets.md)témakörben talál további információt.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Az Azure-fájlszinkronizálás korlátai

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Az Azure-várólista tárolási korlátai

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Az Azure Table tárolási korlátai

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Virtuálisgép lemezkorlátai

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

További információ: [Virtuális gépméretek](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="managed-virtual-machine-disks"></a>Felügyelt virtuálisgép-lemezek

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Nem felügyelt virtuálisgép-lemezek

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple rendszer korlátai

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>A Stream Analytics korlátai

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Virtuális gépek korlátai

### <a name="virtual-machines-limits"></a>Virtuális gépek korlátai

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtuális gépek korlátai – Azure Resource Manager

A következő korlátozások az Azure Resource Manager és az Azure erőforráscsoportok használatakor érvényesek.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Megosztott képgaléria korlátai

Előfizetésenként vannak korlátok a megosztott képgalériák használatával történő erőforrások üzembe helyezésére:

- 100 megosztott képgaléria előfizetésenként, régiónként
- 1000 képdefiníció előfizetésenként, régiónként
- 10 000 képverzió előfizetésenként, régiónként

## <a name="virtual-machine-scale-sets-limits"></a>A virtuális gépek méretezési csoportja korlátozza

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Lásd még

* [Az Azure korlátainak és növelésének megismerése](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Virtuális gép és felhőszolgáltatás mérete az Azure-hoz](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Méretek az Azure Felhőszolgáltatásaihoz](../../cloud-services/cloud-services-sizes-specs.md)
* [Az Azure-erőforrásokra vonatkozó elnevezési szabályok és korlátozások](resource-name-rules.md)

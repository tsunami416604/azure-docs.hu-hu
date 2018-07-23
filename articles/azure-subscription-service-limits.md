---
title: Azure-előfizetés korlátok és kvóták
description: Gyakori Azure-előfizetés és a szolgáltatásokra vonatkozó korlátozások, kvóták és megkötések listáját tartalmazza. Ez magában foglalja a korlátok és maximális értékeket növelésére vonatkozó információkat.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 07/13/2018
ms.author: byvinyal
ms.openlocfilehash: 570eee6cc849c474cfeacf5a9ba1798f64f27183
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188075"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozások, kvóták és megkötések
Ez a dokumentum felsorolja az egyes a leggyakrabban használt Microsoft Azure korlátok, kvóták néven is ismert. Ez a dokumentum jelenleg nem vonatkozik Azure-szolgáltatásokhoz. Az idő múlásával a lista fog kibontható vagy frissítve, hogy biztosítsák a platform további.

Látogasson el [Azure díjszabás áttekintése](https://azure.microsoft.com/pricing/) tudhat meg többet az Azure díjszabása. Itt meg tudja becsülni a költségeket, használja a [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) vagy egy szolgáltatás a díjszabás részleteit ismertető oldal (például [Windows virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Tippek a költségek kezeléséhez: [az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése](billing/billing-getting-started.md).

> [!NOTE]
> Ha azt szeretné, a korlát vagy a fenti kvóta emelése az **alapértelmezett korlát**, [nyisson meg egy online támogatási kérést díjmentesen](azure-resource-manager/resource-manager-quota-errors.md). Korlátait fentiekben nem léptethető elő a **maximálisan** értékét az alábbi táblázatban látható. Ha nincs **maximálisan** oszlop, akkor az erőforrás nem állítható korlátokkal rendelkeznek.
>
> [Az ingyenes próbaverziós előfizetésekben](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak korlátozása vagy a kvóta növekszik. Ha rendelkezik egy [ingyenes próba-előfizetésre](https://azure.microsoft.com/offers/ms-azr-0044p), frissíthet egy [használatalapú](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetés. További információkért lásd: [Azure ingyenes próbaverzió frissítése használatalapú fizetésre](billing/billing-upgrade-azure-subscription.md) és [ingyenes próba-előfizetéssel kapcsolatos gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-the-azure-resource-manager"></a>Korlátozások és az Azure Resource Manager
Már lehetséges úgy, hogy több Azure-erőforrások az egyetlen Azure-erőforráscsoportot. Erőforráscsoportok használatakor a korlátok, amelyek többször is a globális regionális szinten az Azure Resource Manager felügyelete. Azure erőforráscsoportokkal kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md).

Az alábbi korlátok egy új táblát bővült, hogy minden korlátokat különbségek az Azure Resource Manager használata esetén. Ha például van egy **előfizetési korlátozásait** tábla és a egy **előfizetési korlátok – Azure Resource Manager** tábla. Mindkét forgatókönyvet korlát vonatkozik, ha azt csak az első táblázatban látható. Eltérő jelzés hiányában korlátok egészére érvényesek minden régióban.

> [!NOTE]
> Fontos kiemelni, hogy meg Azure-erőforráscsoport erőforrásokra vonatkozó kvótákat régiók szerinti az előfizetés által elérhető, és amelyek nem előfizetésenként, mivel a szolgáltatás felügyeleti kvótáit. Példaként használjuk a vCPU-kvóták. Ha szeretné a kvóta növelésére vcpu-k támogatását, döntse el, melyik régióban található, és végezze el az Azure-erőforráscsoport vCPU-kvóták egy adott kérés a összegek és a kívánt régióban szeretné hány vcpu-k szeretne. Ezért ha szüksége van az alkalmazás futtatásához használja a Nyugat-Európában 30 vcpu-k, kifejezetten igényeljen 30 vcpu-k Nyugat-Európában. Azonban nem fog tudni egy vCPU-kvóta bármelyik régióban növelése – csak Nyugat-Európa fog rendelkezni a 30-vCPU-kvóta.
> <!-- --> Ennek eredményeképpen, előfordulhat, hogy érdemes figyelembe venni, mellett dönt, az Azure-erőforráscsoport kvóták kell lennie minden olyan egy adott régióban a számítási feladatok számára hasznos lehet, és minden régióban, amelybe a központi telepítési releváns, összeg kérelem. Lásd: [telepítési problémák elhárítása](resource-manager-common-deployment-errors.md) felderítése az adott régió esetében az aktuális kvótái további segítséget itt találhat.
>
>

## <a name="service-specific-limits"></a>Szolgáltatásspecifikus korlátok
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [APP SERVICE](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automatizálás](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure Event Grid](#azure-event-grid-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Biztonsági mentés](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [TARTALOMKÉZBESÍTÉSI HÁLÓZAT (CDN)](#cdn-limits)
* [Felhőszolgáltatások](#cloud-services-limits)
* [Tárolópéldányok](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Kubernetes-szolgáltatást](#kubernetes-service-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake analitikai szolgáltatás](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Event Hubs](#event-hubs-limits)
* [Az Azure-tűzfal](#azure-firewall-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub eszközregisztrációs szolgáltatás](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Felügyelt identitás](#managed-identity-limits)
* [Médiaszolgáltatások](#media-services-limits)
* [Mobilmarketing](#mobile-engagement-limits)
* [Mobile Services](#mobile-services-limits)
* [Figyelés](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [Hálózat](#networking-limits)
* [A Network Watcher](#network-watcher-limits)
* [Értesítési központ szolgáltatás](#notification-hub-service-limits)
* [Erőforráscsoport](#resource-group-limits)
* [Szerepköralapú hozzáférés-vezérlés](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Search](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple rendszerbe](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Előfizetés](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtuális gépek](#virtual-machines-limits)
* [Virtual Machine Scale Sets](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Előfizetés korlátai
#### <a name="subscription-limits"></a>Előfizetés korlátai
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Előfizetési korlátok – Azure Resource Manager
Az alábbi korlátok vonatkoznak az Azure Resource Manager és az Azure-erőforráscsoportok használata esetén. Korlátok, amelyek nem változtak az Azure Resource Manager alább nem láthatók. Tekintse meg az előző táblázatban a ezeket a korlátokat.

További információ a korlátok a Resource Manager-kérelmek kezelésére: [szabályozás Resource Manager-kérelmek](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Erőforráscsoport-korlátok
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtuális gépek korlátaira
#### <a name="virtual-machine-limits"></a>Virtuális gépek korlátai
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtuális gépek korlátok – Azure Resource Manager
Az alábbi korlátok vonatkoznak az Azure Resource Manager és az Azure-erőforráscsoportok használata esetén. Korlátok, amelyek nem változtak az Azure Resource Manager alább nem láthatók. Tekintse meg az előző táblázatban a ezeket a korlátokat.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Virtual Machine Scale Sets korlátok
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances korlátok
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Korlátozza a tárolóregisztrációs adatbázis
Az alábbi táblázat ismerteti a funkciókat és korlátokat az alapszintű, Standard és prémium szintű, [szolgáltatásszintek](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="kubernetes-service-limits"></a>Kubernetes-szolgáltatásokra vonatkozó korlátozások
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Hálózatkezelési korlátok
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Hálózatkezelési korlátok
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Az Application Gateway-korlátozások
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>A Network Watcher korlátozza.
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>A TRAFFIC Manager-korlátok
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS-korlátok
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Azure tűzfal korlátai
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

### <a name="storage-limits"></a>Tárolási korlátok
A tárfiókok korlátai további részletekért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](storage/common/storage-scalability-targets.md).

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Az Azure Blob storage-korlátok
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Az Azure Files-korlátok
Az Azure Files korlátok további részletekért lásd: [Azure Files méretezhetőségi és teljesítménycéljai](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Az Azure File Sync-korlátok
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure Queue storage korlátai
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table storage korlátai
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Virtuális gép korlátok
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Lásd: [virtuálisgép-méretek](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) további részleteket.

#### <a name="managed-virtual-machine-disks"></a>Felügyelt virtuálisgép-lemezek

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Nem felügyelt virtuálisgép-lemezek

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Cloud Services korlátok
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Az App Service korlátai
A következő App Service – korlátok Web Apps, Mobile Apps, API Apps és Logic Apps korlátok közé tartoznak.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Scheduler – korlátozások
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch-korlátok
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>A BizTalk Services korlátok
Az alábbi táblázat a korlátokat az Azure Biztalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Az Azure Cosmos DB-korlátok
Az Azure Cosmos DB egy globális szintű adatbázist, amelyben átviteli sebesség és tárterület skálázhatók kezelésére, függetlenül az alkalmazás által kért. Ha az Azure Cosmos DB biztosítja a skála kapcsolatos kérdése van, küldjön e-mailek askcosmosdb@microsoft.com.

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Az Azure Database for MySQL-korlátok, lásd: [korlátozások az Azure Database for MySQL-hez](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Az Azure Database for PostgreSQL-korlátok, lásd: [korlátozások az Azure Database for postgresql-hez](postgresql/concepts-limits.md).

### <a name="mobile-engagement-limits"></a>A Mobile Engagement-korlátok
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Keresés korlátok
Tarifacsomag határozza meg, a kapacitás és a keresési szolgáltatás korlátozásait. Szintek a következők:

* *Ingyenes* értékelése és a kisebb fejlesztési projektek szánt más Azure-előfizetők, megosztott több-bérlős szolgáltatás.
* *Alapszintű* dedikált számítási erőforrások biztosít a kisebb méretű, magas rendelkezésre állású lekérdezési számítási feladatok esetében legfeljebb három replikákkal rendelkező, a termelési számítási feladatokhoz.
* *Standard (S1, S2, S3, nagy kapacitású S3)* nagyobb éles számítási feladatokra van. Többszintű létezniük a standard szintű, így választhat, hogy egy erőforrás-konfiguráció, amely a legjobban illik a számításifeladat-profil.

**Előfizetés vonatkozó korlátok**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Keresési szolgáltatás vonatkozó korlátok**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

További kapcsolatos részletesebb szintű, például a dokumentumok méretétől, a lekérdezések száma másodpercenként, kulcsok, kérelmek és válaszok, vonatkozó korlátozások: [korlátozások az Azure Search szolgáltatás](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>A Media Services-korlátok
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>A CDN-korlátok
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>A Mobile Services korlátok
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>A figyelő korlátok
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Notification Hub szolgáltatási korlátozások
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs-korlátok
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>A Service Bus-korlátok
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Az IoT Hub-korlátok
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service-korlátok
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>A Data Factory korlátai
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>A Data Lake Analytics-korlátok
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store-korlátok
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Korlátozza a Database Migration Service
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics-korlátok
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Az Active Directory-korlátok
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Az Azure Event Grid-korlátok
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps-korlátozások
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="storsimple-system-limits"></a>A StorSimple rendszer korlátok
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Log Analytics-korlátok
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Biztonsági mentési korlátok
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>A Site Recovery korlátai
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights-korlátok
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Az API Management-korlátok
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Az Azure Redis Cache-korlátok
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>A Key Vault-korlátok
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation-korlátok
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="managed-identity-limits"></a>Felügyelt identitás korlátok
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Szerepköralapú hozzáférés-vezérlési korlátozások
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Az SQL Database-korlátok
Az SQL Database-korlátok, lásd: [SQL Database erőforrás-korlátozások az önálló adatbázisok](sql-database/sql-database-vcore-resource-limits-single-databases.md) és [SQL Database erőforrás-korlátozások rugalmas készletek és a készletezett adatbázisok](sql-database/sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="sql-data-warehouse-limits"></a>Az SQL Data Warehouse-korlátok
Az SQL Data Warehouse-korlátok, lásd: [SQL Data Warehouse Erőforráskorlátok](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Lásd még
[Az Azure korlátai és nő ismertetése](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Virtuális gép és Felhőszolgáltatás-méretek az Azure-hoz](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[A Cloud Services méretei](cloud-services/cloud-services-sizes-specs.md)

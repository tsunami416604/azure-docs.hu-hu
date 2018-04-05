---
title: Azure-előfizetés korlátozásai és a kvóták |} Microsoft Docs
description: A közös Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések listáját tartalmazza. Ide tartoznak a korlátjának növelésére korlátok együtt maximális értékeket.
services: ''
documentationcenter: ''
author: rothja
manager: jeffreyg
editor: ''
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: byvinyal
ms.openlocfilehash: f8b97aeb4c37578b884c924bd1919a59fa0f1cf8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozások, kvóták és megkötések
Ez a dokumentum mutatja a leggyakrabban használt Microsoft Azure korlátok, kvóták néven is ismert. Ez a dokumentum jelenleg nem fedi le az összes Azure-szolgáltatásokhoz. Adott idő alatt a lista lesz kibontható vagy frissíteni, amelyek több, a platform.

Látogasson el a [Azure díjszabása áttekintése](https://azure.microsoft.com/pricing/) tudhat meg többet az Azure-beli árakról. Van, a költségek használatával megbecsülhető a [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) vagy egy szolgáltatás árképzési részleteit megjelenítő oldalon felkeresésével (például [Windows virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Tippek a költségek kezeléséhez, tekintse meg a [Azure számlázás és költség felügyeleti váratlan költségek megakadályozása](billing/billing-getting-started.md).

> [!NOTE]
> Ha azt szeretné, a korlát vagy a fenti kvóta emelése a **alapértelmezett korlát**, [nyissa meg az online támogatás ügyfélkérés díjmentesen](azure-supportability/resource-manager-core-quotas-request.md). A korlátai nem léptethető elő fent a **maximális** az alábbi táblázatban szereplő érték. Ha nincs **maximális** oszlop, akkor az erőforrás nem állítható korlátokkal rendelkeznek.
>
> [Ingyenes próba-előfizetések](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak a korlát vagy kvóta növekszik. Ha rendelkezik egy [ingyenes próba-előfizetést](https://azure.microsoft.com/offers/ms-azr-0044p), frissíthet egy [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetés. További információkért lásd: [frissítése az Azure ingyenes próbaverzió használatalapú fizetésre](billing/billing-upgrade-azure-subscription.md) és [ingyenes előfizetés – GYIK](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-the-azure-resource-manager"></a>Korlátozásai és az Azure erőforrás-kezelő
Már lehetséges a több Azure-erőforrások az Azure egyetlen erőforráscsoporthoz kombinálni. Erőforráscsoportok használata esetén, amelyek egyszer volt a globális korlátok regionális szinten az Azure Resource Manager felügyelhető legyen. Azure erőforráscsoport-sablonok kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md).

Az alábbi korlátok új tábla összes különbséget korlátok megfelelően az Azure Resource Manager használatakor bővült. Például van egy **előfizetési korlátozásait** tábla és egy **előfizetési korlátozásait - Azure Resource Manager** tábla. Ha a megadott korlát érvényes, a mindkét forgatókönyvet, csak látható az első tábla. Hiányában korlátok legyenek globális minden régióban.

> [!NOTE]
> Fontos, hogy Azure erőforráscsoport-sablonok az erőforrásokra vonatkozó kvótákat /-régióban elérhető-e az előfizetés, és nem előfizetésenként, mert a szolgáltatás felügyeleti kvóták emelje ki. Most használja vCPU kvóták példaként. Ha a kvóta növelését Vcpu támogatása van szüksége, döntse el, melyik régióban, és végezze el az összegek és régiók, amelyet egy adott kérelem az Azure-erőforráscsoport vCPU kvóták kívánt hány Vcpu szeretné. Ezért ha 30 Vcpu, Nyugat-Európában használatával futtassa az alkalmazást nem kell, kifejezetten igényeljen 30 Vcpu, Nyugat-Európában. Azonban Ön nem rendelkezik a vCPU kvóta növelése más régióban – csak Nyugat-Európában fog rendelkezni a 30-vCPU kvótát.
> <!-- -->
> Ennek köszönhetően Ön érdemes figyelembe venni annak eldöntése, az Azure-erőforráscsoport kvóták kell lennie a munkaterheléshez bármely egy régióban, és minden régióban, amelybe a központi telepítés tervezi, hogy mennyi kérelem. Lásd: [telepítési problémák elhárítása](resource-manager-common-deployment-errors.md) további segítséget itt találhat az aktuális kvóták adott régióban felderítéséhez.
>
>

## <a name="service-specific-limits"></a>Szolgáltatás-specifikus korlátok
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [APP SERVICE](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automatizálás](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Az Azure Event rács](#azure-event-grid-limits)
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Biztonsági mentés](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [TARTALOMKÉZBESÍTÉSI HÁLÓZAT (CDN)](#cdn-limits)
* [Felhőszolgáltatások](#cloud-services-limits)
* [Tárolópéldányok](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [A Tárolószolgáltatás (AKS)](#container-service-aks-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake analitikai szolgáltatás](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Adatbázis-szolgáltatás áttelepítése](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Event Hubs](#event-hubs-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub eszköz kiépítése szolgáltatáshoz](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Médiaszolgáltatások](#media-services-limits)
* [Mobilmarketing](#mobile-engagement-limits)
* [Mobile Services](#mobile-services-limits)
* [Figyelés](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [Hálózat](#networking-limits)
* [Hálózati figyelőt](#network-watcher-limits)
* [Értesítési központ szolgáltatás](#notification-hub-service-limits)
* [Erőforráscsoport](#resource-group-limits)
* [Scheduler](#scheduler-limits)
* [Search](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Előfizetés](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtuális gépek](#virtual-machines-limits)
* [Virtual Machine Scale Sets](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Előfizetési korlátozásait
#### <a name="subscription-limits"></a>Előfizetési korlátozásait
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Előfizetési korlátozásait - Azure Resource Manager
Az alábbi korlátokat alkalmazza, ha az Azure Resource Manager és az Azure erőforráscsoport-sablonok használatával. Korlátok, nem módosított rendelkező az Azure erőforrás-kezelő nem az alábbiak. Tekintse meg az előző táblázatban ezeket a határértékeket.

Erőforrás-kezelő kérelmekre vonatkozó korlátozások kezelésére vonatkozó információkért lásd: [sávszélesség-szabályozás erőforrás-kezelő kérelmek](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Erőforráscsoport korlátok
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtuális gépek korlátok
#### <a name="virtual-machine-limits"></a>Virtuális gépek korlátai
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtuális gépek korlátok - Azure Resource Manager
Az alábbi korlátokat alkalmazza, ha az Azure Resource Manager és az Azure erőforráscsoport-sablonok használatával. Korlátok, nem módosított rendelkező az Azure erőforrás-kezelő nem az alábbiak. Tekintse meg az előző táblázatban ezeket a határértékeket.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Virtuálisgép-méretezési készlet korlátai
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Tároló példányok korlátok
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Tároló beállításjegyzék korlátok
Az alábbi táblázat részletezi a szolgáltatások és a Basic, Standard és Premium határértékeinek [szolgáltatásszintek](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="container-service-aks-limits"></a>Korlátozza a tároló szolgáltatás (AKS)
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Hálózatkezelési korlátok
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Hálózatkezelési korlátok
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Alkalmazásátjáró korlátozza.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Korlátozza a hálózati figyelőt
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>A TRAFFIC Manager-korlátok
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS-korlátok
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Tárolási korlátai
A tárfiókok korlátai további részletekért lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](storage/common/storage-scalability-targets.md).

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Az Azure Blob storage korlátok
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Az Azure fájlok korlátok
Azure-fájlok korlátozások további részletekért lásd: [Azure fájlok méretezhetőségi és Teljesítménycélok](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Az Azure fájlszinkronizálás korlátok
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Az Azure várólista tárolási korlátai
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Az Azure Table storage korlátok
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Virtuális gépek lemez korlátai
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Lásd: [virtuálisgép-méretek](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) további részleteket.

#### <a name="managed-virtual-machine-disks"></a>Felügyelt virtuális gépek lemezei

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Nem felügyelt virtuális gépek lemezei

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Cloud Services korlátok
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>App Service szolgáltatásra vonatkozó korlátok
Az alábbi korlátokat App Service Web Apps, a Mobile Apps, az API-alkalmazások és a Logic Apps korlátok tartalmazza.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>A Feladatütemező korlátok
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Kötegelt korlátok
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk szolgáltatások korlátok
A következő táblázat a korlátok Azure Biztalk szolgáltatások.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Az Azure Cosmos DB korlátok
Azure Cosmos-adatbázis egy globális méretű adatbázist, amelyben átviteli sebesség és tárterület is méretezhető kezelni, függetlenül az alkalmazás által igényelt. Ha az Azure Cosmos DB biztosít méretezésének kérdése van, kérjük, küldjön e-mailek askcosmosdb@microsoft.com.

### <a name="mobile-engagement-limits"></a>A Mobile Engagement korlátok
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Keresési korlátok
Tarifacsomagok határozza meg, a kapacitás és a keresőszolgáltatása határain. Szolgáltatásszintek:

* *Szabad* értékelési és kis fejlesztési projektek szánt más Azure-előfizetők megosztott, több-bérlős szolgáltatást.
* *Alapszintű* dedikált számítási erőforrások biztosít a termelési számítási feladatokhoz kisebb méretekben, magas rendelkezésre állású lekérdezés munkaterhelések legfeljebb három replikával.
* *Standard (S1, S2, S3, S3 nagy sűrűségű)* értéke nagyobb a termelési számítási feladatokhoz. Több szinten vannak a standard csomagot, hogy Ön egy erőforrás-konfigurációhoz, amely a legjobban illik a munkaterhelés-profil.

**Előfizetésenként korlátok**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Érhető el keresési szolgáltatásonként korlátok**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

További kapcsolatos részletesebb felügyeletét, például a dokumentum mérete, a lekérdezések száma másodpercenként, kulcsok, kérések és válaszok, használati korlátait: [szolgáltatási korlátait, az Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>A Media Services korlátok
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>CDN-korlátok
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>A Mobile Services korlátok
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>A figyelő korlátok
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Értesítési központ szolgáltatásra vonatkozó korlátozások
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs korlátok
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>A Service Bus korlátok
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Az IoT-központ korlátok
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Az IoT Hub eszköz kiépítése szolgáltatásra vonatkozó korlátozások
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Adat-előállító korlátozza.
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>A Data Lake Analytics korlátok
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store korlátok
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Adatbázis áttelepítési szolgáltatásra vonatkozó korlátozások
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>A Stream Analytics korlátozza.
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory-korlátok
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Az Azure Event rács korlátok
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple rendszer korlátok
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>A Naplóelemzési korlátozza.
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Biztonsági mentési korlátok
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>A Site Recovery korlátai
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights korlátok
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Korlátozza az API Management
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Azure Redis Cache-korlátok
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Key Vault korlátok
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Többtényezős hitelesítés
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automatizálási korlátok
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>SQL-adatbázis korlátok
SQL adatbázis-korlátok, lásd: [SQL adatbázis erőforrás korlátok](sql-database/sql-database-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Az SQL Data Warehouse korlátok
Az SQL Data Warehouse-korlátok, lásd: [SQL Data Warehouse erőforrás korlátok](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Lásd még
[Azure korlátozását és növekszik ismertetése](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Virtuális gépek és Felhőszolgáltatások mérete az Azure-bA](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[A Felhőszolgáltatások mérete](cloud-services/cloud-services-sizes-specs.md)

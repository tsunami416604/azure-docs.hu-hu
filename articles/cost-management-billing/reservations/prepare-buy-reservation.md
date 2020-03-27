---
title: Az Azure-foglalás megvásárlásának előkészítése
description: Ismerje meg a fontosabb szempontokat, mielőtt Azure-foglalást vásárol.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235655"
---
# <a name="prepare-to-buy-a-reservation"></a>Foglalásvásárlás előkészítése

Az Azure Reservationsszel csökkentheti költségeit, ha több Azure-erőforrásra egy vagy három évre előre kötelezettséget vállal. Mielőtt elkötelezné magát a foglalásvásárlás mellett, tekintse át a következő szakaszokat, hogy felkészülhessen a vásárlásra.

## <a name="who-can-buy-a-reservation"></a>Ki vásárolhat foglalást?

Egy csomag megvásárlásához rendelkeznie kell egy előfizetés-tulajdonosi szerepkörrel egy nagyvállalatban (MS-AZR-0017P vagy MS-AZR-0148P) vagy használatalapú fizetéses előfizetéssel (MS-AZR-0003P vagy MS-AZR-0023P) vagy Microsoft-ügyfélszerződéses előfizetéssel. A felhőszolgáltatók az Azure Portalt vagy a  [Partnerközpontot](/partner-center/azure-reservations)  használhatják az Azure Reservations megvásárlásához.

A Nagyvállalati Szerződéssel (EA) rendelkező ügyfelek az EA-rendszergazdákra korlátozhatják a vásárlásokat a **Fenntartott példányok hozzáadása** beállítás EA Portalon történő letiltásával. Foglalás vásárlásához az EA-rendszergazdáknak legalább egy EA-előfizetésben előfizetés-tulajdonosnak kell lenniük. Ez a beállítás olyan vállalatok számára hasznos, amelyek azt szeretnék, hogy egy központi csapat vásárolja meg a foglalásokat különböző költséghelyek számára. A vásárlás után a központi csapatok hozzáadhatják a költséghely-tulajdonosokat a foglalásokhoz. A tulajdonosok ezután beállíthatják a foglalás hatókörét az előfizetéseikre. A központi csapatnak nem kell előfizetés-tulajdonosi hozzáféréssel rendelkeznie a foglalás vásárlási helyén.

A foglalási kedvezmény csak olyan erőforrásokra érvényes, amelyek nagyvállalati, felhőszolgáltatói (CSP), Microsoft-ügyfélszerződéses vagy egyéni, használatalapú díjas csomagokkal lettek megvásárolva.

## <a name="scope-reservations"></a>A foglalások hatókörének beállítása

A foglalásokhoz egy előfizetést vagy erőforráscsoportokat állíthat be hatókörként. A foglalás hatókörének beállítása megadja a foglalási kedvezmények alkalmazásának helyét. Ha egy erőforráscsoportot állít be a foglalás hatóköreként, akkor a foglalási kedvezmények csak az erőforráscsoportra érvényesek, nem a teljes előfizetésre.

### <a name="reservation-scoping-options"></a>A foglalás hatókörkezelési lehetőségei

Az igényeitől függően három lehetősége van a foglalás hatókörének beállítására:

- **Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.
- **Egy előfizetésre vonatkozó hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található egyező erőforrásokra alkalmazza.
- **Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található egyező erőforrásokra alkalmazza. A Nagyvállalati Szerződéssel rendelkező ügyfelek esetében a számlázási környezet a regisztráció. A Microsoft-ügyfélszerződés ügyfelei esetében a számlázási profil a számlázási hatókör. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.

A foglalási kedvezmények a használatra történő alkalmazása során az Azure a következő sorrendben dolgozza fel a foglalást:

1. Erőforráscsoport hatókörű foglalások
2. Egyetlen hatókörrel rendelkező foglalások
3. Megosztott hatókörrel rendelkező foglalások

A foglalások hatókörének beállításától függően egy erőforráscsoport több foglalásból is kaphat foglalási kedvezményeket.

A foglalás megvásárlása után bármikor frissítheti a hatókört. Ehhez lépjen a foglalásra, kattintson a **Konfigurálás** lehetőségre, és állítsa be újból a foglalás hatókörét. A foglalás hatókörének újbóli beállítása nem számít kereskedelmi tranzakciónak. A foglalási időszak nem módosul. A hatókör frissítésével kapcsolatos további információkért lásd [a foglalás megvásárlása utáni hatókörfrissítést](manage-reserved-vm-instance.md#change-the-reservation-scope) bemutató cikket.

![A foglalási hatókör módosítását bemutató példa](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>Foglalások vásárlása

Foglalásokat az Azure Portalon, API-kkal, a PowerShellben és a parancssori felületen vásárolhat. Ha készen áll a foglalás vásárlására, olvassa el az alábbi, az Ön által használt szolgáltatásokra vonatkozó cikkeket:

- [APP SERVICE](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](../../data-explorer/pricing-reserved-capacity.md)
- [Disk Storage](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Szoftvercsomagok](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [Virtuális gépek](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>További lépések

- [Foglalások kezelése az Azure-erőforrásoknál](manage-reserved-vm-instance.md)

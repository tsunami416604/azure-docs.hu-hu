---
title: API-k az Azure-foglalás automatizálásához | Microsoft Docs
description: Ismerje meg azokat az Azure API-kat, amelyekkel programozott módon kérhet le foglalási információkat.
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 50de654fb9222951a7380a322160496421006e7a
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719681"
---
# <a name="apis-for-azure-reservation-automation"></a>API-k az Azure-foglalás automatizálásához

Az Azure API-k használatával programozott módon kérhet le információkat a szervezet számára az Azure-szolgáltatások vagy -szoftverek foglalásaival kapcsolatban.

## <a name="find-reservation-plans-to-buy"></a>Foglalási csomagok keresése vásárláshoz

A foglalási javaslat API-javaslatokat ad arra vonatkozóan, hogy mely foglalási csomagokat érdemes megvásárolni a szervezet használati adatai alapján. További információk: [Foglalási javaslatok kérése](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Emellett elemezheti saját erőforrás-használatát a használatai API használati adatai segítségével. További információk: [Használati adatok – Lista a számlázási időszakra számlázási fiók szerint](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod). A legtöbb esetben elsősorban a folyamatosan használt Azure-erőforrásokat érdemes lefoglalni.

## <a name="buy-a-reservation"></a>Foglalás vásárlása

A REST API-kkal programozott módon vásárolhat Azure-foglalásokat és szoftvercsomagokat. További tudnivalókért lásd: [Foglalás rendelése – Vásárlás API](/rest/api/reserved-vm-instances/reservationorder/purchase).

Íme egy mintakérés egy REST API-val történő vásárláshoz:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Kérés törzse:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Foglalásokat vásárolhat az Azure Portalon is. További információkért tekintse át a következő cikkeket:

Szolgáltatáscsomagok:
- [Virtuális gép](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Szoftvercsomagok:
- [SUSE Linux-szoftverek](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Foglalások kérése

Ha Ön Nagyvállalati Szerződéssel rendelkező Azure-ügyfél (EA-ügyfél), akkor lekérheti a szervezet által megvásárolt foglalásokat a [Fenntartott példányok tranzakciós díjainak lekérése API-val](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Ha másféle előfizetése van, akkor a [Foglalás rendelése – Lista](/rest/api/reserved-vm-instances/reservationorder/list) API használatával kérheti le a foglalások listáját, amelyeket megvásárolt, és jogosult megtekinteni. Alapértelmezés szerint a fiók tulajdonosának vagy a foglalást megvásárló személynek van engedélye a foglalást megtekinteni.

## <a name="see-reservation-usage"></a>Foglalás használatának megtekintése

Ha Ön EA-ügyfél, programozott módon megtekintheti, hogyan vannak kihasználva a szervezeten belüli foglalások. További információk: [Fenntartott példányok használatának lekérdezése vállalati ügyfeleknek](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). Egyéb előfizetések esetén használja a [Foglalások összegzése – Felsorolás foglalási rendelés és foglalás szerint](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation) API-t.

Ha azt tapasztalja, hogy a szervezete foglalásai nincsenek kihasználva:

- Ellenőrizze, hogy a szervezet által létrehozott virtuális gépek megfelelnek-e a foglalásban szereplő virtuálisgép-méretnek.
- Ellenőrizze, hogy a példányméret rugalmassága be van-e kapcsolva. További információk: [Foglalások kezelése – A Reserved VM Instances optimalizálási beállításának módosítása](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Módosítsa a foglalás hatókörét közösre, hogy az szélesebb körre vonatkozzon. További információk: [Foglalások kezelése – Foglalás hatókörének módosítása](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- Cserélje be a nem használt mennyiséget. További információk: [Foglalások kezelése](billing-manage-reserved-vm-instance.md).

## <a name="give-access-to-reservations"></a>A foglalásokhoz való hozzáférés megadása

A [Foglalás – Működés – Lista API](/rest/api/reserved-vm-instances/reservationorder/list) használatával lekérheti az összes olyan foglalás listáját, amelyekhez egy adott felhasználónak hozzáférése van. A foglalások programozott módon való hozzáférésével kapcsolatban lásd a következők cikkek valamelyikét:

- [Hozzáférés-kezelés az RBAC és a REST API használatával](../role-based-access-control/role-assignments-rest.md)
- [Hozzáférés-kezelés az RBAC és az Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)
- [Hozzáférés-kezelés az RBAC és az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Foglalás felosztása vagy egyesítése

Miután egy foglalásban egynél több erőforrás-példányt vásárol, érdemes lehet a foglalásban lévő példányokat különböző előfizetésekhez hozzárendelni. Módosíthatja a foglalások hatókörét, hogy az ugyanabban a számlázási környezetben található összes előfizetésre kiterjedjenek. Azonban költségkezelési vagy költséghatékonysági okokból érdemes lehet megtartani a hatókört „egyszeri előfizetésként”, és a foglalási példányokat egy bizonyos előfizetéshez hozzárendelni.

Egy foglalás felosztásához használja a [Foglalás – Felosztás](/rest/api/reserved-vm-instances/reservation/split) API-t. A foglalások a PowerShell használatával is feloszthatók. További információk: [Foglalások kezelése – Egy foglalás felosztása két foglalásra](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Ha két foglalást szeretne egyesíteni egy foglalásban, használja a [Foglalás – egyesítés](/rest/api/reserved-vm-instances/reservation/merge) API-t.

## <a name="change-scope-for-a-reservation"></a>Foglalás hatókörének módosítása

A foglalás hatóköre kiterjedhet egyetlen előfizetésre, egyetlen erőforráscsoportra, vagy a számlázási környezethez tartozó összes előfizetésre. Ha a hatókört egyetlen előfizetésre vagy erőforráscsoportra állítja be, a foglalást a rendszer a kiválasztott előfizetésben futó erőforrásokhoz igazítja. Ha törli vagy áthelyezi az előfizetést vagy az erőforráscsoportot, a foglalás nem lesz felhasználva.  Ha a hatókört közösre állítja, az Azure a foglalást a számlázási környezethez tartozó összes előfizetésben futó erőforrásokhoz igazítja. A számlázási környezet a foglalás megvásárlásához használt előfizetéstől függ. A hatókör a vásárláskor választható ki, és később bármikor módosítható. További információk: [Foglalások kezelése – Hatókör módosítása](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

A hatókör programozott módon történő módosításához használja a [Foglalás – Frissítés](/rest/api/reserved-vm-instances/reservation/update) API-t.

## <a name="learn-more"></a>Részletek

- [Mik azok az Azure-beli foglalások?](billing-save-compute-costs-reservations.md)
- [A virtuális gépek foglalására vonatkozó kedvezmény alkalmazásának ismertetése](billing-understand-vm-reservation-charges.md)
- [A SUSE Linux Enterprise szoftvercsomag kedvezményei alkalmazásának ismertetése](billing-understand-suse-reservation-charges.md)
- [Az egyéb foglalási kedvezmények alkalmazásának ismertetése](billing-understand-reservation-charges.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](billing-understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](billing-understand-reserved-instance-usage-ea.md)
- [A Reservations díjában nem szereplő Windows-szoftverköltségek](billing-reserved-instance-windows-software-costs.md)
- [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)

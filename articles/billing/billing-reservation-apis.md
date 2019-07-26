---
title: API-k az Azure foglalási automatizálásához | Microsoft Docs
description: Ismerje meg azokat az Azure API-kat, amelyek segítségével programozott módon kérhet le foglalási információkat.
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2019
ms.author: banders
ms.openlocfilehash: 20ed2bcf793ab5c3913ccf66d338e71c1a99a003
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478921"
---
# <a name="apis-for-azure-reservation-automation"></a>API-k az Azure foglalási automatizálásához

Az Azure API-k használatával programozott módon kérheti le a szervezet adatait az Azure-szolgáltatással vagy a szoftverekkel kapcsolatos foglalásokkal kapcsolatban.

## <a name="find-reservation-plans-to-buy"></a>A megvásárolni kívánt foglalási csomagok keresése

A foglalási javaslat API-val javaslatokat kaphat arra vonatkozóan, hogy mely foglalásokat tervezi megvásárolni a szervezet használati adatai alapján. További információ: foglalási [javaslatok](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)beszerzése.

Az erőforrás-használatot a használati API használatának részleteivel is elemezheti. További információ: [használati részletek – lista a számlázási időszakra vonatkozóan számlázási fiókkal](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod). A konzisztensen használt Azure-erőforrások általában a legjobb választás a foglaláshoz.

## <a name="buy-a-reservation"></a>Foglalás vásárlása

A REST API-kkal programozott módon vásárolhat Azure-foglalásokat és-szoftvereket. További információ: foglalási [rendelés – vásárlási API](/rest/api/reserved-vm-instances/reservationorder/purchase).

Íme egy példa a vásárlásra a REST API használatával:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Kérelem törzse:

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

A Azure Portal foglalását is megvásárolhatja. További információkért tekintse át a következő cikkeket:

Szolgáltatási csomagok:
- [Virtuális gép](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Szoftvercsomagok:
- [SUSE Linux szoftver](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Foglalások beolvasása

Ha Ön Azure-ügyfél Nagyvállalati Szerződés (nagyvállalati szerződéssel), a fenntartott példány tranzakciós költségeinek beszerzése [API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)-val lefoglalhatja a szervezet által vásárolt foglalásokat. Egyéb előfizetések esetén szerezze be a megvásárolt foglalások listáját, és jogosult a megtekintésre az API [-foglalási sorrend-lista](/rest/api/reserved-vm-instances/reservationorder/list)használatával. Alapértelmezés szerint a fiók tulajdonosa vagy a foglalást megvásárló személy jogosult a foglalás megtekintésére.

## <a name="see-reservation-usage"></a>A foglalás használatának megtalálása

Ha Ön nagyvállalati ügyfél, programozott módon megtekintheti, hogyan használják a szervezeten belüli foglalásokat. További információ: fenntartott [példányok használatának](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)beolvasása nagyvállalati ügyfelek számára. Egyéb előfizetések esetén használja az API [-foglalások összegzése-listát a foglalási sorrend és a foglalás alapján](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Ha azt tapasztalja, hogy a szervezet foglalásai jelenleg használatban vannak:

- Győződjön meg arról, hogy a szervezet által létrehozott virtuális gépek egyeznek a foglalásban található virtuálisgép-mérettel.
- Győződjön meg arról, hogy a példány méretének rugalmassága be van kapcsolva. További információ: fenntartások [kezelése – a fenntartott VM-példányok optimalizálási beállításainak módosítása](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Módosítsa a foglalás hatókörét úgy, hogy az szélesebb körben is érvénybe lépjen. További információ: foglalások [kezelése – foglalás hatókörének módosítása](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- A fel nem használt mennyiség cseréje. További információ: fenntartások [kezelése – lemondás és cserék](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Hozzáférés biztosítása a foglalásokhoz

Azon foglalások listájának beolvasása, amelyekhez a felhasználónak a [foglalási művelet-lista API](/rest/api/reserved-vm-instances/reservationorder/list)használatával hozzá kell férnie. Ha programozott módon kíván hozzáférést biztosítani a foglaláshoz, tekintse meg a következő cikkek egyikét:

- [Hozzáférés kezelése a RBAC és a REST API használatával](../role-based-access-control/role-assignments-rest.md)
- [Hozzáférés kezelése a RBAC és a Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)
- [Hozzáférés kezelése a RBAC és az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Foglalás felosztása vagy egyesítése

Miután egy foglalásban egynél több erőforrás-példányt vásárolt, érdemes lehet a foglalásban lévő példányokat hozzárendelni a különböző előfizetésekhez. A foglalás hatókörét módosíthatja úgy, hogy az azonos számlázási környezetben lévő összes előfizetésre vonatkozzon. A költségek kezeléséhez és a költségvetéshez azonban érdemes megtartani a hatókört "egyszeri előfizetésként", és a foglalási példányok hozzárendelését egy adott előfizetéshez.

Foglalás felosztásához használja az API [-foglalás – felosztás](/rest/api/reserved-vm-instances/reservation/split)lehetőséget. A lefoglalást a PowerShell használatával is eloszthatja. További információkért lásd: foglalások [kezelése – foglalások felosztása két foglalásra](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Ha két foglalást szeretne egyesíteni egy foglalásban, használja az API [-foglalás – egyesítés](/rest/api/reserved-vm-instances/reservation/merge)lehetőséget.

## <a name="change-scope-for-a-reservation"></a>Foglalás hatókörének módosítása

A foglalás hatóköre lehet egy előfizetés, egy erőforráscsoport vagy a számlázási környezetben lévő összes előfizetés. Ha a hatókört egy előfizetésre vagy egy erőforráscsoporthoz állítja be, a foglalás a kiválasztott előfizetéshez tartozó erőforrások futtatásával egyezik meg. Ha törli vagy áthelyezi az előfizetést vagy az erőforráscsoportot, a foglalás nem lesz felhasználva.  Ha a hatókört Megosztottra állítja, az Azure megfelel a foglalásnak a számlázási környezetben lévő összes előfizetésben futó erőforrásoknak. A számlázási környezet a foglalás megvásárlásához használt előfizetéstől függ. Kiválaszthatja a hatókört a vásárláskor, vagy bármikor módosíthatja a vásárlás után. További információ: fenntartások [kezelése – a hatókör módosítása](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

Ha programozott módon szeretné módosítani a hatókört, használja az API [-foglalás – frissítés](/rest/api/reserved-vm-instances/reservation/update)lehetőséget.

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról

- [Mik az Azure-foglalások](billing-save-compute-costs-reservations.md)
- [A virtuális gépek foglalási kedvezményének ismertetése](billing-understand-vm-reservation-charges.md)
- [A SUSE Linux Enterprise szoftvercsomag-kedvezmény alkalmazásának ismertetése](billing-understand-suse-reservation-charges.md)
- [Az egyéb foglalási kedvezmények alkalmazásának ismertetése](billing-understand-reservation-charges.md)
- [Az utólagos elszámolású előfizetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A Windows-szoftverek nem tartalmazzák a foglalásokat](billing-reserved-instance-windows-software-costs.md)
- [Azure Reservations a partner Center Cloud Solution Provider (CSP) programban](https://docs.microsoft.com/partner-center/azure-reservations)

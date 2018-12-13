---
title: API-k a foglalást az Azure automationhöz |} A Microsoft Docs
description: További információ az Azure API-kat, hogy programozott módon megszerezni a foglalási információkat használhatja.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: cwatson
ms.openlocfilehash: 01af1249039f8bfa1238cbbc12a77074e9347a39
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53264048"
---
# <a name="apis-for-azure-reservation-automation"></a>API-k a foglalást az Azure automation

Azure API-k használatával programozott módon az Azure szolgáltatás, vagy a szoftverfrissítési foglalások adatainak a szervezet lekérése.

## <a name="find-reservation-plans-to-buy"></a>Keresse meg a Foglalás csomag megvásárlása

A javaslatok API-t, amelyen foglalást vásárolhat megtervezése javaslatokat beolvasni a szervezet használat alapján foglalás további használatához. További információkért lásd: [foglalás javaslatok](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Az erőforrás-használatot a Használatalapú API részletes használati használatával is elemezheti. További információkért lásd: [használat részleteiről - listát a számlázási időszak szerint számlázási fiók](/rest/api/consumption/usagedetails/listforbillingperiodbybillingaccount). Az Azure-erőforrások konzisztens módon használó rendszerint egy foglalás a legjobb lehetőség.

## <a name="buy-a-reservation"></a>Foglalás vásárlása

Nem lehet jelenleg vásárolhat egy foglalást programozott módon. Megvásárolni a foglalást, tekintse meg a következő cikkeket:

Szolgáltatáscsomagok:
- [Virtuális gép](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Szoftverfrissítési csomagok:
- [SUSE Linux-szoftver](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Foglalások beolvasása

Ha Ön nagyvállalati szerződéssel (EA-ügyfél) rendelkező Azure-ügyfelek, beszerezheti a foglalások használatával vásárolt a szervezet a [beolvasni a fenntartott példányok tranzakciós díjat API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Más előfizetések listájának megtekintéséhez, amelytől vásárolta foglalások és engedélye annak megtekintésére, az API-val [Foglalásrendelés - lista](/rest/api/reserved-vm-instances/reservationorder/list). Alapértelmezés szerint a fiók tulajdonosa vagy a Foglalás vásárolta, aki rendelkezik a Foglalás megtekintéséhez szükséges engedélyekkel.

## <a name="see-reservation-usage"></a>Foglalás használati

Ha Ön nagyvállalati szerződéssel rendelkező ügyfelek, szoftveresen megtekintheti hogyan a foglalások a szervezetben vannak használatban. További információkért lásd: [első fenntartott példány használata a vállalati ügyfelek](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). Másik előfizetés esetén az API-val [foglalások összegzéseket - lista által foglalás rendelési és a Foglalás](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Ha látja, hogy a szervezet foglalások éppen használt alatt:

- Ellenőrizze, hogy a virtuális gépek, amelyek a szervezet által létrehozott felel meg, hogy a Foglalás található a Virtuálisgép-méretet.
- Ellenőrizze, hogy a példány mérete rugalmasan be van kapcsolva. További információkért lásd: [kezelés foglalások - módosítás optimalizálása beállítás a Reserved VM Instances](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- A megosztott szélesebb körben vonatkozik, hogy a foglalás hatókörének módosítása További információkért lásd: [foglalások kezelése – a foglalás hatókörének módosítása](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).
- Exchange-a nem használt mennyiség. További információkért lásd: [foglalások - általi hóközi lemondás és cseréje kezelése](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Hozzáférést biztosít a foglalások

Minden foglalást listájának lekéréséhez, hogy egy felhasználó rendelkezik-e a hozzáférést a [lista API - művelet - foglalás](/rest/api/reserved-vm-instances/reservationorder/list). Programozott módon biztosíthat hozzáférést egy foglalást, az alábbi cikkekben talál:

- [Rbac-RÓL és a REST API-hozzáférés kezelése](../role-based-access-control/role-assignments-rest.md)
- [Rbac-RÓL és az Azure PowerShell-hozzáférés kezelése](../role-based-access-control/role-assignments-powershell.md)
- [Rbac-RÓL és az Azure CLI-hozzáférés kezelése](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Foglalás felosztása és egyesítése

Miután vásárol egy foglalást belül egynél több erőforráspéldány, érdemes a Foglalás példányok hozzárendelése eltérő előfizetésekben. A Foglalás hatóköre módosítható úgy, hogy ugyanabban a számlázási környezetben belüli összes előfizetésre vonatkozik. Azonban a cost management vagy a költségvetés célokra, érdemes lehet az "egy előfizetés" hatókör tartani, és rendelje hozzá a Foglalás példányok egy meghatározott előfizetésre. 

A Foglalás felosztása, az API-val [- foglalás felosztása](/rest/api/reserved-vm-instances/reservation/split). PowerShell-lel is oszthatja a foglalást. További információkért lásd: [foglalások - felosztás foglalás be két foglalást kezelése](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Két foglalást egyesít egy foglalás megadása, hogy az API-val [foglalás - egyesítési](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>A foglalás hatókörének módosítása

A Foglalás hatóköre egy előfizetés vagy az összes előfizetést a számlázási környezetben lehet. Egyetlen előfizetéshez állítsa be a hatókört, ha a Foglalás megfeleltetett futó erőforrások a kijelölt előfizetésben. A megosztott hatókör beállítása, ha az Azure megfelel a Foglalás számlázási környezetében előfizetések futtató erőforrások. A számlázási környezetben szolgáltatás függ az előfizetést, amellyel megvásárolni a foglalást. További információkért lásd: [foglalások kezelése – a hatókör módosítása](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).

A hatókör módosítása a programozott módon, az API-val [foglalás - frissítés](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Részletek

- [Mik azok a foglalást az Azure-hoz](billing-save-compute-costs-reservations.md)
- [Megismerheti, hogyan kell alkalmazni a Virtuálisgép-foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
- [Megismerheti, hogyan kell alkalmazni a SUSE Linux Enterprise szoftverfrissítési csomag kedvezmény](billing-understand-suse-reservation-charges.md)
- [Megismerheti, hogyan más foglalás kedvezmények](billing-understand-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)
- [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](https://docs.microsoft.com/partner-center/azure-reservations)
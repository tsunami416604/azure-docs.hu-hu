---
title: Az Azure SQL Database foglalási kedvezményeinek ismertetése | Microsoft Docs
description: Megtudhatja, hogy a rendszer hogyan alkalmazza a foglalási kedvezményt a futó Azure SQL-adatbázisokra. Az adatbázisokra alkalmazott kedvezmény óraalapú.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 1df60eedfb776164be7e78f2994027b8d111828b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681957"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-database"></a>A foglalási kedvezmény alkalmazása az Azure SQL Database-re

A fenntartott Azure SQL Database-kapacitás megvásárlása után a rendszer automatikusan alkalmazza a foglalási kedvezményt a foglalás attribútumaival és mennyiségével egyező SQL-adatbázisokra. A foglalás az SQL Database-adatbázis számítási költségeit fedezi. A szoftverért, a tárolásért és a hálózatkezelésért a normál díjakat kell fizetnie. Az [Azure Hybrid Benefittel](https://azure.microsoft.com/pricing/hybrid-benefit/) fedezheti az SQL Database licencelési költségeit.

A foglalási kedvezmények nem alkalmazhatók a kiszolgáló nélküli Azure SQL Database-re.

A Reserved Virtual Machine Instanceszel kapcsolatban tekintse meg [az Azure Reserved VM Instances-kedvezményt bemutató](../manage/understand-vm-reservation-charges.md) cikket.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ez azt jelenti, hogy ha nem rendelkezik megfelelő erőforrásokkal egy adott órában, akkor az arra az órára vonatkozó foglalási mennyiség elveszik. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

## <a name="discount-applied-to-running-sql-databases"></a>A futó SQL-adatbázisokra alkalmazott kedvezmények

 A fenntartott SQL Database-kapacitásra vonatkozó kedvezményt a rendszer óránként alkalmazza a futó SQL-adatbázisokra. A megvásárolt foglalást a rendszer megfelelteti a számítási erőforrások futó SQL-adatbázisok általi használatának. Azon SQL-adatbázisok esetében, amelyek nem futnak egy teljes óráig, a rendszer automatikusan a foglalás jellemzőinek megfelelő más SQL-adatbázisokra alkalmazza a foglalást. A kedvezmény párhuzamosan futó SQL-adatbázisokra is alkalmazható. Ha nem rendelkezik olyan, a foglalási attribútumokkal egyező SQL-adatbázisokkal, amelyek egy teljes órán át futnak, akkor arra az órára nem kapja meg a foglalási kedvezménnyel járó teljes előnyt.

Az alábbi példák bemutatják, hogyan lesz alkalmazva a fenntartott SQL Database-kapacitásra érvényes kedvezmény a megvásárolt magok száma alapján, és az alapján, mikor futnak.

- 1\. forgatókönyv: Megvásárol egy fenntartott SQL Database-kapacitást egy 8 magos SQL Database-adatbázishoz. Egy olyan 16 magos SQL Database-adatbázist futtat, amely egyezik a foglalás többi attribútumával. A 8 magos SQL Database-adatbázis számításierőforrás-használatáért használatalapú díjat kell fizetnie. A foglalási kedvezményt a 8 magos SQL-adatbázis egy órányi számításierőforrás-használatára kapja meg.

A többi példa esetében azt feltételezzük, hogy a fenntartott SQL Database-kapacitást egy 16 magos SQL Database-adatbázishoz vásárolta, és a többi foglalási attribútum megegyezik a futó SQL-adatbázisokkal.

- 2\. forgatókönyv: Két 8 magos SQL-adatbázist futtat egy-egy óráig. A 16 magos foglalási kedvezményt a rendszer mindkét 8 magos SQL-adatbázis számításierőforrás-használatára alkalmazza.
- 3\. forgatókönyv: Egy 16 magos SQL Database-adatbázist futtat 13:00-tól 13:30-ig. Egy másik 16 magos SQL Database-adatbázist 13:30-tól 14:00-ig futtat. A foglalási kedvezmény mindkettőt fedezi.
- 4\. forgatókönyv: Egy 16 magos SQL Database-adatbázist futtat 13:00-tól 13:45-ig. Egy másik 16 magos SQL Database-adatbázist 13:30-tól 14:00-ig futtat. A 15 perces átfedésért használatalapú díjat kell fizetnie. A fennmaradó idő számításierőforrás-használatára érvényes a foglalási kedvezmény.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [az Azure Reservations használatát ismertető](understand-reserved-instance-usage-ea.md) cikket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../../azure-sql/database/reserved-capacity-overview.md)
- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
- [A foglalási kihasználtság ismertetése CSP-előfizetésnél](/partner-center/azure-reservations)

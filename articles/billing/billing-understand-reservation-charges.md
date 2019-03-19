---
title: Megismerheti a kedvezményes Azure-foglalások |} A Microsoft Docs
description: Ismerje meg, hogyan a foglalási kedvezményt alkalmazott futtató SQL-adatbázisok.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: banders
ms.openlocfilehash: ff31b9be0e927cca7d272e5deaa5ecf48a52580d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845458"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Megismerheti, hogyan kell alkalmazni az Azure a foglalási kedvezményt SQL-adatbázisok

Miután egy Azure SQL Database szolgáltatás számára fenntartott kapacitást vásárol, a rendszer automatikusan alkalmazza a foglalási kedvezményt SQL Database-adatbázisokhoz, amelyek megfelelnek a attribútumok és a Foglalás mennyiségét. Foglalás az SQL-adatbázis számítási költségeit tartalmazza. Szoftverek, tárolási és hálózatkezelési díjkötelesek a normál díjakat. Olyan SQL-adatbázisok esetén is terjed ki a licencelési költségei [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

A Reserved Virtual Machine Instances, lásd: [megismerheti az Azure Reserved VM Instances kedvezményes](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>SQL-adatbázisok alkalmazza a foglalási kedvezményt

 Óránként futó SQL-adatbázisok az SQL Database tartalékkapacitást kedvezmény érvényes. A compute használatát a futó SQL-adatbázisok által kibocsátott a foglalást, amely a vásárlás megfeleltetett. A rendszer azon SQL-adatbázisok esetében, amelyek nem futnak egy adott teljes órában, automatikusan a foglalás jellemzőinek megfelelő más SQL-adatbázisokra alkalmazza a foglalást. SQL-adatbázisok, amelyek egy időben futnak a kedvezményeket is alkalmazhat. Ha nem rendelkezik SQL-adatbázisok, amelyek teljes órát futnak, amelyek megfelelnek a Foglalás attribútumok, nem teljes kiaknázásához az adott órában a foglalási kedvezményt kap.

Az alábbi példák bemutatják, hogyan a SQL Database szolgáltatás számára fenntartott kapacitás kedvezmény érvényes számától függően a magok, vásárolt, és amikor futnak.

- 1. forgatókönyv: Egy 8 mag SQL-adatbázis egy SQL Database szolgáltatás számára fenntartott kapacitást vásárol. Egy 16 mag, amelyek megfelelnek a Foglalás attribútumait a többi SQL-adatbázis futtatása. 8 magos SQL-adatbázis számítási használat a használatalapú fizetéssel díjkötelesek. 8 mag, SQL Database a számítási feladatok használatáért egy órányi a foglalási kedvezményt kap.

Ezekben a példákban a többi feltételezik, hogy az SQL Database szolgáltatás számára fenntartott kapacitást vásárol egy 16 Core SQL Database és a többi a Foglalás attribútumok egyeznek a futó SQL-adatbázisok.

- 2. forgatókönyv: Két SQL-adatbázisok 8 maggal rendelkező, egy óránál futtatja. Compute használat mindkét a 8 mag, SQL Database-adatbázisok a 16 mag foglalási kedvezményt alkalmazza.
- 3. forgatókönyv: Egyet futtat, 13:30 = 1997031213 16 magos, 1 du az SQL Database. Egy másik 16 mag SQL Database 1:30 2 pm futtassa. A foglalási kedvezményt is tartoznak.
- 4. forgatókönyv: Egyet futtat, 13:45-kor 16 magos, 1 du az SQL Database. Egy másik 16 mag SQL Database 1:30 2 pm futtassa. A 15 perces átfedési használatalapú fizetéssel díjkötelesek. A compute használatát a hátralevő időben alkalmazza a foglalási kedvezményt.

Ismertetése és használati jelentések számlázási megtekintheti az alkalmazást az Azure-foglalások: [ismertetése Azure foglalás használatának](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [CSP-előfizetésekben foglalás használati adatai](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

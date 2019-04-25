---
title: Megismerheti a foglalások kedvezmény az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg, hogyan a foglalási kedvezményt kell alkalmazni az Azure SQL Database futtatása.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 4b4c6b390e9b3a0cf764f998523fe3c1cdc66026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370287"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Hogyan kell alkalmazni a foglalási kedvezményt az Azure SQL Database

Miután egy Azure SQL Database szolgáltatás számára fenntartott kapacitást vásárol, a rendszer automatikusan alkalmazza a foglalási kedvezményt SQL Database-adatbázisokhoz, amelyek megfelelnek a attribútumok és a Foglalás mennyiségét. Foglalás az SQL-adatbázis számítási költségeit tartalmazza. Szoftverek, tárolási és hálózatkezelési díjkötelesek a normál díjakat. Olyan SQL-adatbázisok esetén is terjed ki a licencelési költségei [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

A Reserved Virtual Machine Instances, lásd: [megismerheti az Azure Reserved VM Instances kedvezményes](billing-understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Hogyan alkalmazza a foglalási kedvezményt

A foglalási kedvezményt a "*használata – it-vagy-elveszíti-it*". Tehát ha az adott órán belül nem rendelkezik megfelelő erőforrásokat, majd elvesznek a foglalási mennyiség az adott órában. Nem sokat fel nem használt fenntartott órán keresztül továbbítja.

Ha leállítja egy erőforrást, a foglalási kedvezményt automatikusan alkalmazza a megadott hatókörben lévő másik egyező erőforrás. A megadott hatókörben lévő nem megfelelő erőforrások találhatók, akkor a szolgáltatás számára fenntartott órák *elveszett*.

## <a name="discount-applied-to-sql-databases"></a>SQL Database-adatbázisokhoz alkalmazott

 Óránként futó SQL-adatbázisok az SQL Database tartalékkapacitást kedvezmény érvényes. A compute használatát a futó SQL-adatbázisok által kibocsátott a foglalást, amely a vásárlás megfeleltetett. A rendszer azon SQL-adatbázisok esetében, amelyek nem futnak egy adott teljes órában, automatikusan a foglalás jellemzőinek megfelelő más SQL-adatbázisokra alkalmazza a foglalást. SQL-adatbázisok, amelyek egy időben futnak a kedvezményeket is alkalmazhat. Ha nem rendelkezik SQL-adatbázisok, amelyek teljes órát futnak, amelyek megfelelnek a Foglalás attribútumok, nem teljes kiaknázásához az adott órában a foglalási kedvezményt kap.

Az alábbi példák bemutatják, hogyan a SQL Database szolgáltatás számára fenntartott kapacitás kedvezmény érvényes számától függően a magok, vásárolt, és amikor futnak.

- 1. forgatókönyv: Egy 8 mag SQL-adatbázis egy SQL Database szolgáltatás számára fenntartott kapacitást vásárol. Egy 16 mag, amelyek megfelelnek a Foglalás attribútumait a többi SQL-adatbázis futtatása. 8 magos SQL-adatbázis számítási használat a használatalapú fizetéssel díjkötelesek. 8 mag, SQL Database a számítási feladatok használatáért egy órányi a foglalási kedvezményt kap.

Ezekben a példákban a többi feltételezik, hogy az SQL Database szolgáltatás számára fenntartott kapacitást vásárol egy 16 Core SQL Database és a többi a Foglalás attribútumok egyeznek a futó SQL-adatbázisok.

- 2. forgatókönyv: Két SQL-adatbázisok 8 maggal rendelkező, egy óránál futtatja. Compute használat mindkét a 8 mag, SQL Database-adatbázisok a 16 mag foglalási kedvezményt alkalmazza.
- 3. forgatókönyv: Egyet futtat, 13:30 = 1997031213 16 magos, 1 du az SQL Database. Egy másik 16 mag SQL Database 1:30 2 pm futtassa. A foglalási kedvezményt is tartoznak.
- 4. forgatókönyv: Egyet futtat, 13:45-kor 16 magos, 1 du az SQL Database. Egy másik 16 mag SQL Database 1:30 2 pm futtassa. A 15 perces átfedési használatalapú fizetéssel díjkötelesek. A compute használatát a hátralevő időben alkalmazza a foglalási kedvezményt.

Ismertetése és használati jelentések számlázási megtekintheti az alkalmazást az Azure-foglalások: [ismertetése Azure foglalás használatának](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [CSP-előfizetésekben foglalás használati adatai](/partner-center/azure-reservations)

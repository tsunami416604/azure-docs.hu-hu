---
title: A Azure Backup újdonságai
description: Ismerkedjen meg a Azure Backup új szolgáltatásaival.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: e48a7df1e42591843c30b77026a4d8656773a57d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511506"
---
# <a name="whats-new-in-azure-backup"></a>A Azure Backup újdonságai

A Azure Backup folyamatosan fejleszti és felszabadítja az Azure-ban tárolt adatai védelmét javító új funkciókat. Ezek az új funkciók kibővítik az adatvédelmet az új számítási feladatokra, javítják a biztonságot, és javítják a biztonsági mentési adatok rendelkezésre állását. Emellett új felügyeleti, monitorozási és automatizálási képességeket is felvehetnek.

Az új kiadásokról további információt az oldal könyvjelzővel vagy a [frissítésekre való feliratkozással](https://azure.microsoft.com/updates/?query=backup)olvashat.

## <a name="updates-summary"></a>Frissítések összegzése

- 2020. november
  - [Az Azure-beli virtuális gépeken SAP HANA adatbázisok növekményes biztonsági mentései](#incremental-backups-for-sap-hana-databases)
- 2020. szeptember
  - [Biztonsági mentési központ](#backup-center)
  - [Az Azure Database for PostgreSQL biztonsági mentése](#backup-azure-database-for-postgresql)
  - [Szelektív lemez biztonsági mentése és visszaállítása](#selective-disk-backup-and-restore)
  - [Régiók közötti visszaállítás SQL Server és SAP HANA Azure-beli virtuális gépeken található adatbázisok esetében](#cross-region-restore-for-sql-server-and-sap-hana)
  - [A legfeljebb 32 lemezzel rendelkező virtuális gépek biztonsági mentésének támogatása](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Az SQL Azure-beli virtuális gépeken való egyszerűsített biztonsági mentési konfigurációs felülete](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Backup SAP HANA a RHEL Azure-ban Virtual Machines](#backup-sap-hana-in-rhel-azure-virtual-machines)
  - [A zóna redundáns tárolója (ZRS) a biztonsági másolati adatként](#zone-redundant-storage-zrs-for-backup-data)
  - [Az Azure-beli virtuális gépek SQL Server és SAP HANA számítási feladatainak törlése](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="incremental-backups-for-sap-hana-databases"></a>SAP HANA adatbázisok növekményes biztonsági mentései

A Azure Backup mostantól támogatja az Azure-beli virtuális gépeken üzemeltetett SAP HANA-adatbázisok növekményes biztonsági mentését. Ez lehetővé teszi, hogy a SAP HANA-adatai gyorsabb és költséghatékonyabb biztonsági mentést készítsenek.

További információkért tekintse meg [a biztonsági mentési szabályzat létrehozása során elérhető különböző lehetőségeket](sap-hana-faq-backup-azure-vm.md#policy) , valamint [a SAP HANA adatbázisok biztonsági mentési szabályzatának létrehozását](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)ismertető témakört.

## <a name="backup-center"></a>Biztonsági mentési központ

A Azure Backup lehetővé tette, hogy egy új natív felügyeleti képességgel kezelhesse a teljes biztonsági mentési hagyatékot egy központi konzolról. A Backup Center lehetővé teszi, hogy az Azure natív felügyeleti tapasztalataival összhangban egységes módon figyelje, működtesse, szabályozza és optimalizálja az adatvédelem méretezését.

A Backup Center segítségével összesített képet kaphat a leltárról az előfizetések, a helyszínek, az erőforráscsoportok, a tárolók és még a bérlők között az Azure Lighthouse használatával. A Backup Center egyben egy műveleti központ is, amelyből kiválthatja a biztonsági másolatok kapcsolódó tevékenységeit, például a biztonsági mentést, a visszaállítást, a házirendek vagy tárolók létrehozását, mindezt egyetlen helyről. A Azure Policy zökkenőmentes integrációja mellett mostantól szabályozhatja a környezetet, és nyomon követheti a megfelelőséget egy biztonsági mentési perspektívában. A Azure Backupra jellemző, beépített Azure-szabályzatok lehetővé teszik a biztonsági másolatok méretezését is.

További információ: [a Backup Center áttekintése](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql"></a>Az Azure Database for PostgreSQL biztonsági mentése

A Azure Backup és az Azure Database Services együttesen létrehoz egy nagyvállalati szintű biztonsági mentési megoldást az Azure PostgreSQL-hez (mostantól előzetes verzióban érhető el). Az adatvédelmi és megfelelőségi igényeknek megfelelően egy ügyfél által vezérelt biztonsági mentési szabályzattal is találkozhat, amely lehetővé teszi a biztonsági másolatok megőrzését akár 10 évig. Ezzel a részletes szabályozással felügyelheti a biztonsági mentési és visszaállítási műveleteket az egyes adatbázis szintjén. Hasonlóképpen, a PostgreSQL-verziókon vagy a blob Storage-ban is könnyedén visszaállíthatók.

További információ: [Azure Database for PostgreSQL biztonsági mentés](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Szelektív lemez biztonsági mentése és visszaállítása

Azure Backup támogatja a virtuális gép összes lemezének (operációs rendszerének és adattípusának) biztonsági mentését egy virtuális gépen, a virtuális gépek biztonsági mentési megoldásával együtt. Most, hogy a szelektív lemezek biztonsági mentési és visszaállítási funkcióját használja, biztonsági mentést készíthet a virtuális gépek adatlemezei egy részhalmazáról. Ez hatékony és költséghatékony megoldást kínál a biztonsági mentési és visszaállítási igények kielégítésére. Az egyes helyreállítási pontok csak a biztonsági mentési művelet részét képező lemezeket tartalmazzák.

További információ: [szelektív lemezek biztonsági mentése és visszaállítása az Azure Virtual Machines szolgáltatásban](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana"></a>Régiók közötti visszaállítás SQL Server és SAP HANA

A régiók közötti visszaállítás bevezetésével mostantól egy másodlagos régióban visszaállíthatja a visszatárolást, amely csökkenti a valós állásidőt a környezete elsődleges régiójában. Így a másodlagos régió teljesen visszaállítja az ügyfél által vezérelt vezérlést. A Azure Backup a másodlagos régióba replikált biztonsági másolatokat használja az ilyen visszaállításokhoz.

Az Azure-beli virtuális gépek régiók közötti visszaállításának támogatása mellett a szolgáltatás kibővült az SQL-és SAP HANA-adatbázisok Azure-beli virtuális gépeken való visszaállítására is.

További információkért lásd: [tartományok közötti visszaállítás az SQL-adatbázisokhoz](restore-sql-database-azure-vm.md#cross-region-restore) és [a SAP HANA-adatbázisok régiók közötti visszaállításához](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>A legfeljebb 32 lemezzel rendelkező virtuális gépek biztonsági mentésének támogatása

Eddig Azure Backup 16 felügyelt lemezt támogatott virtuális gépenként. A Azure Backup jelenleg legfeljebb 32 felügyelt lemez biztonsági mentését támogatja virtuális gépenként.

További információkért lásd a [VM Storage támogatási mátrixát](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>SQL-alapú biztonsági mentési konfiguráció az Azure-beli virtuális gépeken

Az Azure-beli virtuális gépeken lévő SQL Server biztonsági másolatainak konfigurálása mostantól még egyszerűbbé válik a Azure Portal virtuálisgép-ablaktábláján integrált beágyazott biztonsági mentési konfigurációval. Néhány lépésben engedélyezheti a SQL Server biztonsági mentését, hogy megvédje az összes meglévő adatbázist, valamint a jövőben hozzáadott fájlokat.

További információ: [SQL Server biztonsági mentése a virtuális gép paneljéről](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines"></a>Biztonsági mentési SAP HANA a RHEL Azure-beli virtuális gépeken

A Azure Backup az Azure natív biztonsági mentési megoldása, amelyet az SAP BackInt tanúsított. Azure Backup már hozzáadta a Red Hat Enterprise Linux (RHEL) támogatását, amely az egyik legszélesebb körben használt Linux operációs rendszer, SAP HANA fut.

További információ: [SAP HANA adatbázis biztonsági mentési forgatókönyvének támogatási mátrixa](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data"></a>A zóna redundáns tárolója (ZRS) a biztonsági másolati adatként

Az Azure Storage nagyszerű egyensúlyt biztosít a nagy teljesítményű, magas rendelkezésre állású és magas adatrugalmassággal és a különböző redundancia-lehetőségekkel. Azure Backup lehetővé teszi, hogy kiterjessze ezeket az előnyöket a biztonsági másolatok adataira is, a biztonsági másolatok tárolásához a helyileg redundáns tárolás (LRS) és a Geo-redundáns tárolás (GRS) segítségével. Most további tartóssági lehetőségek állnak rendelkezésre a zóna redundáns tárolásának (ZRS) támogatásával.

További információ: Storage- [redundancia beállítása a Recovery Services-tárolóhoz](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server és SAP HANA számítási feladatokhoz tartozó helyreállítható törlés

A biztonsági problémákkal, például a kártevővel, a ransomware és a behatolással kapcsolatos problémák egyre nagyobbak. Ezek a biztonsági problémák költségesek lehetnek a pénz és az adatmennyiség tekintetében. Az ilyen támadások elleni védelem érdekében a Azure Backup biztonsági funkciókat biztosít a biztonsági mentési adatainak a törlés után még a védelme érdekében.

Az egyik ilyen funkció a Soft delete. A helyreállítható törléssel akkor is, ha egy rosszindulatú színész töröl egy biztonsági másolatot (vagy véletlenül törli a biztonsági mentési adatmennyiséget), a biztonsági mentési adat 14 további napig tart, így a biztonsági mentési elem helyreállítása adatvesztés nélkül történik. A "Soft Delete" állapotban lévő biztonsági mentési adatok megőrzésének további 14 napja nem jár semmilyen költséggel.

Az Azure-beli virtuális gépekre vonatkozó, a Soft delete támogatása mellett az Azure-beli virtuális gépeken SQL Server és SAP HANA munkaterhelések is védve vannak a Soft delete használatával.

További információ: az [Azure virtuális gépen futó SQL Server rendszerhez készült Soft delete és SAP HANA az Azure-beli virtuális](soft-delete-sql-saphana-in-azure-vm.md)gépek számítási feladataihoz.

## <a name="next-steps"></a>Következő lépések

- [Azure Backup útmutató és ajánlott eljárások](guidance-best-practices.md)

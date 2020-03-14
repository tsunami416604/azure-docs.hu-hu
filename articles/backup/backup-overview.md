---
title: Mi az az Azure Backup?
description: Áttekintést nyújt a Azure Backup szolgáltatásról, valamint arról, hogy miként járul hozzá az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához.
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240295"
---
# <a name="what-is-the-azure-backup-service"></a>Mi a Azure Backup szolgáltatás?

Az Azure Backup szolgáltatás egyszerű, biztonságos és költséghatékony megoldásokat kínál adatai biztonsági mentéséhez és a Microsoft Azure-felhőből történő helyreállításához.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Mire használhatom a biztonsági mentést?

- **Helyszíni biztonsági** mentés fájlok, mappák, rendszerállapotok a [Microsoft Azure Recovery Services (MARS) ügynök](backup-support-matrix-mars-agent.md)használatával. Vagy használja a DPM vagy a Azure Backup Server (MABS) ügynököt a helyszíni virtuális gépek ([Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) és [VMware](backup-azure-backup-server-vmware.md)) és más helyszíni [munkaterhelések](backup-mabs-protection-matrix.md) elleni védelemhez
- Az Azure-beli **virtuális gépek** [teljes Windows-/Linux-alapú virtuális gépekről](backup-azure-vms-introduction.md) (biztonsági mentési bővítmények használatával) vagy fájlok, mappák és rendszerállapot biztonsági mentését - a [Mars-ügynök](backup-azure-manage-mars.md)használatával.
- **Azure Files megosztások** - [Azure-fájlmegosztás biztonsági mentése egy Storage-fiókba](backup-afs.md)
- **SQL Server Azure-beli virtuális gépeken** -  Azure-beli [virtuális gépeken futó SQL Server-adatbázisok biztonsági mentése](backup-azure-sql-database.md)
- SAP HANA Azure-beli virtuális gépeken **található adatbázisok** - [biztonsági mentési SAP HANA Azure-beli virtuális gépeken futó adatbázisok](backup-azure-sap-hana-database.md)

![Azure Backup áttekintése](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Miért érdemes az Azure Backupot használni?

A Azure Backup a következő kulcsfontosságú előnyöket nyújtja:

- Helyszíni **biztonsági mentés kiszervezése**: Azure Backup egyszerű megoldást kínál a helyszíni erőforrások felhőbe történő biztonsági mentéséhez. Rövid és hosszú távú biztonsági mentést készíthet, anélkül, hogy bonyolult, helyszíni biztonsági mentési megoldásokat kellene üzembe helyeznie.
- **Azure IaaS virtuális gépek biztonsági mentése**: Azure Backup független és elkülönített biztonsági mentéseket biztosít az eredeti adatvesztés elleni védelemhez. A biztonsági mentések a helyreállítási pontok beépített felügyeletét biztosító Recovery Services-tárolóban tárolódnak. A konfiguráció és a méretezhetőség egyszerű, a biztonsági mentések optimalizáltak, és szükség esetén egyszerűen visszaállíthatók.
- **Könnyen méretezhető** – a Azure Backup az Azure-felhő mögöttes teljesítményének és korlátlan skálájának használatával biztosítja a magas rendelkezésre állást karbantartási vagy figyelési terhelés nélkül.
- **Korlátlan adatátviteli**lehetőség: Azure Backup nem korlátozza az átvitt bejövő vagy kimenő adatok mennyiségét, vagy a továbbított adatok díját.
  - Kimenő adatokon a Recovery Services-tárolóból a visszaállítási művelet során átvitt adatok értendők.
  - Ha az Azure import/export szolgáltatással nagy mennyiségű adat importálására szolgáló offline kezdeti biztonsági mentést hajt végre, akkor a rendszer a bejövő adatokhoz kapcsolódó költségeket is felhasználja.  [További információk](backup-azure-backup-import-export.md).
- **Adatbiztonság megőrzése**: Azure Backup biztosít olyan megoldásokat, amelyekkel biztonságossá teheti az adatátvitelt és [a nyugalmi](backup-azure-security-feature-cloud.md) [állapotban lévő](backup-azure-security-feature.md) adatok védelmét.
- **Központosított figyelés és felügyelet**: a Azure Backup [beépített monitorozási és riasztási képességeket](backup-azure-monitoring-built-in-monitor.md) biztosít egy Recovery Services-tárolóban. Ezek a képességek további felügyeleti infrastruktúra nélkül érhetők el. A figyelés és a jelentéskészítés méretezését a [Azure monitor használatával](backup-azure-monitoring-use-azuremonitor.md)is növelheti.
- Alkalmazás **-konzisztens biztonsági másolatok beolvasása**: az alkalmazás-konzisztens biztonsági mentés azt jelenti, hogy egy helyreállítási pont rendelkezik az összes szükséges adattal a biztonsági másolat visszaállításához. A Azure Backup az alkalmazással konzisztens biztonsági mentéseket biztosít, amelyek biztosítják, hogy az adatokat nem kell visszaállítani további javításokkal. Az alkalmazáskonzisztens adatok visszaállítása rövidebb idő alatt végrehajtható, így gyorsan visszatérhet egy működőképes állapotba.
- **Rövid és hosszú távú adatok megőrzése**: [Recovery Services](backup-azure-recovery-services-vault-overview.md) -tárolókat használhat a rövid és hosszú távú adatmegőrzéshez.
- **Automatikus tárolófelügyelet** – A hibrid környezetek gyakran megkövetelik a heterogén tárhely alkalmazását – valamennyi tárhely szükséges a helyszínen, valamennyi a felhőben. Azure Backup esetén a helyszíni tárolóeszközök használata díjmentes. Az Azure Backup automatikusan foglalja le és kezeli a Backup-tárolót, és használatalapú modellt alkalmaz. Így csak a felhasznált tárterületért kell fizetnie. [További](https://azure.microsoft.com/pricing/details/backup) információ a díjszabásról.
- **Több tárolási lehetőség** – a Azure Backup kétféle replikálási lehetőséget kínál a tárterület/az adatmennyiség nagyfokú rendelkezésre állásának megőrzésére.
  - A [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) háromszor replikálja az adatait (az adatait három példányban hozza létre) egy adatközpont tárolási méretezési egységében. Az adatok összes másolata ugyanabban a régióban található. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén.
  - A [geo-redundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md) az alapértelmezett és ajánlott replikációs lehetőség. A GRS az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől). A GRS több mint LRS, de a GRS magasabb szintű tartósságot biztosít az adataihoz, még akkor is, ha van regionális leállás.

## <a name="next-steps"></a>Következő lépések

- [Tekintse át](backup-architecture.md) a különböző biztonsági mentési forgatókönyvek architektúráját és összetevőit.
- [Ellenőrizze](backup-support-matrix.md) a biztonsági mentés támogatási követelményeit és korlátozásait, valamint az [Azure virtuális gépek biztonsági mentését](backup-support-matrix-iaas.md).

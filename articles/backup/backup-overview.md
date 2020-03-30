---
title: Mi az az Azure Backup?
description: Áttekintést nyújt az Azure Backup szolgáltatás, és hogyan járul hozzá az üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégia.
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240295"
---
# <a name="what-is-the-azure-backup-service"></a>Mi az Azure Backup szolgáltatás?

Az Azure Backup szolgáltatás egyszerű, biztonságos és költséghatékony megoldásokat kínál adatai biztonsági mentéséhez és a Microsoft Azure-felhőből történő helyreállításához.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Mit tudok biztonsági másolatot csinálni?

- **Helyszíni** – Fájlok, mappák, rendszerállapot biztonsági mentése a [Microsoft Azure Recovery Services (MARS) ügynök](backup-support-matrix-mars-agent.md)használatával. Vagy használja a DPM vagy az Azure Backup Server (MABS) ügynök védelme helyszíni virtuális gépek ([Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) és [VMWare](backup-azure-backup-server-vmware.md)) és egyéb [helyszíni számítási feladatok](backup-mabs-protection-matrix.md)
- **Az Azure virtuális gépei** - [biztonsági másolatot készítanek a teljes Windows/Linux virtuális gépekről](backup-azure-vms-introduction.md) (biztonsági mentési bővítmények használatával), vagy biztonsági másolatot készíthetnek a fájlokról, mappákról és rendszerállapotról a [MARS-ügynök](backup-azure-manage-mars.md)használatával.
- **Az Azure Files megosztások** - [Az Azure File-megosztások biztonsági és biztonsági tartaléka tárfiókba](backup-afs.md)
- **SQL Server az Azure-beli virtuális gépekben** -  [Az Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentése](backup-azure-sql-database.md)
- **SAP HANA-adatbázisok az Azure-beli virtuális gépek** - [biztonsági másolatának biztonsági másolatában az SAP HANA-adatbázisokban, amelyek Azure-beli virtuális gépeken futnak](backup-azure-sap-hana-database.md)

![Az Azure biztonsági mentés áttekintése](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Miért érdemes az Azure Backupot használni?

Az Azure Backup a következő legfontosabb előnyöket nyújtja:

- **A helyszíni biztonsági mentés kiürítése:** Az Azure Backup egyszerű megoldást kínál a helyszíni erőforrások felhőbe való biztonsági mentéséhez. Rövid és hosszú távú biztonsági mentést kaphat anélkül, hogy összetett helyszíni biztonsági mentési megoldásokat kellene üzembe helyeznie.
- **Az Azure IaaS virtuális gépek biztonsági mentése:** Az Azure Backup független és elkülönített biztonsági mentéseket biztosít az eredeti adatok véletlen megsemmisítése ellen. A biztonsági mentések a helyreállítási pontok beépített kezelésével rendelkező Recovery Services-tárolóban tárolódnak. A konfiguráció és a méretezhetőség egyszerű, a biztonsági mentések optimalizáltak, és szükség szerint könnyen visszaállítható.
- **Egyszerűen skálázhatja** – Az Azure Backup az Azure-felhő alapjául szolgáló teljesítményt és korlátlan skálát használja a magas rendelkezésre állás biztosításához karbantartás és felügyelet nélkül.
- **Korlátlan adatátvitel beszerzése:** Az Azure Backup nem korlátozza az átvitt vagy kimenő adatok mennyiségét, és nem számít fel díjat az átvitt adatokért.
  - Kimenő adatokon a Recovery Services-tárolóból a visszaállítási művelet során átvitt adatok értendők.
  - Ha az Azure importálási/exportálási szolgáltatás használatával offline kezdeti biztonsági mentést hajt végre nagy mennyiségű adat importálásához, a bejövő adatokhoz kapcsolódó költségek merülnek fel.  [További információ](backup-azure-backup-import-export.md).
- **Az adatok biztonságának megőrzéséhez**: Az Azure Backup megoldásokat kínál [az adatok átvitel közbeni](backup-azure-security-feature.md) és nyugalmi állapotban történő [védelmére.](backup-azure-security-feature-cloud.md)
- **Központosított figyelés és felügyelet:** Az Azure Backup [beépített figyelési és riasztási lehetőségeket](backup-azure-monitoring-built-in-monitor.md) biztosít a Recovery Services-tárolóban. Ezek a képességek további felügyeleti infrastruktúra nélkül érhetők el. Az [Azure Monitor használatával](backup-azure-monitoring-use-azuremonitor.md)is növelheti a figyelés és a jelentéskészítés méretét.
- **Alkalmazáskonzisztens biztonsági mentések:** Alkalmazáskonzisztens biztonsági mentés azt jelenti, hogy a helyreállítási pont rendelkezik az összes szükséges adattal a biztonsági másolat visszaállításához. Az Azure Backup alkalmazáskonzisztens biztonsági mentéseket biztosít, amelyek biztosítják, hogy az adatok visszaállításához nincs szükség további javításokra. Az alkalmazáskonzisztens adatok visszaállítása rövidebb idő alatt végrehajtható, így gyorsan visszatérhet egy működőképes állapotba.
- **Rövid és hosszú távú adatok megőrzése**: A [Recovery Services-tárolók](backup-azure-recovery-services-vault-overview.md) at rövid és hosszú távú adatmegőrzéshez használhatja.
- **Automatikus tárolófelügyelet** – A hibrid környezetek gyakran megkövetelik a heterogén tárhely alkalmazását – valamennyi tárhely szükséges a helyszínen, valamennyi a felhőben. Az Azure Backup használatával a helyszíni tárolóeszközök használata nem jár költséggel. Az Azure Backup automatikusan foglalja le és kezeli a Backup-tárolót, és használatalapú modellt alkalmaz. Tehát csak a felhasznált tárhelyért kell fizetnie. [További információ](https://azure.microsoft.com/pricing/details/backup) az árképzésről.
- **Többtárolási lehetőség** – Az Azure Backup kétféle replikációt kínál a tárhely/adat magas rendelkezésre állásának megőrzéséhez.
  - [A helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) háromszor replikálja az adatokat (három másolatot hoz létre az adatokból) egy adatközpont tárolóméretezési egységében. Az adatok összes másolata ugyanabban a régióban található. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén.
  - [Georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md) az alapértelmezett és ajánlott replikációs lehetőség. A GRS az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől). A GRS többe kerül, mint az LRS, de a GRS nagyobb tartósságot biztosít az adatok számára, még akkor is, ha regionális kimaradás van.

## <a name="next-steps"></a>További lépések

- [Tekintse át](backup-architecture.md) az architektúrát és az összetevőket a különböző biztonsági mentési forgatókönyvekhez.
- [Ellenőrizze](backup-support-matrix.md) a biztonsági mentés és az [Azure VM biztonsági mentéstámogatási követelményeit és korlátozásait.](backup-support-matrix-iaas.md)

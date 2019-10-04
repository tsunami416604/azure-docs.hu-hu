---
title: Azure Backup Server és DPM – gyakori kérdések
description: 'Válaszok a gyakori kérdésekre a következővel kapcsolatban: A Azure Backup Server és a DPM.'
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 16cf4bcb6d83d22417d83d0b20ee564431f82840
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689340"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server és DPM – gyakori kérdések
Ez a cikk a Azure Backup Server és a DPM kapcsolatos gyakori kérdésekre ad választ.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Használhatom az Azure Backup kiszolgálót egy operációs rendszer nélküli helyreállítás (BMR) biztonsági másolatának létrehozásához egy fizikai kiszolgálón? <br/>
Igen.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Regisztrálhatom a kiszolgálót több tárba?
Nem. Egy DPM vagy Azure Backup-kiszolgáló csak egy tárolóban regisztrálható.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Használhatom a DPM az alkalmazások biztonsági mentését Azure Stack-ben?
Nem. A Azure Backup használatával biztosíthatja a Azure Stackt, Azure Backup nem támogatja a DPM használatát az alkalmazások Azure Stack-beli biztonsági mentéséhez.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Ha telepítettem Azure Backup ügynököt a fájlok és mappák védeleméhez, telepíthetem a System Center DPM-t a helyszíni számítási feladatok Azure-ba való biztonsági mentésére?
Igen. Először is be kell állítania a DPM, majd telepítenie kell a Azure Backup ügynököt.  Az összetevők ebben a sorrendben történő telepítése biztosítja, hogy a Azure Backup ügynök együttműködik a DPM. Az ügynök telepítése a DPM telepítése előtt nem javasolt vagy nem támogatott.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Miért nem lehet külső DPM-kiszolgálót felvenni a HASZNÁLNA 7 és a legújabb Azure Backup ügynök telepítése után?
A felhőben védett adatforrásokkal rendelkező DPM-kiszolgálók esetében (a 7. kumulatív frissítésnél korábbi kumulatív frissítés használatával) a HASZNÁLNA 7 és a legújabb Azure Backup ügynök telepítése után meg kell várnia a **külső DPM-kiszolgáló hozzáadásának**megkezdését. Az egynapos időszak szükséges ahhoz, hogy feltöltse a DPM védelmi csoportok metaadatait az Azure-ba. A védelmi csoport metaadatait a rendszer első alkalommal feltölti egy éjszakai feladattal.

## <a name="vmware-and-hyper-v-backup"></a>VMware és Hyper-V biztonsági mentés

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Tudok biztonsági mentést készíteni VMware vCenter-kiszolgálókról az Azure-ban?
Igen. A Azure Backup Server használatával biztonsági mentést készíthet VMware vCenter Server és ESXi-gazdagépekről az Azure-ba.

- [További](backup-mabs-protection-matrix.md) információ a támogatott verziókról.
- [](backup-azure-backup-server-vmware.md) A VMware-kiszolgáló biztonsági mentését a következő lépésekkel végezheti el.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Külön licencre van szükségem egy teljes helyszíni VMware/Hyper-V fürt helyreállításához?
Nincs szükség külön licencelésre a VMware/Hyper-V-védelemhez.

- Ha Ön a System Center-ügyfél, használja a System Center Data Protection Manager (DPM) szolgáltatást a VMware virtuális gépek elleni védelemhez.
- Ha nem a System Center-ügyfél, használhatja a Azure Backup Server (utólagos elszámolású) lehetőséget a VMware virtuális gépek elleni védelemhez.


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Helyreállítható egy SharePoint-elem az eredeti helyre, ha a SharePoint az SQL AlwaysOn használatával van konfigurálva (a lemezes védelemmel)?
Igen, az elem visszaállítható az eredeti SharePoint-webhelyre.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Helyre lehet állítani egy SharePoint-adatbázist az eredeti helyre, ha a SharePoint az SQL AlwaysOn használatával van konfigurálva?
Mivel a SharePoint-adatbázisok az SQL-AlwaysOn vannak konfigurálva, csak akkor módosíthatók, ha a rendelkezésre állási csoport el lett távolítva. Ennek eredményeképpen a DPM nem tudja visszaállítani az adatbázist az eredeti helyére. SQL Server-adatbázist egy másik SQL Server-példányra is helyreállíthatja.

## <a name="next-steps"></a>További lépések

További gyakori kérdések:

- [További](backup-support-matrix-mabs-dpm.md) információ a Azure Backup Server és a DPM támogatási mátrixáról.
- [További](backup-azure-mabs-troubleshoot.md) információ a Azure Backup Server és a DPM hibaelhárítási irányelveiről.

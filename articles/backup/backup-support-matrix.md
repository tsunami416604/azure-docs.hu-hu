---
title: Az Azure Backup támogatási mátrixa
description: Provides a summary of support settings and limitations for the Azure Backup service.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: c424b905f44d6fd3a80a08ef925df4d0a06dd7d8
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705377"
---
# <a name="support-matrix-for-azure-backup"></a>Support matrix for Azure Backup

You can use [Azure Backup](backup-overview.md) to back up data to the Microsoft Azure cloud platform. This article summarizes the general support settings and limitations for Azure Backup scenarios and deployments.

Other support matrices are available:

- Support matrix for [Azure virtual machine (VM) backup](backup-support-matrix-iaas.md)
- Support matrix for backup by using [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Support matrix for backup by using the [Microsoft Azure Recovery Services (MARS) agent](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Vault support

Azure Backup uses Recovery Services vaults to orchestrate and manage backups. It also uses vaults to store backed-up data.

The following table describes the features of Recovery Services vaults:

**Funkció** | **Részletek**
--- | ---
**Vaults in subscription** | Up to 500 Recovery Services vaults in a single subscription.
**Machines in a vault** | Up to 1,000 Azure VMs in a single vault.<br/><br/> Up to 50 MABS servers can be registered in a single vault.
**Data sources in vault storage** | Maximum 54,400 GB. There's no limit for Azure VM backups.
**Backups to vault** | **Azure VMs:** Once a day.<br/><br/>**Machines protected by DPM/MABS:** Twice a day.<br/><br/> **Machines backed up directly by using the MARS agent:** Three times a day.
**Backups between vaults** | Backup is within a region.<br/><br/> You need a vault in every Azure region that contains VMs you want to back up. You can't back up to a different region.
**Move vaults** | You can [move vaults](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) across subscriptions or between resource groups in the same subscription. However, moving vaults across regions isn’t supported.
**Move data between vaults** | Moving backed-up data between vaults isn't supported.
**Modify vault storage type** | You can modify the storage replication type (either geo-redundant storage or locally redundant storage) for a vault before backups are stored. After backups begin in the vault, the replication type can't be modified.

## <a name="on-premises-backup-support"></a>On-premises backup support

Here's what's supported if you want to back up on-premises machines:

**Gép** | **What's backed up** | **Hely** | **Szolgáltatások**
--- | --- | --- | ---
**Direct backup of Windows machine with MARS agent** | Fájlok, mappák, rendszerállapot | Back up to Recovery Services vault. | Back up three times a day<br/><br/> No app-aware backup<br/><br/> Restore file, folder, volume
**Direct backup of Linux machine with MARS agent** | Backup not supported
**Back up to DPM** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Back up to local DPM storage. A DPM ezután biztonsági mentést készít a tárból. | App-aware snapshots<br/><br/> Full granularity for backup and recovery<br/><br/> Linux supported for VMs (Hyper-V/VMware)<br/><br/> Oracle not supported
**Back up to MABS** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Back up to MABS local storage. A MABS ezután biztonsági mentést készít a tárba. | App-aware snapshots<br/><br/> Full granularity for backup and recovery<br/><br/> Linux supported for VMs (Hyper-V/VMware)<br/><br/> Oracle not supported

## <a name="azure-vm-backup-support"></a>Azure VM backup support

### <a name="azure-vm-limits"></a>Azure VM limits

**Korlát** | **Részletek**
--- | ---
**Azure VM data disks** | Limit of 16 <br> Ha regisztrálni szeretne a 16-nál több (legfeljebb 32) adatlemezzel rendelkező virtuális gépek privát előzetes verziójára, írjon nekünk a következő címre: AskAzureBackupTeam@microsoft.com
**Azure VM data disk size** | Individual disk size can be up to 32 TB and a maximum of 256 TB combined for all disks in a VM.

### <a name="azure-vm-backup-options"></a>Azure VM backup options

Here's what's supported if you want to back up Azure VMs:

**Gép** | **What's backed up** | **Hely** | **Szolgáltatások**
--- | --- | --- | ---
**Azure VM backup by using VM extension** | Entire VM | Biztonsági mentés a tárba. | Extension installed when you enable backup for a VM.<br/><br/> Back up once a day.<br/><br/> App-Aware Backup a Windows rendszerű virtuális gépekhez; fájl-konzisztens biztonsági mentés Linux rendszerű virtuális gépekhez. A Linux rendszerű gépekhez egyéni parancsfájlok használatával is konfigurálhatja az alkalmazások konzisztenciáját.<br/><br/> Virtuális gép vagy lemez visszaállítása.<br/><br/> Nem lehet biztonsági mentést készíteni egy Azure-beli virtuális gépről egy helyszíni helyre.
**Azure virtuális gépek biztonsági mentése a MARS-ügynök használatával** | Fájlok, mappák, rendszerállapot | Biztonsági mentés a tárba. | Naponta három alkalommal készíthet biztonsági másolatot.<br/><br/> Ha a teljes virtuális gép helyett adott fájlokról vagy mappákról szeretne biztonsági másolatot készíteni, a MARS-ügynök a virtuálisgép-bővítmény mellett is futhat.
**Azure-beli virtuális gép DPM** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági mentés a DPM-t futtató Azure-beli virtuális gép helyi tárolójába. A DPM ezután biztonsági mentést készít a tárból. | Az alkalmazással kompatibilis Pillanatképek.<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége.<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware).<br/><br/> Az Oracle nem támogatott.
**Azure-beli virtuális gép MABS** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági mentés a MABS-t futtató Azure-beli virtuális gép helyi tárolójába. A MABS ezután biztonsági mentést készít a tárba. | Az alkalmazással kompatibilis Pillanatképek.<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége.<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware).<br/><br/> Az Oracle nem támogatott.

## <a name="linux-backup-support"></a>Linux biztonsági mentési támogatás

Ha a Linux rendszerű gépek biztonsági mentését kívánja végezni, a következők támogatottak:

**Biztonsági mentés típusa** | **Linux (Azure által támogatott)**
--- | ---
**A Linux rendszerű helyszíni gép közvetlen biztonsági mentése** | Nem támogatott. A MARS-ügynököt csak Windows rendszerű gépekre lehet telepíteni.
**Az ügynök-bővítmény használata a Linux rendszerű Azure-beli virtuális gépek biztonsági mentésére** | Alkalmazás-konzisztens biztonsági mentés [egyéni parancsfájlok](backup-azure-linux-app-consistent.md)használatával.<br/><br/> Fájl szintű helyreállítás.<br/><br/> Visszaállítás egy virtuális gép helyreállítási pontról vagy lemezről történő létrehozásával.
**A DPM használata a Linux rendszerű helyszíni vagy Azure-beli virtuális gépek biztonsági mentésére** | A Linux vendég virtuális gépek fájl-konzisztens biztonsági mentése a Hyper-V-ben és a VMWare-en.<br/><br/> A Hyper-V és a VMWare Linux vendég virtuális gépek virtuálisgép-helyreállítása.<br/><br/> A fájl-konzisztens biztonsági mentés nem érhető el az Azure-beli virtuális gépen.
**A MABS használata a Linux rendszerű helyszíni vagy Azure-beli virtuális gépek biztonsági mentésére** | A Linux vendég virtuális gépek fájl-konzisztens biztonsági mentése a Hyper-V-ben és a VMWare-en.<br/><br/> A Hyper-V és a VMWare Linux vendég virtuális gépek virtuálisgép-helyreállítása.<br/><br/> A fájl-konzisztens biztonsági mentés nem érhető el az Azure-beli virtuális gépekhez.

## <a name="daylight-saving-time-support"></a>Nyári időtakarékosság támogatása

Az Azure-beli virtuális gépek biztonsági mentései esetében a Azure Backup nem támogatja az automatikus időzítő beállítását a nyári időszámításhoz. Szükség szerint manuálisan módosítsa a biztonsági mentési házirendeket.

## <a name="disk-deduplication-support"></a>Lemezes deduplikálás támogatása

A lemezes deduplikálás támogatása a következő:

- A lemezek deduplikálása támogatott a helyszínen, ha DPM vagy MABs használ a Windows rendszerű Hyper-V virtuális gépek biztonsági mentésére. A Windows Server az adatok deduplikálása (a gazdagép szintjén) olyan virtuális merevlemezeken (VHD-k) végezhető el, amelyek a biztonsági mentési tárolóként csatlakoznak a virtuális GÉPHEZ.
- A deduplikálás nem támogatott az Azure-ban a biztonsági mentési összetevőkhöz. Ha a DPM és a MABS üzembe helyezése az Azure-ban történik, a virtuális géphez csatolt tárolók nem lehetnek deduplikálva.

## <a name="security-and-encryption-support"></a>Biztonsági és titkosítási támogatás

A Azure Backup támogatja a titkosítást az átvitel közbeni és a nyugalmi adatokhoz.

### <a name="network-traffic-to-azure"></a>Az Azure-ba irányuló hálózati forgalom

- A kiszolgálókról a Recovery Services-tárolóra irányuló biztonsági mentési forgalom titkosítása Advanced Encryption Standard 256 használatával történik.
- Biztonsági mentési adat küldése biztonságos HTTPS-kapcsolaton keresztül történik.
- A biztonsági mentési adatforgalom titkosított formában tárolódik a Recovery Services-tárolóban.
- Csak Ön rendelkezik a jelszóval az adatzárolás feloldásához. A Microsoft nem tudja visszafejteni a biztonsági mentési adatforrásokat.

    > [!WARNING]
    > A tároló beállítása után csak Ön férhet hozzá a titkosítási kulcshoz. A Microsoft soha nem tart fenn másolatot, és nem fér hozzá a kulcshoz. Ha a kulcs rossz helyre került, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.

### <a name="data-security"></a>Adatbiztonság

- Az Azure-beli virtuális gépek biztonsági mentésekor be kell állítania a titkosítást a virtuális gépen *belül* .
- Az Azure Backup támogatja az Azure Disk Encryption szolgáltatást, amely a BitLockert használja Windows rendszerű virtuális gépeken és a **dm-crypt**-et Linux rendszerű virtuális gépeken.
- A háttérben Azure Backup az [Azure Storage Service Encryptiont](../storage/common/storage-service-encryption.md)használja, amely megvédi az inaktív adatok védelmét.

**Gép** | **Átvitel közben** | **Nyugalmi állapotban**
--- | --- | ---
**Helyszíni Windows rendszerű gépek DPM/MABS nélkül** | ![Igen][green] | ![Igen][green]
**Azure-beli virtuális gépek** | ![Igen][green] | ![Igen][green]
**Helyszíni Windows-számítógépek vagy Azure-beli virtuális gépek DPM-mel** | ![Igen][green] | ![Igen][green]
**Helyszíni Windows-számítógépek vagy Azure-beli virtuális gépek MABS-mel** | ![Igen][green] | ![Igen][green]

## <a name="compression-support"></a>Tömörítés támogatása

A Backup a következő táblázatban összefoglalt biztonsági mentési forgalom tömörítését támogatja.

- Az Azure-beli virtuális gépek esetében a virtuálisgép-bővítmény közvetlenül az Azure Storage-fiókból olvassa be az adatokat a tárolási hálózaton keresztül, ezért nem szükséges tömöríteni a forgalmat.
- Ha a DPM-t vagy a MABS-t használja, akkor a biztonsági mentés előtt tömörítheti a sávszélességet.

**Gép** | **Tömörítés a MABS/DPM (TCP)** | **Tömörítés a tárolóba (HTTPS)**
--- | --- | ---
**Helyszíni Windows rendszerű gépek közvetlen biztonsági mentése** | n/a | ![Igen][green]
**Azure-beli virtuális gépek biztonsági mentése virtuálisgép-bővítmény használatával** | n/a | n/a
**Biztonsági mentés helyszíni/Azure-alapú gépeken a MABS/DPM használatával** | ![Igen][green] | ![Igen][green]

## <a name="retention-limits"></a>Megőrzési korlátok

**Beállítás** | **Korlátok**
--- | ---
**Helyreállítási pontok maximális száma védett példányon (gép vagy munkaterhelés)** | 9 999
**Helyreállítási pont maximális lejárati ideje** | Korlátlan
**Maximális biztonsági mentés gyakorisága DPM/MABS** | 15 percenként az SQL Serverhez<br/><br/> Óránként egyszer más számítási feladatokhoz
**Maximális biztonsági mentési gyakoriság a tárolóhoz** | **A Marsot futtató helyszíni Windows-számítógépek vagy Azure-beli virtuális gépek:** Naponta háromszor<br/><br/> **DPM/MABS:** Kettő/nap<br/><br/> **Azure-beli virtuális gép biztonsági mentése:** Naponta egy
**Helyreállítási pont megőrzése** | Napi, heti, havi, éves
**Maximális megőrzési idő** | A biztonsági mentés gyakoriságától függően változik
**Helyreállítási pontok a DPM-vagy MABS-lemezen** | 64 fájlkiszolgálók esetén; 448 alkalmazás-kiszolgálókhoz <br/><br/>Korlátlan szalagos helyreállítási pontok a helyszíni DPM

## <a name="cross-region-restore"></a>Régiók közötti visszaállítás

Azure Backup hozzáadta a régiók közötti visszaállítási szolgáltatást az adatok rendelkezésre állásának és a rugalmassági képesség megerősítéséhez, így az ügyfelek teljes körűen vezérelhetik az adatok másodlagos régióba való visszaállítását. A szolgáltatás konfigurálásához látogasson el [a régióbeli visszaállítás beállítása című cikkre.](backup-create-rs-vault.md#set-cross-region-restore) Ez a funkció a következő felügyeleti típusok esetén támogatott:

| Biztonságimásolat-kezelés típusa | Támogatott                                                    | Támogatott régiók |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Igen. A korlátozott, 4 TB-nál kisebb lemezekkel rendelkező virtuális gépek és virtuális gépek esetében a nyilvános korlátozott előzetes verzió támogatott | USA nyugati középső régiója   |
| MARS-ügynök/helyszíni | Nem                                                           | –               |
| SQL/SAP HANA          | Nem                                                           | –               |
| AFS                    | Nem                                                           | –               |



## <a name="next-steps"></a>Következő lépések

- [Tekintse át](backup-support-matrix-iaas.md) az Azure virtuális gépek biztonsági mentésének támogatási mátrixát.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

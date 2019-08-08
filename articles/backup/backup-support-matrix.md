---
title: Az Azure Backup támogatási mátrixa
description: Összefoglalja a Azure Backup szolgáltatás támogatási beállításait és korlátozásait.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: dacurwin
ms.openlocfilehash: 6b79b34b1db6c201a8f132e4c608e251edb3666a
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68735421"
---
# <a name="support-matrix-for-azure-backup"></a>Azure Backup támogatási mátrixa

A [Azure Backup](backup-overview.md) használatával biztonsági mentést készíthet az Microsoft Azure Cloud platformra. Ez a cikk a Azure Backup forgatókönyvek és központi telepítések általános támogatási beállításait és korlátozásait foglalja össze.

További támogatási mátrixok érhetők el:

- Az [Azure virtuális gép (VM) biztonsági mentésének](backup-support-matrix-iaas.md) támogatási mátrixa
- A biztonsági mentéshez használható mátrix a [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md) használatával
- A biztonsági mentéshez használható mátrix a [Microsoft Azure Recovery Services-(MARS-) ügynök](backup-support-matrix-mars-agent.md) használatával

## <a name="vault-support"></a>Tár támogatása

A Azure Backup Recovery Services-tárolókat használ a biztonsági másolatok előkészítéséhez és kezeléséhez. A tárolókat is használ a biztonsági másolatok tárolásához.

A következő táblázat a Recovery Services-tárolók szolgáltatásait ismerteti:

**Funkció** | **Részletek**
--- | ---
**Tárolók az előfizetésben** | Akár 500 Recovery Services-tároló egyetlen előfizetésben.
**Tárolóban lévő gépek** | Akár 1 000 Azure-beli virtuális gép egyetlen tárolóban.<br/><br/> Akár 50 MABS-kiszolgáló is regisztrálható egyetlen tárolóban.
**Adatforrások a tárolóban** | Maximális 54 400 GB. Az Azure-beli virtuális gépek biztonsági mentései nem megengedettek.
**Biztonsági mentések a tárba** | **Azure-beli virtuális gépek:** Naponta egyszer.<br/><br/>**DPM/MABS által védett gépek:** Naponta kétszer.<br/><br/> **A közvetlenül a MARS-ügynök használatával biztonsági mentést készít a gépekről:** Naponta háromszor.
**Tárolók közötti biztonsági másolatok** | A biztonsági mentés egy régión belül található.<br/><br/> Minden olyan Azure-régióban szüksége van egy tárolóra, amely tartalmazza a biztonsági mentésre használni kívánt virtuális gépeket. Nem lehet biztonsági másolatot készíteni egy másik régióra.
**Tárolók áthelyezése** | A tárolókat áthelyezheti előfizetések között, illetve az azonos előfizetésben található erőforráscsoportok között is. [](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)
**Adatáthelyezés a tárolók között** | A biztonsági másolatok tárolók közötti áthelyezése nem támogatott.
**Tár tárolási típusának módosítása** | A tároló replikációs típusát (vagy a Geo-redundáns tárterületet vagy a helyileg redundáns tárolást) a biztonsági másolatok tárolása előtt módosíthatja. Miután a biztonsági mentések megkezdődnek a tárolóban, a replikálás típusa nem módosítható.

## <a name="on-premises-backup-support"></a>Helyszíni biztonsági mentési támogatás

A következő mi támogatott, ha biztonsági mentést szeretne készíteni a helyszíni gépekről:

**Gép** | **A biztonsági mentés** | **Location** | **Szolgáltatások**
--- | --- | --- | ---
**Windows rendszerű gép közvetlen biztonsági mentése a MARS-ügynökkel** | Fájlok, mappák, rendszerállapot | Biztonsági mentés Recovery Services-tárolóba. | Napi három alkalommal történő biztonsági mentés<br/><br/> Nincs alkalmazás-kompatibilis biztonsági másolat<br/><br/> Fájl, mappa, kötet visszaállítása
**A Linux-gép közvetlen biztonsági mentése a MARS-ügynökkel** | A biztonsági mentés nem támogatott
**Biztonsági mentés a DPM** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági mentés a helyi DPM-tárolóba. A DPM ezután biztonsági mentést készít a tárból. | Alkalmazás-kompatibilis Pillanatképek<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware)<br/><br/> Az Oracle nem támogatott
**Biztonsági mentés a MABS** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági mentés a MABS helyi tárolóba. A MABS ezután biztonsági mentést készít a tárba. | Alkalmazás-kompatibilis Pillanatképek<br/><br/> A biztonsági mentés és a helyreállítás teljes részletessége<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware)<br/><br/> Az Oracle nem támogatott

## <a name="azure-vm-backup-support"></a>Azure-beli virtuális gépek biztonsági mentésének támogatása

### <a name="azure-vm-limits"></a>Azure-beli virtuális gépek korlátai

**Korlát** | **Részletek**
--- | ---
**Azure-beli VM-adatlemezek** | Legfeljebb 16
**Azure-beli virtuális gép adatlemezének mérete** | Az egyes lemezek akár 4 095 GB méretűek is lehetnek

### <a name="azure-vm-backup-options"></a>Azure virtuális gépek biztonsági mentési lehetőségei

Ha az Azure-beli virtuális gépek biztonsági mentését kívánja végezni, a következők támogatottak:

**Gép** | **A biztonsági mentés** | **Location** | **Szolgáltatások**
--- | --- | --- | ---
**Azure virtuális gépek biztonsági mentése virtuálisgép-bővítmény használatával** | Teljes virtuális gép | Biztonsági mentés a tárba. | A bővítmény akkor lett telepítve, ha engedélyezi a virtuális gép biztonsági mentését.<br/><br/> Naponta egyszer készít biztonsági mentést.<br/><br/> App-Aware Backup a Windows rendszerű virtuális gépekhez; fájl-konzisztens biztonsági mentés Linux rendszerű virtuális gépekhez. A Linux rendszerű gépekhez egyéni parancsfájlok használatával is konfigurálhatja az alkalmazások konzisztenciáját.<br/><br/> Virtuális gép vagy lemez visszaállítása.<br/><br/> Nem lehet biztonsági mentést készíteni egy Azure-beli virtuális gépről egy helyszíni helyre.
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

## <a name="compression-support"></a>A tömörítés támogatása

A Backup a következő táblázatban összefoglalt biztonsági mentési forgalom tömörítését támogatja.

- Az Azure-beli virtuális gépek esetében a virtuálisgép-bővítmény közvetlenül az Azure Storage-fiókból olvassa be az adatokat a tárolási hálózaton keresztül, ezért nem szükséges tömöríteni a forgalmat.
- Ha a DPM-t vagy a MABS-t használja, akkor a biztonsági mentés előtt tömörítheti a sávszélességet.

**Gép** | **Tömörítés a MABS/DPM (TCP)** | **Tömörítés a tárolóba (HTTPS)**
--- | --- | ---
**Helyszíni Windows rendszerű gépek közvetlen biztonsági mentése** | NA | ![Igen][green]
**Azure-beli virtuális gépek biztonsági mentése virtuálisgép-bővítmény használatával** | NA | NA
**Biztonsági mentés helyszíni/Azure-alapú gépeken a MABS/DPM használatával** | ![Igen][green] | ![Igen][green]

## <a name="retention-limits"></a>Megőrzési korlátok

**Beállítás** | **Korlátok**
--- | ---
**Helyreállítási pontok maximális száma védett példányon (gép vagy munkaterhelés)** | 9 999
**Helyreállítási pont maximális lejárati ideje** | Korlátlan
**Maximális biztonsági mentés gyakorisága DPM/MABS** | 15 percenként az SQL Serverhez<br/><br/> Óránként egyszer más számítási feladatokhoz
**Maximális biztonsági mentési gyakoriság a tárolóhoz** | **A MARSot futtató helyszíni Windows-számítógépek vagy Azure-beli virtuális gépek:** Naponta háromszor<br/><br/> **DPM/MABS:** Kettő/nap<br/><br/> **Azure-beli virtuális gép biztonsági mentése:** Naponta egy
**Helyreállítási pont megőrzése** | Napi, heti, havi, éves
**Maximális megőrzési idő** | A biztonsági mentés gyakoriságától függően változik
**Helyreállítási pontok a DPM-vagy MABS-lemezen** | 64 fájlkiszolgálók esetén; 448 alkalmazás-kiszolgálókhoz <br/><br/>Korlátlan szalagos helyreállítási pontok a helyszíni DPM

## <a name="next-steps"></a>További lépések

- [Tekintse át](backup-support-matrix-iaas.md) az Azure virtuális gépek biztonsági mentésének támogatási mátrixát.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

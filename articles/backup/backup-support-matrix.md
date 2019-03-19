---
title: Az Azure Backup támogatási mátrixa
description: Támogatási beállításainak és az Azure Backup szolgáltatás korlátozásai összegzését tartalmazza.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 51bd4b935b32bea20d3f5de0b8cda62dfdbf07b8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898720"
---
# <a name="azure-backup-support-matrix"></a>Az Azure Backup támogatási mátrixa

Használhat [Azure Backup](backup-overview.md) az adatok biztonsági mentésére a Microsoft Azure felhőplatformon. Ez a cikk összegzi az általános támogatási beállításait és a korlátozások az Azure Backup-forgatókönyveinek és központi telepítések.

Egyéb támogatási mátrixok érhetők el:

- Támogatási mátrixa [Azure virtuális gépek (VM) biztonsági mentése](backup-support-matrix-iaas.md)
- A biztonsági mentés támogatási mátrixa [System Center Data Protection Manager (DPM) vagy a Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- A biztonsági mentés támogatási mátrixa a [a Microsoft Azure Recovery Services-(MARS) ügynök](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Tároló támogatása

Az Azure Backup Recovery Services-tárolók vezényléséhez és a biztonsági másolatok kezelése a használ. Tárolók mentett adatok tárolására is használ. 

A következő táblázat ismerteti a Recovery Services-tárolók jellemzői:

**Funkció** | **Részletek**
--- | ---
**Tárolók az előfizetés** | Legfeljebb 500 Recovery Services-tárolók egyetlen előfizetéssel.
**Egy tároló gépek** | Legfeljebb 1000 Azure virtuális gépek Ezenkívül egyetlen tárban.<br/><br/> Legfeljebb 50 MABS kiszolgálók Ezenkívül egyetlen tárban lehet regisztrálni.
**Adatforrások tároló storage-ban** | Maximális 54,400 GB. Az Azure virtuális gép biztonsági mentéseinek nincs korlátozva van.
**Biztonsági másolatok a tárba** | **Az Azure virtuális gépek:** Naponta egyszer.<br/><br/>**A DPM/MABS által védett gépek:** Naponta kétszer.<br/><br/> **A MARS-ügynök használatával közvetlenül a gépek biztonsági mentése:** Napi három alkalomnál. 
**Biztonsági másolatok tárolók között** | Biztonsági mentés van egy adott régión belül.<br/><br/> Minden Azure-régióban, amely tartalmazza a készíteni kívánt virtuális gépeket tároló van szüksége. Nem készíthet biztonsági másolatot egy másik régióba. 
**Helyezze át a tárolók** | Is [helyezze át a tárolók](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) előfizetések között, vagy ugyanabban az előfizetésben erőforráscsoportok között.
**Tárolók közötti adatáthelyezés** | Adatok biztonsági tárolók közötti áthelyezése nem támogatott.
**Tároló tárolási típus módosítása** | Módosíthatja a tárolóreplikáció típusa (helyileg redundáns tárolás vagy georedundáns tárolást) tároló, mielőtt biztonsági másolatai. Miután a biztonsági mentések fordulnak a tárolóban, a replikáció típusa nem módosítható.

## <a name="on-premises-backup-support"></a>Támogatja a helyszíni biztonsági mentést

Íme, mi támogatott, ha azt szeretné, a helyszíni gépek biztonsági mentéséhez:

**Gép** | **Milyen biztonsági mentése** | **Hely** | **Szolgáltatások**
--- | --- | --- | ---
**Közvetlen biztonsági mentést a MARS-ügynök Windows-gép** | Fájlok, mappák, a rendszer állapota | Készítsen biztonsági másolatot a Recovery Services-tárolóba. | Napi három alkalomnál készíteni<br/><br/> Nincs alkalmazás együttműködő biztonsági mentés<br/><br/> Fájl, mappa, kötet visszaállítása
**Közvetlen biztonsági mentést a MARS-ügynök Linux-gép** | Biztonsági mentés nem támogatott
**A DPM biztonsági mentés** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok | Készítsen biztonsági másolatot helyi DPM-tároló. A DPM majd biztonsági mentést készít a tárba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Teljes részletességgel biztonsági mentés és helyreállítás<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) támogatják<br/><br/> Nem támogatott Oracle
**Biztonsági mentés a MABS** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok | Készítsen biztonsági másolatot a MABS helyi tárba. MABS majd biztonsági mentést készít a tárolóba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Teljes részletességgel biztonsági mentés és helyreállítás<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) támogatják<br/><br/> Nem támogatott Oracle

## <a name="azure-vm-backup-support"></a>Az Azure virtuális gépek biztonsági mentési támogatása

### <a name="azure-vm-limits"></a>Az Azure virtuális gépekre korlátai

**Korlát** | **Részletek**
--- | ---
**Az Azure virtuális gép adatlemezek** | 16-os korlátot
**Az Azure virtuális gép adatlemez mérete** | Az egyes lemezek legfeljebb 4095 GB is lehet.

### <a name="azure-vm-backup-options"></a>Azure virtuális gép biztonsági mentési lehetőségek

Íme, mi támogatott, ha azt szeretné, az Azure virtuális gépek biztonsági mentése:

**Gép** | **Milyen biztonsági mentése** | **Hely** | **Szolgáltatások**
--- | --- | --- | ---
**Az Azure VM backup Virtuálisgép-bővítmény használatával** | Teljes virtuális gép | Készítsen biztonsági másolatot a tárba. | A bővítmény telepítve van a virtuális gép biztonsági mentésének engedélyezésekor.<br/><br/> Biztonsági mentést naponta egyszer.<br/><br/> App-t támogató biztonsági mentési Windows virtuális gépek; Linux rendszerű virtuális gépek fájlkonzisztens biztonsági mentésre. Alkalmazás-konzisztencia a Linux rendszerű gépek egyéni parancsfájlok segítségével konfigurálhatja.<br/><br/> Virtuális gép vagy lemez visszaállítása.<br/><br/> Nem lehet biztonsági másolatot egy Azure virtuális gép egy helyszíni helyhez.
**Az Azure VM backup MARS-ügynök használatával** | Fájlok és mappák | Készítsen biztonsági másolatot a tárba. | Napi három alkalomnál készíteni.<br/><br/> Ha szeretne biztonsági másolatot készíteni a teljes virtuális gép helyett meghatározott fájlokat vagy mappákat, a MARS-ügynök futtathatja a Virtuálisgép-bővítmény mellett.
**A DPM Azure virtuális Gépen** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok | Készítsen biztonsági másolatot a DPM rendszert futtató Azure virtuális gépek helyi tárba. A DPM majd biztonsági mentést készít a tárba. | Alkalmazás-alkalmazásfüggő pillanatképek.<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/> Oracle nem támogatott.
**Az Azure virtuális Gépet, a MABS** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok | Készítsen biztonsági másolatot a MABS futtató Azure virtuális gépek helyi tárba. MABS majd biztonsági mentést készít a tárolóba. | Alkalmazás-alkalmazásfüggő pillanatképek.<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/> Oracle nem támogatott.

## <a name="linux-backup-support"></a>Biztonsági mentési Linux-támogatás

Íme, mi támogatott, ha azt szeretné, Linux rendszerű gépek biztonsági mentéséhez:

**Biztonsági mentés típusa** | **Linux (Azure által támogatott)**
--- | ---
**Közvetlen biztonsági mentést, Linux rendszert futtató helyszíni gépre** | Nem támogatott. A MARS-ügynök csak Windows gépeken telepíthető.
**Linux rendszert futtató Azure virtuális gép biztonsági másolatának ügynök bővítmény használata** | Az alkalmazáskonzisztens biztonsági mentés [egyéni parancsfájlok](backup-azure-linux-app-consistent.md).<br/><br/> A fájlszintű helyreállítási.<br/><br/> Virtuális gép létrehozása egy helyreállítási pontot vagy a lemez visszaállításához.
**A helyszíni vagy Linux rendszert futtató Azure virtuális gép biztonsági mentése a DPM használatával** | Linux rendszerű Vendég virtuális gépek a Hyper-V és VMWare fájlkonzisztens biztonsági mentésre.<br/><br/> Virtuális gép helyreállítása a Hyper-V és VMWare Linux rendszerű Vendég virtuális gépek.<br/><br/> Azure virtuális gép nem érhető el fájlkonzisztens biztonsági mentésre.
**MABS segítségével helyszíni gépre vagy Linux rendszert futtató Azure virtuális gép biztonsági mentése** | Linux rendszerű Vendég virtuális gépek a Hyper-V és VMWare fájlkonzisztens biztonsági mentésre.<br/><br/> Virtuális gép helyreállítása a Hyper-V és VMWare Linux rendszerű Vendég virtuális gépek.<br/><br/> Az Azure virtuális gépek nem érhető el fájlkonzisztens biztonsági mentésre.

## <a name="daylight-saving-time-support"></a>Nyári időszámítás támogatása

Az Azure Backup nem támogatja az Azure virtuális gép biztonsági mentéseinek nyári időszámítás óra automatikus illesztését. Biztonsági mentési szabályzatok manuálisan szükség szerint módosítsa.

## <a name="disk-deduplication-support"></a>Lemez deduplikációs támogatás

A deduplikáció lemeztámogatás a következőképpen történik:

- Lemezdeduplikáció támogatott helyszíni, a Windows rendszert futtató Hyper-V virtuális gépek biztonsági mentése a DPM- vagy MABs használatakor. A Windows Server az adatok deduplikációját (a gazdagép szintjén) a virtuális merevlemezeket (VHD) a virtuális Géphez backup-tárolóként csatlakoztatott.
- A deduplikáció nem támogatott az Azure-ban egyik Backup-összetevőhöz. Ha a DPM és a MABS az Azure-ban van telepítve, a virtuális géphez csatolt tárolólemezek nem deduplikálhatók.

## <a name="security-and-encryption-support"></a>Biztonság és titkosítás támogatása

Az Azure Backup támogatja a titkosítás az átvitel és inaktív adatok.

### <a name="network-traffic-to-azure"></a>Hálózati forgalom az Azure-bA

- A Recovery Services-tárolóba kiszolgálók biztonsági mentési érkező az Advanced Encryption Standard 256 eljárással van titkosítva.
- Biztonsági mentési adatok egy biztonságos HTTPS-kapcsolaton keresztül zajlik.
- Biztonsági mentési adatokat a Recovery Services-tárolót, titkosított formában tárolja.
- Csak Ön rendelkezik az adatok zárolásának feloldására szolgáló jelszóval. A Microsoft nem tudja visszafejteni a biztonsági mentési adatokat.

    > [!WARNING]
    > Miután beállította a tárolót, csak Önnek lesz hozzáférése a titkosítási kulcshoz. A Microsoft soha nem őriz másolatot, és nem rendelkezik hozzáféréssel a kulcshoz. Ha az ügyfél elveszíti a kulcsot, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.

### <a name="data-security"></a>Adatbiztonság

- Ha biztonsági mentése Azure virtuális gépek, állítsa be a titkosítási kell *belül* a virtuális gépet.
- Az Azure Backup támogatja az Azure Disk Encryption szolgáltatást, amely a BitLockert használja Windows rendszerű virtuális gépeken és a **dm-crypt**-et Linux rendszerű virtuális gépeken.
- A háttérben az Azure Backup használja [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), amely védi az inaktív adatok.

**Gép** | **Az átvitel során** | **Inaktív állapotban**
--- | --- | ---
**A helyi Windows számítógépek DPM/MABS nélkül** | ![Igen][green] | ![Igen][green]
**Az Azure virtuális gépek** | ![Igen][green] | ![Igen][green]
**A helyi Windows-számítógépek vagy az Azure-beli virtuális gépek DPM-mel** | ![Igen][green] | ![Igen][green]
**A helyi Windows-számítógépek vagy az Azure-beli virtuális gépek a MABS** | ![Igen][green] | ![Igen][green]

## <a name="compression-support"></a>A tömörítés támogatása

Backup támogatja a tömörítést, a biztonsági mentési forgalom, a következő táblázat foglalja össze.

- Az Azure virtuális gépek a Virtuálisgép-bővítmény olvassa be az adatokat közvetlenül az Azure storage-fiókot a tárolóhálózaton keresztül, nem kell tömöríteni a forgalmat.
- Ha a DPM- vagy MABS használja, mentheti a sávszélesség tömörítésével az adatokat, mielőtt biztonsági mentését.

**Gép** | **Tömörítse a MABS és DPM (TCP)** | **A tároló (HTTPS) tömörítése**
--- | --- | ---
**Közvetlen biztonsági mentést, a helyi Windows-számítógépek** | NA | ![Igen][green]
**Virtuálisgép-bővítmény használatával Azure virtuális gépek biztonsági mentése** | NA | NA
**Helyszíni/Azure gépeken MABS/DPM használatával biztonsági mentést** | ![Igen][green] | ![Igen][green]

## <a name="retention-limits"></a>Adatmegőrzési korlátok

**Beállítás** | **Korlátok**
--- | ---
**Helyreállítási pontok maximális száma a védett példányonként (gép vagy számítási feladat)** | 9,999
**Helyreállítási pontok maximális száma lejárati idő** | Korlátlan
**A DPM/MABS maximális biztonsági mentés gyakorisága** | 15 percenként az SQL Serverhez<br/><br/> Más számítási feladatok óránként egyszer
**Maximális biztonsági mentés gyakorisága a tárba** | **A helyi Windows-gépeket vagy MARS futó Azure virtuális gépek:** Három naponta<br/><br/> **DPM/MABS:** Két, naponként<br/><br/> **Az Azure virtuális gép biztonsági mentése:** Egy / nap
**Helyreállítási pont megőrzése** | Napi, heti, havi, éves
**Maximális megőrzési időtartam** | A biztonsági mentés gyakoriságától függően változik
**A DPM/MABS lemezen helyreállítási pontok** | 64 fájlkiszolgálókhoz; alkalmazás-kiszolgálók 448 <br/><br/>Korlátlan számú szalagos helyreállítási pontok a helyszíni DPM-hez

## <a name="next-steps"></a>További lépések

- [Tekintse át a támogatási mátrix](backup-support-matrix-iaas.md) Azure VM backup esetében.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

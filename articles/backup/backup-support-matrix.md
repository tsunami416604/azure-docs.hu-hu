---
title: Az Azure biztonsági mentés támogatási mátrixa
description: Támogatási beállításainak és az Azure Backup szolgáltatás korlátozásai összegzését tartalmazza.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b1fa723863e6485e977e075986c3779efed1e689
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360225"
---
# <a name="azure-backup-support-matrix"></a>Az Azure biztonsági mentés támogatási mátrixa

Használhatja a [Azure Backup szolgáltatás](backup-overview.md) a biztonsági másolatokat a Microsoft Azure felhőbe. Ez a cikk összefoglalja a támogatási beállításainak és az Azure Backup-forgatókönyveinek és központi telepítések korlátozások.

## <a name="vault-support"></a>Tároló támogatása

Azure biztonsági mentések az Recovery Services-tárolók vezényléséhez és a biztonsági másolatok kezelése a, és tárolja a biztonsági másolat.

**Beállítás** | **Részletek**
--- | ---
Tárolók száma | Legfeljebb 500 Recovery Services-tárolók egyetlen előfizetéssel.
Egy tároló gépek | Legfeljebb 1000 Azure virtuális gépek Ezenkívül egyetlen tárban.<br/><br/> Legfeljebb 50 helyszíni gépeken az Azure Backup ügynököt (Microsoft Azure Recovery Services agent (MABS)) regisztrálni lehet egy közös tároló.
Az adatforrás tároló storage-ban | Maximális 54400 GB. Az Azure virtuális gép biztonsági mentéseinek nincs korlátozva van.
Biztonsági másolatok a tárba | Az Azure virtuális gépek: egyszer a napra. A DPM/MABS által védett gépek: naponta kétszer; Gépek biztonsági mentése közvetlenül a MARS-ügynök használatával: napi háromszori.  
Tár áthelyezése | Biztonsági mentés a Recovery Services-tárolók helyezheti át a különböző előfizetésekhez és erőforráscsoportokhoz. [További információk](backup-azure-move-recovery-services-vault.md).
Tárolók közötti adatáthelyezés | Az adatok biztonsági másolatát tároló közötti áthelyezése nem támogatott.
Tárolóreplikáció típusa | Módosíthatja a tárolóreplikáció típusa (GRS vagy LRS) egy tároló előtt biztonsági másolatai. Miután a biztonsági mentések fordulnak a tárolóban, a replikáció típusa nem módosítható.



## <a name="on-premises-backup-support"></a>Támogatja a helyszíni biztonsági mentést 

Íme, mi támogatott, ha azt szeretné, a helyszíni gépek biztonsági mentéséhez.

**Gép** | **Hely** | **Biztonsági mentés** | **Szolgáltatások**
--- | --- | --- | ---
**Windows fizikai/virtuális (nincs biztonsági mentési kiszolgálóra)** | Fájlok, mappák, a rendszer állapota | Biztonsági másolat a Recovery services-tároló | A biztonsági mentés napi háromszori.<br/><br/> Nincs alkalmazás együttműködő biztonsági mentés.<br/><br/> A visszaállítási fájl, mappa, kötet.
**Linux rendszerű fizikai vagy virtuális (nincs biztonsági mentési kiszolgálóra)** | A biztonsági mentés nem támogatott.
**Fizikai/virtuális DPM-mel** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok. | Biztonsági másolat a dpm-hez (a DPM-kiszolgálón, vagy szalagra helyileg csatlakoztatott lemez.<br/><br/> A DPM majd biztonsági mentést készít a tárba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/>. Oracle nem támogatott.
**Fizikai a MABS virtuális** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok. | Biztonsági másolat a MABS (a MABS kiszolgálóhoz helyileg csatlakoztatott lemez. Szalag nem támogatott.<br/><br/> MABS majd biztonsági mentést készít a tárolóba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/>. Oracle nem támogatott.


## <a name="azure-vms"></a>Azure-beli virtuális gépek

### <a name="azure-vm-limits"></a>Az Azure virtuális gépekre korlátai

**Korlát** | **Részletek**
--- | ---
Az Azure virtuális gép adatlemezek | 16-os korlát.
Az Azure virtuális gép adatlemez mérete | Az egyes lemezek legfeljebb 4095 GB is lehet.


### <a name="azure-vm-backup-options"></a>Azure virtuális gép biztonsági mentési lehetőségek

Íme, mi támogatott, ha azt szeretné, az Azure virtuális gépek biztonsági mentése.

**Gép** | **Hely** | **Biztonsági mentés** | **Szolgáltatások**
--- | --- | --- | ---
**Az Azure virtuális gépek (nincs biztonsági mentési kiszolgálóra)** | Fájlok, mappák, a rendszer állapota | Biztonsági másolat a tárba. | A biztonsági mentés naponta egyszer.<br/><br/> App-t támogató biztonsági mentési Windows virtuális gépek, Linux rendszerű virtuális gépek fájlkonzisztens biztonsági mentésre. Alkalmazás-konzisztencia a Linux rendszerű gépek egyéni parancsfájlok segítségével konfigurálhatja.<br/><br/> Virtuálisgép-lemez visszaállítása.<br/><br/> Nem lehet biztonsági mentése Azure virtuális gépen a helyszíni helyre.
**A DPM Azure virtuális Gépen** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok. | Biztonsági másolat a DPM rendszert futtató Azure-ban (egy a DPM-kiszolgáló helyileg csatlakoztatott lemez. Szalag nem támogatott.<br/><br/> A DPM majd biztonsági mentést készít a tárba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/>. Oracle nem támogatott.
**Az Azure virtuális Gépet, a MABS** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok. | Biztonsági másolat (a MABS kiszolgálóhoz helyileg csatlakoztatott lemez) Azure-ban futó MABS. Szalag nem támogatott.<br/><br/> MABS majd biztonsági mentést készít a tárolóba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/>. Oracle nem támogatott.





## <a name="linux-backup-support"></a>Biztonsági mentési Linux-támogatás

Íme, mi támogatott, ha azt szeretné, Linux rendszerű gépek biztonsági mentéséhez.

**Biztonsági mentés** | **Linux (Azure által támogatott)**
--- | --- 
**A helyszíni Linuxos gép (nem a DPM- vagy MABS)**. | Nem. A MARS-ügynök csak Windows-gépeken telepíthető. 
**Az Azure virtuális gép (nem a DPM- vagy MABS)** | Alkalmazáskonzisztens biztonsági mentés használatával [egyéni parancsfájlok](backup-azure-linux-app-consistent.md).<br/><br/> A fájlszintű helyreállítási.<br/><br/> Virtuális gép létrehozása egy helyreállítási pontot vagy a lemez visszaállításához.
**A helyszíni gépek vagy az Azure virtuális gép a dpm-mel** | Hyper-V és VMware virtuális gépek Linux rendszerű vendég virtuális gépeinek fájlkonzisztens biztonsági mentése<br/><br/> Hyper-V és VMware virtuális gépek Linux rendszerű vendég virtuális gépeinek visszaállítása</br></br> Az Azure virtuális gépek nem érhető el fájlkonzisztens biztonsági mentés
**A helyi gép/Azure-beli virtuális Gépet a MABS** | Hyper-V és VMware virtuális gépek Linux rendszerű vendég virtuális gépeinek fájlkonzisztens biztonsági mentése<br/><br/> Virtuális gép Hyper-V és VMWare Linux rendszerű Vendég virtuális gépek visszaállítása</br></br> Az Azure virtuális gépek nem érhető el fájlkonzisztens biztonsági mentésre.

## <a name="disk-support"></a>Lemezek támogatása

A deduplikáció lemeztámogatás a következőképpen történik:
- Lemez deduplikáció támogatott helyszíni, a Windows rendszert futtató Hyper-V virtuális gépek biztonsági mentése a DPM- vagy MABs használatakor. A Windows Server az adatok deduplikációját (a gazdagép szintjén) a virtuális géphez Backup-tárolóként csatlakoztatott virtuális merevlemezeken (VHD-ken) végzi el.
- A deduplikáció nem támogatott az Azure-ban egyik Backup-összetevőhöz. Ha a System Center DPM és Backup Server az Azure-ban van telepítve, a virtuális géphez csatolt tárolólemezek nem deduplikálhatók.


## <a name="securityencryption-support"></a>Biztonság és titkosítás támogatása

Az Azure Backup támogatja a titkosítás az átvitel és inaktív adatok:

Hálózati forgalom az Azure-bA:
- Biztonsági mentés a Recovery Services-tárolóba kiszolgálókról érkező forgalom az Advanced Encryption Standard 256 eljárással van titkosítva.
- Biztonsági mentési adatok egy biztonságos HTTPS-kapcsolaton keresztül zajlik.
- A biztonsági mentési adatokat a Recovery Services-tárolót, titkosított formában van tárolva.
- Csak Ön rendelkezik az adatok zárolásának feloldására szolgáló jelszóval. A Microsoft nem tudja visszafejteni a biztonsági mentési adatokat.
    > [!WARNING]
    > Miután beállította a tárolót, csak Önnek lesz hozzáférése a titkosítási kulcshoz. A Microsoft soha nem őriz másolatot, és nem rendelkezik hozzáféréssel a kulcshoz. Ha az ügyfél elveszíti a kulcsot, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.
Adatbiztonság:
- Amikor az Azure virtuális gépek biztonsági mentésének kell titkosítás beállítása *belül* a virtuális gépet.
- Az Azure Backup támogatja az Azure Disk Encryption szolgáltatást, amely a BitLockert használja Windows rendszerű virtuális gépeken és a **dm-crypt**-et Linux rendszerű virtuális gépeken.
- Az Azure Backup a háttérben az [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) használatával biztosít védelmet az inaktív adatok számára.


**Gép** | **Az átvitel során** | **Inaktív állapotban**
--- | --- | ---
A helyi Windows számítógépek DPM/MABS nélkül | ![Igen][green] | ![Igen][green]
Azure-beli virtuális gépek | ![Igen][green] | ![Igen][green] 
Helyszíni/Azure-beli virtuális gépek DPM-mel | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek a MABS | ![Igen][green] | ![Igen][green]



## <a name="compression-support"></a>A tömörítés támogatása

Backup támogatja a tömörítést, a biztonsági mentési forgalom, az alábbi táblázat foglalja össze. Vegye figyelembe:

- Az Azure virtuális gépek a Virtuálisgép-bővítmény olvassa be az adatokat közvetlenül az Azure storage-fiókot a tárolóhálózaton keresztül, ezért nem kell tömöríteni a forgalmat.
- DPM- vagy MABS használja, ha az adatokat, biztonsági másolatot a DPM/MABS, sávszélesség mentése előtt is tömörítse. 

**Gép** | **Tömörítse a MABS és DPM (TCP)** | **(HTTPS) tömörítése a tárba**
--- | --- | ---
A helyi Windows számítógépek DPM/MABS nélkül | NA | Igen
Azure-beli virtuális gépek | NA | NA
Helyszíni/Azure-beli virtuális gépek DPM-mel | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek a MABS | ![Igen][green] | ![Igen][green]



## <a name="retention-limits"></a>Adatmegőrzési korlátok

**Beállítás** | **Korlátok** 
--- | --- 
Helyreállítási pontok maximális száma a védett példányonként (a gép/számítási feladat | 9999
Helyreállítási pontok maximális száma lejárati idő | Korlátlan
A DPM/MABS maximális biztonsági mentés gyakorisága | 15 percenként az SQL Serverhez<br/><br/> Miután egy órában más számítási feladatokhoz.
Maximális biztonsági mentés gyakorisága a tárba | A helyszíni MARS futó Windows-gépek vagy az Azure virtuális gépek: három naponta<br/><br/> DPM/MABS: Két, naponként<br/><br/> Az Azure virtuális gép biztonsági mentése: Naponta egyszer
Helyreállítási pont megőrzése | Napi, heti, havi, éves.
Maximális megőrzési időtartam | Biztonsági mentés gyakoriságától függően.
A DPM/MABS lemezen helyreállítási pontok | 64 fájlkiszolgálókhoz, 448 alkalmazások kiszolgálói.<br/><br/> Szalag-helyreállítási pontok a helyszíni dpm korlátlanok.

## <a name="next-steps"></a>További lépések

- [Azure-beli virtuális gépek biztonsági mentése](backup-azure-arm-vms-prepare.md)
- [Közvetlen Windows-gépek biztonsági mentése](tutorial-backup-windows-server-to-azure.md), anélkül, hogy egy biztonsági mentési kiszolgálóra.
- [Állítsa be a MABS](backup-azure-microsoft-azure-backup.md) Azure adatforrásáig és vissza másolatot a MABS történő biztonsági mentés.
- [Állítsa be a DPM](backup-azure-dpm-introduction.md) Azure adatforrásáig és vissza másolatot a dpm-hez történő biztonsági mentés.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

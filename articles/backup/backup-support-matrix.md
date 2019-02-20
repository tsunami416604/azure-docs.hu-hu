---
title: Az Azure biztonsági mentés támogatási mátrixa
description: Támogatási beállításainak és az Azure Backup szolgáltatás korlátozásai összegzését tartalmazza.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: ff4ee1d88bd13e647d0f6218d7e9c9b2c57a5a01
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429570"
---
# <a name="azure-backup-support-matrix"></a>Az Azure biztonsági mentés támogatási mátrixa

Használhatja a [Azure Backup szolgáltatás](backup-overview.md) a biztonsági másolatokat a Microsoft Azure felhőbe. Ez a cikk általános támogatási beállításait és a korlátozások az Azure Backup-forgatókönyveinek és központi telepítések foglalja össze.

Egyéb támogatási mátrixok érhetők el:

[Támogatási mátrix](backup-support-matrix-iaas.md) Azure VM backup [támogatási mátrix](backup-support-matrix-mabs-dpm.md) (MABS) a System Center DPM vagy a Microsoft Azure Backup server használatával biztonsági mentés [támogatási mátrix](backup-support-matrix-mars-agent.md) a biztonsági témakört a Microsoft Azure-ral Recovery Services-(MARS-) ügynök

## <a name="vault-support"></a>Tároló támogatása

Azure biztonsági mentések az Recovery Services-tárolók vezényléséhez és a biztonsági másolatok kezelése a, és tárolja a biztonsági másolat.

**Beállítás** | **Részletek**
--- | ---
**Tárolók az előfizetés** | Legfeljebb 500 Recovery Services-tárolók egyetlen előfizetéssel.
**Egy tároló gépek** | Legfeljebb 1000 Azure virtuális gépek Ezenkívül egyetlen tárban.<br/><br/> Legfeljebb 50 MABS kiszolgálók Ezenkívül egyetlen tárban lehet regisztrálni.
**Adatforrások tároló storage-ban** | Maximális 54400 GB. Az Azure virtuális gép biztonsági mentéseinek nincs korlátozva van.
**Biztonsági másolatok a tárba** | Az Azure virtuális gépek: naponta egyszer<br/><br/>A DPM/MABS által védett gépek: naponta kétszer<br/><br/> Gépek biztonsági mentése közvetlenül a MARS-ügynök használatával: napi háromszori. 
**Biztonsági másolatok tárolók között** | Biztonsági mentés van egy adott régión belül.<br/><br/> Minden Azure-régióban, amely tartalmazza a készíteni kívánt virtuális gépeket tároló van szüksége. Nem készíthet biztonsági másolatot egy másik régióba. 
**Tár áthelyezése** | Is [helyezze át a tárolók](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) előfizetések között, vagy ugyanabban az előfizetésben erőforráscsoportok között.
**Tárolók közötti adatáthelyezés** | Az adatok biztonsági másolatát tároló közötti áthelyezése nem támogatott.
**Tároló tárolási típus módosítása** | Módosíthatja a tárolóreplikáció típusa (GRS vagy LRS) egy tároló előtt biztonsági másolatai. Miután a biztonsági mentések fordulnak a tárolóban, a replikáció típusa nem módosítható.



## <a name="on-premises-backup-support"></a>Támogatja a helyszíni biztonsági mentést

Íme, mi támogatott, ha azt szeretné, a helyszíni gépek biztonsági mentéséhez.

**Gép** | **Biztonsági mentés** | **Hely** | **Szolgáltatások**
--- | --- | --- | ---
**Közvetlen biztonsági mentést a MARS-ügynök Windows-gép** | Fájlok, mappák, a rendszer állapota | Biztonsági mentés a Recovery services-tároló | A biztonsági mentés napi háromszori.<br/><br/> Nincs alkalmazás együttműködő biztonsági mentés.<br/><br/> A visszaállítási fájl, mappa, kötet.
**Közvetlen biztonsági mentést a MARS-ügynök Linux-gép** | A biztonsági mentés nem támogatott.
**A dpm biztonsági mentése** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok. | Készítsen biztonsági másolatot helyi DPM-tároló. A DPM majd biztonsági mentést készít a tárba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/>. Oracle nem támogatott.
**Vissza a MABS** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok. | Készítsen biztonsági másolatot a MABS helyi tárba. MABS majd biztonsági mentést készít a tárolóba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/>. Oracle nem támogatott.


## <a name="azure-vm-backup-support"></a>Az Azure virtuális gépek biztonsági mentési támogatása

### <a name="azure-vm-limits"></a>Az Azure virtuális gépekre korlátai

**Korlát** | **Részletek**
--- | ---
Az Azure virtuális gép adatlemezek | 16-os korlát.
Az Azure virtuális gép adatlemez mérete | Az egyes lemezek legfeljebb 4095 GB is lehet.


### <a name="azure-vm-backup-options"></a>Azure virtuális gép biztonsági mentési lehetőségek

Íme, mi támogatott, ha azt szeretné, az Azure virtuális gépek biztonsági mentése.

**Gép** | **Biztonsági mentés** | **Hely** | **Szolgáltatások**
--- | --- | --- | ---
**VM-bővítmény használata az Azure virtuális gép biztonsági mentése** | Teljes virtuális gép | Biztonsági mentési tárba való | A bővítmény telepítve van a virtuális gép biztonsági mentésének engedélyezésekor.<br/><br/> A biztonsági mentés naponta egyszer.<br/><br/> App-t támogató biztonsági mentési Windows virtuális gépek, Linux rendszerű virtuális gépek fájlkonzisztens biztonsági mentésre. Alkalmazás-konzisztencia a Linux rendszerű gépek egyéni parancsfájlok segítségével konfigurálhatja.<br/><br/> Virtuálisgép-lemez visszaállítása.<br/><br/> Nem lehet biztonsági mentése Azure virtuális gépen a helyszíni helyre.
**Az Azure VM backup MARS-ügynök használatával** | Fájlok és mappák | Biztonsági mentési tárba való | A biztonsági mentés napi háromszori.<br/><br/> A MARS-ügynök mellett a Virtuálisgép-bővítmény futtatható, ha a teljes virtuális Gépet, hanem konkrét fájlok és mappák biztonsági.
**A DPM Azure virtuális Gépen** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok. | Készítsen biztonsági másolatot a dpm-et futtató Azure virtuális gépek helyi tárba. A DPM majd biztonsági mentést készít a tárba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/>. Oracle nem támogatott.
**Az Azure virtuális Gépet, a MABS** | Fájlok, mappák, kötetek, rendszerállapot, az alkalmazásadatok. | Helyi tároló MABS futtató Azure virtuális gépek biztonsági mentése. MABS majd biztonsági mentést készít a tárolóba. | Alkalmazás-alkalmazásfüggő pillanatképek<br/><br/> Biztonsági mentési és helyreállítási teljes részletességgel.<br/><br/> Linux rendszerű virtuális gépek (a Hyper-V vagy VMware) esetében támogatott.<br/><br/> Oracle nem támogatott.





## <a name="linux-backup-support"></a>Biztonsági mentési Linux-támogatás

Íme, mi támogatott, ha azt szeretné, Linux rendszerű gépek biztonsági mentéséhez.

**Biztonsági mentés** | **Linux (Azure által támogatott)**
--- | ---
**Közvetlen biztonsági mentést, a Linuxot futtató helyszíni gépre**. | Nem. A MARS-ügynök csak Windows-gépeken telepíthető.
**Készítsen biztonsági másolatot az Azure linuxos virtuális Gépet (ügynök bővítmény)** | Alkalmazáskonzisztens biztonsági mentés használatával [egyéni parancsfájlok](backup-azure-linux-app-consistent.md).<br/><br/> A fájlszintű helyreállítási.<br/><br/> Virtuális gép létrehozása egy helyreállítási pontot vagy a lemez visszaállításához.
**A helyszíni vagy a DPM használatával Linux operációs rendszert futtató Azure virtuális gép biztonsági mentése** | Hyper-V és VMware virtuális gépek Linux rendszerű vendég virtuális gépeinek fájlkonzisztens biztonsági mentése<br/><br/> Hyper-V és VMware virtuális gépek Linux rendszerű vendég virtuális gépeinek visszaállítása</br></br> Az Azure virtuális gépek nem érhető el fájlkonzisztens biztonsági mentés
**A helyi gép/Azure-beli virtuális gép biztonsági mentése Linux MABS-alapú** | Hyper-V és VMware virtuális gépek Linux rendszerű vendég virtuális gépeinek fájlkonzisztens biztonsági mentése<br/><br/> Virtuális gép Hyper-V és VMWare Linux rendszerű Vendég virtuális gépek visszaállítása</br></br> Az Azure virtuális gépek nem érhető el fájlkonzisztens biztonsági mentésre.

## <a name="daylight-saving-support"></a>Nyári időszámításra támogatása

Az Azure Backup nem támogatja az Azure virtuális gép biztonsági mentéseinek a nyári időszámítás – mentés módosítása a óra automatikus illesztését. Biztonsági mentési szabályzatok manuálisan szükség szerint módosítsa.


## <a name="disk-deduplication-support"></a>Lemez deduplikációs támogatás

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

Backup támogatja a tömörítést, a biztonsági mentési forgalom, az alábbi táblázat foglalja össze. 

- Az Azure virtuális gépek a Virtuálisgép-bővítmény olvassa be az adatokat közvetlenül az Azure storage-fiókot a tárolóhálózaton keresztül, ezért nem kell tömöríteni a forgalmat.
- DPM- vagy MABS használja, ha az adatokat, biztonsági másolatot a DPM/MABS, sávszélesség mentése előtt is tömörítse.

**Gép** | **Tömörítse a MABS és DPM (TCP)** | **(HTTPS) tömörítése a tárba**
--- | --- | ---
**Közvetlen biztonsági mentést, a helyi Windows-számítógépek** | NA | Igen
**VM-bővítmény használata az Azure virtuális gépek biztonsági mentése** | NA | NA
** Biztonsági másolatot készíteni a helyszíni/Azure gépeken MABS/DPM-mel | ![Igen][green] | ![Igen][green]



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

- [Tekintse át a támogatási mátrix](backup-support-matrix-iaas.md) Azure VM backup esetében.


[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

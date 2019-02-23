---
title: Az Azure Backup a Microsoft Azure Recovery Services-(MARS) Agent ügynököt futtató gépek biztonsági mentése támogatási mátrixa
description: Ez a cikk összegzi az Azure Backup támogatja a Microsoft Azure Recovery Services-(MARS) Agent ügynököt futtató gépek biztonsági mentésekor.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: b579816a4367537a7da4aa0f68c8776465295448
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670752"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services-(MARS) ügynök biztonsági mentés támogatási mátrixa

Használhatja a [Azure Backup szolgáltatás](backup-overview.md) a helyszíni gépek és alkalmazások és Azure virtuális gépek biztonsági mentése. Ez a cikk összegzi a támogatási beállításokat és a Microsoft Azure Recovery Services-(MARS)-ügynökkel rendelkező gépek biztonsági mentését vonatkozó korlátozások.

## <a name="about-the-mars-agent"></a>Tudnivalók a MARS-ügynök

A MARS-ügynök szolgál az Azure Backup szolgáltatás biztonsági mentése az Azure-beli és helyszíni gépek adatainak a biztonsági mentés a Recovery Services-tároló az Azure-ban. A MARS-ügynök a következő használható:
- Futtassa az ügynök a helyi Windows-számítógépek úgy, hogy azok biztonsági másolatot készíthet közvetlenül az Azure-beli biztonsági mentési Recovery Services-tárolóba.
- Futtassa az ügynököt a Windows Azure virtuális gépeken, így közvetlenül a tárolóba való biztonsági mentés.
- Futtassa az ügynök a Microsoft Azure Backup Server (MABS) vagy a System Center Data Protection - Manager (DPM) kiszolgálóhoz. Ebben a forgatókönyvben gépeket és számítási feladatok biztonsági mentése a MABS/dpm-hez, és a MABS és a DPM biztonsági mentés egy tárolót az Azure-ban a MARS-ügynök biztonsági majd. 

Milyen biztonsági másolatot készíthet attól függ, ahová az agent telepítve van.

- [További](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) kapcsolatos biztonsági mentési architektúra a MARS-ügynök használatával.
- [További]() MABS/DPM névjegye [biztonsági mentési architektúra](backup-architecture.md#architecture-back-up-to-dpmmabs)és [követelmények](backup-support-matrix-mabs-dpm.md).


## <a name="supported-installation"></a>Támogatott telepítési

**Telepítés** | **Részletek**
--- | ---
**Töltse le a legújabb MARS-ügynök** | Az ügynök legújabb verzióját letöltheti a tárolóból vagy [töltenie közvetlenül](https://aka.ms/azurebackup_agent).
**Telepítse a gépen közvetlenül** | A MARS-ügynök telepítheti közvetlenül a egy helyszíni Windows server vagy a Windows Azure virtuális Gépen fut az [támogatott operációs rendszerek]().
**Telepítse a tartalék kiszolgáló** | Ha beállította a DPM- vagy MABS biztonsági mentése az Azure-ba, töltse le és telepítse a MARS-ügynököt a kiszolgálón. Az ügynök megfelelően telepíthető a [támogatott operációs rendszerek](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) a backup server támogatási mátrixa.

> [!NOTE]
> Alapértelmezett biztonsági mentés engedélyezése Azure virtuális gépek rendelkeznek az Azure Backup bővítmény telepítése. Ez a bővítmény a teljes virtuális gép biztonsági mentését. Telepítse, és futtassa a MARS-ügynök-beli virtuális gépen a kiterjesztéssel együtt, ha szeretne biztonsági másolatot készíteni a meghatározott mappákról és fájlokat ahelyett, hogy a teljes virtuális gép.
> A MARS-ügynök-beli virtuális gépen való futtatásakor, biztonsági másolatot készít a virtuális gépen az ideiglenes tároló található fájlok és mappák. Biztonsági mentés sikertelen lesz, ha a fájlok és mappák törlődnek az ideiglenes tároló, vagy ha a rendszer eltávolítja az ideiglenes tárhely.


## <a name="cache-folder-support"></a>Gyorsítótár-mappa támogatása

Biztonsági mentésekor a MARS-ügynökkel rendelkező, az ügynök pillanatképet készít az adatok, és az Azure-bA küldés előtt a helyi gyorsítótár mappában tárolja azokat. A gyorsítótármappa (új) rendelkezik a követelmények számát.

**Cache** | **Részletek**
--- | ---
**Gyorsítótár mérete** |  Gyorsítótár-mappa méretét a szabad hely a biztonsági mentési adatok teljes mérete legalább 5 – 10 %-át kell lennie. 
**Gyorsítótár helye** | A gyorsítótár mappája a biztonsági mentés alatt álló gép helyi kell lennie, és online állapotban kell lennie.<br/><br/> A gyorsítótár mappája nem található egy hálózati megosztáson, cserélhető adathordozón vagy egy kapcsolat nélküli köteten. 
**Ügyfélgyorsítótár-mappa** | A gyorsítótármappa titkosítani kell, a deduplikált kötetek, vagy egy mappát, amely tömörített és ritka/újraelemzési-pont
**Módosíthatja a gyorsítótár helye** | Módosíthatja a gyorsítótár helyét a biztonságimásolat-készítő motor (net stop bengine) leállításával és a gyorsítótármappa másolása az új meghajtó (Győződjön meg arról, elég hely van). Ezután frissítse HKLM\SOFTWARE\Microsoft\Windows Azure Backup (Config/ScratchLocation és konfiguráció/CloudBackupProvider/ScratchLocation) területen két beállításjegyzékbeli bejegyzést az új helyre, és indítsa újra a motor.

## <a name="networking-and-access-support"></a>Hálózat- és hozzáférés-támogatás

### <a name="url-access"></a>URL-hozzáférés

A MARS-ügynök a következő URL-címekhez hozzá kell férnie:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>Sávszélesség-szabályozási támogatása

**Funkció** | **Részletek**
--- | ---
Sávszélesség-szabályozás | Támogatott<br/><br/> Használat **tulajdonságainak módosítása** a MARS-ügynök sávszélesség módosításához.
Hálózati sávszélesség-szabályozás | Nem elérhető a Windows Server 2008 R2, Windows Server 2008 SP2 vagy Windows 7 rendszerű gépek biztonsági.

## <a name="support-for-direct-backups"></a>A közvetlen biztonsági mentések támogatása

A következő táblázat összefoglalja a helyszíni gépek és Azure virtuális gépeken futó operációs rendszerekről közvetlenül az Azure-bA a MARS-ügynökkel rendelkező is készíthető.

- Az összes operációs rendszer 64 bites rendszer.
- Az összes operációs rendszer a legújabb szolgáltatások csomagokat és frissítéseket kell futtatnia.
- Melyik a DPM és a MABS kiszolgálók készíthető a MARS-ügynök a részletekért tekintse át a [Ez a táblázat](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

**Operációs rendszer** | **Fájlok és mappák** | **Rendszerállapot**
--- | --- | ---
A Windows 10-es (nagyvállalati, otthoni Pro) | Igen | Nem
Windows 8.1 (vállalati, Pro)| Igen |Nem
A Windows 8 (vállalati, Pro) | Igen | Nem
Windows 7 (Ultimate, Enterprise, Pro, otthoni prémium vagy alapszintű, alapszintű) | Igen | Nem
Windows Server 2016 (Standard, Datacenter, Essentials) | Igen | Igen
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Igen | Igen
Windows Server 2012 (Standard, Datacenter, Foundation) | Igen | Igen
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Igen | Igen
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Igen | Nem
A Windows Storage Server 2016 és 2012 R2/2012 (standard szintű, munkacsoporthoz | Igen | Nem


## <a name="backup-limits"></a>Biztonsági mentési korlátok

Az Azure Backup korlátozza az adatforrás méretét, egy fájl vagy mappa, amely a biztonsági mentésre alkalmas helyezi. Az egyik kötetéről biztonsági mentésre kijelölt elemek mérete nem haladhatja meg a méret a táblázatban összefoglalt.

**Operációs rendszer** | **Maximális mérete**
--- | ---
Windows Server 2012 vagy újabb |  54 400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 vagy újabb  | 54 400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>Biztonsági mentés támogatott fájltípusok

**Típus** | **Támogatott** 
--- | --- 
Titkosított   | Igen 
Tömörített | Igen
Ritka | Igen 
Tömörített és ritka | Igen
Rögzített hivatkozások  | Nem támogatott<br/><br/> Kihagyva
Újraelemzési pontok   | Nem támogatott<br/><br/> Kihagyva
Titkosított és ritka |  Nem támogatott<br/><br/> Kihagyva
Tömörített adatfolyam   | Nem támogatott<br/><br/> Kihagyva
Ritka adatfolyam   | Nem támogatott<br/><br/> Kihagyva
Egy meghajtót (szinkronizált fájlok olyan ritka adatfolyam)    | Nem támogatott 

## <a name="supported-drivesvolumes-for-backup"></a>Biztonsági mentés támogatott meghajtókról/kötetekről

**Meghajtó vagy kötet** | **Támogatott** | **Részletek**
--- | --- | ---
Csak olvasható kötetek   | Nem támogatott | A kötet működéséhez a VSS írhatónak kell lennie.
Offline kötetek | Nem támogatott |   A kötet online-hoz a VSS használata kell lennie.
Hálózati megosztás   | Nem támogatott |   A kötet helyi biztonsági mentés a kiszolgálón kell lennie.
A BitLocker által védett kötetek | Nem támogatott |   Biztonsági mentés működéséhez a kötet zárolása előtt.
Fájlrendszer azonosítása  | Nem támogatott |   Csak NTFS esetén.
Cserélhető adathordozó | Nem támogatott |   Az összes biztonsági másolati elem forrás rögzített állapottal kell rendelkeznie.
A deduplikált meghajtók | Támogatott.<br/><br/> Az Azure Backup a deduplikált adatokat normális adatokká alakítja át. Ez a optimalizálja az adatokat, titkosítja, tárolja és elküldi azokat a tárolóban.

## <a name="support-for-initial-offline-backup"></a>Kezdeti offline biztonsági mentés támogatása

Az Azure Backup támogatja az "offline áttöltést" kezdeti biztonsági mentési adatok átvitele az Azure-ban a lemezeket. Ez azért hasznos, ha a kezdeti biztonsági mentés valószínűleg terabájt (TB-osra bővül) tartományán. Offline biztonsági mentés esetén támogatott:

- Közvetlen biztonsági mentést a fájlokat és mappákat a MARS-ügynököt futtató helyi gépen.
- A számítási feladatok és a egy DPM-kiszolgáló vagy a MABS fájlok biztonsági mentése.
- Rendszerállapotfájlok offline biztonsági másolat nem használható.


## <a name="support-for-restore"></a>Visszaállítás támogatása

- Az új [azonnali helyreállítás](backup-instant-restore-capability.md) kiadás az Azure Backup szolgáltatás lehetővé teszi adatok visszaállítása előtt a tárolóba lett átmásolva.<br/><br/> A funkció használatához a biztonsági mentés alatt álló gépen kell futnia .NET-keretrendszer 4.5.2-es vagy újabb verziója.
- Biztonsági mentések nem lehet visszaállítani a célszámítógépen az operációs rendszer egy korábbi verzióját. Például egy Windows 7 számítógépről készült biztonsági másolatok visszaállíthatók, a Windows 8-as vagy újabb. Azonban a Windows 8 rendszerű számítógép egy biztonsági nem állítható vissza egy Windows 7 rendszerű számítógépen.


## <a name="next-steps"></a>További lépések
- [További](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) kapcsolatos biztonsági mentési architektúra a MARS-ügynök használatával.
- [Ismerje meg,](backup-support-matrix-mabs-dpm.md) mit támogat a MARS-ügynök a Microsoft Azure Backup Server (MABS) vagy a System Center DPM futtatásakor.



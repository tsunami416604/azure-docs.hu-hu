---
title: Az Azure Backup a Microsoft Azure Recovery Services-(MARS) Agent ügynököt futtató gépek biztonsági mentése támogatási mátrixa
description: Ez a cikk összegzi az Azure Backup támogatja, amely a Microsoft Azure Recovery Services-(MARS) ügynököt futtató gépek biztonsági mentésekor.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: e12ca7561181412318fa594093b047cd95e4e6bc
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448033"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services-(MARS) ügynök biztonsági mentés támogatási mátrixa

Használhatja a [Azure Backup szolgáltatás](backup-overview.md) a helyszíni gépek és alkalmazások biztonsági mentése és biztonsági mentése Azure-beli virtuális gépek (VM). Ez a cikk összegzi támogatási beállításait és korlátozások, a Microsoft Azure Recovery Services-(MARS) ügynök biztonsági mentésének előkészületei használatakor.

## <a name="the-mars-agent"></a>A MARS-ügynök

Az Azure Backup biztonsági másolatokat a helyszíni gépek és Azure virtuális gépek az Azure-beli biztonsági mentési Recovery Services-tárolót használ a MARS-ügynök. A MARS-ügynök a következőket teheti:
- Futtassa a helyi Windows-gépeken, hogy azok biztonsági másolatot készíthet közvetlenül az Azure-beli biztonsági mentési Recovery Services-tárolóba.
- Futtassa a Windows virtuális gépeken, hogy azok biztonsági másolatot készíthet közvetlenül egy tárolót.
- Futtassa a Microsoft Azure Backup Server (MABS) vagy a System Center Data Protection Manager (DPM) kiszolgáló. Ebben a forgatókönyvben gépeket és számítási feladatok biztonsági mentése a MABS vagy a DPM-kiszolgálóhoz. A MARS-ügynök ezután menti a kiszolgáló egy tárolót az Azure-ban. 

A biztonsági mentési lehetőségek attól függnek, ahová az agent telepítve van. További információkért lásd: [a MARS-ügynök használatával az Azure Backup architektúrájának](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders). További információ a MABS és a DPM biztonsági mentési architektúra: [biztonsági mentést a DPM- vagy MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). További tájékoztatás [követelmények](backup-support-matrix-mabs-dpm.md) a biztonsági mentési architektúra.

## <a name="supported-installations"></a>Támogatott telepítések

**Telepítés** | **Részletek**
--- | ---
A legújabb MARS-ügynök letöltése | Az ügynök legújabb verzióját letöltheti a tárolóból vagy [töltenie közvetlenül](https://aka.ms/azurebackup_agent).
Telepítse a gépen közvetlenül | A MARS-ügynök közvetlenül egy helyszíni Windows server vagy bármely futó Windows virtuális gépen telepítheti a [támogatott operációs rendszerek](https://docs.microsoft.com/en-us/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Telepítse a tartalék kiszolgáló | Ha beállította a DPM- vagy MABS biztonsági mentése az Azure-ba, töltse le és telepítse a MARS-ügynököt a kiszolgálón. Telepítheti az ügynököt [támogatott operációs rendszerek](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) a backup server támogatási mátrixa.

> [!NOTE]
> Alapértelmezés szerint engedélyezettek a biztonsági mentés az Azure virtuális gépek rendelkeznek az Azure Backup bővítmény telepítése. Ez a bővítmény a teljes virtuális gép biztonsági mentését. Telepítse, és futtassa a MARS-ügynök-beli virtuális gépen a kiterjesztéssel együtt, ha szeretne biztonsági másolatot készíteni a meghatározott mappákról és fájlokat ahelyett, hogy a teljes virtuális gép.
> A MARS-ügynök-beli virtuális gépen való futtatásakor, biztonsági másolatot készít fájlokat vagy mappákat, amelyek az ideiglenes tároló, a virtuális gépen. Biztonsági mentések sikertelenek lesznek, ha a fájlok vagy mappák törlődnek az ideiglenes tároló, vagy ha a rendszer eltávolítja az ideiglenes tárhely.


## <a name="cache-folder-support"></a>Gyorsítótár-mappa támogatása

A MARS-ügynök használatakor a biztonsági másolatokat az ügynök pillanatképet készít az adatokról, és azután küldi el az adatokat az Azure-ban tárolja azokat egy helyi ügyfélgyorsítótár mappájában található. A gyorsítótármappa (új) különböző követelményekkel rendelkezik:

**Cache** | **Részletek**
--- | ---
Méret |  Az ügyfélgyorsítótár mappájában található szabad hely a biztonsági mentési adatok teljes mérete legalább 5-10 %-os lehet. 
Hely | A gyorsítótármappa helyben kell tárolni, amelyek másolat készül a gépen, és online állapotban kell lennie. A gyorsítótár mappája nem lehet a hálózati megosztás, cserélhető adathordozón vagy egy kapcsolat nélküli köteten. 
Mappa | A gyorsítótármappa titkosítani kell a deduplikált kötetek vagy tömörített, Ez ritka vagy egy újraelemzési pontot tartalmazó mappában.
Helyadatok módosításainak | A gyorsítótár helyének módosításához a biztonságimásolat-készítő motor leállítása (`net stop bengine`) és a gyorsítótármappa másolása az új meghajtó. (Győződjön meg arról, az új meghajtó nincs elegendő lemezterület.) Frissítse a két beállításjegyzékbeli bejegyzést **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** és **Config/CloudBackupProvider/ScratchLocation**) az új helyre, és indítsa újra a motor.

## <a name="networking-and-access-support"></a>Hálózat- és hozzáférés-támogatás

### <a name="url-access"></a>URL-hozzáférés

A MARS-ügynök a következő URL-címekhez hozzá kell férnie:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Sávszélesség-szabályozási támogatása

**Funkció** | **Részletek**
--- | ---
Sávszélesség-szabályozás | Támogatott. A MARS-ügynök használatával **tulajdonságainak módosítása** sávszélesség módosításához.
Hálózati sávszélesség-szabályozás | Nem érhető el a Windows Server 2008 R2, Windows Server 2008 SP2 vagy Windows 7 rendszerű gépek biztonsági másolat.

## <a name="support-for-direct-backups"></a>A közvetlen biztonsági mentések támogatása

A MARS-ügynök használatával közvetlenül az Azure egyes operációs rendszerek arról, hogy a helyszíni gépek és Azure virtuális gépek biztonsági mentése. Az operációs rendszerek 64 bites kell lennie, és fut a legújabb szolgáltatások csomagokat és frissítéseket kell lennie. Az alábbi táblázat ezeket az operációs rendszereket foglalja össze:

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
A Windows Storage Server 2016 és 2012 R2/2012 (standard szintű, munkacsoporthoz tartozó) | Igen | Nem

További információkért lásd: [támogatott MABS és a DPM operációs rendszerek](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Biztonsági mentési korlátok

Az Azure Backup korlátozza az adatforrás méretét, egy fájl vagy mappa, amely a biztonsági mentésre alkalmas. A cikkek, amelyek egy egyetlen kötetéről biztonsági mentése nem haladhatja meg az az alábbi táblázatban foglaltak méretek:

**Operációs rendszer** | **Maximális mérete**
--- | ---
Windows Server 2012 vagy újabb |  54 400 GB
Windows Server 2008 R2 SP1 |    1,700 GB
Windows Server 2008 SP2 | 1,700 GB
Windows 8 vagy újabb  | 54 400 GB
Windows 7   | 1,700 GB


## <a name="supported-file-types-for-backup"></a>Biztonsági mentés támogatott fájltípusok

**Típus** | **Támogatás** 
--- | --- 
Titkosított   | Támogatott. 
Tömörített | Támogatott.
Ritka | Támogatott.
Tömörített és ritka | Támogatott.
Rögzített hivatkozások  | Nem támogatott. Kihagyva.
Újraelemzési pontok   | Nem támogatott. Kihagyva.
Titkosított és ritka |  Nem támogatott. Kihagyva.
Tömörített adatfolyam   | Nem támogatott. Kihagyva.
Ritka adatfolyam   | Nem támogatott. Kihagyva.
A onedrive vállalati verzió (szinkronizált fájlok olyan ritka adatfolyam)  | Nem támogatott. 

## <a name="supported-drives-or-volumes-for-backup"></a>Támogatott meghajtók vagy kötetek biztonsági mentés

**Meghajtó vagy kötet** | **Támogatás** | **Részletek**
--- | --- | ---
Csak olvasható kötetek   | Nem támogatott | Kötet árnyékmásolata szolgáltatás (VSS) csak akkor, ha a kötet írható működik.
Offline kötetek | Nem támogatott |   VSS csak akkor, ha a kötet online működik.
Hálózati megosztás   | Nem támogatott |   A kötet helyi a kiszolgálón kell lennie.
A BitLocker által védett kötetek | Nem támogatott |   A kötetet a biztonsági mentés megkezdése előtt kell oldani.
Fájlrendszer azonosítása  | Nem támogatott |   Csak az NTFS fájlrendszer támogatott.
Cserélhető adathordozó | Nem támogatott |   Az összes biztonsági másolati elem forrás rendelkeznie kell egy *rögzített* állapotát.
A deduplikált meghajtók | Támogatott | Az Azure Backup a deduplikált adatokat normális adatokká alakítja át. Optimalizálja, titkosítja, tárolja és elküldi az adatokat a tárolóba.

## <a name="support-for-initial-offline-backup"></a>Kezdeti offline biztonsági mentés támogatása

Az Azure Backup támogatja *kapcsolat nélküli beültetés* kezdeti biztonsági mentési adatok átvitele az Azure-bA lemezek használatával. Ez a támogatás akkor hasznos, ha a kezdeti biztonsági mentés várhatóan terabájt (TB-osra bővül) mérete tartományán. Offline biztonsági mentés esetén támogatott:

- Közvetlen biztonsági mentést a fájlokat és mappákat a MARS-ügynököt futtató helyi gépen.
- A számítási feladatok és a egy DPM-kiszolgáló vagy a MABS fájlok biztonsági mentése.

Rendszerállapotfájlok offline biztonsági másolat nem használható.


## <a name="support-for-data-restoration"></a>Adatok helyreállítása támogatása

Használatával a [azonnali visszaállítása](backup-instant-restore-capability.md) funkció az Azure Backup szolgáltatás, visszaállíthatja az adatokat előtt a tárolóba másolja. A gép biztonsági mentését végzi a kell futó .NET-keretrendszer 4.5.2-es vagy újabb verziója.

Biztonsági mentések nem lehet visszaállítani a célszámítógépen az operációs rendszer korábbi verzióját futtató. Például egy Windows 7 rendszert futtató számítógépről készült biztonsági másolatok visszaállíthatók, a Windows 8-as vagy újabb. Azonban egy olyan számítógépről, amely egy Windows 8 rendszerű készült biztonsági másolatok nem lehet visszaállítani, Windows 7 rendszert futtató számítógépen.


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [biztonsági mentési architektúra, amely a MARS Agent](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders).
- Ismerje meg, milyen rendelkezik támogatással, [MABS és a egy DPM-kiszolgáló a MARS-ügynök futtathatja](backup-support-matrix-mabs-dpm.md).
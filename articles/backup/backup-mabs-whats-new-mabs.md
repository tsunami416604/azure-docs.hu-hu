---
title: What's new in Microsoft Azure Backup Server
description: A Microsoft Azure Backup Server biztosít védelme érdekében a virtuális gép, fájlok és mappák, számítási feladatok és további fejlett biztonsági funkciókat. Ismerje meg, és az Azure Backup Server v3-as frissítés telepítése.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan; kasinh
ms.openlocfilehash: 8e80d935bac94f3d4b2380799a1aed256828af75
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625878"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>What's new in Microsoft Azure Backup Server

A Microsoft Azure Backup Server 3-as (MABS V3) verzióját a legújabb frissítés, és fontos hibajavításokat tartalmaz, a Windows Server 2019 támogatja, SQL 2017 és más funkciókat és fejlesztéseket tartalmaz. A javított hibák listáját és a telepítési utasításokat a MABS V3, lásd a Tudásbázis [4457852](https://support.microsoft.com/en-us/help/4457852/microsoft-azure-backup-server-v3).

A következő funkciók megtalálhatók a MABS V3:

## <a name="volume-to-volume-migration"></a>Kötet a kötet áttelepítése
A Modern Backup Storage (MBP) a MABS v2-ben, bejelentettük munkaterhelés-támogató tárhellyel, ahol konfigurálhatja az egyes számítási feladatok bizonyos tárolási, biztonsági mentése tároló tulajdonságai alapján. A konfigurációt követően előfordulhat az egyes adatforrások biztonsági mentések áthelyezése más tárolási optimalizált erőforrások kihasználtságát kell. MABS V3 lehetővé teszi az áttelepítése a biztonsági mentések és a egy másik kötetre kell tárolni konfiguráljon [3 lépést](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/).

## <a name="prevent-unexpected-data-loss"></a>Váratlan adatvesztés elkerülése
A vállalatok számára, a MABS kezeli a Rendszergazdák csoport. Áll irányelvek a tárolási, biztonsági mentésekhez használandó, amelyek egy adott backup-tárolóként MABS helytelen kötet fontos adatok elvesztését eredményezheti. A MABS V3, az ilyen forgatókönyvek például megakadályozása konfigurálása a köteteket, amelyek nem érhetők el a storage használatával azokat [PowerShell-parancsmagokat](https://docs.microsoft.com/system-center/dpm/add-storage#volume-exclusion).

## <a name="custom-size-allocation"></a>Egyéni méret foglalási
A modern Backup Storage (MBP) felhasznál tárolási dinamikusan, szükséges. Ehhez a MABS készül biztonsági másolat, ha konfigurálva van a védelem az adatok mérete számítja ki. Azonban ha sok fájlok és mappák éppen készül biztonsági másolat együtt, a fájlkiszolgáló is méretének hosszú időt vehet igénybe. A MABS V3 konfigurálhat, fogadja el a kötet mérete az alapértelmezett helyett minden egyes fájl méretének kiszámítása MABS ezért így időt takarít meg.

## <a name="optimized-cc-for-rct-vms"></a>Az RCT virtuális gépekhez optimalizált CC
MABS rct-t használja (a natív változáskövetését Hyper-V), mely csökken szükség időigényes konzisztencia ellenőrzi a forgatókönyvek a virtuális gép leáll. Az RCT a VSS pillanatkép-alapú biztonsági mentések által biztosított a change tracking nagyobb rugalmasságot biztosít. MABS v3-as és a tárhelyhasználata további optimalizálja bármely konzisztencia-ellenőrzések során csak a módosított adatok átvitele.

## <a name="support-to-tls-12"></a>A TLS 1.2 támogatása
A TLS 1.2-es biztonságos módja a kommunikáció a legjobban az osztály-titkosítás segítségével a Microsoft által javasolt. MABS mostantól támogatja a TLS 1.2 kommunikációs MABS és a védett kiszolgálók között, tanúsítványalapú hitelesítést, valamint a felhőbeli biztonsági mentést.

## <a name="vmware-vm-protection-support"></a>VMware virtuális gépek védelem támogatása
VMware virtuális gépek biztonsági mentésének támogatása az éles környezetekben üzemelő példányok. MABS V3 VMware virtuális gépek védelmét a következőket kínálja:

-   Támogatás a vCenter és az ESXi-6.5, támogatja a 5.5 és 6.0.
- VMware virtuális gépek felhőbe automatikus védelmét. Ha új VMware virtuális gépek hozzá vannak adva egy védett mappában, azok automatikusan a lemezes és felhőalapú védi.
- Helyreállítás hatékonyság fejlesztései VMware másodlagos helyet a helyreállításhoz.

## <a name="sql-2017-support"></a>SQL 2017-támogatás
MABS V3 SQL 2017 MABS-adatbázisként telepíthetők. Az SQL server frissítése SQL 2016-ból az SQL 2017, vagy frissen telepítheti. SQL 2017 munkaterhelés mindkét MABS V3 fürtözött és nem fürtözött környezetben is készíthető.

## <a name="windows-server-2019-support"></a>A Windows Server a 2019-támogatás
MABS V3 is telepíthető a Windows Server 2019. MABS V3 használata WS2019, vagy frissítés WS2019 előtt az operációs rendszer telepítése/frissítése MABS v3-as, vagy frissítheti az operációs rendszer közzététele a WS2016 V3 telepítése/frissítése is.

MABS V3 egy teljes kiadás, és telepíthető közvetlenül a Windows Server 2016, Windows Server 2019, vagy a MABS V2 frissíthetők. Előtt, frissítsen, vagy telepítse a Backup Server v3-as, olvassa el a telepítési előfeltételek.
További információ a telepítési/frissítési lépések MABS keresés [Itt](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).


> [!NOTE]

> MABS alap, a System Center Data Protection Manager ugyanazt a kódot tartalmaz. Data Protection Manager 1807 MABS v3 megegyezik.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan készíti elő a kiszolgálót, vagy a munkaterhelések védelmének megkezdése:
- [Backup Server számítási feladatainak előkészítése](backup-azure-microsoft-azure-backup.md)
- [VMware-kiszolgáló biztonsági mentése Backup Server használatával](backup-azure-backup-server-vmware.md)
- [SQL Server biztonsági mentése Backup Server használatával](backup-azure-sql-mabs.md)
- [Modern Backup Storage használata biztonsági mentési kiszolgálóra](backup-mabs-add-storage.md)

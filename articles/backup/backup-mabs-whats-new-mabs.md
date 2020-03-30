---
title: A Microsoft Azure Backup Server újdonságai
description: A Microsoft Azure Backup Server továbbfejlesztett biztonsági mentési lehetőségeket biztosít a virtuális gépek, a fájlok és mappák, a munkaterhelések és egyebek védelmére. Ismerje meg, hogyan telepítheti vagy frissítheti az Azure Backup Server V3 rendszert.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582806"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>A Microsoft Azure Backup Server újdonságai

A Microsoft Azure Backup Server 3-as verziója (MABS V3) a legújabb frissítés, amely kritikus hibajavításokat, Windows Server 2019-támogatást, SQL 2017-támogatást, valamint egyéb funkciókat és fejlesztéseket tartalmaz. A javított hibák listájának és a MABS V3 telepítési útmutatójának megtekintéséhez lásd a [TUDÁSBÁZIS 4457852.](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)

A MABS V3 a következő funkciókat tartalmazza:

## <a name="volume-to-volume-migration"></a>Kötetről kötetre történő áttelepítés

A Modern Backup Storage (MBS) a MABS V2-ben bejelentette, hogy számítási feladatokat tud a tárolás tulajdonságai alapján, ahol konfigurálja bizonyos számítási feladatok biztonsági mentését adott tárolóra. A konfiguráció után azonban előfordulhat, hogy bizonyos adatforrások biztonsági másolatait át kell helyeznie más tárolóba az optimalizált erőforrás-kihasználtság érdekében. A MABS V3 lehetővé teszi a biztonsági mentések áttelepítését, és beállíthatja, hogy [három lépésben](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)egy másik köteten tárolják őket.

## <a name="prevent-unexpected-data-loss"></a>Váratlan adatvesztés megelőzése

A vállalatoknál a MABS-t egy rendszergazdai csoport kezeli. Bár vannak olyan biztonsági mentések tárolására vonatkozó irányelvek, amelyeket biztonsági mentések készítéséhez kell használni, a MABS biztonsági mentési tárolóként megadott helytelen kötet kritikus adatok elvesztéséhez vezethet. A MABS V3-mal megakadályozhatja az ilyen forgatókönyveket, ha ezeket a köteteket úgy konfigurálja, mint amelyek nem érhetők el a [PowerShell-parancsmagok](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage)használatával a tároláshoz.

## <a name="custom-size-allocation"></a>Egyéni méretfelosztás

A modern biztonsági mentési tároló (MBS) a tárhelyet vékonyan, szükség szerint és szükség esetén használja fel. Ehhez a MABS kiszámítja a védelemre konfigurált adatok biztonsági másolatot. Ha azonban sok fájlról és mappáról van biztonsági másolato, mint például a fájlkiszolgálóesetében, a méretszámítás hosszú időt vehet igénybe. A MABS V3 segítségével beállíthatja, hogy a MABS elfogadja a kötet méretét alapértelmezettként, ahelyett, hogy kiszámolná az egyes fájlok méretét, így időt takaríthat meg.

## <a name="optimized-cc-for-rct-vms"></a>Optimalizált CC RCT virtuális gépekhez

A MABS az RCT-t (a Hyper-V natív változáskövetését) használja, ami csökkenti az időigényes konzisztencia-ellenőrzések szükségességét a virtuális gép összeomlása esetén. Az RCT a VSS pillanatkép-alapú biztonsági mentések által biztosított változáskövetésnél nagyobb rugalmasságot biztosít. A MABS V3 tovább optimalizálja a hálózati és tárolási felhasználást azáltal, hogy csak a módosított adatokat viszi át a konzisztencia-ellenőrzések során.

## <a name="support-to-tls-12"></a>A TLS 1.2 támogatása

A TLS 1.2 a Microsoft által javasolt biztonságos kommunikációs mód a legjobb osztálytitkosítással. A MABS mostantól támogatja a TLS 1.2-es kommunikációt a MABS és a védett kiszolgálók között a tanúsítványalapú hitelesítéshez és a felhőalapú biztonsági mentésekhez.

## <a name="vmware-vm-protection-support"></a>VMware VM védelem támogatása

VMware VM biztonsági mentés most már támogatott éles környezetben. A MABS V3 a következőket kínálja a VMware VM védelemhez:

- A vCenter és az ESXi 6.5 támogatása, valamint az 5.5 és a 6.0 támogatása.
- A VMware virtuális gépek automatikus védelme a felhőbe. Ha új VMware virtuális gépeket ad hozzá egy védett mappához, azok automatikusan védettek a lemezés a felhő.
- Helyreállítási hatékonyság javítása vmware alternatív hely helyreállítása.

## <a name="sql-2017-support"></a>Az SQL 2017 támogatása

A MABS V3 az SQL 2017-tel mabs adatbázisként telepíthető. Az SQL-kiszolgálót frissítheti AZ SQL 2016-ról az SQL 2017-re, vagy frissen telepítheti. Az SQL 2017-es számítási feladatokról fürtözött és nem fürtözött környezetben is biztonsági másolatot tud fésülni a MABS V3-mal.

## <a name="windows-server-2019-support"></a>A Windows Server 2019 támogatása

A MABS V3 telepíthető Windows Server 2019 rendszerre. A MABS V3 és a WS2019 használatához frissítse az operációs rendszert WS2019-re, mielőtt mabs V3-ra települne/frissítene, vagy frissítheti az operációs rendszert a V3 telepítése/frissítése után a WS2016-on.

A MABS V3 egy teljes kiadás, és közvetlenül telepíthető a Windows Server 2016, Windows Server 2019 rendszerre, vagy frissíthető a MABS V2-ről. A Backup Server V3-ra való frissítés vagy telepítés előtt olvassa el a telepítési előfeltételeket.
A [MABS](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package)telepítési/frissítési lépéseiről itt talál további információt.

> [!NOTE]
>
> A MABS kódbázisa megegyezik a System Center Data Protection Manager kódbázissal. A MABS v3 egyenértékű az 1807-es adatvédelmi menedzserrel.

## <a name="next-steps"></a>További lépések

További információ a kiszolgáló előkészítéséről vagy a munkaterhelés védelmének megkezdéséről:

- [Ismert problémák a MABS V3-ban](backup-mabs-release-notes-v3.md)
- [Biztonságimásolat-kiszolgáló munkaterhelésének előkészítése](backup-azure-microsoft-azure-backup.md)
- [VMware-kiszolgáló biztonsági mentése a Biztonsági másolat kiszolgálójával](backup-azure-backup-server-vmware.md)
- [Az SQL Server biztonsági mentése a Biztonsági másolat kiszolgálójával](backup-azure-sql-mabs.md)
- [Modern biztonsági mentési tároló használata a biztonsági másolat kiszolgálójával](backup-mabs-add-storage.md)

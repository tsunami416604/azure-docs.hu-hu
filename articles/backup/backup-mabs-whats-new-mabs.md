---
title: Az Microsoft Azure Backup-kiszolgáló újdonságai
description: A Microsoft Azure Backup-kiszolgáló továbbfejlesztett biztonsági mentési lehetőségeket biztosít a virtuális gépek, fájlok és mappák, munkaterhelések és egyebek védelméhez. Megtudhatja, hogyan telepítheti vagy frissíthet Azure Backup Server v3-ra.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582806"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Az Microsoft Azure Backup-kiszolgáló újdonságai

A Microsoft Azure Backup Server 3. verzió (MABS v3) a legújabb frissítés, amely kritikus hibajavításokat, Windows Server 2019-támogatást, SQL 2017-támogatást és egyéb funkciókat és fejlesztéseket tartalmaz. A rögzített hibák listájának és a MABS v3 telepítési utasításának megtekintéséhez lásd a TUDÁSBÁZIS [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3). számú cikkét.

A MABS v3 a következő funkciókat tartalmazza:

## <a name="volume-to-volume-migration"></a>Kötetről kötetre való Migrálás

A MABS v2 modern biztonsági másolati tárhely (MBS)-ben bejelentettük a számítási feladatok ellátására szolgáló tárhelyet, ahol bizonyos számítási feladatokat úgy konfigurálhat, hogy a tárolási tulajdonságok alapján biztonsági mentést lehessen készíteni egy adott tárhelyre. A konfigurálást követően azonban előfordulhat, hogy bizonyos adatforrások biztonsági másolatait át kell helyeznie más tárolóba az optimalizált erőforrás-használat érdekében. A MABS v3 lehetővé teszi a biztonsági másolatok átkonfigurálását és a különböző köteteken való tárolását [három lépésben](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

## <a name="prevent-unexpected-data-loss"></a>Váratlan adatvesztés megakadályozása

A vállalatokban a MABS-t a rendszergazdák csoportja felügyeli. Noha a biztonsági mentéshez használt tárterületre vonatkozó irányelvek is megtalálhatók, a biztonsági mentési tár MABS miatt helytelen kötet a kritikus adatvesztéshez vezethet. A MABS v3 használatával megakadályozhatja, hogy ezeket a köteteket konfigurálja úgy, hogy [ezeket a PowerShell-parancsmagokat](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage)használó tárolók számára ne legyenek elérhetők.

## <a name="custom-size-allocation"></a>Egyéni méret kiosztása

A modern biztonsági másolati tárhely (MBS) a tárolást vékonyan, és szükség esetén használja. Ehhez a MABS kiszámítja a biztonsági mentésre kerülő adatmennyiséget, amikor a védelemre van konfigurálva. Ha azonban sok fájl és mappa biztonsági mentése folyamatban van, például egy fájlkiszolgáló esetében, a méret kiszámítása hosszú időt is igénybe vehet. A MABS v3 használatával beállíthatja, hogy a MABS az alapértelmezett értékként fogadja el a kötet méretét az egyes fájlok méretének kiszámítása helyett, így időt takaríthat meg.

## <a name="optimized-cc-for-rct-vms"></a>RCT virtuális gépekhez optimalizált CC

A MABS a RCT (a natív változások követése a Hyper-V-ben) használja, ami csökkenti az időigényes konzisztencia-ellenőrzés szükségességét a virtuális gépek összeomlása esetén. A RCT nagyobb rugalmasságot biztosít, mint a VSS pillanatkép-alapú biztonsági mentések által biztosított változások nyomon követése. A MABS v3 a konzisztencia-ellenőrzések során csak a módosult adatmennyiségek átvitelével optimalizálja a hálózat és a tárterület felhasználását.

## <a name="support-to-tls-12"></a>A TLS 1,2 támogatása

A TLS 1,2 a Microsoft által a legjobb osztályú titkosítással javasolt kommunikáció biztonságos módja. A MABS mostantól támogatja a TLS 1,2 kommunikációt a MABS és a védett kiszolgálók között, a tanúsítványalapú hitelesítéshez és a Felhőbeli biztonsági mentésekhez.

## <a name="vmware-vm-protection-support"></a>VMware VM-védelem támogatása

A VMware virtuális gép biztonsági mentése mostantól támogatott az éles környezetben. A MABS v3 a következőt kínálja a VMware virtuális gépek védelméhez:

- A vCenter és ESXi 6,5 támogatását, valamint a 5,5 és 6,0 támogatását.
- VMware virtuális gépek automatikus védelme a felhőben. Ha új VMware virtuális gépeket ad hozzá egy védett mappához, azok automatikusan a lemezhez és a felhőhöz lesznek védve.
- Helyreállítási hatékonyság javítása a VMware alternatív hely helyreállításához.

## <a name="sql-2017-support"></a>SQL 2017-támogatás

A MABS v3 az SQL 2017-mel telepíthető MABS-adatbázisként. Frissítheti az SQL Servert az SQL 2016-ről az SQL 2017-re, vagy telepítheti azt frissen. Az SQL 2017 számítási feladatok biztonsági mentését is elvégezheti fürtözött és nem fürtözött környezetben is a MABS v3 használatával.

## <a name="windows-server-2019-support"></a>Windows Server 2019 támogatás

A MABS v3 telepíthető a Windows Server 2019-es verzióra. A MABS v3 és a WS2019 használatával az operációs rendszer a MABS v3 verzióra való telepítése vagy frissítése előtt, vagy a WS2016-re való frissítés után frissítheti az operációs RENDSZERét a WS2019-re.

A MABS v3 egy teljes kiadás, amely közvetlenül telepíthető a Windows Server 2016, a Windows Server 2019 vagy a MABS v2 verzióról is. A Backup Server v3 verzióra való frissítés előtt olvassa el a telepítési előfeltételek című részt.
További információ [a MABS telepítési](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package)/frissítési lépéseiről.

> [!NOTE]
>
> A MABS ugyanazzal a kóddal rendelkezik, mint a System Center Data Protection Manager. A MABS v3 egyenértékű a Data Protection Manager 1807-vel.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan készítheti elő a kiszolgálót, vagy megkezdheti a munkaterhelés védelmét:

- [A MABS v3 ismert problémái](backup-mabs-release-notes-v3.md)
- [A biztonsági mentési kiszolgáló munkaterhelésének előkészítése](backup-azure-microsoft-azure-backup.md)
- [Egy VMware-kiszolgáló biztonsági mentése a Backup Server használatával](backup-azure-backup-server-vmware.md)
- [Biztonsági másolat készítése a Backup Server használatával SQL Server](backup-azure-sql-mabs.md)
- [modern biztonsági másolati tárhely használata a Backup Serverrel](backup-mabs-add-storage.md)

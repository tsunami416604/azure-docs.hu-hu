---
title: Hibaelhárítási útmutató az SQL Server virtuális gépek az Azure Backup |} Microsoft Docs
description: Hibaelhárítási információk az Azure SQL Server virtuális gépek biztonsági mentéséről.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 1c87382c2aae70b022fb391f80f7c75b0a4e5fe6
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296210"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Készítsen biztonsági mentést az Azure SQL Server hibaelhárítása

Ez a cikk elhárításával kapcsolatban biztosít információkat (előzetes verzió) Azure SQL Server virtuális gépek védelmét.

## <a name="public-preview-limitations"></a>Nyilvános előzetes verzió korlátozásai

A nyilvános előzetes verzió korlátozásai megtekintéséhez tekintse meg a cikket, [az Azure SQL Server-adatbázis biztonsági mentése](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>Az SQL Server engedélyei

SQL Server adatbázis-védelem konfigurálása virtuális gépen, a **AzureBackupWindowsWorkload** bővítmény telepíteni kell, hogy a virtuális gép. A hibaüzenet, ha **UserErrorSQLNoSysadminMembership**, az azt jelenti, hogy az SQL-példány nem rendelkezik a szükséges biztonsági engedélyekkel. Ez a hiba elhárításához kövesse [nem Piactéri SQL virtuális gépek engedélyeinek beállítása](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Kapcsolatos hibák elhárítása

Olvassa el az alábbi táblázatok problémákat és az Azure SQL Server védelme során előforduló hibák elhárítása.

## <a name="backup-failures"></a>Biztonsági mentési hibák

Az alábbi táblázatok hibakód szerint vannak rendszerezve.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az SQL-adatbázis nem támogatja a kért biztonsági mentés típusát. | Akkor következik be, amikor az adatbázis helyreállítási modelljét nem engedélyezi a kért biztonsági mentés típusát. A hiba akkor fordulhat elő, a következő esetekben: <br/><ul><li>Egy adatbázis egy egyszerű helyreállítási modellt használó nem engedélyezi a napló biztonsági mentését.</li><li>Eltérés és a napló biztonsági mentések nem engedélyezettek a Master adatbázis.</li></ul>További részletekért tekintse meg a [SQL helyreállítási modell](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentációját. | Ha a napló biztonsági mentését az adatbázis egyszerű helyreállítási modell nem sikerül, próbálja meg a következő beállítások egyikét:<ul><li>Ha az adatbázis egyszerű helyreállítási módban van, tiltsa le a napló biztonsági mentést.</li><li>Használja a [SQL dokumentáció](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) adatbázis helyreállítási modelljét teljes vagy Tömegesen naplózott módosításához. </li><li> Ha nem kívánja módosítani a helyreállítási modell, és a szabványos házirendje, amely nem módosítható több adatbázisok biztonsági mentése, figyelmen kívül hagyja a hibát. A teljes és különbözeti biztonsági másolatok száma ütemezés fog működni. A napló-biztonságimásolatokat a rendszer kihagyja, ami ebben az esetben várt.</li></ul>Ha a Master adatbázis, és konfigurált különbségi vagy a napló biztonsági mentési, használja az alábbi műveletek közül:<ul><li>A portálhoz, és módosítsa a biztonsági mentési házirend-ütemezést a fő adatbázist, teljes használja.</li><li>Ha egy normál házirend, és nem módosítható több adatbázisok biztonsági mentése, figyelmen kívül hagyja a hibát. A teljes biztonsági mentés ütemezése / fog működni. Eltérés vagy a napló biztonsági mentések nem történik meg, amely ebben az esetben várt.</li></ul> |
| Művelet szakítva, mert egy ütköző művelet már futott ugyanazon az adatbázison. | Tekintse meg a [kapcsolatos blogbejegyzést biztonsági mentése és visszaállítása korlátozások](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) , amelyek párhuzamosan futnak.| [SQL Server Management Studio (SSMS) használatával figyelheti a biztonsági mentési feladatok.](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) Ha az ütköző művelet sikertelen lesz, indítsa újra a műveletet.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| SQL-adatbázis nem létezik. | Az adatbázis-t törölték vagy átnevezték. | <ul><li>Ellenőrizze, ha az adatbázis véletlenül törölték vagy átnevezték.</li><li>Ha az adatbázis véletlenül törölt, biztonsági mentést, a folytatáshoz az adatbázis visszaállítása az eredeti helyre.</li><li>Ha törli az adatbázist, és nem kell jövőbeli biztonsági mentések, majd a Recovery Services-tároló az [stop biztonsági mentés, a "Törlés/adatbázis megőrzése"](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms).</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Napló lánc megszakad. | Az adatbázis vagy a virtuális gép biztonsági mentése egy másik biztonsági mentési megoldást, amely a naplózási láncban csonkolja használatával.|<ul><li>Ellenőrizze, hogy egy másik biztonsági mentési megoldás vagy parancsfájl használatban van. Ha igen, akkor állítsa le a többi biztonsági mentési megoldás. </li><li>Ha a biztonsági mentés az ad hoc napló biztonsági mentését, indítható el egy teljes biztonsági mentés egy új naplóláncában elindításához. Az ütemezett naplóalapú biztonsági mentések nincs szükség beavatkozásra, az Azure Backup szolgáltatás automatikusan elindítja a teljes biztonsági mentés a probléma megoldásához.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Azure biztonsági mentés nem tud csatlakozni az SQL-példány. | Azure biztonsági mentés nem tud kapcsolódni az SQL-példány. | Az Azure portál hiba menüjében a további részletek segítségével az alapvető okok szűkítéséhez. Tekintse meg [SQL biztonsági mentési hibaelhárítási](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) és javítja a hibát.<br/><ul><li>Ha az alapértelmezett SQL-beállítások nem teszik lehetővé a távoli kapcsolatokat, módosítsa a beállításokat. Tekintse meg az alábbi hivatkozások a beállítások módosításához.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Ha bejelentkezési problémák vannak, tekintse meg az alábbi hivatkozások oldhatja meg a problémát:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Első teljes biztonsági mentést az adatforrásra vonatkozó hiányzik. | Az adatbázis teljes biztonsági mentés hiányzik. Naplófájl és különbségi biztonsági mentések szülőjét, hogy egy teljes biztonsági mentést, így a teljes biztonsági mentést kell tenni ahhoz, hogy kiváltsa különbözeti vagy biztonsági másolatok jelentkezzen. | Egy teljes biztonsági mentés alkalmi kiváltani.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Nem lehet biztonsági másolatok készítéséhez, az adatforrás tranzakciónaplója megtelt. | Az adatbázis tranzakciós naplók területe megtelt. | A probléma megoldásához, tekintse meg a [SQL dokumentáció](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Az SQL-adatbázis nem támogatja a kért biztonsági mentés típusát. | Mindig a rendelkezésre állási csoport másodlagos replikák teljes és különbözeti biztonsági másolatok nem támogatottak. | <ul><li>Elindul az ad hoc biztonsági mentés, ha indítható el, a biztonsági mentést végrehajtó elsődleges csomóponton.</li><li>Ha a biztonsági mentési házirend ütemezett, győződjön meg arról, hogy regisztrálva van-e az elsődleges csomópont. A csomópont regisztrálni [kövesse a következő leírást Fedezze fel az SQL Server-adatbázis](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> | 

## <a name="restore-failures"></a>Állítsa vissza a hibák

A következő hibakódok láthatók, ha a helyreállítási feladat sikertelen.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite 

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A célként megadott helyen már van azonos nevű adatbázis | Helyreállítási célhely már van azonos nevű adatbázis.  | <ul><li>Változtassa meg a célként megadott adatbázis nevét</li><li>Másik lehetőségként a kényszerített felülírási lehetőséget a Helyreállítás lap</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Visszaállítás sikertelen volt, mert az adatbázis nem tudta offline állapotba. | A visszaállítás közben, cél-adatbázist vissza kell offline állapotba kerül. Azure biztonsági mentés nincs képes offline ezeket az adatokat. | Az Azure portál hiba menüjében a további részletek segítségével az alapvető okok szűkítéséhez. További információkért lásd: a [SQL dokumentáció](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| A kiszolgáló ujjlenyomattal rendelkező tanúsítvány nem található a célszámítógépen. | A Master adatbázis a cél-példányon nem tartalmaz egy érvényes titkosítási ujjlenyomatot. | Importálja a forráspéldányon a célpéldányon használható érvényes tanúsítvány-ujjlenyomatot. |

## <a name="registration-failures"></a>Regisztrációs hibák

Az alábbi hibakódok a eszközregisztrációs hibák vannak.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError 

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| SQL Always On rendelkezésre állási csoportnak a biztonsági mentési beállítás nem teljesíthető, mivel egyes csomópontokat a következő rendelkezésre állási csoport nem regisztrált. | Biztonsági mentések végrehajtásához szükséges csomópontok nincsenek regisztrálva, vagy nem érhetők el. | <ul><li>Győződjön meg arról, hogy az adatbázis biztonsági mentéshez szükséges összes csomópontjának regisztrált és megfelelő, és próbálkozzon újra a művelettel.</li><li>Biztonsági mentési beállítás módosítása SQL Always On rendelkezésre állási csoportnak.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| SQL server virtuális gép leáll, és az Azure Backup szolgáltatás nem érhető el. | Virtuális gép le van állítva. | Győződjön meg arról, hogy az SQL server fut-e. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Hibaüzenet | Lehetséges okok | Javasolt művelet |
|---|---|---|
| Az Azure Backup szolgáltatás Azure virtuális gép vendégügynökének használja a biztonsági mentés, de a vendégügynök nem érhető el a cél kiszolgálón. | A vendégügynök nem engedélyezett, vagy nem megfelelő | [Telepítse a virtuális gép vendégügynökének](../virtual-machines/extensions/agent-windows.md) manuálisan. |

## <a name="next-steps"></a>További lépések

Az SQL Server virtuális gépen (nyilvános előzetes verzió) Azure biztonsági mentéssel kapcsolatos további információkért lásd: [Azure biztonsági mentés SQL virtuális gépek (nyilvános előzetes verzió)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
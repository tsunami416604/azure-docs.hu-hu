---
title: Az Azure SQL Database automatikus, georedundáns biztonsági mentés |} A Microsoft Docs
description: SQL Database automatikusan hoz létre a helyi adatbázis biztonsági mentésének néhány perces időközönként és georedundancia Azure írásvédett georedundáns tárolást használ.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: fcd1868c1b17d6c1ad895757b32293d0abc79a24
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731566"
---
# <a name="automated-backups"></a>Automatikus biztonsági mentések

Az SQL Database automatikusan létrehozza, melyek folyamatosan 7 – 35 nap közötti, és Azure írásvédett georedundáns tárolás (RA-GRS) használ, győződjön meg arról, hogy azok megmaradnak akkor is, ha nem érhető el az adatközpontot. Ezek a biztonsági másolatok jönnek létre automatikusan, és külön díj nélkül. Nem kell semmit, így fordulhat elő, és meg is [módosítsa a biztonsági mentések megőrzési időszaka](#how-to-change-the-pitr-backup-retention-period). Adatbázisok biztonsági mentése bármely üzleti folytonossági és vészhelyreállítási stratégia alapvető részét képezik, mert azok megvédheti adatait a véletlen adatsérülések vagy -törlések. Ha a biztonsági szabályokat, hogy a biztonsági másolatok elérhetők hosszabb időt (akár 10 év), konfigurálhat egy [hosszú távú megőrzés](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Mi az SQL-adatbázis biztonsági másolatát

Az SQL Database az SQL Server-technológia segítségével hozza létre [teljes](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server), [különbözeti](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server), és [tranzakciónapló](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) a jelen időponthoz – biztonsági másolatok visszaállítása (PITR). A tranzakciónapló biztonsági mentései általában 5 – 10 percenként történik, és a különbözeti biztonsági mentések általánosan 12 óránként akkor is a gyakoriságot, a számítási méret- és adatbázis-tevékenység mennyisége alapján. Teljes, különbségi és a tranzakciós napló biztonsági mentések lehetővé teszik egy adott – időponthoz ugyanarra a kiszolgálóra, amelyen az adatbázis egy adatbázis helyreállításához. A biztonsági mentések tárolódnak az RA-GRS tárolási blobokban, amely replikálja a rendszer egy [párosított adatközpontba](../best-practices-availability-paired-regions.md) Protection adatok adatközponti üzemkimaradások esetére. Ha az adatbázis visszaállításához a a szolgáltatás kitalálja, hogy melyik teljes, különbségi és a tranzakciós napló biztonsági mentéseket kell állítani.

Ezek a biztonsági mentések használhatók:

- Adatbázis visszaállítása egy-időponthoz a megőrzési időn belül. Ez a művelet létrehoz egy új adatbázist az eredeti adatbázissal megegyező kiszolgálóra.
- Törölt adatbázis visszaállítása az idő, azt törölték, vagy a megőrzési időtartamon belül bármikor. A törölt adatbázisok csak állítható vissza ugyanazon a kiszolgálón, ahol az eredeti adatbázis lett létrehozva.
- Adatbázis visszaállítása egy másik földrajzi régióban. A GEO-visszaállítás lehetővé teszi egy földrajzi katasztrófa utáni helyreállítás, amikor nem fér hozzá a kiszolgálóhoz és adatbázishoz. Ez létrehoz egy új adatbázist a világ bármely pontján található bármely meglévő Server.
- Adatbázis visszaállítása egy adott hosszú távú biztonsági másolatból, ha az adatbázis konfigurálva van a hosszú távú adatmegőrzési (LTR). Az LTR lehetővé teszi az adatbázis megfelelőségi kérelem teljesítéséhez kívánja lefuttatni, vagy az alkalmazás régi verziója, régebbi verzióját. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).
- A visszaállítás végrehajtásához lásd: [adatbázis visszaállítás biztonsági mentésből](sql-database-recovery-using-backups.md).

> [!NOTE]
> Az Azure storage-ban az előfizetési időszak *replikációs* fájlok másolása egyik helyről egy másikra hivatkozik. SQL *adatbázis-replikáció* több másodlagos adatbázis elsődleges adatbázis szinkronizálva tartja hivatkozik.

## <a name="how-long-are-backups-kept"></a>Mennyi ideig biztonsági mentések őrzi meg

Minden egyes SQL-adatbázisokban egy alapértelmezett biztonsági másolat megőrzési idejének 7 és 35 nap közötti, amely a vásárlási modell és a szolgáltatási rétegben függ. Az SQL Database-kiszolgáló az adatbázis biztonsági másolatának megőrzési ideje frissítheti. További információkért lásd: [módosítsa biztonsági mentések megőrzési időszaka](#how-to-change-the-pitr-backup-retention-period).

Ha töröl egy adatbázist, az SQL Database biztosítják a biztonsági másolatok olvasásainál, online adatbázis megegyező módon. Például ha törli egy hét napos megőrzési idővel rendelkező alapszintű adatbázis, egy biztonsági másolatot, amely négy napnál régebbi mentése további három nappal.

Ha szeretné megőrizni a biztonsági mentéseket hosszabb a maximális adatmegőrzési időtartam, módosíthatja az adatbázis hozzáadása egy vagy több hosszú távú megőrzési időtartamú biztonsági másolat tulajdonságai. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Ha törli az Azure SQL server, SQL-adatbázisokat üzemeltető, az összes rugalmas készletek és adatbázisok, a kiszolgálóhoz tartozó is törlődik, és nem állítható helyre. Kiszolgáló törlése nem állítható vissza. De ha konfigurálta a hosszú távú adatmegőrzés, az adatbázisok LTR biztonsági másolatainak nem törlődik, és ezeket az adatbázisokat vissza tudja állítani.

### <a name="default-backup-retention-period"></a>Alapértelmezett biztonsági másolatainak megőrzési ideje

#### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

A DTU-alapú vásárlási modell használatával létrehozott adatbázis alapértelmezett megőrzési időtartama a szolgáltatási rétegben függ:

- Alapszintű szolgáltatásszint 1 hét.
- Standard szintű szolgáltatáscsomagban 5 hétre.
- Prémium szintű szolgáltatási szinten 5 hétre.

#### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

Ha használja a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md), az alapértelmezett biztonsági másolat megőrzési idejének 7 nap (egyetlen, a készletezett és adatbázisok példány). Az összes Azure SQL-adatbázis (önálló, készletezett, és a példányok adatbázisai, akkor is [módosítsa a biztonsági másolat megőrzési idejének 35 napon belül](#how-to-change-the-pitr-backup-retention-period).

> [!WARNING]
> Ha csökkenti az aktuális megőrzési időszak, az összes meglévő biztonsági másolatok, az új megőrzési időszaknál korábbi már nem érhetők el. Aktuális megőrzési időszakán növeli, ha az SQL Database megtartja a meglévő biztonsági másolatok a hosszabb adatmegőrzési idő eléréséig.

## <a name="how-often-do-backups-happen"></a>Milyen gyakran történjen a biztonsági mentéseket

### <a name="backups-for-point-in-time-restore"></a>Biztonsági másolatok pont kötött visszaállításhoz

Az SQL Database időponthoz visszaállítást (PITR) önkiszolgáló automatikusan létrejön egy teljes biztonsági mentés, a különbségi biztonsági mentés és a tranzakciónaplók biztonsági mentését támogatja. Teljes adatbázis biztonsági mentését hetente jön létre, adatbázis különbözeti biztonsági mentések általánosan 12 óránként jön létre, és tranzakciós naplók biztonsági másolatát általában jönnek létre 5-10 percenként az a gyakoriságot, a számítási méret- és adatbázis-tevékenység mennyisége alapján. Az első teljes biztonsági mentés van ütemezve, egy adatbázis létrehozása után azonnal. Ez általában 30 percen belül befejeződik, de eltarthat hosszabb, amikor az adatbázis jelentős méretű. Ha például a kezdeti biztonsági mentés több időt vesz igénybe a visszaállított adatbázis vagy az adatbázis-másolat. Az első teljes biztonsági mentés után minden további biztonsági mentés ütemezett automatikus és csendes a háttérben kezeli. A pontos időzítés az összes adatbázis biztonsági mentés az SQL Database szolgáltatás határozza meg, a rendszer teljes számítási feladatnak egyenlegének.

A PITR a biztonsági mentések georedundáns és által védett [Azure Storage-régiók közti replikáció](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

További információkért lásd: [-időponthoz visszaállítása](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Biztonsági másolatok hosszú távú megőrzésének

Egyetlen vagy készletezett adatbázisok teljes biztonsági mentések konfigurálása hosszú távú megőrzésének (LTR) lehetőséget az Azure Blob storage akár 10 évig kínálnak. Ha LTR-szabályzat engedélyezve van, a heti teljes biztonsági mentés automatikusan átmásolja egy másik RA-GRS-tárolóba. A különböző megfelelőségi követelménynek megfelel, heti, havi és/vagy éves biztonsági mentések különböző megőrzési időtartamú választhat. A tárhelyhasználat biztonsági mentéseket és a megőrzési időszak a kiválasztott gyakorisága függ. Használhatja a [LTR díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=sql-database) LTR tárolási költségek becsléséhez.

PITR, például az LTR biztonsági mentések rendszer georedundáns és által védett [Azure Storage-régiók közti replikációs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

További információkért lásd: [hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Tárolási költségek
Az adatbázisok 7 napi automatikus biztonsági másolata alapértelmezés szerint RA-GRS Standard blobtárolóra lesz másolva. A tárolót a heti teljes biztonsági mentésekhez, a napi differenciális biztonsági mentésekhez, illetve a tranzakciónaplók 5 percenként másolt biztonsági másolataihoz használja a rendszer. A tranzakciónapló mérete attól függ, hogy az adatbázis a változási gyakoriság. Az adatbázis méretének 100%-ával egyenlő minimális tárhelyet többletdíj felszámolása nélkül biztosítjuk. A biztonsági mentési tár ezt meghaladó használata GB/hó díjszabási alapon történik.

Tárolási díjszabás kapcsolatos további információkért lásd: a [díjszabás](https://azure.microsoft.com/pricing/details/sql-database/single/) lapot. 

## <a name="are-backups-encrypted"></a>Titkosított biztonsági mentés

Ha az adatbázis a TDE van titkosítva, a biztonsági másolatok titkosítása automatikusan megtörténik az LTR biztonsági másolatokkal együtt az inaktív. Ha a TDE engedélyezve van egy Azure SQL database, a biztonsági másolatok is titkosítást kapnak. Minden új Azure SQL-adatbázisok a TDE alapértelmezés szerint engedélyezve vannak konfigurálva. A TDE további információkért lásd: [az Azure SQL Database transzparens adattitkosítási](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Hogyan biztosítja a Microsoft biztonsági mentési integritását

Töltheti az Azure SQL Database mérnöki csapat automatikusan teszteli, automatikus biztonsági adatbázismentés az adatbázisok visszaállítása a szolgáltatás között. Változónevét adatbázisokat is kapnak a sértetlenségi ellenőrzések DBCC CHECKDB utasítás segítségével. Az integritás-ellenőrzése során talált problémákat a mérnöki csapathoz egy riasztást eredményez. Az Azure SQL Database adatintegritásának kapcsolatos további információkért lásd: [adatok integritásának megőrzése, az Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Hogyan tegye hatással megfelelőségi automatikus biztonsági másolatokat

Ha az adatbázis telepít át az alapértelmezett PITR a megőrzési 35 nap a DTU-alapú szolgáltatási szintről a Virtuálismag-alapú szolgáltatásréteghez, PITR a megőrzési megőrzi a rendszer győződjön meg arról, hogy az alkalmazás adat-helyreállítási házirend nem sérül. Ha az alapértelmezett megőrzési sem felel meg a megfelelőségi előírásokat, módosíthatja a PITR a megőrzési időszak a PowerShell vagy REST API használatával. Lásd: [módosítsa biztonsági mentések megőrzési időszaka](#how-to-change-the-pitr-backup-retention-period) további részletekért.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>A PITR a biztonsági másolat megőrzési idejének módosítása

Módosíthatja az alapértelmezett PITR a biztonsági másolat megőrzési idejének az Azure Portal, PowerShell vagy a REST API használatával. A támogatott értékek a következők: 7, 14, 21, 28 és 35 nap. Az alábbi példák bemutatják, hogyan PITR a Megtartás módosítása 28 nap.

> [!NOTE]
> Ezen API-k csak negatív hatással lesz a PITR a megőrzési időtartam. Ha az adatbázis konfigurált balról jobbra, ez nem érinti. Az LTR-megőrzési időszak módosításával kapcsolatos további információkért lásd: [hosszú távú megőrzés](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Az Azure portal használatával PITR a biztonsági másolat megőrzési idejének módosítása

Az Azure Portallal PITR a biztonsági másolat megőrzési idejének módosításához nyissa meg a kiszolgáló objektum amelynek megőrzési ideje módosítani a portálon, és válassza ki a megfelelő beállítást szeretné melyik kiszolgáló objektumon, módosít alapján.

#### <a name="change-pitr-for-a-sql-database-server"></a>Egy SQL Database-kiszolgáló PITR módosítása

![Változás PITR a az Azure portal](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Felügyelt példány PITR módosítása

![Változás PITR a az Azure portal](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell-lel PITR a biztonsági másolat megőrzési idejének módosítása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Változás PITR a megőrzési időszak a REST API használatával

#### <a name="sample-request"></a>Kérésminta

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>A kérelem törzse

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Mintaválasz

Állapotkód: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

További információkért lásd: [biztonsági mentés megőrzési REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>További lépések

- Adatbázisok biztonsági mentése bármely üzleti folytonossági és vészhelyreállítási stratégia alapvető részét képezik, mert azok megvédheti adatait a véletlen adatsérülések vagy -törlések. A többi Azure SQL Database üzletmenet-folytonossági megoldások kapcsolatos további információkért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
- Visszaállítását egy időpontra az Azure portal használatával, lásd: [database visszaállítása egy időpontra az Azure portal használatával](sql-database-recovery-using-backups.md).
- Visszaállítását egy időpontra a PowerShell használatával, lásd: [adatbázis visszaállítása egy időpontra a PowerShell használatával](scripts/sql-database-restore-database-powershell.md).
- Konfigurálhatja, kezelheti és visszaállítani a hosszú távú megőrzésének automatikus biztonsági másolatokat az Azure Blob storage, az Azure portal használatával, lásd: [az Azure portal használatával hosszú távú megőrzésének kezelése](sql-database-long-term-backup-retention-configure.md).
- Konfigurálhatja, kezelheti és visszaállítani a hosszú távú megőrzésének automatikus biztonsági mentések PowerShell-lel az Azure Blob storage-ban, lásd: [PowerShell-lel hosszú távú megőrzésének kezelése](sql-database-long-term-backup-retention-configure.md).

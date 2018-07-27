---
title: Az Azure SQL Database automatikus, georedundáns biztonsági mentés |} A Microsoft Docs
description: SQL Database automatikusan hoz létre a helyi adatbázis biztonsági mentésének néhány perces időközönként és georedundancia Azure írásvédett georedundáns tárolást használ.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: Active
ms.date: 07/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 1ddc663e6a7dc2d09a140b148c5297299d30d016
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262847"
---
# <a name="learn-about-automatic-sql-database-backups"></a>További információ az automatikus SQL-adatbázisok biztonsági mentése

SQL Database automatikusan létrehozza és Azure írásvédett georedundáns tárolás (RA-GRS) használ a georedundáns tárolás. Ezek a biztonsági másolatok jönnek létre automatikusan, és külön díj nélkül. Nem kell semmit ennek eléréséhez. Adatbázisok biztonsági mentése bármely üzleti folytonossági és vészhelyreállítási stratégia alapvető részét képezik, mert azok megvédheti adatait a véletlen adatsérülések vagy -törlések. Ha a biztonsági szabályok a, hogy a biztonsági másolatok elérhetők hosszabb időt igényelnek, konfigurálhatja a hosszú távú adatmegőrzési házirend. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Mi az SQL-adatbázis biztonsági másolatát?

Az SQL Database az SQL Server-technológia segítségével hozza létre [teljes](https://msdn.microsoft.com/library/ms186289.aspx), [különbözeti](http://msdn.microsoft.com/library/ms175526.aspx), és [tranzakciónapló](https://msdn.microsoft.com/library/ms191429.aspx) a jelen időponthoz – biztonsági másolatok visszaállítása (PITR). A tranzakciónapló biztonsági mentései általában 5 – 10 percenként történik, és a különbözeti biztonsági mentések általánosan 12 óránként akkor is a gyakoriságot, a teljesítményszintet és az adatbázis-tevékenység mennyisége alapján. Tranzakciónapló biztonsági mentéseivel, teljes és különbségi biztonsági másolatok, az adatbázis visszaállítása egy adott – időponthoz ugyanarra a kiszolgálóra, amelyen az adatbázis teszi lehetővé. Ha az adatbázis visszaállításához a a szolgáltatás kitalálja, hogy melyik teljes, különbségi és a tranzakciós napló biztonsági mentéseket kell állítani.


Ezek a biztonsági mentések használhatók:

* Adatbázis visszaállítása egy-időponthoz a megőrzési időn belül. Ez a művelet létrehoz egy új adatbázist az eredeti adatbázissal megegyező kiszolgálóra.
* Törölt adatbázis visszaállítása az idő, azt törölték, vagy a megőrzési időtartamon belül bármikor. A törölt adatbázisok csak állítható vissza ugyanazon a kiszolgálón, ahol az eredeti adatbázis lett létrehozva.
* Adatbázis visszaállítása egy másik földrajzi régióban. Ez lehetővé teszi, hogy egy földrajzi katasztrófa utáni helyreállítás, amikor nem fér hozzá a kiszolgálóhoz és adatbázishoz. Ez létrehoz egy új adatbázist a világ bármely pontján található bármely meglévő Server. 
* Adatbázis visszaállítása egy adott hosszú távú biztonsági másolatból, ha az adatbázis konfigurálva van a hosszú távú adatmegőrzési (LTR). Ez lehetővé teszi, hogy állítsa vissza az adatbázist megfelelőségi kérelem teljesítéséhez kívánja lefuttatni, vagy az alkalmazás régi verziója, régebbi verzióját. Lásd: [hosszú távú megőrzés](sql-database-long-term-retention.md).
* A visszaállítás végrehajtásához lásd: [adatbázis visszaállítás biztonsági mentésből](sql-database-recovery-using-backups.md).

> [!NOTE]
> Az Azure storage-ban az előfizetési időszak *replikációs* fájlok másolása egyik helyről egy másikra hivatkozik. SQL *adatbázis-replikáció* több másodlagos adatbázis elsődleges adatbázis szinkronizálva tartja hivatkozik. 
> 

## <a name="how-long-are-backups-kept"></a>Mennyi ideig biztonsági mentések őrzi meg?
Minden egyes SQL-adatbázis biztonsági mentése tartalmaz egy alapértelmezett megőrzési időtartamot, amely az adatbázis szolgáltatási szintjét alapul, és nem egyezik meg a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md). Az adatbázis biztonsági másolatának megőrzési ideje frissítheti. Lásd: [módosítsa biztonsági mentések megőrzési időszaka](#how-to-change-backup-retention-period) további részletekért.

Ha töröl egy adatbázist, az SQL Database biztosítják a biztonsági másolatok olvasásainál, online adatbázis megegyező módon. Például ha törli egy hét napos megőrzési idővel rendelkező alapszintű adatbázis, egy biztonsági másolatot, amely négy napnál régebbi mentése további három nappal.

Ha szeretné megőrizni a biztonsági mentéseket hosszabb a maximális PITR a megőrzési időszak, módosíthatja az adatbázis hozzáadása egy vagy több hosszú távú megőrzési időtartamú biztonsági másolat tulajdonságai. Lásd: [hosszú távú adatmegőrzés](sql-database-long-term-retention.md) további részletekért.

> [!IMPORTANT]
> Ha törli az Azure SQL server, SQL-adatbázisokat üzemeltető, az összes rugalmas készletek és adatbázisok, a kiszolgálóhoz tartozó is törlődik, és nem állítható helyre. Kiszolgáló törlése nem állítható vissza. De ha konfigurálta a hosszú távú adatmegőrzés, az adatbázisok LTR biztonsági másolatainak nem törlődik, és ezeket az adatbázisokat vissza tudja állítani.

### <a name="pitr-retention-for-dtu-based-service-tiers"></a>DTU-alapú szolgáltatásszintek PITR a megőrzési ideje
A DTU-alapú vásárlási modell használatával létrehozott adatbázis alapértelmezett megőrzési időtartama a szolgáltatási rétegben függ:

* Alapszintű szolgáltatásszint 1 hét.
* Standard szintű szolgáltatáscsomagban 5 hétre.
* Prémium szintű szolgáltatási szinten 5 hétre.

Ha csökkenti az aktuális PITR a megőrzési időszak, az összes meglévő biztonsági másolatok, az új megőrzési időszaknál korábbi már nem lesz elérhető. 

Ha növeli a jelenlegi PITR a megőrzési időszak, az SQL Database megtartja a meglévő biztonsági másolatok mindaddig, amíg a hosszabb megőrzési időtartamot.

## <a name="how-often-do-backups-happen"></a>Milyen gyakran történjen a biztonsági mentéseket?
### <a name="backups-for-point-in-time-restore"></a>Biztonsági másolatok pont kötött visszaállításhoz
Az SQL Database időponthoz visszaállítást (PITR) önkiszolgáló automatikusan létrejön egy teljes biztonsági mentés, a különbségi biztonsági mentés és a tranzakciónaplók biztonsági mentését támogatja. Teljes adatbázis biztonsági mentését hetente jön létre, adatbázis különbözeti biztonsági mentések általánosan 12 óránként jön létre, és tranzakciós naplók biztonsági másolatát általában jönnek létre 5-10 percenként az a gyakoriságot, a teljesítményszintet és az adatbázis-tevékenység mennyisége alapján. Az első teljes biztonsági mentés van ütemezve, egy adatbázis létrehozása után azonnal. Ez általában 30 percen belül befejeződik, de eltarthat hosszabb, amikor az adatbázis jelentős méretű. Ha például a kezdeti biztonsági mentés több időt vesz igénybe a visszaállított adatbázis vagy az adatbázis-másolat. Az első teljes biztonsági mentés után minden további biztonsági mentés ütemezett automatikus és csendes a háttérben kezeli. A pontos időzítés az összes adatbázis biztonsági mentés az SQL Database szolgáltatás határozza meg, a rendszer teljes számítási feladatnak egyenlegének.

A PITR a biztonsági mentések georedundáns és által védett [Azure Storage-régiók közti replikáció](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

További információkért lásd: [-időponthoz visszaállítása](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Biztonsági másolatok hosszú távú megőrzésének
SQL Database teljes biztonsági mentések konfigurálása hosszú távú megőrzésének (LTR) lehetőséget kínál az Azure blob storage akár 10 évig. Ha LTR-szabályzat engedélyezve van, a heti teljes biztonsági mentés automatikusan átmásolja egy másik RA-GRS-tárolóba. A különböző megfelelőségi követelménynek megfelel, heti, havi és/vagy éves biztonsági mentések különböző megőrzési időtartamú választhat. A tárhelyhasználat biztonsági mentéseket és a megőrzési időszak a kiválasztott gyakorisága függ. Használhatja a [LTR díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=sql-database) LTR tárolási költségek becsléséhez. 

PITR, például az LTR biztonsági mentések rendszer georedundáns és által védett [Azure Storage-régiók közti replikációs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

További információkért lásd: [hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

## <a name="are-backups-encrypted"></a>Titkosított biztonsági mentés?

Ha az adatbázis a TDE van titkosítva, a biztonsági másolatok titkosítása automatikusan megtörténik az LTR biztonsági másolatokkal együtt az inaktív. Ha a TDE engedélyezve van egy Azure SQL database, a biztonsági másolatok is titkosítást kapnak. Minden új Azure SQL-adatbázisok a TDE alapértelmezés szerint engedélyezve vannak konfigurálva. A TDE további információkért lásd: [az Azure SQL Database transzparens adattitkosítási](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-do-automated-backups-impact-my-compliance"></a>Hogyan tegye automatikus biztonsági másolatok hatással van a megfelelőség?

Ha az adatbázis telepít át az alapértelmezett PITR a megőrzési 35 nap a DTU-alapú szolgáltatási szintről a Virtuálismag-alapú szolgáltatásréteghez, PITR a megőrzési megőrzi a rendszer győződjön meg arról, hogy az alkalmazás adat-helyreállítási házirend nem sérül. Ha az alapértelmezett megőrzési sem felel meg a megfelelőségi előírásokat, módosíthatja a PITR a megőrzési időszak a PowerShell vagy REST API használatával. Lásd: [módosítsa biztonsági mentések megőrzési időszaka](#how-to-change-backup-retention-period) további részletekért.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>Biztonsági másolat megőrzési idejének módosítása
Módosíthatja az alapértelmezett megőrzési REST API vagy a PowerShell használatával. A támogatott értékek a következők: 7, 14, 21, 28 és 35 nap. Az alábbi példák bemutatják, hogyan PITR a Megtartás módosítása 28 nap. 

> [!NOTE]
> Ezek API-k csak negatív hatással lesz a PITR a megőrzési időtartam. Ha az adatbázis konfigurált balról jobbra, ez nem érinti. Lásd: [hosszú távú adatmegőrzés](sql-database-long-term-retention.md) módosítása az LTR-megőrzési időszak részleteit.

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell-lel PITR a biztonsági másolat megőrzési idejének módosítása
```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```
> [!IMPORTANT]
> Az API verzió kezdve azurerm.SQL-hez PowerShell-modult tartalmaz [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview). 

### <a name="change-pitr-retention-period-using-rest-api"></a>Változás PITR a megőrzési időszak a REST API használatával
**Mintakérelem**
```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```
**Kérelem törzse**
```json
{
  "properties":{  
      "retentionDays":28
   }
}
```
**Mintaválasz**

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
Lásd: [biztonsági mentés megőrzési REST API-val](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies) további részletekért.

## <a name="next-steps"></a>További lépések

- Adatbázisok biztonsági mentése bármely üzleti folytonossági és vészhelyreállítási stratégia alapvető részét képezik, mert azok megvédheti adatait a véletlen adatsérülések vagy -törlések. A többi Azure SQL Database üzletmenet-folytonossági megoldások kapcsolatos további információkért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
- Visszaállítását egy időpontra az Azure portal használatával, lásd: [database visszaállítása egy időpontra az Azure portal használatával](sql-database-recovery-using-backups.md).
- Visszaállítását egy időpontra a PowerShell használatával, lásd: [adatbázis visszaállítása egy időpontra a PowerShell használatával](scripts/sql-database-restore-database-powershell.md).
- Konfigurálhatja, kezelheti és visszaállítani a hosszú távú megőrzésének automatikus biztonsági másolatokat az Azure Portalon az Azure blob storage-ban, lásd: [az Azure portal használatával hosszú távú megőrzésének kezelése](sql-database-long-term-backup-retention-configure.md).
- Konfigurálhatja, kezelheti és visszaállítani a hosszú távú megőrzésének automatikus biztonsági másolatokat az Azure-blog storage PowerShell-lel, tekintse meg [PowerShell-lel hosszú távú megőrzésének kezelése](sql-database-long-term-backup-retention-configure.md).

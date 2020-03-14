---
title: Adatbázis visszaállítása biztonsági másolatból
description: Ismerje meg az időponthoz való visszaállítást, amely lehetővé teszi, hogy akár 35 napig is visszaállítson egy Azure SQL Database-adatbázist.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268742"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Azure SQL Database helyreállítása automatikus adatbázis-biztonsági mentéssel

Alapértelmezés szerint a rendszer a Azure SQL Database biztonsági mentéseket földrajzilag replikált blob Storage-tárolóban (RA-GRS Storage-típus) tárolja. A következő beállítások érhetők el az adatbázis-helyreállításhoz az [automatikus adatbázis-biztonsági mentések](sql-database-automated-backups.md)használatával. A következőket teheti:

- Hozzon létre egy új adatbázist ugyanazon a SQL Database-kiszolgálón, amely a megőrzési időtartamon belül egy megadott időpontra lett helyreállítva.
- Hozzon létre egy adatbázist ugyanazon a SQL Database-kiszolgálón, amely a törölt adatbázis törlési idejére lett helyreállítva.
- Hozzon létre egy új adatbázist az ugyanabban a régióban található bármelyik SQL Database-kiszolgálón, amely a legutóbbi biztonsági másolatok pontjára lett helyreállítva.
- Hozzon létre egy új adatbázist bármely más régióban található SQL Database-kiszolgálón, amely a legutóbbi replikált biztonsági másolatok pontjára lett helyreállítva.

Ha [hosszú távú adatmegőrzést](sql-database-long-term-retention.md)állított be, akkor létrehozhat egy új adatbázist is a hosszú távú adatmegőrzési biztonsági másolatból bármely SQL Database-kiszolgálón.

> [!IMPORTANT]
> A meglévő adatbázisok nem írhatók felül a visszaállítás során.

A standard vagy prémium szintű szolgáltatási szintek használatakor az adatbázis-visszaállítás további tárolási költséget eredményezhet. Az extra költségek akkor merülnek fel, ha a visszaállított adatbázis maximális mérete nagyobb, mint a céladatbázis szolgáltatási szintjéhez és a teljesítmény szintjéhez tartozó tárterület mennyisége. A további tárterület részletes díjszabását a [SQL Database díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/sql-database/)tekintheti meg. Ha a felhasznált terület tényleges mennyisége kevesebb, mint a tárhelyek mennyisége, akkor ezt a többletköltséget elkerülheti, ha a maximális adatbázis méretét a befoglalt mennyiségre állítja.

## <a name="recovery-time"></a>Helyreállítás ideje

Az adatbázis automatikus biztonsági mentések használatával történő visszaállításának helyreállítási idejét számos tényező befolyásolja:

- Az adatbázis mérete.
- Az adatbázis számítási mérete.
- A tranzakciós naplók száma.
- Azon tevékenység mennyisége, amelyet újra kell játszani a visszaállítási pontra történő helyreállításhoz.
- A hálózati sávszélesség, ha a visszaállítás egy másik régióba történik.
- A megcélzott régióban feldolgozott egyidejű visszaállítási kérelmek száma.

Nagyméretű vagy nagyon aktív adatbázisok esetén a visszaállítás több órát is igénybe vehet. Ha egy régióban hosszabb ideig tartó leállás történik, előfordulhat, hogy a rendszer nagy számú geo-visszaállítási kérelmet indít el a vész-helyreállításhoz. Sok kérelem esetén az egyes adatbázisok helyreállítási ideje növekedhet. A legtöbb adatbázis-visszaállítás kevesebb, mint 12 óra alatt elvégezhető.

Egyetlen előfizetés esetében az egyidejű visszaállítási kérelmek száma korlátozott. Ezek a korlátozások az időponthoz kötött visszaállítások, a Geo-visszaállítások és a hosszú távú adatmegőrzési biztonsági mentés bármely kombinációjára érvényesek.

| | **A feldolgozás alatt álló egyidejű kérelmek maximális száma** | **A beküldött egyidejű kérelmek maximális száma** |
| :--- | --: | --: |
|Önálló adatbázis (előfizetés)|10|60|
|Rugalmas készlet (/készlet)|4|200|
||||

Nincs beépített módszer a teljes kiszolgáló visszaállítására. A feladat végrehajtásával kapcsolatos példát a [Azure SQL Database: teljes kiszolgáló helyreállítása](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)című témakörben talál.

> [!IMPORTANT]
> Az automatikus biztonsági mentéssel történő helyreállításhoz a SQL Server közreműködő szerepkör tagjának kell lennie az előfizetésben, vagy az előfizetés tulajdonosának kell lennie. További információt a [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md)című témakörben talál. A helyreállítást a Azure Portal, a PowerShell vagy a REST API használatával végezheti el. A Transact-SQL nem használható.

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A Azure Portal, a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/databases)használatával visszaállíthat egy önálló, készletezett vagy példány-adatbázist egy korábbi időpontra. A kérelem bármilyen szolgáltatási szintet vagy számítási méretet megadhat a visszaállított adatbázis számára. Győződjön meg arról, hogy elegendő erőforrása van azon a kiszolgálón, amelyhez az adatbázist állítja vissza. Ha elkészült, a Restore egy új adatbázist hoz létre az eredeti adatbázissal megegyező kiszolgálón. A visszaállított adatbázist a szolgáltatás szintjétől és a számítási mérettől függően a normál díjszabás szerint számítjuk fel. Az adatbázis visszaállítása után nem számítunk fel díjat.

Általában egy adatbázis visszaállítása egy korábbi helyre helyreállítás céljából. A visszaállított adatbázist felhasználhatja az eredeti adatbázis helyett, vagy adatforrásként használhatja az eredeti adatbázis frissítéséhez.

- **Adatbázis cseréje**

  Ha azt szeretné, hogy a visszaállított adatbázis helyettesítse az eredeti adatbázist, akkor meg kell adnia az eredeti adatbázis számítási méretét és a szolgáltatási szintet. Ezután átnevezheti az eredeti adatbázist, és visszaállíthatja az eredeti nevet a visszaállított adatbázisnak az [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-database) parancs használatával a T-SQL-ben.

- **Adathelyreállítás**

  Ha a visszaállított adatbázisból szeretne adatokat lekérni egy felhasználói vagy alkalmazáshiba általi helyreállításhoz, írnia kell és végre kell hajtania egy olyan adathelyreállítási parancsfájlt, amely kinyeri az adatokat a visszaállított adatbázisból, és az eredeti adatbázisra vonatkozik. Bár a visszaállítási művelet végrehajtása hosszú időt is igénybe vehet, a visszaállítási folyamat során az adatbázis-lista látható. Ha a visszaállítás során törli az adatbázist, a visszaállítási művelet meg lesz szakítva, és nem kell fizetnie az adatbázisnak, amely nem végezte el a visszaállítást.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Időponthoz való visszaállítás Azure Portal használatával

Egy SQL-adatbázis vagy-példány adatbázisát a Azure Portal visszaállítani kívánt adatbázis áttekintés paneljéről állíthatja helyre egy adott időpontra.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Egyetlen vagy készletezett adatbázis helyreállításához a Azure Portal használatával nyissa meg az adatbázis-áttekintés lapot, és válassza a **visszaállítás** lehetőséget az eszköztáron. Válassza ki a biztonsági mentési forrást, és válassza ki azt az időponthoz tartozó biztonsági mentési pontot, amelyből új adatbázist kíván létrehozni. 

  ![Az adatbázis-visszaállítási lehetőségek képernyőképe](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

Felügyelt példány adatbázisának egy időpontra történő helyreállításához a Azure Portal segítségével nyissa meg az adatbázis-áttekintés lapot, és válassza a **visszaállítás** lehetőséget az eszköztáron. Válassza ki azt az időponthoz tartozó biztonsági mentési pontot, amelyből új adatbázist kíván létrehozni. 

  ![Az adatbázis-visszaállítási lehetőségek képernyőképe](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Az adatbázis biztonsági másolatból való programozott visszaállításával kapcsolatban lásd: a [helyreállítás programozott módon történő végrehajtása automatizált biztonsági mentéssel](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Adatbázis-visszaállítás törölve

A törölt adatbázist visszaállíthatja a törlési időre vagy egy korábbi időpontra ugyanazon a SQL Database kiszolgálón vagy ugyanazon a felügyelt példányon. Ezt a Azure Portal, a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)vagy a [Rest (CreateMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)használatával végezheti el. Egy törölt adatbázist úgy állíthat vissza, hogy létrehoz egy új adatbázist a biztonsági mentésből.

> [!IMPORTANT]
> Ha töröl egy Azure SQL Database-kiszolgálót vagy felügyelt példányt, az összes adatbázisa is törlődik, és nem állítható helyre. A törölt kiszolgálók vagy a felügyelt példányok nem állíthatók vissza.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Az adatbázis-visszaállítás törölve a Azure Portal használatával

A törölt adatbázisokat a kiszolgáló és a példány erőforrásból a Azure Portalból állíthatja vissza.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Egy vagy készletezett törölt adatbázis helyreállításához a Azure Portal használatával nyissa meg a kiszolgáló áttekintése lapot, és válassza a **törölt adatbázisok**lehetőséget. Válassza ki a visszaállítani kívánt törölt adatbázist, és írja be az új adatbázis nevét, amely a biztonsági másolatból visszaállított adatokkal lesz létrehozva.

  ![Képernyőkép a törölt Azure SQL Database-adatbázis visszaállításáról](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

Felügyelt adatbázis helyreállításához a Azure Portal segítségével nyissa meg a felügyelt példányok áttekintése lapot, és válassza a **törölt adatbázisok**lehetőséget. Válassza ki a visszaállítani kívánt törölt adatbázist, és írja be az új adatbázis nevét, amely a biztonsági másolatból visszaállított adatokkal lesz létrehozva.

  ![Képernyőkép a törölt Azure SQL-példány-adatbázis visszaállításáról](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Adatbázis-visszaállítás törölve a PowerShell használatával

A következő szkriptek segítségével visszaállíthatja a törölt adatbázist Azure SQL Database és felügyelt példányok számára a PowerShell használatával.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

A törölt Azure SQL Database-adatbázisok visszaállítását bemutató minta PowerShell-parancsfájlért lásd: [SQL Database-adatbázis visszaállítása a PowerShell használatával](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

A törölt példányok adatbázisának visszaállítását bemutató minta PowerShell-parancsfájlért lásd: [törölt adatbázis visszaállítása felügyelt példányon a PowerShell használatával](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> A törölt adatbázisok programozott visszaállításával kapcsolatban lásd: a [helyreállítás programozott módon történő végrehajtása automatizált biztonsági mentéssel](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Georedundáns helyreállítás

Bármely Azure-régióban lévő kiszolgálón visszaállíthat egy SQL-adatbázist a legutóbbi földrajzilag replikált biztonsági mentésből. A Geo-visszaállítás a forrásként egy földrajzilag replikált biztonsági mentést használ. Akkor is kérheti a Geo-visszaállítást, ha az adatbázis vagy az adatközpont egy leállás miatt nem érhető el.

A Geo-visszaállítás az alapértelmezett helyreállítási lehetőség, ha az adatbázis nem érhető el az üzemeltetési régióban lévő incidens miatt. Az adatbázist bármely másik régióban található kiszolgálón visszaállíthatja. A biztonsági másolat készítése és az egy másik régióban lévő Azure-blobba való földrajzi replikálása között késés történik. Ennek eredményeképpen a visszaállított adatbázis akár egy óráig is elvégezhető az eredeti adatbázis mögött. Az alábbi ábrán egy másik régió utolsó elérhető biztonsági másolatából származó adatbázis-visszaállítás látható.

![Geo-visszaállítási ábra](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-visszaállítás a Azure Portal használatával

A Azure Portal létrehozhat egy új, egy vagy felügyelt példány-adatbázist, és kiválaszthat egy elérhető geo-visszaállítási biztonsági mentést. Az újonnan létrehozott adatbázis tartalmazza a Geo-visszaállított biztonsági mentési adatkészleteket.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Ha egyetlen SQL-adatbázist szeretne geo-helyre visszaállítani a régió és a kiszolgáló Azure Portal közül, kövesse az alábbi lépéseket:

1. Az **irányítópulton**válassza a **Hozzáadás** > **SQL Database létrehozása**lehetőséget. Az **alapvető beállítások** lapon adja meg a szükséges adatokat.
2. Válassza a **További beállítások**lehetőséget.
3. A **meglévő adatfelhasználáshoz**válassza a **biztonsági mentés**lehetőséget.
4. A **biztonsági mentéshez**válassza ki a biztonsági mentést az elérhető geo-visszaállítási biztonsági mentések listájából.

    ![Képernyőkép a SQL Database létrehozásának lehetőségeiről](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Fejezze be az új adatbázis biztonsági másolatból való létrehozásának folyamatát. Az egyetlen Azure SQL Database-adatbázis létrehozásakor a visszaállított geo-visszaállítási biztonsági másolat szerepel.

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

Felügyelt példányok adatbázisának geo-visszaállítása a Azure Portal egy meglévő felügyelt példányra egy tetszőleges régióban, válassza ki azt a felügyelt példányt, amelyen vissza kívánja állítani az adatbázist. Kövesse az alábbi lépéseket:

1. Válassza az **új adatbázis**lehetőséget.
2. Írja be a kívánt adatbázis nevét.
3. A **meglévő adat használata**területen válassza a **biztonsági mentés**lehetőséget.
4. Válasszon ki egy biztonsági másolatot az elérhető geo-visszaállítási biztonsági másolatok listájából.

    ![Képernyőfelvétel az új adatbázis-beállításokról](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Fejezze be az új adatbázis létrehozásának folyamatát. A példány-adatbázis létrehozásakor a visszaállított geo-visszaállítási biztonsági másolatot tartalmazza.

### <a name="geo-restore-by-using-powershell"></a>Geo-visszaállítás a PowerShell használatával

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Egy olyan PowerShell-parancsfájlhoz, amely bemutatja, hogyan végezhető el a Geo-visszaállítás egyetlen SQL-adatbázishoz, lásd: a [PowerShell használata egy Azure SQL-alapú önálló adatbázis visszaállítására egy korábbi időpontra](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

A felügyelt példányok adatbázisának geo-visszaállítását bemutató PowerShell-szkriptet a következő témakörben talál: a [felügyelt példányok adatbázisának visszaállítása másik földrajzi régióra a PowerShell használatával](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Geo-visszaállítási megfontolások

A Geo-másodlagos adatbázison nem hajtható végre időponthoz való visszaállítás. Csak egy elsődleges adatbázison végezhető el. További információ a Geo-visszaállítás a leállás utáni helyreállításhoz való használatáról: [áramkimaradás](sql-database-disaster-recovery.md)miatti helyreállítás.

> [!IMPORTANT]
> A Geo-visszaállítás a SQL Databaseban rendelkezésre álló legalapvetőbb vész-helyreállítási megoldás. Az automatikusan létrehozott geo-replikált biztonsági mentéseket a helyreállítási időkorlát (RPO) értéke 1 óra, a becsült helyreállítási idő pedig legfeljebb 12 óra. Nem garantálja, hogy a célként megadott régió a regionális leállás után helyreállíthatja az adatbázisokat, mivel a kereslet jelentős növekedése várható. Ha az alkalmazás viszonylag kis adatbázisokat használ, és nem kritikus fontosságú a vállalat számára, a Geo-visszaállítás egy megfelelő vész-helyreállítási megoldás. Az üzleti szempontból kritikus fontosságú alkalmazások esetében, amelyeknek nagy adatbázisokra van szükségük, és biztosítaniuk kell az üzletmenet folytonosságát, használja az [automatikus feladatátvételi csoportokat](sql-database-auto-failover-group.md). Sokkal alacsonyabb RPO és helyreállítási idő célkitűzést kínál, és a kapacitás mindig garantált. További információ az üzletmenet-folytonossági lehetőségekről: [az üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>A helyreállítás programozott módon történő végrehajtása automatizált biztonsági másolatok használatával

A helyreállításhoz Azure PowerShell vagy a REST API is használhatja. A következő táblázatok ismertetik az elérhető parancsok készletét.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az Module és a AzureRm modulok parancsainak argumentumai nagy mértékben azonosak.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Önálló vagy készletezett adatbázis visszaállításához tekintse meg a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)című témakört.

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Egy vagy több adatbázist kér le. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Lekéri egy adatbázis georedundáns biztonsági másolatát. |
  | [Visszaállítás – AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Visszaállít egy SQL-adatbázist. |

  > [!TIP]
  > Az adatbázisok időponthoz való visszaállítását bemutató minta PowerShell-parancsfájlt az [SQL-adatbázis visszaállítása a PowerShell használatával](scripts/sql-database-restore-database-powershell.md)című témakörben talál.

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

A felügyelt példányok adatbázisának visszaállításával kapcsolatban lásd: [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Egy vagy több felügyelt példány beolvasása. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Lekéri egy példány-adatbázist. |
  | [Visszaállítás – AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Visszaállítja egy példány-adatbázist. |

### <a name="rest-api"></a>REST API

Egy vagy készletezett adatbázis visszaállítása a REST API használatával:

| API | Leírás |
| --- | --- |
| [REST (createMode = helyreállítás)](https://docs.microsoft.com/rest/api/sql/databases) |Visszaállítja az adatbázist. |
| [Adatbázis-létrehozási vagy-frissítési állapot beolvasása](https://docs.microsoft.com/rest/api/sql/operations) |Visszaadja az állapotot egy visszaállítási művelet során. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Egy vagy készletezett adatbázis az Azure CLI használatával történő visszaállításához tekintse meg [az az SQL db Restore](/cli/azure/sql/db#az-sql-db-restore)című témakört.

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

A felügyelt példányok adatbázisának az Azure CLI használatával történő visszaállításához lásd [az az SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Összegzés

Az automatikus biztonsági mentések védik az adatbázisokat a felhasználók és alkalmazások hibáiból, a véletlen adatbázis-törlésből és a hosszan tartó kimaradásokból. Ez a beépített képesség minden szolgáltatási réteghez és számítási mérethez elérhető.

## <a name="next-steps"></a>Következő lépések

- [Üzletmenet-folytonosság áttekintése](sql-database-business-continuity.md)
- [Automatikus biztonsági mentések SQL Database](sql-database-automated-backups.md)
- [Hosszú távú megőrzés](sql-database-long-term-retention.md)
- A gyorsabb helyreállítási lehetőségek megismeréséhez lásd: [aktív földrajzi replikálás](sql-database-active-geo-replication.md) vagy [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md).

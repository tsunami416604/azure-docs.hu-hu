---
title: Adatbázis visszaállítása biztonsági másolatból
description: Ismerje meg a pont-az-időben-visszaállítás, amely lehetővé teszi, hogy az Azure SQL-adatbázis visszaállítása legfeljebb 35 napig.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268742"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Azure SQL-adatbázis helyreállítása automatikus adatbázis-biztonsági mentések használatával

Alapértelmezés szerint az Azure SQL Database biztonsági mentései georeplikált blob storage (RA-GRS storage-típus) tárolók. Az alábbi lehetőségek állnak rendelkezésre az adatbázis [automatikus biztonsági másolatainak](sql-database-automated-backups.md)segítségével történő helyreállításához. A következőket teheti:

- Hozzon létre egy új adatbázist ugyanazon az SQL Database-kiszolgálón, amely az adatmegőrzési időszakon belül egy megadott időpontra helyreállt.
- Hozzon létre egy adatbázist ugyanazon az SQL Database-kiszolgálón, amely a törölt adatbázis törlési idejére helyreállt.
- Hozzon létre egy új adatbázist az ugyanabban a régióban található bármely SQL Database-kiszolgálón, amely a legutóbbi biztonsági mentések pontjáig helyreállt.
- Hozzon létre egy új adatbázist bármely SQL Database-kiszolgálóbármely más régióban, helyreállt a pont a legutóbbi replikált biztonsági mentések.

Ha [a biztonsági mentés hosszú távú megőrzését](sql-database-long-term-retention.md)konfigurálta, bármely SQL Database-kiszolgálón létrehozhat egy új adatbázist bármely hosszú távú megőrzési biztonsági másolatból.

> [!IMPORTANT]
> A visszaállítás során nem lehet felülírni egy meglévő adatbázist.

A Standard vagy prémium szintű szolgáltatási szintek használata esetén az adatbázis-visszaállítás többletköltséget vonhat maga után. A többletköltség akkor merül fel, ha a visszaállított adatbázis maximális mérete nagyobb, mint a céladatbázis szolgáltatási szintjéhez és teljesítményszintjéhez tartozó tárterület mennyisége. Az extra tárterület díjszabási részleteit az [SQL Database díjszabási lapján találja.](https://azure.microsoft.com/pricing/details/sql-database/) Ha a tényleges felhasznált terület kisebb, mint a benne foglalt tárterület, elkerülheti ezt a többletköltséget, ha az adatbázis maximális méretét a benne foglalt összegre állítja be.

## <a name="recovery-time"></a>Helyreállítási idő

Az adatbázis automatikus adatbázis-biztonsági mentések használatával történő visszaállításának helyreállítási idejét számos tényező befolyásolja:

- Az adatbázis mérete.
- Az adatbázis számítási mérete.
- Az érintett tranzakciós naplók száma.
- A visszaállítási pont helyreállításához visszajátszandó tevékenység mennyisége.
- A hálózati sávszélesség, ha a visszaállítás egy másik régióba.
- A célrégióban feldolgozott egyidejű visszaállítási kérelmek száma.

Nagy vagy nagyon aktív adatbázis esetén a visszaállítás több órát is igénybe vehet. Ha egy régióban hosszan tartó kimaradás van, lehetséges, hogy a vész-helyreállítási kérelmek nagy számú geo-visszaállítási kérelmek et indít a vész-helyreállítási. Ha sok kérelem van, az egyes adatbázisok helyreállítási ideje nőhet. A legtöbb adatbázis-visszaállítás kevesebb mint 12 óra alatt befejeződik.

Egyetlen előfizetés esetén az egyidejű visszaállítási kérelmek száma korlátozott. Ezek a korlátozások a pont-az-időben történő visszaállítások, geo-visszaállítások és a hosszú távú megőrzési biztonsági mentés visszaállításai bármilyen kombinációjára vonatkoznak.

| | **A feldolgozás alatt álló egyidejű kérelmek maximális száma** | **A benyújtott egyidejű kérelmek maximális száma** |
| :--- | --: | --: |
|Egyetlen adatbázis (előfizetésenként)|10|60|
|Elasztikus készlet (készletenként)|4|200|
||||

Nincs beépített módszer a teljes kiszolgáló visszaállítására. A feladat elvégzésének például az Azure SQL Database: Full Server Recovery című [témakörben található.](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)

> [!IMPORTANT]
> Az automatikus biztonsági mentések használatával történő helyreállításhoz az ELŐFIZETÉSBEN az SQL Server közreműködői szerepkör tagjának kell lennie, vagy az előfizetés tulajdonosának kell lennie. További információ: [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md). Az Azure Portalon, a PowerShellen vagy a REST API-n keresztül helyreállíthatja. A Transact-SQL nem használható.

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Az Azure Portal, a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/databases)használatával visszaállíthatja az önálló, készletezett vagy példányadatbázist egy korábbi időpontra. A kérelem megadhatja a visszaállított adatbázis bármely szolgáltatási rétegét vagy számítási méretét. Győződjön meg arról, hogy elegendő erőforrással rendelkezik azon a kiszolgálón, amelyre az adatbázist visszaállítja. Ha befejeződött, a visszaállítás új adatbázist hoz létre ugyanazon a kiszolgálón, mint az eredeti adatbázis. A visszaállított adatbázis díja a szolgáltatási szint és a számítási méret alapján normál díjakat. Az adatbázis-visszaállítás befejezéséig nem kell díjat fizetnie.

Az adatbázist általában visszaállítja egy korábbi pontra helyreállítási célokra. A visszaállított adatbázist az eredeti adatbázis helyettesítéseként kezelheti, vagy adatforrásként használhatja az eredeti adatbázis frissítéséhez.

- **Adatbázis cseréje**

  Ha azt szeretné, hogy a visszaállított adatbázis helyettesítse az eredeti adatbázist, meg kell adnia az eredeti adatbázis számítási méretét és szolgáltatási szintjét. Ezután átnevezheti az eredeti adatbázist, és a T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) parancsával átnevezheti az eredeti nevet, és a visszaállított adatbázisnak megadhatja az eredeti nevet.

- **Adat-helyreállítás**

  Ha azt tervezi, hogy a visszaállított adatbázisból adatokat szeretne beolvasni, hogy helyreállítsa a felhasználó vagy alkalmazás hibáját, olyan adat-helyreállítási parancsfájlt kell írnia és végrehajtania, amely adatokat olvas be a visszaállított adatbázisból, és az eredeti adatbázisra vonatkozik. Bár a visszaállítási művelet hosszú időt vehet igénybe, a visszaállítási adatbázis a visszaállítási folyamat során látható az adatbázislistában. Ha a visszaállítás során törli az adatbázist, a visszaállítási művelet megszakad, és nem kell fizetnie a visszaállítást nem lefolytatott adatbázisért.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Időponthoz való visszaállítás az Azure Portal használatával

Egyetlen SQL-adatbázis- vagy példányadatbázist az Azure Portalon visszaállítani kívánt adatbázis áttekintő paneljéből egy adott időpontban helyreállíthat.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Ha egy adott vagy készletbe adott adatbázist szeretne helyreállítani egy adott időpontban az Azure Portal használatával, nyissa meg az adatbázis áttekintése lapot, és válassza a **Visszaállítás** lehetőséget az eszköztáron. Válassza ki a biztonsági másolat forrását, és válassza ki azt a ponthoz érkezett biztonsági mentési pontot, amelyből új adatbázis jön létre. 

  ![Képernyőkép az adatbázis-visszaállítási beállításokról](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

Ha az Azure Portal használatával egy adott időpontban szeretne helyreállítani egy felügyelt példány-adatbázist, nyissa meg az adatbázis áttekintése lapot, és válassza a **Visszaállítás** lehetőséget az eszköztáron. Válassza ki azt a pont-az-időpontban biztonsági mentési pontot, amelyből új adatbázis jön létre. 

  ![Képernyőkép az adatbázis-visszaállítási beállításokról](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Ha programozott módon szeretné visszaállítani az adatbázist egy biztonsági másolatból, olvassa el [a Programozott helyreállítás automatikus biztonsági másolatokkal történő, programozott végrehajtása című témakört.](sql-database-recovery-using-backups.md)

## <a name="deleted-database-restore"></a>Törölt adatbázis-visszaállítás

A törölt adatbázis térhet vissza a törlési időre vagy egy korábbi időpontra ugyanazon az SQL Database-kiszolgálón vagy ugyanazon a felügyelt példányon. Ezt az Azure Portalon, a [PowerShellen](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)vagy a [REST-en (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)keresztül valósíthatja meg. A törölt adatbázist úgy állíthatja vissza, hogy új adatbázist hoz létre a biztonsági másolatból.

> [!IMPORTANT]
> Ha töröl egy Azure SQL Database-kiszolgálót vagy felügyelt példányt, az összes adatbázisa is törlődik, és nem lehet visszaőket visszatenni. Törölt kiszolgáló vagy felügyelt példány nem állítható vissza.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Törölt adatbázis-visszaállítás az Azure Portal használatával

A törölt adatbázisokat az Azure Portalról állíthatja vissza a kiszolgálóról és a példányerőforrásból.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Ha egyetlen vagy készletbe adott törölt adatbázist szeretne visszaállítani a törlési időre az Azure Portal használatával, nyissa meg a kiszolgáló áttekintése lapot, és válassza **a Törölt adatbázisok lehetőséget.** Jelölje ki a visszaállítani kívánt törölt adatbázist, és írja be a biztonsági másolatból visszaállított adatokkal létrehozandó új adatbázis nevét.

  ![Képernyőkép a törölt Azure SQL-adatbázis visszaállításáról](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

Felügyelt adatbázis helyreállításához az Azure Portal használatával nyissa meg a felügyelt példány áttekintő lapját, és válassza **a Törölt adatbázisok lehetőséget.** Jelölje ki a visszaállítani kívánt törölt adatbázist, és írja be a biztonsági másolatból visszaállított adatokkal létrehozandó új adatbázis nevét.

  ![Képernyőkép a törölt Azure SQL-példány adatbázisának visszaállításáról](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Törölt adatbázis-visszaállítás a PowerShell használatával

A következő mintaparancsfájlok segítségével állítsa vissza az Azure SQL Database törölt adatbázisát és egy felügyelt példányt a PowerShell használatával.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

A törölt Azure-SQL-adatbázis visszaállítását bemutató PowerShell-parancsfájlmintáját a [PowerShell használatával SQL-adatbázis visszaállítása című témakörben található.](scripts/sql-database-restore-database-powershell.md)

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

A törölt példányadatbázis visszaállítását bemutató PowerShell-parancsfájlmintáját [lásd: Törölt adatbázis visszaállítása a felügyelt példányon a PowerShell használatával](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> A törölt adatbázis programozott visszaállításáról [lásd: Programozott helyreállítás automatikus biztonsági másolatokkal.](sql-database-recovery-using-backups.md)

## <a name="geo-restore"></a>Georedundáns helyreállítás

Az SQL-adatbázis bármely Azure-régió bármely kiszolgálóján visszaállítható a legújabb georeplikált biztonsági mentések. A geo-visszaállítás egy georeplikált biztonsági másolatot használ forrásaként. Akkor is kérhet geo-visszaállítást, ha az adatbázis vagy az adatközpont kimaradás miatt nem érhető el.

Geo-visszaállítás az alapértelmezett helyreállítási lehetőség, ha az adatbázis nem érhető el, mert egy incidens a gazdaterületen. Az adatbázist bármely másik régióban található kiszolgálón visszaállíthatja. A biztonsági mentés készítése és a földrajzi replikálás a másik régióban lévő Azure blobba történő georeplikálása között késleltetés van. Ennek eredményeképpen a visszaállított adatbázis akár egy órával is lemaradhat az eredeti adatbázis mögött. A következő ábrán egy adatbázis-visszaállítás látható egy másik régióban utoljára rendelkezésre álló biztonsági másolatból.

![A geo-visszaállítás grafikája](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-visszaállítás az Azure Portal használatával

Az Azure Portalon hozzon létre egy új egy- vagy felügyelt példány-adatbázist, és válasszon egy elérhető geo-visszaállítási biztonsági mentést. Az újonnan létrehozott adatbázis tartalmazza a geo-visszaállított biztonsági mentési adatokat.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Egyetlen SQL-adatbázis geo-visszaállításához az Azure Portalról a kiválasztott régióban és kiszolgálón, kövesse az alábbi lépéseket:

1. Az **irányítópulton**válassza az**SQL-adatbázis** **létrehozása** > lehetőséget. Az **Alapok** lapon adja meg a szükséges adatokat.
2. Válassza a **További beállítások lehetőséget**.
3. A **Meglévő adatok használata**csoportban válassza a Biztonsági másolat **lehetőséget.**
4. A **Biztonsági másolat listában**válasszon egy biztonsági másolatot az elérhető geo-visszaállítási biztonsági mentések listájából.

    ![Képernyőkép: SQL-adatbázis létrehozása beállítások](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Az új adatbázis létrehozásának befejezése a biztonsági másolatból. Egyetlen Azure SQL-adatbázis létrehozásakor tartalmazza a visszaállított geo-visszaállítási biztonsági mentést.

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

Ha egy felügyelt példány adatbázisát az Azure Portalról egy általa választott régióban lévő meglévő felügyelt példányra szeretné helyreállítani, válasszon ki egy felügyelt példányt, amelyen vissza szeretné állítani az adatbázist. Kövesse az alábbi lépéseket:

1. Válassza az **Új adatbázis lehetőséget.**
2. Írja be a kívánt adatbázisnevet.
3. A **Meglévő adatok használata**csoportban válassza a Biztonsági másolat **lehetőséget.**
4. Válasszon egy biztonsági másolatot az elérhető geo-visszaállítási biztonsági mentések listájából.

    ![Az Új adatbázis beállításainak képernyőképe](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Az új adatbázis létrehozásának befejezése. A példányadatbázis létrehozásakor az tartalmazza a visszaállított geo-visszaállítási biztonsági mentést.

### <a name="geo-restore-by-using-powershell"></a>Geo-visszaállítás a PowerShell használatával

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Egyetlen SQL-adatbázis geo-visszaállítási műveletét bemutató PowerShell-parancsfájlról a PowerShell használata egy [azure-beli egyetlen adatbázis visszaállítása egy korábbi időpontra című témakörben található.](scripts/sql-database-restore-database-powershell.md)

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

Egy PowerShell-parancsfájl, amely bemutatja, hogyan végezhetgeo-visszaállítás egy felügyelt példány adatbázis, [a PowerShell használata a felügyelt példány adatbázisának visszaállítása egy másik georégióba.](scripts/sql-managed-instance-restore-geo-backup.md)

### <a name="geo-restore-considerations"></a>Geo-visszaállítási szempontok

Egy földrajzi szekunder adatbázison nem hajtható végre időponthoz kötött visszaállítás. Ezt csak elsődleges adatbázisban teheti meg. A kimaradás utáni helyreállítás geo-visszaállításának használatáról a [Kimaradás helyreállítása](sql-database-disaster-recovery.md)című témakörben talál részletes információt.

> [!IMPORTANT]
> Geo-visszaállítás a legalapvetőbb vész-helyreállítási megoldás érhető el az SQL Database. Automatikusan létrehozott georeplikált biztonsági mentések helyreállítási pont célkitűzés (RPO) egyenlő 1 óra, és a becsült helyreállítási idő legfeljebb 12 óra. Nem garantálja, hogy a célrégió képes lesz az adatbázisok visszaállítására egy regionális kimaradás után, mert a kereslet jelentős növekedése valószínű. Ha az alkalmazás viszonylag kis adatbázisokat használ, és nem kritikus fontosságú az üzleti, geo-visszaállítás megfelelő vész-helyreállítási megoldás. A nagy adatbázisokat igénylő és az üzletmenet folytonosságát biztosító, üzleti legkritikusabb alkalmazások esetében használja az [automatikus feladatátvételi csoportokat.](sql-database-auto-failover-group.md) Sokkal alacsonyabb RPO- és helyreállítási időcélt kínál, és a kapacitás mindig garantált. Az üzletmenet folytonossági lehetőségeiről az [Üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md)című témakörben olvashat bővebben.

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>A helyreállítás programozott végrehajtása automatikus biztonsági másolatokkal

Az Azure PowerShell vagy a REST API helyreállítási is használhatja. Az alábbi táblázatok a rendelkezésre álló parancsokat ismertetik.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai nagymértékben azonosak.

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Önálló vagy készletezett adatbázis visszaállításáról a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)című témakörben található.

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlDatabase adatbázis](/powershell/module/az.sql/get-azsqldatabase) |Egy vagy több adatbázist kér le. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
  | [Get-AzSqlDatabaseGeobackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Lekéri egy adatbázis georedundáns biztonsági másolatát. |
  | [Visszaállítás-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Visszaállít egy SQL-adatbázist. |

  > [!TIP]
  > Egy powershell-parancsfájlminta, amely bemutatja, hogyan hajthatja végre az adatbázis időponthoz való visszaállítását, [olvassa el az SQL-adatbázis visszaállítása a PowerShell használatával című témakört.](scripts/sql-database-restore-database-powershell.md)

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

Felügyelt példányadatbázis visszaállításához olvassa el a [Restore-AzSqlInstanceDatabase című témakört.](/powershell/module/az.sql/restore-azsqlinstancedatabase)

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Beszerzi egy vagy több felügyelt példányt. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Lead egy példányadatbázist. |
  | [Visszaállítás-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Egy példányadatbázis visszaállítása. |

### <a name="rest-api"></a>REST API

Egyetlen vagy készletbe adott adatbázis visszaállítása a REST API használatával:

| API | Leírás |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Adatbázis visszaállítása. |
| [Adatbázis állapotának létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/operations) |A visszaállítási művelet során az állapotot adja vissza. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="single-azure-sql-database"></a>Egyetlen Azure SQL-adatbázis

Egyetlen vagy készletezett adatbázis visszaállítása az Azure CLI használatával lásd: [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Felügyelt példány adatbázisa

Felügyelt példányadatbázis visszaállítása az Azure CLI használatával, lásd: [az sql midb visszaállítása.](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Összefoglalás

Az automatikus biztonsági mentések védik az adatbázisokat a felhasználói és alkalmazáshibáktól, a véletlen adatbázis-törléstől és a hosszabb leállásoktól. Ez a beépített funkció minden szolgáltatási szinthez és számítási mérethez elérhető.

## <a name="next-steps"></a>További lépések

- [Üzletmenet-folytonosság – áttekintés](sql-database-business-continuity.md)
- [AZ SQL Database automatikus biztonsági mentései](sql-database-automated-backups.md)
- [Hosszú távú megőrzés](sql-database-long-term-retention.md)
- A gyorsabb helyreállítási lehetőségekről az [Aktív georeplikáció](sql-database-active-geo-replication.md) vagy [az Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)című témakörben olvashat.

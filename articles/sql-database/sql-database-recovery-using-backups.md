---
title: Azure SQL Database-adatbázis visszaállítása biztonsági másolatból | Microsoft Docs
description: Ismerje meg az időponthoz való visszaállítást, amely lehetővé teszi, hogy visszaállítson egy Azure SQL Databaset egy korábbi időpontra (legfeljebb 35 nap).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: 00982ea837783a7e7a9dca257f04c77d48aceef2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103116"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Azure SQL Database helyreállítása automatikus adatbázis-biztonsági mentéssel

Alapértelmezés szerint a SQL Database a biztonsági mentések földrajzilag replikált blob Storage-ban tárolódnak (RA-GRS). A következő beállítások érhetők el az adatbázis-helyreállításhoz az [automatikus adatbázis-biztonsági mentések](sql-database-automated-backups.md)használatával:

- Hozzon létre egy új adatbázist ugyanazon a SQL Database kiszolgálón, amely a megőrzési időn belül egy megadott időpontra lett helyreállítva.
- Hozzon létre egy adatbázist ugyanazon a SQL Database kiszolgálón, amely a törölt adatbázis törlési idejére lett helyreállítva.
- Hozzon létre egy új adatbázist minden olyan SQL Database-kiszolgálón, amely ugyanabban a régióban lett helyreállítva, a legfrissebb biztonsági másolatok pontjáig.
- Hozzon létre egy új adatbázist bármely SQL Database-kiszolgálón bármely más régióban, a legutóbbi replikált biztonsági másolatok pontjáig.

Ha a [biztonsági mentés hosszú távú megőrzését](sql-database-long-term-retention.md)állította be, akkor bármely SQL Database-kiszolgálón létrehozhat egy új ADATBÁZIST bármely ltr biztonsági másolatból.

> [!IMPORTANT]
> Egy meglévő adatbázis nem írható felül a visszaállítás során.

Standard vagy prémium szintű szolgáltatási szint használata esetén a visszaállított adatbázis a következő feltételek mellett extra tárterületet eredményez:

- A P11 – P15 – S4-S12 vagy P1 – P6 visszaállítása, ha az adatbázis maximális mérete meghaladja a 500 GB-ot.
- A P1 – P6 – S4 – S12 visszaállítása, ha az adatbázis maximális mérete meghaladja a 250 GB-ot.

Az extra költségek akkor merülnek fel, ha a visszaállított adatbázis maximális mérete nagyobb, mint a céladatbázis szolgáltatási szintjéhez és a teljesítmény szintjéhez tartozó tárterület mennyisége. A benne foglalt mennyiség fölött kiosztott extra tárterületet külön kell fizetni. A további tárterület részletes díjszabását a [SQL Database díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/sql-database/)tekintheti meg. Ha a felhasznált terület tényleges mennyisége kevesebb, mint a tárhelyek mennyisége, akkor ezt a többletköltséget elkerülheti, ha a maximális adatbázis méretét a befoglalt mennyiségre állítja.

> [!NOTE]
> Az adatbázisok [automatikus biztonsági mentése](sql-database-automated-backups.md) az [adatbázis-másolat](sql-database-copy.md)létrehozásakor használatos.

## <a name="recovery-time"></a>Helyreállítás ideje

Az adatbázis automatikus biztonsági mentések használatával történő visszaállításának helyreállítási idejét számos tényező befolyásolja:

- Az adatbázis mérete
- Az adatbázis számítási mérete
- A tranzakciós naplók száma
- A visszajátszani kívánt tevékenység mennyisége a visszaállítási pontra történő helyreállításhoz
- A hálózati sávszélesség, ha a visszaállítás egy másik régióba esik
- A megcélzott régióban feldolgozott egyidejű visszaállítási kérelmek száma

Nagy és/vagy nagyon aktív adatbázisok esetén a visszaállítás több óráig is eltarthat. Ha hosszabb ideig tartó leállás van egy régióban, lehetséges, hogy nagy számú geo-visszaállítási kérést dolgoz fel más régiók. Sok kérelem esetén a helyreállítási idő növelheti az adott régióban lévő adatbázisok helyreállításának idejét. A legtöbb adatbázis-visszaállítás kevesebb, mint 12 óra alatt elvégezhető.

Egyetlen előfizetés esetében az egyidejű visszaállítási kérelmek száma korlátozott.  Ezek a korlátozások az adott időponthoz tartozó visszaállítások bármely kombinációjára érvényesek, a Geo visszaállítja és visszaállítja a hosszú távú adatmegőrzési biztonsági mentést:

| | **A feldolgozás alatt álló egyidejű kérelmek maximális száma** | **A beküldött egyidejű kérelmek maximális száma** |
| :--- | --: | --: |
|Önálló adatbázis (előfizetés)|10|60|
|Rugalmas készlet (/készlet)|4|200|
||||

Jelenleg nincs beépített módszer a teljes kiszolgáló visszaállítására. A [Azure SQL Database: A kiszolgáló teljes](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) helyreállítási parancsfájlja egy példa arra, hogyan hajthatja végre ezt a feladatot.

> [!IMPORTANT]
> Az automatikus biztonsági másolatok használatával történő helyreállításhoz az előfizetésben szereplő SQL Server közreműködő szerepkör tagjának kell lennie, vagy az előfizetés tulajdonosának [kell lennie – lásd a RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md). A helyreállítást az Azure Portal, a PowerShell vagy a REST API használatával végezheti. A Transact-SQL nem használható.

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A Azure Portal, a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)vagy a [REST API](https://docs.microsoft.com/rest/api/sql/databases)használatával visszaállíthat egy önálló, készletezett vagy példány-adatbázist egy korábbi időpontra. A kérelem bármilyen szolgáltatási szintet vagy számítási méretet megadhat a visszaállított adatbázis számára. Győződjön meg arról, hogy elegendő erőforrással rendelkezik azon a kiszolgálón, amelyhez az adatbázist állítja vissza. Ha elkészült, a rendszer létrehoz egy új adatbázist az eredeti adatbázissal megegyező kiszolgálón. A visszaállított adatbázist a szolgáltatási szintjétől és a számítási mérettől függően a normál díjszabás szerint számítjuk fel. Az adatbázis visszaállítása után nem számítunk fel díjat.

Általában egy adatbázis visszaállítása egy korábbi helyre helyreállítás céljából. A visszaállított adatbázist felhasználhatja az eredeti adatbázis helyett, vagy forrás adatként használhatja az eredeti adatbázis frissítéséhez.

- **Adatbázis cseréje**

  Ha a visszaállított adatbázis az eredeti adatbázis helyettesítésére szolgál, akkor meg kell adnia az eredeti adatbázis számítási méretét és a szolgáltatási szintet. Ezután átnevezheti az eredeti adatbázist, és megadhatja az eredeti nevet a visszaállított adatbázisnak az [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-database) parancs használatával a T-SQL-ben.

- **Adathelyreállítás**

  Ha a visszaállított adatbázisból szeretne adatokat lekérni egy felhasználói vagy alkalmazáshiba általi helyreállításhoz, írnia kell és végre kell hajtania egy olyan adathelyreállítási parancsfájlt, amely kinyeri az adatokat a visszaállított adatbázisból, és az eredeti adatbázisra vonatkozik. Bár a visszaállítási művelet végrehajtása hosszú időt is igénybe vehet, a visszaállítási folyamat során az adatbázis-lista látható. Ha a visszaállítás során törli az adatbázist, a visszaállítási művelet meg lesz szakítva, és nem kell fizetnie az adatbázisnak, amely nem végezte el a visszaállítást.

Ha egyetlen, készletezett vagy példányos adatbázist kíván helyreállítani egy időpontra a Azure Portal használatával, nyissa meg az adatbázis oldalát, és kattintson a **visszaállítás** elemre az eszköztáron.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Az adatbázis biztonsági másolatból való programozott visszaállításával kapcsolatban lásd: a [helyreállítás programozott végrehajtása automatizált biztonsági mentéssel](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Adatbázis-visszaállítás törölve

A törölt adatbázist visszaállíthatja a törlési időre vagy egy korábbi időpontra ugyanazon a SQL Database kiszolgálón a Azure Portal, a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)vagy a [Rest (CreateMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)használatával. [A törölt adatbázist a PowerShell használatával állíthatja vissza a felügyelt példányon](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> A törölt adatbázisok visszaállítását bemutató minta PowerShell-parancsfájlhoz lásd: [SQL-adatbázis visszaállítása a PowerShell használatával](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Ha töröl egy Azure SQL Database Server-példányt, az összes adatbázisa is törlődik, és nem állítható helyre. A törölt kiszolgálók visszaállítása jelenleg nem támogatott.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Az adatbázis-visszaállítás törölve lett a Azure Portal használatával

Ha a Azure Portal segítségével kívánja helyreállítani a törölt adatbázist, nyissa meg a kiszolgáló lapját, és a műveletek területen kattintson a **törölt adatbázisok**elemre.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Törölt adatbázis programozott visszaállításával kapcsolatban lásd: a [helyreállítás programozott módon történő végrehajtása automatizált biztonsági mentéssel](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Georedundáns helyreállítás

Bármely Azure-régióban lévő kiszolgálón visszaállíthat egy SQL-adatbázist a legutóbbi földrajzilag replikált biztonsági mentésből. A Geo-visszaállítás a forrásként egy földrajzilag replikált biztonsági mentést használ. Akkor is kérhető, ha az adatbázis vagy az adatközpont kimaradás miatt nem érhető el.

A Geo-visszaállítás az alapértelmezett helyreállítási lehetőség, ha az adatbázis nem érhető el az üzemeltetési régióban lévő incidens miatt. Az adatbázist bármely más régióban lévő kiszolgálóra is visszaállíthatja. A biztonsági másolat készítése és az egy másik régióban lévő Azure-blobba való földrajzi replikálása között késés történik. Ennek eredményeképpen a visszaállított adatbázis akár egy óráig is elvégezhető az eredeti adatbázis mögött. A következő ábra az adatbázis visszaállítását mutatja be egy másik régióban elérhető legutóbbi biztonsági mentésből.

![Geo-visszaállítás](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> A Geo-visszaállítási műveleteket bemutató minta PowerShell-parancsfájlhoz lásd: [SQL-adatbázis visszaállítása a PowerShell használatával](scripts/sql-database-restore-database-powershell.md).

A földrajzi másodlagos időponthoz való visszaállítás jelenleg nem támogatott. Az időponthoz való visszaállítás csak elsődleges adatbázison végezhető el. További információ a Geo-visszaállítás a leállás utáni helyreállításhoz való használatáról: [áramkimaradás](sql-database-disaster-recovery.md)miatti helyreállítás.

> [!IMPORTANT]
> A Geo-visszaállítás a SQL Databaseban rendelkezésre álló legalapvetőbb vész-helyreállítási megoldás. Az automatikusan létrehozott geo-replikált biztonsági mentéseket a RPO = 1 óra és a becsült helyreállítási idő (legfeljebb 12 óra) alapján végzi. Nem garantálja, hogy a célként megadott régió képes legyen visszaállítani az adatbázis (oka) t egy regionális ourage után, mert a kereslet jelentős növekedése valószínűleg várható. A viszonylag kis adatbázisokat használó, nem üzleti szempontból kritikus fontosságú alkalmazások esetében a Geo-visszaállítás egy megfelelő vész-helyreállítási megoldás. A nagyméretű adatbázisokat használó, üzleti szempontból kritikus fontosságú alkalmazások esetében biztosítania kell az üzletmenet folytonosságát, ezért [automatikus feladatátvételi csoportokat](sql-database-auto-failover-group.md)kell használnia. Sokkal alacsonyabb RPO és RTO kínál, és a kapacitás mindig garantált. További információ az üzletmenet-folytonossági lehetőségekről: [az üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Geo-visszaállítás a Azure Portal használatával

Egy adatbázis geo-visszaállításához a Azure Portal segítségével nyissa meg az SQL-adatbázisok lapot, majd kattintson a **Hozzáadás**gombra. A **forrás kiválasztása** szövegmezőben válassza a **biztonsági mentés**lehetőséget. Adja meg azt a biztonsági másolatot, amelyből a helyreállítást el szeretné végezni a régióban és a választott kiszolgálón.

> [!Note]
> A Azure Portal használatával történő geo-visszaállítás felügyelt példányban nem érhető el. Ehelyett használja a PowerShellt.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Automatizált biztonsági mentésekkel programozott módon hajthatja végre a helyreállítást

Ahogy korábban már említettük, a Azure Portalon kívül az adatbázis-helyreállítás programozott módon is elvégezhető Azure PowerShell vagy a REST API használatával. A következő táblázatok ismertetik az elérhető parancsok készletét.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

- Önálló vagy készletezett adatbázis visszaállításához tekintse meg a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)című témakört.

  | A parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Egy vagy több adatbázist kér le. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Lekéri egy adatbázis georedundáns biztonsági másolatát. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Visszaállít egy SQL-adatbázist. |

  > [!TIP]
  > Az adatbázisok időponthoz való visszaállítását bemutató minta PowerShell-parancsfájlt az [SQL-adatbázis visszaállítása a PowerShell használatával](scripts/sql-database-restore-database-powershell.md)című témakörben talál.

- A felügyelt példányok adatbázisának visszaállításával kapcsolatban lásd: [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | A parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Egy vagy több felügyelt példány beolvasása. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Lekéri egy példány-adatbázist. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Visszaállítja egy példány-adatbázist. |

### <a name="rest-api"></a>REST API

Egyetlen vagy készletezett adatbázis visszaállítása a REST API használatával:

| API | Leírás |
| --- | --- |
| [REST (createMode = helyreállítás)](https://docs.microsoft.com/rest/api/sql/databases) |Adatbázis visszaállítása |
| [Adatbázis-létrehozási vagy-frissítési állapot beolvasása](https://docs.microsoft.com/rest/api/sql/operations) |Visszaadja az állapotot egy visszaállítási művelet során. |

### <a name="azure-cli"></a>Azure CLI

- Ha egy vagy készletezett adatbázist szeretne visszaállítani az Azure CLI használatával, tekintse meg [az az SQL db Restore](/cli/azure/sql/db#az-sql-db-restore)című témakört.
- Felügyelt példány visszaállítása az Azure CLI használatával: [az SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Összegzés

Az automatikus biztonsági mentések védik az adatbázisokat a felhasználók és alkalmazások hibáiból, a véletlen adatbázis-törlésből és a hosszan tartó kimaradásokból. Ez a beépített képesség minden szolgáltatási réteghez és számítási mérethez elérhető.

## <a name="next-steps"></a>További lépések

- Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).
- Az automatikus biztonsági mentések Azure SQL Databaseáról a [SQL Database automatizált biztonsági mentések](sql-database-automated-backups.md)című témakörben olvashat bővebben.
- A hosszú távú adatmegőrzésről a [hosszú távú](sql-database-long-term-retention.md)adatmegőrzéssel foglalkozó témakörben olvashat bővebben.
- A gyorsabb helyreállítási lehetőségek megismeréséhez lásd: [aktív földrajzi replikálás](sql-database-active-geo-replication.md) vagy [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md).

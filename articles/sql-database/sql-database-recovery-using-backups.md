---
title: Azure SQL-adatbázis visszaállítása biztonsági másolatból |} A Microsoft Docs
description: Ismerje meg, amely lehetővé teszi, hogy állítsa vissza az Azure SQL Database egy korábbi időpontra az időt (legfeljebb 35 napra) időponthoz visszaállítás.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: ba6493f77b622a814c970b07fc2a23e7ce1d3624
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987562"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Automatikus biztonsági adatbázismentés használatával Azure SQL-adatbázis helyreállítása

Alapértelmezés szerint az SQL Database biztonsági mentések blob georeplikált tárolás (RA-GRS) vannak tárolva. Az alábbi lehetőségek érhetők el az adatbázis helyreállítási használatával [adatbázisok biztonsági mentése automatikus](sql-database-automated-backups.md):

- Hozzon létre egy új adatbázist az adatmegőrzési időszakán belül egy adott időpontra vissza az egyazon logikai kiszolgálón.
- Az egyazon logikai kiszolgálón, és a Törlés időpontja, törölt adatbázis helyreállítása az adatbázis létrehozása.
- Hozzon létre egy új adatbázist ugyanabban a régióban, hogy a legújabb biztonsági mentések a helyreállított egyetlen logikai kiszolgálón.
- Hozzon létre egy új adatbázist, hogy a legújabb replikált biztonsági mentések a helyreállított bármelyik más régióban egyetlen logikai kiszolgálón.

Ha konfigurálta a [biztonsági mentés hosszú távú megőrzés](sql-database-long-term-retention.md) is létrehozhat egy új adatbázist bármely LTR backup bármelyik régióban egyetlen logikai kiszolgálón.

> [!IMPORTANT]
> Létező adatbázis visszaállítás során nem írható felül.

Standard vagy prémium szintű szolgáltatási csomag használatakor a visszaállított adatbázis tekintetében az extra tárterület költsége a következő feltételek:

- Állítsa vissza a P11 – p15 szintű, S4-S12, P1 – P6, vagy ha az adatbázis maximális mérete 500 GB-nál nagyobb.
- Állítsa vissza az S4-S12, P1 – P6 szintű, ha az adatbázis maximális mérete 250 GB-nál nagyobb.

A felesleges költségek azért, mert a visszaállított adatbázis maximális mérete nagyobb, mint a számítási méret foglalt tárhely, és extra tárterületek árából, a bennefoglalt összegen felül külön kell fizetnie. Extra tárterület díjszabásáról, tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/). Ha a felhasznált lemezterület tényleges mennyisége kisebb, mint a foglalt tárhely, majd ezzel többletköltség elkerülhető azzal, hogy csökkenti az adatbázis maximális méretét a csomagban foglalt adatmennyiségen.

> [!NOTE]
> [Adatbázisok biztonsági mentése automatikus](sql-database-automated-backups.md) használatosak, amikor létrehoz egy [adatbázis másolása](sql-database-copy.md).

## <a name="recovery-time"></a>Helyreállítási idő

Az automatikus biztonsági adatbázismentés használatával adatbázis visszaállítása a helyreállítási idő több tényezőtől hatással van:

- Az adatbázis mérete
- Az adatbázis számítási mérete
- Érintett tranzakciós naplók száma
- A tevékenység, amelyet a helyreállítást a visszaállítási pont megismétlését mennyisége
- A hálózati sávszélességet, ha a visszaállítást egy másik régióba
- A célrégióban feldolgozott egyidejű visszaállítási kérések száma

Nagyon nagy méretű és/vagy aktív adatbázis esetén a visszaállítás több óráig is eltarthat. Ha hosszan tartó szolgáltatáskimaradás egy régióban, akkor lehet, hogy nincsenek-e más régiókban által feldolgozott geo-visszaállítás kérések nagy számú. Ha sok kérelem, a helyreállítási idő növelhető az adatbázisok az adott régióban. A legtöbb adatbázis visszaállítása kisebb, mint 12 órát.

Szolgáltatást egy előfizetéshez, vannak bizonyos korlátozások számú párhuzamos visszaállítási kérések (beleértve a pont a idő visszaállítás, georedundáns visszaállítás és hosszú távú megőrzési biztonsági másolatból való visszaállítása) alatt beküldött és kezdett a:

| | **Feldolgozás alatt álló egyidejű kérelmek maximális száma** | **Az elküldött egyidejű kérelmek maximális száma** |
| :--- | --: | --: |
|Önálló adatbázis (előfizetésenként)|10|60|
|Rugalmas készlet (készletenként)|4|200|
||||

Tömeges visszaállítás beépített funkció sem van. A [Azure SQL Database: a teljes kiszolgáló helyreállítási](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) parancsfájl Ez a feladat teljesítéséért egyik módja egy példát.

> [!IMPORTANT]
> Szeretné használni az automatikus biztonsági másolatokból, csak az előfizetés az SQL Server közreműködői szerepköre tagjának és tekintse meg az előfizetés tulajdonosa - kell [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md). A helyreállítást az Azure Portal, a PowerShell vagy a REST API használatával végezheti. A Transact-SQL nem használható.

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Visszaállíthatja egy önálló, készletezett vagy a felügyelt példány adatbázisa egy korábbi időpontra az időben, mint egy új adatbázist ugyanazon a kiszolgálón az Azure portal használatával [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), vagy a [REST API-val](https://docs.microsoft.com/rest/api/sql/databases). Egy adatbázis bármely szolgáltatásszinthez vissza tudja állítani, vagy a számítási méret. Győződjön meg arról, hogy a szükséges erőforrásokat a kiszolgálón, amelyhez állítja vissza az adatbázist. Ha elkészült, a visszaállított adatbázis egy normál, teljes mértékben elérhető, online adatbázis. A visszaállított adatbázis díját a normál díját a szolgáltatási szint és a számítási méret alapján számoljuk el. Nem terheli az adatbázis-visszaállítás befejezéséig.

Általában állítsa vissza egy adatbázis egy korábbi időpontra, helyreállítási célból. Ebben, való kezelése a visszaállított adatbázis az eredeti adatbázis helyett, vagy adatokat beolvasni, és frissítse az eredeti adatbázist.

- **Adatbázis cseréje**

  Célja, hogy a visszaállított adatbázis az eredeti adatbázis helyett, ha ellenőriznie kell, a számítási méret és/vagy szolgáltatásszint megfelelő, és szükség esetén az adatbázis méretezése. Nevezze át az eredeti adatbázist, és adjon a visszaállított adatbázis az eredeti nevét használja a [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) a T-SQL paranccsal.

- **Adat-helyreállítás**

  Ha azt tervezi, adatokat lekérni a visszaállított adatbázis helyreállítása egy felhasználónak vagy alkalmazásnak hiba, meg kell írási és végrehajtási adatok kinyeréséhez a visszaállított adatbázisból az eredeti adatbázist a szükséges adatok helyreállítási szkripteket. Bár a visszaállítási művelet elvégzéséhez hosszú időt is igénybe vehet, az adatbázis visszaállítása közben a visszaállítási folyamat során az adatbázisok listája látható. Ha törli az adatbázist a visszaállítás során, a visszaállítási művelet meg lett szakítva, és nem terheli az adatbázis, amely a visszaállítás nem fejeződött be.

Egy önálló, készletezett vagy felügyelt példány adatbázis helyreállítása az Azure portal használatával, nyissa meg az adatbázishoz tartozó lap, és kattintson a **visszaállítása** az eszköztáron.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Programozott módon egy adatbázis visszaállítása biztonsági másolatból, lásd: [programozott módon teljesítő recovery segítségével automatizált biztonsági mentések](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Törölt adatbázis visszaállítása

Egy törölt adatbázist visszaállíthatja az egyazon logikai kiszolgálón, az Azure Portalon, a törölt adatbázisok törlésének időpontja [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), vagy a [REST (createMode visszaállítási =)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Akkor is törölt adatbázis visszaállítása egy korábbi időpontra a során a megőrzési használatával [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!Note]
> Törölt adatbázis visszaállítása a felügyelt példány nem érhető el.
> [!TIP]
> Egy PowerShell-példaszkript megjelenítő törölt adatbázis visszaállítása, lásd: [PowerShell-lel SQL-adatbázis visszaállítása](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Ha törli egy Azure SQL Database server-példány, az összes adatbázis is törlődik, és nem állítható helyre. Jelenleg nem törölt kiszolgáló visszaállítása nem támogatott.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Törölt adatbázis visszaállítása az Azure portal használatával

Az Azure Portalon során törölt adatbázis helyreállítása a [DTU-alapú modell megőrzési időszak](sql-database-service-tiers-dtu.md) vagy [Virtuálismag-alapú modell megőrzési időszak](sql-database-service-tiers-vcore.md) az Azure Portalon nyissa meg az oldalt, a kiszolgáló és a a Műveletek területen, kattintson a **törölt adatbázisok**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Programozott módon a törölt adatbázis visszaállítása, lásd: [programozott módon teljesítő recovery segítségével automatizált biztonsági mentések](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Georedundáns helyreállítás

A biztonsági mentésekből legutóbbi georeplikált állíthatja vissza egy SQL-adatbázis egyetlen kiszolgálón bármely Azure-régióban. A GEO-visszaállítás georedundáns biztonsági másolat használja forrásként, és segítségével helyreállíthat egy adatbázist, akkor is, ha az adatbázis vagy az Adatközpont-leállás miatt elérhetetlenné.

> [!Note]
> A GEO-visszaállítás a felügyelt példány nem érhető el.

A GEO-visszaállítás az alapértelmezett helyreállítási lehetőséget arra esetén az adatbázist üzemeltető régióban incidens miatt nem érhető el az adatbázist. Ha egy nagy méretű incidens egy elérhetetlensége régió eredményez az adatbázis-alkalmazását, egy kiszolgálóhoz az összes adatbázis helyreállíthatja a georeplikált biztonsági másolatokból. Ha egy biztonsági mentés készül, és egy Azure georeplikált késleltetés van egy másik régióban található blob. Ez a késleltetés is lehet akár egy órát, tehát ha katasztrófa történik, is lehet másolatot az adatvesztést egy óra. Az alábbi ábrán látható az adatbázis helyreállítása egy másik régióban elérhető másolatból.

![A GEO-visszaállítás](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Egy PowerShell-példaszkript georedundáns visszaállítás végrehajtása megjelenítése, lásd: [PowerShell-lel SQL-adatbázis visszaállítása](scripts/sql-database-restore-database-powershell.md).

Időponthoz visszaállítást a geo-secondary jelenleg nem támogatott. Csak az elsődleges adatbázisok időponthoz visszaállítási teheti meg. A kimaradás utáni helyreállítása geo-visszaállítás használatával kapcsolatos részletes információkért lásd: [helyreállítás szolgáltatáskimaradás](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Biztonsági másolatokból való helyreállítás a legalapvetőbb a vészhelyreállítási megoldások az SQL Database, a leghosszabb helyreállítási időkorlátot (RPO) és a becsült helyreállítási idő (ERT). Megoldások (például alapszintű szolgáltatásszint vagy bérlői adatbázisok, rugalmas készletek a kis méretű) kis méretű adatbázisok használata esetén a geo-visszaállítás gyakran egy ésszerű Vészhelyreállítási megoldás egy ERT akár 12 órát (általában sokkal kevesebb). Megoldások használatával nagy méretű adatbázisokhoz, és rövidebb helyreállítási megkövetelése, akkor érdemes megfontolni [feladatátvételi csoportok és az aktív georeplikáció](sql-database-geo-replication-overview.md). Aktív georeplikáció sokkal alacsonyabb ERT és RPO kínál csak szükséges, hogy a folyamatosan replikált másodlagos feladatátvétel. Az üzletmenet-folytonossági lehetőségeket további információkért lásd: [üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>A GEO-visszaállítás az Azure portal használatával

A geo-visszaállítás során az adatbázis a [DTU-alapú modell megőrzési időszak](sql-database-service-tiers-dtu.md) vagy [Virtuálismag-alapú modell megőrzési időszak](sql-database-service-tiers-vcore.md) az Azure portal használatával, nyissa meg az SQL Database-adatbázisok oldalt, és kattintson **hozzáadása** . Az a **forrás kiválasztása** szövegbeviteli mezőben válasszon **Backup**. Adja meg a biztonsági mentés, amelyből a helyreállításhoz a régióban, és a választott kiszolgálón.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programozott módon a helyreállítás biztonsági másolatokkal automatikus végrehajtása

Korábban már említettük, az Azure Portalon kívül, mint az adatbázis helyreállításának programozott módon az Azure PowerShell-lel vagy a REST API használatával elvégezhető. Az alábbi táblázatok ismertetik az elérhető parancsok.

### <a name="powershell"></a>PowerShell

- Egyetlen vagy készletezett adatbázisok visszaállítását, lásd: [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase)

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Egy vagy több adatbázist kér le. |
  | [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
  | [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Lekéri egy adatbázis georedundáns biztonsági másolatát. |
  | [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Visszaállít egy SQL-adatbázist. |

  > [!TIP]
  > Egy PowerShell-példaszkript egy adatbázis-időponthoz visszaállításhoz bemutató, lásd: [PowerShell-lel SQL-adatbázis visszaállítása](scripts/sql-database-restore-database-powershell.md).

- Felügyelt példány adatbázisok visszaállítását, lásd: [-időponthoz visszaállítását egy adatbázist az Azure SQL felügyelt példányhoz az azurerm.SQL-hez a PowerShell-könyvtár](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/28/point-in-time-restore-of-a-database-on-azure-sql-managed-instance-using-azurerm-sql-powershell-library/)

### <a name="rest-api"></a>REST API

A REST API használatával egyetlen vagy készletezett adatbázisok visszaállítását:

| API | Leírás |
| --- | --- |
| [REST (createMode = Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Visszaállít egy adatbázist |
| [Get létrehozása, vagy az adatbázis állapotának frissítése](https://docs.microsoft.com/rest/api/sql/operations) |A visszaállítási művelet során állapotának visszaadása |

### <a name="azure-cli"></a>Azure CLI

Azure CLI-vel önálló vagy készletezett adatbázisok visszaállítását, lásd: [az sql db restore](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-restore).

## <a name="summary"></a>Összegzés

Automatikus biztonsági mentést a felhasználói és az alkalmazáshibák, véletlen törlése és a hosszabb kimaradások az adatbázisok védelmét. A beépített kapacitásprofilokban szolgáltatási szintek és a számítási méret érhető el.

## <a name="next-steps"></a>További lépések

- Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
- További információ az Azure SQL Database automatikus biztonsági mentések, lásd: [SQL-adatbázis automatikus biztonsági mentések](sql-database-automated-backups.md).
- Hosszú távú megőrzés kapcsolatos további információkért lásd: [hosszú távú megőrzés](sql-database-long-term-retention.md).
- Gyorsabb helyreállítási beállítások kapcsolatos további információkért lásd: [feladatátvételi csoportok és az aktív georeplikáció](sql-database-geo-replication-overview.md).

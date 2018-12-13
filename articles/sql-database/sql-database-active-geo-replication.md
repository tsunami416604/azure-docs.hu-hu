---
title: Aktív georeplikáció – Azure SQL Database |} A Microsoft Docs
description: Aktív georeplikáció használatával az egyes adatbázisok olvasható másodlagos adatbázis létrehozása az azonos vagy eltérő adatközpontban (régió).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: 844adf70a62377814e813604f1789633367b3da7
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284856"
---
# <a name="create-readable-secondary-databases-using-active-geo-replication"></a>Aktív georeplikáció használatával olvasható másodlagos adatbázis létrehozása

Aktív georeplikáció az Azure SQL Database szolgáltatás, amely lehetővé teszi, hogy az egyes adatbázisok olvasható másodlagos adatbázis létrehozása az azonos vagy eltérő adatközpontban (régió) egy logikai kiszolgálón.

> [!NOTE]
> Aktív georeplikáció által a felügyelt példány nem támogatott.

Aktív georeplikáció üzleti folytonossági megoldás, amely lehetővé teszi az alkalmazásnak, hogy az egyes adatbázisok egy regionális katasztrófa vagy a nagyméretű méretezési szolgáltatáskimaradás esetén gyors vészhelyreállítás tervezték. Ha engedélyezve van a georeplikáció, az alkalmazás kezdeményezhet feladatátvételt egy másodlagos adatbázist egy másik Azure-régióban. Legfeljebb négy meghatározhatják az azonos vagy eltérő régiókban támogatottak, és a másodlagos példány hozható létre a csak olvasási hozzáféréssel lekérdezésekhez is használható. A feladatátvétel kell kezdeményeznie manuálisan az alkalmazás vagy a felhasználó. A feladatátvételt követően az új elsődleges rendelkezik egy másik kapcsolat végpontját. Az alábbi ábra egy tipikus konfigurációja aktív georeplikációt használ georedundáns felhőalapú alkalmazások.

![Aktív georeplikáció](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> Az SQL Database automatikus feladatátvételi csoportok is támogatja. További információkért lásd: használatával [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md). Aktív georeplikáció által a felügyelt példány belül létrehozott adatbázisok esetében is, nem támogatott. Fontolja meg [feladatátvételi csoportok](sql-database-auto-failover-group.md) felügyelt példányaival.

Ha bármilyen okból az elsődleges adatbázis meghibásodik, vagy egyszerűen csak van szüksége, offline állapotba kell helyezni, a másodlagos adatbázisok valamelyik is kezdeményezhető feladatátvétel. Feladatátvétel a másodlagos adatbázisok egyik aktiválásakor összes másodlagos a rendszer automatikusan összekapcsolja az új elsődleges.

Replikáció és a egy önálló adatbázis feladatátvételét, vagy egy kiszolgálón vagy az aktív georeplikáció használatával rugalmas készlet adatbázisai is kezelheti. A segítségével teheti meg:

- Az [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: Önálló adatbázis](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Rugalmas készlet](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [A Transact-SQL: Önálló adatbázis és rugalmas készlet](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API-VAL: Önálló adatbázis](https://docs.microsoft.com/rest/api/sql/replicationlinks)

A feladatátvételt követően ellenőrizze a hitelesítési követelmények, a kiszolgáló és az adatbázis az új elsődleges. További információkért lásd: [vész-helyreállítása után az SQL Database biztonsági](sql-database-geo-replication-security-config.md).

Aktív georeplikáció kihasználja a [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technológia az SQL Server replikálása aszinkron módon véglegesített tranzakciók száma, az elsődleges adatbázison egy másodlagos adatbázis-pillanatkép-elkülönítés használatával. Automatikus feladatátvételi csoportok a csoport szemantikát felett aktív georeplikációt biztosít, de az azonos aszinkrón replikációs mechanizmus használatos. Közben lekérdezhet, a másodlagos adatbázis némileg lehet az elsődleges adatbázis mögött, a másodlagos adatok garantáltan soha többé nem kell a részleges tranzakciók. Régiók közötti redundancia lehetővé teszi az alkalmazások gyorsan helyreállíthatók a teljes adatközpont vagy az egy kínai adatközpont természeti katasztrófa, katasztrofális az emberi hibák vagy rosszindulatú jogszabályok által okozott részei végleges adatvesztést. Az adott helyreállítási Időkorlát adatokat tekinthet meg [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

Az alábbi ábra példát mutat be egy aktív georeplikáció által beállított, az elsődleges régióban az USA északi középső Régiójában és a másodlagos régióban az USA déli középső Régiója.

![georeplikációs kapcsolatban](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Mivel a másodlagos adatbázisok olvasható, azokat a csak olvasható munkaterhelésekkel, például jelentéskészítéssel feladatok kiszervezése használható. Ha aktív georeplikációt használ, lehetséges, a másodlagos adatbázis létrehozásához az ugyanabban a régióban, az elsődleges, de nem növeli a katasztrofális hibák az alkalmazás rugalmasságát. Ha automatikus feladatátvételi csoportokat használ, a másodlagos adatbázis mindig létrejön egy másik régióban.

Mellett vész helyreállítási aktív georeplikáció a következő esetekben használhatók:

- **Adatbázis-migrálási**: Aktív georeplikáció használatával adatbázis áttelepítése az egyik kiszolgálóról a másikra online, minimális állásidővel.
- **Alkalmazásfrissítések**: Létrehozhat egy extra másodlagos sikertelen vissza másolatként alkalmazásfrissítések során.

Elérése érdekében a valódi üzleti folytonosság, adatbázis-redundancia adatközpontok között csak a megoldás része. Egy alkalmazás (szolgáltatás) – teljes körű helyreállítása, végzetes hiba van szükség a szolgáltatást alkotó összetevők és minden függő szolgáltatás helyreállítás után. Ezen összetevők közé tartoznak az ügyfélszoftver (például egy egyéni JavaScript böngésző), a webes előtérrendszerek, a storage és a DNS. Rendkívül fontos, hogy az összes összetevő ellenálljanak a azonos hibákat, és a helyreállítási idő célértéke (RTO) az alkalmazás belül elérhetővé válnak. Ezért kell azonosítsa az összes függő szolgáltatást, és megismerheti a garanciák és képességeket biztosítanak. Ezt követően végre kell hajtania megfelelő lépéseket annak érdekében, hogy a szolgáltatások, amelytől a feladatátvétel során a szolgáltatás függvények. Vész-helyreállítási megoldások tervezésével kapcsolatos további információkért lásd: [felhőalapú megoldások tervezéséhez a vész-helyreállítási használatával aktív georeplikáció](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktív georeplikáció terminológia és képességek

- **Az automatikus aszinkron replikáció**

 Létrehozhat egy másodlagos adatbázis csak meglévő adatbázis hozzáadásával. Minden Azure SQL Database Server a másodlagos hozható létre. Létrehozása után megjelenik a másodlagos adatbázis az adatokat másolja az elsődleges adatbázisból. Ez a folyamat van áttöltésként is ismert. Miután létrehozott és áttöltésekor másodlagos adatbázis, az elsődleges adatbázis frissítések aszinkron módon replikált a másodlagos adatbázis automatikusan. Aszinkron replikáció azt jelenti, hogy tranzakció véglegesítve az elsődleges adatbázison, mielőtt azok replikálódnak a másodlagos adatbázis.

- **Olvasható másodlagos adatbázis**

  Az alkalmazás hozzáférhet egy másodlagos adatbázis csak olvasható műveletekhez az elsődleges adatbázis eléréséhez használt azonos vagy eltérő rendszerbiztonsági használatával. A másodlagos adatbázisok annak érdekében, hogy a frissítések az elsődleges (napló visszajátszását) replikációs nem késnek-e a másodlagos végrehajtott lekérdezések snapshot elkülönítési módban működik.

  > [!NOTE]
  > A napló visszajátszását a másodlagos adatbázis késik, ha az elsődleges sémafrissítések. Az utóbbi séma zárolva van a másodlagos adatbázis szükséges.

- **Tervezett feladatátvétel**

  Tervezett feladatátvétel előtt az elsődleges szerepre a másodlagos kapcsolók elsődleges és másodlagos adatbázisok közötti teljes szinkronizálást hajt végre. Ez garantálja, hogy nincs adatvesztés. Tervezett feladatátvétel használja a következő esetekben: – hajtsa végre a DR működéseinek éles környezetben ha az adatok elvesztése nem elfogadható; (b), helyezze át az adatbázist egy másik régióban; és (c) adja vissza az adatbázist az elsődleges régió felé, a szolgáltatáskiesés megszüntetése után (feladat-visszavétel).

- **Nem tervezett feladatátvétel**

  Nem tervezett vagy a kényszerített feladatátvétel közvetlenül a másodlagos vált, amennyiben az elsődleges szerepkör minden olyan elsődleges-szinkronizálás nélkül. Ez a művelet adatvesztést eredményez. Nem tervezett feladatátvétel helyreállítási módszerként leállás során használatos az elsődleges nem érhető el. Az eredeti elsődleges újra online állapotba kerül, ha azt automatikusan szinkronizálás nélkül újracsatlakoztatására és egy új másodlagos lesz.

- **Több olvasható másodlagos példánnyal**

  Minden elsődleges legfeljebb 4 másodlagos adatbázis hozható létre. Ha csak egy másodlagos adatbázist, és ez nem sikerül, az alkalmazás van kitéve nagyobb eséllyel addig, amíg létrejön egy új másodlagos adatbázis. Ha több másodlagos adatbázis létezik, az alkalmazás akkor is védett akkor is, ha egy másodlagos adatbázis nem sikerül. A további másodlagos példány hozható létre is használható a horizontális felskálázás a csak olvasható számítási feladatokhoz

  > [!NOTE]
  > Ha egy globálisan elosztott alkalmazás létrehozása, és meg kell adnia a csak olvasási hozzáféréssel az adatok több mint négy régióban aktív georeplikációt használ, másodlagos, egy másodlagos (láncolási néven ismert folyamat) hozhat létre. Ily módon érheti el adatbázis-replikáció gyakorlatilag korlátlan méretezhetőségét. Emellett láncolási csökkenti a replikáció az elsődleges adatbázisból. Kompromisszumot kötni a rendszer a megnövekedett replikációs késés a levél – a legtöbb másodlagos adatbázisokon.

- **A rugalmas készletben található adatbázisok georeplikáció**

  Minden másodlagos adatbázis külön-külön részt venni a rugalmas készlet vagy egyáltalán nem tudja bármilyen rugalmas készletben is. A készlet minden egyes másodlagos adatbázis választás külön, és nem függ semmilyen más másodlagos konfigurációjának adatbázis-(akár a elsődleges vagy másodlagos). Minden rugalmas készletet szerepel egy adott régión belül, így az azonos topológia több másodlagos adatbázis soha nem oszthatnak meg a rugalmas készlet.

- **A másodlagos adatbázis konfigurálható számítási mérete**

  Elsődleges és másodlagos adatbázisok ugyanazon a szolgáltatásszinten van szüksége. A másodlagos adatbázis jön létre a számítási mérete (dtu-k vagy virtuális magok) megegyezik az elsődleges is erősen ajánlott. A kisebb számítási méretű másodlagos fennáll a kockázata, egy nagyobb replikációs késés, a másodlagos lehetséges elérhetetlensége van, és ezért fennáll a kockázata, jelentős adatvesztés a feladatátvétel után. A közzétett RPO eredményeként = 5 mp nem garantálható. A többi kockázati, hogy a feladatátvételt követően az alkalmazás teljesítménye csökkenhet az új elsődleges számítási kapacitását hiánya miatt a nagyobb számítási méretre frissítéséig. A frissítés idején az adatbázis méretétől függ. Ezenkívül jelenleg ilyen szükségesek, hogy az elsődleges és másodlagos adatbázisok online és, ezért nem végezhető el, amíg a szolgáltatáskimaradás elhárítása után teljesítményköltségeket csökkenti. Ha úgy dönt, hogy a másodlagos létrehozása a kisebb számítási mérete, a napló i/o százalékos diagramra az Azure portal nyújt egy jó módszer a szükséges ahhoz, hogy a replikáció terhelés fenntartása másodlagos minimális számítási mérete. Például, ha az elsődleges adatbázis P6 (1000 DTU), és i/o-százalék naplót a másodlagos kell lennie legalább 50 %-a P4 (500 DTU). Is lekérhet használatával i/o-naplóadatok [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vagy [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) adatbázis-nézeteket.  Az SQL-adatbázis számítási méretek további információkért lásd: [Mik az SQL Database szolgáltatási szinteken](sql-database-service-tiers.md).

- **Felhasználó általi feladatátvétel és feladat-visszavétel**

  Egy másodlagos adatbázis explicit módon lehessen kapcsolni az elsődleges szerepre bármikor az alkalmazás vagy a felhasználó. Egy valódi kimaradás során az "tervezett" kell beállítással, amely azonnal elősegíti a másodlagos kell az elsődleges. A sikertelen elsődleges állítja helyre, és megint elérhetővé válik, amikor a rendszer automatikusan jelöli meg a helyreállított elsődleges, másodlagos, és naprakészen tartása az új elsődleges állapotba. Replikáció aszinkron jellege miatt kisebb mennyiségű adatot elvesznek, nem tervezett feladatátvételek során ha elsődleges meghibásodik, a legutóbbi módosítások a másodlagos replikálás előtt. Több másodlagos helyekkel elsődleges átadja a feladatokat, amikor a rendszer automatikusan újrakonfigurálja a replikációs kapcsolatok, és hivatkozásokat tartalmaz a fennmaradó másodlagos példány hozható létre az újonnan előléptetett elsődleges felhasználói beavatkozás nélkül. A szolgáltatáskimaradás, amely miatt a feladatátvétel után, az alkalmazás az elsődleges régióba visszaadandó kívánatos lehet. Ehhez a feladatátvétel parancs a "tervezett" lehetőséggel lehet meghívni.

- **Hitelesítő adatok és -tűzfalszabályok szinkronban tartása**

  Azt javasoljuk, [adatbázis-tűzfalszabályok](sql-database-firewall-configure.md) adatbázisok georeplikált, így ezek a szabályok replikálható, minden másodlagos adatbázisát az elsődleges azonos tűzfalszabályok rendelkezésre áll az adatbázissal. Ezzel a módszerrel nem kell az ügyfelek manuális konfigurálása és karbantartása, mind az elsődleges és másodlagos adatbázisok üzemeltetési kiszolgáló tűzfalszabályait. Ehhez hasonlóan használatával [tartalmazott adatbázis felhasználóit](sql-database-manage-logins.md) adatok hozzáférés biztosítja, mind az elsődleges, mind a másodlagos adatbázisok mindig azonos felhasználói hitelesítő adatokat, így a feladatátvétel során a nem a felhasználónevek és jelszavak eltérések miatt szolgáltatások. Igény szerinti hozzáadásával [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), ügyfelek kezelhetik a felhasználói hozzáférést az elsődleges és másodlagos adatbázisok, és szükségtelenné teszik a kezeléséhez hitelesítő adatait érvényesítette adatbázisokban.

## <a name="upgrading-or-downgrading-a-primary-database"></a>A frissítés, vagy egy elsődleges adatbázis alacsonyabb szolgáltatásszintre

Frissítés vagy Visszaléptetés a különböző számítási méret (belül ugyanazon a szolgáltatásszinten, nem az általános célú és a kritikus fontosságú üzleti között) az elsődleges adatbázis bármely másodlagos adatbázisok leválasztása nélkül is. Amikor frissít, javasoljuk, hogy először frissítse a másodlagos adatbázist, és utána frissítse az elsődleges. Ha alacsonyabb szolgáltatásszintre, fordított sorrendben: először alacsonyabbra az elsődleges, majd a gyűjteményt majd a másodlagos. Frissítésekor vagy eltérő szolgáltatási réteg az adatbázisról, a javaslat lép érvénybe.

> [!NOTE]
> Ha másodlagos adatbázis nem javasoljuk, hogy a másodlagos adatbázisról, a feladatátvételi csoport konfigurációjának részeként hozta létre. Ez azért szükséges az adatszint rendelkezzen elegendő kapacitással a normál számítási feladatok feldolgozásához a feladatátvétel aktiválása után.

## <a name="preventing-the-loss-of-critical-data"></a>Megakadályozza a fontos adatok elvesztését

A nagy kiterjedésű hálózaton magas késést, mert a folyamatos másolás használ egy aszinkrón replikációs mechanizmus. Az aszinkron replikáció révén kis mértékű adatvesztést elkerülhetetlen, ha hiba történik. Egyes alkalmazások azonban szükség lehet adatvesztés nélkül. Ezek a kritikus frissítések védelme érdekében a alkalmazásfejlesztő meghívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) közvetlenül a tranzakció véglegesítése után a rendszer eljárást. Hívó **sp_wait_for_database_copy_sync** blokkolja a hívó szálat, amíg a másodlagos adatbázisra továbbítva lett az utolsó véglegesített tranzakció. Azt azonban nem vár a továbbított játssza vissza és a másodlagos véglegesített tranzakciók. **az sp_wait_for_database_copy_sync** hivatkozás egy adott folyamatos másolása hatókörét. A kapcsolódási jogok az elsődleges adatbázissal rendelkező bármelyik felhasználó meghívhatja ezt az eljárást.

> [!NOTE]
> **az sp_wait_for_database_copy_sync** megakadályozza az adatvesztést a feladatátvételt követően, de nem garantálja a teljes szinkronizálás olvasási hozzáféréssel. A késleltetés által okozott egy **sp_wait_for_database_copy_sync** eljáráshívási is lehet, és a hívás idején a tranzakciós napló méretétől függ.

## <a name="programmatically-managing-active-geo-replication"></a>Aktív georeplikáció szoftveres kezelése

Korábban említett aktív georeplikáció is kezelhetők programozott módon az Azure PowerShell és a REST API használatával. Az alábbi táblázatok ismertetik az elérhető parancsok. Aktív georeplikáció tartalmaz egy, az Azure Resource Manager API-k Management, beleértve a [Azure SQL Database REST API-t](https://docs.microsoft.com/rest/api/sql/) és [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview). Ezekkel az API-erőforráscsoportok használatát, és támogatja a szerepköralapú biztonság (RBAC). Szerepkörök hozzáférést megvalósításával további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>A T-SQL: Egyetlen vagy készletezett adatbázisok feladatátvételét kezelése

> [!IMPORTANT]
> Ezeket a Transact-SQL-parancsokat csak aktív georeplikáció vonatkoznak, és nem vonatkoznak a feladatátvételi csoportok. Emiatt a is nem vonatkoznak a felügyelt példányok csak támogatják a feladatátvételi csoportok szerint.

| Parancs | Leírás |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |MÁSODLAGOS ON-kiszolgáló hozzáadása argumentum használatával egy meglévő adatbázist, és elindítja az adatreplikációt egy másodlagos adatbázis létrehozása |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |A FELADATÁTVÉTELI vagy FORCE_FAILOVER_ALLOW_DATA_LOSS használatával válthat egy másodlagos adatbázist elsődlegessé, hogy feladatátvételt kezdeményezzen |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Használja másodlagos ON SERVER eltávolítása egy SQL-adatbázis és a megadott másodlagos adatbázis közötti adatreplikáció leáll. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Az egyes adatbázisok az összes meglévő replikációs hivatkozások adatait adja vissza az Azure SQL Database logikai kiszolgálón. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Lekéri egy adott SQL-adatbázis a legutóbbi replikációs utolsó replikációs késés és egyéb információ a replikációs hivatkozást. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Az összes adatbázis-műveletek, beleértve a replikációs hivatkozások állapotát állapotát jeleníti meg. |
| [az sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |hatására végrehajtott tranzakciók lapblobokban replikálja, és arra vonatkozik, az aktív másodlagos adatbázis várnia, hogy az alkalmazás. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Egyetlen vagy készletezett adatbázisok feladatátvételét kezelése

| Parancsmag | Leírás |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase) |Egy vagy több adatbázist kér le. |
| [New-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Létrehoz egy másodlagos adatbázist egy meglévő adatbázishoz, és elkezdi az adatok replikálását. |
| [Set-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Egy másodlagos adatbázist elsődlegessé tesz, hogy feladatátvételt kezdeményezzen. |
| [Remove-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Leállítja az adatreplikációt egy SQL-adatbázis és a megadott másodlagos adatbázis között. |
| [Get-AzureRmSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Lekéri az Azure SQL Database és egy erőforráscsoport vagy SQL-kiszolgáló közötti georeplikációs hivatkozásokat. |
|  | |

> [!IMPORTANT]
> Mintaszkriptek, lásd: [konfigurálása és a egy önálló adatbázis aktív georeplikációs feladatátvételi](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) és [konfigurálása és a egy készletezett adatbázis aktív georeplikációt használ feladatátvételi](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: Egyetlen vagy készletezett adatbázisok feladatátvételét kezelése

| API | Leírás |
| --- | --- |
| [Hozzon létre vagy az adatbázis frissítése (createMode visszaállítási =)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Hoz létre, frissít, vagy visszaállítja az elsődleges vagy másodlagos adatbázis. |
| [Get létrehozása, vagy az adatbázis állapotának frissítése](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |A létrehozási művelet során állapotának visszaadása. |
| [Másodlagos adatbázist beállítani az elsődleges (tervezett feladatátvétel)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Beállítja a másodlagos adatbázis elsődleges által az aktuális elsődleges adatbázis feladatátvételét. **Ezt a beállítást a felügyelt példány esetében nem támogatott.**|
| [Másodlagos adatbázist beállítani az elsődleges (nem tervezett feladatátvétel)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Beállítja a másodlagos adatbázis elsődleges által az aktuális elsődleges adatbázis feladatátvételét. Ez a művelet adatvesztést okozhat. **Ezt a beállítást a felügyelt példány esetében nem támogatott.**|
| [Replikációs hivatkozás beszerzése](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Lekéri egy adott replikációs hivatkozás egy adott SQL-adatbázis georeplikációs partnerséget. Lekéri az adatokat a sys.geo_replication_links katalógusnézet látható. **Ezt a beállítást a felügyelt példány esetében nem támogatott.**|
| [Replikációs hivatkozások - lista-adatbázis](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Minden replikációs hivatkozásnak lekéri egy adott SQL-adatbázis georeplikációs partnerséget. Lekéri az adatokat a sys.geo_replication_links katalógusnézet látható. |
| [Replikációs hivatkozás törlése](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Egy adatbázis-replikációs hivatkozás törlése. Nem hajtható végre feladatátvétel során. |
|  | |

## <a name="next-steps"></a>További lépések

- Mintaszkriptek lásd:
  - [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Készletezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- Az SQL Database automatikus feladatátvételi csoportok is támogatja. További információkért lásd: használatával [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md).
- Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md)
- További információ az Azure SQL Database automatikus biztonsági mentések, lásd: [SQL-adatbázis automatikus biztonsági mentések](sql-database-automated-backups.md).
- Az automatikus biztonsági másolatokból helyreállítási kapcsolatos további információkért lásd: [adatbázis visszaállítása a szolgáltatás által létrehozott biztonsági másolatokból](sql-database-recovery-using-backups.md).
- Hitelesítési követelmények, egy új elsődleges kiszolgáló és az adatbázis kapcsolatos további információkért lásd: [vész-helyreállítása után az SQL Database biztonsági](sql-database-geo-replication-security-config.md).

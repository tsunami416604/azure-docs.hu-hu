---
title: Aktív georeplikáció
description: Az aktív földrajzi replikálás használatával az egyes adatbázisokból olvasható másodlagos adatbázisok hozhatók létre ugyanazon vagy más adatközpontban (régió).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 07/09/2019
ms.openlocfilehash: 33697fd8d3b0c6faea423026e1462834c6b1ef4c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822660"
---
# <a name="creating-and-using-active-geo-replication"></a>Aktív geo-replikáció létrehozása és használata

Az aktív geo-replikálás Azure SQL Database funkció, amely lehetővé teszi, hogy az azonos vagy eltérő adatközpontban (régióban) található SQL Database-kiszolgálón lévő egyes adatbázisok olvasható másodlagos adatbázisait hozzon létre.

> [!NOTE]
> Felügyelt példány nem támogatja az aktív geo-replikációt. A felügyelt példányok földrajzi feladatátvételéhez használjon [automatikus feladatátvételi csoportokat](sql-database-auto-failover-group.md).

Az aktív földrajzi replikálás olyan üzletmenet-folytonossági megoldás, amely lehetővé teszi, hogy az alkalmazás az egyes adatbázisok gyors vész-helyreállítását regionális katasztrófa vagy nagy léptékű leállás esetén hajtsa végre. Ha a Geo-replikáció engedélyezve van, az alkalmazás feladatátvételt kezdeményezhet egy másik Azure-régióban található másodlagos adatbázisba. Akár négy formátumú másodlagos zónák is támogatott ugyanazon vagy különböző régiókban, és a formátumú másodlagos zónák csak olvasási hozzáférési lekérdezésekhez használható. A feladatátvételt az alkalmazásnak vagy a felhasználónak manuálisan kell kezdeményeznie. A feladatátvételt követően az új elsődleges végpont eltérő kapcsolódási ponttal rendelkezik. Az alábbi ábra egy geo-redundáns felhőalapú alkalmazás tipikus konfigurációját szemlélteti az aktív geo-replikáció használatával.

![aktív geo-replikáció](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> A SQL Database támogatja az automatikus feladatátvételi csoportokat is. További információ: [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)használata. Emellett az aktív földrajzi replikálás nem támogatott a felügyelt példányon belül létrehozott adatbázisok esetén. Vegye fontolóra a felügyelt példányokkal rendelkező [feladatátvételi csoportok](sql-database-auto-failover-group.md) használatát.

Ha bármilyen okból kifolyólag az elsődleges adatbázis meghibásodik, vagy egyszerűen offline állapotba kell helyezni, a feladatátvételt bármely másodlagos adatbázisba elindíthatja. Ha a feladatátvételt az egyik másodlagos adatbázisra aktiválja, az összes többi formátumú másodlagos zónák automatikusan az új elsődlegeshez lesz kapcsolva.

Az egyes adatbázisok replikálását és feladatátvételét kezelheti egy kiszolgálón vagy egy rugalmas készletben az aktív földrajzi replikálás használatával. Ezt a következőket teheti:

- Az [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: önálló adatbázis](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: rugalmas készlet](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: önálló adatbázis vagy rugalmas készlet](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: önálló adatbázis](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Az aktív geo-replikálás kihasználja SQL Server [mindig](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technológiáját, hogy aszinkron módon replikálja a véglegesített tranzakciókat az elsődleges adatbázison egy másodlagos adatbázisba a pillanatkép-elkülönítés használatával. Az automatikus feladatátvételi csoportok lehetővé teszik a csoportos szemantika használatát az aktív földrajzi replikáláson, de ugyanazt az aszinkron replikációs módszert használják. Amíg a másodlagos adatbázis egy adott ponton nem lehet az elsődleges adatbázis mögött, a másodlagos adat garantáltan soha nem lesz részleges tranzakció. A régiók közötti redundancia lehetővé teszi az alkalmazások számára, hogy a természeti katasztrófák, a katasztrofális emberi hibák vagy a kártékony tevékenységek által okozott teljes adatközpontok vagy adatközpontok részeinek végleges elvesztésével gyorsan helyreállítsák az alkalmazásokat. Az adott RPO-információk az [üzletmenet folytonosságának áttekintése című témakörben](sql-database-business-continuity.md)találhatók.

> [!NOTE]
> Ha a két régió között hálózati hiba történik, a kapcsolatok újbóli létrehozásához 10 másodpercenként próbálkozunk.
> [!IMPORTANT]
> Annak biztosításához, hogy a feladatátvétel előtt a rendszer replikálja az elsődleges adatbázis kritikus módosításait, kényszerítse a szinkronizálást, hogy biztosítsa a kritikus módosítások (például a jelszavak frissítései) replikálását. A kényszerített szinkronizálás hatással van a teljesítményre, mert blokkolja a hívó szálat, amíg az összes véglegesített tranzakció replikálódik. Részletekért lásd: [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Az elsődleges adatbázis és a Geo-másodlagos közötti replikációs késés figyeléséhez tekintse meg a következőt: [sys. dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Az alábbi ábra egy olyan aktív geo-replikálási példát mutat be, amely az Egyesült Államok északi középső régiójában, az USA déli középső régiójában, a másodlagos régióban található.

![földrajzi replikálási kapcsolat](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Mivel a másodlagos adatbázisok olvashatók, használhatók csak olvasható munkaterhelések, például jelentési feladatok kiszervezéséhez. Ha aktív földrajzi replikálást használ, akkor a másodlagos adatbázist ugyanabban a régióban lehet létrehozni az elsődlegesnél, de az alkalmazás nem fokozza a katasztrofális hibák miatti rugalmasságot. Ha automatikus feladatátvételi csoportokat használ, a másodlagos adatbázist a rendszer mindig egy másik régióban hozza létre.

A vész-helyreállítási aktív földrajzi replikáción kívül a következő esetekben is használhatók:

- **Adatbázis-áttelepítés**: az aktív geo-replikálás használatával áttelepíthet egy adatbázist az egyik kiszolgálóról a másikra minimális állásidővel.
- **Alkalmazások frissítése**: az alkalmazások frissítése során további másodlagosként is létrehozhat egy feladat-visszavételi másolatot.

A valós Üzletmenet-folytonosság eléréséhez az adatközpontok közötti adatbázis-redundancia csak a megoldás része. Egy alkalmazás (szolgáltatás) teljes körű helyreállítása végzetes hiba után a szolgáltatást és a függő szolgáltatásokat alkotó összes összetevő helyreállítását igényli. Ezen összetevők közé tartoznak például az ügyfélszoftverek (például egy egyéni JavaScripttel rendelkező böngésző), a webes kezelőfelületek, a tárterület és a DNS. Kritikus fontosságú, hogy minden összetevő azonos hibákra legyen rugalmas, és az alkalmazás helyreállítási idő célkitűzésén (RTO) belül elérhetővé váljon. Ezért az összes függő szolgáltatást azonosítania kell, és ismernie kell az általuk nyújtott garanciákat és képességeket. Ezt követően meg kell tennie a megfelelő lépéseket annak biztosításához, hogy a szolgáltatás a szolgáltatások feladatátvétele alatt működjön, amelytől függ. A megoldásoknak a vész-helyreállításhoz való kialakításával kapcsolatos további információkért lásd: [felhőalapú megoldások tervezése a vész-helyreállításhoz az aktív földrajzi replikálás használatával](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktív geo-replikációs terminológia és képességek

- **Automatikus aszinkron replikáció**

  Létrehozhat egy másodlagos adatbázist egy meglévő adatbázis hozzáadásával. A másodlagos a Azure SQL Database-kiszolgálókon is létrehozható. A létrehozást követően a másodlagos adatbázis az elsődleges adatbázisból másolt adatokkal lesz feltöltve. Ez a folyamat más néven kivetés. A másodlagos adatbázis létrehozása és összevetése után a rendszer automatikusan replikálja az elsődleges adatbázis frissítéseit a másodlagos adatbázisba. Az aszinkron replikáció azt jelenti, hogy a tranzakciók véglegesítése az elsődleges adatbázison történik, mielőtt a rendszer replikálja őket a másodlagos adatbázisba.

- **Olvasható másodlagos adatbázisok**

  Egy alkalmazás az elsődleges adatbázishoz való hozzáféréshez használt azonos vagy eltérő rendszerbiztonsági tag használatával tud hozzáférni egy másodlagos adatbázishoz az írásvédett műveletekhez. A másodlagos adatbázisok pillanatkép-elkülönítési módban működnek, így biztosítva, hogy az elsődleges (log replay) frissítések replikálása ne legyen késleltetve a másodlagos lekérdezésekben.

> [!NOTE]
> A napló újrajátszása késleltetve van a másodlagos adatbázisban, ha az elsődlegesen vannak séma-frissítések. Az utóbbihoz a másodlagos adatbázis sémájának zárolása szükséges.
> [!IMPORTANT]
> A Geo-replikálás használatával létrehozhat egy másodlagos adatbázist ugyanabban a régióban, ahol az elsődleges. Ezt a másodlagost használhatja a csak olvasási feladatok terheléselosztásához ugyanabban a régióban. Az ugyanabban a régióban található másodlagos adatbázisok azonban nem biztosítanak további hibatűrési rugalmasságot, ezért nem megfelelő feladatátvételi cél a vész-helyreállításhoz. Emellett a rendelkezésre állási zónák elkülönítését sem fogja garantálni. A rendelkezésre állási zónák elkülönítésének eléréséhez használja az üzleti szempontból kritikus vagy prémium szintű szolgáltatási szintet a [zóna redundáns konfigurációjával](sql-database-high-availability.md#zone-redundant-configuration) .   
>

- **Tervezett feladatátvétel**

  A tervezett feladatátvétel az elsődleges és a másodlagos adatbázisok szerepköreit a teljes szinkronizálás befejeződése után váltja át. Ez egy online művelet, amely nem eredményez adatvesztést. A művelet időpontja a szinkronizálni kívánt elsődleges tranzakciós napló méretétől függ. A tervezett feladatátvételt a következő esetekben tervezték meg: (a) a DR működés közbeni működéséhez, ha az adatvesztés nem fogadható el; (b) az adatbázis másik régióba való áthelyezéséhez; és (c) az adatbázis visszaadása az elsődleges régióba a leállás csökkentése után (feladat-visszavétel).

- **Nem tervezett feladatátvétel**

  A nem tervezett vagy kényszerített feladatátvétel azonnal átváltja a másodlagost az elsődleges szerepkörre anélkül, hogy szinkronizálnia kellene az elsődlegesével. Az elsődleges, de a másodlagosra nem replikált tranzakciók elvesznek. A művelet a leállás során helyreállítási módszerként van kialakítva, ha az elsődleges nem érhető el, de az adatbázis rendelkezésre állását gyorsan vissza kell állítani. Amikor az eredeti elsődleges újra online állapotba kerül, automatikusan újrakapcsolódik, és új másodlagos lesz. A rendszer a feladatátvétel előtt minden nem szinkronizált tranzakciót megtart a biztonságimásolat-fájlban, de az ütközések elkerülése érdekében nem szinkronizálja az új elsődleges művelettel. Ezeket a tranzakciókat manuálisan kell egyesíteni az elsődleges adatbázis legújabb verziójával.
 
- **Több olvasható formátumú másodlagos zónák**

  Akár 4 másodlagos adatbázis is létrehozható az egyes elsődlegesekhez. Ha csak egy másodlagos adatbázis van, és az nem sikerül, az alkalmazás nagyobb kockázatnak van kitéve, amíg új másodlagos adatbázist nem hoznak létre. Ha több másodlagos adatbázis létezik, az alkalmazás akkor is védve marad, ha a másodlagos adatbázisok egyike meghibásodik. A további formátumú másodlagos zónák a csak olvasási feladatok felskálázására is felhasználhatók

  > [!NOTE]
  > Ha aktív geo-replikálást használ egy globálisan elosztott alkalmazás létrehozásához, és csak olvasási hozzáférést kell biztosítania a négynél több régióban található adatmennyiséghez, másodlagos másodlagos (azaz láncolási folyamat) létrehozását is létrehozhatja. Így gyakorlatilag korlátlan számú adatbázis-replikáció valósítható meg. Emellett a láncolás csökkenti az elsődleges adatbázis replikációjának terhelését is. A kikapcsolás a levelek és a legfelső szintű adatbázisok megnövekedett replikálási késése.

- **Adatbázisok földrajzi replikálása rugalmas készletben**

  Minden másodlagos adatbázis külön részt vehet egy rugalmas készletben, vagy egyáltalán nem lehet rugalmas készletben. Az egyes másodlagos adatbázisok készletének kiválasztása különálló, és nem függ más másodlagos adatbázisok konfigurációjától (elsődleges vagy másodlagos). Az egyes rugalmas készletek egyetlen régióban találhatók, ezért az azonos topológiában található több másodlagos adatbázis soha nem oszthat meg rugalmas készletet.


- **Felhasználó által vezérelt feladatátvétel és feladat-visszavétel**

  Egy másodlagos adatbázis az alkalmazás vagy a felhasználó által bármikor átváltható az elsődleges szerepkörre. Valós leállás esetén a "nem tervezett" beállítást kell használni, amely azonnal előlépteti a másodlagosat elsődlegesként. Ha a sikertelen elsődleges helyreállítások újra elérhetővé válnak, a rendszer automatikusan másodlagosként jelöli meg a helyreállított elsődlegest, és az új elsődleges értékkel állítja be azt. A replikáció aszinkron jellege miatt a nem tervezett feladatátvételek során a rendszer kis mennyiségű adatvesztést okozhat, ha az elsődleges meghibásodás előtt a legutóbbi módosítások replikálódnak a másodlagosra. Ha a több formátumú másodlagos zónák rendelkező elsődleges művelet feladatátvételt végez, a rendszer automatikusan újrakonfigurálja a replikálási kapcsolatokat, és a fennmaradó formátumú másodlagos zónák az újonnan előléptetett elsődleges értékre kapcsolja anélkül, hogy felhasználói beavatkozásra lenne szükség. A feladatátvételt okozó leállás után érdemes lehet az alkalmazást az elsődleges régióba visszaállítani. Ehhez a feladatátvételi parancsot a "tervezett" beállítással kell meghívni.

## <a name="preparing-secondary-database-for-failover"></a>Másodlagos adatbázis előkészítése feladatátvételhez

Annak biztosítása érdekében, hogy az alkalmazás azonnal hozzáférhessen az új elsődleges feladatátvételhez, ellenőrizze, hogy a másodlagos kiszolgáló és az adatbázis hitelesítési követelményei megfelelően vannak-e konfigurálva. Részletekért lásd: [SQL Database biztonság a katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md). A megfelelőség biztosítása érdekében a feladatátvételt követően ellenőrizze, hogy a másodlagos adatbázis biztonsági mentési adatmegőrzési szabályzata megegyezik-e az elsődleges értékkel. Ezek a beállítások nem részei az adatbázisnak, és nem replikálódnak. Alapértelmezés szerint a másodlagos beállítás a hét nap alapértelmezett PITR megőrzési időtartamával lesz konfigurálva. Részletekért lásd: [SQL Database automatikus biztonsági mentések](sql-database-automated-backups.md).

> [!IMPORTANT]
> Ha az adatbázis egy feladatátvételi csoport tagja, nem kezdeményezhet feladatátvételt a Geo-Replication faiover parancs használatával. Érdemes lehet feladatátvételi parancsot használni a csoport számára. Ha egy egyedi adatbázist kell feladatátvételt végeznie, először el kell távolítania azt a feladatátvételi csoportból. Részletekért lásd: [feladatátvételi csoportok](sql-database-auto-failover-group.md) . 


## <a name="configuring-secondary-database"></a>Másodlagos adatbázis konfigurálása

Mind az elsődleges, mind a másodlagos adatbázisnak azonos szolgáltatási szinten kell lennie. Javasoljuk továbbá, hogy a másodlagos adatbázist ugyanazzal a számítási mérettel (DTU vagy virtuális mag) hozza létre, mint az elsődlegesként. Ha az elsődleges adatbázis nagy mennyiségű írási feladatot észlel, akkor előfordulhat, hogy az alacsonyabb számítási mérettel rendelkező másodlagos nem tud lépést tartani. Ennek hatására az ismételt késés a másodlagos és lehetséges elérhetetlenség miatt nem lesz elérhető. A másodlagos adatbázis esetében, amely az elsődleges adatbázishoz képest késik, szintén felmerülhet a nagy adatvesztés kockázata, és kényszerített feladatátvételre lehet szükség. A kockázatok enyhítése érdekében a hatékony aktív földrajzi replikálás az elsődleges napló sebességét fogja szabályozni, hogy a formátumú másodlagos zónák felkerüljön. Egy kiegyensúlyozatlan másodlagos konfiguráció másik következménye, hogy a feladatátvételt követően az alkalmazás teljesítménye az új elsődleges számítási kapacitás hiánya miatt fog szenvedni. A szükséges szintre kell frissíteni a magasabb számítási kapacitást, amely a leállás mérséklése előtt nem lesz lehetséges. 


> [!IMPORTANT]
> A közzétett RPO = 5 MP nem garantálható, ha a másodlagos adatbázis ugyanazzal a számítási mérettel van konfigurálva, mint az elsődleges. 


Ha úgy dönt, hogy az alacsonyabb számítási mérettel hozza létre a másodlagos értéket, a log IO százalékos diagramja Azure Portal jó módszert kínál a replikálási terhelés fenntartásához szükséges másodlagos minimális számítási méret becslésére. Ha például az elsődleges adatbázisa P6 (1000 DTU), és a log IO százalék értéke 50%, a másodlagosnak legalább P4 (500 DTU) értékkel kell rendelkeznie. A log IO-adatai a [sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vagy a [sys. dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) adatbázis-nézetek használatával is lekérhető.  A szabályozás HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO várakozási állapotként szerepel a [sys. dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és a [sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) adatbázis-nézetekben. 

A SQL Database számítási méretekkel kapcsolatos további információkért lásd: [Mi a SQL Database szolgáltatási szintek](sql-database-purchase-models.md).

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>A hitelesítő adatok és a tűzfalszabályok szinkronizálásának megtartása

Javasoljuk, hogy az [adatbázis-szintű IP-tűzfalszabályok](sql-database-firewall-configure.md) használatával geo-alapú adatbázisokat használjon, így ezek a szabályok replikálhatók az adatbázissal annak biztosítása érdekében, hogy az összes másodlagos adatbázis azonos IP-tűzfalszabályok legyenek az elsődlegesek. Ez a megközelítés szükségtelenné teszi az ügyfelek számára a tűzfalszabályok manuális konfigurálását és karbantartását az elsődleges és másodlagos adatbázisokat üzemeltető kiszolgálókon. Hasonlóképpen, a [tárolt adatbázis-felhasználók](sql-database-manage-logins.md) adathozzáféréshez való használata biztosítja, hogy az elsődleges és a másodlagos adatbázisok mindig ugyanazzal a felhasználói hitelesítő adatokkal rendelkezzenek, ezért a feladatátvétel során a bejelentkezések és a jelszavak nem egyeznek. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)hozzáadásával az ügyfelek az elsődleges és a másodlagos adatbázisokhoz is kezelhetik a felhasználói hozzáférést, így nem kell a hitelesítő adatoknak az adatbázisokban való kezelését.

## <a name="upgrading-or-downgrading-primary-database"></a>Elsődleges adatbázis frissítése vagy visszaminősítése

A másodlagos adatbázisok leválasztása nélkül frissítheti vagy visszaminősítheti az elsődleges adatbázist más számítási méretre (ugyanazon a szolgáltatási szinten belül, általános célú és üzletileg kritikus között). A frissítéskor javasoljuk, hogy először frissítse a másodlagos adatbázist, majd frissítse az elsődlegest. Ha a rendszer visszaminősíti a visszalépést, a sorrend megfordításakor a rendszer visszaminősíti az elsődlegest, majd lecseréli a másodlagost. Ha az adatbázist egy másik szolgáltatási szintre frissíti vagy visszaminősíti, ez a javaslat érvénybe lép.

> [!NOTE]
> Ha a másodlagos adatbázist a feladatátvételi csoport konfigurációjának részeként hozta létre, a másodlagos adatbázis visszalépéséhez nem ajánlott. Ezzel biztosíthatja, hogy az adatmennyiség elegendő kapacitással legyen feldolgozva a rendszeres számítási feladatok elvégzése után a feladatátvétel aktiválása után.

> [!IMPORTANT]
> A feladatátvételi csoportban lévő elsődleges adatbázis nem méretezhető magasabb szintűre, kivéve, ha a másodlagos adatbázist először a magasabb szintűre méretezi. Ha az elsődleges adatbázist a másodlagos adatbázis skálázása előtt próbálja meg méretezni, a következő hibaüzenet jelenhet meg:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>A kritikus fontosságú adatmennyiség elvesztésének megakadályozása

A nagyméretű hálózatok nagy késése miatt a folyamatos másolás aszinkron replikációs mechanizmust használ. Az aszinkron replikáció során az adatvesztés elkerülhető, ha hiba történik. Előfordulhat azonban, hogy egyes alkalmazások nem igényelnek adatvesztést. A kritikus frissítések elleni védelem érdekében az alkalmazás fejlesztője a tranzakció véglegesítése után azonnal meghívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) rendszereljárást. A hívás **sp_wait_for_database_copy_sync** blokkolja a hívó szálat, amíg az utolsó véglegesített tranzakció át nem lett továbbítva a másodlagos adatbázisba. Azonban nem várja meg, amíg a továbbított tranzakciók újra le lesznek játszva és véglegesítve lettek a másodlagoson. **sp_wait_for_database_copy_sync** hatóköre egy adott folyamatos másolási hivatkozásra vonatkozik. Minden olyan felhasználó, aki az elsődleges adatbázishoz kapcsolódási jogokkal rendelkezik, meghívhatja ezt az eljárást.

> [!NOTE]
> **sp_wait_for_database_copy_sync** megakadályozza az adatvesztést a feladatátvétel után, de nem garantálja a teljes szinkronizálást az olvasási hozzáféréshez. A **sp_wait_for_database_copy_synci** eljárás hívása által okozott késleltetés jelentős lehet, és a tranzakciós napló méretétől függ a hívás időpontjában.

## <a name="monitoring-geo-replication-lag"></a>Földrajzi replikációs késés figyelése

A RPO vonatkozó késés figyeléséhez használja a [sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) *replication_lag_sec* oszlopát az elsődleges adatbázison. Másodpercek alatt az elsődleges és a másodlagoson megőrzött tranzakciók közötti késést jeleníti meg. Például Ha a késés értéke 1 másodperc, akkor az azt jelenti, hogy ha az elsődlegest egy kimaradás okozta, és a feladatátvételt kezdeményezték, a legutóbbi átmenetek közül 1 másodperc nem lesz mentve. 

Annak érdekében, hogy az elsődleges adatbázison végrehajtott, a másodlagosnál alkalmazott változásokkal kapcsolatos késés mértékét, azaz a másodlagosról való olvasásra elérhető legyen, hasonlítsa össze *last_commit* időt a másodlagos adatbázison ugyanazzal az értékkel az elsődleges adatbázison.

> [!NOTE]
> Az elsődleges adatbázis *replication_lag_sec* néha NULL értékkel rendelkezik, ami azt jelenti, hogy az elsődleges nem tudja, hogy meddig van a másodlagos.   Ez általában a folyamat újraindítása után történik, és átmeneti feltételnek kell lennie. Vegye fontolóra az alkalmazás riasztását, ha a *replication_lag_sec* hosszabb ideig null értéket ad vissza. Ez azt jelzi, hogy a másodlagos adatbázis nem tud kommunikálni az elsődlegesvel állandó csatlakozási hiba miatt. Olyan feltételek is fennállnak, amelyek a másodlagos és az elsődleges adatbázis *last_commit* ideje közötti különbséget okozhatják. Például Ha az elsődleges végrehajtás hosszú időn belül nem változik, a különbség egy nagy értékre ugrik, mielőtt a rendszer gyorsan visszatér 0-ra. Vegye figyelembe, hogy a hiba feltétele, ha a két érték közötti különbség hosszú ideig nagy marad.


## <a name="programmatically-managing-active-geo-replication"></a>Az aktív földrajzi replikáció programozott kezelése

Amint azt korábban már említettük, az aktív geo-replikáció programozott módon felügyelhető Azure PowerShell és a REST API használatával is. A következő táblázatok ismertetik az elérhető parancsok készletét. Az aktív geo-replikálás Azure Resource Manager API-kat tartalmaz a felügyelethez, beleértve a [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) és [Azure PowerShell parancsmagokat](https://docs.microsoft.com/powershell/azure/overview). Ezek az API-k az erőforráscsoportok használatát igénylik, és támogatják a szerepköralapú biztonságot (RBAC). A hozzáférési szerepkörök megvalósításával kapcsolatos további információkért lásd: [Azure szerepköralapú Access Control](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: az önálló és a készletezett adatbázisok feladatátvételének kezelése

> [!IMPORTANT]
> Ezek a Transact-SQL-parancsok csak az aktív földrajzi replikálásra vonatkoznak, és nem vonatkoznak a feladatátvételi csoportokra. Ezért nem vonatkoznak a felügyelt példányokra is, mivel csak a feladatátvételi csoportokat támogatják.

| Parancs | Leírás |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Másodlagos adatbázis hozzáadása egy meglévő adatbázishoz, és az adatreplikálás elindításához használja a másodlagos kiszolgáló hozzáadása argumentumot. |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Feladatátvétel vagy FORCE_FAILOVER_ALLOW_DATA_LOSS használatával váltson át másodlagos adatbázist elsődlegesre a feladatátvétel indításához |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Használja a másodlagos eltávolítása a kiszolgálón lehetőséget a SQL Database és a megadott másodlagos adatbázis közötti adatreplikáció megszakításához. |
| [sys. geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |A Azure SQL Database-kiszolgálón található egyes adatbázisok összes meglévő replikációs hivatkozására vonatkozó információt adja vissza. |
| [sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Lekérdezi a legutóbbi replikálási időt, a legutóbbi replikálási késést és az adott SQL-adatbázis replikációs hivatkozásával kapcsolatos egyéb információkat. |
| [sys. dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Megjeleníti az összes adatbázis-művelet állapotát, beleértve a replikációs hivatkozások állapotát is. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |azt eredményezi, hogy az alkalmazás megvárja az összes véglegesített tranzakció replikálását és az aktív másodlagos adatbázis által történő elfogadását. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: az önálló és a készletezett adatbázisok feladatátvételének kezelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

| Parancsmag | Leírás |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Egy vagy több adatbázist kér le. |
| [Új – AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Létrehoz egy másodlagos adatbázist egy meglévő adatbázishoz, és elkezdi az adatok replikálását. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Egy másodlagos adatbázist elsődlegessé tesz, hogy feladatátvételt kezdeményezzen. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Leállítja az adatreplikációt egy SQL-adatbázis és a megadott másodlagos adatbázis között. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Lekéri az Azure SQL-adatbázis és egy erőforráscsoport vagy SQL-kiszolgáló közötti georeplikációs hivatkozásokat. |
|  | |

> [!IMPORTANT]
> A példákat lásd: [önálló adatbázisok konfigurálása és feladatátvétele az aktív geo-replikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) , valamint [a készletezett adatbázisok konfigurálása és feladatátvétele az aktív geo-replikáció használatával](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: önálló és készletezett adatbázisok feladatátvételének kezelése

| API | Leírás |
| --- | --- |
| [Adatbázis létrehozása vagy frissítése (createMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Egy elsődleges vagy egy másodlagos adatbázis létrehozása, frissítése vagy visszaállítása. |
| [Adatbázis-létrehozási vagy-frissítési állapot beolvasása](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Az állapotot adja vissza egy létrehozási művelet során. |
| [Másodlagos adatbázis beállítása elsődlegesként (tervezett feladatátvétel)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Meghatározza, hogy melyik másodlagos adatbázis legyen elsődleges, ha az aktuális elsődleges adatbázisból feladatátvételt hajt végre. **Ez a beállítás felügyelt példány esetén nem támogatott.**|
| [Másodlagos adatbázis beállítása elsődlegesként (nem tervezett feladatátvétel)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Meghatározza, hogy melyik másodlagos adatbázis legyen elsődleges, ha az aktuális elsődleges adatbázisból feladatátvételt hajt végre. A művelet adatvesztéshez vezethet. **Ez a beállítás felügyelt példány esetén nem támogatott.**|
| [Replikációs hivatkozás beolvasása](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Egy adott SQL-adatbázis adott replikációs hivatkozásának beolvasása egy földrajzi replikálási partnerségben. Lekéri a sys. geo_replication_links Catalog nézetben látható adatokat. **Ez a beállítás felügyelt példány esetén nem támogatott.**|
| [Replikációs hivatkozások – lista adatbázis szerint](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Egy adott SQL-adatbázis összes replikációs hivatkozásának beolvasása egy földrajzi replikálási partnerségben. Lekéri a sys. geo_replication_links Catalog nézetben látható adatokat. |
| [Replikációs hivatkozás törlése](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Töröl egy adatbázis-replikációs hivatkozást. Feladatátvétel közben nem végezhető el. |
|  | |

## <a name="next-steps"></a>További lépések

- A minta parancsfájlokat lásd:
  - [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Rugalmas készletbe helyezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- A SQL Database támogatja az automatikus feladatátvételi csoportokat is. További információ: [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)használata.
- Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md)
- Az automatikus biztonsági mentések Azure SQL Databaseáról a [SQL Database automatizált biztonsági mentések](sql-database-automated-backups.md)című témakörben olvashat bővebben.
- Ha többet szeretne megtudni a helyreállítás automatizált biztonsági mentéséről, olvassa el [az adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatokból](sql-database-recovery-using-backups.md)című témakört.
- Az új elsődleges kiszolgáló és adatbázis hitelesítési követelményeinek megismeréséhez tekintse meg a SQL Database biztonság a vész- [helyreállítás után](sql-database-geo-replication-security-config.md)című témakört.

---
title: Aktív georeplikáció
description: Az aktív georeplikáció használatával azonos vagy különböző adatközpontokban (régióban) lévő különálló adatbázisok olvasható másodlagos adatbázisait hozhassa létre.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/17/2020
ms.openlocfilehash: b80b58d64ea27df95c2704243d8a89fa6ca12e2a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548508"
---
# <a name="creating-and-using-active-geo-replication"></a>Aktív georeplikáció létrehozása és használata

Az aktív georeplikáció egy Azure SQL Database szolgáltatás, amely lehetővé teszi, hogy az egyes adatbázisok olvasható másodlagos adatbázisait egy SQL Database-kiszolgálón azonos vagy különböző adatközpontokban (régióban) hozza létre.

> [!NOTE]
> Az aktív georeplikációt a felügyelt példány nem támogatja. A felügyelt példányok földrajzi feladatátvételhez használja az [automatikus feladatátvételi csoportokat.](sql-database-auto-failover-group.md)

Az aktív georeplikáció olyan üzletmenet-folytonossági megoldásként van kialakítva, amely lehetővé teszi az alkalmazás számára, hogy regionális katasztrófa vagy nagymértékű kimaradás esetén gyors vészhelyreállítást hajtson végre az egyes adatbázisokban. Ha a georeplikáció engedélyezve van, az alkalmazás kezdeményezheti feladatátvétel egy másik Azure-régióban egy másodlagos adatbázisba. Legfeljebb négy másodlagos támogatott ugyanabban a régióban, és a másodlagos is használható írásvédett hozzáférési lekérdezések. A feladatátvételt az alkalmazásnak vagy a felhasználónak manuálisan kell kezdeményeznie. A feladatátvétel után az új elsődleges egy másik csatlakozási végpont. Az alábbi ábra egy georedundáns felhőalkalmazás aktív georeplikációval történő tipikus konfigurációját mutatja be.

![aktív georeplikáció](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> Az SQL Database támogatja az automatikus feladatátvételi csoportokat is. További információt az [automatikus feladatátvételi csoportok használata című témakörben talál.](sql-database-auto-failover-group.md) Emellett az aktív georeplikáció nem támogatott a felügyelt példányon belül létrehozott adatbázisok esetében. Fontolja meg [feladatátvételi csoportok](sql-database-auto-failover-group.md) használatát felügyelt példányokkal.

Ha bármilyen okból az elsődleges adatbázis meghibásodik, vagy egyszerűen offline állapotba kell helyezni, kezdeményezheti a feladatátvételt bármelyik másodlagos adatbázisba. Ha a feladatátvétel a másodlagos adatbázisok egyikére van aktiválva, az összes többi másodlagos automatikusan kapcsolódik az új elsődleges.

Az egyes adatbázisok vagy adatbázisok egy csoportjának replikációját és feladatátvételét kezelheti egy kiszolgálón vagy egy rugalmas készletben az aktív georeplikáció használatával. Ezt a következő használatával teheti meg:

- Az [Azure portál](sql-database-geo-replication-portal.md)
- [PowerShell: Egyetlen adatbázis](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Rugalmas készlet](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Egyetlen adatbázis vagy rugalmas készlet](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Egyetlen adatbázis](https://docs.microsoft.com/rest/api/sql/replicationlinks)


Az aktív georeplikáció az SQL Server [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technológiáját használja az elsődleges adatbázis véglegesített tranzakcióinak aszinkron replikálásához egy másodlagos adatbázisba pillanatkép-elkülönítés használatával. Az automatikus feladatátvételi csoportok biztosítják a csoport szemantikáját az aktív georeplikáció felett, de ugyanazt az aszinkron replikációs mechanizmust használják. Bár egy adott ponton a másodlagos adatbázis kissé az elsődleges adatbázis mögött lehet, a másodlagos adatok garantáltan soha nem rendelkeznek részleges tranzakciókkal. A régiók közötti redundancia lehetővé teszi, hogy az alkalmazások gyorsan helyreálljanak egy teljes adatközpont vagy egy adatközpont természeti katasztrófák, katasztrofális emberi hibák vagy rosszindulatú cselekmények által okozott részeinek végleges elvesztése esetén. A konkrét RPO-adatok az [üzletmenet-folytonosság áttekintése](sql-database-business-continuity.md)oldalon találhatók.

> [!NOTE]
> Ha hálózati hiba van két régió között, 10 másodpercenként újra megpróbáljuk a kapcsolatok helyreállítását.
> [!IMPORTANT]
> Annak biztosítása érdekében, hogy az elsődleges adatbázis kritikus módosítása a feladatátvétel előtt replikálja a másodlagos adatbázisba, kényszerítheti a szinkronizálást a kritikus módosítások (például jelszófrissítések) replikációjának biztosítása érdekében. A kényszerített szinkronizálás hatással van a teljesítményre, mivel blokkolja a hívó szálat, amíg az összes véglegesített tranzakció replikálása meg nem ismétlődik. További részletek: [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Az elsődleges adatbázis és a geoszekunder közötti replikációs késés figyeléséhez olvassa el a [sys.dm_geo_replication_link_status című témakört.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)

Az alábbi ábra egy példát mutat be az USA északi középső régiójában elsődleges és másodlagos, usa déli középső régióban elsődlegessel konfigurált aktív georeplikációra.

![georeplikációs kapcsolat](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Mivel a másodlagos adatbázisok olvashatók, csak olvasási számítási feladatok, például a jelentéskészítési feladatok kiszervezéséhez használhatók. Ha aktív georeplikációt használ, a másodlagos adatbázis t a másodlagos adatbázis az elsődleges, de ez nem növeli az alkalmazás ellenálló képességét a katasztrofális hibák. Ha automatikus feladatátvételi csoportokat használ, a másodlagos adatbázis mindig egy másik régióban jön létre.

A vész-helyreállítási aktív georeplikáció mellett a következő esetekben is használható:

- **Adatbázis-áttelepítés**: Az aktív georeplikáció segítségével az adatbázist az egyik kiszolgálóról a másikra telepítheti online, minimális állásidővel.
- **Alkalmazásfrissítések**: Az alkalmazásfrissítések során létrehozhat egy további másodlagos példányt feladat-visszavételi másolatként.

A valódi üzletmenet-folytonosság elérése érdekében az adatközpontok közötti adatbázis-redundancia hozzáadása csak egy része a megoldásnak. Egy alkalmazás (szolgáltatás) teljes körű helyreállítása egy katasztrofális hiba után a szolgáltatást és a függő szolgáltatásokat alkotó összes összetevő helyreállítását igényli. Ilyen összetevők például az ügyfélszoftver (például egy egyéni JavaScript-alapú böngésző), a webes előtér-végek, a tárolás és a DNS. Fontos, hogy minden összetevő rugalmas legyen az azonos hibákkal szemben, és elérhetővé váljon az alkalmazás helyreállítási idő célkitűzésén (RTO) belül. Ezért azonosítania kell az összes függő szolgáltatást, és meg kell értenie az általuk nyújtott garanciákat és képességeket. Ezután meg kell tennie a megfelelő lépéseket annak biztosítására, hogy a szolgáltatás a szolgáltatások feladatátvétele során, amelytől függ, megfelelő lépéseket kell tennie. A vész-helyreállítási megoldások tervezéséről további információt a [Felhőbeli megoldások tervezése az aktív georeplikáció használatával című vész-helyreállítási megoldásokról](sql-database-designing-cloud-solutions-for-disaster-recovery.md)talál.

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktív georeplikációs terminológia és képességek

- **Automatikus aszinkron replikáció**

  Másodlagos adatbázist csak meglévő adatbázishoz való hozzáadással hozhat létre. A másodlagos bármely Azure SQL Database-kiszolgálón létrehozható. A létrehozás után a másodlagos adatbázis az elsődleges adatbázisból másolt adatokkal lesz feltöltve. Ezt a folyamatot vetésnek nevezzük. A másodlagos adatbázis létrehozása és beültetése után az elsődleges adatbázis frissítései automatikusan replikálódnak a másodlagos adatbázisba. Az aszinkron replikáció azt jelenti, hogy a tranzakciók véglegesítése az elsődleges adatbázisban történik, mielőtt replikálnák őket a másodlagos adatbázisba.

- **Olvasható másodlagos adatbázisok**

  Az alkalmazások hozzáférhetnek egy másodlagos adatbázis csak olvasható műveletek segítségével az azonos vagy különböző rendszerbiztonsági tagok eléréséhez használt elsődleges adatbázis. A másodlagos adatbázisok pillanatkép-elkülönítési módban működnek annak biztosítása érdekében, hogy az elsődleges frissítések (napló ismétlés) replikációját ne késsék a másodlagoson végrehajtott lekérdezések.

> [!NOTE]
> A napló visszajátszása késik a másodlagos adatbázisban, ha sémafrissítések vannak az elsődlegesen. Ez utóbbihoz sémazárolásszükséges a másodlagos adatbázison.
> [!IMPORTANT]
> A georeplikáció segítségével másodlagos adatbázist hozhat létre az elsődleges régióban. Ezzel a másodlagos betöltési-kiegyenlítési egy csak olvasható számítási feladatok ugyanabban a régióban. Azonban egy másodlagos adatbázis ugyanabban a régióban nem nyújt további hiba rugalmasságot, és ezért nem megfelelő feladatátvételi cél a vész-helyreállítási. Emellett nem garantálja a rendelkezésre állási zóna elkülönítését. Használja az üzleti kritikus vagy prémium szintű szolgáltatásszint [zóna redundáns konfigurációval](sql-database-high-availability.md#zone-redundant-configuration) a rendelkezésre állási zóna elkülönítésének eléréséhez.   
>

- **Tervezett feladatátvétel**

  A tervezett feladatátvétel az elsődleges és másodlagos adatbázisok szerepköreit váltja át a teljes szinkronizálás befejezése után. Ez egy online művelet, amely nem eredményez adatvesztést. A művelet ideje a szinkronizálandó elsődleges tranzakciónapló méretétől függ. A tervezett feladatátvétel a következő esetekben készült: (a) vész-monoxid-gyakorlatok végrehajtása éles környezetben, ha az adatvesztés nem elfogadható; b) az adatbázis áthelyezése egy másik régióba; és (c) az adatbázis visszaküldése az elsődleges régióba a kimaradás mérséklése után (feladat-visszavétel).

- **Nem tervezett feladatátvétel**

  Nem tervezett vagy kényszerített feladatátvétel azonnal átváltja a másodlagos az elsődleges szerepkör nélkül szinkronizálás az elsődleges. Az elsődleges, de nem replikált, a másodlagos tranzakciók elvesznek. Ez a művelet helyreállítási módszerként van kimaradások során, amikor az elsődleges nem érhető el, de az adatbázis rendelkezésre állását gyorsan vissza kell állítani. Amikor az eredeti elsődleges újra online, akkor automatikusan újra csatlakozik, és lesz egy új másodlagos. A feladatátvétel előtti összes nem szinkronizált tranzakció megmarad a biztonságimásolat-fájlban, de az ütközések elkerülése érdekében nem lesz szinkronizálva az új elsődleges tranzakcióval. Ezeket a tranzakciókat manuálisan kell egyesíteni az elsődleges adatbázis legújabb verziójával.
 
- **Több olvasható másodlagos**

  Minden elsődlegeshez legfeljebb 4 másodlagos adatbázis hozható létre. Ha csak egy másodlagos adatbázis van, és sikertelen, az alkalmazás nagyobb kockázatnak van kitéve, amíg létre nem jön egy új másodlagos adatbázis. Ha több másodlagos adatbázis létezik, az alkalmazás akkor is védett marad, ha a másodlagos adatbázisok egyike meghibásodik. A további másodlagos fájlok is használhatók az írásvédett számítási feladatok horizontális felskálázására

  > [!NOTE]
  > Ha egy globálisan elosztott alkalmazás létrehozásához aktív georeplikációt használ, és négynél több területen kell csak olvasható hozzáférést biztosítania az adatokhoz, másodlagos (láncolásnak nevezett folyamat) másodlagos t. Így gyakorlatilag korlátlan méretű adatbázis-replikáció érhető el. Ezenkívül a láncolás csökkenti az elsődleges adatbázis replikációjának terhelését. A kompromisszum a levél-legfelső másodlagos adatbázisok megnövekedett replikációs késése.

- **Adatbázisok földrajzi replikációja rugalmas készletben**

  Minden másodlagos adatbázis külön-külön részt vehet egy rugalmas készletben, vagy egyáltalán nem lehet semmilyen rugalmas készletben. A készlet választás minden másodlagos adatbázis külön, és nem függ a konfiguráció bármely más másodlagos adatbázis (akár elsődleges, akár másodlagos). Minden rugalmas készlet egyetlen régióban található, ezért több másodlagos adatbázisok ugyanabban a topológiában soha nem osztozik egy rugalmas készlet.


- **Felhasználó által vezérelt feladatátvétel és feladat-feladat-visszavétel**

  A másodlagos adatbázis tiklatható explicit módon az elsődleges szerepkörre az alkalmazás vagy a felhasználó által. A tényleges kimaradás során a "nem tervezett" opciót kell használni, amely azonnal elősegíti a másodlagos, hogy az elsődleges. Amikor a sikertelen elsődleges helyreáll, és ismét elérhetővé válik, a rendszer automatikusan másodlagosként jelöli meg a helyreállított elsődleges állapotot, és naprakészen állítja az új elsődleges sel. A replikáció aszinkron jellege miatt kis mennyiségű adat elveszhet a nem tervezett feladatátvételek során, ha az elsődleges sikertelen lesz, mielőtt replikálja a legutóbbi módosításokat a másodlagosra. Ha egy több másodlagos elsődleges feladatát, a rendszer automatikusan újrakonfigurálja a replikációs kapcsolatokat, és felhasználói beavatkozás nélkül kapcsolja össze a fennmaradó másodlagos adatokat az újonnan előléptetett elsődleges állapottal. Miután a feladatátvételt okozó kimaradás mérséklése befejeződött, kívánatos lehet az alkalmazás visszaküldése az elsődleges régióba. Ehhez a feladatátvételi parancsot a "tervezett" opcióval kell meghívni.

## <a name="preparing-secondary-database-for-failover"></a>Másodlagos adatbázis előkészítése feladatátvételre

Annak érdekében, hogy az alkalmazás a feladatátvétel után azonnal hozzáférhessen az új elsődleges elemhez, győződjön meg arról, hogy a másodlagos kiszolgáló és az adatbázis hitelesítési követelményei megfelelően vannak konfigurálva. További információt az [SQL Database biztonsága a vészhelyreállítás után című témakörben talál.](sql-database-geo-replication-security-config.md) A feladatátvétel utáni megfelelőség biztosítása érdekében győződjön meg arról, hogy a másodlagos adatbázis biztonsági mentési adatmegőrzési házirendje megegyezik az elsődleges biztonsági mentési házirenddel. Ezek a beállítások nem részei az adatbázisnak, és nem replikálódnak. Alapértelmezés szerint a másodlagos lesz konfigurálva egy alapértelmezett PITR megőrzési időszak hét nap. További információt az [SQL Database automatikus biztonsági másolatai című témakörben talál.](sql-database-automated-backups.md)

> [!IMPORTANT]
> Ha az adatbázis egy feladatátvételi csoport tagja, a georeplikáció faiover paranccsal nem kezdeményezheti a feladatátvételt. Fontolja meg a feladatátvételi parancs használatát a csoporthoz. Ha egy adott adatbázisfeladat-átvételre van szüksége, először el kell távolítania a feladatátvételi csoportból. A részleteket a [feladatátvételi csoportokban](sql-database-auto-failover-group.md) találja. 


## <a name="configuring-secondary-database"></a>Másodlagos adatbázis konfigurálása

Mind az elsődleges, mind a másodlagos adatbázisoknak azonos szolgáltatási szinttel kell rendelkezniük. Azt is erősen ajánlott, hogy a másodlagos adatbázis jön létre az elsődleges azonos számítási mérettel (DT-k vagy virtuális magok). Ha az elsődleges adatbázis nagy írási számítási feladatokat tapasztal, előfordulhat, hogy egy kisebb számítási méretű másodlagos nem tud lépést tartani vele. Ez okozza a redo lag a másodlagos és a potenciális elérhetetlenség. Egy másodlagos adatbázis, amely elmarad az elsődleges is kockáztatja a nagy adatvesztés, ha egy kényszerű feladatátvétel szükséges. Ezek a kockázatok csökkentése érdekében a hatékony aktív georeplikáció szabályozza az elsődleges naplóarány, hogy a másodlagos felzárkózni. A kiegyensúlyozatlan másodlagos konfiguráció másik következménye az, hogy az alkalmazás teljesítményének feladatátvétele után az új elsődleges elégtelen számítási kapacitása miatt. A szükséges szintre való magasabb számítási szintre kell frissíteni, ami nem lehetséges, amíg a kimaradás nem mérséklődik. 


> [!IMPORTANT]
> A közzétett RPO = 5 mp nem garantálható, kivéve, ha a másodlagos adatbázis van konfigurálva az elsődleges azonos számítási méret. 


Ha úgy dönt, hogy alacsonyabb számítási mérettel hozza létre a másodlagos, a log IO százalékos diagram az Azure Portalon jó módja annak, hogy megbecsülje a másodlagos, amely a replikációs terhelés fenntartásához szükséges minimális számítási méret. Ha például az elsődleges adatbázis P6 (1000 DTU), és a napló I/o százaléka 50%, a másodlagos kell legalább P4 (500 DTU). A napló i/ni-adatait a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vagy a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) adatbázisnézetek használatával is bekérheti.  A szabályozás a [sys.dm_exec_requests és sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) adatbázisnézetekben HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO várakozási állapotként van jelentve. 

Az SQL Database számítási méretéről a Mik azok az [SQL Database Service-rétegek](sql-database-purchase-models.md)című témakörben talál további információt.

## <a name="cross-subscription-geo-replication"></a>Előfizetések közötti georeplikáció

A különböző előfizetésekhez tartozó két adatbázis közötti aktív georeplikáció beállításához (akár ugyanazon bérlő alatt, akár nem) kell végrehajtania az ebben a szakaszban leírt speciális eljárást.  Az eljárás SQL-parancsokon alapul, és a következőket igényli: 

- Kiemelt bejelentkezés létrehozása mindkét kiszolgálón
- Az IP-cím hozzáadása a módosítást mindkét kiszolgálón végrehajtó ügyfél engedélyezési listájához (például az SQL Server Management Studio rendszert futtató állomás IP-címe). 

A módosításokat végző ügyfélnek hálózati hozzáférésre van szüksége az elsődleges kiszolgálóhoz. Bár az ügyfél azonos IP-címét hozzá kell adni a másodlagos kiszolgáló engedélyezési listájához, a másodlagos kiszolgálóval való hálózati kapcsolat nem feltétlenül szükséges. 

### <a name="on-the-master-of-the-primary-server"></a>Az elsődleges kiszolgáló főkiszolgálóján

1. Adja hozzá az IP-címet a módosításokat végző ügyfél engedélyezési listájához (további információért lásd: [Tűzfal konfigurálása](sql-database-firewall-configure.md)). 
1. Hozzon létre egy bejelentkezési dedikált beállítás aktív georeplikáció (és állítsa be a hitelesítő adatokat, ha szükséges):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Hozzon létre egy megfelelő felhasználót, és rendelje hozzá a dbmanager szerepkörhöz: 

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. A lekérdezés segítségével jegyezze fel az új bejelentkezés ii. 

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>Az elsődleges kiszolgáló forrásadatbázisában

1. Hozzon létre egy felhasználót ugyanahhoz a bejelentkezéshez:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Adja hozzá a felhasználót a db_owner szerepkörhöz:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>A másodlagos kiszolgáló főkiszolgálóján 

1. Adja hozzá az IP-címet a módosításokat végző ügyfél engedélyezési listájához. Meg kell megegyeznie pontos IP-címét az elsődleges szerver. 
1. Hozza létre ugyanazt a bejelentkezési nevet, mint az elsődleges kiszolgálón, ugyanazzal a felhasználónévjelszóval és SID-vel: 

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Hozzon létre egy megfelelő felhasználót, és rendelje hozzá a dbmanager szerepkörhöz:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Az elsődleges kiszolgáló főkiszolgálóján

1. Jelentkezzen be az elsődleges kiszolgáló főkiszolgálójára az új bejelentkezéssel. 
1. A forrásadatbázis másodlagos kópiájának létrehozása a másodlagos kiszolgálón (szükség szerint módosítsa az adatbázis nevét és kiszolgálónevét):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

A kezdeti beállítás után a létrehozott felhasználók, bejelentkezések és tűzfalszabályok eltávolíthatók. 


## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>A hitelesítő adatok és a tűzfalszabályok szinkronizálása

Javasoljuk, [hogy adatbázis-szintű IP-tűzfalszabályokat](sql-database-firewall-configure.md) használjon a georeplikált adatbázisokhoz, hogy ezek a szabályok replikálhatók legyenek az adatbázissal annak érdekében, hogy minden másodlagos adatbázis ugyanolyan IP-tűzfalszabályokkal rendelkezzen, mint az elsődleges. Ez a megközelítés szükségtelenné teszi, hogy az ügyfelek manuálisan konfigurálják és karbantartsák a tűzfalszabályokat az elsődleges és másodlagos adatbázisokat egyaránt tartalmazó kiszolgálókon. Hasonlóképpen a [tartalmazott adatbázis-felhasználók](sql-database-manage-logins.md) adatelérés biztosítja, hogy mind az elsődleges és a másodlagos adatbázisok mindig ugyanazokkal a felhasználói hitelesítő adatokkal, így a feladatátvétel során nincs fennakadások miatt eltérések a bejelentkezések és jelszavak. Az Azure [Active Directory](../active-directory/fundamentals/active-directory-whatis.md)hozzáadásával az ügyfelek kezelhetik a felhasználói hozzáférést az elsődleges és másodlagos adatbázisokhoz, és így nincs szükség hitelesítő adatok kezelésére az adatbázisokban.

## <a name="upgrading-or-downgrading-primary-database"></a>Elsődleges adatbázis frissítése vagy visszaminősítése

Az elsődleges adatbázist frissítheti vagy visszaminősítheti egy másik számítási méretre (ugyanazon a szolgáltatási szinten belül, nem pedig az általános cél és az üzleti szempontból kritikus) anélkül, hogy leválasztaná a másodlagos adatbázisokat. A frissítés kor azt javasoljuk, hogy először frissítse a másodlagos adatbázist, majd frissítse az elsődleges. Visszaminősítéskor fordítsa meg a sorrendet: először az elsődleges, majd a másodlagos visszaminősítése. Ha frissíti vagy visszaminősíti az adatbázist egy másik szolgáltatási szintre, ez a javaslat érvényesítése.

> [!NOTE]
> Ha másodlagos adatbázist hozott létre a feladatátvételi csoport konfigurációjának részeként, nem ajánlott a másodlagos adatbázis visszaminősítése. Ez biztosítja, hogy az adatréteg elegendő kapacitással rendelkezzen a feladatátvétel aktiválása után a rendszeres munkaterhelés feldolgozásához.

> [!IMPORTANT]
> A feladatátvételi csoport elsődleges adatbázisa nem skálázható magasabb szintre, kivéve, ha a másodlagos adatbázis először a magasabb szintre van méretezve. Ha a másodlagos adatbázis méretezése előtt megpróbálja méretezni az elsődleges adatbázist, a következő hibaüzenet jelenhet meg:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>A kritikus adatok elvesztésének megakadályozása

A nagy kiterjedésű hálózatok nagy késése miatt a folyamatos másolás aszinkron replikációs mechanizmust használ. Az aszinkron replikáció bizonyos adatvesztést elkerülhetetlenné tesz, ha hiba történik. Előfordulhat azonban, hogy egyes alkalmazások nem igényelnek adatvesztést. A kritikus frissítések védelme érdekében az alkalmazásfejlesztő a tranzakció véglegesítése után azonnal felhívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) rendszereljárását. A **hívás sp_wait_for_database_copy_sync** blokkolja a hívó szálat, amíg az utolsó véglegesített tranzakció t nem továbbította a másodlagos adatbázisba. Azonban nem várja meg, hogy a továbbított tranzakciók at visszajátsszák és véglegesítsék a másodlagos oldalon. **sp_wait_for_database_copy_sync** hatóköre egy adott folyamatos másolási hivatkozás. Ezt az eljárást bármely felhasználó meghívhatja, aki kapcsolati jogosultsággal van az elsődleges adatbázishoz.

> [!NOTE]
> **sp_wait_for_database_copy_sync** megakadályozza az adatvesztést a feladatátvétel után, de nem garantálja az olvasási hozzáférés teljes szinkronizálását. A **sp_wait_for_database_copy_sync** eljáráshívás által okozott késleltetés jelentős lehet, és a hívás időpontjában a tranzakciós napló méretétől függ.

## <a name="monitoring-geo-replication-lag"></a>A georeplikációk és a georeplikációk akadozásának figyelés

Az RPO-val kapcsolatos késés figyeléséhez használja *replication_lag_sec* [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) oszlopát az elsődleges adatbázisban. Azt mutatja, késés másodpercben az elsődleges és a másodlagos véglegesített tranzakciók között másodpercben. Például Ha a lag értéke 1 másodperc, az azt jelenti, ha az elsődleges hatással van egy kimaradás ebben a pillanatban, és feladatátvétel tanusít, 1 másodperc a legutóbbi átmenetek nem lesz mentve. 

A késés méréséhez az elsődleges adatbázis ban a másodlagos, azaz a másodlagos olvasni, hasonlítsa össze *last_commit* időt a másodlagos adatbázis ban az azonos értékű az elsődleges adatbázisban.

> [!NOTE]
> Néha *replication_lag_sec* az elsődleges adatbázis null értékkel rendelkezik, ami azt jelenti, hogy az elsődleges jelenleg nem tudja, milyen messze van a másodlagos.   Ez általában a folyamat újraindítása után történik, és átmeneti állapotnak kell lennie. Fontolja meg az alkalmazás riasztását, ha a *replication_lag_sec* hosszabb ideig null értéket ad vissza. Ez azt jelzi, hogy a másodlagos adatbázis nem tud kommunikálni az elsődleges állandó kapcsolódási hiba miatt. Vannak olyan feltételek is, amelyek miatt a különbség *a másodlagos* és az elsődleges adatbázis last_commit idő nagy lesz. Például ha hosszú módosítás nélküli idő elteltével véglegesítés történik az elsődleges en, a különbség nagy értékre ugrik, mielőtt gyorsan visszatérne a 0-ra. Tekintse úgy, hogy hibafeltétel, ha a különbség a két érték között továbbra is nagy hosszú ideig.


## <a name="programmatically-managing-active-geo-replication"></a>Aktív georeplikáció programozott kezelése

Ahogy azt korábban már tárgyaltuk, az aktív georeplikáció is kezelhető programozott módon az Azure PowerShell és a REST API használatával. Az alábbi táblázatok a rendelkezésre álló parancsokat ismertetik. Az aktív georeplikáció az Azure Resource Manager API-k készletét tartalmazza a felügyelethez, beleértve az [Azure SQL Database REST API-t](https://docs.microsoft.com/rest/api/sql/) és az Azure [PowerShell-parancsmagokat.](https://docs.microsoft.com/powershell/azure/overview) Ezek az API-k erőforráscsoportok használatát igénylik, és támogatják a szerepköralapú biztonságot (RBAC). A hozzáférési szerepkörök megvalósításáról az [Azure szerepköralapú hozzáférés-vezérléscímű](../role-based-access-control/overview.md)témakörben talál további információt.

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Egy- és készletalapú adatbázisok feladatátvételének kezelése

> [!IMPORTANT]
> Ezek a Transact-SQL parancsok csak az aktív georeplikációra vonatkoznak, a feladatátvételi csoportokra nem. Mint ilyen, nem vonatkoznak a felügyelt példányokra, mivel csak feladatátvételi csoportokat támogatnak.

| Parancs | Leírás |
| --- | --- |
| [ADATBÁZIS MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Másodlagos kiszolgálón hozzáadása argumentum használata másodlagos adatbázis létrehozásához és az adatreplikáció elindításához |
| [ADATBÁZIS MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Feladatátvétel vagy FORCE_FAILOVER_ALLOW_DATA_LOSS használatával a feladatátvétel kezdeményezéséhez elsődleges ként használt másodlagos adatbázis közötti váltáshoz |
| [ADATBÁZIS MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Az ELTÁVOLÍTÁS MÁSODLAGOS A KISZOLGÁLÓN használatával megszakíthatja az SQL-adatbázis és a megadott másodlagos adatbázis közötti adatreplikációt. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Az Azure SQL Database-kiszolgálón lévő egyes adatbázisok összes meglévő replikációs kapcsolatára vonatkozó információt ad vissza. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Lekéri az utolsó replikációs időt, az utolsó replikációs késést és az adott SQL-adatbázis replikációs kapcsolatával kapcsolatos egyéb információkat. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Az összes adatbázis-művelet állapotát jeleníti meg, beleértve a replikációs kapcsolatok állapotát is. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |hatására az alkalmazás megvárja, amíg az aktív másodlagos adatbázis replikálja és nyugtázza az összes véglegesített tranzakciót. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Egy- és készletezésű adatbázisok feladatátvételének kezelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

| Parancsmag | Leírás |
| --- | --- |
| [Get-AzSqlDatabase adatbázis](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Egy vagy több adatbázist kér le. |
| [Új-AzSqlDatabaseMásodlagos](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Létrehoz egy másodlagos adatbázist egy meglévő adatbázishoz, és elkezdi az adatok replikálását. |
| [Set-AzsqlDatabaseMásodlagos](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Egy másodlagos adatbázist elsődlegessé tesz, hogy feladatátvételt kezdeményezzen. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Leállítja az adatreplikációt egy SQL-adatbázis és a megadott másodlagos adatbázis között. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Lekéri az Azure SQL-adatbázis és egy erőforráscsoport vagy SQL-kiszolgáló közötti georeplikációs hivatkozásokat. |
|  | |

> [!IMPORTANT]
> Mintaparancsfájlok esetén olvassa el A [ Egyetlen adatbázis konfigurálása és feladatátvétel aktív georeplikációval,](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) [valamint A készletezett adatbázis konfigurálása és feladatátvételaktív georeplikációval című](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)témakört.

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: Egy- és készletezésű adatbázisok feladatátvételének kezelése

| API | Leírás |
| --- | --- |
| [Adatbázis létrehozása vagy frissítése (createMode=Visszaállítás)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Elsődleges vagy másodlagos adatbázis létrehozása, frissítése vagy visszaállítása. |
| [Adatbázis állapotának létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |A létrehozási művelet állapotát adja eredményül. |
| [Másodlagos adatbázis beállítása elsődlegesként (tervezett feladatátvétel)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Beállítja, hogy melyik másodlagos adatbázis az elsődleges az aktuális elsődleges adatbázisból való átadással. **Ez a beállítás nem támogatott a felügyelt példány.**|
| [Másodlagos adatbázis beállítása elsődlegesként (nem tervezett feladatátvétel)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Beállítja, hogy melyik másodlagos adatbázis az elsődleges az aktuális elsődleges adatbázisból való átadással. Ez a művelet adatvesztést okozhat. **Ez a beállítás nem támogatott a felügyelt példány.**|
| [Replikációs hivatkozás beszereznie](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Egy adott SQL-adatbázis adott replikációs kapcsolatának lekéri a georeplikációs partnerségben. A sys.geo_replication_links katalógusnézetben látható információkat olvassa be. **Ez a beállítás nem támogatott a felügyelt példány.**|
| [Replikációs hivatkozások – Lista adatbázis szerint](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Lekéri egy adott SQL-adatbázis összes replikációs kapcsolatát egy georeplikációs partnerségben. A sys.geo_replication_links katalógusnézetben látható információkat olvassa be. |
| [Replikációs kapcsolat törlése](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Adatbázis-replikációs hivatkozás törlése. Feladatátvétel közben nem végezhető el. |
|  | |

## <a name="next-steps"></a>További lépések

- A mintaparancsfájlok a következő témakörökben láthatók:
  - [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Rugalmas készletbe helyezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- Az SQL Database támogatja az automatikus feladatátvételi csoportokat is. További információt az [automatikus feladatátvételi csoportok használata című témakörben talál.](sql-database-auto-failover-group.md)
- Az üzletmenet folytonosságának áttekintését és forgatókönyveit az [Üzletmenet folytonosságának áttekintése című témakörben találja.](sql-database-business-continuity.md)
- Az Azure SQL Database automatikus biztonsági másolatairól az [SQL Database automatikus biztonsági mentései](sql-database-automated-backups.md)című témakörben olvashat.
- Az automatikus biztonsági mentések helyreállítási használatáról az [Adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági mentések ből című témakörben](sql-database-recovery-using-backups.md)olvashat.
- Az új elsődleges kiszolgáló és adatbázis hitelesítési követelményeiről az [SQL Database biztonsága a vészhelyreállítás után című témakörben](sql-database-geo-replication-security-config.md)olvashat.

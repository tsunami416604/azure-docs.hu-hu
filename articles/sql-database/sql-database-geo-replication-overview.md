---
title: Feladatátvételi csoportok és aktív georeplikáció - Azure SQL Database |} Microsoft Docs
description: Automatikus feladatátvételt csoportok használata az aktív georeplikáció, és engedélyezze a autoomatic feladatátvételi kimaradás esetén.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 39c61fd3b2f03c99d74fded22927f319cc7f4cdd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="overview-failover-groups-and-active-geo-replication"></a>Áttekintés: Feladatátvételi csoportok és aktív georeplikáció
Aktív georeplikáció legfeljebb négy olvasható másodlagos adatbázisok konfigurálása az ugyanazon vagy másik data center helyeken (régió) teszi lehetővé. Másodlagos adatbázisok érhetők el, a lekérdezésre, és a feladatátvételi, ha van egy adatközpont-meghibásodás után, vagy nem az elsődleges adatbázishoz való kapcsolódáshoz. A feladatátvételi kell kezdeményeznie manuálisan az alkalmazás a felhasználó. A feladatátvétel után az új elsődleges van egy másik kapcsolat végpontját. 

> [!NOTE]
> Aktív georeplikáció érhető el az összes olyan adatbázis összes szolgáltatásrétegekben minden régióban.
>  

Az Azure SQL adatbázis automatikus feladatátvételt csoportok (az előzetes verzió) lehetővé teszi az SQL-adatbázis automatikusan felügyeli a georeplikáció kapcsolat, a kapcsolódási és a feladatátvételi méretekben. Azt az ügyfelek automatikusan helyreállíthatók katasztrofális regionális meghibásodásokkal vagy nem tervezett eseményeket, amelyek az SQL Database szolgáltatás rendelkezésre állásának az elsődleges régióban részleges vagy teljes elveszik után több kapcsolódó adatbázis másodlagos régióban kapnak. Emellett használhatják az olvasható másodlagos adatbázis csak olvasható munkaterhelések kiszervezéséhez.  Automatikus feladatátvételt csoportok tartalmaz, amely több adatbázist, mert azok le kell állítani az elsődleges kiszolgálón. Elsődleges és másodlagos kiszolgálók ugyanahhoz az előfizetéshez kell lennie. Automatikus feladatátvételt csoportok a csoport egyetlen másodlagos kiszolgálót egy másik régióban található összes adatbázis replikációs támogatja. Aktív georeplikáció, automatikus feladatátvételt csoportok nélkül lehetővé teszi, hogy legfeljebb négy másodlagos adatbázis bármely régióban.

Aktív georeplikációt használ, és bármely okból az elsődleges adatbázis meghiúsul, vagy egyszerűen csak offline állapotba kell, ha a másodlagos adatbázisok egyik is kezdeményezze a feladatátvételt. A másodlagos adatbázisok közül az egyik feladatátvételi aktiválásakor más másodlagos adatbázisok a rendszer automatikusan összekapcsolja az új elsődleges. Ha automatikus feladatátvételt csoportokat (az előzetes verzió) adatbázis helyreállítása és bármely kimaradás, amely hatással van egy vagy több csoport eredmények az adatbázisok kezelése az automatikus feladatátvételt használ. Konfigurálhatja az automatikus feladatátvételt házirendet, amely az alkalmazás igényeinek leginkább megfelelő, vagy nem vesznek részt, és manuális-aktiválást használjon. Emellett automatikus feladatátvételt csoportok (az előzetes verzió) adja meg az olvasási és írási, és csak olvasható figyelő végpontokat a továbbra is változatlan feladatátvételek során. Akár manuális vagy automatikus feladatátvétel aktiválási, feladatátvevő vált, az összes másodlagos adatbázisok a csoport elsődleges. Az adatbázis feladatátvétel befejezése után a DNS-rekord automatikusan frissül az új régió átirányítja a végpontok közé.

Kezelheti a replikációs és feladatátvételi egy önálló adatbázis vagy egy adatbázis-kiszolgálón vagy egy aktív georeplikációt használ a rugalmas készletben. Megteheti, hogy használatával 

- A [Azure-portálon](sql-database-geo-replication-portal.md)
- [PowerShell: Önálló adatbázis](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: A rugalmas készlet](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [PowerShell: Feladatátvételi csoport](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [Transact-SQL: Önálló adatbázis vagy a rugalmas készlet](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Önálló adatbázis](/rest/api/sql/replicationlinks/failover)
- [REST API: Feladatátvételi csoport](/rest/api/sql/failovergroups/failover). 
 
A feladatátvétel után ellenőrizze a hitelesítési követelmények, a kiszolgáló és az adatbázis az új elsődleges. További információkért lásd: [SQL-adatbázis biztonsági katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md). Ez egyaránt vonatkozik aktív georeplikáció és automatikus feladatátvételt csoportok (az előzetes verzió).

Aktív georeplikáció kihasználja a [mindig a](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technológia az SQL Server aszinkron módon replikálja a véglegesített tranzakciókat, az elsődleges adatbázis egy másodlagos adatbázis használatával, olvassa el véglegesített pillanatkép-elkülönítés (RCSI). Automatikus feladatátvételt csoportokat adja meg a csoport szemantikáját fölött aktív georeplikáció, de a azonos aszinkron replikációs eljárás használatos. A álljon, a másodlagos adatbázis előfordulhat, hogy valamivel mögött az elsődleges adatbázis, a másodlagos adatok soha nem kell a részleges tranzakciók garantáltan. Határokon területi redundanciát lehetővé teszi az alkalmazások gyors helyreállítás egy állandó adatvesztést egy teljes datacenter vagy természeti katasztrófa, katasztrofális emberi hibákat vagy rosszindulatú tevékenységéért által okozott adatközpontban részei. A helyreállítási Időkorlát adatait található a [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

Az alábbi ábra példáját mutatja be aktív georeplikáció konfigurált, az északi középső Régiójában régióban egy elsődleges és másodlagos régióban déli középső Régiójában.

![a georeplikáció kapcsolat](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Mivel a másodlagos adatbázisok olvasható, hogy csak olvasható munkaterhelésekkel, például jelentéskészítéssel feladatok kiszervezéséhez használható. Ha az aktív georeplikációt használ, lehet a másodlagos adatbázist létrehozni ugyanabban a régióban, az elsődleges, de nem növeli a végzetes hibák az alkalmazás rugalmas. Ha az automatikus feladatátvételt csoportokat (az előzetes verzió) használ, a másodlagos adatbázisnak mindig létrejön egy másik régióban.

Mellett katasztrófa utáni helyreállítás aktív georeplikáció is használható a következő esetekben:

* **Adatbázis-áttelepítési**: aktív georeplikáció használatával adatbázis áttelepítése egy kiszolgálóról egy másikra online minimális állásidővel.
* **Alkalmazásfrissítések**: alkalmazás frissítése során sikertelen hátsó másolatként egy extra másodlagos is létrehozhat.

A valódi üzleti folytonosságot, adatbázis-redundancia adatközpont között hozzáadása: csak a megoldás részét. Helyreállítás egy alkalmazás (szolgáltatás)-végpontok után végzetes hiba a szolgáltatás alkotó összetevők és a függő szolgáltatás sem szükséges. Ezen összetevők közé az ügyfélszoftver (például egy egyéni JavaScript böngésző), a előtér-webkiszolgálók, a tároló és a DNS. Nagyon fontos, hogy az összes összetevő lehetséges legyen a azonos hibák, és belül a helyreállítási idő célkitűzése (RTO) az alkalmazás elérhetővé válnak. Ezért szüksége minden függő szolgáltatás és garanciák és képességeket biztosítanak. Ezt követően annak érdekében, hogy a szolgáltatások, amelytől a feladatátvétel során a szolgáltatás funkciók megfelelő lépéseket kell végrehajtania. Vész-helyreállítási megoldások tervezésével kapcsolatos további információkért lásd: [felhőalapú megoldások kialakítása vész-helyreállítási használatával aktív georeplikáció](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Aktív georeplikáció képességek
Az aktív georeplikáció szolgáltatás a következő alapvető lehetőségeket biztosítja:
* **Automatikus aszinkron replikáció**: csak létrehozhat egy másodlagos adatbázis ad hozzá egy meglévő adatbázist. A másodlagos bármely Azure SQL adatbázis-kiszolgáló hozhatók létre. Létrehozása után a másodlagos adatbázis feltöltődik az elsődleges adatbázisból másolt adatokra. Ez a folyamat összehangolása néven ismert. Miután másodlagos adatbázis létrehozták, és a rendezés, az elsődleges adatbázissal frissítések aszinkron módon replikálva vannak a másodlagos adatbázis automatikusan. Aszinkron replikáció, az azt jelenti, hogy tranzakciók elkötelezettek az elsődleges adatbázist, mielőtt azok replikálódnak a másodlagos adatbázis. 
* **Másodlagos adatbázisok olvasható**: egy alkalmazás egy másodlagos adatbázis csak olvasható műveletekhez az elsődleges adatbázis eléréséhez használt ugyanazon vagy másik rendszerbiztonsági hozzáférhet. A másodlagos adatbázisok annak érdekében, hogy a másodlagos végrehajtott lekérdezések által nem késik a replikálás (napló ismétlés) elsősorban a frissítések a snapshot elkülönítési üzemmódban működjenek.

   > [!NOTE]
   > A napló ismétlési késik másodlagos adatbázison, ha séma frissítések érhetők el az elsődleges. Ez utóbbi szükséges a másodlagos adatbázis séma zárolását. 
   > 

* **Több olvasható másodlagos adatbázis**: két vagy több másodlagos adatbázist növelni a redundanciát és az elsődleges adatbázis és az alkalmazás adatvédelmi szintet. Ha több másodlagos adatbázist, az alkalmazás akkor is védett akkor is, ha egy másodlagos adatbázis nem sikerül. Ha csak egy másodlagos adatbázist, és nem sikerül, az alkalmazás van kitéve nagyobb eséllyel addig, amíg létrejön egy új másodlagos adatbázis.

   > [!NOTE]
   > Aktív georeplikáció segítségével összeállíthat egy globálisan elosztott alkalmazást és meg kell adni a csak olvasható hozzáférést négynél több régióban lévő adatokhoz, ha egy másodlagos (Ez a folyamat láncolás) másodlagos hozhat létre. Így az adatbázis-replikáció gyakorlatilag korlátlan méretezési érhető el. Ezenkívül láncolás csökkenti a replikációt az elsődleges adatbázisból. A kompromisszum a levél szélső másodlagos adatbázisok a megnövekedett replikációs késés. 
   >

* **Az adatbázisok rugalmas készlethez támogatási**: minden egyes replikának külön részt venni a rugalmas készletekben található, vagy nem kell minden bármely rugalmas készletben. A készlet minden egyes replikához elkülönített és nem függ semmilyen más replika konfigurációja (akár elsődleges vagy másodlagos). Minden egyes rugalmas készlet egyetlen régión belül található, ezért a azonos topológia több replika is ne ossza a rugalmas készletekben.
* **A másodlagos adatbázis konfigurálható teljesítményszintjének**: elsődleges és másodlagos adatbázisok kell lennie ugyanazon a szolgáltatási rétegben. Egy másodlagos adatbázis alacsonyabb teljesítményszintre (Dtu) az elsődleges mint hozhatja létre. Ez a beállítás nem ajánlott az alkalmazások magas adatbázis írási tevékenységet, mert a megnövekedett replikációs késés jelentős adatvesztés kockázatát növeli a feladatátvétel után. Emellett a feladatátvételt követően az alkalmazás van a teljesítményre csak az új elsődleges magasabb teljesítményszintre frissítése után. A napló IO százalékos diagram Azure-portál biztosít a másodlagos a replikációs terhelés fenntartása érdekében a szükséges minimális teljesítményszintjének becslésére egy jó módszer. Például, ha az elsődleges adatbázis P6 (1000 DTU) és a hozzá tartozó napló IO százalékban a másodlagos kell lennie legalább 50 % P4 (500 DTU). IO naplóadatokat használatával is lekérhet [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vagy [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) adatbázis-nézetek.  Az SQL-adatbázisa teljesítményszintjeit további információkért lásd: [Mik azok a SQL Database Service Tiers](sql-database-service-tiers.md). 
* **Felhasználó által szabályozott feladatátvétel és a feladat-visszavétel**: egy másodlagos adatbázis is explicit módon kell kapcsolni az elsődleges szerepkör bármikor az alkalmazás vagy a felhasználó. Valós kimaradás során a "nem tervezett" beállítást kell használni, amely azonnal elősegíti a másodlagos kell az elsődleges. A sikertelen elsődleges állítja helyre, és újból elérhető lesz, amikor a rendszer automatikusan jelöli meg a helyreállított elsődleges, másodlagos, és érdekében, hogy naprakész, és az új elsődleges. Replikációs aszinkron jellege miatt kisebb mennyiségű adatot elvesznek, nem tervezett feladatátvételek során Ha egy elsődleges meghibásodik, a legutóbbi módosítások a másodlagos replikálás előtt. Ha több másodlagos adatbázist az elsődleges átadja, a a rendszer automatikusan újrakonfigurálja a replikációs kapcsolat, és hivatkozásokat tartalmaz a fennmaradó másodlagos adatbázis az újonnan előléptetett elsődleges felhasználói beavatkozás nélkül. A leállás, ami miatt a feladatátvétel elhárítására, miután lehet az alkalmazás az elsődleges régióban való visszatéréshez. Ehhez a "tervezett" beállítást kell hívható meg a feladatátvételi parancsot. 
* **Hitelesítő adatok és a tűzfalszabályok szinkronban tartása**: azt javasoljuk, [adatbázis-tűzfalszabályok](sql-database-firewall-configure.md) georeplikált adatbázisokhoz, ezek a szabályok lehessen replikálni az adatbázis összes másodlagos adatbázis biztosítja a azonos tűzfalszabályok elsődlegesként. Ez a megközelítés nem kell az ügyfelek manuális konfigurálása és karbantartása tűzfalszabályok mind az elsődleges és másodlagos adatbázisok futtató kiszolgálókon. Ehhez hasonlóan használatával [tartalmazott adatbázis-felhasználók](sql-database-manage-logins.md) adatokhoz hozzáférést biztosítja, mind az elsődleges, mind a másodlagos adatbázisok mindig azonos felhasználó hitelesítő adatait, így a feladatátvétel során nincs szolgáltatások miatt felhasználónevek és jelszavak nem egyezik. Hozzáadásával [Azure Active Directory](../active-directory/active-directory-whatis.md), az ügyfelek az elsődleges és másodlagos adatbázisok felhasználói hozzáférését kezelheti és elkerülve kezelése hitelesítő adatainak adatbázisokban regisztrálását.

## <a name="auto-failover-group-capabilities"></a>Automatikus feladatátvételt csoport képességek

Automatikus feladatátvételt csoportok szolgáltatása egy hatékony elvonása, aktív georeplikáció szintű replikációs csoport és az automatikus feladatátvételre. Emellett eltávolítja a feladatátvételt követően az SQL kapcsolati karakterlánc módosítása a további figyelő végpontok közé kezeléséről szükségességét. 

* **Feladatátvételi csoport**: egy vagy több feladatátvételi csoportok hozhatók létre különböző régiókban (elsődleges és másodlagos kiszolgálók) két kiszolgáló között. Minden egyes csoport tartalmazhat egy vagy több adatbázis helyreállított egységként abban az esetben, ha minden egyes elsődleges adatbázisát. az elsődleges régióban kimaradás miatt elérhetetlenné válik.  
* **Elsődleges kiszolgáló**: egy feladatátvételi csoport elsődleges adatbázisokat üzemeltető kiszolgálón.
* **Másodlagos kiszolgáló**: A kiszolgáló, amelyen a másodlagos adatbázisok a feladatátvételi csoport. A másodlagos kiszolgáló ugyanabban a régióban, mint az elsődleges kiszolgáló nem lehet.
* **Adatbázisok hozzáadása feladatátvételi csoport**: a kiszolgáló vagy egy rugalmas készlet belül több adatbázisok üzembe a azonos feladatátvételi csoport. Ha egy önálló adatbázis felvétele a csoportba, automatikusan létrehoz egy másodlagos adatbázist az azonos kiadást és teljesítményszint szükséges. Ha az elsődleges adatbázis a rugalmas készletekben található, a másodlagos automatikusan létrejön a rugalmas készlet ugyanazzal a névvel. Ha egy adatbázist, amely már rendelkezik a másodlagos kiszolgáló egy másodlagos adatbázis felvételéhez, hogy a georedundáns replikáció örökli a csoport.

   > [!NOTE]
   > Egy adatbázis, amely már rendelkezik egy másodlagos adatbázis nem a feladatátvételi csoport részét képező kiszolgálók hozzáadásakor a másodlagos kiszolgáló egy új másodlagos jön létre. 
   >

* **Feladatátvételi csoport olvasási és írási figyelő**: A DNS CNAME rekord jöttek létre,  **&lt;feladatátvételi csoportnév&gt;. database.windows.net** a jelenlegi elsődleges kiszolgáló URL-címre mutat. Az számára teszi lehetővé az olvasási és írási SQL transzparens módon csatlakoznak az elsődleges adatbázissal, ha az elsődleges vált, a feladatátvételt követően. 
* **Feladatátvételi csoport olvasási figyelő**: A DNS CNAME rekord jöttek létre,  **&lt;feladatátvételi csoportnév&gt;. secondary.database.windows.net** a másodlagos kiszolgáló URL-címre mutat. A csak olvasható SQL alkalmazások transzparens módon csatlakozzanak a másodlagos adatbázist, a megadott terheléselosztási szabályok használatával lehetővé teszi. Opcionálisan megadhatja, ha azt szeretné, a csak olvasható forgalmat automatikusan átirányítja az elsődleges kiszolgáló esetén a másodlagos kiszolgáló nem érhető el.
* **Automatikus feladatátvétel házirend**: alapértelmezés szerint a feladatátvételi csoport van konfigurálva, az Automatikus feladatátvétel házirendjével. A rendszer elindítja a feladatátvétel, amint a hiba lép fel. Ha szeretné felügyelni a feladatátvételi munkafolyamat az alkalmazásból, kikapcsolhatja automatikus feladatátvételre. 
* **Kézi feladatátvételre**: feladatátvételi manuálisan is kezdeményezhető, tetszőleges időpontban, függetlenül az Automatikus feladatátvétel konfigurálása. Ha automatikus feladatátvétel házirend nincs beállítva, kézi feladatátvételre a feladatátvételi csoport adatbázisok helyreállításához szükséges. A kényszerített vagy rövid feladatátvételt (a teljes adatszinkronizálás) is kezdeményezhető. Az utóbbi használható helyezze át az aktív kiszolgáló az elsődleges régióban. Amikor befejeződött a feladatátvétel, a DNS-rekordok automatikusan frissülnek annak biztosítása érdekében a megfelelő kiszolgálóhoz való kapcsolódás.
* **Türelmi időszak adatvesztéssel**: az elsődleges és másodlagos adatbázisok aszinkron replikáció-kel vannak szinkronizálva, mert a feladatátvétel adatvesztést eredményezhet. Testre szabhatja a automatikus feladatátvétel házirendet tükrözzék az alkalmazás tolerancia adatok veszhetnek el. Konfigurálásával **GracePeriodWithDataLossHours**, szabályozhatja, hogy a rendszer mennyi ideig vár, valószínűleg eredmény adatvesztést a feladatátvétel kezdeményezése előtt. 

   > [!NOTE]
   > Amikor rendszer azt észleli, hogy továbbra is online-e a csoportban lévő adatbázisok (például a szolgáltatáskimaradás csak hatással a szolgáltatás-ellenőrzési vezérlősík), a feladatátvételt a teljes adatszinkronizálás (rövid feladatátvétel) által beállított értékétől függetlenül azonnal aktiválja **GracePeriodWithDataLossHours**. Ez a viselkedés garantálja, hogy nincs-e adatvesztés nélküli a helyreállítás során. A türelmi időszak lép életbe, csak akkor, ha a rövid feladatátvétel során nem lehetséges. Ha a szolgáltatáskimaradás elhárítására a türelmi időszak lejárta előtt, a feladatátvétel nincs aktiválva.
   >

* **Több feladatátvételi csoport**: feladatátvételek méretezésének szabályozásához az ugyanarra a két kiszolgáló több feladatátvételi csoport konfigurálható. Minden csoport külön átvétele. A több-bérlős-alkalmazás rugalmas készletek használja, ha ez a funkció segítségével összekeveri az elsődleges és másodlagos adatbázisok minden készletben. Ily módon csökkentheti a nem tervezett kimaradás hatását fele a bérlők számára.

## <a name="best-practices-of-building-highly-available-service"></a>Ajánlott eljárások a magas rendelkezésre állású szolgáltatás létrehozása

Egy magas rendelkezésre állású szolgáltatás által használt Azure SQL-adatbázis létrehozásához kövesse ezeket az irányelveket:

- **Használjon feladatátvételi csoport**: egy vagy több feladatátvételi csoportok hozhatók létre különböző régiókban (elsődleges és másodlagos kiszolgálók) két kiszolgáló között. Minden egyes csoport tartalmazhat egy vagy több adatbázis helyreállított egységként abban az esetben, ha minden egyes elsődleges adatbázisát. az elsődleges régióban kimaradás miatt elérhetetlenné válik. A feladatátvételi csoport földrajzi-másodlagos adatbázis elsődleges a azonos szolgáltatási cél hoz létre. Ha egy már meglévő georeplikáció kapcsolat ad hozzá a feladatátvételi csoport, győződjön meg arról a földrajzi másodlagos van konfigurálva a azonos szolgáltatásiszint-célkitűzés elsődlegesként.
- **Olvasási és írási figyelő használja az OLTP-munkaterhelés**: OLTP műveletek végrehajtásakor használni  **&lt;feladatátvételi csoportnév&gt;. database.windows.net** kiszolgálóként URL-cím és a kapcsolatok vannak az elsődleges automatikusan irányítva. Az URL-cím nem módosítja a feladatátvétel után. Vegye figyelembe, hogy a feladatátvétel magában foglalja a DNS-rekordja, így az ügyfélkapcsolatok a rendszer átirányítja az új elsődleges csak, miután az ügyfél DNS-gyorsítótár nem frissítik frissítése.
- **Használja a csak olvasható figyelő csak olvasható munkaterhelés**: Ha egy logikailag elkülönített írásvédett munkaterhelés, amely tűri az egyes elavulási adatok, a másodlagos adatbázis használhatja az alkalmazásban. A csak olvasható munkamenetek, használjon  **&lt;feladatátvételi csoportnév&gt;. secondary.database.windows.net** kiszolgálóként URL-cím és a kapcsolat automatikusan irányítja a rendszer a másodlagos. Javasolt továbbá jelző olvassa el a cél a kapcsolat-karakterláncban **ApplicationIntent = ReadOnly**. 
- **Elő kell készíteni a teljesítmény romlását**: SQL feladatátvételi döntési nem függ össze a többi az alkalmazás vagy többi használt szolgáltatások. Az alkalmazás "keverhetők" néhány egy régió tartozik, és néhány más összetevőkkel. A teljesítménycsökkenés elkerülése érdekében ellenőrizze a redundáns alkalmazástelepítés, a vész-Helyreállítási régióban, és ez a cikk útmutatását. Megjegyzés: az alkalmazás a vész-Helyreállítási régióban nem rendelkezik a különböző kapcsolati karakterláncok használata.  
- **Készítse elő a adatvesztés**: kimaradás észlelhető, ha az SQL automatikusan elindítja a írható-olvasható feladatátvételi nulla adatveszteséget a Tudásbázis legjobb esetén. Ellenkező esetben megvárja által megadott időtartam **GracePeriodWithDataLossHours**. Ha a megadott **GracePeriodWithDataLossHours**, elő kell készíteni a adatvesztés. Általában során kimaradások, Azure rendelkezésre állási előtérbe. Ha Ön az adatveszteség, feltétlenül állítson be **GracePeriodWithDataLossHours** egy megfelelően nagy számára, például 24 óra. 

> [!IMPORTANT]
> 800 vagy kisebb dtu-inak száma és több mint 250 adatbázisait a georeplikáció a rugalmas készletek többek között a tervezett feladatátvételek már problémák merülhetnek fel, és teljesítménycsökkenést.  A problémát valószínűleg több írási igényes munkaterhelések esetén fordulhat elő, ha a georeplikáció végpontok széles körben külön földrajzi hely, vagy több másodlagos végpont az egyes adatbázisok használatakor.  Ezeknek a problémáknak a jelenség szerepelnek, ha a georeplikáció lag növekszik adott idő alatt.  Ennek segítségével követhetők nyomon [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Ha ezeket a problémákat, majd azok mérséklési például készlet dtu-k számának növelése vagy georeplikált adatbázisokat a készlethez számának csökkentése.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Frissítés vagy alacsonyabb verziójúra változtatása az elsődleges adatbázis
Frissíthet vagy visszaminősítését (belül ugyanazt a szolgáltatási réteg) különböző teljesítményszintet az elsődleges adatbázis bármely másodlagos adatbázisok leválasztása nélkül. Ha a verziófrissítést, azt javasoljuk, hogy először frissítse a másodlagos adatbázist, és frissítse az elsődleges. Amikor alacsonyabb verziójúra változtatása, fordított sorrendben: visszaminősítését először az elsődleges, és a másodlagos majd használni. Ha frissíteni, vagy az adatbázis egy másik szolgáltatásszinthez megállapításában, ez a javaslat kényszerítése. 

> [!NOTE]
> Ha a feladatátvételi csoport konfigurációja nem javasoljuk, hogy a másodlagos adatbázis visszaminősítését részeként másodlagos adatbázis hozott létre. Ez a, ellenőrizze az adatrétegbeli van elegendő kapacitással a normál munkaterhelését feldolgozni feladatátvételi aktiválása után. 
>

## <a name="preventing-the-loss-of-critical-data"></a>A fontos adatok elvesztését megakadályozása
A nagy késleltetésű nagy kiterjedésű hálózatok, mert a folyamatos másolás egy aszinkron replikációs mechanizmust alkalmaz. Aszinkron replikáció révén adatvesztést elkerülhetetlen, ha hiba történik. Egyes alkalmazások azonban adatvesztés nélküli lehet szükség. A kritikus frissítések védelme érdekében az alkalmazásfejlesztő meghívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) rendszer eljárás közvetlenül a tranzakció véglegesítése után. Hívása **sp_wait_for_database_copy_sync** letiltja a hívó szál, amíg az utolsó véglegesített tranzakció továbbítva lett a másodlagos adatbázis. Azonban ez nem vár a továbbított tranzakciók visszajátszani és véglegesítve lett a másodlagos. **az sp_wait_for_database_copy_sync** adott folyamatos másolás hivatkozás hatókörébe tartozik. Az elsődleges adatbázis a kapcsolati jogosultsággal rendelkező felhasználók hívhatják ezt az eljárást.

> [!NOTE]
> **az sp_wait_for_database_copy_sync** adatvesztés megakadályozza a feladatátvételt követően, de nem garantálja a teljes szinkronizálás az olvasási hozzáférést. A késleltetés által okozott egy **sp_wait_for_database_copy_sync** eljáráshívás jelentős lehet, és a hívás idején a tranzakciós napló méretétől függ. 
> 

## <a name="programmatically-managing-failover-groups-and-active-geo-replication"></a>Programozott módon a feladatátvételi csoportok és aktív georeplikáció kezelése
Korábban bemutatott automatikus feladatátvételt csoportok (az előzetes verzió) és az aktív georeplikáció is kezelhetők programozott módon, Azure PowerShell és a REST API-t. Az alábbi táblázatban láthatók az elérhető parancsok készlete.

**Az Azure Resource Manager API és a szerepkör alapú biztonsági**: aktív georeplikáció tartalmaz egy Azure Resource Manager API-kezelésre, beleértve a [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) és [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview). Ezen API-k erőforráscsoportok használatát, és támogatja a szerepköralapú biztonsággal (RBAC). Hozzáférés szerepkörök megvalósításához további információkért lásd: [átruházásához hozzáférés-vezérlés](../role-based-access-control/overview.md).

## <a name="manage-sql-database-failover-using-transact-sql"></a>SQL-adatbázis feladatátvétel Transact-SQL használatával kezelése

| Parancs | Leírás |
| --- | --- |
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |MÁSODLAGOS ON SERVER hozzáadása argumentum segítségével egy meglévő adatbázist, és elindul adatreplikáció másodlagos adatbázis létrehozása |
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |FELADATÁTVÉTEL vagy FORCE_FAILOVER_ALLOW_DATA_LOSS használatával válthat egy másodlagos az adatbázis elsődleges feladatátvétel kezdeményezése |
| [Az ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Használja a másodlagos ON SERVER eltávolítása egy SQL-adatbázis és a megadott másodlagos adatbázis közötti adatreplikáció leáll. |
| [sys.geo_replication_links (az Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Az összes meglévő replikációs hivatkozások az egyes adatbázisok információt ad vissza az Azure SQL Database logikai kiszolgálón. |
| [sys.dm_geo_replication_link_status (az Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |A replikáció legutóbbi utolsó replikációs késés és egyéb információkat a replikációs hivatkozás lekérdezi a megadott SQL-adatbázis. |
| [sys.dm_operation_status (az Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Minden adatbázis-műveleteknél, többek között a replikációs hivatkozások állapotának állapotát jeleníti meg. |
| [sp_wait_for_database_copy_sync (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |hatására a alkalmazás Várjon, amíg a végrehajtott tranzakciók replikációja és az aktív másodlagos adatbázis nyugtázni. |
|  | |

## <a name="manage-sql-database-failover-using-powershell"></a>SQL-adatbázis feladatátvétel PowerShell használatával kezelése

| Parancsmag | Leírás |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Egy vagy több adatbázist kér le. |
| [New-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary) |Létrehoz egy másodlagos adatbázist egy meglévő adatbázishoz, és elkezdi az adatok replikálását. |
| [Set-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary) |Egy másodlagos adatbázist elsődlegessé tesz, hogy feladatátvételt kezdeményezzen. |
| [Remove-AzureRmSqlDatabaseSecondary](/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary) |Leállítja az adatreplikációt egy SQL-adatbázis és a megadott másodlagos adatbázis között. |
| [Get-AzureRmSqlDatabaseReplicationLink](/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink) |Lekéri az Azure SQL Database és egy erőforráscsoport vagy SQL-kiszolgáló közötti georeplikációs hivatkozásokat. |
| [New-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   Ez a parancs létrehoz egy feladatátvételi csoport, és regisztrálja azt az elsődleges és másodlagos kiszolgálók|
| [Remove-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup) | A feladatátvételi csoport eltávolítása a kiszolgálóról, és törli az összes másodlagos adatbázisok a csoport tartalmazza |
| [Get-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup) | Lekéri a feladatátvételi csoport konfigurálása |
| [Set-AzureRmSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup) |   A feladatátvételi csoport konfigurációját módosítja |
| [Switch-AzureRMSqlDatabaseFailoverGroup](/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup) | Eseményindítók a másodlagos kiszolgáló a feladatátvételi csoport feladatainak átvétele |
|  | |

> [!IMPORTANT]
> Mintaparancsfájlok, lásd: [konfigurálása és a feladatátvétel egy önálló adatbázis aktív georeplikációt használ](scripts/sql-database-setup-geodr-and-failover-database-powershell.md), [konfigurálása és a feladatátvétel egy készletezett adatbázis aktív georeplikációt használ](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md), és [ Konfigurálja és feladatátvételi egy feladatátvételi csoport számára egy adatbázist (előzetes verzió)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

## <a name="manage-sql-database-failover-using-the-rest-api"></a>SQL-adatbázis feladatátvétel REST API használatával kezelése
| API | Leírás |
| --- | --- |
| [Hozzon létre vagy az adatbázis frissítése (createMode = visszaállítása)](/rest/api/sql/Databases/CreateOrUpdate) |Hoz, frissítéseket, vagy egy elsődleges vagy másodlagos adatbázis visszaállítása. |
| [Get létrehozása, vagy az adatbázis állapotának frissítése](/rest/api/sql/Databases/CreateOrUpdate) |A létrehozási művelet során állapotának visszaadása. |
| [Állítsa be a másodlagos adatbázist az elsődleges (tervezett feladatátvétel)](/rest/api/sql/replicationlinks/failover) |Melyik adatbázis-replikát elsődleges által átveszi az aktuális elsődleges replika-adatbázis beállítása. |
| [Állítsa be a másodlagos adatbázist az elsődleges (a nem tervezett feladatátvétel)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Melyik adatbázis-replikát elsődleges által átveszi az aktuális elsődleges replika-adatbázis beállítása. Ez a művelet adatvesztést eredményezhet. |
| [Replikációs hivatkozás beszerzése](/rest/api/sql/replicationlinks/get) |Lekéri egy adott replikációs hivatkozás egy georeplikáció partneri kapcsolat áll fenn az adott SQL-adatbázis. Lekérdezi a sys.geo_replication_links katalógusnézetben látható információt. |
| [Replikációs hivatkozások - adatbázis által listája](/rest/api/sql/replicationlinks/listbydatabase) | Lekérdezi az összes replikációs hivatkozás a georeplikáció együttműködve adott SQL-adatbázis. Lekérdezi a sys.geo_replication_links katalógusnézetben látható információt. |
| [Replikációs hivatkozás törlése](/rest/api/sql/databases/delete) | Egy adatbázis-replikációs hivatkozást törli. Nem hajtható végre, a feladatátvétel során. |
| [Hozzon létre vagy feladatátvételi csoport frissítése](/rest/api/sql/failovergroups/createorupdate) | Létrehozza vagy frissíti egy feladatátvételi csoport |
| [Feladatátvételi csoport törlése](/rest/api/sql/failovergroups/delete) | A feladatátvételi csoport eltávolítása a kiszolgálóról |
| [A feladatátvétel (tervezett)](/rest/api/sql/failovergroups/failover) | Átadja a feladatokat az aktuális elsődleges kiszolgálóról a kiszolgálón. |
| [Kényszerített feladatátvételi adatvesztés engedélyezése](/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails keresztül az aktuális elsődleges kiszolgálóról a kiszolgálóhoz. Ez a művelet adatvesztést eredményezhet. |
| [Get feladatátvételi csoport](/rest/api/sql/failovergroups/get) | Egy feladatátvételi csoport lekérése. |
| [Listázza a csoportokat feladatátvételi kiszolgáló](/rest/api/sql/failovergroups/listbyserver) | A kiszolgálón a feladatátvételi csoportok listája. |
| [Frissítés feladatátvételi csoport](/rest/api/sql/failovergroups/update) | Egy feladatátvételi csoport frissíti. |
|  | |

## <a name="next-steps"></a>További lépések
* Mintaparancsfájlok lásd:
   - [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
   - [Készletezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
   - [Önálló adatbázis feladatátvételi csoportjának konfigurálása és feladatainak átvétele (előzetes verzió)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md)
* További tudnivalók az Azure SQL adatbázis automatikus biztonsági mentés című [SQL-adatbázis biztonsági mentések automatikus](sql-database-automated-backups.md).
* A helyreállítás automatikus biztonsági mentés használatával kapcsolatos további tudnivalókért lásd: [adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatból](sql-database-recovery-using-backups.md).
* Egy új elsődleges kiszolgáló és az adatbázis hitelesítési követelményeivel kapcsolatos további tudnivalókért lásd: [SQL-adatbázis biztonsági katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md).


---
title: Feladatátvételi csoportok – Azure SQL Database | Microsoft Docs
description: Az automatikus feladatátvételi csoportok egy SQL Database funkció, amely lehetővé teszi egy SQL Database-kiszolgálón vagy a felügyelt példányon lévő összes adatbázisban lévő adatbázis-csoport replikálásának és automatikus/összehangolt feladatátvételének kezelését.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 07/18/2019
ms.openlocfilehash: 05e16a67e6b01ce3bd1f03f0649baa1358414ea7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035069"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Automatikus feladatátvételi csoportok használata több adatbázis átlátható és koordinált feladatátvételének engedélyezéséhez

Az automatikus feladatátvételi csoportok egy SQL Database funkció, amely lehetővé teszi a SQL Database-kiszolgálón vagy a felügyelt példányban lévő összes adatbázisban lévő adatbázisok replikálásának és feladatátvételének kezelését egy másik régióba. A meglévő [aktív geo-replikálási](sql-database-active-geo-replication.md) szolgáltatás egyik deklaratív absztrakciója, amely a földrajzilag replikált adatbázisok nagy léptékű üzembe helyezésének és kezelésének egyszerűsítésére szolgál. A feladatátvételt manuálisan is kezdeményezheti, vagy delegálhatja a SQL Database szolgáltatásnak egy felhasználó által definiált házirend alapján. Az utóbbi lehetőség lehetővé teszi, hogy egy végzetes hiba vagy más nem tervezett esemény után automatikusan helyreállítson több kapcsolódó adatbázist egy másodlagos régióban, ami az SQL Database szolgáltatás rendelkezésre állásának teljes vagy részleges elvesztését eredményezi az elsődleges régióban. A feladatátvételi csoportok tartalmazhatnak egy vagy több adatbázist, jellemzően ugyanazt az alkalmazást használják. Emellett az olvasható másodlagos adatbázisokat is használhatja az írásvédett lekérdezési feladatok kiszervezéséhez. Mivel az automatikus feladatátvételi csoportok több adatbázist is tartalmaznak, ezeket az adatbázisokat az elsődleges kiszolgálón kell konfigurálni. A feladatátvételi csoportban lévő adatbázisokhoz tartozó elsődleges és másodlagos kiszolgálóknak ugyanahhoz az előfizetéshez kell tartoznia. Az automatikus feladatátvételi csoportok támogatják a csoportban lévő összes adatbázis replikációját egy másik régióban lévő egyetlen másodlagos kiszolgálóra.

> [!NOTE]
> Ha SQL Database-kiszolgálón található önálló vagy készletezett adatbázisokkal dolgozik, és több formátumú másodlagos zónák szeretne ugyanabban vagy különböző régiókban, használja az [aktív földrajzi replikálást](sql-database-active-geo-replication.md). 

Ha automatikus feladatátvételi házirenddel rendelkező automatikus feladatátvételi csoportokat használ, a csoport egy vagy több adatbázisára hatással lévő kimaradások automatikusan feladatátvételt eredményeznek. Az automatikus feladatátvételi csoportok emellett olyan írási és olvasási figyelőket is biztosítanak, amelyek változatlanok maradnak a feladatátvételek során. Akár kézi, akár automatikus feladatátvételi aktiválást használ, a feladatátvétel a csoportban lévő összes másodlagos adatbázist elsődlegesre váltja. Az adatbázis-feladatátvétel befejeződése után a rendszer automatikusan frissíti a DNS-rekordot, hogy átirányítsa a végpontokat az új régióba. Az adott RPO-és RTO-információk esetében lásd: [az üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).

Ha automatikus feladatátvételi házirenddel rendelkező automatikus feladatátvételi csoportokat használ, az SQL Database-kiszolgáló vagy a felügyelt példány adatbázisait érintő kimaradások automatikusan feladatátvételt eredményeznek. Az automatikus feladatátvételi csoportot az alábbiak szerint kezelheti:

- Az [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [PowerShell Feladatátvételi csoport](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: Feladatátvételi](https://docs.microsoft.com/rest/api/sql/failovergroups)csoport.

A feladatátvételt követően gondoskodjon arról, hogy a kiszolgáló és az adatbázis hitelesítési követelményei az új elsődlegesen legyenek konfigurálva. Részletekért lásd: [SQL Database biztonság a katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md).

A valós Üzletmenet-folytonosság eléréséhez az adatközpontok közötti adatbázis-redundancia csak a megoldás része. Egy alkalmazás (szolgáltatás) teljes körű helyreállítása végzetes hiba után a szolgáltatást és a függő szolgáltatásokat alkotó összes összetevő helyreállítását igényli. Ezen összetevők közé tartoznak például az ügyfélszoftverek (például egy egyéni JavaScripttel rendelkező böngésző), a webes kezelőfelületek, a tárterület és a DNS. Kritikus fontosságú, hogy minden összetevő azonos hibákra legyen rugalmas, és az alkalmazás helyreállítási idő célkitűzésén (RTO) belül elérhetővé váljon. Ezért az összes függő szolgáltatást azonosítania kell, és ismernie kell az általuk nyújtott garanciákat és képességeket. Ezt követően meg kell tennie a megfelelő lépéseket annak biztosításához, hogy a szolgáltatás a szolgáltatások feladatátvétele alatt működjön, amelytől függ. A megoldásoknak a vész-helyreállításhoz való kialakításával kapcsolatos további információkért lásd: [felhőalapú megoldások tervezése a vész-helyreállításhoz az aktív földrajzi replikálás használatával](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Automatikus feladatátvételi csoport terminológiája és képességei

- **Feladatátvételi csoport (köd)**

  A feladatátvételi csoport egyetlen SQL Database kiszolgáló vagy egyetlen felügyelt példány által felügyelt adatbázisok névvel ellátott csoportja, amely a feladatátvételt egységként egy másik régióba helyezi, ha az összes vagy néhány elsődleges adatbázis elérhetetlenné válik az elsődleges régió meghibásodása miatt. Felügyelt példányok esetén a feladatátvételi csoport a példány összes felhasználói adatbázisát tartalmazza, ezért csak egy feladatátvételi csoport konfigurálható egy példányon.
  
  > [!IMPORTANT]
  > A feladatátvételi csoport nevének globálisan egyedinek kell lennie `.database.windows.net` a tartományon belül.

- **Kiszolgálók SQL Database**

     A SQL Database-kiszolgálókkal egy adott SQL Database-kiszolgálón lévő felhasználói adatbázisok némelyike vagy mindegyike feladatátvételi csoportba helyezhető. Emellett a SQL Database-kiszolgálók több feladatátvételi csoportot is támogatnak egyetlen SQL Database-kiszolgálón.

- **Elsődleges**

  A feladatátvételi csoportban lévő elsődleges adatbázisokat üzemeltető SQL Database-kiszolgáló vagy felügyelt példány.

- **Másodlagos**

  A feladatátvételi csoportban található másodlagos adatbázisokat üzemeltető SQL Database-kiszolgáló vagy felügyelt példány. A másodlagos nem lehet ugyanabban a régióban, mint az elsődleges.

- **Önálló adatbázisok hozzáadása a feladatátvételi csoporthoz**

  Ugyanazon a SQL Database kiszolgálón több önálló adatbázis is elhelyezhető ugyanabba a feladatátvételi csoportba. Ha egyetlen adatbázist ad hozzá a feladatátvételi csoporthoz, a automatikusan létrehoz egy másodlagos adatbázist ugyanazzal a kiadással és számítási mérettel a másodlagos kiszolgálón.  A kiszolgálót a feladatátvételi csoport létrehozásakor adta meg. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal a másodlagos kiszolgálón, a csoport örökli a földrajzi replikálási hivatkozást. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, akkor a rendszer egy új másodlagost hoz létre a másodlagos kiszolgálón.
  
  > [!IMPORTANT]
  > Felügyelt példányokban az összes felhasználói adatbázis replikálódik. A feladatátvételi csoportban nem választhat felhasználói adatbázisok egy részhalmazát.

- **Adatbázisok hozzáadása rugalmas készletből a feladatátvételi csoportba**

  Egy rugalmas készletben lévő összes vagy több adatbázis ugyanabba a feladatátvételi csoportba helyezhető. Ha az elsődleges adatbázis egy rugalmas készletben van, a rendszer automatikusan létrehozza a másodlagost a rugalmas készletben ugyanazzal a névvel (másodlagos készlettel). Győződjön meg arról, hogy a másodlagos kiszolgáló tartalmaz egy rugalmas készletet ugyanazzal a pontos névvel és elegendő szabad kapacitással a feladatátvételi csoport által létrehozandó másodlagos adatbázisok üzemeltetéséhez. Ha olyan adatbázist ad hozzá a készlethez, amely már rendelkezik másodlagos adatbázissal a másodlagos készletben, akkor a csoport örökli a földrajzi replikálási hivatkozást. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, a rendszer egy új másodlagost hoz létre a másodlagos készletben.
  
- **DNS-zóna**

  Egy új példány létrehozásakor automatikusan generált egyedi azonosító. A példányhoz több tartományra kiterjedő (SAN) tanúsítvány van kiépítve, amely az ügyfélkapcsolatokat az azonos DNS-zónában lévő bármelyik példánnyal hitelesíti. Az ugyanabban a feladatátvételi csoportban lévő két felügyelt példánynak meg kell osztania a DNS-zónát. 
  
  > [!NOTE]
  > SQL Database kiszolgálókhoz létrehozott feladatátvételi csoportok esetében nincs szükség DNS-zóna AZONOSÍTÓra.

- **Feladatátvételi csoport írási-olvasási figyelője**

  DNS-CNAME rekord, amely az aktuális elsődleges URL-címre mutat. A rendszer automatikusan létrehozza a feladatátvételi csoport létrehozásakor, és lehetővé teszi az írható és olvasható SQL-feladatok transzparens újracsatlakozását az elsődleges adatbázishoz, amikor az elsődleges módosítások a feladatátvételt követően változnak. Ha a feladatátvételi csoportot egy SQL Database-kiszolgálón hozza létre, a figyelő URL-címéhez tartozó DNS CNAME-rekord `<fog-name>.database.windows.net`a következőként jön létre:. Ha a feladatátvételi csoportot felügyelt példányon hozták létre, a figyelő URL-címéhez tartozó DNS CNAME rekord a `<fog-name>.zone_id.database.windows.net`következő lesz:.

- **Feladatátvételi csoport írásvédett figyelője**

  Egy olyan DNS-CNAME rekord, amely a másodlagos URL-címére mutató írásvédett figyelőre mutat. A rendszer automatikusan létrehozza a feladatátvételi csoport létrehozásakor, és lehetővé teszi, hogy a csak olvasási jogosultsággal rendelkező SQL-munkaterhelés transzparens módon kapcsolódjon a másodlagoshoz a megadott terheléselosztási szabályok használatával. Ha a feladatátvételi csoportot egy SQL Database-kiszolgálón hozza létre, a figyelő URL-címéhez tartozó DNS CNAME-rekord `<fog-name>.secondary.database.windows.net`a következőként jön létre:. Ha a feladatátvételi csoportot felügyelt példányon hozták létre, a figyelő URL-címéhez tartozó DNS CNAME rekord a `<fog-name>.zone_id.secondary.database.windows.net`következő lesz:.

- **Automatikus feladatátvételi szabályzat**

  Alapértelmezés szerint a feladatátvételi csoport automatikus feladatátvételi házirenddel van konfigurálva. A SQL Database szolgáltatás elindítja a feladatátvételt a hiba észlelése után, és a türelmi időszak lejárt. A rendszernek ellenőriznie kell, hogy a kiesést nem lehet-e enyhíteni a [SQL Database szolgáltatás beépített, magas rendelkezésre állású infrastruktúrája](sql-database-high-availability.md) által a hatás skálázása miatt. Ha az alkalmazásból szeretné vezérelni a feladatátvételi munkafolyamatot, kikapcsolhatja az automatikus feladatátvételt.

- **Csak olvasási feladatátvételi szabályzat**

  Alapértelmezés szerint a csak olvasási figyelő feladatátvétele le van tiltva. Gondoskodik arról, hogy az elsődleges teljesítmény ne legyen hatással a másodlagos kapcsolat nélküli állapotra. Ez azonban azt is jelenti, hogy a csak olvasási munkamenetek nem fognak tudni csatlakozni, amíg a másodlagos helyre nem kerül. Ha nem tudja elviselni a leállást a csak olvasási munkamenetek esetében, és az elsődlegesnél a csak olvasási és írási-olvasási forgalom esetében átmenetileg használja az elsődlegest, engedélyezheti a feladatátvételt a csak olvasási figyelő számára. Ebben az esetben a csak olvasási forgalom automatikusan átirányítva lesz az elsődlegesre, ha a másodlagos nem érhető el.

- **Tervezett feladatátvétel**

   A tervezett feladatátvétel teljes szinkronizálást végez az elsődleges és a másodlagos adatbázisok között, mielőtt a másodlagos kapcsolók az elsődleges szerepkörre kerülnek. Ez nem garantálja az adatvesztést. A tervezett feladatátvételt a következő esetekben használja a rendszer:

  - Vész-helyreállítási (DR) gyakorlatok készítése az éles környezetben, ha az adatvesztés nem fogadható el
  - Adatbázisok áthelyezése másik régióba
  - Az adatbázisok visszaküldése az elsődleges régióba a leállás csökkentése után (feladat-visszavétel).

- **Nem tervezett feladatátvétel**

   A nem tervezett vagy kényszerített feladatátvétel azonnal átváltja a másodlagost az elsődleges szerepkörre anélkül, hogy szinkronizálnia kellene az elsődlegesével. A művelet adatvesztést eredményezhet. A nem tervezett feladatátvétel helyreállítási módszerként használatos a leállás során, ha az elsődleges nem érhető el. Amikor az eredeti elsődleges ismét online állapotba kerül, automatikusan újrakapcsolódik a szinkronizálás nélkül, és új másodlagos lesz.

- **Manuális feladatátvétel**

  A feladatátvételt manuálisan is kezdeményezheti az automatikus feladatátvételi konfigurációtól függetlenül. Ha az automatikus feladatátvételi házirend nincs konfigurálva, a feladatátvételi csoportban lévő adatbázisok másodlagosra történő helyreállításához manuális feladatátvétel szükséges. Kényszerített vagy felhasználóbarát feladatátvételt is kezdeményezhet (teljes adatszinkronizálással). Az utóbbit az elsődlegesnek a másodlagos régióba való áthelyezésére lehet használni. A feladatátvétel befejezésekor a rendszer automatikusan frissíti a DNS-rekordokat, hogy biztosítsa az új elsődleges kapcsolat elérését

- **Türelmi időszak adatvesztéssel**

  Mivel az elsődleges és másodlagos adatbázisok aszinkron replikálással vannak szinkronizálva, a feladatátvétel adatvesztést okozhat. Testreszabhatja az automatikus feladatátvételi szabályzatot, hogy tükrözze az alkalmazás adatvesztési tűréshatárát. A **GracePeriodWithDataLossHours**konfigurálásával szabályozhatja, hogy a rendszer mennyi ideig várjon, mielőtt elindítja a feladatátvételt, amely valószínűleg adatvesztést okoz.

- **Több feladatátvételi csoport**

  Több feladatátvételi csoportot is beállíthat ugyanahhoz a pár kiszolgálóhoz a feladatátvételi skála szabályozása érdekében. A csoportok egymástól függetlenül hajtják végre a feladatátvételt. Ha a több-bérlős alkalmazás rugalmas készleteket használ, ezt a képességet használhatja az egyes készletekben lévő elsődleges és másodlagos adatbázisok összekeveréséhez. Így csökkentheti a leállás hatását a bérlők felére.

  > [!NOTE]
  > A felügyelt példány nem támogatja több feladatátvételi csoport használatát.
  
## <a name="permissions"></a>Engedélyek
A feladatátvételi csoport engedélyei a [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)használatával kezelhetők. A [SQL Server közreműködő](../role-based-access-control/built-in-roles.md#sql-server-contributor) szerepkör rendelkezik a feladatátvételi csoportok kezeléséhez szükséges engedélyekkel. 

### <a name="create-failover-group"></a>Feladatátvételi csoport létrehozása
A feladatátvételi csoport létrehozásához írási hozzáféréssel kell rendelkeznie az elsődleges és a másodlagos kiszolgálókhoz, valamint a feladatátvételi csoport összes adatbázisához is RBAC kell. Felügyelt példány esetén az elsődleges és a másodlagos felügyelt példányhoz is írási hozzáférésre van szükség, de az egyes adatbázisokra vonatkozó engedélyek nem relevánsak, mert az egyes felügyelt példány-adatbázisok nem vehetők fel és nem távolíthatók el egy feladatátvételi csoportból a RBAC. 

### <a name="update-a-failover-group"></a>Feladatátvételi csoport frissítése
A feladatátvételi csoport frissítéséhez írási hozzáféréssel kell rendelkeznie a feladatátvételi csoporthoz, és az aktuális elsődleges kiszolgálón vagy felügyelt példányon lévő összes adatbázishoz RBAC kell.  

### <a name="failover-a-failover-group"></a>Feladatátvételi csoport feladatátvétele
A feladatátvételi csoport feladatátvételéhez írási hozzáféréssel kell rendelkeznie a feladatátvételi csoporthoz az új elsődleges kiszolgálón vagy a felügyelt példányon RBAC. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Ajánlott eljárások a feladatátvételi csoportok használatáról önálló adatbázisokkal és rugalmas készletekkel

Az automatikus feladatátvételi csoportot az elsődleges SQL Database-kiszolgálón kell konfigurálni, és azt egy másik Azure-régióban lévő másodlagos SQL Database-kiszolgálóhoz kell kapcsolni. A csoportok tartalmazhatják a kiszolgálók összes vagy néhány adatbázisát. A következő ábra egy geo-redundáns felhőalapú alkalmazás tipikus konfigurációját mutatja be, amely több adatbázist és automatikus feladatátvételi csoportot használ.

![automatikus feladatátvétel](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Az önálló adatbázis feladatátvételi csoportba való felvételével kapcsolatos részletes oktatóanyagért lásd: [egyetlen adatbázis hozzáadása feladatátvételi csoporthoz](sql-database-single-database-failover-group-tutorial.md) . 


Az üzletmenet folytonosságát szem előtt tartva az alábbi általános irányelvek szerint járjon el:

- **Több adatbázis feladatátvételének kezeléséhez használjon egy vagy több feladatátvételi csoportot**

  A különböző régiókban (elsődleges és másodlagos kiszolgálókon) lévő két kiszolgáló között egy vagy több feladatátvételi csoport is létrehozható. Az egyes csoportok tartalmazhatnak egy vagy több olyan adatbázist, amelyeket egységként állítanak be abban az esetben, ha az összes vagy néhány elsődleges adatbázis elérhetetlenné válik az elsődleges régióban bekövetkező leállás miatt. A feladatátvételi csoport a földrajzilag másodlagos adatbázist hozza létre ugyanazzal a szolgáltatási céllal, mint az elsődleges. Ha egy meglévő geo-replikációs kapcsolatot ad hozzá a feladatátvételi csoporthoz, győződjön meg arról, hogy a Geo-másodlagos kiszolgáló ugyanazzal a szolgáltatási réteggel és számítási mérettel van konfigurálva, mint az elsődleges.

- **Olvasási és írási figyelő használata a OLTP számítási feladatokhoz**

  OLTP műveletek végrehajtásakor használja `<fog-name>.database.windows.net` a kiszolgáló URL-címét, és a kapcsolatok automatikusan az elsődlegesre lesznek irányítva. Ez az URL-cím nem változik a feladatátvétel után. Megjegyzés: a feladatátvétel magában foglalja a DNS-rekord frissítését, hogy az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítésekor legyenek átirányítva az új elsődlegesre.

- **Írásvédett munkaterheléshez csak olvasási figyelő használható**

  Ha az adatok bizonyos elavulása érdekében logikailag elszigetelt írásvédett munkaterheléssel rendelkezik, használhatja az alkalmazás másodlagos adatbázisát. Csak olvasási munkamenetek esetén használja `<fog-name>.secondary.database.windows.net` a kiszolgáló URL-címét, a kapcsolat pedig automatikusan a másodlagosra lesz irányítva. Azt is javasoljuk, hogy a **ApplicationIntent = ReadOnly**használatával a rendszer a következővel jelezze a kapcsolatok karakterláncának olvasási szándékát:.

- **Készüljön fel a teljesítmény romlására**

  Az SQL-feladatátvételi döntés független a többi alkalmazástól vagy más használt szolgáltatástól. Előfordulhat, hogy az alkalmazás "vegyes", és egy adott régióban néhány összetevővel rendelkezik. A romlás elkerülése érdekében gondoskodjon a redundáns alkalmazások telepítéséről a DR régióban, és kövesse ezeket a [hálózati biztonsági irányelveket](#failover-groups-and-network-security).

  > [!NOTE]
  > A DR régióban lévő alkalmazásnak nem kell másik kapcsolódási karakterláncot használnia.  

- **Felkészülés az adatvesztésre**

  Ha a rendszer kimaradást észlel, az SQL megvárja a **GracePeriodWithDataLossHours**által megadott időszakot. Az alapértelmezett érték 1 óra. Ha nem engedheti meg az adatvesztést, ügyeljen arra, hogy a **GracePeriodWithDataLossHours** megfelelően nagy számú, például 24 óráig állítsa be. A manuális csoport feladatátvételi szolgáltatásával visszatérhet a másodlagosról az elsődlegesre.

  > [!IMPORTANT]
  > A 800-es vagy kevesebb DTU és több mint 250 adatbázissal rendelkező, Geo-replikációt használó rugalmas készletek olyan problémákba ütközhet, mint a hosszabb tervezett feladatátvétel és a csökkentett teljesítmény.  Ezek a problémák nagyobb valószínűséggel fordulnak elő az írási igényű munkaterhelések esetében, ha a Geo-replikációs végpontok földrajzilag széles körben elkülönülnek, vagy ha az egyes adatbázisokhoz több másodlagos végpont is használatos.  Ezeknek a problémáknak a tünetei akkor jelennek meg, ha a földrajzi replikálási késés idővel növekszik.  Ez a késés figyelhető a [sys. DM _geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)használatával.  Ha ezek a problémák lépnek fel, a mérséklések közé tartozik a készlet DTU számának növelése vagy a Geo-replikált adatbázisok számának csökkentése ugyanabban a készletben.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Ajánlott eljárások a feladatátvételi csoportok használatáról felügyelt példányokkal

> [!IMPORTANT]
> A felügyelt példányok automatikus feladatátvételi csoportjai nyilvános előzetes verzióban érhetők el.

Az automatikus feladatátvételi csoportot az elsődleges példányon kell konfigurálni, és egy másik Azure-régióban található másodlagos példánnyal kell csatlakoznia.  A példány összes adatbázisa replikálva lesz a másodlagos példányra. 

A következő ábra egy geo-redundáns felhőalapú alkalmazás tipikus konfigurációját szemlélteti, amely felügyelt példányt és automatikus feladatátvételi csoportot használ.

![automatikus feladatátvétel](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Tekintse meg a felügyelt [példány hozzáadása feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md) című témakört, amely részletesen ismerteti a felügyelt példányok feladatátvételi csoport használatára való hozzáadásának lépéseit. 

Ha az alkalmazás felügyelt példányt használ adatcsomagként, kövesse az alábbi általános irányelveket az üzletmenet folytonosságának tervezésekor:

- **A másodlagos példány létrehozása ugyanabban a DNS-zónában, mint az elsődleges példány**

  Annak biztosítása érdekében, hogy az elsődleges és a másodlagos példányok feladatátvétele ne szakítsa meg a kapcsolatot az elsődleges példánnyal, ugyanabban a DNS-zónában kell lennie. A szolgáltatás garantálja, hogy ugyanaz a többtartományos (SAN) tanúsítvány használható az ügyfélkapcsolatok hitelesítésére a feladatátvételi csoport két példányának egyikén. Ha az alkalmazás készen áll az éles környezetben való üzembe helyezésre, hozzon létre egy másodlagos példányt egy másik régióban, és győződjön meg róla, hogy az elsődleges példánnyal osztja meg a DNS-zónát. Ezt megteheti egy `DNS Zone Partner` opcionális paraméter megadásával a Azure Portal, a PowerShell vagy a REST API használatával. 

  A másodlagos példánynak az elsődleges példánnyal azonos DNS-zónában való létrehozásával kapcsolatos további információkért lásd: [feladatátvételi csoportok kezelése felügyelt példányokkal (előzetes verzió)](#powershell-managing-failover-groups-with-managed-instances-preview).

- **Replikációs forgalom engedélyezése két példány között**

  Mivel minden példány el van különítve a saját VNet, engedélyezni kell a két irányú adatforgalmat a virtuális hálózatok között. Lásd: [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Feladatátvételi csoport konfigurálása a teljes példány feladatátvételének kezeléséhez**

  A feladatátvételi csoport a példány összes adatbázisának feladatátvételét fogja kezelni. Egy csoport létrehozásakor a példány minden adatbázisa automatikusan a másodlagos példányra lesz replikálva. A feladatátvételi csoportok nem használhatók az adatbázisok egy részhalmazának részleges feladatátvételének kezdeményezésére.

  > [!IMPORTANT]
  > Ha egy adatbázis el lett távolítva az elsődleges példányból, akkor a rendszer automatikusan elveszi a Geo másodlagos példányon is.

- **Olvasási és írási figyelő használata a OLTP számítási feladatokhoz**

  OLTP műveletek végrehajtásakor használja `<fog-name>.zone_id.database.windows.net` a kiszolgáló URL-címét, és a kapcsolatok automatikusan az elsődlegesre lesznek irányítva. Ez az URL-cím nem változik a feladatátvétel után. A feladatátvétel magában foglalja a DNS-rekord frissítését, így az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítésekor lesznek átirányítva az új elsődlegesre. Mivel a másodlagos példány megosztja a DNS-zónát az elsődleges értékkel, az ügyfélalkalmazás újra csatlakozhat ugyanahhoz a SAN-tanúsítványhoz.

- **Közvetlen kapcsolódás a földrajzilag replikált másodlagoshoz a csak olvasási lekérdezésekhez**

  Ha az adatok bizonyos elavulása érdekében logikailag elszigetelt írásvédett munkaterheléssel rendelkezik, használhatja az alkalmazás másodlagos adatbázisát. Ha közvetlenül a földrajzilag replikált másodlagoshoz szeretne csatlakozni `server.secondary.zone_id.database.windows.net` , használja a kiszolgáló URL-címét, és a kapcsolat közvetlenül a földrajzilag replikált másodlagosra történik.

  > [!NOTE]
  > Bizonyos szolgáltatási rétegekben a Azure SQL Database támogatja a csak olvasható [replikák](sql-database-read-scale-out.md) használatát, hogy csak egy írásvédett replikát és `ApplicationIntent=ReadOnly` a paramétert használja a kapcsolódási karakterláncban. Ha egy földrajzilag replikált másodlagos beállítást konfigurált, ezzel a képességgel csatlakozhat egy írásvédett replikához az elsődleges helyen vagy a földrajzilag replikált helyen.
  > - Az elsődleges helyen található írásvédett replikához való kapcsolódáshoz használja `<fog-name>.zone_id.database.windows.net`a következőt:.
  > - A másodlagos helyen található írásvédett replikához való kapcsolódáshoz használja `<fog-name>.secondary.zone_id.database.windows.net`a következőt:.

- **Készüljön fel a teljesítmény romlására**

  Az SQL-feladatátvételi döntés független a többi alkalmazástól vagy más használt szolgáltatástól. Előfordulhat, hogy az alkalmazás "vegyes", és egy adott régióban néhány összetevővel rendelkezik. A romlás elkerülése érdekében gondoskodjon a redundáns alkalmazások telepítéséről a DR régióban, és kövesse ezeket a [hálózati biztonsági irányelveket](#failover-groups-and-network-security).

- **Felkészülés az adatvesztésre**

  Ha a rendszer áramszünetet észlel, az SQL automatikusan elindítja az írási és olvasási feladatátvételt, ha a lehető legjobb ismerete nulla adatvesztést okoz. Ellenkező esetben a által `GracePeriodWithDataLossHours`megadott időszakra vár. Ha meg van `GracePeriodWithDataLossHours`adva, fel kell készülnie az adatvesztésre. Általánosságban elmondható, hogy az Azure továbbra is rendelkezésre áll. Ha nem engedheti meg az adatvesztést, ügyeljen arra, hogy a GracePeriodWithDataLossHours megfelelően nagy számú, például 24 óráig állítsa be.

  Az írható-olvasható figyelő DNS-frissítése a feladatátvétel megkezdése után azonnal megtörténik. Ez a művelet nem eredményez adatvesztést. Az adatbázis-szerepkörök váltásának folyamata azonban normál körülmények között akár 5 percet is igénybe vehet. Amíg a művelet be nem fejeződik, az új elsődleges példány egyes adatbázisai továbbra is írásvédettek maradnak. Ha a feladatátvételt a PowerShell használatával kezdeményezik, a teljes művelet szinkronban van. Ha a Azure Portal használatával kezdeményezik, akkor a felhasználói felület a befejezési állapotot jelzi. Ha a REST API használatával kezdeményezik, a végrehajtás figyeléséhez használja a standard Azure Resource Manager lekérdezési mechanizmusát.

  > [!IMPORTANT]
  > A manuális csoport feladatátvételével visszahelyezheti az elsődleges helyre az elsődleges helyet. Ha a feladatátvételt okozó leállás le lett csökkentve, az elsődleges adatbázisokat az eredeti helyre helyezheti át. Ehhez el kell indítania a csoport manuális feladatátvételét.

## <a name="failover-groups-and-network-security"></a>Feladatátvételi csoportok és hálózati biztonság

Egyes alkalmazásokhoz a biztonsági szabályok megkövetelik, hogy az adatcsomaghoz való hálózati hozzáférés egy adott összetevőhöz vagy összetevőkhöz, például egy virtuális géphez, egy webszolgáltatáshoz, stb. van korlátozva. Ez a követelmény az üzletmenet folytonosságának kialakítására és a feladatátvételi csoportok használatára vonatkozó kihívásokat mutatja be. Az ilyen korlátozott hozzáférés megvalósításakor vegye figyelembe az alábbi beállításokat.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Feladatátvételi csoportok és virtuális hálózati szabályok használata

Ha [Virtual Network szolgáltatási végpontokat és szabályokat](sql-database-vnet-service-endpoint-rule-overview.md) használ az SQL-adatbázishoz való hozzáférés korlátozására, vegye figyelembe, hogy az egyes Virtual Network szolgáltatási végpontok csak egy Azure-régióra vonatkoznak. A végpont nem teszi lehetővé más régiók számára, hogy fogadják a kommunikációt az alhálózatból. Ezért csak az ugyanabban a régióban üzembe helyezett ügyfélalkalmazások csatlakozhatnak az elsődleges adatbázishoz. Mivel a feladatátvétel az SQL-ügyfél munkameneteinek egy másik (másodlagos) régióban lévő kiszolgálóra való átirányítását eredményezi, akkor ezek a munkamenetek sikertelenek lesznek, ha az adott régión kívüli ügyféltől származik. Emiatt az automatikus feladatátvételi házirend nem engedélyezhető, ha a résztvevő kiszolgálók szerepelnek a Virtual Network szabályokban. A manuális feladatátvétel támogatásához kövesse az alábbi lépéseket:

1. Az alkalmazás előtér-összetevőinek (webszolgáltatás, virtuális gépek stb.) redundáns másolatainak kiépítése a másodlagos régióban
2. A [virtuális hálózati szabályok](sql-database-vnet-service-endpoint-rule-overview.md) konfigurálása egyenként az elsődleges és a másodlagos kiszolgáló számára
3. Az [előtér-feladatátvétel engedélyezése Traffic Manager-konfiguráció használatával](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. A manuális feladatátvétel elindítása a leállás észlelésekor. Ez a beállítás olyan alkalmazásokra van optimalizálva, amelyek konzisztens késést igényelnek az előtér és az adatréteg között, és támogatja a helyreállítást, ha az előtér, az adatréteg vagy mindkettő hatással van a kimaradásra.

> [!NOTE]
> Ha az írásvédett figyelőt a csak olvasási feladatok terheléselosztásához használja, akkor győződjön meg arról, hogy ez a számítási feladat a másodlagos régióban lévő virtuális gépen vagy más erőforráson fut, így a másodlagos adatbázishoz csatlakozhat.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Feladatátvételi csoportok és SQL Database-tűzfalszabályok használata

Ha az üzletmenet-folytonossági terv automatikus feladatátvételi csoportokkal való feladatátvételt igényel, akkor a hagyományos tűzfalszabályok használatával korlátozhatja az SQL-adatbázishoz való hozzáférést.  Az automatikus feladatátvétel támogatásához kövesse az alábbi lépéseket:

1. [Nyilvános IP-cím létrehozása](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Hozzon létre egy nyilvános Load balancert](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) , és rendelje hozzá a nyilvános IP-címet.
3. [Hozzon létre egy virtuális hálózatot és a virtuális gépeket](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) az előtér-összetevőkhöz
4. [Hozzon létre egy hálózati biztonsági csoportot](../virtual-network/security-overview.md) , és konfigurálja a bejövő kapcsolatokat.
5. Győződjön meg arról, hogy a kimenő kapcsolatok az SQL [szolgáltatás címkével](../virtual-network/security-overview.md#service-tags)vannak megnyitva az Azure SQL Database-ben.
6. Hozzon létre egy [SQL Database-tűzfalszabály](sql-database-firewall-configure.md) , amely engedélyezi a bejövő forgalmat az 1. lépésben létrehozott nyilvános IP-címről.

További információ arról, hogyan konfigurálhatja a kimenő hozzáférést és a tűzfalszabályok által használandó IP-címet a terheléselosztó [kimenő kapcsolatainak](../load-balancer/load-balancer-outbound-connections.md)megtekintéséhez.

A fenti konfiguráció biztosítja, hogy az automatikus feladatátvétel ne blokkolja az előtér-összetevők kapcsolatait, és azt feltételezi, hogy az alkalmazás képes tolerálni az előtér és az adatréteg közötti nagyobb késést.

> [!IMPORTANT]
> A regionális kimaradások üzletmenet-folytonosságának garantálása érdekében biztosítania kell a földrajzi redundanciát mind az előtér-összetevők, mind az adatbázisok számára.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>A földrajzi replikálás engedélyezése a felügyelt példányok és a virtuális hálózatok között

Ha az elsődleges és a másodlagos felügyelt példányok közötti feladatátvételi csoportot állít be két különböző régióban, az egyes példányok különálló virtuális hálózattal vannak elkülönítve. A virtuális hálózatok közötti replikációs forgalom engedélyezéséhez ellenőrizze, hogy teljesülnek-e az előfeltételek:

1. A két felügyelt példánynak különböző Azure-régiókban kell lennie.
1. A két felügyelt példánynak ugyanazt a szolgáltatási szintet kell megadnia, és ugyanazzal a tárolási mérettel kell rendelkeznie. 
1. A másodlagos felügyelt példánynak üresnek kell lennie (nincs felhasználói adatbázis).
1. A felügyelt példányok által használt virtuális hálózatokat [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy expressz útvonalon keresztül kell csatlakoztatni. Ha két virtuális hálózat egy helyszíni hálózaton keresztül kapcsolódik, győződjön meg arról, hogy nincs tűzfalszabály blokkolja a 5022-es és a 11000-11999-es portot. A globális VNet-társítás nem támogatott.
1. A felügyelt példányok két virtuális hálózatok nem rendelkezhet átfedéses IP-címekkel.
1. Be kell állítania a hálózati biztonsági csoportokat (NSG) úgy, hogy a 5022-es és a 11000 ~ 12000-as tartomány nyitott bejövő és kimenő a többi felügyelt példányhoz tartozó alhálózat kapcsolataihoz. Ez lehetővé teszi a példányok közötti replikációs forgalmat.

   > [!IMPORTANT]
   > Helytelenül konfigurált NSG biztonsági szabályok vezetnek az adatbázis-másolási műveletek elakadása érdekében.

7. A másodlagos példány a helyes DNS-zóna azonosítójával van konfigurálva. A DNS-zóna felügyelt példányok egyik tulajdonsága, az azonosítója pedig szerepel az állomásnév címe között. A zóna azonosítója véletlenszerű karakterláncként jön létre, ha az első felügyelt példány létrejön az egyes VNet, és ugyanaz az azonosító az ugyanazon alhálózaton lévő összes többi példányhoz van rendelve. Hozzárendelés után a DNS-zóna nem módosítható. Az ugyanabban a feladatátvételi csoportban található felügyelt példányoknak meg kell osztaniuk a DNS-zónát. Ezt úgy érheti el, ha a másodlagos példány létrehozásakor a DnsZonePartner paraméter értékeként átadja az elsődleges példány zónájának AZONOSÍTÓját. 

## <a name="upgrading-or-downgrading-a-primary-database"></a>Elsődleges adatbázis frissítése vagy visszaminősítése

A másodlagos adatbázisok leválasztása nélkül frissítheti vagy visszaminősítheti az elsődleges adatbázist más számítási méretre (ugyanazon a szolgáltatási szinten belül, általános célú és üzletileg kritikus között). A frissítéskor javasoljuk, hogy először frissítse az összes másodlagos adatbázist, majd frissítse az elsődlegest. A visszalépést követően a sorrend megfordításakor a rendszer visszaminősíti az elsődlegest, majd lecseréli az összes másodlagos adatbázist. Ha az adatbázist egy másik szolgáltatási szintre frissíti vagy visszaminősíti, ez a javaslat érvénybe lép.

Ezt a sorozatot kifejezetten arra a problémára érdemes elkerülni, hogy az alacsonyabb SKU-ban lévő másodlagos példány túlterhelt legyen, és a frissítés vagy a lefokozási folyamat során újra kell telepíteni őket. A problémát úgy is elkerülheti, ha az elsődleges írásvédett, az összes írási és olvasási feladatnak az elsődlegesen való hatásának rovására kerül. 

> [!NOTE]
> Ha a másodlagos adatbázist a feladatátvételi csoport konfigurációjának részeként hozta létre, a másodlagos adatbázis visszalépéséhez nem ajánlott. Ezzel biztosíthatja, hogy az adatmennyiség elegendő kapacitással legyen feldolgozva a rendszeres számítási feladatok elvégzése után a feladatátvétel aktiválása után.

> [!IMPORTANT]
> A feladatátvételi csoport tagjaként működő felügyelt példányok frissítése vagy visszaminősítése jelenleg nem támogatott.

## <a name="preventing-the-loss-of-critical-data"></a>A kritikus fontosságú adatmennyiség elvesztésének megakadályozása

A nagyméretű hálózatok nagy késése miatt a folyamatos másolás aszinkron replikációs mechanizmust használ. Az aszinkron replikáció során az adatvesztés elkerülhető, ha hiba történik. Előfordulhat azonban, hogy egyes alkalmazások nem igényelnek adatvesztést. A kritikus frissítések elleni védelem érdekében az alkalmazás fejlesztői a tranzakció véglegesítése után azonnal meghívhatják a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) rendszereljárást. A **sp_wait_for_database_copy_sync** meghívásával a rendszer addig blokkolja a hívó szálat, amíg az utolsó véglegesített tranzakció át nem lett továbbítva a másodlagos adatbázisba. Azonban nem várja meg, amíg a továbbított tranzakciók újra le lesznek játszva és véglegesítve lettek a másodlagoson. a **sp_wait_for_database_copy_sync** hatóköre egy adott folytonos másolási hivatkozásra terjed ki. Minden olyan felhasználó, aki az elsődleges adatbázishoz kapcsolódási jogokkal rendelkezik, meghívhatja ezt az eljárást.

> [!NOTE]
> a **sp_wait_for_database_copy_sync** megakadályozza az adatvesztést a feladatátvétel után, de nem garantálja a teljes szinkronizálást az olvasási hozzáféréshez. A **sp_wait_for_database_copy_sync** eljárás hívása által okozott késleltetés jelentős lehet, és a tranzakciós napló méretétől függ a hívás időpontjában.

## <a name="failover-groups-and-point-in-time-restore"></a>Feladatátvételi csoportok és időponthoz történő visszaállítás

További információ a feladatátvételi csoportokkal való időpontra történő visszaállításról: időponthoz való [helyreállítás (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Feladatátvételi csoportok programozott kezelése

Ahogy azt korábban említettük, az automatikus feladatátvételi csoportok és az aktív geo-replikáció programozott módon is felügyelhető Azure PowerShell és a REST API használatával. A következő táblázatok ismertetik az elérhető parancsok készletét. Az aktív geo-replikálás Azure Resource Manager API-kat tartalmaz a felügyelethez, beleértve a [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) és [Azure PowerShell parancsmagokat](https://docs.microsoft.com/powershell/azure/overview). Ezek az API-k az erőforráscsoportok használatát igénylik, és támogatják a szerepköralapú biztonságot (RBAC). A hozzáférési szerepkörök megvalósításával kapcsolatos további információkért lásd: [Azure szerepköralapú Access Control](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: Az SQL Database feladatátvételének kezelése önálló adatbázisokkal és rugalmas készletekkel

| A parancsmag | Leírás |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt mind az elsődleges, mind a másodlagos kiszolgálókon.|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Eltávolítja a feladatátvételi csoportot a kiszolgálóról, és törli a csoportba tartozó összes másodlagos adatbázist. |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | A feladatátvételi csoport konfigurációjának beolvasása |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Módosítja a feladatátvételi csoport konfigurációját. |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Elindítja a feladatátvételi csoport feladatátvételét a másodlagos kiszolgálóra. |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Egy vagy több adatbázis hozzáadását Azure SQL Database feladatátvételi csoportba|
|  | |

> [!IMPORTANT]
> Egy minta parancsfájl esetében lásd: [feladatátvételi csoport konfigurálása és feladatátvétele egyetlen adatbázishoz](scripts/sql-database-add-single-db-to-failover-group-powershell.md).
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell: Feladatátvételi csoportok kezelése felügyelt példányokkal (előzetes verzió)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>A PowerShell legújabb kiadás előtti verziójának telepítése

1. Frissítse a PowerShellGet modult a 1.6.5 (vagy a legújabb előzetes verzióra). Lásd: PowerShell-előnézeti [webhely](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

   ```powershell
      install-module PowerShellGet -MinimumVersion 1.6.5 -force
   ```

2. Egy új PowerShell-ablakban hajtsa végre a következő parancsokat:

   ```powershell
      import-module PowerShellGet
      get-module PowerShellGet #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>PowerShell-parancsmagok példány-feladatátvételi csoport létrehozásához

| API | Leírás |
| --- | --- |
| New-AzureRmSqlDatabaseInstanceFailoverGroup |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt mind az elsődleges, mind a másodlagos kiszolgálókon.|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |Módosítja a feladatátvételi csoport konfigurációját.|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |A feladatátvételi csoport konfigurációjának beolvasása|
| Switch-AzureRmSqlDatabaseInstanceFailoverGroup |Elindítja a feladatátvételi csoport feladatátvételét a másodlagos kiszolgálóra.|
| Remove-AzureRmSqlDatabaseInstanceFailoverGroup | Feladatátvételi csoport eltávolítása|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: SQL Database-alapú feladatátvételi csoportok kezelése önálló és készletezett adatbázisokkal

| API | Leírás |
| --- | --- |
| [Feladatátvételi csoport létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Feladatátvételi csoport létrehozása vagy frissítése |
| [Feladatátvételi csoport törlése](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | A feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Feladatátvétel (tervezett)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Feladatátvétel az aktuális elsődleges kiszolgálóról erre a kiszolgálóra. |
| [Adatvesztés kényszerített feladatátvételének engedélyezése](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Baja az aktuális elsődleges kiszolgálóról erre a kiszolgálóra. A művelet adatvesztéshez vezethet. |
| [Feladatátvételi csoport beolvasása](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Feladatátvételi csoport beolvasása. |
| [Feladatátvételi csoportok listázása kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Felsorolja a kiszolgálók feladatátvételi csoportjait. |
| [Feladatátvételi csoport frissítése](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Frissíti a feladatátvételi csoportot. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>REST API: Feladatátvételi csoportok kezelése felügyelt példányokkal (előzetes verzió)

| API | Leírás |
| --- | --- |
| [Feladatátvételi csoport létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Feladatátvételi csoport létrehozása vagy frissítése |
| [Feladatátvételi csoport törlése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | A feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Feladatátvétel (tervezett)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Feladatátvétel az aktuális elsődleges kiszolgálóról erre a kiszolgálóra. |
| [Adatvesztés kényszerített feladatátvételének engedélyezése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |Baja az aktuális elsődleges kiszolgálóról erre a kiszolgálóra. A művelet adatvesztéshez vezethet. |
| [Feladatátvételi csoport beolvasása](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Feladatátvételi csoport beolvasása. |
| [Feladatátvételi csoportok listázása hely szerint](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Egy helyen lévő feladatátvételi csoportok felsorolása. |

## <a name="next-steps"></a>További lépések

- Részletes oktatóanyagok:
    - [Önálló adatbázis hozzáadása egy feladatátvételi csoporthoz](sql-database-single-database-failover-group-tutorial.md)
    - [Rugalmas készlet hozzáadása feladatátvételi csoporthoz](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Felügyelt példány hozzáadása feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md)
- A minta parancsfájlokat lásd:
  - [A PowerShell használatával konfigurálhatja az aktív földrajzi replikálást egyetlen adatbázishoz Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [A PowerShell használatával konfigurálhatja az aktív földrajzi replikálást egy készletezett adatbázishoz Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Azure SQL Database önálló adatbázis hozzáadása feladatátvételi csoporthoz a PowerShell használatával](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md)
- Az automatikus biztonsági mentések Azure SQL Databaseáról a [SQL Database automatizált biztonsági mentések](sql-database-automated-backups.md)című témakörben olvashat bővebben.
- Ha többet szeretne megtudni a helyreállítás automatizált biztonsági mentéséről, olvassa el [az adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatokból](sql-database-recovery-using-backups.md)című témakört.
- Az új elsődleges kiszolgáló és adatbázis hitelesítési követelményeinek megismeréséhez tekintse meg a SQL Database biztonság a vész- [helyreállítás után](sql-database-geo-replication-security-config.md)című témakört.

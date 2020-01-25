---
title: Feladatátvételi csoportok
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
ms.date: 1/05/2020
ms.openlocfilehash: 7b45ddce0435a903c63855dea8a01353a7ab36ec
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722543"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Automatikus feladatátvételi csoportok használata több adatbázis átlátható és koordinált feladatátvételének engedélyezéséhez

Az automatikus feladatátvételi csoportok egy SQL Database funkció, amely lehetővé teszi a SQL Database-kiszolgálón vagy a felügyelt példányban lévő összes adatbázisban lévő adatbázisok replikálásának és feladatátvételének kezelését egy másik régióba. A meglévő [aktív geo-replikálási](sql-database-active-geo-replication.md) szolgáltatás egyik deklaratív absztrakciója, amely a földrajzilag replikált adatbázisok nagy léptékű üzembe helyezésének és kezelésének egyszerűsítésére szolgál. A feladatátvételt manuálisan is kezdeményezheti, vagy delegálhatja a SQL Database szolgáltatásnak egy felhasználó által definiált házirend alapján. Az utóbbi lehetőség lehetővé teszi, hogy egy végzetes hiba vagy más nem tervezett esemény után automatikusan helyreállítson több kapcsolódó adatbázist egy másodlagos régióban, ami az SQL Database szolgáltatás rendelkezésre állásának teljes vagy részleges elvesztését eredményezi az elsődleges régióban. A feladatátvételi csoportok tartalmazhatnak egy vagy több adatbázist, jellemzően ugyanazt az alkalmazást használják. Emellett az olvasható másodlagos adatbázisokat is használhatja az írásvédett lekérdezési feladatok kiszervezéséhez. Mivel az automatikus feladatátvételi csoportok több adatbázist is tartalmaznak, ezeket az adatbázisokat az elsődleges kiszolgálón kell konfigurálni. Az automatikus feladatátvételi csoportok támogatják a csoportban lévő összes adatbázis replikációját egy másik régióban lévő egyetlen másodlagos kiszolgálóra.

> [!NOTE]
> Ha SQL Database-kiszolgálón található önálló vagy készletezett adatbázisokkal dolgozik, és több formátumú másodlagos zónák szeretne ugyanabban vagy különböző régiókban, használja az [aktív földrajzi replikálást](sql-database-active-geo-replication.md). 

Ha automatikus feladatátvételi házirenddel rendelkező automatikus feladatátvételi csoportokat használ, a csoport egy vagy több adatbázisára hatással lévő kimaradások automatikusan feladatátvételt eredményeznek. Ezek általában olyan incidensek, amelyeket a beépített automatikus magas rendelkezésre állású műveletek nem képesek önállóan elhárítani. A feladatátvételi eseményindítók példái közé tartozik egy olyan incidens, amely egy SQL-bérlői kör vagy egy ellenőrző gyűrű miatt leállt, mert egy operációsrendszer-kernel memóriája több számítási csomóponton van, vagy egy vagy több bérlői kör okozta incidens, mert rossz hálózati kábelt vágtak le utine hardver leszerelése.  További információ: [SQL Database magas rendelkezésre állás](sql-database-high-availability.md).

Az automatikus feladatátvételi csoportok emellett olyan írási és olvasási figyelőket is biztosítanak, amelyek változatlanok maradnak a feladatátvételek során. Akár kézi, akár automatikus feladatátvételi aktiválást használ, a feladatátvétel a csoportban lévő összes másodlagos adatbázist elsődlegesre váltja. Az adatbázis-feladatátvétel befejeződése után a rendszer automatikusan frissíti a DNS-rekordot, hogy átirányítsa a végpontokat az új régióba. Az adott RPO-és RTO-információk esetében lásd: [az üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).

Ha automatikus feladatátvételi házirenddel rendelkező automatikus feladatátvételi csoportokat használ, az SQL Database-kiszolgáló vagy a felügyelt példány adatbázisait érintő kimaradások automatikusan feladatátvételt eredményeznek. Az automatikus feladatátvételi csoportot az alábbiak szerint kezelheti:

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [Azure CLI: feladatátvételi csoport](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: feladatátvételi csoport](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: feladatátvételi csoport](/rest/api/sql/failovergroups).

A feladatátvételt követően gondoskodjon arról, hogy a kiszolgáló és az adatbázis hitelesítési követelményei az új elsődlegesen legyenek konfigurálva. Részletekért lásd: [SQL Database biztonság a katasztrófa utáni helyreállítás után](sql-database-geo-replication-security-config.md).

A valós Üzletmenet-folytonosság eléréséhez az adatközpontok közötti adatbázis-redundancia csak a megoldás része. Egy alkalmazás (szolgáltatás) teljes körű helyreállítása végzetes hiba után a szolgáltatást és a függő szolgáltatásokat alkotó összes összetevő helyreállítását igényli. Ezen összetevők közé tartoznak például az ügyfélszoftverek (például egy egyéni JavaScripttel rendelkező böngésző), a webes kezelőfelületek, a tárterület és a DNS. Kritikus fontosságú, hogy minden összetevő azonos hibákra legyen rugalmas, és az alkalmazás helyreállítási idő célkitűzésén (RTO) belül elérhetővé váljon. Ezért az összes függő szolgáltatást azonosítania kell, és ismernie kell az általuk nyújtott garanciákat és képességeket. Ezt követően meg kell tennie a megfelelő lépéseket annak biztosításához, hogy a szolgáltatás a szolgáltatások feladatátvétele alatt működjön, amelytől függ. A megoldásoknak a vész-helyreállításhoz való kialakításával kapcsolatos további információkért lásd: [felhőalapú megoldások tervezése a vész-helyreállításhoz az aktív földrajzi replikálás használatával](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Automatikus feladatátvételi csoport terminológiája és képességei

- **Feladatátvételi csoport (köd)**

  A feladatátvételi csoport egyetlen SQL Database kiszolgáló vagy egyetlen felügyelt példány által felügyelt adatbázisok névvel ellátott csoportja, amely a feladatátvételt egységként egy másik régióba helyezi, ha az összes vagy néhány elsődleges adatbázis elérhetetlenné válik az elsődleges régió meghibásodása miatt. Felügyelt példányok esetén a feladatátvételi csoport a példány összes felhasználói adatbázisát tartalmazza, ezért csak egy feladatátvételi csoport konfigurálható egy példányon.
  
  > [!IMPORTANT]
  > A feladatátvételi csoport nevének globálisan egyedinek kell lennie a `.database.windows.net` tartományon belül.

- **Kiszolgálók SQL Database**

     A SQL Database-kiszolgálókkal egy adott SQL Database-kiszolgálón lévő felhasználói adatbázisok némelyike vagy mindegyike feladatátvételi csoportba helyezhető. Emellett a SQL Database-kiszolgálók több feladatátvételi csoportot is támogatnak egyetlen SQL Database-kiszolgálón.

- **Elsődleges**

  A feladatátvételi csoportban lévő elsődleges adatbázisokat üzemeltető SQL Database-kiszolgáló vagy felügyelt példány.

- **Másodlagos**

  A feladatátvételi csoportban található másodlagos adatbázisokat üzemeltető SQL Database-kiszolgáló vagy felügyelt példány. A másodlagos nem lehet ugyanabban a régióban, mint az elsődleges.

- **Önálló adatbázisok hozzáadása a feladatátvételi csoporthoz**

  Ugyanazon a SQL Database kiszolgálón több önálló adatbázis is elhelyezhető ugyanabba a feladatátvételi csoportba. Ha egyetlen adatbázist ad hozzá a feladatátvételi csoporthoz, a automatikusan létrehoz egy másodlagos adatbázist ugyanazzal a kiadással és számítási mérettel a másodlagos kiszolgálón.  A kiszolgálót a feladatátvételi csoport létrehozásakor adta meg. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal a másodlagos kiszolgálón, a csoport örökli a földrajzi replikálási hivatkozást. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, akkor a rendszer egy új másodlagost hoz létre a másodlagos kiszolgálón.

  > [!IMPORTANT]
  > Győződjön meg arról, hogy a másodlagos kiszolgálónak nincs azonos nevű adatbázisa, kivéve, ha egy meglévő másodlagos adatbázis. A felügyelt példány feladatátvételi csoportjaiban minden felhasználói adatbázis replikálódik. A feladatátvételi csoportban nem választhat felhasználói adatbázisok egy részhalmazát.

- **Adatbázisok hozzáadása rugalmas készletből a feladatátvételi csoportba**

  Egy rugalmas készletben lévő összes vagy több adatbázis ugyanabba a feladatátvételi csoportba helyezhető. Ha az elsődleges adatbázis egy rugalmas készletben van, a rendszer automatikusan létrehozza a másodlagost a rugalmas készletben ugyanazzal a névvel (másodlagos készlettel). Győződjön meg arról, hogy a másodlagos kiszolgáló tartalmaz egy rugalmas készletet ugyanazzal a pontos névvel és elegendő szabad kapacitással a feladatátvételi csoport által létrehozandó másodlagos adatbázisok üzemeltetéséhez. Ha olyan adatbázist ad hozzá a készlethez, amely már rendelkezik másodlagos adatbázissal a másodlagos készletben, akkor a csoport örökli a földrajzi replikálási hivatkozást. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, a rendszer egy új másodlagost hoz létre a másodlagos készletben.
  
- **Kezdeti előkészítés** 

  Adatbázisok, rugalmas készletek vagy felügyelt példányok feladatátvételi csoportba való felvételekor az adatreplikáció elindítása előtt kezdeti előkészítési fázis van. A kezdeti előkészítési fázis a leghosszabb és legdrágább művelet. A kezdeti beültetés befejezése után a rendszer szinkronizálja az adatokat, majd csak a további adatváltozásokat replikálja. A kezdeti vetõmag befejezéséhez szükséges idő az adatok méretétől, a replikált adatbázisok számától, valamint a feladatátvételi csoportban lévő entitások közötti kapcsolat sebességétől függ. Normál körülmények között a tipikus előkészítési sebesség 50-500 GB egy óra egyetlen adatbázishoz vagy rugalmas készlethez, és 18-35 GB a felügyelt példányok számára. A rendszer az összes adatbázis párhuzamos előkészítését végzi. A jelzett előkészítési sebességet, valamint az adatbázisok számát és az adatmennyiség teljes méretét is használhatja, hogy megbecsülje, mennyi ideig tart a kezdeti előkészítési fázis az adatreplikáció megkezdése előtt.

  Felügyelt példányok esetén a két példány közötti expressz útvonal-kapcsolat sebességét is figyelembe kell venni a kezdeti előkészítési fázis idejének becslése során. Ha a két példány közötti kapcsolat sebessége lassabb a szükségesnél, a magokra adott idő valószínűleg különösen hatással van. Felhasználhatja a megadott előkészítési sebességet, az adatbázisok számát, az adatmennyiség teljes méretét, valamint a kapcsolat sebességét annak becsléséhez, hogy mennyi ideig tart a kezdeti előkészítési fázis az adatreplikálás megkezdése előtt. Egy 100 GB-os adatbázis esetében például a kezdeti vetőmag-fázis a 2,8-5,5 órán belül bárhol elveszik, ha a hivatkozás képes a 35 GB/óra lenyomására. Ha a hivatkozás csak 10 GB/óra átvitelt tud végezni, akkor a 100 GB-os adatbázis kivetése körülbelül 10 órát vesz igénybe. Ha több adatbázis replikálására van szükség, a rendszer párhuzamosan hajtja végre a bevezetést, és ha lassú kapcsolati sebességgel kombinálja, a kezdeti előkészítési fázis jóval hosszabb időt is igénybe vehet, különösen akkor, ha az összes adatbázisból származó adatok párhuzamos kihelyezése meghaladja a rendelkezésre álló sávszélesség csatolása. Ha a két példány közötti hálózati sávszélesség korlátozott, és több felügyelt példányt ad hozzá egy feladatátvételi csoporthoz, érdemes egymás után több felügyelt példányt hozzáadni a feladatátvételi csoporthoz.

  
- **DNS-zóna**

  Egy új példány létrehozásakor automatikusan generált egyedi azonosító. A példányhoz több tartományra kiterjedő (SAN) tanúsítvány van kiépítve, amely az ügyfélkapcsolatokat az azonos DNS-zónában lévő bármelyik példánnyal hitelesíti. Az ugyanabban a feladatátvételi csoportban lévő két felügyelt példánynak meg kell osztania a DNS-zónát.
  
  > [!NOTE]
  > SQL Database kiszolgálókhoz létrehozott feladatátvételi csoportok esetében nincs szükség DNS-zóna AZONOSÍTÓra.

- **Feladatátvételi csoport írási-olvasási figyelője**

  DNS-CNAME rekord, amely az aktuális elsődleges URL-címre mutat. A rendszer automatikusan létrehozza a feladatátvételi csoport létrehozásakor, és lehetővé teszi az írható és olvasható SQL-feladatok transzparens újracsatlakozását az elsődleges adatbázishoz, amikor az elsődleges módosítások a feladatátvételt követően változnak. Ha a feladatátvételi csoport egy SQL Database kiszolgálón jön létre, a figyelő URL-címéhez tartozó DNS CNAME-rekord `<fog-name>.database.windows.net`ként jön létre. Ha a feladatátvételi csoportot felügyelt példányon hozza létre, a figyelő URL-címéhez tartozó DNS CNAME-rekord `<fog-name>.zone_id.database.windows.net`ként jön létre.

- **Feladatátvételi csoport írásvédett figyelője**

  Egy olyan DNS-CNAME rekord, amely a másodlagos URL-címére mutató írásvédett figyelőre mutat. A rendszer automatikusan létrehozza a feladatátvételi csoport létrehozásakor, és lehetővé teszi, hogy a csak olvasási jogosultsággal rendelkező SQL-munkaterhelés transzparens módon kapcsolódjon a másodlagoshoz a megadott terheléselosztási szabályok használatával. Ha a feladatátvételi csoport egy SQL Database kiszolgálón jön létre, a figyelő URL-címéhez tartozó DNS CNAME-rekord `<fog-name>.secondary.database.windows.net`ként jön létre. Ha a feladatátvételi csoportot felügyelt példányon hozza létre, a figyelő URL-címéhez tartozó DNS CNAME-rekord `<fog-name>.zone_id.secondary.database.windows.net`ként jön létre.

- **Automatikus feladatátvételi szabályzat**

  Alapértelmezés szerint a feladatátvételi csoport automatikus feladatátvételi házirenddel van konfigurálva. A SQL Database szolgáltatás elindítja a feladatátvételt a hiba észlelése után, és a türelmi időszak lejárt. A rendszernek ellenőriznie kell, hogy a kiesést nem lehet-e enyhíteni a [SQL Database szolgáltatás beépített, magas rendelkezésre állású infrastruktúrája](sql-database-high-availability.md) által a hatás skálázása miatt. Ha az alkalmazásból szeretné vezérelni a feladatátvételi munkafolyamatot, kikapcsolhatja az automatikus feladatátvételt.
  
  > [!NOTE]
  > Mivel a leállás skálázásának ellenőrzése és az, hogy milyen gyorsan lehet enyhíteni az operatív csapat által végzett emberi műveleteket, a türelmi időszak nem állítható be egy óra alatt.  Ez a korlátozás a feladatátvételi csoport összes adatbázisára vonatkozik, az adatszinkronizálási állapotuktól függetlenül. 

- **Csak olvasási feladatátvételi szabályzat**

  Alapértelmezés szerint a csak olvasási figyelő feladatátvétele le van tiltva. Gondoskodik arról, hogy az elsődleges teljesítmény ne legyen hatással a másodlagos kapcsolat nélküli állapotra. Ez azonban azt is jelenti, hogy a csak olvasási munkamenetek nem fognak tudni csatlakozni, amíg a másodlagos helyre nem kerül. Ha nem tudja elviselni a leállást a csak olvasási munkamenetek esetében, és az elsődlegesnél a csak olvasási és írási/olvasási forgalom esetében az elsődlegeset használja, akkor a `AllowReadOnlyFailoverToPrimary` tulajdonság konfigurálásával engedélyezheti a feladatátvételt a csak olvasási figyelő számára. Ebben az esetben a csak olvasási forgalom automatikusan átirányítva lesz az elsődlegesre, ha a másodlagos nem érhető el.

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

  Mivel az elsődleges és másodlagos adatbázisok aszinkron replikálással vannak szinkronizálva, a feladatátvétel adatvesztést okozhat. Testreszabhatja az automatikus feladatátvételi szabályzatot, hogy tükrözze az alkalmazás adatvesztési tűréshatárát. `GracePeriodWithDataLossHours`konfigurálásával szabályozhatja, hogy a rendszer mennyi ideig várjon, amíg a rendszer megvárja a feladatátvételt, amely valószínűleg adatvesztést okoz.

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

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Több adatbázis feladatátvételének kezelése egy vagy több feladatátvételi csoport használatával

A különböző régiókban (elsődleges és másodlagos kiszolgálókon) lévő két kiszolgáló között egy vagy több feladatátvételi csoport is létrehozható. Az egyes csoportok tartalmazhatnak egy vagy több olyan adatbázist, amelyeket egységként állítanak be abban az esetben, ha az összes vagy néhány elsődleges adatbázis elérhetetlenné válik az elsődleges régióban bekövetkező leállás miatt. A feladatátvételi csoport a földrajzilag másodlagos adatbázist hozza létre ugyanazzal a szolgáltatási céllal, mint az elsődleges. Ha egy meglévő geo-replikációs kapcsolatot ad hozzá a feladatátvételi csoporthoz, győződjön meg arról, hogy a Geo-másodlagos kiszolgáló ugyanazzal a szolgáltatási réteggel és számítási mérettel van konfigurálva, mint az elsődleges.
  
> [!IMPORTANT]
> Az önálló adatbázisok és a rugalmas készletek jelenleg nem támogatják a feladatátvételi csoportok létrehozását a különböző előfizetésekben található két kiszolgáló között. Ha a feladatátvételi csoport létrehozása után áthelyezi az elsődleges vagy a másodlagos kiszolgálót egy másik előfizetésre, a feladatátvételi kérelmek és egyéb műveletek meghibásodását okozhatják.

### <a name="using-read-write-listener-for-oltp-workload"></a>Írási-olvasási figyelő használata a OLTP számítási feladatokhoz

OLTP műveletek végrehajtásakor használja a `<fog-name>.database.windows.net` kiszolgáló URL-címét, és a kapcsolatok automatikusan az elsődlegesre lesznek irányítva. Ez az URL-cím nem változik a feladatátvétel után. Megjegyzés: a feladatátvétel magában foglalja a DNS-rekord frissítését, hogy az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítésekor legyenek átirányítva az új elsődlegesre.

### <a name="using-read-only-listener-for-read-only-workload"></a>Írásvédett munkaterheléshez csak olvasási figyelő használata

Ha az adatok bizonyos elavulása érdekében logikailag elszigetelt írásvédett munkaterheléssel rendelkezik, használhatja az alkalmazás másodlagos adatbázisát. Csak olvasási munkamenetek esetén használja a `<fog-name>.secondary.database.windows.net` kiszolgáló URL-címét, a kapcsolat pedig automatikusan a másodlagosra lesz irányítva. Azt is javasoljuk, hogy a `ApplicationIntent=ReadOnly`használatával jelezze a kapcsolódó karakterláncot a következővel:. Ha biztosítani kívánja, hogy a csak olvasási feladattal rendelkező munkaterhelés újrakapcsolódjon a feladatátvétel után, vagy ha a másodlagos kiszolgáló offline állapotba kerül, konfigurálja a feladatátvételi házirend `AllowReadOnlyFailoverToPrimary` tulajdonságát.

### <a name="preparing-for-performance-degradation"></a>A teljesítmény romlásának előkészítése

Egy tipikus Azure-alkalmazás több Azure-szolgáltatást használ, és több összetevőből áll. A feladatátvételi csoport automatikus feladatátvétele az Azure SQL-összetevők állapotán alapul. Előfordulhat, hogy a leállás nem érinti az elsődleges régió többi Azure-szolgáltatását, és ezek összetevői továbbra is elérhetők lesznek az adott régióban. Ha az elsődleges adatbázisok a DR régióra váltanak, a függő összetevők közötti késés megnövekszik. Ha el szeretné kerülni az alkalmazás teljesítményének nagyobb késleltetését, győződjön meg arról, hogy az alkalmazás összes összetevőjének redundancia van a DR régióban, és kövesse ezeket a [hálózati biztonsági irányelveket](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Adatvesztés előkészítése

Ha a rendszer kimaradást észlel, az SQL megvárja a `GracePeriodWithDataLossHours`által megadott időszakot. Az alapértelmezett érték 1 óra. Ha nem engedheti meg az adatvesztést, ügyeljen arra, hogy a `GracePeriodWithDataLossHours`t elég nagy számra, például 24 órára állítsa be. A manuális csoport feladatátvételi szolgáltatásával visszatérhet a másodlagosról az elsődlegesre.

> [!IMPORTANT]
> A 800-es vagy kevesebb DTU és több mint 250 adatbázissal rendelkező, Geo-replikációt használó rugalmas készletek olyan problémákba ütközhet, mint a hosszabb tervezett feladatátvétel és a csökkentett teljesítmény.  Ezek a problémák nagyobb valószínűséggel fordulnak elő az írási igényű munkaterhelések esetében, ha a Geo-replikációs végpontok földrajzilag széles körben elkülönülnek, vagy ha az egyes adatbázisokhoz több másodlagos végpont is használatos.  Ezeknek a problémáknak a tünetei akkor jelennek meg, ha a földrajzi replikálási késés idővel növekszik.  Ez a késés figyelhető a [sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)használatával.  Ha ezek a problémák lépnek fel, a mérséklések közé tartozik a készlet DTU számának növelése vagy a Geo-replikált adatbázisok számának csökkentése ugyanabban a készletben.

### <a name="changing-secondary-region-of-the-failover-group"></a>A feladatátvételi csoport másodlagos régiójának módosítása

A módosítási folyamat szemléltetése érdekében feltételezzük, hogy az a kiszolgáló az elsődleges kiszolgáló, a B kiszolgáló a meglévő másodlagos kiszolgáló, a C kiszolgáló pedig az új másodlagos a harmadik régióban.  Az áttérés elvégzéséhez kövesse az alábbi lépéseket:

1.  Hozzon létre további formátumú másodlagos zónák az A kiszolgálón lévő összes adatbázisról a C kiszolgálóra [aktív geo-replikáció](sql-database-active-geo-replication.md)használatával. Az A kiszolgáló minden adatbázisa két formátumú másodlagos zónák fog rendelkezni, egyet a B kiszolgálón, egyet pedig a C kiszolgálón. Ez garantálja, hogy az elsődleges adatbázisok védelme az áttérés során is megmaradjon.
2.  Törölje a feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek. Ennek az az oka, hogy a feladatátvételi csoport figyelőkhöz tartozó SQL-aliasok törölve lettek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét.
3.  Hozza létre újra a feladatátvételi csoportot ugyanazzal a névvel az A és a C kiszolgáló között. Ezen a ponton a bejelentkezések sikertelenek lesznek.
4.  Adja hozzá az A kiszolgálón lévő összes elsődleges adatbázist az új feladatátvételi csoporthoz.
5.  A B kiszolgáló eldobása. A B kiszolgálón lévő összes adatbázist automatikusan törli a rendszer. 


### <a name="changing-primary-region-of-the-failover-group"></a>A feladatátvételi csoport elsődleges régiójának módosítása

A módosítási folyamat szemléltetése érdekében feltételezzük, hogy az a kiszolgáló az elsődleges kiszolgáló, a B kiszolgáló a meglévő másodlagos kiszolgáló, a C kiszolgáló pedig az új elsődleges a harmadik régióban.  Az áttérés elvégzéséhez kövesse az alábbi lépéseket:

1.  Végezzen el egy tervezett feladatátvételt az elsődleges kiszolgáló B kiszolgálóra való átváltásához. a kiszolgáló lesz az új másodlagos kiszolgáló. A feladatátvétel több perc állásidőt eredményezhet. A tényleges idő a feladatátvételi csoport méretétől függ.
2.  Hozzon létre további formátumú másodlagos zónák az egyes adatbázisokról a B kiszolgálón a C kiszolgálóra az [aktív földrajzi replikálás](sql-database-active-geo-replication.md)használatával. A B kiszolgálón található minden adatbázis két formátumú másodlagos zónák rendelkezik, egyet az A kiszolgálón, egy pedig a C kiszolgálón. Ez garantálja, hogy az elsődleges adatbázisok védelme az áttérés során is megmaradjon.
3.  Törölje a feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek. Ennek az az oka, hogy a feladatátvételi csoport figyelőkhöz tartozó SQL-aliasok törölve lettek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét.
4.  Hozza létre újra a feladatátvételi csoportot ugyanazzal a névvel az A és a C kiszolgáló között. Ezen a ponton a bejelentkezések sikertelenek lesznek.
5.  Vegye fel a B kiszolgálón található összes elsődleges adatbázist az új feladatátvételi csoportba. 
6.  Végezzen el egy tervezett feladatátvételt a feladatátvételi csoportban a B és C kapcsolóhoz. Most a C kiszolgáló lesz az elsődleges és a B-a másodlagos. Az A kiszolgálón lévő összes másodlagos adatbázis automatikusan a C-ben lévő elsődlegesekhez lesz társítva. Ahogy az 1. lépésben, a feladatátvétel több perc állásidőt eredményezhet.
6.  Dobja el az A kiszolgálót. Az A-ben lévő összes adatbázist automatikusan törli a rendszer.

> [!IMPORTANT]
> A feladatátvételi csoport törlésekor a figyelő végpontok DNS-rekordjai is törlődnek. Ezen a ponton nem nulla a valószínűsége annak, hogy valaki más feladatátvételi csoportot vagy kiszolgálói aliast hozzon létre ugyanazzal a névvel, ami megakadályozza, hogy újra használja. A kockázat minimalizálásához ne használja az általános feladatátvételi csoportok nevét.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Ajánlott eljárások a feladatátvételi csoportok használatáról felügyelt példányokkal

Az automatikus feladatátvételi csoportot az elsődleges példányon kell konfigurálni, és egy másik Azure-régióban található másodlagos példánnyal kell csatlakoznia.  A példány összes adatbázisa replikálva lesz a másodlagos példányra.

A következő ábra egy geo-redundáns felhőalapú alkalmazás tipikus konfigurációját szemlélteti, amely felügyelt példányt és automatikus feladatátvételi csoportot használ.

![automatikus feladatátvétel](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Tekintse meg a felügyelt [példány hozzáadása feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md) című témakört, amely részletesen ismerteti a felügyelt példányok feladatátvételi csoport használatára való hozzáadásának lépéseit.

Ha az alkalmazás felügyelt példányt használ adatcsomagként, kövesse az alábbi általános irányelveket az üzletmenet folytonosságának tervezésekor:

### <a name="creating-the-secondary-instance"></a>A másodlagos példány létrehozása 

Annak biztosítása érdekében, hogy az elsődleges és a másodlagos példányok feladatátvétele ne szakítsa meg a kapcsolatot az elsődleges példánnyal, ugyanabban a DNS-zónában kell lennie. A szolgáltatás garantálja, hogy ugyanaz a többtartományos (SAN) tanúsítvány használható az ügyfélkapcsolatok hitelesítésére a feladatátvételi csoport két példányának egyikén. Ha az alkalmazás készen áll az éles környezetben való üzembe helyezésre, hozzon létre egy másodlagos példányt egy másik régióban, és győződjön meg róla, hogy az elsődleges példánnyal osztja meg a DNS-zónát. Ezt megteheti egy `DNS Zone Partner` választható paraméter megadásával a Azure Portal, a PowerShell vagy a REST API használatával.

> [!IMPORTANT]
> Az alhálózatban létrehozott első példány határozza meg a DNS-zónát az azonos alhálózaton lévő összes további példánynál. Ez azt jelenti, hogy az azonos alhálózatból származó két példány nem tartozhat különböző DNS-zónákhoz.

Az elsődleges példánnyal azonos DNS-zónában található másodlagos példány létrehozásával kapcsolatos további információkért lásd: [másodlagos felügyelt példány létrehozása](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

### <a name="enabling-replication-traffic-between-two-instances"></a>A replikálási forgalom engedélyezése két példány között

Mivel minden példány el van különítve a saját VNet, engedélyezni kell a két irányú adatforgalmat a virtuális hálózatok között. Lásd: [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Feladatátvételi csoport létrehozása különböző előfizetésekben található felügyelt példányok között

A felügyelt példányok között két különböző előfizetésben hozhat létre feladatátvételi csoportot. A PowerShell API használatakor megteheti a `PartnerSubscriptionId` paraméter megadásával a másodlagos példányhoz. REST API használatakor a `properties.managedInstancePairs` paraméterben szereplő minden példány-azonosító saját subscriptionID rendelkezhet.
  
> [!IMPORTANT]
> Az Azure Portal nem támogatja a feladatátvételi csoportokat a különböző előfizetések között.

### <a name="managing-failover-to-secondary-instance"></a>Feladatátvétel kezelése másodlagos példányra

A feladatátvételi csoport a példány összes adatbázisának feladatátvételét fogja kezelni. Egy csoport létrehozásakor a példány minden adatbázisa automatikusan a másodlagos példányra lesz replikálva. A feladatátvételi csoportok nem használhatók az adatbázisok egy részhalmazának részleges feladatátvételének kezdeményezésére.

> [!IMPORTANT]
> Ha egy adatbázis el lett távolítva az elsődleges példányból, akkor a rendszer automatikusan elveszi a Geo másodlagos példányon is.

### <a name="using-read-write-listener-for-oltp-workload"></a>Írási-olvasási figyelő használata a OLTP számítási feladatokhoz

OLTP műveletek végrehajtásakor használja a `<fog-name>.zone_id.database.windows.net` kiszolgáló URL-címét, és a kapcsolatok automatikusan az elsődlegesre lesznek irányítva. Ez az URL-cím nem változik a feladatátvétel után. A feladatátvétel magában foglalja a DNS-rekord frissítését, így az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítésekor lesznek átirányítva az új elsődlegesre. Mivel a másodlagos példány megosztja a DNS-zónát az elsődleges értékkel, az ügyfélalkalmazás újra csatlakozhat ugyanahhoz a SAN-tanúsítványhoz.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>A csak olvasási figyelő használata a másodlagos példányhoz való kapcsolódáshoz

Ha az adatok bizonyos elavulása érdekében logikailag elszigetelt írásvédett munkaterheléssel rendelkezik, használhatja az alkalmazás másodlagos adatbázisát. Ha közvetlenül a földrajzilag replikált másodlagoshoz szeretne csatlakozni, használja a `server.secondary.zone_id.database.windows.net` kiszolgáló URL-címére, és a kapcsolat közvetlenül a földrajzilag replikált másodlagosra történik.

> [!NOTE]
> Bizonyos szolgáltatási rétegekben a Azure SQL Database támogatja a csak olvasható [replikák](sql-database-read-scale-out.md) használatát, hogy csak egy írásvédett replika kapacitásával és a kapcsolódási karakterláncban szereplő `ApplicationIntent=ReadOnly` paraméterrel terheléselosztást lehessen alkalmazni az írásvédett lekérdezési feladatok kiegyensúlyozására. Ha egy földrajzilag replikált másodlagos beállítást konfigurált, ezzel a képességgel csatlakozhat egy írásvédett replikához az elsődleges helyen vagy a földrajzilag replikált helyen.
> - Az elsődleges helyen található írásvédett replikához való kapcsolódáshoz használja a `<fog-name>.zone_id.database.windows.net`.
> - A másodlagos helyen található írásvédett replikához való kapcsolódáshoz használja a `<fog-name>.secondary.zone_id.database.windows.net`.

### <a name="preparing-for-performance-degradation"></a>A teljesítmény romlásának előkészítése

Egy tipikus Azure-alkalmazás több Azure-szolgáltatást használ, és több összetevőből áll. A feladatátvételi csoport automatikus feladatátvétele az Azure SQL-összetevők állapotán alapul. Előfordulhat, hogy a leállás nem érinti az elsődleges régió többi Azure-szolgáltatását, és ezek összetevői továbbra is elérhetők lesznek az adott régióban. Ha az elsődleges adatbázisok a DR régióra váltanak, a függő összetevők közötti késés megnövekszik. Ha el szeretné kerülni az alkalmazás teljesítményének nagyobb késleltetését, győződjön meg arról, hogy az alkalmazás összes összetevőjének redundancia van a DR régióban, és kövesse ezeket a [hálózati biztonsági irányelveket](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Adatvesztés előkészítése

Ha a rendszer áramszünetet észlel, az SQL automatikusan elindítja az írási és olvasási feladatátvételt, ha a lehető legjobb ismerete nulla adatvesztést okoz. Ellenkező esetben a `GracePeriodWithDataLossHours`által megadott időszakra vár. Ha a `GracePeriodWithDataLossHours`t adta meg, előkészítheti az adatvesztést. Általánosságban elmondható, hogy az Azure továbbra is rendelkezésre áll. Ha nem engedheti meg az adatvesztést, ügyeljen arra, hogy a GracePeriodWithDataLossHours megfelelően nagy számú, például 24 óráig állítsa be.

Az írható-olvasható figyelő DNS-frissítése a feladatátvétel megkezdése után azonnal megtörténik. Ez a művelet nem eredményez adatvesztést. Az adatbázis-szerepkörök váltásának folyamata azonban normál körülmények között akár 5 percet is igénybe vehet. Amíg a művelet be nem fejeződik, az új elsődleges példány egyes adatbázisai továbbra is írásvédettek maradnak. Ha a feladatátvételt a PowerShell használatával kezdeményezik, a teljes művelet szinkronban van. Ha a Azure Portal használatával kezdeményezik, akkor a felhasználói felület a befejezési állapotot jelzi. Ha a REST API használatával kezdeményezik, a végrehajtás figyeléséhez használja a standard Azure Resource Manager lekérdezési mechanizmusát.

> [!IMPORTANT]
> A manuális csoport feladatátvételével visszahelyezheti az elsődleges helyre az elsődleges helyet. Ha a feladatátvételt okozó leállás le lett csökkentve, az elsődleges adatbázisokat az eredeti helyre helyezheti át. Ehhez el kell indítania a csoport manuális feladatátvételét.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>A feladatátvételi csoport másodlagos régiójának módosítása

Tegyük fel, hogy az a példány az elsődleges példány, a B példány a meglévő másodlagos példány, a C példány pedig a harmadik régió új másodlagos példánya.  Az áttérés elvégzéséhez kövesse az alábbi lépéseket:

1.  Hozzon létre a C példányt ugyanazzal a mérettel, mint a és ugyanabban a DNS-zónában. 
2.  Törölje a feladatátvételi csoportot az A és A B példány között. Ezen a ponton a bejelentkezések sikertelenek lesznek, mert a feladatátvételi csoport figyelőkhöz tartozó SQL-aliasok törölve lettek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét. A másodlagos adatbázisok le lesznek választva az elsődlegesekről, és írási-olvasási adatbázisok lesznek. 
3.  Hozzon létre egy azonos nevű feladatátvételi csoportot az A és A C példány között. kövesse a [feladatátvételi csoport utasításait a felügyelt példányok oktatóanyagával](sql-database-managed-instance-failover-group-tutorial.md). Ez egy adatméreti művelet, amely akkor fejeződik be, amikor az A példány összes adatbázisát kivezetik és szinkronizálják.
4.  Törölje a B példányt, ha nem szükséges a szükségtelen díjak elkerüléséhez.

> [!NOTE]
> A 2. lépés és a 3. lépés elvégzése után az "a" példányban lévő adatbázisok nem védettek maradnak az A példány katasztrofális meghibásodása miatt.

### <a name="changing-primary-region-of-the-failover-group"></a>A feladatátvételi csoport elsődleges régiójának módosítása

Tegyük fel, hogy az a példány az elsődleges példány, a B példány a meglévő másodlagos példány, a C példány pedig a harmadik régió új elsődleges példánya.  Az áttérés elvégzéséhez kövesse az alábbi lépéseket:

1.  Hozzon létre egy C példányt ugyanazzal a mérettel, mint B és ugyanabban a DNS-zónában. 
2.  Kapcsolódjon a B példányhoz, és manuálisan végezze el a feladatátvételt az elsődleges példány B-re való átváltásához. az A példány automatikusan az új másodlagos példány lesz.
3.  Törölje a feladatátvételi csoportot az A és A B példány között. Ezen a ponton a bejelentkezések sikertelenek lesznek, mert a feladatátvételi csoport figyelőkhöz tartozó SQL-aliasok törölve lettek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét. A másodlagos adatbázisok le lesznek választva az elsődlegesekről, és írási-olvasási adatbázisok lesznek. 
4.  Hozzon létre egy azonos nevű feladatátvételi csoportot az A és A C példány között. kövesse a [feladatátvételi csoport utasításait a felügyelt példányok oktatóanyagával](sql-database-managed-instance-failover-group-tutorial.md). Ez egy adatméreti művelet, amely akkor fejeződik be, amikor az A példány összes adatbázisát kivezetik és szinkronizálják.
5.  Ha nem szeretné elkerülni A szükségtelen költségeket, törölje az A példányt.

> [!NOTE] 
> A 3. lépés és a 4. lépés befejezése után az A példányban lévő adatbázisok nem védettek maradnak az A példány katasztrofális meghibásodása miatt.

> [!IMPORTANT]
> A feladatátvételi csoport törlésekor a figyelő végpontok DNS-rekordjai is törlődnek. Ezen a ponton nem nulla a valószínűsége annak, hogy valaki más feladatátvételi csoportot vagy kiszolgálói aliast hozzon létre ugyanazzal a névvel, ami megakadályozza, hogy újra használja. A kockázat minimalizálásához ne használja az általános feladatátvételi csoportok nevét.

## <a name="failover-groups-and-network-security"></a>Feladatátvételi csoportok és hálózati biztonság

Egyes alkalmazásokhoz a biztonsági szabályok megkövetelik, hogy az adatcsomaghoz való hálózati hozzáférés egy adott összetevőhöz vagy összetevőkhöz, például egy virtuális géphez, egy webszolgáltatáshoz, stb. van korlátozva. Ez a követelmény az üzletmenet folytonosságának kialakítására és a feladatátvételi csoportok használatára vonatkozó kihívásokat mutatja be. Az ilyen korlátozott hozzáférés megvalósításakor vegye figyelembe az alábbi beállításokat.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Feladatátvételi csoportok és virtuális hálózati szabályok használata

Ha [Virtual Network szolgáltatási végpontokat és szabályokat](sql-database-vnet-service-endpoint-rule-overview.md) használ az SQL-adatbázishoz való hozzáférés korlátozására, vegye figyelembe, hogy az egyes Virtual Network szolgáltatási végpontok csak egy Azure-régióra vonatkoznak. A végpont nem teszi lehetővé más régiók számára, hogy fogadják a kommunikációt az alhálózatból. Ezért csak az ugyanabban a régióban üzembe helyezett ügyfélalkalmazások csatlakozhatnak az elsődleges adatbázishoz. Mivel a feladatátvétel az SQL-ügyfél munkameneteinek egy másik (másodlagos) régióban lévő kiszolgálóra való átirányítását eredményezi, akkor ezek a munkamenetek sikertelenek lesznek, ha az adott régión kívüli ügyféltől származik. Emiatt az automatikus feladatátvételi házirend nem engedélyezhető, ha a résztvevő kiszolgálók szerepelnek a Virtual Network szabályokban. A manuális feladatátvétel támogatásához kövesse az alábbi lépéseket:

1. Az alkalmazás előtér-összetevőinek (webszolgáltatás, virtuális gépek stb.) redundáns másolatainak kiépítése a másodlagos régióban
2. A [virtuális hálózati szabályok](sql-database-vnet-service-endpoint-rule-overview.md) konfigurálása egyenként az elsődleges és a másodlagos kiszolgáló számára
3. Az [előtér-feladatátvétel engedélyezése Traffic Manager-konfiguráció használatával](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. A manuális feladatátvétel elindítása a leállás észlelésekor. Ez a beállítás olyan alkalmazásokra van optimalizálva, amelyek konzisztens késést igényelnek az előtér és az adatréteg között, és támogatja a helyreállítást, ha az előtér, az adatréteg vagy mindkettő hatással van a kimaradásra.

> [!NOTE]
> Ha az írásvédett **figyelőt** a csak olvasási feladatok terheléselosztásához használja, akkor győződjön meg arról, hogy ez a számítási feladat a másodlagos régióban lévő virtuális gépen vagy más erőforráson fut, így a másodlagos adatbázishoz csatlakozhat.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Feladatátvételi csoportok és SQL Database-tűzfalszabályok használata

Ha az üzletmenet-folytonossági terv automatikus feladatátvételi csoportokkal való feladatátvételt igényel, akkor a hagyományos tűzfalszabályok használatával korlátozhatja az SQL-adatbázishoz való hozzáférést. Az automatikus feladatátvétel támogatásához kövesse az alábbi lépéseket:

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
2. A két felügyelt példánynak ugyanazt a szolgáltatási szintet kell megadnia, és ugyanazzal a tárolási mérettel kell rendelkeznie.
3. A másodlagos felügyelt példánynak üresnek kell lennie (nincs felhasználói adatbázis).
4. A felügyelt példányok által használt virtuális hálózatokat [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [expressz útvonalon](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)keresztül kell csatlakoztatni. Ha két virtuális hálózat egy helyszíni hálózaton keresztül kapcsolódik, győződjön meg arról, hogy nincs tűzfalszabály blokkolja a 5022-es és a 11000-11999-es portot. A globális VNet-társítás nem támogatott.
5. A felügyelt példányok két virtuális hálózatok nem rendelkezhet átfedéses IP-címekkel.
6. Be kell állítania a hálózati biztonsági csoportokat (NSG) úgy, hogy a 5022-es és a 11000 ~ 12000-as tartomány a másik felügyelt példány alhálózatában lévő kapcsolatokhoz nyitott bejövő és kimenő. Ez lehetővé teszi a példányok közötti replikációs forgalmat.

   > [!IMPORTANT]
   > Helytelenül konfigurált NSG biztonsági szabályok vezetnek az adatbázis-másolási műveletek elakadása érdekében.

7. A másodlagos példány a helyes DNS-zóna azonosítójával van konfigurálva. A DNS-zóna felügyelt példányok és virtuális fürtök egyik tulajdonsága, és az azonosítója szerepel az állomásnév címe mezőben. A zóna azonosítója véletlenszerű karakterláncként jön létre, ha az első felügyelt példány létrejön az egyes VNet, és ugyanaz az azonosító az ugyanazon alhálózaton lévő összes többi példányhoz van rendelve. Hozzárendelés után a DNS-zóna nem módosítható. Az ugyanabban a feladatátvételi csoportban található felügyelt példányoknak meg kell osztaniuk a DNS-zónát. Ezt úgy érheti el, ha a másodlagos példány létrehozásakor a DnsZonePartner paraméter értékeként átadja az elsődleges példány zónájának AZONOSÍTÓját. 

   > [!NOTE]
   > A feladatátvételi csoportok felügyelt példánnyal való konfigurálásával kapcsolatos részletes oktatóanyagért lásd: [felügyelt példány hozzáadása feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Elsődleges adatbázis frissítése vagy visszaminősítése

A másodlagos adatbázisok leválasztása nélkül frissítheti vagy visszaminősítheti az elsődleges adatbázist más számítási méretre (ugyanazon a szolgáltatási szinten belül, általános célú és üzletileg kritikus között). A frissítéskor javasoljuk, hogy először frissítse az összes másodlagos adatbázist, majd frissítse az elsődlegest. A visszalépést követően a sorrend megfordításakor a rendszer visszaminősíti az elsődlegest, majd lecseréli az összes másodlagos adatbázist. Ha az adatbázist egy másik szolgáltatási szintre frissíti vagy visszaminősíti, ez a javaslat érvénybe lép.

Ezt a sorozatot kifejezetten arra a problémára érdemes elkerülni, hogy az alacsonyabb SKU-ban lévő másodlagos példány túlterhelt legyen, és a frissítés vagy a lefokozási folyamat során újra kell magot adni. A problémát úgy is elkerülheti, ha az elsődleges írásvédett, az összes írási és olvasási feladatnak az elsődlegesen való hatásának rovására kerül.

> [!NOTE]
> Ha a másodlagos adatbázist a feladatátvételi csoport konfigurációjának részeként hozta létre, a másodlagos adatbázis visszalépéséhez nem ajánlott. Ezzel biztosíthatja, hogy az adatmennyiség elegendő kapacitással legyen feldolgozva a rendszeres számítási feladatok elvégzése után a feladatátvétel aktiválása után.

## <a name="preventing-the-loss-of-critical-data"></a>A kritikus fontosságú adatmennyiség elvesztésének megakadályozása

A nagyméretű hálózatok nagy késése miatt a folyamatos másolás aszinkron replikációs mechanizmust használ. Az aszinkron replikáció során az adatvesztés elkerülhető, ha hiba történik. Előfordulhat azonban, hogy egyes alkalmazások nem igényelnek adatvesztést. A kritikus frissítések elleni védelem érdekében az alkalmazás fejlesztője a tranzakció véglegesítése után azonnal meghívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) rendszereljárást. A hívás `sp_wait_for_database_copy_sync` blokkolja a hívó szálat, amíg az utolsó véglegesített tranzakció át nem lett továbbítva a másodlagos adatbázisba. Azonban nem várja meg, amíg a továbbított tranzakciók újra le lesznek játszva és véglegesítve lettek a másodlagoson. `sp_wait_for_database_copy_sync` hatóköre egy adott folyamatos másolási hivatkozásra vonatkozik. Minden olyan felhasználó, aki az elsődleges adatbázishoz kapcsolódási jogokkal rendelkezik, meghívhatja ezt az eljárást.

> [!NOTE]
> `sp_wait_for_database_copy_sync` megakadályozza az adatvesztést a feladatátvétel után, de nem garantálja a teljes szinkronizálást az olvasási hozzáféréshez. A `sp_wait_for_database_copy_sync`i eljárás hívása által okozott késleltetés jelentős lehet, és a tranzakciós napló méretétől függ a hívás időpontjában.

## <a name="failover-groups-and-point-in-time-restore"></a>Feladatátvételi csoportok és időponthoz történő visszaállítás

További információ a feladatátvételi csoportokkal való időpontra történő visszaállításról: [időponthoz való helyreállítás (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>A feladatátvételi csoportok korlátai

Vegye figyelembe a következő korlátozásokat:

- Nem hozható létre feladatátvételi csoport két kiszolgáló vagy példány között ugyanabban az Azure-régióban.
- A feladatátvételi csoport nem nevezhető át. Törölnie kell a csoportot, majd újra létre kell hoznia egy másik névvel. 
- A feladatátvételi csoportban lévő példányok nem támogatják az adatbázis átnevezését. Az adatbázisok átnevezéséhez átmenetileg törölnie kell a feladatátvételi csoportot.

## <a name="programmatically-managing-failover-groups"></a>Feladatátvételi csoportok programozott kezelése

Ahogy azt korábban említettük, az automatikus feladatátvételi csoportok és az aktív geo-replikáció programozott módon is felügyelhető Azure PowerShell és a REST API használatával. A következő táblázatok ismertetik az elérhető parancsok készletét. Az aktív geo-replikálás Azure Resource Manager API-kat tartalmaz a felügyelethez, beleértve a [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) és [Azure PowerShell parancsmagokat](https://docs.microsoft.com/powershell/azure/overview). Ezek az API-k az erőforráscsoportok használatát igénylik, és támogatják a szerepköralapú biztonságot (RBAC). A hozzáférési szerepkörök megvalósításával kapcsolatos további információkért lásd: [Azure szerepköralapú Access Control](../role-based-access-control/overview.md).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Az SQL Database feladatátvételének kezelése önálló adatbázisokkal és rugalmas készletekkel

| Parancsmag | Leírás |
| --- | --- |
| [Új – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt mind az elsődleges, mind a másodlagos kiszolgálókon.|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Feladatátvételi csoport konfigurációjának beolvasása |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Feladatátvételi csoport konfigurációjának módosítása |
| [Kapcsoló – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Feladatátvételi csoport feladatátvételét indítja el a másodlagos kiszolgálóra |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Egy vagy több adatbázis beadása egy feladatátvételi csoportba|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>SQL Database-alapú feladatátvételi csoportok kezelése felügyelt példányokkal

| Parancsmag | Leírás |
| --- | --- |
| [Új – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és a másodlagos példányokon is.|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Feladatátvételi csoport konfigurációjának módosítása|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Feladatátvételi csoport konfigurációjának beolvasása|
| [Kapcsoló – AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Feladatátvételi csoport feladatátvételét indítja el a másodlagos példányra|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Feladatátvételi csoport eltávolítása|

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Az SQL Database feladatátvételének kezelése önálló adatbázisokkal és rugalmas készletekkel

| Parancs | Leírás |
| --- | --- |
| [az SQL feladatátvétel-csoport létrehozása](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt mind az elsődleges, mind a másodlagos kiszolgálókon.|
| [az SQL feladatátvétel-csoport törlése](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [az SQL feladatátvétel-Group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Feladatátvételi csoport konfigurációjának beolvasása |
| [az SQL feladatátvétel-csoport frissítése](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Módosítja a feladatátvételi csoport konfigurációját, és/vagy egy vagy több adatbázist vesz fel egy feladatátvételi csoportba.|
| [az SQL feladatátvétel-csoport beállítása – elsődleges](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Feladatátvételi csoport feladatátvételét indítja el a másodlagos kiszolgálóra |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>SQL Database-alapú feladatátvételi csoportok kezelése felügyelt példányokkal

| Parancs | Leírás |
| --- | --- |
| [az SQL instance-feladatátvétel-Group Create](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és a másodlagos példányokon is. |
| [az SQL instance-feladatátvétel-Group Update](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | A feladatátvételi csoport konfigurációjának módosítása|
| [az SQL instance-feladatátvétel-Group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Feladatátvételi csoport konfigurációjának beolvasása|
| [az SQL instance-feladatátvétel-Group set-Primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Feladatátvételi csoport feladatátvételét indítja el a másodlagos példányra|
| [az SQL instance-feladatátvétel-csoport törlése](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Feladatátvételi csoport eltávolítása |

* * *

> [!IMPORTANT]
> Egy minta parancsfájl esetében lásd: [feladatátvételi csoport konfigurálása és feladatátvétele egyetlen adatbázishoz](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: az SQL Database feladatátvételi csoportok kezelése egyetlen és készletezett adatbázisokkal

| API | Leírás |
| --- | --- |
| [Feladatátvételi csoport létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Feladatátvételi csoport létrehozása vagy frissítése |
| [Feladatátvételi csoport törlése](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Feladatátvétel (tervezett)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | A teljes adatszinkronizálással elindítja az aktuális elsődleges kiszolgálóról a másodlagos kiszolgálóra irányuló feladatátvételt.|
| [Adatvesztés kényszerített feladatátvételének engedélyezése](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Az aktuális elsődleges kiszolgálóról a másodlagos kiszolgálóra történő feladatátvételt az adatok szinkronizálása nélkül indítja el. A művelet adatvesztést okozhat. |
| [Feladatátvételi csoport beolvasása](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Lekéri a feladatátvételi csoport konfigurációját. |
| [Feladatátvételi csoportok listázása kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | A kiszolgálón található feladatátvételi csoportok felsorolása. |
| [Feladatátvételi csoport frissítése](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | A feladatátvételi csoport konfigurációjának frissítése. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: feladatátvevő csoportok kezelése felügyelt példányokkal

| API | Leírás |
| --- | --- |
| [Feladatátvételi csoport létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Feladatátvételi csoport konfigurációjának létrehozása vagy frissítése |
| [Feladatátvételi csoport törlése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Feladatátvételi csoport eltávolítása a példányból |
| [Feladatátvétel (tervezett)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | A teljes adatszinkronizálással elindítja az aktuális elsődleges példány feladatátvételét erre a példányra. |
| [Adatvesztés kényszerített feladatátvételének engedélyezése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Az aktuális elsődleges példányról a másodlagos példányra történő feladatátvételt az adatok szinkronizálása nélkül indítja el. A művelet adatvesztést okozhat. |
| [Feladatátvételi csoport beolvasása](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Lekéri a feladatátvételi csoport konfigurációját. |
| [Feladatátvételi csoportok listázása hely szerint](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Egy helyen lévő feladatátvételi csoportok felsorolása. |

## <a name="next-steps"></a>Következő lépések

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

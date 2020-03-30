---
title: Feladatátvételi csoportok
description: Az automatikus feladatátvételi csoportok egy SQL Database szolgáltatás, amely lehetővé teszi az SQL Database-kiszolgálón vagy a felügyelt példány összes adatbázis-csoportjának replikációs és automatikus / koordinált feladatátvételének kezelését.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269834"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Több adatbázis transzparens és összehangolt feladatátvételének engedélyezése automatikus feladatátvételi csoportok használatával

Az automatikus feladatátvételi csoportok az SQL Database szolgáltatás, amely lehetővé teszi az SQL Database-kiszolgálón lévő adatbázisok egy csoportjának vagy egy felügyelt példány összes adatbázisának egy másik régióba való replikációjának és feladatátvételének kezelését. Ez egy deklaratív absztrakció a meglévő [aktív georeplikációs](sql-database-active-geo-replication.md) szolgáltatás tetején, amelynek célja a georeplikált adatbázisok nagy méretű üzembe helyezésének és felügyeletének egyszerűsítése. A feladatátvételt manuálisan is kezdeményezheti, vagy delegálhatja az SQL Database szolgáltatásban egy felhasználó által definiált házirend alapján. Az utóbbi beállítás lehetővé teszi, hogy automatikusan helyreállítsa a több kapcsolódó adatbázisok egy másodlagos régióban egy katasztrofális hiba vagy más nem tervezett esemény, amely az SQL Database szolgáltatás rendelkezésre állásának teljes vagy részleges elvesztését eredményezi az elsődleges régióban. A feladatátvételi csoport tartalmazhat egy vagy több adatbázist, amelyeket általában ugyanaz az alkalmazás használ. Emellett az olvasható másodlagos adatbázisok segítségével kiszervezheti az írásvédett lekérdezési számítási feladatokat. Mivel az automatikus feladatátvételi csoportok több adatbázist foglalnak magukban, ezeket az adatbázisokat az elsődleges kiszolgálón kell konfigurálni. Az automatikus feladatátvételi csoportok támogatják a csoport összes adatbázisának replikációját egy másik régióban lévő egyetlen másodlagos kiszolgálóra.

> [!NOTE]
> Ha egy- vagy készletezésű adatbázisokkal dolgozik egy SQL Database-kiszolgálón, és ugyanabban vagy különböző régiókban több másodlagos adatbázist szeretne használni, használja az [aktív georeplikációt.](sql-database-active-geo-replication.md) 

Ha automatikus feladatátvételi házirenddel rendelkező automatikus feladatátvételi csoportokat használ, a csoport egy vagy több adatbázisát érintő kimaradás automatikus feladatátvételt eredményez. Ezek általában olyan incidensek, amelyeket a beépített automatikus magas rendelkezésre állású műveletek nem lehet önmagam enyhíteni. A feladatátvételi eseményindítók közé tartozik egy olyan incidens, amelyet egy SQL-bérlői gyűrű vagy vezérlőgyűrű okozott, mivel több számítási csomóponton lévő operációsrendszer-kernel memóriavesztés eközben nem működik, vagy egy vagy több bérlői csengetés által okozott incidens, mert rossz hálózati kábelt vágtak ki a hiba során a hardver ekként történő rutinszerű leszerelése.  További információt az [SQL Database Magas rendelkezésre állású adatbázisában](sql-database-high-availability.md)talál.

Emellett az automatikus feladatátvételi csoportok írási és olvasási és csak olvasható figyelő végpontokat biztosítanak, amelyek változatlanok maradnak a feladatátvételek során. Akár manuális, akár automatikus feladatátvételi aktiválást használ, a feladatátvétel a csoport összes másodlagos adatbázisát elsődlegesre váltja át. Az adatbázis feladatátvétele befejezése után a DNS-rekord automatikusan frissül, hogy a végpontokat az új régióba irányítsa át. A konkrét RPO- és RTO-adatokat az [Üzletmenet-folytonosság áttekintése](sql-database-business-continuity.md)című témakörben találja.

Ha automatikus feladatátvételi házirenddel rendelkező automatikus feladatátvételi csoportokat használ, az SQL Database-kiszolgáló vagy a felügyelt példány adatbázisait befolyásoló kimaradás automatikus feladatátvételt eredményez. Az automatikus feladatátvételi csoport a következő használatával kezelhető:

- [Azure-portál](sql-database-implement-geo-distributed-database.md)
- [Azure CLI: Feladatátvételi csoport](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: Feladatátvételi csoport](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: Feladatátvételi csoport](/rest/api/sql/failovergroups).

Feladatátvétel után győződjön meg arról, hogy a kiszolgáló és az adatbázis hitelesítési követelményei az új elsődleges en vannak konfigurálva. További információt az [SQL Database biztonsága a vészhelyreállítás után című témakörben talál.](sql-database-geo-replication-security-config.md)

A valódi üzletmenet-folytonosság elérése érdekében az adatközpontok közötti adatbázis-redundancia hozzáadása csak egy része a megoldásnak. Egy alkalmazás (szolgáltatás) teljes körű helyreállítása egy katasztrofális hiba után a szolgáltatást és a függő szolgáltatásokat alkotó összes összetevő helyreállítását igényli. Ilyen összetevők például az ügyfélszoftver (például egy egyéni JavaScript-alapú böngésző), a webes előtér-végek, a tárolás és a DNS. Fontos, hogy minden összetevő rugalmas legyen az azonos hibákkal szemben, és elérhetővé váljon az alkalmazás helyreállítási idő célkitűzésén (RTO) belül. Ezért azonosítania kell az összes függő szolgáltatást, és meg kell értenie az általuk nyújtott garanciákat és képességeket. Ezután meg kell tennie a megfelelő lépéseket annak biztosítására, hogy a szolgáltatás a szolgáltatások feladatátvétele során, amelytől függ, megfelelő lépéseket kell tennie. A vész-helyreállítási megoldások tervezéséről további információt a [Felhőbeli megoldások tervezése az aktív georeplikáció használatával című vész-helyreállítási megoldásokról](sql-database-designing-cloud-solutions-for-disaster-recovery.md)talál.

## <a name="auto-failover-group-terminology-and-capabilities"></a>Automatikus feladatátvételi csoport terminológiája és képességei

- **Feladatátvételi csoport (KÖD)**

  A feladatátvételi csoport egyetlen SQL Database-kiszolgáló vagy egyetlen felügyelt példány által kezelt adatbázis-névvel ellátott csoport, amely egy másik régióba való egységként átveheti a feladatátvételt abban az esetben, ha az elsődleges régióban kimaradás miatt az összes vagy néhány elsődleges adatbázis elérhetetlenné válik. Felügyelt példányok létrehozásakor a feladatátvételi csoport tartalmazza a példány összes felhasználói adatbázisát, ezért csak egy feladatátvételi csoport konfigurálható egy példányon.
  
  > [!IMPORTANT]
  > A feladatátvételi csoport nevének globálisan `.database.windows.net` egyedinek kell lennie a tartományon belül.

- **SQL-adatbáziskiszolgálók**

     Az SQL Database-kiszolgálók segítségével egyetlen SQL Database-kiszolgáló nain belüli felhasználói adatbázisok egy része vagy egésze feladatátvételi csoportba helyezhető. Az SQL Database-kiszolgáló emellett több feladatátvételi csoportot is támogat egyetlen SQL Database-kiszolgálón.

- **Elsődleges**

  Az SQL Database-kiszolgáló vagy a feladatátvételi csoport elsődleges adatbázisait tároló felügyelt példány.

- **Másodlagos**

  Az SQL Database-kiszolgáló vagy a feladatátvételi csoport másodlagos adatbázisait tároló felügyelt példány. A másodlagos nem lehet ugyanabban a régióban, mint az elsődleges.

- **Egyetlen adatbázis hozzáadása feladatátvételi csoporthoz**

  Ugyanazon az SQL Database-kiszolgálón több adatbázist is elhelyezhet ugyanabban a feladatátvételi csoportban. Ha egyetlen adatbázist ad hozzá a feladatátvételi csoporthoz, az automatikusan létrehoz egy másodlagos adatbázist ugyanazzal a kiadással és számítási mérettel a másodlagos kiszolgálón.  Ezt a kiszolgálót a feladatátvételi csoport létrehozásakor adta meg. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal a másodlagos kiszolgálón, a csoport örökli azt a georeplikációs kapcsolatot. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, új másodlagos jön létre a másodlagos kiszolgálón.

  > [!IMPORTANT]
  > Győződjön meg arról, hogy a másodlagos kiszolgáló nem rendelkezik azonos nevű adatbázissal, kivéve, ha az egy meglévő másodlagos adatbázis. A felügyelt példány feladatátvételi csoportjaiban az összes felhasználói adatbázis replikálása történik. A feladatátvételi csoportban a replikációhoz a felhasználói adatbázisok egy részhalmaza nem választható ki.

- **Adatbázisok hozzáadása rugalmas készletben feladatátvételi csoporthoz**

  Egy rugalmas készleten belül az összes vagy több adatbázist ugyanabba a feladatátvételi csoportba helyezheti. Ha az elsődleges adatbázis egy rugalmas készletben van, a másodlagos automatikusan létrejön a rugalmas készlet azonos nevű (másodlagos készlet). Győződjön meg arról, hogy a másodlagos kiszolgáló tartalmaz egy rugalmas készlet azonos pontos nevét, és elég szabad kapacitása a feladatátvételi csoport által létrehozott másodlagos adatbázisok üzemeltetéséhez. Ha olyan adatbázist ad hozzá a készlethez, amely már rendelkezik másodlagos adatbázissal a másodlagos készletben, a csoport örökli a georeplikációs kapcsolatot. Ha olyan adatbázist ad hozzá, amely már rendelkezik másodlagos adatbázissal egy olyan kiszolgálón, amely nem része a feladatátvételi csoportnak, egy új másodlagos jön létre a másodlagos készletben.
  
- **Kezdeti vetés** 

  Adatbázisok, rugalmas készletek vagy felügyelt példányok hozzáadásakor egy feladatátvételi csoporthoz, az adatreplikáció megkezdése előtt van egy kezdeti elrendeződési fázis. A kezdeti vetési fázis a leghosszabb és legdrágább művelet. A kezdeti vetés befejezése után az adatok szinkronizálása, majd csak a későbbi adatváltozások replikálása történik. A kezdeti mag befejezéséhez szükséges idő az adatok méretétől, a replikált adatbázisok számától és a feladatátvételi csoportban lévő entitások közötti kapcsolat sebességétél. Normál körülmények között a tipikus vetési sebesség 50–500 GB óránként egy adatbázis vagy rugalmas készlet, és 18–35 GB óránként egy felügyelt példány. A vetés az összes adatbázis párhuzamosan történik. Használhatja a megadott vetési sebesség, valamint az adatbázisok száma és az adatok teljes mérete megbecsülni, hogy mennyi ideig tart a kezdeti vetési fázis ban az adatreplikáció megkezdése.

  Felügyelt példányok esetén a két példány közötti expressz útvonal-kapcsolat sebességét is figyelembe kell venni a kezdeti vetési fázis idejének becslésénél. Ha a két példány közötti kapcsolat sebessége lassabb, mint ami szükséges, a vetőmaghoz szükséges idő valószínűleg jelentősen érintett. A megadott vetési sebesség, az adatbázisok száma, az adatok teljes mérete és a kapcsolat sebessége segítségével megbecsülheti, hogy a kezdeti vetési fázis mennyi ideig tart az adatreplikáció megkezdése előtt. Egy 100 GB-os adatbázis esetén például a kezdeti magfázis 2,8–5,5 órát vesz igénybe, ha a kapcsolat óránként 35 GB-ot képes lenyomni. Ha a kapcsolat csak 10 GB/óra átvitelt tud, akkor a 100 GB-os adatbázis létrehozása körülbelül 10 órát vesz igénybe. Ha több adatbázist kell replikálni, a vetés párhuzamosan kerül végrehajtásra, és lassú kapcsolati sebességgel kombinálva a kezdeti vetési fázis jelentősen hosszabb időt vehet igénybe, különösen akkor, ha az összes adatbázisból származó adatok párhuzamos vetése meghaladja a rendelkezésre álló adatokat. sávszélességet. Ha a két példány közötti hálózati sávszélesség korlátozott, és több felügyelt példányt ad hozzá egy feladatátvevő csoporthoz, fontolja meg több felügyelt példány hozzáadását a feladatátvételi csoporthoz egymás után, egyenként.

  
- **DNS-zóna**

  Egy egyedi azonosító, amely automatikusan jön létre, amikor egy új példány jön létre. A példány többtartományos (SAN) tanúsítványa ki van építve az ügyfélkapcsolatok hitelesítéséhez az ugyanabban a DNS-zónában lévő bármely példányhoz. Az azonos feladatátvételi csoportban lévő két felügyelt példánynak meg kell osztania a DNS-zónát.
  
  > [!NOTE]
  > Az SQL Database-kiszolgálókhoz létrehozott feladatátvételi csoportok hozadékához nem szükséges DNS-zónaazonosító.

- **Feladatátvevő csoport írás-olvasási figyelő**

  Az aktuális elsődleges URL-címére mutató DNS CNAME rekord. A feladatátvételi csoport létrehozásakor automatikusan létrejön, és lehetővé teszi, hogy az írási és olvasási SQL-munkaterhelés transzparens módon csatlakozzon újra az elsődleges adatbázishoz, amikor az elsődleges megváltozik a feladatátvétel után. Amikor a feladatátvételi csoportot egy SQL Database-kiszolgálón hozták létre, `<fog-name>.database.windows.net`a figyelő URL-címéhez tartozó DNS CNAME rekord a . Amikor a feladatátvételi csoport egy felügyelt példányon jön létre, a `<fog-name>.zone_id.database.windows.net`figyelő URL-címének DNS CNAME rekordja a .

- **Feladatátvevő csoport írásvédett figyelő**

  Létrejött egy DNS CNAME rekord, amely a másodlagos URL-címére mutat. A feladatátvételi csoport létrehozásakor automatikusan létrejön, és lehetővé teszi, hogy az írásvédett SQL-számítási feladat a megadott terheléselosztási szabályok használatával transzparens módon csatlakozzon a másodlagoshoz. Amikor a feladatátvételi csoportot egy SQL Database-kiszolgálón hozták létre, `<fog-name>.secondary.database.windows.net`a figyelő URL-címéhez tartozó DNS CNAME rekord a . Amikor a feladatátvételi csoport egy felügyelt példányon jön létre, a `<fog-name>.zone_id.secondary.database.windows.net`figyelő URL-címének DNS CNAME rekordja a .

- **Automatikus feladatátvételi házirend**

  Alapértelmezés szerint a feladatátvételi csoport automatikus feladatátvételi házirenddel van konfigurálva. Az SQL Database szolgáltatás a hiba észlelése és a türelmi időszak lejárta után elindítja a feladatátvételt. A rendszernek ellenőriznie kell, hogy a kimaradás nem csökkenthető-e az SQL Database szolgáltatás beépített [magas rendelkezésre állású infrastruktúrájával](sql-database-high-availability.md) a hatás mértéke miatt. Ha az alkalmazásból szeretné vezérelni a feladatátvételi munkafolyamatot, kikapcsolhatja az automatikus feladatátvételt.
  
  > [!NOTE]
  > Mivel a kimaradás mértékének ellenőrzése és a csökkenthetőség milyen gyorsan az operatív csapat emberi műveleteit foglalja magában, a türelmi időszak nem állítható be egy óra alatt.  Ez a korlátozás a feladatátvételi csoport összes adatbázisára vonatkozik, függetlenül azok adatszinkronizálási állapotától. 

- **Csak olvasási feladatátvételi házirend**

  Alapértelmezés szerint az írásvédett figyelő feladatátvétele le van tiltva. Biztosítja, hogy az elsődleges teljesítménynem befolyásolja, ha a másodlagos offline állapotban van. Ez azonban azt is jelenti, hogy az írásvédett munkamenetek nem tudnak csatlakozni, amíg a másodlagos helyre nem áll. Ha nem tudja elviselni az állásidőt az írásvédett munkamenetek, és rendben van, hogy ideiglenesen használja az elsődleges mind az írásvédett és az írás-olvasási forgalom rovására a lehetséges teljesítményromlás az elsődleges, engedélyezheti a feladatátvételt az írásvédett figyelő a `AllowReadOnlyFailoverToPrimary` tulajdonság konfigurálásával. Ebben az esetben az írásvédett forgalom automatikusan átlesz irányítva az elsődleges, ha a másodlagos nem érhető el.

- **Tervezett feladatátvétel**

   A tervezett feladatátvétel teljes szinkronizálást hajt végre az elsődleges és a másodlagos adatbázisok között, mielőtt a másodlagos kapcsolók az elsődleges szerepkörre váltanak. Ez garantálja az adatvesztést. A tervezett feladatátvétel a következő esetekben használatos:

  - Vész-helyreállítási (DR) gyakorlatok végrehajtása éles környezetben, ha az adatvesztés nem elfogadható
  - Az adatbázisok áthelyezése egy másik régióba
  - Adja vissza az adatbázisokat az elsődleges régióba a kimaradás csökkentése (feladat-visszavétel) után.

- **Nem tervezett feladatátvétel**

   Nem tervezett vagy kényszerített feladatátvétel azonnal átváltja a másodlagos az elsődleges szerepkör nélkül szinkronizálás az elsődleges. Ez a művelet adatvesztést eredményez. Nem tervezett feladatátvételi a helyreállítási módszer kimaradások során, ha az elsődleges nem érhető el. Amikor az eredeti elsődleges újra online állapotba kerül, automatikusan újra csatlakozik szinkronizálás nélkül, és új másodlagossá válik.

- **Kézi feladatátvétel**

  Az automatikus feladatátvételi konfigurációtól függetlenül manuálisan is kezdeményezheti a feladatátvételt. Ha az automatikus feladatátvételi házirend nincs konfigurálva, manuális feladatátvételre van szükség a feladatátvételi csoport ban lévő adatbázisok másodlagos helyreállítása érdekében. Kezdeményezhet kényszerített vagy rövid feladatátvételt (teljes adatszinkronizálással). Ez utóbbi felhasználható az elsődleges másodlagos régióba való áthelyezésére. A feladatátvétel befejezésekor a DNS-rekordok automatikusan frissülnek, hogy biztosítsák az új elsődleges

- **Türelmi idő adatvesztéssel**

  Mivel az elsődleges és másodlagos adatbázisok szinkronizálása aszinkron replikációval történik, a feladatátvétel adatvesztést okozhat. Testreszabhatja az automatikus feladatátvételi házirendet, hogy tükrözze az alkalmazás adatvesztésre vonatkozó toleranciáját. A konfigurálásával `GracePeriodWithDataLossHours`szabályozhatja, hogy a rendszer mennyi ideig várjon az adatvesztést valószínűleg eredményező feladatátvétel megkezdése előtt.

- **Több feladatátvételi csoport**

  Több feladatátvételi csoportot is konfigurálhat ugyanahhoz a kiszolgálópárhoz a feladatátvételek méretezésének szabályozásához. Minden csoport egymástól függetlenül átadja a feletteállót. Ha a több-bérlős alkalmazás rugalmas készletek, ezt a képességet használhatja az elsődleges és másodlagos adatbázisok keverésére minden készletben. Így csökkentheti a kimaradás hatását a bérlők nek csak a felére.

  > [!NOTE]
  > A felügyelt példány nem támogat több feladatátvételi csoportot.
  
## <a name="permissions"></a>Engedélyek

A feladatátvevő csoport engedélyeit [a rendszer szerepköralapú hozzáférés-vezérléssel (RBAC)](../role-based-access-control/overview.md)kezeli. Az [SQL Server Közreműködőszerepkör](../role-based-access-control/built-in-roles.md#sql-server-contributor) rendelkezik a feladatátvételi csoportok kezeléséhez szükséges összes engedéllyel.

### <a name="create-failover-group"></a>Feladatátvételi csoport létrehozása

Feladatátvételi csoport létrehozásához rBAC írási hozzáférést kell biztosítania mind az elsődleges, mind a másodlagos kiszolgálókhoz, valamint a feladatátvételi csoport összes adatbázisához. Felügyelt példány esetén rBAC-írási hozzáférésre van szükség mind az elsődleges, mind a másodlagos felügyelt példányhoz, de az egyes adatbázisokra vonatkozó engedélyek nem relevánsak, mivel az egyes felügyelt példányok adatbázisai nem adhatók hozzá, illetve nem távolíthatók el a feladatátvételi csoportból. 

### <a name="update-a-failover-group"></a>Feladatátvevő csoport frissítése

Feladatátvételi csoport frissítéséhez rBAC írási hozzáférést kell biztosítania a feladatátvételi csoporthoz, valamint az aktuális elsődleges kiszolgáló vagy felügyelt példány összes adatbázisához.  

### <a name="failover-a-failover-group"></a>Feladatátvételi csoport átvétele

Feladatátvételi csoport feladatátvételi csoport, rBAC írási hozzáférést kell biztosítania a feladatátvételi csoport az új elsődleges kiszolgáló vagy felügyelt példány.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>A feladatátvételi csoportok egyetlen adatbázissal és rugalmas készletekkel való használatának ajánlott gyakorlati tanácsok

Az automatikus feladatátvételi csoportot az elsődleges SQL Database-kiszolgálón kell konfigurálni, és egy másik Azure-régióban a másodlagos SQL-adatbázis-kiszolgálóhoz kell csatlakoztatni. A csoportok ezekben a kiszolgálókban az összes vagy néhány adatbázist tartalmazhatják. Az alábbi ábra egy georedundáns felhőalkalmazás több adatbázist és automatikus feladatátvételi csoportot használó tipikus konfigurációját mutatja be.

![automatikus feladatátvétel](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Egyetlen [adatbázis hozzáadása feladatátvételi csoporthoz](sql-database-single-database-failover-group-tutorial.md) című részletes, részletes, lépésenkénti oktatóanyag, amely egyetlen adatbázist ad hozzá egy feladatátvételi csoporthoz című témakörben.

Ha egy szolgáltatást az üzletmenet folytonosságát szem előtt tartva tervez, kövesse az alábbi általános irányelveket:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Több adatbázis feladatátvételének kezelése egy vagy több feladatátvételi csoport használatával

Egy vagy több feladatátvételi csoport hozható létre két kiszolgáló között különböző régiókban (elsődleges és másodlagos kiszolgálók). Minden csoport tartalmazhat egy vagy több adatbázist, amelyek egységként helyreállnak abban az esetben, ha az összes vagy néhány elsődleges adatbázis elérhetetlenné válik az elsődleges régióban egy kimaradás miatt. A feladatátvételi csoport geomásodlagos adatbázist hoz létre az elsődlegessel azonos szolgáltatáscéllal. Ha egy meglévő georeplikációs kapcsolatot ad hozzá a feladatátvételi csoporthoz, győződjön meg arról, hogy a geo-másodlagos konfigurálva van az elsődleges szolgáltatásszinttel és számítási mérettel.
  
> [!IMPORTANT]
> Feladatátvételi csoportok létrehozása két kiszolgáló között a különböző előfizetések jelenleg nem támogatott egyetlen adatbázisok és rugalmas készletek. Ha az elsődleges vagy másodlagos kiszolgálót a feladatátvételi csoport létrehozása után áthelyezi egy másik előfizetésre, az a feladatátvételi kérelmek és egyéb műveletek hibáit eredményezheti.

### <a name="using-read-write-listener-for-oltp-workload"></a>Írás-olvasásfigyelő használata oltp számítási feladatokhoz

Az OLTP-műveletek végrehajtásakor használja `<fog-name>.database.windows.net` a kiszolgáló URL-címét, és a kapcsolatok automatikusan az elsődleges. Ez az URL-cím nem változik a feladatátvétel után. Vegye figyelembe, hogy a feladatátvétel magában foglalja a DNS-rekord frissítését, így az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítése után kerülnek átirányításra az új elsődleges rekedésbe.

### <a name="using-read-only-listener-for-read-only-workload"></a>Csak olvasható figyelő használata csak olvasási feladatokhoz

Ha egy logikailag elkülönített írásvédett számítási feladatok, amely toleráns bizonyos adatfagyás, használhatja a másodlagos adatbázis az alkalmazásban. Csak olvasható munkamenetek `<fog-name>.secondary.database.windows.net` esetén használja a kiszolgáló URL-címét, és a kapcsolat automatikusan a másodlagoshoz kerül. Azt is javasoljuk, hogy a kapcsolatban `ApplicationIntent=ReadOnly`a karakterlánc olvasási szándékát a használatával adja meg. Ha biztosítani szeretné, hogy az írásvédett munkaterhelés újra csatlakozzon a feladatátvétel után, vagy `AllowReadOnlyFailoverToPrimary` ha a másodlagos kiszolgáló offline állapotba kerül, konfigurálja a feladatátvételi házirend tulajdonságát.

### <a name="preparing-for-performance-degradation"></a>Felkészülés a teljesítmény romlására

Egy tipikus Azure-alkalmazás több Azure-szolgáltatást használ, és több összetevőből áll. A feladatátvételi csoport automatikus feladatátvételi aktiválása az Azure SQL-összetevők állapota alapján történik. Az elsődleges régióban lévő egyéb Azure-szolgáltatásokat nem érintheti a szolgáltatáskimaradás, és összetevőik továbbra is elérhetők lehetnek az adott régióban. Miután az elsődleges adatbázisok átváltanak a VÉSZ-régióra, a függő összetevők közötti késés növekedhet. A nagyobb késés nek az alkalmazás teljesítményére gyakorolt hatásának elkerülése érdekében biztosítsa az alkalmazás összes összetevőjének redundanciát a VÉSZ-régióban, és kövesse ezeket a [hálózati biztonsági irányelveket.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Felkészülés az adatvesztésre

Ha kimaradást észlel, az SQL a megadott `GracePeriodWithDataLossHours`időszakra vár. Az alapértelmezett érték 1 óra. Ha nem engedheti meg magának az `GracePeriodWithDataLossHours` adatvesztést, győződjön meg róla, hogy elég nagy számra, például 24 órára van beállítva. A kézi csoport feladatátvétel használatával a másodlagos az elsődleges.

> [!IMPORTANT]
> A 800 vagy kevesebb DRU-val és több mint 250, georeplikációt használó adatbázissal rendelkező rugalmas készletek problémákat okozhatnak, például a hosszabb tervezett feladatátvételeket és a leromlott teljesítményt.  Ezek a problémák nagyobb valószínűséggel fordulnak elő írási intenzív számítási feladatok nál, ha a földrajzi hely földrajzi lag szerint széles körben elvannak választva a georeplikációs végpontok, vagy ha minden adatbázishoz több másodlagos végpontot használnak.  Ezeknek a problémáknak a tünetei akkor jelentkeznek, amikor a georeplikációs késés idővel növekszik.  Ez a lag lehet ellenőrizni segítségével [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Ha ezek a problémák fordulnak elő, majd a kockázatcsökkentési szint tartalmazza a készlet DIT-ek számának növelését vagy az ugyanabban a készletben lévő georeplikált adatbázisok számának csökkentését.

### <a name="changing-secondary-region-of-the-failover-group"></a>A feladatátvételi csoport másodlagos régiójának módosítása

A változási sorrend szemléltetéséhez feltételezzük, hogy az A kiszolgáló az elsődleges kiszolgáló, a B kiszolgáló a meglévő másodlagos kiszolgáló, a C kiszolgáló pedig a harmadik régió új másodlagos kiszolgálója.  Az átmenet végrehajtásához hajtsa végre az alábbi lépéseket:

1.  Hozzon létre további másodlagos adatokat az A és a C kiszolgáló közötti minden adatbázisból [az aktív georeplikáció](sql-database-active-geo-replication.md)használatával. Az A kiszolgálón minden adatbázisnak két másodlagos adatbázisa lesz, az egyik a B kiszolgálón, a másik pedig a C kiszolgálón. Ez garantálja, hogy az elsődleges adatbázisok védettek maradjanak az átmenet során.
2.  Törölje a feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek. Ennek az az oka, hogy a feladatátvevő csoport figyelőinek SQL-aliasai törlődtek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét.
3.  Hozza létre újra a feladatátvételi csoportot ugyanazzal a névvel az A és C kiszolgálók között. Ezen a ponton a bejelentkezések leáll nem.
4.  Az A kiszolgáló összes elsődleges adatbázisának hozzáadása az új feladatátvételi csoporthoz.
5.  Drop szerver B. A B adatbázisai automatikusan törlődnek. 


### <a name="changing-primary-region-of-the-failover-group"></a>A feladatátvételi csoport elsődleges régiójának módosítása

A változási sorrend szemléltetéséhez feltételezzük, hogy az A kiszolgáló az elsődleges kiszolgáló, a B kiszolgáló a meglévő másodlagos kiszolgáló, a C kiszolgáló pedig az új elsődleges a harmadik régióban.  Az átmenet végrehajtásához hajtsa végre az alábbi lépéseket:

1.  Tervezett feladatátvétel végrehajtása az elsődleges kiszolgáló B-kiszolgálóra való váltásához. A feladatátvétel több perc nyi leállást eredményezhet. A tényleges idő a feladatátvételi csoport méretétől függ.
2.  Hozzon létre további másodlagos adatokat a B kiszolgálón a B kiszolgálón [az aktív georeplikáció](sql-database-active-geo-replication.md)használatával. A B kiszolgálón minden adatbázisnak két másodlagos adatbázisa lesz, az egyik az A kiszolgálón, a másik pedig a C kiszolgálón. Ez garantálja, hogy az elsődleges adatbázisok védettek maradjanak az átmenet során.
3.  Törölje a feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek. Ennek az az oka, hogy a feladatátvevő csoport figyelőinek SQL-aliasai törlődtek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét.
4.  Hozza létre újra a feladatátvételi csoportot ugyanazzal a névvel az A és C kiszolgálók között. Ezen a ponton a bejelentkezések leáll nem.
5.  Adja hozzá az összes elsődleges adatbázist a B-n az új feladatátvételi csoporthoz. 
6.  A feladatátvételi csoport tervezett feladatátvételvégrehajtása a B és c közötti váltáshoz. Most a C szerver lesz az elsődleges és a B - a másodlagos. Az A kiszolgálón lévő összes másodlagos adatbázis automatikusan kapcsolódik a C elsődleges adatbázisaihoz. Az 1.
6.  Dobd el az A szervert. Az A összes adatbázisa automatikusan törlődik.

> [!IMPORTANT]
> A feladatátvételi csoport törlésekor a figyelő végpontjainak DNS-rekordjai is törlődnek. Ezen a ponton nem nulla a valószínűsége annak, hogy valaki más azonos nevű feladatátvételi csoportot vagy kiszolgálóaliast hoz létre, ami megakadályozza, hogy újra használja. A kockázat minimalizálása érdekében ne használjon általános feladatátvételi csoportneveket.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Gyakorlati tanácsok a feladatátvevő csoportok felügyelt példányokkal való használatához

Az automatikus feladatátvételi csoportot az elsődleges példányon kell konfigurálni, és egy másik Azure-régiómásodlagos példányához kell csatlakoztatni.  A példány összes adatbázisa replikálódik a másodlagos példányra.

Az alábbi ábra egy georedundáns felhőalkalmazás felügyelt példány és automatikus feladatátvételi csoport használatával történő tipikus konfigurációját mutatja be.

![automatikus feladatátvétel](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Lásd: [Felügyelt példány hozzáadása feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md) részletes, lépésről lépésre történő oktatóanyagért, amely felügyelt példányt ad hozzá a feladatátvételi csoport használatához.

Ha az alkalmazás felügyelt példányt használ adatrétegként, kövesse az alábbi általános irányelveket az üzletmenet folytonosságának tervezésekor:

### <a name="creating-the-secondary-instance"></a>A másodlagos példány létrehozása 

Annak biztosításához, hogy a feladatátvétel után az elsődleges példány nem megszakított kapcsolat a feladatátvétel után, mind az elsődleges, mind a másodlagos példánynak ugyanabban a DNS-zónában kell lennie. Ez garantálja, hogy ugyanaz a többtartományos (SAN) tanúsítvány használható az ügyfélkapcsolatok hitelesítésére a feladatátvételi csoport két példányának bármelyikével. Amikor az alkalmazás készen áll az éles környezetben, hozzon létre egy másodlagos példányt egy másik régióban, és győződjön meg arról, hogy megosztja a DNS-zónát az elsődleges példánysal. Ezt az Azure Portal, `DNS Zone Partner` a PowerShell vagy a REST API használatával választható paraméter megadásával teheti meg.

> [!IMPORTANT]
> Az alhálózatban létrehozott első példány határozza meg az ugyanazon alhálózat összes további példányának DNS-zónáját. Ez azt jelenti, hogy ugyanattól az alhálózattól származó két példány nem tartozhat különböző DNS-zónákhoz.

A másodlagos példány az elsődleges példányével azonos DNS-zónában történő létrehozásáról további információt a [Másodlagos felügyelt példány létrehozása című témakörben](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance)talál.

### <a name="enabling-replication-traffic-between-two-instances"></a>Replikációs forgalom engedélyezése két példány között

Mivel minden példány a saját virtuális hálózatában van elkülönítve, engedélyezni kell a virtuális hálózatok közötti kétirányú forgalmat. Tekintse meg [az Azure VPN-átjáróját](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Feladatátvételi csoport létrehozása a felügyelt példányok között különböző előfizetésekben

Feladatátvételi csoportot két különböző előfizetésben hozhat létre a felügyelt példányok között. PowerShell API használatakor megteheti a `PartnerSubscriptionId` másodlagos példány paraméterének megadásával. REST API használatakor a `properties.managedInstancePairs` paraméterben szereplő minden példányazonosító saját előfizetési azonosítóval rendelkezhet.
  
> [!IMPORTANT]
> Az Azure Portal nem támogatja a feladatátvételi csoportok létrehozását a különböző előfizetések között. Emellett a különböző előfizetések és/vagy erőforráscsoportok között a meglévő feladatátvételi csoportok esetében a feladatátvétel nem kezdeményezhető manuálisan az elsődleges példányportálon keresztül. Indítsa el a geo-másodlagos példány helyett.

### <a name="managing-failover-to-secondary-instance"></a>Feladatátvétel kezelése a másodlagos példányba

A feladatátvételi csoport kezeli a példány összes adatbázisának feladatátvételét. Csoport létrehozásakor a példány minden adatbázisa automatikusan georeplikálva lesz a másodlagos példányra. Feladatátvevő csoportok nem használhatók az adatbázisok egy részhalmazának részleges feladatátvételének kezdeményezésére.

> [!IMPORTANT]
> Ha egy adatbázist eltávolítanak az elsődleges példányból, akkor a földrajzi másodlagos példányon is automatikusan eldobódik.

### <a name="using-read-write-listener-for-oltp-workload"></a>Írás-olvasásfigyelő használata oltp számítási feladatokhoz

Az OLTP-műveletek végrehajtásakor használja `<fog-name>.zone_id.database.windows.net` a kiszolgáló URL-címét, és a kapcsolatok automatikusan az elsődleges. Ez az URL-cím nem változik a feladatátvétel után. A feladatátvétel magában foglalja a DNS-rekord frissítését, így az ügyfélkapcsolatok csak az ügyfél DNS-gyorsítótárának frissítése után kerülnek átirányításra az új elsődleges rekettre. Mivel a másodlagos példány megosztja a DNS-zónát az elsődlegessel, az ügyfélalkalmazás ugyanazzal a SAN-tanúsítvánnyal tud újra csatlakozni hozzá.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Írásvédett figyelő használata a másodlagos példányhoz való csatlakozáshoz

Ha egy logikailag elkülönített írásvédett számítási feladatok, amely toleráns bizonyos adatfagyás, használhatja a másodlagos adatbázis az alkalmazásban. Közvetlenül a georeplikált másodlagos, `server.secondary.zone_id.database.windows.net` használja a kiszolgáló URL-címét, és a kapcsolat közvetlenül a georeplikált másodlagos.

> [!NOTE]
> Bizonyos szolgáltatási szinteken az Azure SQL Database támogatja az [írásvédett replikák](sql-database-read-scale-out.md) használatát az olvasási feladatok betöltéséhez egy `ApplicationIntent=ReadOnly` csak olvasható replika kapacitásával és a paraméter használatával a kapcsolati karakterláncban. Ha beállított egy georeplikált másodlagos, ezt a lehetőséget, hogy csatlakozzon egy csak olvasható replika az elsődleges helyen vagy a georeplikált helyen.
> - Ha írásvédett kópiához szeretne csatlakozni `<fog-name>.zone_id.database.windows.net`az elsődleges helyen, használja a programot.
> - Ha csak olvasható kópiához szeretne csatlakozni `<fog-name>.secondary.zone_id.database.windows.net`a másodlagos helyen, használja a programot.

### <a name="preparing-for-performance-degradation"></a>Felkészülés a teljesítmény romlására

Egy tipikus Azure-alkalmazás több Azure-szolgáltatást használ, és több összetevőből áll. A feladatátvételi csoport automatikus feladatátvételi aktiválása az Azure SQL-összetevők állapota alapján történik. Az elsődleges régióban lévő egyéb Azure-szolgáltatásokat nem érintheti a szolgáltatáskimaradás, és összetevőik továbbra is elérhetők lehetnek az adott régióban. Miután az elsődleges adatbázisok átváltanak a VÉSZ-régióra, a függő összetevők közötti késés növekedhet. A nagyobb késés nek az alkalmazás teljesítményére gyakorolt hatásának elkerülése érdekében biztosítsa az alkalmazás összes összetevőjének redundanciát a VÉSZ-régióban, és kövesse ezeket a [hálózati biztonsági irányelveket.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Felkészülés az adatvesztésre

Ha egy kimaradás észlelése, SQL automatikusan elindítja az írási és írási feladatátvételt, ha nincs adatvesztés a legjobb tudásunk szerint. Ellenkező esetben a megadott időszakra `GracePeriodWithDataLossHours`vár. Ha a `GracePeriodWithDataLossHours`megadott, készüljön fel az adatvesztésre. Általában a kimaradások során az Azure előnyben részesíti a rendelkezésre állást. Ha nem engedheti meg magának az adatvesztést, győződjön meg arról, hogy a GracePeriodWithDataLossHours-ot megfelelően nagy számra állítja be, például 24 órára.

Az írás-olvasási figyelő DNS-frissítése a feladatátvétel megkezdése után azonnal megtörténik. Ez a művelet nem eredményez adatvesztést. Az adatbázis-szerepkörök közötti váltás azonban normál körülmények között akár 5 percet is igénybe vehet. Amíg el nem készül, az új elsődleges példány egyes adatbázisai továbbra is írásvédettek maradnak. Ha a feladatátvétel t a PowerShell használatával kezdeményezi, a teljes művelet szinkron. Ha az Azure Portal használatával kezdeményezett, a felhasználói felület jelzi a befejezési állapot. Ha a REST API-val kezdeményezett, használja a szabványos Azure Resource Manager lekérdezési mechanizmus befejezésének figyeléséhez.

> [!IMPORTANT]
> A manuális csoport feladatátvétel segítségével helyezze át az elsődleges elemeket az eredeti helyre. Ha a feladatátvételt okozó kimaradás mérséklésre kerül, áthelyezheti az elsődleges adatbázisokat az eredeti helyre. Ehhez kezdeményezze a csoport manuális feladatátvételét.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>A feladatátvételi csoport másodlagos régiójának módosítása

Tegyük fel, hogy az A példány az elsődleges példány, a B példány a meglévő másodlagos példány, és a C példány az új másodlagos példány a harmadik régióban.  Az átmenet végrehajtásához hajtsa végre az alábbi lépéseket:

1.  Hozzon létre C példányt az A-val megegyező és ugyanabban a DNS-zónában. 
2.  Törölje az A és B példányok közötti feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek, mert a feladatátvevő csoport figyelőiNEK SQL-aliasai törlődtek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét. A másodlagos adatbázisok lelesznek választva az elsődleges, és lesz írási és írási adatbázisok. 
3.  Hozzon létre egy feladatátvételi csoportot ugyanazzal a [failover group with managed instance tutorial](sql-database-managed-instance-failover-group-tutorial.md)névvel az A és c példány között. Ez egy adatméret-művelet, és akkor fejeződik be, amikor az A példány összes adatbázisa be van vetve és szinkronizálva van.
4.  Törölje a B példányt, ha nincs szükség a szükségtelen költségek elkerülésére.

> [!NOTE]
> lépés után és amíg a 3.

### <a name="changing-primary-region-of-the-failover-group"></a>A feladatátvételi csoport elsődleges régiójának módosítása

Tételezzük fel, hogy az A példány az elsődleges példány, a B példány a meglévő másodlagos példány, a C példány pedig a harmadik régió új elsődleges példánya.  Az átmenet végrehajtásához hajtsa végre az alábbi lépéseket:

1.  Hozzon létre C példányt a B-vel azonos méretű és ugyanabban a DNS-zónában. 
2.  Csatlakozzon a B-példányhoz, és manuálisan adja át az elsődleges példányt B.A példányra.
3.  Törölje az A és B példányok közötti feladatátvételi csoportot. Ezen a ponton a bejelentkezések sikertelenek lesznek, mert a feladatátvevő csoport figyelőiNEK SQL-aliasai törlődtek, és az átjáró nem ismeri fel a feladatátvételi csoport nevét. A másodlagos adatbázisok lelesznek választva az elsődleges, és lesz írási és írási adatbázisok. 
4.  Hozzon létre egy feladatátvételi csoportot ugyanazzal a névvel az A és c példány között. [failover group with managed instance tutorial](sql-database-managed-instance-failover-group-tutorial.md) Ez egy adatméret-művelet, és akkor fejeződik be, amikor az A példány összes adatbázisa be van vetve és szinkronizálva van.
5.  Törölje az A példányt, ha nincs szükség a szükségtelen költségek elkerülésére.

> [!NOTE] 
> lépés után, és amíg a 4 lépés befejeződik az adatbázisok például A marad védtelen egy katasztrofális hiba az A példány.

> [!IMPORTANT]
> A feladatátvételi csoport törlésekor a figyelő végpontjainak DNS-rekordjai is törlődnek. Ezen a ponton nem nulla a valószínűsége annak, hogy valaki más azonos nevű feladatátvételi csoportot vagy kiszolgálóaliast hoz létre, ami megakadályozza, hogy újra használja. A kockázat minimalizálása érdekében ne használjon általános feladatátvételi csoportneveket.

## <a name="failover-groups-and-network-security"></a>Feladatátvételi csoportok és hálózati biztonság

Egyes alkalmazások esetében a biztonsági szabályok megkövetelik, hogy az adatréteghez való hálózati hozzáférés egy adott összetevőre vagy összetevőkre, például virtuális gépre, webszolgáltatásra stb. Ez a követelmény néhány kihívást jelent az üzletmenet folytonosságának kialakítása és a feladatátvételi csoportok használata szempontjából. Az ilyen korlátozott hozzáférés megvalósításakor vegye figyelembe a következő lehetőségeket.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Feladatátvételi csoportok és virtuális hálózati szabályok használata

Ha virtuális [hálózati szolgáltatás végpontjait és szabályait](sql-database-vnet-service-endpoint-rule-overview.md) használja az SQL-adatbázishoz való hozzáférés korlátozására, vegye figyelembe, hogy minden virtuális hálózati szolgáltatás végpontja csak egy Azure-régióra vonatkozik. A végpont nem teszi lehetővé, hogy más régiók fogadják az alhálózatkommunikációt. Ezért csak az ugyanabban a régióban telepített ügyfélalkalmazások csatlakozhatnak az elsődleges adatbázishoz. Mivel a feladatátvétel eredményeképpen az SQL-ügyfélmunkamenetek egy másik (másodlagos) régióban lévő kiszolgálóra kerülnek átirányítva, ezek a munkamenetek sikertelenek lesznek, ha az adott régión kívüli ügyfélről származnak. Ezért az automatikus feladatátvételi házirend nem engedélyezhető, ha a részt vevő kiszolgálók szerepelnek a virtuális hálózati szabályokban. A manuális feladatátvétel támogatásához kövesse az alábbi lépéseket:

1. Az alkalmazás előtér-összetevőinek redundáns másolatainak kiépítése (webszolgáltatás, virtuális gépek stb.) a másodlagos régióban
2. A [virtuális hálózati szabályok](sql-database-vnet-service-endpoint-rule-overview.md) egyéni konfigurálása az elsődleges és a másodlagos kiszolgálóhoz
3. Az [előtér-feladatátvétel engedélyezése Traffic Manager-konfigurációhasználatával](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Kézi feladatátvétel indítása a kimaradás észlelésekor. Ez a beállítás az okra az alkalmazásokra van optimalizálva, amelyek konzisztens késést igényelnek az előtér és az adatréteg között, és támogatja a helyreállítást, ha az előtér- vagy adatréteg vagy mindkettő hatással van a szolgáltatáskimaradásra.

> [!NOTE]
> Ha csak **olvasható figyelőt** használ egy csak olvasható számítási feladat terheléselosztásához, győződjön meg arról, hogy ez a számítási feladat végrehajtása egy virtuális gép vagy a másodlagos régió más erőforrásában, így csatlakozhat a másodlagos adatbázishoz.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Feladatátvételi csoportok és SQL-adatbázis tűzfalszabályainak használata

Ha az üzletmenet-folytonossági terv feladatátvételt igényel az automatikus feladatátvételű csoportok használatával, korlátozhatja az SQL-adatbázishoz való hozzáférést a hagyományos tűzfalszabályok használatával. Az automatikus feladatátvétel támogatásához kövesse az alábbi lépéseket:

1. [Nyilvános IP létrehozása](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Hozzon létre egy nyilvános terheléselosztót,](../load-balancer/quickstart-load-balancer-standard-public-portal.md) és rendelje hozzá a nyilvános IP-címet.
3. [Virtuális hálózat és virtuális gépek létrehozása az](../load-balancer/quickstart-load-balancer-standard-public-portal.md) előtér-összetevőkhöz
4. [Hozzon létre hálózati biztonsági csoportot,](../virtual-network/security-overview.md) és konfigurálja a bejövő kapcsolatokat.
5. Győződjön meg arról, hogy a kimenő kapcsolatok meg vannak nyitva az Azure SQL-adatbázis számára az "Sql" [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags)használatával.
6. Hozzon létre egy [SQL-adatbázis tűzfalszabályát,](sql-database-firewall-configure.md) amely lehetővé teszi az 1.

A kimenő hozzáférés konfigurálásáról és a tűzfalszabályokban használandó IP-címről a [Kimenő kapcsolatok terheléselosztása](../load-balancer/load-balancer-outbound-connections.md)című témakörben talál további információt.

A fenti konfiguráció biztosítja, hogy az automatikus feladatátvétel nem blokkolja az előtér-összetevők kapcsolatait, és feltételezi, hogy az alkalmazás képes elviselni az előtér és az adatréteg közötti hosszabb késést.

> [!IMPORTANT]
> A regionális kimaradások üzletmenet-folytonosságának biztosításához biztosítania kell a földrajzi redundanciát mind az előtér-összetevők, mind az adatbázisok számára.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Georeplikáció engedélyezése a felügyelt példányok és a virtuális hálózatok között

Ha két különböző régióban állít be feladatátvételi csoportot az elsődleges és a másodlagos felügyelt példányok között, minden példány egy független virtuális hálózat használatával van elkülönítve. A virtuális hálózatok közötti replikációs forgalom engedélyezéséhez győződjön meg arról, hogy ezek az előfeltételek teljesülnek:

1. A két felügyelt példánynak különböző Azure-régiókban kell lennie.
2. A két felügyelt példánynak azonos szolgáltatási szintnek kell lennie, és azonos tárolási mérettel kell rendelkeznie.
3. A másodlagos felügyelt példánynak üresnek kell lennie (nincs enek felhasználói adatbázis).
4. A felügyelt példányok által használt virtuális hálózatokat [VPN-átjárón](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [expressz útvonalon](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)keresztül kell csatlakoztatni. Ha két virtuális hálózat csatlakozik egy helyszíni hálózaton keresztül, győződjön meg arról, hogy nincs tűzfalszabály blokkolja az 5022 és az 11000-11999 portokat. A globális virtuális társviszony-létesítés nem támogatott.
5. A két felügyelt példány virtuális hálózatának nem lehet nek egymást átfedő IP-címe.
6. Be kell állítania a hálózati biztonsági csoportokat (NSG), hogy az 5022-es portok és az 11000~12000 tartomány nyitott bejövő és kimenő kapcsolatok a másik felügyelt példány alhálózatából. Ez lehetővé teszi a példányok közötti replikációs forgalmat.

   > [!IMPORTANT]
   > A helytelenül konfigurált NSG biztonsági szabályok beragadt adatbázis-másolási műveletekhez vezetnek.

7. A másodlagos példány a megfelelő DNS-zónaazonosítóval van konfigurálva. A DNS-zóna egy felügyelt példány és egy virtuális fürt tulajdonsága, és azonosítója szerepel az állomásnévcímben. A zónaazonosító véletlen karakterláncként jön létre, amikor az első felügyelt példány minden virtuális hálózatban létrejön, és ugyanaz az azonosító van hozzárendelve az ugyanazon alhálózat összes többi példányához. A hozzárendelést követően a DNS-zóna nem módosítható. Az ugyanabban a feladatátvételi csoportban szereplő felügyelt példányoknak meg kell osztaniuk a DNS-zónát. Ezt úgy érheti el, hogy a másodlagos példány létrehozásakor átadja az elsődleges példány zónaazonosítóját a DnsZonePartner paraméter értékeként. 

   > [!NOTE]
   > A feladatátvevő csoportok felügyelt példányokkal történő konfigurálásának részletes oktatóanyagát a Felügyelt példány hozzáadása [a feladatátvételi csoporthoz című](sql-database-managed-instance-failover-group-tutorial.md)témakörben ismerteti.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Elsődleges adatbázis frissítése vagy visszaminősítése

Az elsődleges adatbázist frissítheti vagy visszaminősítheti egy másik számítási méretre (ugyanazon a szolgáltatási szinten belül, nem pedig az általános cél és az üzleti szempontból kritikus) anélkül, hogy leválasztaná a másodlagos adatbázisokat. A frissítés során azt javasoljuk, hogy először frissítse az összes másodlagos adatbázist, majd frissítse az elsődleges. Visszaminősítéskor fordítsa meg a sorrendet: először az elsődleges leminősítés, majd az összes másodlagos adatbázis visszaminősítése. Ha frissíti vagy visszaminősíti az adatbázist egy másik szolgáltatási szintre, ez a javaslat érvényesítése.

Ez a szekvencia kifejezetten a probléma elkerülése érdekében, ahol a másodlagos egy alacsonyabb termékváltozatban túlterhelt, és újra kell eletetni egy frissítési vagy visszalépési folyamat során. A probléma elkerülése érdekében az elsődleges csak olvasható, rovására az összes írási és írási számítási feladatok az elsődleges.

> [!NOTE]
> Ha másodlagos adatbázist hozott létre a feladatátvételi csoport konfigurációjának részeként, nem ajánlott a másodlagos adatbázis visszaminősítése. Ez biztosítja, hogy az adatréteg elegendő kapacitással rendelkezzen a feladatátvétel aktiválása után a rendszeres munkaterhelés feldolgozásához.

## <a name="preventing-the-loss-of-critical-data"></a>A kritikus adatok elvesztésének megakadályozása

A nagy kiterjedésű hálózatok nagy késése miatt a folyamatos másolás aszinkron replikációs mechanizmust használ. Az aszinkron replikáció bizonyos adatvesztést elkerülhetetlenné tesz, ha hiba történik. Előfordulhat azonban, hogy egyes alkalmazások nem igényelnek adatvesztést. A kritikus frissítések védelme érdekében az alkalmazásfejlesztő a tranzakció véglegesítése után azonnal felhívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) rendszereljárását. A `sp_wait_for_database_copy_sync` hívás blokkolja a hívó szálat, amíg az utolsó véglegesített tranzakció t el nem küldi a másodlagos adatbázisba. Azonban nem várja meg, hogy a továbbított tranzakciók at visszajátsszák és véglegesítsék a másodlagos oldalon. `sp_wait_for_database_copy_sync`hatóköre egy adott folyamatos másolási hivatkozás. Ezt az eljárást bármely felhasználó meghívhatja, aki kapcsolati jogosultsággal van az elsődleges adatbázishoz.

> [!NOTE]
> `sp_wait_for_database_copy_sync`megakadályozza az adatvesztést a feladatátvétel után, de nem garantálja az olvasási hozzáférés teljes szinkronizálását. Az `sp_wait_for_database_copy_sync` eljáráshívás által okozott késleltetés jelentős lehet, és a hívás időpontjában a tranzakciós napló méretétől függ.

## <a name="failover-groups-and-point-in-time-restore"></a>Feladatátvételi csoportok és időponthoz futó visszaállítás

A feladatátvételi csoportokkal való időponthoz való használatról a Point in Time [Recovery (PITR) című témakörben](sql-database-recovery-using-backups.md#point-in-time-restore)talál további információt.

## <a name="limitations-of-failover-groups"></a>A feladatátvevő csoportok korlátai

Ne feledje, a következő korlátozások:

- Feladatátvételi csoport nem hozható létre két kiszolgáló vagy példány között ugyanabban az Azure-régiókban.
- A feladatátvevő csoport nem nevezhető át. Törölnie kell a csoportot, és újra létre kell hoznia egy másik névvel. 
- Az adatbázis átnevezése nem támogatott a feladatátvételi csoport példányai számára. Az adatbázis átnevezéséhez ideiglenesen törölnie kell a feladatátvételi csoportot.

## <a name="programmatically-managing-failover-groups"></a>Feladatátvételi csoportok programozott kezelése

Ahogy azt korábban már tárgyaltuk, az automatikus feladatátvételi csoportok és az aktív georeplikáció az Azure PowerShell és a REST API használatával is programozott módon kezelhetők. Az alábbi táblázatok a rendelkezésre álló parancsokat ismertetik. Az aktív georeplikáció az Azure Resource Manager API-k készletét tartalmazza a felügyelethez, beleértve az [Azure SQL Database REST API-t](https://docs.microsoft.com/rest/api/sql/) és az Azure [PowerShell-parancsmagokat.](https://docs.microsoft.com/powershell/azure/overview) Ezek az API-k erőforráscsoportok használatát igénylik, és támogatják a szerepköralapú biztonságot (RBAC). A hozzáférési szerepkörök megvalósításáról az [Azure szerepköralapú hozzáférés-vezérléscímű](../role-based-access-control/overview.md)témakörben talál további információt.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>SQL-adatbázis feladatátvétel kezelése egyetlen adatbázissal és rugalmas készletekkel

| Parancsmag | Leírás |
| --- | --- |
| [Új-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Ez a parancs feladatátvételi csoportot hoz létre, és regisztrálja azt az elsődleges és másodlagos kiszolgálókon is|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Feladatátvevő csoport konfigurációjának beolvasása |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Feladatátvevő csoport konfigurációjának módosítja |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Feladatátvételi csoport feladatátvételi eseményindítóia a másodlagos kiszolgálóra |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Egy vagy több adatbázis hozzáadása feladatátvételi csoporthoz|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>SQL-adatbázis feladatátvételi csoportjainak kezelése felügyelt példányokkal

| Parancsmag | Leírás |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Ez a parancs feladatátvételi csoportot hoz létre, és regisztrálja azt mind az elsődleges, mind a másodlagos példányokon|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Feladatátvevő csoport konfigurációjának módosítja|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Feladatátvevő csoport konfigurációjának beolvasása|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Egy feladatátvételi csoport feladatátvételi eseményindítói a másodlagos példánynak|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Feladatátvételi csoport eltávolítása|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>SQL-adatbázis feladatátvétel kezelése egyetlen adatbázissal és rugalmas készletekkel

| Parancs | Leírás |
| --- | --- |
| [az sql feladatátvételi csoport létrehozása](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Ez a parancs feladatátvételi csoportot hoz létre, és regisztrálja azt az elsődleges és másodlagos kiszolgálókon is|
| [az sql feladatátvételi csoport törlése](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [az sql feladatátvételi csoport megjelenítése](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Feladatátvevő csoport konfigurációjának beolvasása |
| [az sql feladatátvételi csoport frissítése](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Módosítja a feladatátvevő csoport konfigurációját, és/vagy hozzáad egy vagy több adatbázist egy feladatátvevő csoporthoz|
| [az sql feladatátvételi csoport set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Feladatátvételi csoport feladatátvételi eseményindítóia a másodlagos kiszolgálóra |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>SQL-adatbázis feladatátvételi csoportjainak kezelése felügyelt példányokkal

| Parancs | Leírás |
| --- | --- |
| [az sql instance-feladatátvételi csoport létrehozása](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Ez a parancs feladatátvételi csoportot hoz létre, és regisztrálja azt mind az elsődleges, mind a másodlagos példányokon |
| [az sql instance-feladatátvételi csoport frissítése](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Feladatátvevő csoport konfigurációjának módosítja|
| [az sql instance-feladatátvételi csoport megjelenítése](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Feladatátvevő csoport konfigurációjának beolvasása|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Egy feladatátvételi csoport feladatátvételi eseményindítói a másodlagos példánynak|
| [az sql instance-feladatátvételi csoport törlése](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Feladatátvételi csoport eltávolítása |

* * *

> [!IMPORTANT]
> Mintaparancsfájlról a [Feladatátvételi csoport konfigurálása és feladatátvétele egyetlen adatbázishoz](scripts/sql-database-add-single-db-to-failover-group-powershell.md)című témakörben található.

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: SQL-adatbázis feladatátvételi csoportjainak kezelése egy- és készletezésű adatbázisokkal

| API | Leírás |
| --- | --- |
| [Feladatátvevő csoport létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Feladatátvételi csoport létrehozása vagy frissítése |
| [Feladatátvevő csoport törlése](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Feladatátvétel (tervezett)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Elindítja a feladatátvételt az aktuális elsődleges kiszolgálóról a másodlagos kiszolgálóra teljes adatszinkronizálással.|
| [Feladatátvétel kényszerítése adatvesztést engedélyezve](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Elindítja a feladatátvételt az aktuális elsődleges kiszolgálóról a másodlagos kiszolgálóra az adatok szinkronizálása nélkül. Ez a művelet adatvesztést okozhat. |
| [Feladatátvételi csoport beszereznie](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | A feladatátvevő csoport konfigurációjának beolvasása. |
| [Feladatátvételi csoportok listázása kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | A kiszolgálófeladat-átvételi csoportok listája. |
| [Feladatátvételi csoport frissítése](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Frissíti a feladatátvevő csoport konfigurációját. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Feladatátvevő csoportok kezelése felügyelt példányokkal

| API | Leírás |
| --- | --- |
| [Feladatátvevő csoport létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Feladatátvevő csoport konfigurációjának létrehozása vagy frissítése |
| [Feladatátvevő csoport törlése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Feladatátvételi csoport eltávolítása a példányból |
| [Feladatátvétel (tervezett)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Elindítja a feladatátvételt az aktuális elsődleges példánytól a teljes adatszinkronizálással rendelkező példányig. |
| [Feladatátvétel kényszerítése adatvesztést engedélyezve](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Elindítja a feladatátvételt az aktuális elsődleges példányról a másodlagos példányra az adatok szinkronizálása nélkül. Ez a művelet adatvesztést okozhat. |
| [Feladatátvételi csoport beszereznie](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | lekéri a feladatátvevő csoport konfigurációját. |
| [Feladatátvevő csoportok listázása – hely szerint listázás](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Egy adott helyen a feladatátvételi csoportok listája. |

## <a name="next-steps"></a>További lépések

- Részletes oktatóanyagokat lásd:
    - [Egyetlen adatbázis hozzáadása feladatátvételi csoporthoz](sql-database-single-database-failover-group-tutorial.md)
    - [Rugalmas készlet hozzáadása feladatátvételi csoporthoz](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Felügyelt példány hozzáadása feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md)
- A mintaparancsfájlok a következő témakörökben láthatók:
  - [Egyetlen adatbázis aktív georeplikációjának konfigurálása a PowerShell használatával az Azure SQL Database-ben](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [A PowerShell használatával aktív georeplikációt konfigurálhat egy készletezett adatbázishoz az Azure SQL Database-ben](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Azure SQL Database egyetlen adatbázis hozzáadása feladatátvételi csoporthoz a PowerShell használatával](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Az üzletmenet folytonosságának áttekintését és forgatókönyveit az [Üzletmenet folytonosságának áttekintése című témakörben találja.](sql-database-business-continuity.md)
- Az Azure SQL Database automatikus biztonsági másolatairól az [SQL Database automatikus biztonsági mentései](sql-database-automated-backups.md)című témakörben olvashat.
- Az automatikus biztonsági mentések helyreállítási használatáról az [Adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági mentések ből című témakörben](sql-database-recovery-using-backups.md)olvashat.
- Az új elsődleges kiszolgáló és adatbázis hitelesítési követelményeiről az [SQL Database biztonsága a vészhelyreállítás után című témakörben](sql-database-geo-replication-security-config.md)olvashat.

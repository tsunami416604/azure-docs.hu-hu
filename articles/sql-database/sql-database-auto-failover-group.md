---
title: Feladatátvételi csoportok – Azure SQL Database |} A Microsoft Docs
description: Automatikus feladatátvételi csoportok az SQL Database szolgáltatás lehetővé teszi, hogy a replikáció és a egy csoport egy SQL Database-kiszolgálón lévő adatbázisokhoz, vagy az összes adatbázis automatikus / koordinált feladatátvétele kezelése a felügyelt példány.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: cf163b2b01b4205a4a3d2123263988998130c42a
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848388"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Automatikus feladatátvételi csoportok segítségével átlátható és koordinált több adatbázis feladatátvételét engedélyezése

Automatikus feladatátvételi csoportok lehetővé teszi az SQL-adatbázis, amely lehetővé teszi, hogy a replikáció és a egy SQL Database-kiszolgálón lévő adatbázisokhoz, vagy egy felügyelt példány (jelenleg nyilvános előzetes verzióban elérhető a felügyelt példány) egy másik régióban található összes adatbázis egy csoport feladatátvétele kezelése. Használja az azonos technológiára [aktív georeplikáció](sql-database-active-geo-replication.md). Manuálisan is kezdeményezhető feladatátvétel, vagy az SQL Database szolgáltatás egy felhasználó által definiált szabályzat alapján lehet delegálni. Az utóbbi lehetőséget lehetővé teszi, hogy automatikusan helyreállítani egy másodlagos régióban több kapcsolódó adatbázisok egy Katasztrofális hiba vagy egyéb nem tervezett esemény, amely az SQL Database szolgáltatás rendelkezésre állása az elsődleges régióban teljes vagy részleges elvesztését eredményezi. Emellett használhatja az olvasható másodlagos adatbázis csak olvasható lekérdezési számítási feladatok kiszervezéséhez. Automatikus feladatátvételi csoportok több adatbázis között, mivel ezeknek az adatbázisoknak az elsődleges kiszolgálón kell konfigurálni. Az adatbázisok a feladatátvételi csoport elsődleges és másodlagos kiszolgálók ugyanabban az előfizetésben kell lennie. Automatikus feladatátvételi csoportok támogatja az összes adatbázis replikálása a csoportban csak egy másodlagos kiszolgáló egy másik régióban.

> [!NOTE]
> Működő önálló vagy készletezett adatbázisok egy SQL Database-kiszolgálóhoz, és szeretné, az azonos vagy eltérő régiókban lévő több másodlagos példány hozható létre, amikor [aktív georeplikáció](sql-database-active-geo-replication.md).

Ha automatikus feladatátvételi csoportok használ az automatikus feladatátvételi szabályzat, bármilyen kimaradásról, amely hatással van egy vagy több, az adatbázisok az automatikus feladatátvételi csoport eredményez. Ezenkívül automatikus feladatátvételi csoportok adja meg az olvasási és írási, és a továbbra is csak olvasási figyelői végpontok feladatátvételek során változatlan marad. Akár manuális vagy automatikus feladatátvételi aktiválási, feladatátvételi minden másodlagos adatbázisát a csoport vált, amennyiben az elsődleges. Az adatbázis-feladatátvétel befejezése után a DNS-rekord automatikusan frissül a végpontok átirányítása az új régióban. Az adott RPO és RTO adatokat, lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

Ha automatikus feladatátvételi csoportok automatikus feladatátvételi szabályzat, bármilyen kimaradásról, amely hatással van az SQL Database server-adatbázisok vagy a felügyelt példányok adatai az automatikus feladatátvételt használ. Automatikus feladatátvételi csoport használata kezelheti:

- Az [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [PowerShell: Feladatátvételi csoport](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [REST API: Feladatátvételi csoport](https://docs.microsoft.com/rest/api/sql/failovergroups).

A feladatátvételt követően ellenőrizze a hitelesítési követelmények, a kiszolgáló és az adatbázis az új elsődleges. További információkért lásd: [vész-helyreállítása után az SQL Database biztonsági](sql-database-geo-replication-security-config.md).

Elérése érdekében a valódi üzleti folytonosság, adatbázis-redundancia adatközpontok között csak a megoldás része. Egy alkalmazás (szolgáltatás) – teljes körű helyreállítása, végzetes hiba van szükség a szolgáltatást alkotó összetevők és minden függő szolgáltatás helyreállítás után. Ezen összetevők közé tartoznak az ügyfélszoftver (például egy egyéni JavaScript böngésző), a webes előtérrendszerek, a storage és a DNS. Rendkívül fontos, hogy az összes összetevő ellenálljanak a azonos hibákat, és a helyreállítási idő célértéke (RTO) az alkalmazás belül elérhetővé válnak. Ezért kell azonosítsa az összes függő szolgáltatást, és megismerheti a garanciák és képességeket biztosítanak. Ezt követően végre kell hajtania megfelelő lépéseket annak érdekében, hogy a szolgáltatások, amelytől a feladatátvétel során a szolgáltatás függvények. Vész-helyreállítási megoldások tervezésével kapcsolatos további információkért lásd: [felhőalapú megoldások tervezéséhez a vész-helyreállítási használatával aktív georeplikáció](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Automatikus feladatátvételi csoport terminológia és képességek

- **Feladatátvételi csoport**

  Egy feladatátvételi csoportot belül egyetlen felügyelt példány átveheti egy másik régióba egységként abban az esetben az összes vagy néhány elsődleges adatbázist az elsődleges régióban leállás miatt elérhetetlenné válik, vagy egy SQL Database-kiszolgáló felügyelt adatbázisok egy csoportja.

  - **SQL Database-kiszolgálók**

     Az SQL Database-kiszolgálók vagy azok egy részét a felhasználói adatbázisok SQL-adatbázis egyetlen kiszolgálón helyezhető egy feladatátvételi csoportot. Egy SQL Database-kiszolgálót is, egy SQL Database-kiszolgálón támogatja a több, feladatátvételi csoportok.

  - **Felügyelt példány**
  
     Felügyelt példány használatával egy feladatátvételi csoportot tartalmazza a felügyelt példány összes felhasználói adatbázishoz, és ezért a felügyelt példány csak támogatja egy egyszeri feladatátvételi csoportot.

- **Elsődleges**

  Az SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz, amelyen az elsődleges adatbázisok a feladatátvételi csoportban.

- **Másodlagos**

  Az SQL Database-kiszolgálóhoz vagy a felügyelt példányhoz, amelyen a másodlagos adatbázisok a feladatátvételi csoportban. A másodlagos és az elsődleges ugyanabban a régióban nem lehet.

- **Önálló adatbázisok hozzáadása a feladatátvételi csoporthoz**

  Is elhelyezhető, több különálló adatbázisok az SQL Database ugyanarra a kiszolgálóra ugyanabban a feladatátvételi csoportban. Ha egy adatbázist a feladatátvételi csoporthoz adja hozzá, automatikusan létrehoz egy másodlagos adatbázis ugyanakkorák edition és a számítási használata a másodlagos kiszolgálón.  A feladatátvételi csoport létrehozásakor megadott kiszolgálón. Ha hozzáad egy adatbázist, amely már rendelkezik egy másodlagos adatbázist a másodlagos kiszolgálón, a georeplikációs hivatkozás örökli a csoport. Amikor hozzáad egy adatbázist, amely már rendelkezik egy másodlagos adatbázis egy kiszolgálót, amely nem a feladatátvételi csoport része, egy új másodlagos jön létre a másodlagos kiszolgálón.
  
> [!IMPORTANT]
  > Felügyelt példány, az összes felhasználói adatbázisban lesznek replikálva. A feladatátvételi csoport nem választhat replikációs felhasználói adatbázisokat egy részét.

- **A rugalmas készletben található adatbázisok hozzáadása a feladatátvételi csoport**

  Is elhelyezhető, az összes vagy több adatbázis egy rugalmas készletben lévő azonos feladatátvételi csoport. Ha az elsődleges adatbázis egy rugalmas készletben, a másodlagos automatikusan létrejön a rugalmas készletben az azonos nevű (másodlagos készlet). Biztosítania kell, hogy a másodlagos kiszolgáló tartalmaz egy azonos pontos nevét és a másodlagos adatbázisok a feladatátvételi csoport által létrehozott üzemeltetésére elegendő szabad kapacitás tartalmazó rugalmas készlet. Ha hozzáadta az adatbázist a készlet, amely már rendelkezik egy másodlagos adatbázist a másodlagos készletben, a georeplikációs hivatkozás örökli a csoport. Amikor hozzáad egy adatbázist, amely már rendelkezik egy másodlagos adatbázis egy kiszolgálót, amely nem a feladatátvételi csoport része, egy új másodlagos a másodlagos készlet jön létre.
  
  - **Feladatátvételi csoport olvasási és írási figyelője**

  Egy DNS CNAME-rekordot, amely az aktuális elsődleges URL-cím formátumú. Lehetővé teszi az olvasási és írási SQL alkalmazásokat az elsődleges változásakor a feladatátvételt követően az elsődleges adatbázis transzparens módon csatlakoznak.

  - **SQL Database-kiszolgáló DNS CNAME-rekordot az olvasási és írási figyelő**

     Egy SQL Database-kiszolgálón, a DNS CNAME-rekordot a feladatátvételi csoport, amely az aktuális elsődleges URL-címre mutat formázott `failover-group-name.database.windows.net`.

  - **Felügyelt példány DNS CNAME rekord olvasási és írási figyelőhöz**

     Felügyelt példány, a DNS CNAME-rekordot a feladatátvételi csoport, amely az aktuális elsődleges URL-címre mutat formázott `failover-group-name.zone_id.database.windows.net`.

- **Feladatátvételi csoport csak olvasható figyelője**

  Egy DNS CNAME-rekordot, amely a csak olvasható figyelő, amely a másodlagos URL-cím formátumú. Lehetővé teszi az átlátható módon csatlakozás a megadott terheléselosztási szabályok használata a másodlagos csak olvasható SQL alkalmazások.

  - **SQL Database-kiszolgáló DNS CNAME-rekordot a csak olvasási figyelői**

     Egy SQL Database-kiszolgálón, a DNS CNAME-rekordot a csak olvasható figyelőhöz, amely a másodlagos URL-címre mutat formázott `failover-group-name.secondary.database.windows.net`.

  - **Felügyelt példány DNS CNAME rekord a csak olvasási figyelői**

     Felügyelt példány, a DNS CNAME-rekordot a csak olvasható figyelőhöz, amely a másodlagos URL-címre mutat formázott `failover-group-name.zone_id.database.windows.net`.

- **Az automatikus feladatátvételi szabályzat**

  Alapértelmezés szerint egy feladatátvételi csoport automatikus feladatátvételi szabályzat van konfigurálva. Az SQL Database szolgáltatás elindítja a feladatátvétel után a hibát észleli, és a türelmi időszak lejárt. A rendszer ellenőriznie kell, hogy a szolgáltatáskiesés megszüntetése után nem szünteti meg a beépített [az SQL Database szolgáltatás magas rendelkezésre állású infrastruktúrák](sql-database-high-availability.md) a méretezési csoport, az ütközés miatt. Ha szeretné szabályozni a feladatátvételi munkafolyamat az alkalmazásból, kikapcsolhatja az automatikus feladatátvételt.

- **Csak olvasási feladatátvételi szabályzat**

  Alapértelmezés szerint le van tiltva a csak olvasási figyelői feladatátvételét. Ez biztosítja, hogy az elsődleges teljesítményének nem változik, ha a másodlagos offline állapotban. Azonban azt is jelenti a csak olvasható munkamenetek nem lesz képes csatlakozni, amíg a másodlagos helyre van. Ha állásidő zavarják meg a csak olvasható munkamenetek és az OK gombra a átmenetileg egyaránt csak olvasható és írható-olvasható forgalomhoz a teljesítménycsökkenés az elsődleges rovására használja az elsődleges, a csak olvasási figyelői a feladatátvételi engedélyezheti. Ebben az esetben a csak olvasható forgalom automatikusan irányítja az elsődleges, ha nem érhető el a másodlagos.

- **Tervezett feladatátvétel**

   Tervezett feladatátvétel előtt az elsődleges szerepre a másodlagos kapcsolók elsődleges és másodlagos adatbázisok közötti teljes szinkronizálást hajt végre. Ez garantálja, hogy nincs adatvesztés. Tervezett feladatátvétel a következő esetekben használja:

  - Éles környezetben vészhelyreállítási (DR) próba végrehajtása, ha az adatok elvesztése nem elfogadható
  - Az adatbázisok áthelyezése egy másik régióba
  - Vissza az adatbázisokat a szolgáltatáskiesés megszüntetése után az elsődleges régió (feladat-visszavétel) problémák elhárításáról.

- **Nem tervezett feladatátvétel**

   Nem tervezett vagy a kényszerített feladatátvétel közvetlenül a másodlagos vált, amennyiben az elsődleges szerepkör minden olyan elsődleges-szinkronizálás nélkül. Ez a művelet adatvesztést eredményez. Nem tervezett feladatátvétel helyreállítási módszerként leállás során használatos az elsődleges nem érhető el. Az eredeti elsődleges újra online állapotba kerül, ha azt automatikusan szinkronizálás nélkül újracsatlakoztatására és egy új másodlagos lesz.

- **Manuális feladatátvétel**

  Feladatátvételi manuálisan is kezdeményezheti tetszőleges időpontban, függetlenül attól, automatikus feladatátvételi konfigurációjának. Automatikus feladatátvételi szabályzat nem történik meg, ha a feladatátvételi csoportban, a másodlagos adatbázisok helyreállításához szükséges manuális feladatátvételt. (A teljes adatszinkronizálás) kényszerített vagy rövid feladatátvételt kezdeményezhet. Az utóbbi felhasználható az elsődleges áthelyezheti a másodlagos régióba. Feladatátvétel befejezése után a DNS-rekordok automatikusan frissülnek az új elsődleges kapcsolat biztosítása érdekében

- **Az adatvesztés a türelmi időszak**

  Az elsődleges és másodlagos adatbázis szinkronizálva van, az aszinkron replikáció használatával, mert a feladatátvétel az adatvesztést eredményezhet. Az automatikus feladatátvételi szabályzat adatvesztés funkciót biztosít az alkalmazás megfelelően testre szabhatja. Konfigurálásával **Leváltó**, szabályozhatja, hogy a rendszer mennyi ideig vár, amely nagy eséllyel eredmény adatvesztés a feladatátvétel kezdeményezése előtt.

- **Több, feladatátvételi csoportok**

  Beállíthatja, hogy több feladatátvételi csoportok ugyanarra a két kiszolgáló feladatátvételi teszteket beosztásának szabályozására. Minden csoport külön feladatait. A több-bérlős alkalmazás rugalmas készletek használja, ha ez a funkció használatával minden egyes készletben található elsődleges és másodlagos adatbázis vegyesen. Ezzel a módszerrel csökkenthető a leállások hatásának fele a bérlők a.

  > [!IMPORTANT]
  > Felügyelt példány nem támogatja a több, feladatátvételi csoportok.
  
## <a name="permissions"></a>Engedélyek
A feladatátvételi csoport keresztül felügyelt [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md). A [SQL Server Közreműködője](../role-based-access-control/built-in-roles.md#sql-server-contributor) szerepkör rendelkezik a szükséges engedélyekkel, feladatátvételi csoportok kezelése. 

### <a name="create-failover-group"></a>Feladatátvételi csoport létrehozása
Hozzon létre egy feladatátvételi csoportot, kell RBAC írási hozzáféréssel az elsődleges és másodlagos kiszolgálókat, és a feladatátvételi csoportban található összes adatbázishoz. A felügyelt példány mind az elsődleges és másodlagos felügyelt példányhoz az RBAC írási hozzáférésre van szüksége, de az egyes adatbázisok engedélyek nem relevánsak el, mivel az egyes felügyelt példányok adatbázisai nem való hozzáadásának vagy eltávolításának a feladatátvételi csoportból. 

### <a name="update-a-failover-group"></a>A feladatátvételi csoport frissítése
RBAC kell egy feladatátvételi csoportot frissítéséhez írási hozzáféréssel a feladatátvételi csoporthoz, és a jelenlegi elsődleges kiszolgáló vagy a felügyelt példány összes adatbázisára.  

### <a name="failover-a-failover-group"></a>Feladatátvételi csoport feladatátvétele
A feladatátvételt egy feladatátvételi csoportot, a feladatátvételi csoport az új elsődleges kiszolgáló RBAC írási hozzáférésre van szükségük, vagy felügyelt példány. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Ajánlott eljárások a feladatátvételi csoportok használata a különálló adatbázisok és rugalmas adatbáziskészletek

Az automatikus feladatátvételi csoportot az elsődleges SQL Database-kiszolgálón kell konfigurálni, és csatlakozzon, a másodlagos SQL-adatbázis-kiszolgáló egy másik Azure-régióban.  A csoportok összes vagy egyes adatbázisok belefoglalhatja ezeket a kiszolgálókat. Az alábbi ábra egy tipikus konfigurációja egy georedundáns felhőalapú alkalmazás több adatbázisok és automatikus feladatátvételi csoport használatával.

![Automatikus feladatátvétel](./media/sql-database-auto-failover-group/auto-failover-group.png)

Amikor egy olyan szolgáltatást tervez az üzletmenet-folytonossági szem előtt, kövesse az alábbi általános irányelveket:

- **Egy vagy több, feladatátvételi csoportok segítségével kezelheti a több adatbázis feladatátvételét**

  Egy vagy több, feladatátvételi csoportok létrehozhatók különböző régióban (az elsődleges és másodlagos kiszolgálók) két kiszolgáló között. Minden csoport egy vagy több adatbázis helyreállított egységként abban az esetben az összes vagy néhány elsődleges adatbázissal az elsődleges régióban leállás miatt elérhetetlenné lehetnek. A feladatátvételi csoport geo-secondary adatbázisra hoz létre, az elsődleges azonos szolgáltatási célt. Ha a feladatátvételi csoporthoz ad hozzá egy meglévő georeplikációs kapcsolatban, győződjön meg arról, a geo-secondary van konfigurálva, az ugyanazon a szolgáltatásszinten és a számítási méret elsődlegesként.

- **Olvasási és írási figyelő OLTP-munkaterhelések esetében használjon**

  OLTP műveletek végrehajtásakor használni `failover-group-name.database.windows.net` kiszolgálóként URL-CÍMÉT és a kapcsolatokat a rendszer automatikusan átirányítja az elsődleges. Az URL-cím nem változtatja meg a feladatátvételt követően. Vegye figyelembe, hogy a feladatátvétel magában foglalja a DNS-bejegyzést, így az ügyfél-kapcsolatokat a rendszer átirányítja az új elsődleges csak akkor, ha az ügyfél DNS-gyorsítótár nem frissítik frissítése.

- **Csak olvasható munkaterhelések esetében használjon csak olvasási figyelői**

  Ha egy logikailag elkülönített csak olvasható számítási feladat, amely tűri az adatok bizonyos frissesség, használhatja a másodlagos adatbázis az alkalmazásban. Csak olvasható munkamenetek használata `failover-group-name.secondary.database.windows.net` mint a kiszolgáló URL-CÍMÉT és a kapcsolat automatikusan irányul, a másodlagos. Emellett ajánlott jelzik a kapcsolati karakterlánc használatával leképezés olvasása **ApplicationIntent = csak olvasható**.

- **Elő kell készíteni a teljesítményoptimalizált teljesítménycsökkenése**

  SQL feladatátvételi döntés független az alkalmazás vagy a használt egyéb szolgáltatások többi részétől. Az alkalmazás "összekeverhetők" egy adott régióban, és néhány, a másik néhány összetevőt. A probléma elkerülése érdekében győződjön meg, hogy a redundáns alkalmazástelepítés, a Vészhelyreállítás régióban található, és kövesse az alábbi [hálózati biztonsági irányelveket](#failover-groups-and-network-security).

  > [!NOTE]
  > A Vészhelyreállítás régióban található az alkalmazás nem rendelkezik használni egy másik kapcsolati karakterláncot.  

- **Adatvesztés előkészítése**

  Egy kimaradás észlelhető, ha az SQL megvárja, amíg az Ön által megadott időtartam **Leváltó**. Az alapértelmezett értéke 1 óra. Ha az adatok elvesztése nem elfogadható, feltétlenül állítson be **Leváltó** elég nagy számú, például a 24 órán keresztül. Használja a manuális csoport feladatátvétele sikertelen vissza a másodlagos helyről az elsődleges.

> [!IMPORTANT]
> Rugalmas készletek 800 vagy kevesebb dtu-k és több mint 250 adatbázisaihoz georeplikációs többek között már tervezett feladatátvételeket problémák merülhetnek fel, és a teljesítménycsökkenést.  Ezeket a problémákat nagyobb valószínűséggel írási igényű számítási feladatokhoz fordulhat elő, ha a georeplikáció végpontok széles körben külön földrajzi hely szerint, vagy több másodlagos végpont az egyes adatbázisok használatakor.  Ezek a problémák tüneteit jelzi, ha a georeplikáció lag idővel növekszik.  A lag segítségével követhetők [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Ha ezeket a problémákat, majd megoldások például készlet dtu-k számának növelésével vagy a georeplikált azonos egy készletben található adatbázisok számának csökkentését.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Ajánlott eljárások a feladatátvételi csoportok használata a felügyelt példány

Az automatikus feladatátvételi csoportot az elsődleges példány kell konfigurálni, és csatlakozzon, a másodlagos példány egy másik Azure-régióban.  A példány található összes adatbázis replikálása a másodlagos példányra. A következő ábra szemlélteti a felügyelt példányt, automatikus feladatátvételi csoport georedundáns felhőalapú alkalmazások egy tipikus konfigurációja.

![Automatikus feladatátvétel](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!IMPORTANT]
> Automatikus feladatátvételi csoportok felügyelt példány jelenleg nyilvános előzetes verzióban.

Ha az alkalmazás felügyelt példány az adatréteg használja, az üzletmenet folytonosságának tervezése során kövesse az alábbi általános irányelveket:

- **A másodlagos példány ugyanazt a DNS-zónát, az elsődleges példány létrehozása**

  Amikor létrejön egy új példányt, egyedi azonosító automatikusan jön létre, mint a DNS-zóna és a példány DNS-név szerepel. A több tartományt (SAN) tanúsítvány esetében ez a példány üzembe van helyezve, a SAN-mezőt formájában `zone_id.database.windows.net`. Ez a tanúsítvány használható a DNS-zónájában egy példányt az ügyfélkapcsolatok hitelesítése céljából. Győződjön meg, hogy az elsődleges példány nem megszakadt kapcsolat a feladatátvételt követően az elsődleges és másodlagos példányok kell lennie DNS-zónájában. Az alkalmazás éles környezet készen áll, amikor egy másodlagos példány létrehozása egy másik régióban, és ellenőrizze, hogy a DNS-zóna közös az elsődleges példánnyal. Ez történik, adjon meg egy `DNS Zone Partner` nem kötelező paraméter, az Azure portal, PowerShell vagy a REST API használatával.

  Ugyanazt a DNS-zónájában, az elsődleges példány a másodlagos példány létrehozásával kapcsolatos további információkért lásd: [feladatátvételi csoportok kezelése a felügyelt példány (előzetes verzió)](#powershell-managing-failover-groups-with-managed-instances-preview).

- **Két példány közötti replikációs forgalom engedélyezése**

  Minden példány van különítve a saját virtuális hálózaton, mert ezek a virtuális hálózatok közötti kétirányú forgalmat engedélyezni kell. Lásd: [Azure VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Kezelheti az egész példány feladatátvétele egy feladatátvételi csoport konfigurálása**

  A feladatátvételi csoport fogja kezelni a példány az összes adatbázis feladatátvételét. Ha egy csoport jön létre, minden egyes adatbázis a példányban lesz automatikusan georeplikált másodlagos példány. Feladatátvételi csoportok nem használhat az adatbázisok egy részének részleges feladatátvétel.

  > [!IMPORTANT]
  > Ha egy adatbázist az elsődleges példány törlődik, azt is a rendszer eldobja automatikusan másodlagos geo-példányon.

- **Olvasási és írási figyelő OLTP-munkaterhelések esetében használjon**

  OLTP műveletek végrehajtásakor használni `failover-group-name.zone_id.database.windows.net` kiszolgálóként URL-CÍMÉT és a kapcsolatokat a rendszer automatikusan átirányítja az elsődleges. Az URL-cím nem változtatja meg a feladatátvételt követően. A feladatátvétel magában foglalja a DNS-rekord frissítése, ezért az ügyfélkapcsolatok a rendszer átirányítja az új elsődleges csak akkor, ha az ügyfél DNS-gyorsítótár nem frissítik. Mivel a másodlagos példány közös az elsődleges DNS-zónát, az ügyfélalkalmazás lesz képes újra csatlakozni a SAN tanúsítvány használatával.

- **Közvetlen csatlakozás a georeplikált másodlagos csak olvasási lekérdezések**

  Ha egy logikailag elkülönített csak olvasható számítási feladat, amely tűri az adatok bizonyos frissesség, használhatja a másodlagos adatbázis az alkalmazásban. Közvetlen csatlakozás a georeplikált másodlagos, használja a `server.secondary.zone_id.database.windows.net` mint a kiszolgáló URL-CÍMÉT és a kapcsolat jön létre közvetlenül a georeplikált másodlagos.

  > [!NOTE]
  > Az egyes szolgáltatásszintek után az Azure SQL Database használatát támogatja [csak olvasható replikák](sql-database-read-scale-out.md) terheléselosztása csak olvasható lekérdezési számítási feladatok egy csak olvasható replika kapacitását, valamint használatához betölteni a `ApplicationIntent=ReadOnly` paraméter a kapcsolat karakterlánc. Egy georeplikált másodlagos van beállítva, amikor ez a funkció használatával csatlakozni vagy egy csak olvasható replika az elsődleges helyen, illetve a georeplikált helyre.
  > - Csak olvasható replika az elsődleges helyen csatlakozni, használja `failover-group-name.zone_id.database.windows.net`.
  > - A másodlagos helyen csak olvasható replika csatlakozni, használja `failover-group-name.secondary.zone_id.database.windows.net`.

- **Elő kell készíteni a teljesítményoptimalizált teljesítménycsökkenése**

  SQL feladatátvételi döntés független az alkalmazás vagy a használt egyéb szolgáltatások többi részétől. Az alkalmazás "összekeverhetők" egy adott régióban, és néhány, a másik néhány összetevőt. A probléma elkerülése érdekében győződjön meg, hogy a redundáns alkalmazástelepítés, a Vészhelyreállítás régióban található, és kövesse az alábbi [hálózati biztonsági irányelveket](#failover-groups-and-network-security).

- **Adatvesztés előkészítése**

  Egy kimaradás észlelhető, ha SQL automatikusan aktiválja írási-olvasási feladatátvételi, a legjobb, az ismeretek adatvesztés esetén. Ellenkező esetben megvárja az Ön által megadott időszakban `GracePeriodWithDataLossHours`. Ha a megadott `GracePeriodWithDataLossHours`, elő kell készíteni az adatvesztést a. Leállás, általában az Azure rendelkezésre állási periodicitásának. Ha adatvesztés nem veszhet, feltétlenül állítson be Leváltó elég nagy számú, például a 24 órán keresztül.

  Az olvasási és írási figyelő DNS frissítése történik meg azonnal a feladatátvétel kezdeményezése után. Ez a művelet nem adatok elvesztését eredményezi. A folyamat az adatbázis-szerepkörök közötti váltás viszont normál körülmények között akár 5 percig is eltarthat. Amíg az befejeződik, az új elsődleges példány az egyes adatbázisok is csak olvasható. Ha a feladatátvétel indításáig a teljes művelet szinkron válik a PowerShell használatával. Ha az Azure portal használatával elindította azt a felhasználói felület befejezési állapotát jelzik. Ha kezdeményezte a REST API-val, használja a szabványos Azure Resource Manager a lekérdezési mechanizmust, amellyel befejezésének figyeléséhez.

  > [!IMPORTANT]
  > Manuális csoport feladatátvétele használatával elsődleges lépjen vissza az eredeti helyre. A szolgáltatáskimaradás, a feladatátvétel okozó teljesítményköltségeket csökkenti, ha az elsődleges adatbázisok továbbléphet az eredeti helyre. Ehhez a csoport manuális feladatátvételét kell kezdeményezheti.

## <a name="failover-groups-and-network-security"></a>Feladatátvételi csoportok és hálózati biztonság

Egyes alkalmazások, a biztonsági szabályok követelnek meg, hogy a hálózati hozzáférést az adatréteg korlátozott-e egy adott összetevőre vagy az összetevők, például egy virtuális Gépet a web service stb. Ez a követelmény az üzleti folytonossági terv áttekinthet néhány problémát, és a feladatátvételi csoportok használatát mutatja be. Az ilyen korlátozott hozzáférés végrehajtása során vegye figyelembe a következő beállításokat.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Feladatátvételi csoportok és a virtuális hálózati szabályok használata

Ha használ [virtuális hálózati Szolgáltatásvégpontok és szabályok](sql-database-vnet-service-endpoint-rule-overview.md) való hozzáférés korlátozása az SQL database, vegye figyelembe, hogy mindegyik virtuális hálózati szolgáltatásvégpont csak egy Azure-régióra vonatkozik. A végpont nem engedélyezi a más régiókban az alhálózat-kommunikációt fogad. Ezért csak az ugyanabban a régióban üzembe helyezett ügyfélalkalmazások az elsődleges adatbázishoz csatlakozhat. A feladatátvétel folyamatban van, a kiszolgáló (másodlagos) más régiókban található hiba az SQL ügyfélmunkameneteket eredményez, mivel ezek a munkamenetek sikertelen lesz, ha egy adott régión kívül ügyfél származik. Éppen ezért az automatikus feladatátvételi szabályzat nem lehet engedélyezni, ha a virtuális hálózati szabályok a résztvevő kiszolgálókat tartalmazza. Manuális feladatátvétel támogatása érdekében kövesse az alábbi lépéseket:

1. Az alkalmazás a másodlagos régió (webszolgáltatás, virtuális gépek stb.) az előtér-kiszolgálói összetevők redundáns másolatait kiépítése
2. Konfigurálja a [virtuális hálózati szabályok](sql-database-vnet-service-endpoint-rule-overview.md) külön-külön az elsődleges és másodlagos kiszolgáló
3. Engedélyezze a [előtér-feladatátvétel a használatával egy Traffic manager konfigurációja](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Kézi feladatátvételt kezdeményezzen a szolgáltatáskimaradás elhárítása után észlelése esetén. Ez a beállítás az alkalmazások konzisztens késésű az előtér- és az adatszint között van optimalizálva, és a helyreállítási támogatja, ha az előtérbeli, adatrétegbeli vagy mindkettő negatív hatással van a szolgáltatáskiesés megszüntetése után.

> [!NOTE]
> Ha használja a **csak olvasási figyelői** terheléselosztásához egy csak olvasható munkaterhelés, ellenőrizze, hogy, hogy ezt a munkafolyamatot végrehajtja a virtuális gép vagy egyéb erőforrásokat a másodlagos régióban, így, hogy csatlakozhassanak a másodlagos adatbázis.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Feladatátvételi csoportok és az SQL database-tűzfalszabályok használatával

Ha az üzletmenet folytonosságát biztosító terve van szüksége a feladatátvételi csoportok használatával automatikus feladatátvétellel, hozzáférés az SQL Database, a hagyományos tűzfalszabályok használatával korlátozhatja.  Automatikus feladatátvétel támogatása érdekében kövesse az alábbi lépéseket:

1. [Hozzon létre egy nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Nyilvános load balancer létrehozása](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) és rendelje hozzá a nyilvános IP-cím.
3. [Hozzon létre egy virtuális hálózat és a virtuális gépek](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) az előtér-összetevők
4. [Hálózati biztonsági csoport létrehozása](../virtual-network/security-overview.md) és konfigurálja a bejövő kapcsolatokat.
5. Győződjön meg arról, hogy a kimenő kapcsolatok meg nyitva, az Azure SQL database "Sql" használatával [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags).
6. Hozzon létre egy [SQL database-tűzfalszabályt](sql-database-firewall-configure.md) az 1. lépésben létrehozott nyilvános IP-címről érkező forgalom engedélyezéséhez.

A kimenő hozzáférés konfigurálása és milyen IP-címet használja a tűzfalszabályok kapcsolatos további információkért lásd: [betölteni a terheléselosztó kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md).

A fenti konfigurációs biztosítja, hogy az Automatikus feladatátvétel nem blokkolja a kapcsolatokat az előtér-kiszolgálói összetevők, és feltételezi, hogy az alkalmazás működését a hosszabb késéssel az előtér- és az adatszint között.

> [!IMPORTANT]
> A regionális üzemkimaradások utáni helyreállításon üzleti folytonosság biztosítása érdekében biztosítania kell a földrajzi redundancia előtér-kiszolgálói összetevők mind az adatbázisokat.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Felügyelt példányok és a virtuális hálózatok közötti georeplikációs engedélyezése

A feladatátvételi csoportok között két különböző régióban lévő elsődleges és másodlagos felügyelt példányok beállításakor minden példány el különítve egy független virtuális hálózat használatával. Ezek kövesse biztosítása virtuális hálózatok közötti replikációs forgalom engedélyezésére az előfeltételek teljesülnek:

1. A felügyelt példányok kell lennie a különböző Azure-régióban.
2. A másodlagos kell lennie (nincs felhasználói adatbázis) üres.
3. Az elsődleges és másodlagos felügyelt példányok kell ugyanabban az erőforráscsoportban.
4. A virtuális hálózatok, a felügyelt példányok részét képező keresztül csatlakozniuk kell egy [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md). Globális virtuális társhálózatok létesítése nem támogatott.
5. Felügyelt példány két Vnet nem átfedő IP-címek.
6. A hálózati biztonsági csoportok (NSG) ilyen, amely 5022-es port és a tartomány 11000 beállítása kell ~ 12000 alhálózatról a többi felügyelt Instanced nyissa meg a kimenő és bejövő kapcsolatok. Ez az, hogy a példányok közötti replikációs forgalom

    > [!IMPORTANT]
    > Helytelenül van konfigurálva az NSG biztonsági szabályai vezet elakadt adatbázis-másolási műveletek.

7. Másodlagos példány DNS-zóna partner konfigurálni kell. DNS-zóna egy tulajdonságot egy felügyelt példány. Ez azt jelenti, hogy a gazdagép nevét, amely a következő felügyelt példány nevét, és megelőzi részét a `.database.windows.net` előtag. Véletlenszerű karakterlánc létrehozása az első felügyelt példány minden virtuális hálózat létrehozása során. A DNS-zónát a felügyelt példány létrehozása után nem módosítható, és ugyanazon az alhálózaton belül az összes felügyelt példányok megoszthatja az ugyanazon DNS-zóna értéket. Az elsődleges felügyelt példány és a másodlagos felügyelt példány felügyelt példány feladatátvételi csoport beállítása, a DNS-zóna azonos értéket kell osztania. Ehhez a DnsZonePartner paraméter megadásával, a másodlagos felügyelt példány létrehozásakor. A DNS-zóna partner tulajdonság a felügyelt példány megosztására egy feladatátvételi csoport az határozza meg. Megadásával az erőforrás-azonosító egy másik felügyelt példány DnsZonePartner bemeneteként, a felügyelt példány létrehozása folyamatban örökli a partner a felügyelt példánynak azonos DNS zónához értékét.

## <a name="upgrading-or-downgrading-a-primary-database"></a>A frissítés, vagy egy elsődleges adatbázis alacsonyabb szolgáltatásszintre

Frissítés vagy Visszaléptetés a különböző számítási méret (belül ugyanazon a szolgáltatásszinten, nem az általános célú és a kritikus fontosságú üzleti között) az elsődleges adatbázis bármely másodlagos adatbázisok leválasztása nélkül is. A frissítéskor, azt javasoljuk, hogy először frissítse az összes másodlagos adatbázist, és utána frissítse az elsődleges. Ha alacsonyabb szolgáltatásszintre, fordított sorrendben: először alacsonyabbra az elsődleges, majd a majd mind a másodlagos adatbázisok. Frissítésekor vagy eltérő szolgáltatási réteg az adatbázisról, a javaslat lép érvénybe.

Ez a sorozat ajánlott kifejezetten elkerülheti a problémát, ahol alacsonyabb Termékváltozat, a másodlagos lekérdezi-e túlterhelve, és a egy alacsonyabb szintű vagy a frissítés során újra előkészített kell lennie. Azáltal, hogy az elsődleges csak olvasható, az összes olvasási és írási számítási feladatokat az elsődleges érintő rovására is sikerült elkerülheti a problémát. 

> [!NOTE]
> Ha másodlagos adatbázis nem javasoljuk, hogy a másodlagos adatbázisról, a feladatátvételi csoport konfigurációjának részeként hozta létre. Ez azért szükséges az adatszint rendelkezzen elegendő kapacitással a normál számítási feladatok feldolgozásához a feladatátvétel aktiválása után.

## <a name="preventing-the-loss-of-critical-data"></a>Megakadályozza a fontos adatok elvesztését

A nagy kiterjedésű hálózaton magas késést, mert a folyamatos másolás használ egy aszinkrón replikációs mechanizmus. Az aszinkron replikáció révén kis mértékű adatvesztést elkerülhetetlen, ha hiba történik. Egyes alkalmazások azonban szükség lehet adatvesztés nélkül. Ezek a kritikus frissítések védelme érdekében a alkalmazásfejlesztő meghívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) közvetlenül a tranzakció véglegesítése után a rendszer eljárást. Hívó **sp_wait_for_database_copy_sync** blokkolja a hívó szálat, amíg a másodlagos adatbázisra továbbítva lett az utolsó véglegesített tranzakció. Azt azonban nem vár a továbbított játssza vissza és a másodlagos véglegesített tranzakciók. **az sp_wait_for_database_copy_sync** hivatkozás egy adott folyamatos másolása hatókörét. A kapcsolódási jogok az elsődleges adatbázissal rendelkező bármelyik felhasználó meghívhatja ezt az eljárást.

> [!NOTE]
> **az sp_wait_for_database_copy_sync** megakadályozza az adatvesztést a feladatátvételt követően, de nem garantálja a teljes szinkronizálás olvasási hozzáféréssel. A késleltetés által okozott egy **sp_wait_for_database_copy_sync** eljáráshívási is lehet, és a hívás idején a tranzakciós napló méretétől függ.

## <a name="failover-groups-and-point-in-time-restore"></a>Feladatátvételi csoportok és időponthoz visszaállítás

A feladatátvételi csoportok időponthoz visszaállítással kapcsolatos további információkért lásd: [mutasson az idő Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Feladatátvételi csoportok szoftveres kezelése

Ahogy korábban tárgyalt, automatikus feladatátvételi csoportok és az aktív georeplikáció is kezelhetők programozott módon az Azure PowerShell és a REST API használatával. Az alábbi táblázatok ismertetik az elérhető parancsok. Aktív georeplikáció tartalmaz egy, az Azure Resource Manager API-k Management, beleértve a [Azure SQL Database REST API-t](https://docs.microsoft.com/rest/api/sql/) és [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview). Ezekkel az API-erőforráscsoportok használatát, és támogatja a szerepköralapú biztonság (RBAC). Szerepkörök hozzáférést megvalósításával további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: Az SQL database feladatátvételi az önálló adatbázisok és rugalmas készletek kezelése

| Parancsmag | Leírás |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és másodlagos kiszolgálók|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | A feladatátvételi csoport eltávolítása a kiszolgálóról, és törli az összes másodlagos adatbázist tartalmaz a csoport |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | A feladatátvételi csoport konfigurációjának beolvasása |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |A feladatátvételi csoport konfigurációjának módosítása |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | A másodlagos kiszolgáló feladatátvételi csoport feladatátvétele eseményindítók |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Egy Azure SQL Database feladatátvételi csoportot ad hozzá egy vagy több adatbázis|
|  | |

> [!IMPORTANT]
> Mintaparancsprogram lásd [konfigurálása és a egy önálló adatbázis feladatátvételi csoport feladatátvétele](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md).
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell: Feladatátvételi csoportok kezelése a felügyelt példány (előzetes verzió)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>Telepítse a PowerShell legújabb előzetes verziója

1. A PowerShellGet-modul frissítéséhez 1.6.5 (vagy a legújabb előzetes verzió). Lásd: [PowerShell preview-hely](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

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

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>Egy feladatátvételi csoport létrehozása PowerShell-parancsmagok

| API | Leírás |
| --- | --- |
| New-AzureRmSqlDatabaseInstanceFailoverGroup |Ez a parancs létrehoz egy feladatátvételi csoportot, és regisztrálja azt az elsődleges és másodlagos kiszolgálók|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |A feladatátvételi csoport konfigurációjának módosítása|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |A feladatátvételi csoport konfigurációjának beolvasása|
| Switch-AzureRmSqlDatabaseInstanceFailoverGroup |A másodlagos kiszolgáló feladatátvételi csoport feladatátvétele eseményindítók|
| Remove-AzureRmSqlDatabaseInstanceFailoverGroup | A feladatátvételi csoport eltávolítása|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Az SQL database feladatátvételi csoportok egyetlen vagy készletezett adatbázisok kezelése

| API | Leírás |
| --- | --- |
| [Hozzon létre vagy feladatátvételi csoport frissítése](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Létrehoz vagy frissít egy feladatátvételi csoportot |
| [Feladatátvételi csoport törlése](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | A feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Feladatátvétel (tervezett)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Átadja a feladatokat az aktuális elsődleges kiszolgálóról a kiszolgálóhoz. |
| [Kényszerített feladatátvétel adatvesztés engedélyezése](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |ails keresztül az aktuális elsődleges kiszolgálóról a kiszolgálóhoz. Ez a művelet adatvesztést okozhat. |
| [Feladatátvételi csoport lekérése](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | A feladatátvételi csoport lekérése. |
| [Kiszolgáló feladatátvételi csoportok listázása](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | A kiszolgálón a feladatátvételi csoportok listája. |
| [Feladatátvételi csoport](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Frissíti egy feladatátvételi csoportot. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>REST API: Feladatátvételi csoportok kezelése a felügyelt példány (előzetes verzió)

| API | Leírás |
| --- | --- |
| [Hozzon létre vagy feladatátvételi csoport frissítése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Létrehoz vagy frissít egy feladatátvételi csoportot |
| [Feladatátvételi csoport törlése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | A feladatátvételi csoport eltávolítása a kiszolgálóról |
| [Feladatátvétel (tervezett)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Átadja a feladatokat az aktuális elsődleges kiszolgálóról a kiszolgálóhoz. |
| [Kényszerített feladatátvétel adatvesztés engedélyezése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |ails keresztül az aktuális elsődleges kiszolgálóról a kiszolgálóhoz. Ez a művelet adatvesztést okozhat. |
| [Feladatátvételi csoport lekérése](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | A feladatátvételi csoport lekérése. |
| [Feladatátvételi csoportok listázása - lista hely szerint](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Egy helyen a feladatátvételi csoportok listája. |

## <a name="next-steps"></a>További lépések

- Mintaszkriptek lásd:
  - [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Készletezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Önálló adatbázis feladatátvételi csoportjának konfigurálása és feladatainak átvétele](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md)
- További információ az Azure SQL Database automatikus biztonsági mentések, lásd: [SQL-adatbázis automatikus biztonsági mentések](sql-database-automated-backups.md).
- Az automatikus biztonsági másolatokból helyreállítási kapcsolatos további információkért lásd: [adatbázis visszaállítása a szolgáltatás által létrehozott biztonsági másolatokból](sql-database-recovery-using-backups.md).
- Hitelesítési követelmények, egy új elsődleges kiszolgáló és az adatbázis kapcsolatos további információkért lásd: [vész-helyreállítása után az SQL Database biztonsági](sql-database-geo-replication-security-config.md).

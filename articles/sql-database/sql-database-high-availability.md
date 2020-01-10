---
title: Magas rendelkezésre állás
description: Ismerkedjen meg a Azure SQL Database szolgáltatás magas rendelkezésre állási képességeivel és funkcióival
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: b560cee23855d1c0e8a7b3c2cb9d82c184a1ebf6
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732399"
---
# <a name="high-availability-and-azure-sql-database"></a>Magas rendelkezésre állás és Azure SQL Database

A Azure SQL Database magas rendelkezésre állású architektúrájának célja annak biztosítása, hogy az adatbázis legalább 99,99%-ban rendelkezésre álljon (a különböző szintekre vonatkozó SLA-ra vonatkozó további információkért tekintse meg az SLA-t [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/)), és ne aggódjon a karbantartási műveletek és kimaradások következményeivel. Az Azure automatikusan kezeli a kritikus karbantartási feladatokat, például a javításokat, a biztonsági mentéseket, a Windows-és az SQL-frissítéseket, valamint a nem tervezett eseményeket, például a mögöttes hardvereket, szoftvereket vagy hálózati hibákat.  Ha az alapul szolgáló SQL-példány javított vagy feladatátvételt végez, az állásidő nem észlelhető, ha az alkalmazásban [újrapróbálkozási logikát alkalmaz](sql-database-develop-overview.md#resiliency) . Azure SQL Database a legfontosabb körülmények között is gyorsan helyreállítható, így biztosítva, hogy az adatok mindig elérhetők legyenek.

A magas rendelkezésre állású megoldás úgy van kialakítva, hogy a hibák miatt soha ne vesszenek el az véglegesített adatok, hogy a karbantartási műveletek ne befolyásolják a munkaterhelést, és hogy az adatbázis nem lesz egyetlen meghibásodási pont a szoftver architektúrájában. Nincsenek olyan karbantartási időszakok vagy állásidők, amelyeknek az adatbázis frissítése vagy karbantartása közben le kell állítania a munkaterhelést. 

Két magas rendelkezésre állású építészeti modell van használatban a Azure SQL Databaseban:

- A standard rendelkezésre állási modell, amely a számítás és a tárolás elkülönítésén alapul.  A távoli tárolási rétegek magas rendelkezésre állása és megbízhatósága alapján működik. Ez az architektúra olyan költségvetési alapú üzleti alkalmazásokat céloz meg, amelyek bizonyos teljesítménybeli romlást tudnak elviselni a karbantartási tevékenységek során.
- Prémium rendelkezésre állási modell, amely az adatbázismotor folyamatainak egy fürtjén alapul. Arra támaszkodik, hogy a rendelkezésre álló adatbázismotor-csomópontok kvóruma mindig fennáll. Ez az architektúra olyan kritikus fontosságú alkalmazásokat céloz meg, amelyek magas i/o-teljesítménnyel, magas tranzakciós sebességgel rendelkeznek, és a karbantartási tevékenységek során minimális teljesítményt nyújtanak a számítási feladathoz.

A Azure SQL Database a SQL Server adatbázismotor és a Windows operációs rendszer legújabb stabil verzióján fut, és a legtöbb felhasználó nem észleli, hogy a frissítések folyamatosan vannak végrehajtva.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Alapszintű, standard és általános célú szolgáltatási szint rendelkezésre állása

Ezek a szolgáltatási szintek a standard rendelkezésre állási architektúrát használják. Az alábbi ábra négy különböző csomópontot mutat be a elkülönített számítási és tárolási rétegekkel együtt.

![A számítás és a tárolás elkülönítése](media/sql-database-high-availability/general-purpose-service-tier.png)

A standard rendelkezésre állási modell két réteget tartalmaz:

- Az `sqlservr.exe` folyamatot futtató állapot nélküli számítási réteg, amely csak átmeneti és gyorsítótárazott adatokból áll, például a TempDB, a csatolt SSD modell-adatbázisainak, valamint a gyorsítótár, a puffer-készlet és a oszlopcentrikus-készletnek a memóriában történő megtervezésére. Ezt az állapot nélküli csomópontot az Azure Service Fabric üzemelteti, amely inicializálja `sqlservr.exe`, szabályozza a csomópont állapotát, és szükség esetén feladatátvételt hajt végre egy másik csomóponton.
- Az Azure Blob Storage-ban tárolt adatbázis-fájlokkal (. MDF/. ldf) rendelkező állapot-nyilvántartó adatréteg. Az Azure Blob Storage beépített adatelérhetőségi és redundancia-funkcióval rendelkezik. Ez garantálja, hogy az adatfájlban lévő naplófájl vagy oldal minden rekordja megmarad, még akkor is, ha SQL Server folyamat összeomlik.

Ha az adatbázismotor vagy az operációs rendszer frissítése megtörtént, vagy hiba észlelhető, az Azure Service Fabric az állapot nélküli SQL Server folyamatot egy másik, elegendő szabad kapacitással rendelkező állapot nélküli számítási csomópontra helyezi át. Az áthelyezés nem érinti az Azure Blob Storage-ban tárolt adatátvitelt, és az adatfájlok és naplófájlok az újonnan inicializált SQL Server folyamathoz vannak csatolva. Ez a folyamat garantálja az 99,99%-os rendelkezésre állást, de a nagy számítási feladatok némi teljesítménybeli romlást tapasztalhatnak az átállás során, mivel az új SQL Server példány a hideg gyorsítótárral kezdődik.

## <a name="premium-and-business-critical-service-tier-availability"></a>Prémium és üzletileg kritikus szolgáltatási szint rendelkezésre állása

A prémium és üzletileg kritikus szolgáltatási szintek kihasználják a prémium rendelkezésre állási modellt, amely egyesíti a számítási erőforrásokat (SQL Server adatbázismotor-folyamatot) és a tárterületet (helyileg csatlakoztatott SSD) egyetlen csomóponton. A magas rendelkezésre állást úgy érheti el, hogy a számítási és a tárolási tárterületet további csomópontokra replikálja, amelyek három-négy csomópontos fürtöt hoznak létre. 

![Az adatbázismotor csomópontjainak fürtje](media/sql-database-high-availability/business-critical-service-tier.png)

Az alapul szolgáló adatbázisfájlok (. MDF/. ldf) a csatlakoztatott SSD-tárolóba kerülnek, így nagyon kis késleltetésű IO-t biztosítanak a számítási feladatok számára. A magas rendelkezésre állás a SQL Server [Always On rendelkezésre állási csoportokhoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)hasonló technológia használatával valósítható meg. A fürt egyetlen elsődleges replikát (SQL Server folyamatot) tartalmaz, amely elérhető az írási és olvasási ügyfelek számítási feladataihoz, és legfeljebb három másodlagos replikát (számítás és tárolás), amely az adatok másolatait tartalmazza. Az elsődleges csomópont folyamatosan leküldi a változásokat a másodlagos csomópontokra, és gondoskodik arról, hogy az egyes tranzakciók véglegesítése előtt legalább egy másodlagos replikához szinkronizálja az adatokat. Ez a folyamat garantálja, hogy ha az elsődleges csomópont valamilyen okból összeomlik, mindig van egy teljesen szinkronizált csomópont a feladatátvételhez. A feladatátvételt az Azure Service Fabric kezdeményezi. Miután a másodlagos replika az új elsődleges csomópont lesz, létrejön egy másik másodlagos replika, amely biztosítja, hogy a fürt elegendő csomóponttal (kvórum készlettel) rendelkezik. A feladatátvétel befejezése után a rendszer automatikusan átirányítja az SQL-kapcsolatokat az új elsődleges csomópontra.

További előnyként a prémium rendelkezésre állási modell lehetővé teszi a csak olvasási SQL-kapcsolatok átirányítását az egyik másodlagos replikára. Ezt a funkciót az [olvasási Felskálázásnak](sql-database-read-scale-out.md)nevezzük. A szolgáltatás 100%-os további számítási kapacitást biztosít külön díj nélkül a kikapcsolt írásvédett műveletekhez, például az analitikai számítási feladatokhoz az elsődleges replikából.

## <a name="hyperscale-service-tier-availability"></a>Nagy kapacitású szolgáltatási rétegek rendelkezésre állása

Az nagy kapacitású szolgáltatási rétegek architektúrája az [elosztott függvények architektúrája](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture)című témakörben található. 

![Nagy kapacitású funkcionális architektúra](./media/sql-database-hyperscale/hyperscale-architecture.png)

A nagy kapacitású rendelkezésre állási modellje négy réteget tartalmaz:

- Az `sqlservr.exe` folyamatokat futtató állapot nélküli számítási réteg, amely csak átmeneti és gyorsítótárazott adatokkal rendelkezik, például a nem a RBPEX cache, a TempDB, a Model Database stb., a csatlakoztatott SSD esetében, valamint a gyorsítótár, a puffer készlet és a oszlopcentrikus-készlet megtervezése a memóriában. Ez az állapot nélküli réteg tartalmazza az elsődleges számítási replikát és opcionálisan számos másodlagos számítási replikát, amelyek feladatátvételi célként szolgálhatnak.
- A Page Servers által létrehozott állapot nélküli tárolási réteg. Ez a réteg a számítási replikán futó `sqlservr.exe` folyamatok elosztott tárolási motorja. Minden oldal-kiszolgáló csak átmeneti és gyorsítótárazott adatmennyiséget tartalmaz, például a csatlakoztatott SSD-RBPEX gyorsítótárát, valamint a memóriában gyorsítótárazott adatlapokat. Az egyes lapozófájlok egy aktív-aktív konfigurációban található párosított kiszolgálóoldali kiszolgálóval biztosítják a terheléselosztást, a redundanciát és a magas rendelkezésre állást.
- Egy állapot-nyilvántartó tranzakciós napló tárolási rétege, amely a naplózási szolgáltatás folyamatát, a tranzakciós napló lejárati zónáját és a tranzakciónapló hosszú távú tárolását futtató számítási csomópont által lett létrehozva. A kirakodási zóna és a hosszú távú tárolás az Azure Storage-t használja, amely rendelkezésre állást és [redundanciát](https://docs.microsoft.com/azure/storage/common/storage-redundancy) biztosít a tranzakciónapló számára, és biztosítja az adattartósságot a Véglegesített tranzakciók esetében
- Állapot-nyilvántartó adattárolási réteg, amely az Azure Storage-ban tárolt adatbázisfájlok (. MDF/. ndf) használatával frissül. Ez a réteg az Azure Storage adatelérhetőségi és [redundancia](https://docs.microsoft.com/azure/storage/common/storage-redundancy) -funkcióit használja. Ez garantálja, hogy az adatfájlok minden lapja megmaradjon, még akkor is, ha a nagy kapacitású-architektúra összeomlik, vagy ha a számítási csomópontok sikertelenek lesznek.

Az összes nagy kapacitású-rétegben futtatott számítási csomópontok az Azure Service Fabric futnak, amely az egyes csomópontok állapotát vezérli, és szükség szerint végrehajtja a feladatátvételt az elérhető kifogástalan állapotú csomópontokon.

További információ a nagy kapacitású magas rendelkezésre állásáról: [adatbázis magas rendelkezésre állása a nagy kapacitású-ben](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Zóna redundáns konfigurációja

Alapértelmezés szerint a prémium rendelkezésre állási modell csomópontjainak fürtje ugyanabban az adatközpontban jön létre. A [Azure Availability Zones](../availability-zones/az-overview.md)bevezetésével a SQL Database a üzletileg kritikus adatbázis különböző replikáit különböző rendelkezésre állási zónákba helyezheti ugyanabban a régióban. Egyetlen meghibásodási pont kizárása érdekében a vezérlő gyűrűt több zónában is duplikálja három átjárós gyűrűként (GW). Egy adott átjáró-gyűrű útválasztását az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM) vezérli. Mivel a zóna redundáns konfigurációja a prémium vagy üzletileg kritikus szolgáltatási szinten nem hoz létre további adatbázis-redundanciát, külön díj nélkül is engedélyezheti. A zóna redundáns konfigurációjának kiválasztásával a prémium vagy üzletileg kritikus-adatbázisok rugalmasan állíthatók be a hibák sokkal nagyobb részére, beleértve a katasztrofális adatközpont leállását, az alkalmazás logikájának módosítása nélkül. A meglévő prémium vagy üzletileg kritikus adatbázisok vagy készletek a zóna redundáns konfigurációjához is átalakíthatók.

Mivel a zóna redundáns adatbázisai rendelkeznek a különböző adatközpontokban lévő replikákkal, és ezek között a távolságuk, a megnövelt hálózati késés növelheti a bevezetési időt, és így hatással lehet bizonyos OLTP-számítási feladatok teljesítményére. Bármikor visszatérhet az egyzónás konfigurációhoz a zóna redundancia beállítás letiltásával. Ez a folyamat egy olyan online művelet, amely a szolgáltatási csomag szokásos verziófrissítéséhez hasonló. A folyamat végén a rendszer áttelepíti az adatbázist vagy a készletet egy zónába tartozó redundáns gyűrűből egyetlen zónába, vagy fordítva.

> [!IMPORTANT]
> A zónák redundáns adatbázisai és a rugalmas készletek jelenleg csak a prémium és üzletileg kritikus szolgáltatási szinten támogatottak a régiók kiválasztása területen. A üzletileg kritikusi szintű használata esetén a zóna redundáns konfigurációja csak akkor érhető el, ha a Gen5 számítási hardver van kiválasztva. A zóna redundáns adatbázisokat támogató régiókkal kapcsolatos naprakész információ: [szolgáltatások támogatása régiónként](../availability-zones/az-overview.md#services-support-by-region).  
> Ez a funkció felügyelt példányban nem érhető el.

A magas rendelkezésre állású architektúra zóna redundáns verzióját a következő ábra szemlélteti:

![magas rendelkezésre állású architektúra zóna redundáns](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Gyorsított adatbázis-helyreállítás (ADR)

A [gyorsított adatbázis-helyreállítás (ADR)](sql-database-accelerated-database-recovery.md) egy új SQL Database motor-szolgáltatás, amely nagy mértékben javítja az adatbázisok rendelkezésre állását, különösen a hosszú ideig futó tranzakciók jelenlétében. Az ADR jelenleg egyazon adatbázisok, rugalmas készletek és Azure SQL Data Warehouse számára érhető el.

## <a name="testing-application-fault-resiliency"></a>Az alkalmazás hibatűrési rugalmasságának tesztelése

A magas rendelkezésre állás a Azure SQL Database platform alapvető része, amely transzparens módon működik az adatbázis-alkalmazás számára. Azonban Felismertük, hogy tesztelni szeretné, hogy a tervezett vagy nem tervezett események során kezdeményezett automatikus feladatátvételi műveletek hatással lennének-e az alkalmazásra, mielőtt üzembe helyezné az éles környezetben. Meghívhat egy speciális API-t egy adatbázis vagy egy rugalmas készlet újraindításához, amely a feladatátvételt is elindítja. Egy zóna redundáns adatbázis vagy rugalmas készlet esetén az API-hívás eredményeképpen az ügyfélkapcsolatok átirányítása egy olyan rendelkezésre állási zónában lévő új elsődlegesre, amely eltér a régi elsődleges hely rendelkezésre állási zónájától. Így azt is megvizsgálhatja, hogy a feladatátvétel hogyan befolyásolja a meglévő adatbázis-munkameneteket, azt is ellenőrizheti, hogy a hálózati késés változása miatt a végpontok közötti teljesítményt módosítja-e. Mivel az újraindítási művelet zavaró, és nagy számú közülük a platformot, az egyes adatbázisok vagy rugalmas készletek esetében 30 percenként csak egy feladatátvételi hívás engedélyezett. 

A feladatátvételt REST API vagy PowerShell használatával lehet kezdeményezni. REST API esetében lásd: [adatbázis-feladatátvételi](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) és [rugalmas készlet feladatátvétele](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). A PowerShell esetében tekintse meg a következőt: [meghívás-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) és [meghívása – AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). Az REST API-hívások az Azure CLI-ből is elindíthatók az [az Rest](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest) paranccsal.

> [!IMPORTANT]
> A feladatátvételi parancs jelenleg nem érhető el a nagy kapacitású szolgáltatási szintjében és a felügyelt példányok esetében.

## <a name="conclusion"></a>Összegzés

A Azure SQL Database egy beépített, magas rendelkezésre állású megoldást kínál, amely mélyen integrálva van az Azure platformmal. A probléma a hibák észlelése és helyreállítása Service Fabric függ, az Azure Blob Storage az adatvédelem érdekében, valamint a Availability Zones a nagyobb hibatűrés érdekében. Az Azure SQL Database emellett az Always On rendelkezésre állási csoport technológiáját is kihasználja SQL Server a replikációhoz és a feladatátvételhez. Ezeknek a technológiáknak a kombinációja lehetővé teszi, hogy az alkalmazások teljes mértékben felismerje a vegyes tárolási modell előnyeit, és támogassa a legigényesebb SLA-kat.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [Azure Availability Zones](../availability-zones/az-overview.md)
- Tudnivalók a [Service Fabric](../service-fabric/service-fabric-overview.md)
- További tudnivalók az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- A magas rendelkezésre állással és a vész-helyreállítással kapcsolatos további lehetőségekért lásd: [Üzletmenet-folytonosság](sql-database-business-continuity.md)

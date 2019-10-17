---
title: Magas rendelkezésre állás – Azure SQL Database szolgáltatás | Microsoft Docs
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
ms.openlocfilehash: 28b702192b41d3b4a8151e3127a4297c28712fa2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390700"
---
# <a name="high-availability-and-azure-sql-database"></a>Magas rendelkezésre állás és Azure SQL Database

A Azure SQL Database magas rendelkezésre állású architektúrájának célja annak biztosítása, hogy az adatbázis az idő 99,99%-ában működik, és ne aggódjon a karbantartási műveletek és kimaradások következményeivel. Az Azure automatikusan kezeli a kritikus karbantartási feladatokat, például a javításokat, a biztonsági mentéseket, a Windows-és az SQL-frissítéseket, valamint a nem tervezett eseményeket, például a mögöttes hardvereket, szoftvereket vagy hálózati hibákat.  Ha az alapul szolgáló SQL-példány javított vagy feladatátvételt végez, az állásidő nem észlelhető, ha az alkalmazásban [újrapróbálkozási logikát alkalmaz](sql-database-develop-overview.md#resiliency) . Azure SQL Database a legfontosabb körülmények között is gyorsan helyreállítható, így biztosítva, hogy az adatok mindig elérhetők legyenek.

A magas rendelkezésre állású megoldás úgy van kialakítva, hogy a hibák miatt soha ne vesszenek el az véglegesített adatok, hogy a karbantartási műveletek ne befolyásolják a munkaterhelést, és hogy az adatbázis nem lesz egyetlen meghibásodási pont a szoftver architektúrájában. Nincsenek olyan karbantartási időszakok vagy állásidők, amelyeknek az adatbázis frissítése vagy karbantartása közben le kell állítania a munkaterhelést. 

Két magas rendelkezésre állású építészeti modell van használatban a Azure SQL Databaseban:

- A standard rendelkezésre állási modell, amely a számítás és a tárolás elkülönítésén alapul.  A távoli tárolási rétegek magas rendelkezésre állása és megbízhatósága alapján működik. Ez az architektúra olyan költségvetési alapú üzleti alkalmazásokat céloz meg, amelyek bizonyos teljesítménybeli romlást tudnak elviselni a karbantartási tevékenységek során.
- Prémium rendelkezésre állási modell, amely az adatbázismotor folyamatainak egy fürtjén alapul. Arra támaszkodik, hogy a rendelkezésre álló adatbázismotor-csomópontok kvóruma mindig fennáll. Ez az architektúra olyan kritikus fontosságú alkalmazásokat céloz meg, amelyek magas i/o-teljesítménnyel, magas tranzakciós sebességgel rendelkeznek, és a karbantartási tevékenységek során minimális teljesítményt nyújtanak a számítási feladathoz.

A Azure SQL Database a SQL Server adatbázismotor és a Windows operációs rendszer legújabb stabil verzióján fut, és a legtöbb felhasználó nem észleli, hogy a frissítések folyamatosan vannak végrehajtva.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Alapszintű, standard és általános célú szolgáltatási szint rendelkezésre állása

Ezek a szolgáltatási szintek a standard rendelkezésre állási architektúrát használják. Az alábbi ábra négy különböző csomópontot mutat be a elkülönített számítási és tárolási rétegekkel együtt.

![A számítás és a tárolás elkülönítése](media/sql-database-high-availability/general-purpose-service-tier.png)

A standard rendelkezésre állási modell két réteget tartalmaz:

- Az `sqlservr.exe` folyamatot futtató állapot nélküli számítási réteg, amely csak átmeneti és gyorsítótárazott adatokból áll, például a TempDB, a csatolt SSD modell-adatbázisainak, valamint a gyorsítótár, a pufferméret és a oszlopcentrikus-készletnek a memóriában történő megtervezésével. Ezt az állapot nélküli csomópontot az Azure Service Fabric üzemelteti, amely a `sqlservr.exe` inicializálását, a csomópont állapotát vezérli, és szükség esetén feladatátvételt hajt végre egy másik csomóponton.
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

- Egy állapot nélküli számítási réteg, amely a `sqlservr.exe` folyamatot futtatja, és csak átmeneti és gyorsítótárazott adatokból áll, mint például a nem a RBPEX cache, a TempDB, a Model Database stb. a csatlakoztatott SSD esetében, valamint a gyorsítótár, a puffer készlet és a oszlopcentrikus-készlet a memóriában történő megtervezése. Ez az állapot nélküli réteg tartalmazza az elsődleges számítási replikát és opcionálisan számos másodlagos számítási replikát, amelyek feladatátvételi célként szolgálhatnak.
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

## <a name="testing-database-fault-resiliency"></a>Adatbázis-hibatűrési rugalmasság tesztelése

A magas rendelkezésre állás a Azure SQL Database platform fundamenental része, és az adatbázis-alkalmazás számára transzparens módon működik. Azonban Felismertük, hogy tesztelni szeretné, hogy a tervezett vagy nem tervezett események során kezdeményezett automatikus feladatátvételi műveletek hatással lennének-e az alkalmazásra az éles környezetbe történő üzembe helyezése előtt. Meghívhat egy speciális API-t az adatbázis vagy a rugalmas készlet újraindításához, ami viszont elindítja a feladatátvételt. A zóna redundáns adatbázisa vagy a rugalmas készlet esetében az API-hívás azt eredményezi, hogy az ügyfélkapcsolatok átirányítása az új elsődlegesre egy másik az alkalmazásban. Tehát azon kívül, hogy a feladatátvétel milyen hatással van a meglévő adatbázis-munkamenetekre, azt is ellenőrizheti, hogy az hatással van-e a végpontok közötti teljesítményre. Mivel az újraindítási művelet zavaró, és nagy számú közülük kiemelheti a platformot, minden adatbázishoz vagy rugalmas készlethez 30 percenként csak egy feladatátvételi hívás engedélyezett. Részletekért lásd: [adatbázis-feladatátvételi](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) és [rugalmas készlet feladatátvétele](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover).       

> [!IMPORTANT]
> A feladatátvételi parancs jelenleg nem érhető el Hypescale-adatbázisokhoz és felügyelt instancses.  

## <a name="conclusion"></a>Összegzés

A Azure SQL Database egy beépített, magas rendelkezésre állású megoldást kínál, amely mélyen integrálva van az Azure platformmal. A probléma a hibák észlelése és helyreállítása Service Fabric függ, az Azure Blob Storage az adatvédelem érdekében, valamint a Availability Zones a nagyobb hibatűrés érdekében. Az Azure SQL Database emellett az Always On rendelkezésre állási csoport technológiáját is kihasználja SQL Server a replikációhoz és a feladatátvételhez. Ezeknek a technológiáknak a kombinációja lehetővé teszi, hogy az alkalmazások teljes mértékben felismerje a vegyes tárolási modell előnyeit, és támogassa a legigényesebb SLA-kat.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [Azure Availability Zones](../availability-zones/az-overview.md)
- Tudnivalók a [Service Fabric](../service-fabric/service-fabric-overview.md)
- További tudnivalók az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
- A magas rendelkezésre állással és a vész-helyreállítással kapcsolatos további lehetőségekért lásd: [Üzletmenet-folytonosság](sql-database-business-continuity.md)

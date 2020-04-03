---
title: Magas rendelkezésre állás
description: Ismerje meg az Azure SQL Database szolgáltatás magas rendelkezésre állási lehetőségeit és funkcióit
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 04/02/2020
ms.openlocfilehash: 1c4ed77112e8c06db1946d756239e02cb187f3ef
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618492"
---
# <a name="high-availability-and-azure-sql-database"></a>Magas rendelkezésre állás és Az Azure SQL Database

Az Azure SQL Database magas rendelkezésre állású architektúrájának célja annak biztosítása, hogy az adatbázis legalább 99,99%-os idővel üzemeljen (A különböző szintekre vonatkozó sla-val kapcsolatos további információkért tekintse meg [az SLA for Azure SQL Database-t),](https://azure.microsoft.com/support/legal/sla/sql-database/)anélkül, hogy aggódnia kellene a karbantartási műveletek és kimaradások hatása miatt. Az Azure automatikusan kezeli a kritikus karbantartási feladatokat, például a javítást, a biztonsági mentéseket, a Windows és az SQL-frissítéseket, valamint a nem tervezett eseményeket, például az alapul szolgáló hardvereket, szoftvereket vagy hálózati hibákat.  Ha az alapul szolgáló SQL-példány javításra kerül, vagy átadja a feladatait, az állásidő nem észrevehető, ha [újrapróbálkozási logikát alkalmaz](sql-database-develop-overview.md#resiliency) az alkalmazásban. Az Azure SQL Database gyorsan helyreállíthatja még a legkritikusabb körülmények között is, biztosítva, hogy az adatok mindig elérhetők legyenek.

A magas rendelkezésre állású megoldás célja annak biztosítása, hogy a véglegesített adatok soha nem vész el a hibák miatt, hogy a karbantartási műveletek nem befolyásolják a számítási feladatokat, és hogy az adatbázis nem lesz egyetlen hibapont a szoftverarchitektúrában. Nincsenek karbantartási időszakok vagy állásidők, amelyek megkövetelik, hogy állítsa le a számítási feladatok, az adatbázis frissítése vagy karbantartása közben. 

Az Azure SQL Database két magas rendelkezésre állású architekturális modellt használ:

- Standard rendelkezésre állási modell, amely a számítási és tárolási elkülönítésen alapul.  A távtároló csomag magas rendelkezésre állására és megbízhatóságára támaszkodik. Ez az architektúra olyan költségvetés-orientált üzleti alkalmazásokat céloz meg, amelyek elviselnek bizonyos teljesítménycsökkenést a karbantartási tevékenységek során.
- Prémium szintű rendelkezésre állási modell, amely adatbázis-motor folyamatok fürtjén alapul. Arra a tényre támaszkodik, hogy mindig van egy kvóruma a rendelkezésre álló adatbázis-motor csomópontok. Ez az architektúra a kritikus fontosságú alkalmazásokat célozza meg magas I/O-teljesítménnyel, magas tranzakciós aránnyal, és minimális teljesítményhatást garantál a karbantartási tevékenységek során a munkaterhelésre.

Az Azure SQL Database az SQL Server Database Engine és a Windows operációs rendszer legújabb stabil verzióján fut, és a legtöbb felhasználó nem veszi észre, hogy a frissítések folyamatosan végrehajtásra kerülnek.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Alapszintű, standard és általános célú szolgáltatási szint elérhetősége

Ezek a szolgáltatási szintek a szabványos rendelkezésre állási architektúrát használják ki. Az alábbi ábrán négy különböző csomópont látható a leválasztott számítási és tárolási rétegekkel.

![A számítás és a tárolás szétválasztása](media/sql-database-high-availability/general-purpose-service-tier.png)

A szabványos rendelkezésre állási modell két réteget tartalmaz:

- Állapot nélküli számítási réteg, `sqlservr.exe` amely futtatja a folyamatot, és csak átmeneti és gyorsítótárazott adatokat tartalmaz, például a TempDB-t, a csatlakoztatott SSD modelladatbázisait, valamint a tároló, a pufferkészlet és az oszlopcentrikus készlet a memóriában. Ezt az állapotnélküli csomópontot az Azure Service `sqlservr.exe`Fabric inicializálja, szabályozza a csomópont állapotát, és szükség esetén feladatátvételt hajt végre egy másik csomópontra.
- Állapotalapú adatréteg az Azure Blob storage-ban tárolt adatbázisfájlokkal (.mdf/.ldf). Az Azure blob storage beépített adatrendelkezésre állási és redundancia funkcióval rendelkezik. Garantálja, hogy a naplófájlban vagy lapban lévő összes rekord akkor is megőrződik, ha az SQL Server folyamat összeomlik.

Amikor az adatbázis-motor vagy az operációs rendszer frissítése, vagy hiba észlelése, az Azure Service Fabric áthelyezi az állapotnélküli SQL Server-folyamat egy másik állapot nélküli számítási csomópont elegendő szabad kapacitással. Az Azure Blob storage-ban lévő adatokat az áthelyezés nem befolyásolja, és az adatok/naplófájlok az újonnan inicializált SQL Server-folyamathoz vannak csatolva. Ez a folyamat 99,99%-os rendelkezésre állást garantál, de a nagy munkaterhelés némi teljesítménycsökkenést tapasztalhat az átmenet során, mivel az új SQL Server-példány hideg gyorsítótárral kezdődik.

## <a name="premium-and-business-critical-service-tier-availability"></a>Prémium és üzleti legkritikusabb szolgáltatási szint elérhetősége

A prémium szintű és az üzleti legkritikusabb szolgáltatási szintek a prémium szintű rendelkezésre állási modellt használják ki, amely egyetlen csomóponton integrálja a számítási erőforrásokat (SQL Server Database Engine folyamat) és a tárolást (helyileg csatlakoztatott SSD). A magas rendelkezésre állás a számítási és a tárolási replikálásával érhető el egy három és négy csomópontos fürt öt csomópontot létrehozó további csomópontokra. 

![Adatbázis-motor csomópontjainak fürtje](media/sql-database-high-availability/business-critical-service-tier.png)

Az alapul szolgáló adatbázisfájlok (.mdf/.ldf) a csatlakoztatott SSD-tárolóba kerülnek, hogy nagyon alacsony késleltetésű I/-t biztosítsanak a számítási feladatokhoz. A magas rendelkezésre állás az SQL Server Always On Availability Groups technológiához hasonló technológiával [valósítva](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)meg. A fürt tartalmaz egy elsődleges replika (SQL Server folyamat), amely elérhető az írási és olvasási ügyfél számítási feladatok, és legfeljebb három másodlagos replika (számítási és tárolási) adatok másolatát tartalmazó. Az elsődleges csomópont folyamatosan leküldi a módosításokat a másodlagos csomópontok sorrendben, és biztosítja, hogy az adatok szinkronizálása legalább egy másodlagos replika véglegesítése előtt minden egyes tranzakciót. Ez a folyamat garantálja, hogy ha az elsődleges csomópont bármilyen okból összeomlik, mindig van egy teljesen szinkronizált csomópont, amelynek feladatátvételre van szükség. A feladatátvételt az Azure Service Fabric kezdeményezi. Miután a másodlagos replika lesz az új elsődleges csomópont, egy másik másodlagos replika jön létre annak biztosítására, hogy a fürt elegendő csomópont (kvórumkészlet). A feladatátvétel befejezése után az SQL-kapcsolatok automatikusan átlesznek irányítva az új elsődleges csomópontra.

Egy további előny, a prémium szintű rendelkezésre állási modell magában foglalja az olvasási csak SQL-kapcsolatok átirányítása a másodlagos replikák egyikére. Ezt a funkciót [olvasási horizontális felskálázásnak](sql-database-read-scale-out.md)hívják. 100%-kal nagyobb számítási kapacitást biztosít, díjmentesen az elsődleges replika csak olvasási műveletek, például az analitikus számítási feladatok kiszervezéséhez.

## <a name="hyperscale-service-tier-availability"></a>A szolgáltatási szint nagy kapacitású elérhetősége

A nagy kapacitású szolgáltatásiszint architektúrát az [Elosztott függvények architektúrája](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture)ismerteti. 

![Nagy léptékű funkcionális architektúra](./media/sql-database-hyperscale/hyperscale-architecture.png)

A hiperskálázás rendelkezésre állási modellje négy réteget tartalmaz:

- Állapot nélküli számítási réteg, `sqlservr.exe` amely futtatja a folyamatokat, és csak átmeneti és gyorsítótárazott adatokat tartalmaz, például nem fedi le az RBPEX gyorsítótárat, a TempDB-t, a modelladatbázist stb. Ez az állapotmentes réteg tartalmazza az elsődleges számítási replika, és adott esetben számos másodlagos számítási replika, amely feladatátvételi célokként szolgálhat.
- Lapkiszolgálók által létrehozott állapotnélküli tárolási réteg. Ez a réteg a számítási `sqlservr.exe` replikákon futó folyamatok elosztott tárolómotorja. Minden lapkiszolgáló csak átmeneti és gyorsítótárazott adatokat tartalmaz, például a csatolt SSD RBPEX gyorsítótárának és a memóriában gyorsítótárazott adatlapoknak. Minden lapkiszolgáló rendelkezik egy párosított lapkiszolgálóval aktív-aktív konfigurációban, amely biztosítja a terheléselosztást, a redundanciát és a magas rendelkezésre állást.
- A naplószolgáltatási folyamatot, a tranzakciónapló-lefutási zónát és a tranzakciónapló hosszú távú tárolását futtató számítási csomópont által létrehozott állapotalapú tranzakciónapló-tárolási réteg. A leszállási zóna és a hosszú távú tárolás az Azure Storage-ot használja, amely rendelkezésre áll és [redundanciát](https://docs.microsoft.com/azure/storage/common/storage-redundancy) biztosít a tranzakciós naplóhoz, biztosítva az adatok tartósságát a véglegesített tranzakciókhoz.
- Állapotalapú adattárolási réteg az Azure Storage-ban tárolt és lapkiszolgálók által frissített adatbázisfájlokkal (.mdf/.ndf). Ez a réteg az Azure Storage adatelérhetőségi és [redundanciai](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funkcióit használja. Garantálja, hogy az adatfájl minden lapja megmarad, még akkor is, ha a nagy kapacitású architektúra más rétegeiben lévő folyamatok összeomlanak, vagy ha a számítási csomópontok sikertelenek.

Számítási csomópontok az azure Service Fabric, amely szabályozza az egyes csomópontok állapotát, és elvégzi a feladatátvételt a rendelkezésre álló kifogástalan állapotú csomópontok, ha szükséges.

A nagy kapacitásmagas rendelkezésre állásáról az [Adatbázis magas rendelkezésre állása a nagy kapacitású területen](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)című témakörben talál további információt.

## <a name="zone-redundant-configuration"></a>Zóna redundáns konfigurációja

Alapértelmezés szerint a prémium szintű rendelkezésre állási modell csomópontjainak fürtje ugyanabban az adatközpontban jön létre. Az Azure [rendelkezésre állási zónák](../availability-zones/az-overview.md)bevezetésével az SQL Database az üzleti legkritikusabb adatbázis különböző replikáit helyezheti el az ugyanabban a régióban lévő különböző rendelkezésre állási zónákban. Egyetlen meghibásodási pont kiküszöbölése érdekében a vezérlőgyűrű több zónában is megkettőződik, három átjárógyűrű (GW) formájában. Egy adott átjárógyűrűre irányuló útválasztást az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM) vezérli. Mivel a zóna redundáns konfiguráció a prémium vagy üzleti legkritikusabb szolgáltatási szinteken nem hoz létre további adatbázis-redundanciát, engedélyezheti azt többletköltség nélkül. A zóna redundáns konfiguráció kiválasztásával a prémium szintű vagy üzleti legkritikusabb adatbázisok rugalmasak lehetnek a hibák sokkal nagyobb készletével szemben, beleértve a katasztrofális adatközpont-kimaradásokat, az alkalmazáslogikának módosítása nélkül. A meglévő Prémium vagy üzleti legkritikusabb adatbázisokat vagy készleteket a zóna redundáns konfigurációvá is konvertálhatja.

Mivel a zónaredundáns adatbázisok replikák különböző adatközpontokban vannak, és bizonyos távolságra vannak közöttük, a megnövekedett hálózati késés növelheti a véglegesítési időt, és így hatással lehet néhány OLTP számítási feladatok teljesítményére. Bármikor visszatérhet az egyzónás konfigurációhoz a zóna redundancia beállításának letiltásával. Ez a folyamat a rendszeres szolgáltatási szint frissítéséhez hasonló online művelet. A folyamat végén az adatbázis vagy készlet egy zónaredundáns gyűrűről egyetlen zónagyűrűre, vagy fordítva kerül áttelepítésre.

> [!IMPORTANT]
> Zóna redundáns adatbázisok és rugalmas készletek jelenleg csak a prémium szintű és az üzleti legkritikusabb szolgáltatási szintek bizonyos régiókban támogatott. Az üzleti legkritikusabb szint használata esetén a zónaredundáns konfiguráció csak akkor érhető el, ha a Gen5 számítási hardver van kiválasztva. A zónaredundáns adatbázisokat támogató régiókkal kapcsolatos naprakész információkért lásd: [Szolgáltatások támogatása régiónként.](../availability-zones/az-overview.md#services-support-by-region)  
> Ez a funkció nem érhető el a Felügyelt példányban.

A magas rendelkezésre állású architektúra zónaredundáns verzióját a következő ábra szemlélteti:

![magas rendelkezésre állású architektúrazóna redundáns](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Gyorsított adatbázis-helyreállítás (ADR)

[Az Accelerated Database Recovery (ADR)](sql-database-accelerated-database-recovery.md) egy új SQL adatbázis-motorszolgáltatás, amely nagymértékben javítja az adatbázis rendelkezésre állását, különösen hosszú ideig futó tranzakciók esetén. Az ADR jelenleg egyetlen adatbázisokhoz, rugalmas készletekhez és Az Azure SQL Data Warehousehoz érhető el.

## <a name="testing-application-fault-resiliency"></a>Az alkalmazás hibarugalmasságának tesztelése

A magas rendelkezésre állás az Azure SQL Database platform alapvető része, amely transzparens módon működik az adatbázis-alkalmazásában. Azonban tisztában vagyunk azonban azzal, hogy érdemes lehet tesztelni, hogy a tervezett vagy nem tervezett események során kezdeményezett automatikus feladatátvételi műveletek hatással lennének az alkalmazásra, mielőtt éles környezetbe helyezné. Meghívhat egy speciális API-t egy adatbázis vagy egy rugalmas készlet újraindításához, amely viszont feladatátvételt vált ki. Zónaredundáns adatbázis vagy rugalmas készlet esetén az API-hívás eredményeképpen az ügyfélkapcsolatok átirányítása az új elsődleges egy rendelkezésre állási zóna eltér a rendelkezésre állási zóna a régi elsődleges. Így a mellett, hogy teszteli, hogyan befolyásolja a feladatátvétel a meglévő adatbázis-munkamenetek, azt is ellenőrizheti, hogy módosítja-e a végpontok között a teljesítményt a hálózati késés változásai miatt. Mivel az újraindítási művelet tolakodó, és nagy számban ezek stresszelhetik a platformot, minden adatbázishoz vagy rugalmas készlethez 30 percenként csak egy feladatátvételi hívás engedélyezett. 

Feladatátvétel kezdeményezhető REST API vagy PowerShell használatával. A REST API-hoz: [Adatbázis feladatátvétel](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) és [rugalmas készlet feladatátvétel.](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover) A PowerShell, [az Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) és [az Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). A REST API-hívások is kezdeményezhetők az Azure CLI használatával [az rest](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest) parancs.

> [!IMPORTANT]
> A feladatátvételi parancs jelenleg nem érhető el a nagy kapacitású szolgáltatási rétegben és a felügyelt példányban.

## <a name="conclusion"></a>Összegzés

Az Azure SQL Database egy beépített, magas rendelkezésre állású megoldással rendelkezik, amely mélyen integrálva van az Azure platformmal. Függ a Service Fabric a hiba észlelése és helyreállítása, az Azure Blob storage adatvédelem, és a rendelkezésre állási zónák nagyobb hibatűrés. Emellett az Azure SQL-adatbázis az SQL Server mindig rendelkezésre állási csoport technológiáját használja a replikációhoz és a feladatátvételhez. Ezeknek a technológiáknak a kombinációja lehetővé teszi az alkalmazások számára, hogy teljes mértékben kiaknázják a vegyes tárolási modell előnyeit, és támogassák a legigényesebb SLA-kat.

## <a name="next-steps"></a>További lépések

- További információ az [Azure rendelkezésre állási zónáiról](../availability-zones/az-overview.md)
- További információ a [Service Fabricről](../service-fabric/service-fabric-overview.md)
- További információ az [Azure Traffic Managerről](../traffic-manager/traffic-manager-overview.md)
- A magas rendelkezésre állás és a vészhelyreállítás további lehetőségeiért lásd: [Üzletmenet-folytonosság](sql-database-business-continuity.md)

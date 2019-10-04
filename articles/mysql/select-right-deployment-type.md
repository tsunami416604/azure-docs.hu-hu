---
title: A Azure Database for MySQL megfelelő központi telepítési típusának kiválasztása
description: Ez a cikk az infrastruktúra-szolgáltatás (IaaS) vagy a szolgáltatásként elérhető platform (Pásti) a Azure Database for MySQLhez való folytatásához szükséges szempontokat ismerteti.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7bcbf379ea8d046c8c477dc716711a6a6ffa1dc9
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813833"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Válassza ki a megfelelő MySQL-kiszolgáló lehetőséget az Azure-ban

Az Azure-ban a MySQL-kiszolgáló számítási feladatait üzemeltetett infrastruktúra-alapú virtuális gépen (IaaS) vagy üzemeltetett szolgáltatásként (Pásti) is futtathatják. A Péter több üzembe helyezési lehetőséggel rendelkezik, és az egyes üzembe helyezési lehetőségeken belül vannak szolgáltatási szintek. A Péter vagy a IaaS közötti döntés során el kell döntenie, hogy szeretné-e felügyelni az adatbázist, a javítások alkalmazásával és a biztonsági mentések elvégzésével, vagy ha ezeket a műveleteket az Azure-ba szeretné delegálni.</br>

A kérdésre adott válasz alapján vegye figyelembe a következő lehetőségeket: <br/>

A **Azure Database for MySQL** egy teljes körűen felügyelt MySQL-adatbázismotor a közösségi kiadás stabil verziójától függően. Ez az Azure-felhőben üzemeltetett, DBaaS-alapú, szolgáltatásként működő adatbázis-szolgáltatás a platform-szolgáltatás (Péter) iparági kategóriába tartozik. Az Azure-ban felügyelt MySQL-példánnyal a beépített funkciók és funkciók széles körű konfigurálást igényelnek a MySQL-kiszolgáló (akár helyszíni, akár egy Azure-beli virtuális gép) használata esetén. Ha a MySQL-t használja szolgáltatásként, a nagyobb teljesítmény érdekében, megszakítás nélkül, a felskálázást és a kicsinyítést is elvégezheti. Emellett az önálló MySQL-kiszolgálótól eltérően a Azure Database for MySQL olyan további funkciókat is tartalmaz, mint például a beépített magas rendelkezésre állás, az intelligencia és a felügyelet. <br/><br/>
A **MySQL az Azure** -beli virtuális gépen az infrastruktúra-szolgáltatás (IaaS) kategóriába tartozik, és lehetővé teszi a MySQL-kiszolgáló futtatását egy teljes körűen felügyelt virtuális gépen az Azure-felhőben. A MySQL legújabb verzióit és kiadásait IaaS virtuális gépre lehet telepíteni. A Azure Database for MySQL legjelentősebb különbség az, hogy az Azure-beli virtuális gépeken a MySQL lehetővé teszi az adatbázismotor vezérlését. Ez a szabályozás azonban a virtuális gépek és számos DBA-feladat, például az adatbázis-kiszolgáló karbantartása/javítása, a helyreállítás és a magas rendelkezésre állás kialakításának további felelősségi körébe tartozik.

A fenti lehetőségek közötti fő különbségek a következő táblázatban láthatók:

|            | **Azure Database for MySQL** | **MySQL Azure-beli virtuális gépeken**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | A 99,99%-os rendelkezésre állást biztosító SLA-t kínál| Akár 99,95%-os rendelkezésre állás 2 vagy több példánnyal ugyanazon rendelkezésre állási csoporton belül. <br/>99,9%-os egypéldányos virtuális gép Premium Storage használatával <br/> 99,99% a rendelkezésre állási zónával 2 vagy több példánnyal 2 vagy több rendelkezésre állási készletben.<br/> Megjegyzés – a [virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) -ja |
| **Operációs rendszer javítása**        | Automatikus  | Ügyfelek által felügyelt |
|**MySQL-javítás**     | Automatikus  | Ügyfelek által felügyelt |
| **Magas rendelkezésre állás** | A magas rendelkezésre állású (HA) modell a beépített feladatátvételi mechanizmusokon alapul, ha csomópont szintű megszakítás történik. Ilyen esetekben a szolgáltatás automatikusan létrehozza a példányt, és csatolja a tárolót az új példányhoz. | A magas rendelkezésre állást az ügyfél tervezője, implementálja, teszteli és tartja karban. Ebbe beletartozhatnak az Always-On (feladatátvételi fürtszolgáltatás vagy csoportos replikáció), a naplózás és a tranzakciós replikáció, a használatban lévő MySQL-motor verziójától függően.|
| **Zóna redundancia** | Jelenleg nem támogatott. | Az Azure-beli virtuális gépek különböző rendelkezésre állási zónákban való futtatásra állíthatók be. Helyszíni megoldás esetén az ügyfeleknek saját másodlagos adatközpontot kell létrehozniuk, kezelnie és karbantartani.|
| **Hibrid forgatókönyvek** | [Felhőbe irányuló replikálás](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) lehetővé teszi, hogy szinkronizálja az adatokat egy külső MySQL-kiszolgálóról a Azure Database for MySQL szolgáltatásba. A külső kiszolgáló lehet helyszíni, virtuális gépek vagy más felhőalapú szolgáltatók által üzemeltetett adatbázis-szolgáltatás.  <br/> <br/> Az [olvasási replika](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) funkció lehetővé teszi az adatok replikálását egy Azure Database for MySQL-kiszolgálóról (Master) akár öt írásvédett kiszolgálóra (replikára) ugyanazon az Azure-régióban vagy régión belül. Az írásvédett replikák aszinkron módon frissülnek a BinLog replikációs technológiával.   <br/> <br/> Megjegyzés – a régiók közötti olvasási replikálás jelenleg nyilvános előzetes verzióban érhető el.| Ügyfelek által felügyelt <br/>
| **Biztonsági mentés és visszaállítás** | A automatikusan létrehozza a [kiszolgáló biztonsági másolatait](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) , és tárolja őket a helyileg redundáns vagy földrajzilag redundáns tárolóban. A szolgáltatás teljes, differenciális és tranzakciónapló-alapú biztonsági mentéseket tart | Ügyfelek által felügyelt |
| **Adatbázis-műveletek figyelése** | Lehetővé teszi, hogy az ügyfelek [riasztásokat állítsanak be](https://docs.microsoft.com/azure/mysql/concepts-monitoring) az adatbázis-műveletre, és a küszöbértékek elérésekor járjanak. | Ügyfelek által felügyelt |
| **Speciális szál védelme** | Komplex [veszélyforrások elleni védelmet](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal) nyújt, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az adatbázisok eléréséhez vagy kiaknázásához. | Az ügyfeleknek saját maguknak kell kiépíteniük.
| **Biztonsági másolatok (vész-helyreállítási)** | A felhasználó által [helyileg redundáns vagy földrajzilag redundáns tárolásban](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)konfigurált automatizált biztonsági mentéseket tárolja. A biztonsági mentések a kiszolgálók egy adott időpontra történő visszaállítására is használhatók. A megőrzési időtartam 7-35 nap múlva állítható be. A visszaállítás a Azure Portal használatával végezhető el. <br/> | Az ügyfél teljes mértékben felügyeli, beleértve az ütemezési, tesztelési, archiválási, tárolási és adatmegőrzési korlátozásokat. Egy további lehetőség az Azure Recovery Services-tároló használata az Azure-beli virtuális gépek és adatbázisok biztonsági mentésére a virtuális gépeken (előzetes verzióban). |
| **Teljesítményre vonatkozó javaslat** | Proaktív [teljesítményre vonatkozó ajánlást](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) biztosít az ügyfeleknek a munkaterhelések optimalizálásához a használati telemetria alapján. | Ügyfelek által felügyelt |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>A Pásti vagy a IaaS kiválasztásának üzleti motivációi

Több tényező is befolyásolhatja a saját MySQL-adatbázisok üzemeltetésére vonatkozó, a IaaS-t vagy a-t.

### <a name="cost"></a>Költség

Legyen szó akár készpénzre, akár egy olyan létrehozott vállalatra, amely szűk költségvetési megkötések keretében működik, a korlátozott finanszírozás gyakran az elsődleges szempont az adatbázisok üzemeltetéséhez szükséges legjobb megoldás meghatározásakor. Ez a szakasz a számlázási és licencelési alapismereteket ismerteti az Azure-ban az Azure-beli virtuális gépeken a Azure Database for MySQL és a MySQL tekintetében:

#### <a name="billing"></a>Számlázás

A Azure Database for MySQL jelenleg számos különböző szinten elérhető szolgáltatásként érhető el, és az erőforrásokra eltérő árak vonatkoznak, amelyek mindegyikét óránként, rögzített díjszabással számoljuk el. A jelenleg támogatott szolgáltatási rétegekkel, számítási méretekkel és tárolási mennyiségekkel kapcsolatos legfrissebb információkért lásd: [virtuális mag-alapú vásárlási modell](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). A szolgáltatási szintek és a számítási méretek dinamikusan állíthatók be úgy, hogy megfeleljenek az alkalmazás eltérő átviteli igényeinek. A kimenő internetes forgalom számlázása normál [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/)szerint történik.

A Azure Database for MySQL az adatbázis szoftverét a Microsoft automatikusan konfigurálja, javította és frissíti, ami csökkenti az adminisztrációs költségeket. Ezenfelül a [beépített biztonsági mentési](https://docs.microsoft.com/azure/mysql/concepts-backup) funkciókkal is jelentős költségmegtakarítást érhet el, különösen, ha nagyszámú adatbázist használ. Az Azure-beli virtuális gépeken futó MySQL-sel bármelyik MySQL-verziót kiválaszthatja és futtathatja. Függetlenül attól, hogy milyen MySQL-verziót használ, fizetnie kell a kiépített virtuális géphez, valamint a MySQL-hez használt konkrét licenc-típus díját.

Azure Database for MySQL magas rendelkezésre állást biztosít bármilyen csomópont szintű megszakításhoz, és továbbra is fenntartja az 99,99%-os SLA-t a szolgáltatás számára. Ha azonban a virtuális gépeken belül magas rendelkezésre állást (HA) szeretne elérni, az ügyfélnek a MySQL-adatbázisban elérhető magas rendelkezésre állási lehetőségeket, például a [MySQL-replikációt](https://dev.mysql.com/doc/refman/8.0/en/replication.html)kell használnia. A támogatott magas rendelkezésre állási lehetőség használata nem biztosít további SLA-t, de lehetővé teszi, hogy további költségekkel és adminisztratív terheléssel > 99,99%-os rendelkezésre állást érhet el.

Az árakkal kapcsolatos további információkért lásd a következő erőforrásokat:
* [Díjszabás Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Felügyelet

Sok vállalat esetében a felhőalapú szolgáltatásba való áttérésre vonatkozó döntés annyit jelent, mint az adminisztráció bonyolultságának kiszervezése. A IaaS és a Péter segítségével a Microsoft felügyeli a mögöttes infrastruktúrát, és automatikusan replikálja az összes adatát a vész-helyreállítás biztosítására, az adatbázis-szoftverek konfigurálására és frissítésére, kezeli a terheléselosztást, és átlátható feladatátvételt végez, ha van kiszolgálóhiba történt.

* A **Azure Database for MySQL**segítségével továbbra is felügyelheti az adatbázist, de többé nem kell kezelnie az adatbázismotor, az operációs rendszer vagy a hardver kezelését. Ön felelős például a következő területekért: adatbázisok és bejelentkezési adatok, indexek és lekérdezések hangolása, naplózás és biztonság. Emellett a magas rendelkezésre állás konfigurálása egy másik adatközponthoz minimális vagy nem szükséges a konfiguráció vagy az adminisztráció.<br/><br/>
* Az Azure-beli **virtuális gépeken futó MySQL**-sel teljes mértékben szabályozhatja az operációs rendszer és a MySQL-kiszolgáló példányának konfigurációját. Egy virtuális géppel eldöntheti, hogy mikor frissítse/frissítse az operációs rendszert és az adatbázis szoftverét, és ha további szoftvereket, például vírusirtó alkalmazást szeretne telepíteni. Egyes automatizált szolgáltatások jelentősen leegyszerűsítik a frissítést, a biztonsági mentések készítését és a magas rendelkezésre állást. Ezenfelül meghatározhatja a virtuális gép méretét, a lemezek számát, valamint a tárhely konfigurációját is. További információ: virtuális gépek és felhőalapú szolgáltatások mérete az Azure-ban.

### <a name="time-to-move-to-azure-br"></a>Az Azure-ba való áttérés ideje <br/>
* A **Azure Database for MySQL** a megfelelő megoldás a felhőben tervezett alkalmazások számára, ha a fejlesztő és a gyors piacra kerülési idő az új megoldások esetében kritikus fontosságú. A szoftveres DBA-hez hasonló funkciókkal a szolgáltatás tökéletes megoldás a Felhőbeli tervezők és fejlesztők számára, mivel csökkenti az alapul szolgáló operációs rendszer és adatbázis felügyeletének szükségességét.<br/><br/>
* Az Azure-beli **virtuális gépeken futó MySQL** tökéletes választás olyan meglévő vagy új alkalmazásokhoz, amelyek MySQL-adatbázist igényelnek, vagy a MySQL-adatbázis szolgáltatásaihoz férnek hozzá Windows/Linux rendszeren, és el szeretné kerülni az új helyszíni hardver beszerzésének idejét és költségét. Ez a lehetőség alkalmas a meglévő helyszíni alkalmazások és adatbázisok Azure-ba történő áttelepítésére is – olyan esetekben, amikor egy Azure Database for MySQL példány nem megfelelő. Mivel nincs szükség a bemutató, az alkalmazás és az adatrétegek módosítására, időt és költségvetést takaríthat meg a meglévő megoldás újratervezése során. Ehelyett a megoldások Azure-ba való áttelepítésére és az Azure-platform által igényelt teljesítmény-optimalizálásokra koncentrálhat.

## <a name="next-steps"></a>További lépések

* Lásd: [Azure Database for MySQL díjszabása](https://azure.microsoft.com/pricing/details/mysql/)
* Első lépésként [létrehozhatja az első kiszolgálóját](https://review.docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).


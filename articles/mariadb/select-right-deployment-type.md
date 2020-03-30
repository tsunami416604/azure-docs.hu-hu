---
title: A megfelelő üzembe helyezési típus kiválasztása - Azure Database for MariaDB
description: Ez a cikk ismerteti, milyen tényezőket kell figyelembe venni, mielőtt az Azure Database for MariaDB infrastruktúra szolgáltatásként (IaaS) vagy platform szolgáltatásként (PaaS) üzembe helyezése előtt.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4c7eb5e4f22cb432a9d17e6eafa653e62e1f9129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529900"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Válassza ki a megfelelő MariaDB Server lehetőséget az Azure-ban

Az Azure-ral a MariaDB-kiszolgáló számítási feladatai szolgáltatásként (IaaS) vagy szolgáltatásként (PaaS) futtathatók egy üzemeltetett virtuálisgép-infrastruktúrában. A PaaS több telepítési lehetőséggel rendelkezik, és az egyes telepítési lehetőségeken belül vannak szolgáltatási szintek. Ha az IaaS és a PaaS közül választ, el kell döntenie, hogy szeretné-e kezelni az adatbázist, javításokat szeretne alkalmazni, és biztonsági mentéseket szeretne készíteni, vagy ezeket a műveleteket az Azure-ba szeretné delegálni.

A döntés meghozatalakor vegye figyelembe a következő két lehetőséget:

- **Azure-adatbázis a MariaDB-hez:** Ez a beállítás egy teljes körűen felügyelt MariaDB adatbázis-motor, amely a MariaDB közösségi kiadás stabil verzióján alapul. Ez a relációs adatbázis szolgáltatásként (DBaaS), az Azure felhőplatformon üzemeltetett, a PaaS iparági kategóriájába tartozik.

  Az Azure-beli MariaDB felügyelt példányával olyan beépített funkciókat is használhat, amelyek egyébként kiterjedt konfigurációt igényelnek, ha a MariaDB Server a helyszínen vagy egy Azure-beli virtuális gépen található.

  Ha a MariaDB-t szolgáltatásként használja, menet közben is fizetnie kell a nagyobb mértékű vezérlés érdekében, megszakítás nélkül. Az önálló MariaDB-kiszolgálóval ellentétben az Azure Database for MariaDB további funkciókkal is rendelkezik, például a beépített magas rendelkezésre állással, intelligenciával és felügyelettel.

- **MariaDB az Azure virtuális gépeken:** Ez a lehetőség az IaaS iparági kategóriájába tartozik. Ezzel a szolgáltatással futtathatja a MariaDB Servert egy teljes körűen felügyelt virtuális gépen az Azure felhőplatformján. A MariaDB összes legújabb verziója és kiadása telepíthető egy IaaS virtuális gépre.

  A MariaDB-alapú Azure-adatbázistól való legjelentősebb eltérésben az Azure-beli virtuális gépeken található MariaDB az adatbázis-motor vezérlését kínálja. Ez a vezérlő azonban a virtuális gépek és számos adatbázis-felügyeleti (DBA) feladat kezeléséért felelős felelősség rovására történik. Ezek a feladatok közé tartozik az adatbázis-kiszolgálók karbantartása és javítása, az adatbázis helyreállítása és a magas rendelkezésre állású kialakítás.

Az ezen lehetőségek közötti főbb különbségeket az alábbi táblázat sorolja fel:

|            | Azure Database for MariaDB | MariaDB az Azure virtuális gépeken    |
|:-------------------|:-----------------------------|:--------------------|
| Szolgáltatásiszint-szerződés (SLA)                | 99,99%-os sla-t kínál| Akár 99,95%-os rendelkezésre állás két vagy több példányugyanabban a rendelkezésre állási csoportban.<br/><br/>99,9%-os rendelkezésre állás egyetlen példányban virtuális gép prémium szintű tárhely használatával.<br/><br/>99.99% a rendelkezésre állási zónák több példánytöbb rendelkezésre állási csoportok használata.<br/><br/>Lásd a [virtuális gépek SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Operációs rendszer javítása        | Automatikus  | Az ügyfelek kezelik |
| MariaDB javítás     | Automatikus  | Az ügyfelek kezelik |
| Magas rendelkezésre állás | A magas rendelkezésre állású (HA) modell beépített feladatátvételi mechanizmusokon alapul, ha egy csomópont szintű megszakítás történik. Ilyen esetekben a szolgáltatás automatikusan létrehoz egy új példányt, és ehhez a példányhoz tárolót csatol. | Az ügyfelek magas rendelkezésre állást terveznek, valósítanak meg, tesztelnek és tartanak fenn. A képességek közé tartozhat a mindig bekapcsolt feladatátvételi fürtözés, a mindig bekapcsolt csoportreplikáció, a naplószállítás vagy a tranzakciós replikáció.|
| Zóna redundanciája | Jelenleg nem támogatott | Az Azure virtuális gépek beállíthatók a különböző rendelkezésre állási zónákban való futtatásra. A helyszíni megoldás, az ügyfelek létre kell hozniuk, kezelnie és karbantartania saját másodlagos adatközpont.|
| Hibrid forgatókönyvek | A [Data-in replikációval](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication)szinkronizálhatja a külső MariaDB-kiszolgálóadatait az Azure Database for MariaDB szolgáltatásba. A külső kiszolgáló lehet a helyszíni, a virtuális gépek, vagy egy adatbázis-szolgáltatás üzemelteti más felhőszolgáltatók által üzemeltetett.<br/><br/> Az [olvasási replika](https://docs.microsoft.com/azure/mariadb/concepts-read-replicas) szolgáltatással replikálhatja az adatokat egy Azure Database for MariaDB főkiszolgáló akár öt írásvédett replikakiszolgálók. A replikák vagy ugyanabban az Azure-régióban vagy a régiók között. Az írásvédett kópiák aszinkron módon frissülnek a binlog replikációs technológia használatával.<br/><br/>A régiók közötti olvasási replikáció jelenleg nyilvános előzetes verzióban van.| Az ügyfelek kezelik
| Biztonsági mentés és visszaállítás | Automatikusan létrehozza [a kiszolgálóbiztonsági másolatokat,](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) és tárolja azokat a felhasználó által konfigurált, helyileg redundáns vagy georedundáns tárolóban. A szolgáltatás teljes körű, különbözeti és tranzakciós naplóbiztonsági mentést készít | Az ügyfelek kezelik |
| Adatbázis-műveletek figyelése | Lehetővé teszi az ügyfelek számára, hogy [riasztásokat állítsanak be](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) az adatbázis működéséről, és a küszöbértékek elérésekor cselekedjenek. | Az ügyfelek kezelik |
| Fejlett fenyegetésvédelem | [Komplex veszélyforrások elleni védelmet](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal)biztosít. Ez a védelem észleli az adatbázisokhoz való szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket.<br/><br/>A komplex veszélyforrások elleni védelem jelenleg nyilvános előzetes verzióban érhető el.| Az ügyfeleknek maguknak kell felépíteniük ezt a védelmet.
| Vészhelyreállítás | Az automatikus biztonsági másolatokat a felhasználó által konfigurált [helyiredundáns vagy georedundáns tárolókban tárolja.](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal) A biztonsági mentések a kiszolgálót is visszaállíthatják egy adott időpontra. A megőrzési időszak 7 és 35 nap között van. A helyreállítás az Azure Portal használatával történik. | Az ügyfelek teljes körűen kezelik. A feladatok közé tartozik, de nem korlátozódik az ütemezésre, a tesztelésre, az archiválásra, a tárolásra és a megőrzésre. Egy további lehetőség, hogy egy Azure Recovery Services-tároló segítségével biztonsági másolatot avirtuális windows a virtuális gépek és adatbázisok. Ez a beállítás előzetes verzióban érhető el. |
| Teljesítménnyel kapcsolatos javaslatok | A rendszer által létrehozott használati naplófájlokon alapuló [teljesítményjavaslatokat](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) nyújt az ügyfeleknek. A javaslatok segítenek a számítási feladatok optimalizálásában.<br/><br/>A teljesítményre vonatkozó javaslatok jelenleg nyilvános előzetes verzióban vannak elérhetők. | Az ügyfelek kezelik |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Üzleti motivációk a PaaS vagy az IaaS kiválasztásához

Számos tényező befolyásolhatja a Döntést, hogy válassza a PaaS vagy IaaS a MariaDB-adatbázisok üzemeltetéséhez.

### <a name="cost"></a>Költségek

A korlátozott finanszírozás gyakran az elsődleges szempont, amely meghatározza az adatbázisok üzemeltetésének legjobb megoldását. Ez igaz, hogy ön egy startup, kevés készpénzzel, vagy egy olyan cég ben működő csapata, amely szigorú költségvetési megszorítások mellett működik. Ez a szakasz ismerteti az Azure számlázási és licencelési alapjait, ahogy azok az Azure Database for MariaDB és a MariaDB Azure-beli virtuális gépeken való használatára vonatkoznak.

#### <a name="billing"></a>Számlázás

A MariaDB-hez való Azure Database jelenleg szolgáltatásként érhető el több rétegben, különböző erőforrások áraival. Minden erőforrás számlázása óránként, rögzített díj. A jelenleg támogatott szolgáltatási szintekről, számítási méretekről és tárolási mennyiségekről a [virtuálismag-alapú vásárlási modellben](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers)talál legfrissebb információkat. Dinamikusan módosíthatja a szolgáltatási szinteket és a számítási méreteket, hogy megfeleljen az alkalmazás változatos átviteli igényeinek. A kimenő internetes forgalomért rendszeres [adatátviteli sebességgel számlázunk.](https://azure.microsoft.com/pricing/details/data-transfers/)

Az Azure Database for MariaDB segítségével a Microsoft automatikusan konfigurálja, kijavítja és frissíti az adatbázis-szoftvert. Ezek az automatizált műveletek csökkentik a felügyeleti költségeket. Emellett az Azure Database for MariaDB [beépített biztonsági mentési](https://docs.microsoft.com/azure/MariaDB/concepts-backup) képességekkel is rendelkezik. Ezek a képességek jelentős költségmegtakarítást eredményeznek, különösen akkor, ha nagy számú adatbázissal rendelkezik. Ezzel szemben a MariaDB az Azure-beli virtuális gépeken bármelyik MariaDB-verziót kiválaszthatja és futtathatja. Függetlenül attól, hogy milyen MariaDB verziót használ, a kiosztott virtuális gépért és a használt MariaDB licenctípus költségeiért kell fizetnie.

A MariaDB Azure Database beépített, magas rendelkezésre állást biztosít bármilyen csomópontszintű megszakításhoz, miközben fenntartja a szolgáltatás 99,99%-os SLA-garanciáját. A virtuális gépeken belüli magas rendelkezésre állású adatbázis-felhasználóknak azonban a MariaDB-adatbázisban elérhető magas rendelkezésre állási lehetőségeket, például a [MariaDB-replikációt](https://mariadb.com/kb/en/library/setting-up-replication/) kell használniuk. A támogatott magas rendelkezésre állású beállítás használata nem biztosít további SLA-t. De ez lehetővé teszi, hogy nagyobb, mint 99,99%-os adatbázis-rendelkezésre állás további költségek és felügyeleti költségek mellett.

Az árképzésről az alábbi cikkekben talál további információt:
* [Azure-adatbázis a MariaDB-díjszabáshoz](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Adminisztráció

Sok vállalkozás számára a felhőszolgáltatásra való áttérés legalább annyira a felügyelet bonyolultságának kiszervezéséről szól, mint a költségekről. Az IaaS és a PaaS segítségével a Microsoft:

- Az alapul szolgáló infrastruktúra felügyelete.
- Automatikusan replikálja az összes adatot, hogy a vész-helyreállítási.
- Konfigurálja és frissíti az adatbázis-szoftvert.
- Kezeli a terheléselosztást.
- Áttetsző feladatátvétel, ha kiszolgálóhiba van.

Az alábbi lista az egyes lehetőségek kel kapcsolatos felügyeleti szempontokat ismerteti:

* A MariaDB Azure Database segítségével továbbra is felügyelheti az adatbázist. De már nem kell kezelniaz adatbázis-motor, az operációs rendszer, vagy a hardver. A továbbra is felügyelhető elemek közé tartoznak például a következők:

  - Adatbázisok
  - Bejelentkezés
  - Index hangolása
  - Lekérdezés hangolása
  - Naplózás
  - Biztonság

  Emellett a magas rendelkezésre állás konfigurálása egy másik adatközpontban minimális, hogy nincs konfiguráció vagy felügyelet.

* A MariaDB az Azure-beli virtuális gépeken, teljes mértékben szabályozhatja az operációs rendszer és a MariaDB kiszolgálópéldány konfigurációját. A virtuális gép segítségével eldöntheti, hogy mikor kell frissíteni vagy frissíteni az operációs rendszert és az adatbázis-szoftvert. Azt is eldöntheti, hogy mikor kell telepíteni e további szoftvereket, például egy víruskereső alkalmazást. Egyes automatikus funkciók nagymértékben leegyszerűsítik a javítást, a biztonsági mentést és a magas rendelkezésre állást. Szabályozhatja a virtuális gép méretét, a lemezek számát és azok tárolási konfigurációit. További információ: [Virtual machine and cloud service sizes for Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)

### <a name="time-to-move-to-azure"></a>Ideje az Azure-ra költözni

* A MariaDB Azure Database a megfelelő megoldás a felhőtervezésű alkalmazásokhoz, amikor a fejlesztői hatékonyság és az új megoldások piacra kerülésének gyors ideje kritikus fontosságú. A DBA-hoz hasonló programozott funkciókkal a szolgáltatás felhőtervezők és fejlesztők számára is alkalmas, mivel csökkenti az alapul szolgáló operációs rendszer és adatbázis kezelésének szükségességét.

* Ha el szeretné kerülni az új helyszíni hardver beszerzésének idejét és költségeit, az Azure-beli virtuális gépeken futó MariaDB a megfelelő megoldás olyan alkalmazásokhoz, amelyekHez MariaDB-adatbázist vagy hozzáférést igényelnek a MariaDB-szolgáltatásokhoz Windows vagy Linux rendszeren. Ez a megoldás a meglévő helyszíni alkalmazások és adatbázisok átáttelepítésére is alkalmas az Azure ép állapotába, olyan esetekben, amikor az Azure Database for MariaDB nem megfelelő.

  Mivel nincs szükség a bemutató, az alkalmazás és az adatrétegek módosítására, időt és költségvetést takaríthat meg a meglévő megoldás újramegtervezéséhez. Ehelyett az összes megoldás Azure-ba való áttelepítésére összpontosíthat, és megszólíthat néhány olyan teljesítményoptimalizálást, amelyet az Azure platformnak szüksége lehet.

## <a name="next-steps"></a>További lépések

* Lásd: [Azure Database for MariaDB pricing](https://azure.microsoft.com/pricing/details/MariaDB/).
* Első lépések [az első kiszolgáló létrehozásával.](https://docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal)

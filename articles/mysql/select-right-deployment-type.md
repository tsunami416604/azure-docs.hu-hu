---
title: A Azure Database for MySQL megfelelő központi telepítési típusának kiválasztása
description: Ez a cikk azokat a tényezőket ismerteti, amelyeket figyelembe kell vennie, mielőtt üzembe helyezi a Azure Database for MySQL infrastruktúra-szolgáltatásként (IaaS) vagy a szolgáltatásként szolgáló platformként (Pásti).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 035a559f27d11a89dba1983f1bcaf406ef6a0d05
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331946"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Válassza ki a megfelelő MySQL-kiszolgáló lehetőséget az Azure-ban

Az Azure-ban a MySQL-kiszolgáló számítási feladatait üzemeltetett virtuálisgép-infrastruktúrában (IaaS) vagy üzemeltetett szolgáltatásként (Pásti) is futtathatják. A Péter több üzembe helyezési lehetőséggel rendelkezik, és az egyes üzembe helyezési lehetőségeken belül vannak szolgáltatási szintek. Ha a IaaS és a Péter közötti választást választja, el kell döntenie, hogy szeretné-e kezelni az adatbázist, a javítások alkalmazásával és a biztonsági másolatok készítésével, vagy ha ezeket a műveleteket az Azure-ba szeretné delegálni.

A döntés meghozatalakor vegye figyelembe a következő két lehetőséget:

- **Azure Database for MySQL**. Ez a lehetőség egy teljes körűen felügyelt MySQL-adatbázismotor, amely a MySQL Community Edition stabil verzióján alapul. Ez az Azure-beli felhőalapú platformon üzemelő, szolgáltatásként (DBaaS) elérhető, a (z) és a

  Ha felügyelt példánya van az Azure-ban, a MySQL-t használhatja olyan beépített funkciókkal, amelyek egyébként nagy méretű konfigurációt igényelnek, ha a MySQL-kiszolgáló vagy egy Azure-beli virtuális gépen van.

  Ha a MySQL-t használja szolgáltatásként, akkor a nagyobb felügyelet érdekében a megszakadás és a vertikális felskálázás lehetőségével kell fizetnie. Az önálló MySQL-kiszolgálótól eltérően a Azure Database for MySQL olyan további funkciókat is tartalmaz, mint például a beépített magas rendelkezésre állás, az intelligencia és a felügyelet.

- **MySQL Azure-beli virtuális gépeken**. Ez a lehetőség a IaaS iparági kategóriába tartozik. Ezzel a szolgáltatással a MySQL-kiszolgálót egy teljes körűen felügyelt virtuális gépen futtathatja az Azure Cloud platformon. A MySQL legújabb verzióit és kiadásait IaaS virtuális gépre lehet telepíteni.

  A Azure Database for MySQL legjelentősebb különbsége, hogy az Azure-beli virtuális gépeken a MySQL felügyeli az adatbázis-motort. Ez a szabályozás azonban a virtuális gépek és számos adatbázis-felügyeleti (DBA) feladat felügyeletének a felelősségi körébe tartozik. Ezen feladatok közé tartozik az adatbázis-kiszolgálók, az adatbázis-helyreállítás és a magas rendelkezésre állású kialakítás fenntartása és javítása.

A fenti lehetőségek közötti fő különbségek a következő táblázatban láthatók:

|            | A MySQL-hez készült Azure Database | MySQL Azure-beli virtuális gépeken    |
|:-------------------|:-----------------------------|:--------------------|
| Szolgáltatásiszint-szerződés (SLA)                | A 99,99%-os rendelkezésre állást biztosító SLA-t kínál| Akár 99,95%-os rendelkezésre állást biztosít két vagy több példányban ugyanabban a rendelkezésre állási csoporton belül.<br/><br/>99,9%-os rendelkezésre állás egyetlen példányos virtuális géppel Premium Storage használatával.<br/><br/>99,99% a több rendelkezésre állási csoport több példányával rendelkező Availability Zones használatával.<br/><br/>Lásd a [Virtual Machines SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)-t. |
| Operációs rendszer javítása        | Automatikus  | Ügyfelek által felügyelt |
| MySQL-javítás     | Automatikus  | Ügyfelek által felügyelt |
| Magas rendelkezésre állás | A magas rendelkezésre állású (HA) modell olyan beépített feladatátvételi mechanizmusokon alapul, amelyekben csomópont szintű megszakítás történik. Ilyen esetekben a szolgáltatás automatikusan létrehoz egy új példányt, és csatolja a tárolót ehhez a példányhoz. | Az ügyfelek a magas rendelkezésre állás kialakítását, tesztelését és karbantartását is elismerik. A képességek lehetnek többek között a feladatátvételi fürtszolgáltatás, a folyamatos replikálás, a naplózás vagy a tranzakciós replikáció.|
| Zóna redundancia | Jelenleg nem támogatott | Az Azure-beli virtuális gépek különböző rendelkezésre állási zónákban való futtatásra állíthatók be. Helyszíni megoldás esetén az ügyfeleknek saját másodlagos adatközpontot kell létrehozniuk, kezelnie és karbantartani.|
| Hibrid forgatókönyvek | A [felhőbe irányuló replikálás](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)segítségével egy külső MySQL-kiszolgáló adatait szinkronizálhatja a Azure Database for MySQL szolgáltatásba. A külső kiszolgáló lehet helyszíni, virtuális gépek vagy más felhőalapú szolgáltatók által üzemeltetett adatbázis-szolgáltatás.<br/><br/> Az [olvasási replika](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) funkcióval egy Azure Database for MySQL főkiszolgálóról legfeljebb öt írásvédett másodpéldány-kiszolgálóra lehet replikálni az adatait. A replikák vagy ugyanabban az Azure-régióban vagy régiókban találhatók. Az írásvédett replikák aszinkron módon frissülnek a BinLog replikációs technológiával.<br/><br/>A régiók közötti olvasási replikálás jelenleg nyilvános előzetes verzióban érhető el.| Ügyfelek által felügyelt
| Biztonsági mentés és helyreállítás | A automatikusan létrehozza a [kiszolgáló biztonsági másolatait](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) , és tárolja azokat a helyileg redundáns vagy földrajzilag redundáns, felhasználó által konfigurált tárolóban. A szolgáltatás teljes, differenciális és tranzakciónapló-alapú biztonsági mentéseket tart | Ügyfelek által felügyelt |
| Adatbázis-műveletek figyelése | A lehetővé teszi az ügyfeleknek, hogy [riasztásokat állítsanak be](https://docs.microsoft.com/azure/mysql/concepts-monitoring) az adatbázis-műveletre, és a küszöbértékek elérésekor járjanak. | Ügyfelek által felügyelt |
| Komplex veszélyforrások elleni védelem | [Fejlett veszélyforrások elleni védelmet](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal)nyújt. Ez a védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. | Az ügyfeleknek saját maguknak kell elkészíteniük ezt a védelmet.
| Vészhelyreállítás | Az automatizált biztonsági mentéseket a felhasználó által konfigurált [helyileg redundáns vagy földrajzilag redundáns tárolóban](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)tárolja. A biztonsági mentések egy adott időpontban is visszaállíthatják a kiszolgálót. A megőrzési időtartam 7 és 35 nap között van. A visszaállítás a Azure Portal használatával valósul meg. | Az ügyfelek teljes körűen kezelik. A felelősség magában foglalja az ütemezési, tesztelési, archiválási, tárolási és adatmegőrzési feladatokat. Egy másik lehetőség egy Azure Recovery Services-tároló használata az Azure-beli virtuális gépek és adatbázisok biztonsági mentésére a virtuális gépeken. Ez a beállítás előzetes verzióban érhető el. |
| Teljesítménnyel kapcsolatos javaslatok | [Teljesítményre vonatkozó javaslatokat](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) biztosít az ügyfeleknek a rendszer által létrehozott használati naplófájlok alapján. A javaslatok segítenek optimalizálni a munkaterheléseket. | Ügyfelek által felügyelt |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>A Pásti vagy a IaaS kiválasztásának üzleti motivációi

A MySQL-adatbázisok üzemeltetéséhez több tényező is befolyásolhatja a IaaS-t vagy a kiválasztását.

### <a name="cost"></a>Költség

A korlátozott finanszírozás gyakran az elsődleges szempont, amely meghatározza az adatbázisok üzemeltetésének legjobb megoldását. Ez akkor is igaz, ha kevés pénzzel vagy csapattal rendelkezik egy olyan vállalatnál, amely szűk költségvetési megkötések keretében működik. Ez a szakasz a számlázási és licencelési alapismereteket ismerteti az Azure-ban, mivel azok az Azure-beli virtuális gépeken a Azure Database for MySQL és a MySQL

#### <a name="billing"></a>Számlázás

Azure Database for MySQL jelenleg számos különböző szinten érhető el szolgáltatásként, és az erőforrásokra eltérő árak érvényesek. Az összes erőforrás számlázása óránként, rögzített sebességgel történik. A jelenleg támogatott szolgáltatási rétegekkel, számítási méretekkel és tárolási mennyiségekkel kapcsolatos legfrissebb információkért lásd: [virtuális mag-alapú vásárlási modell](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). A szolgáltatási szintek és a számítási méretek dinamikusan állíthatók be úgy, hogy megfeleljenek az alkalmazás eltérő átviteli igényeinek. A kimenő internetes forgalom díját a normál [adatátviteli díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/)alapján számítjuk fel.

A Azure Database for MySQL a Microsoft automatikusan konfigurálja, kijavításokat végez, és frissíti az adatbázis szoftverét. Ezek az automatizált műveletek csökkentik az adminisztrációs költségeket. Emellett a Azure Database for MySQL [beépített biztonsági mentési](https://docs.microsoft.com/azure/mysql/concepts-backup) képességekkel rendelkezik. Ezeknek a képességeknek köszönhetően jelentős költségmegtakarítást érhet el, különösen akkor, ha nagy számú adatbázissal rendelkezik. Ezzel szemben az Azure-beli virtuális gépeken futó MySQL-sel bármelyik MySQL-verziót kiválaszthatja és futtathatja. Függetlenül attól, hogy milyen MySQL-verziót használ, fizetnie kell a kiépített virtuális gépen, valamint a felhasznált MySQL-licencek költségeiért.

A Azure Database for MySQL beépített magas rendelkezésre állást biztosít bármilyen csomópont szintű megszakításhoz, miközben továbbra is fenntartja a szolgáltatás 99,99%-os SLA-garanciáját. A virtuális gépeken belüli magas rendelkezésre állás érdekében azonban az ügyfeleknek a magas rendelkezésre állási lehetőségeket, például a MySQL-adatbázison elérhető [MySQL-replikációt](https://dev.mysql.com/doc/refman/8.0/en/replication.html) kell használniuk. A támogatott magas rendelkezésre állási lehetőség használata nem biztosít további SLA-t. Ugyanakkor további költségekkel és adminisztratív terheléssel több mint 99,99%-os adatbázis-elérhetőséget érhet el.

A díjszabással kapcsolatos további információkért tekintse meg a következő cikkeket:
* [Díjszabás Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Felügyelet

Sok vállalat esetében a felhőalapú szolgáltatásba való áttérésre vonatkozó döntés annyit jelent, mint az adminisztráció bonyolultságának kiszervezése. A IaaS és a Pásti, a Microsoft:

- A mögöttes infrastruktúra felügyelete.
- A automatikusan replikálja az összes adatforrást a vész-helyreállítás biztosításához.
- Az adatbázis szoftverének konfigurálása és frissítése.
- Kezeli a terheléselosztást.
- Transzparens feladatátvételt hajt végre, ha kiszolgálóhiba történt.

A következő lista az egyes lehetőségek felügyeleti szempontjait ismerteti:

* A Azure Database for MySQL használatával továbbra is felügyelheti az adatbázist. Azonban már nincs szükség az adatbázismotor, az operációs rendszer vagy a hardver felügyeletére. Példák a továbbra is felügyelhető elemekre:

  - Adatbázis-kezelés
  - Bejelentkezés
  - Index-finomhangolás
  - Lekérdezés finomhangolása
  - Naplózás
  - Biztonság

  Emellett a magas rendelkezésre állás konfigurálása egy másik adatközponthoz minimálisan szükséges a konfiguráció vagy a felügyelet nélkül.

* Az Azure-beli virtuális gépeken futó MySQL-sel teljes mértékben szabályozhatja az operációs rendszert és a MySQL-kiszolgáló példányának konfigurációját. A virtuális géppel eldöntheti, hogy mikor frissítse vagy frissítse az operációs rendszert és az adatbázis szoftverét. Azt is eldöntheti, hogy mikor telepítsen további szoftvereket, például vírusirtó alkalmazást. Bizonyos automatizált funkciók jelentősen leegyszerűsítik a javítást, a biztonsági mentést és a magas rendelkezésre állást. A virtuális gép méretét, a lemezek számát és a tárolási konfigurációkat is szabályozhatja. További információ: [virtuális gépek és felhőalapú szolgáltatások mérete az Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)-ban.

### <a name="time-to-move-to-azure"></a>Az Azure-ba való áttérés ideje

* A Azure Database for MySQL a megfelelő megoldás a felhőben tervezett alkalmazások számára, ha a fejlesztő és az új megoldások piacra jutásának gyors időpontja kritikus fontosságú. A DBA-hez hasonló programozott funkciókkal a szolgáltatás alkalmas a Felhőbeli építészek és fejlesztők számára, mivel csökkenti a mögöttes operációs rendszer és adatbázis felügyeletének szükségességét.

* Ha el szeretné kerülni az új helyszíni hardver beszerzésének idejét és költségét, a MySQL az Azure-beli virtuális gépeken a megfelelő megoldás olyan alkalmazásokhoz, amelyek MySQL-adatbázist igényelnek, vagy Windows vagy Linux rendszerű MySQL-funkciókhoz férnek hozzá. Ez a megoldás alkalmas a meglévő helyszíni alkalmazások és adatbázisok az Azure-ba való áttelepítésére is, olyan esetekben, amikor a Azure Database for MySQL gyenge illeszkedést biztosít.

  Mivel nincs szükség a bemutató, az alkalmazás és az adatrétegek módosítására, időt és költségvetést takaríthat meg a meglévő megoldás újratervezése során. Ehelyett a megoldások Azure-ba való áttelepítésére és az Azure-platform által igényelt teljesítmény-optimalizálásokra koncentrálhat.

## <a name="next-steps"></a>Következő lépések

* Lásd: [Azure Database for MySQL díjszabása](https://azure.microsoft.com/pricing/details/MySQL/).
* Első lépésként [létrehozhatja az első kiszolgálóját](https://review.docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).

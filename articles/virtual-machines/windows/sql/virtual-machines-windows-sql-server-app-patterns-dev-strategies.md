---
title: SQL Server alkalmazásminták a virtuális gépeken | Microsoft dokumentumok
description: Ez a cikk az Azure-beli virtuális gépekEN az SQL Server alkalmazásmintáit ismerteti. Ez biztosítja a megoldás építészek és fejlesztők alapot a jó alkalmazás architektúra és design.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: cbc2bfbb68910c3eb12352bebb575c4548885a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70124031"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Azure-beli virtuális gépeken futó SQL Server – alkalmazásminták és fejlesztési stratégiák
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Összegzés:
Annak meghatározása, hogy az SQL-Server-alapú alkalmazásokhoz milyen alkalmazásmintát vagy mintákat használjon az Azure-környezetben, fontos tervezési döntés, és alapos ismerete szükséges az SQL Server és az Azure egyes infrastruktúra-összetevői együttműködésének alapos megértéséhez. Az Azure Infrastructure Services SQL Server szolgáltatásával könnyedén áttelepítheti, karbantarthatja és figyelheti a Windows Serverre épülő meglévő SQL Server-alkalmazásokat az Azure-beli virtuális gépekre.

A cikk célja, hogy a megoldástervezők és -fejlesztők számára a jó alkalmazásarchitektúra és -tervezés alapjait biztosítsa, amelyeket követhetnek a meglévő alkalmazások Azure-ba való áttelepítésekor, valamint új alkalmazások azure-beli fejlesztésekor.

Minden egyes alkalmazásminta, talál egy helyszíni forgatókönyv, a megfelelő felhőalapú megoldás, és a kapcsolódó technikai javaslatokat. Emellett a cikk ismerteti az Azure-specifikus fejlesztési stratégiákat, így az alkalmazások megfelelően tervezhetők. A számos lehetséges alkalmazásminta miatt ajánlott, hogy az építészek és a fejlesztők az alkalmazások és a felhasználók számára legmegfelelőbb mintát válasszák ki.

**Műszaki közreműködők:** Luis Carlos Vargas Hering, Madhan Arumugam Ramakrishnan

**Technikai felülvizsgálók:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Bevezetés
Az n-szintű alkalmazások számos típusát fejlesztheti, ha a különböző alkalmazásrétegek összetevőit különböző gépeken és különálló összetevőkön választja el egymástól. Például elhelyezheti az ügyfélalkalmazás és az üzleti szabályok összetevőit egy gépben, az előtér-webrétegben és az adatelérési réteg összetevőiben egy másik gépen, és egy háttéradatbázis-réteget egy másik gépen. Ez a fajta strukturálás segít elkülöníteni az egyes szinteket egymástól. Ha módosítja, hogy az adatok honnan származnak, nem kell módosítania az ügyfelet vagy a webalkalmazást, csak az adatelérési réteg összetevőit.

Egy tipikus *n-szintű* alkalmazás tartalmazza a megjelenítési szintet, az üzleti szintet és az adatréteget:

| Szint | Leírás |
| --- | --- |
| **Megjelenítés** |A *bemutatószint* (webes réteg, előtérréteg) az a réteg, amelyben a felhasználók interakcióba lépnek egy alkalmazással. |
| **Üzleti** |Az *üzleti szint* (középső réteg) az a réteg, amelyet a megjelenítési szint és az adatréteg egymással való kommunikációra használ, és amely tartalmazza a rendszer alapvető funkcióit. |
| **Adatok** |Az *adatréteg* alapvetően az a kiszolgáló, amely az alkalmazás adatait tárolja (például egy SQL Server t futtató kiszolgáló). |

Az alkalmazásrétegek az alkalmazás funkcióinak és összetevőinek logikai csoportosítását írják le; mivel a szintek a funkciók és összetevők fizikai eloszlását írják le külön fizikai kiszolgálókon, számítógépeken, hálózatokon vagy távoli helyeken. Az alkalmazások rétegei ugyanazon a fizikai számítógépen (ugyanazon a szinten) találhatók, vagy külön számítógépeken (n-réteg) is eloszthatók, és az egyes rétegek összetevői jól meghatározott felületeken keresztül kommunikálnak más rétegek összetevőivel. A kifejezésszint fizikai eloszlási mintákra, például kétrétegű, háromrétegű és n szintű regondolva is felfogható. A **kétrétegű alkalmazásminta** két alkalmazásréteget tartalmaz: az alkalmazáskiszolgálót és az adatbázis-kiszolgálót. A közvetlen kommunikáció az alkalmazáskiszolgáló és az adatbázis-kiszolgáló között történik. Az alkalmazáskiszolgáló web- és üzletiréteg-összetevőket is tartalmaz. A **háromrétegű alkalmazásmintában**három alkalmazásréteg található: webkiszolgáló, alkalmazáskiszolgáló, amely az üzleti logikai réteget és/vagy az üzleti szint adatelérési összetevőit tartalmazza, valamint az adatbázis-kiszolgáló. A webkiszolgáló és az adatbázis-kiszolgáló közötti kommunikáció az alkalmazáskiszolgálón keresztül történik. Az alkalmazásrétegekről és -rétegekről a [Microsoft alkalmazásarchitektúra-útmutatóban talál](https://msdn.microsoft.com/library/ff650706.aspx)részletes információt.

Mielőtt elkezdené olvasni ezt a cikket, ismernie kell az SQL Server és az Azure alapvető fogalmait. További információt az [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), SQL Server az Azure virtuális gépekben és Azure.com című [témakörben](virtual-machines-windows-sql-server-iaas-overview.md) [talál.](https://azure.microsoft.com/)

Ez a cikk számos olyan alkalmazásmintát ismertet, amelyek alkalmasak lehetnek az egyszerű alkalmazásokhoz, valamint a rendkívül összetett vállalati alkalmazásokhoz. Az egyes minták részletezése előtt javasoljuk, hogy ismerkedjen meg az Azure-ban elérhető adattárolási szolgáltatásokkal, például az [Azure Storage-](../../../storage/common/storage-introduction.md)és [Az Azure SQL Database-rel](../../../sql-database/sql-database-technical-overview.md)és az SQL Server egy Azure virtuális [gépen](virtual-machines-windows-sql-server-iaas-overview.md)szolgáltatással. Az alkalmazások számára a legjobb tervezési döntések meghozatalához ismerje meg, hogy mikor kell egyértelműen használnia melyik adattárolási szolgáltatást.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Válassza az SQL Server lehetőséget egy Azure virtuális gépen, ha:
* Az SQL Server és a Windows rendszerben is ellenőrizni kell. Ez például magában foglalhatja az SQL Server verzióját, a speciális gyorsjavításokat, a teljesítménykonfigurációt stb.
* Teljes kompatibilitásra van szüksége a helyszíni SQL Server kiszolgálóval, és a meglévő alkalmazásokat az Azure-ba szeretné áthelyezni.
* Szeretné kihasználni az Azure-környezet képességeit, de az Azure SQL Database nem támogatja az alkalmazás által igényelt összes szolgáltatást. Ez a következő területeket foglalhatja magában:
  
  * **Adatbázis mérete**: A cikk frissítésekor az SQL Database legfeljebb 1 TB adatadatbázist támogat. Ha az alkalmazás több mint 1 TB-nyi adatot igényel, és nem szeretne egyéni szilánkolási megoldásokat megvalósítani, ajánlott az SQL Server használata egy Azure virtuális gépen. A legfrissebb információkért lásd: [Az Azure SQL-adatbázis horizontális felskálázása](https://msdn.microsoft.com/library/azure/dn495641.aspx), [A DTU-alapú vásárlási modell](../../../sql-database/sql-database-service-tiers-dtu.md)és a [virtuálismag-alapú vásárlási modell](../../../sql-database/sql-database-service-tiers-vcore.md)(előzetes verzió).
  * **HIPAA-megfelelőség:** Az egészségügyi ügyfelek és a független szoftverszállítók (ISV-k) az [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) helyett az SQL Server alkalmazást választhatják az Azure [virtuális gépeken,](virtual-machines-windows-sql-server-iaas-overview.md) mivel az Azure virtuális gépen található SQL Server relevanst a HIPAA üzleti partnerszerződése (BAA) fedezi. A megfelelőségről a [Microsoft Azure Adatvédelmi központ: Megfelelőség](https://azure.microsoft.com/support/trust-center/compliance/)című témakörben talál további információt.
  * **Példányszintű szolgáltatások:** Az SQL Database jelenleg nem támogatja az adatbázison kívül élő szolgáltatásokat (például csatolt kiszolgálók, ügynöki feladatok, FileStream, Service Broker stb.). További információt az [Azure SQL-adatbázis irányelvei és korlátai](https://msdn.microsoft.com/library/azure/ff394102.aspx)című témakörben talál.

## <a name="1-tier-simple-single-virtual-machine"></a>1 rétegű (egyszerű): egyetlen virtuális gép
Ebben az alkalmazásmintában az SQL Server-alkalmazást és -adatbázist egy önálló virtuális gépre telepíti az Azure-ban. Ugyanaz a virtuális gép tartalmazza az ügyfél-/webalkalmazást, az üzleti összetevőket, az adatelérési réteget és az adatbázis-kiszolgálót. A bemutató, az üzleti és az adatelérési kód logikailag elkülönül, de fizikailag egykiszolgálós gépen található. A legtöbb ügyfél ezzel az alkalmazásmintával kezdi, majd további webes szerepkörök vagy virtuális gépek hozzáadásával horizontálisfelskálázódik a rendszerhez.

Ez az alkalmazásminta akkor hasznos, ha:

* Egyszerű áttelepítést szeretne végrehajtani az Azure platformra, hogy kimassza, hogy a platform megfelel-e az alkalmazás követelményeinek, vagy sem.
* Szeretné megtartani az összes alkalmazásszint üzemelteti ugyanabban a virtuális gép ugyanabban az Azure-adatközpontban, hogy csökkentse a rétegek közötti késéscsökkentése érdekében.
* Gyorsan szeretné kiépíteni a fejlesztési és tesztelési környezeteket rövid ideig.
* A különböző munkaterhelési szinteken stressztesztelést szeretne végezni, de ugyanakkor nem szeretne sok fizikai gépet birtokolni és karbantartani.

Az alábbi ábra bemutatja egy egyszerű helyszíni forgatókönyvet, és hogyan telepítheti a felhőalapú megoldásegyetlen virtuális gép az Azure-ban.

![Egyrétegű alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Az üzleti réteg (üzleti logika és adatelérési összetevők) üzembe helyezése a bemutatóréteggel azonos fizikai rétegen maximalizálhatja az alkalmazások teljesítményét, kivéve, ha méretezhetőség vagy biztonsági aggályok miatt külön réteget kell használnia.

Mivel ez egy nagyon gyakori minta kezdeni, előfordulhat, hogy a következő cikk áttelepítési hasznos az adatok áthelyezése az SQL Server virtuális gép: [Adatbázis áttelepítése az SQL Server egy Azure virtuális gép.](virtual-machines-windows-migrate-sql.md)

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 rétegű (egyszerű): több virtuális gép
Ebben az alkalmazásmintában egy háromrétegű alkalmazást telepít az Azure-ban azáltal, hogy minden egyes alkalmazásréteget egy másik virtuális gépre helyez el. Ez rugalmas környezetet biztosít az egyszerű horizontális felskálázási és horizontális felskálázási forgatókönyvekhez. Ha az egyik virtuális gép tartalmazza az ügyfél-/webalkalmazást, a másik az üzleti összetevőket, a másik pedig az adatbázis-kiszolgálót üzemelteti.

Ez az alkalmazásminta akkor hasznos, ha:

* Összetett adatbázis-alkalmazások azure-beli virtuális gépekre való áttelepítését szeretné végrehajtani.
* Azt szeretné, hogy a különböző alkalmazásszintek különböző régiókban legyenek tárolva. Előfordulhat például, hogy olyan megosztott adatbázisokkal rendelkezik, amelyek jelentéskészítési célokra több régióban vannak telepítve.
* A vállalati alkalmazásokat a helyszíni virtualizált platformokról az Azure virtuális gépekre szeretné áthelyezni. A vállalati alkalmazásokról a [Mi az a vállalati alkalmazások](https://msdn.microsoft.com/library/aa267045.aspx)című témakörben található részletes en.
* Gyorsan szeretné kiépíteni a fejlesztési és tesztelési környezeteket rövid ideig.
* A különböző munkaterhelési szinteken stressztesztelést szeretne végezni, de ugyanakkor nem szeretne sok fizikai gépet birtokolni és karbantartani.

Az alábbi ábra bemutatja, hogyan helyezhet el egy egyszerű háromrétegű alkalmazást az Azure-ban azáltal, hogy az egyes alkalmazásszinteket egy másik virtuális gépbe helyezi.

![Háromrétegű alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Ebben az alkalmazásmintában minden rétegben csak egy virtuális gép (VM) található. Ha több virtuális gép az Azure-ban, azt javasoljuk, hogy hozzon létre egy virtuális hálózatot. [Az Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) létrehoz egy megbízható biztonsági határt, és lehetővé teszi a virtuális gépek számára, hogy kommunikáljanak egymás között a privát IP-címen keresztül. Ezenkívül mindig győződjön meg arról, hogy az összes internetkapcsolat csak a megjelenítési szintre kerül. Ha ezt az alkalmazásmintát követi, kezelje a hálózati biztonsági csoport szabályait a hozzáférés szabályozásához. További információ: [Külső hozzáférés engedélyezése a virtuális géphez az Azure Portalhasználatával.](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

A diagramon az INTERNET protokollok lehetnek TCP, UDP, HTTP vagy HTTPS.

> [!NOTE]
> Virtuális hálózat beállítása az Azure-ban ingyenes. Azonban a helyszíni vpn-átjáróért díjat számítunk fel. Ez a díj a kapcsolat kiépített és rendelkezésre álló idejének alapján történik.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>Két- és 3-rétegű bemutatószintű horizontális felskálával
Ebben az alkalmazásmintában kétrétegű vagy háromrétegű adatbázis-alkalmazást telepít az Azure virtuális gépekre úgy, hogy az egyes alkalmazásszinteket egy másik virtuális gépbe helyezi. Emellett a bejövő ügyfélkérelmek megnövekedett mennyisége miatt horizontálisfelskálázhatja a megjelenítési szintet.

Ez az alkalmazásminta akkor hasznos, ha:

* A vállalati alkalmazásokat a helyszíni virtualizált platformokról az Azure virtuális gépekre szeretné áthelyezni.
* A bejövő ügyfélkérelmek megnövekedett mennyisége miatt ki szeretné bővíteni a megjelenítési szintet.
* Gyorsan szeretné kiépíteni a fejlesztési és tesztelési környezeteket rövid ideig.
* A különböző munkaterhelési szinteken stressztesztelést szeretne végezni, de ugyanakkor nem szeretne sok fizikai gépet birtokolni és karbantartani.
* Olyan infrastruktúra-környezetet szeretne birtokolni, amely igény szerint fel-le skálázható.

Az alábbi ábra bemutatja, hogyan helyezheti el az alkalmazásszinteket több virtuális gépben az Azure-ban a megjelenítési szint skálázásával a bejövő ügyfélkérelmek megnövekedett mennyisége miatt. Ahogy az ábrán látható, az Azure Load Balancer felelős a forgalom több virtuális gép közötti elosztásáért, és annak meghatározásáért, hogy melyik webkiszolgálóhoz csatlakozzon. Ha a webkiszolgálók több példánya van a terheléselosztó mögött, az biztosítja a megjelenítési szint magas rendelkezésre állását.

![Alkalmazásminta – a bemutatóréteg kiszélesedése](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Ajánlott eljárások kétrétegű, 3 rétegű vagy n szintű mintákhoz, amelyek egy rétegben több virtuális géptel rendelkeznek
Javasoljuk, hogy helyezze el az azonos szinthez tartozó virtuális gépeket ugyanabban a felhőszolgáltatásban és ugyanabban a rendelkezésre állási csoportban. Helyezzen el például webkiszolgálókat a **CloudService1** és **az AvailabilitySet1 szolgáltatásban,** valamint adatbázis-kiszolgálókat a **CloudService2** és **az AvailabilitySet2 alkalmazásban.** Az Azure-ban rendelkezésre álló készlet lehetővé teszi, hogy a magas rendelkezésre állású csomópontokat külön tartalék tartományokba helyezze, és frissítse a tartományokat.

Egy réteg több virtuálisgép-példányának kihasználásához konfigurálnia kell az Azure Load Balancer-t az alkalmazásszintek között. A terheléselosztó konfigurálásához az egyes rétegekben hozzon létre egy terheléselosztási végpontot az egyes rétegek virtuális gépein külön-külön. Egy adott szinthez először hozzon létre virtuális gépeket ugyanabban a felhőszolgáltatásban. Ez biztosítja, hogy ugyanazt a nyilvános virtuális IP-címet. Ezután hozzon létre egy végpontot az adott réteg egyik virtuális gépén. Ezután rendelje hozzá ugyanazt a végpontot a többi virtuális gép, hogy a csomag terheléselosztás. Egy terheléselosztásos készlet létrehozásával több virtuális gép között osztja el a forgalmat, és lehetővé teszi a terheléselosztó számára, hogy meghatározza, melyik csomópontot kell csatlakoztatni, ha egy háttér-virtuálisgép-csomópont meghibásodik. Ha például a webkiszolgálók több példánya van egy terheléselosztó mögött, az biztosítja a megjelenítési szint magas rendelkezésre állását.

Ajánlott eljárásként mindig győződjön meg arról, hogy minden internetkapcsolat először a bemutatószintre kerül. A bemutatóréteg hozzáfér az üzleti réteghez, majd az üzleti szint az adatréteghez. A bemutatóréteghez való hozzáférés engedélyezéséről további információt a [Virtuális gép külső elérésének engedélyezése az Azure Portalon keresztül című témakörben talál.](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Vegye figyelembe, hogy az Azure-ban a terheléselosztó hasonlóan működik a helyszíni környezetben. További információ: [Terheléselosztás az Azure-infrastruktúra-szolgáltatásokhoz.](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Emellett azt javasoljuk, hogy hozzon létre egy magánhálózatot a virtuális gépek az Azure Virtuális hálózat használatával. Ez lehetővé teszi számukra, hogy kommunikáljanak egymás között a privát IP-címen keresztül. További információ: [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>Kétrétegű és 3 rétegű üzleti szintű horizontális felskálázható
Ebben az alkalmazásmintában kétrétegű vagy háromrétegű adatbázis-alkalmazást telepít az Azure virtuális gépekre úgy, hogy az egyes alkalmazásszinteket egy másik virtuális gépbe helyezi. Emellett előfordulhat, hogy az alkalmazáskiszolgáló-összetevőket több virtuális gépen szeretné terjeszteni az alkalmazás összetettsége miatt.

Ez az alkalmazásminta akkor hasznos, ha:

* A vállalati alkalmazásokat a helyszíni virtualizált platformokról az Azure virtuális gépekre szeretné áthelyezni.
* Az alkalmazáskiszolgáló összetevőit az alkalmazás összetettsége miatt több virtuális gépre szeretné terjeszteni.
* Azt szeretné, hogy az üzleti logika nehéz helyszíni LOB (üzletági) alkalmazások Azure virtuális gépek. A lob-alkalmazások olyan kritikus fontosságú számítógépes alkalmazások, amelyek létfontosságúak a vállalat futtatásához, például a könyveléshez, az emberi erőforrásokhoz (HR), a bérszámfejtéshez, az ellátásilánc-kezeléshez és az erőforrás-tervezési alkalmazásokhoz.
* Gyorsan szeretné kiépíteni a fejlesztési és tesztelési környezeteket rövid ideig.
* A különböző munkaterhelési szinteken stressztesztelést szeretne végezni, de ugyanakkor nem szeretne sok fizikai gépet birtokolni és karbantartani.
* Olyan infrastruktúra-környezetet szeretne birtokolni, amely igény szerint fel-le skálázható.

Az alábbi ábrán egy helyszíni forgatókönyv és a felhőalapú megoldás látható. Ebben a forgatókönyvben az alkalmazásszinteket több virtuális gépben helyezi el az Azure-ban az üzleti szint horizontális felskálázásával, amely tartalmazza az üzleti logikai réteget és az adatelérési összetevőket. Ahogy az ábrán látható, az Azure Load Balancer felelős a forgalom több virtuális gép közötti elosztásáért, és annak meghatározásáért, hogy melyik webkiszolgálóhoz csatlakozzon. Ha az alkalmazáskiszolgálók több példánya van egy terheléselosztó mögött, az biztosítja az üzleti szint magas rendelkezésre állását. További információ: [Gyakorlati tanácsok a 2-rétegű, 3 rétegű vagy n-tier alkalmazásminták, amelyek több virtuális gép egy rétegben.](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier)

![Alkalmazásminta az üzleti szint horizontális felskálázásával](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 és 3 rétegű prezentációs és üzleti szintekkel kibővített és HADR
Ebben az alkalmazásmintában két- vagy 3rétegű adatbázis-alkalmazást telepít az Azure virtuális gépekre a bemutatószint (webkiszolgáló) és az üzleti szint (alkalmazáskiszolgáló) összetevőinek több virtuális gépen történő elosztásával. Emellett magas rendelkezésre állású és vész-helyreállítási megoldásokat valósít meg az azure-beli virtuális gépekadatbázisaihoz.

Ez az alkalmazásminta akkor hasznos, ha:

* Az SQL Server magas rendelkezésre állású és vész-helyreállítási képességeinek megvalósításával nagyvállalati alkalmazásokat szeretne áthelyezni a helyszíni virtualizált platformokról az Azure-ba.
* A bejövő ügyfélkérelmek megnövekedett mennyisége és az alkalmazás összetettsége miatt ki szeretné bővíteni a megjelenítési szintet és az üzleti szintet.
* Gyorsan szeretné kiépíteni a fejlesztési és tesztelési környezeteket rövid ideig.
* A különböző munkaterhelési szinteken stressztesztelést szeretne végezni, de ugyanakkor nem szeretne sok fizikai gépet birtokolni és karbantartani.
* Olyan infrastruktúra-környezetet szeretne birtokolni, amely igény szerint fel-le skálázható.

Az alábbi ábrán egy helyszíni forgatókönyv és a felhőalapú megoldás látható. Ebben a forgatókönyvben horizontálisan kikell bővíteni a bemutatószintet és az üzleti szint összetevőit több virtuális gépben az Azure-ban. Emellett magas rendelkezésre állású és vész-helyreállítási (HADR) technikákat valósít meg az Azure-beli SQL Server-adatbázisokhoz.

Ha egy alkalmazás több példányát futtatja különböző virtuális gépeken, győződjön meg arról, hogy terheléselosztási kérelmeket futtat rajtuk keresztül. Ha több virtuális géppel rendelkezik, meg kell győződnie arról, hogy az összes virtuális gép elérhető és egy adott időpontban fut. Ha konfigurálja a terheléselosztást, az Azure Load Balancer nyomon követi a virtuális gépek állapotát, és megfelelően irányítja a bejövő hívásokat a kifogástalan ultrás működő virtuálisgép-csomópontokra. A virtuális gépek terheléselosztásának beállításáról az [Azure-infrastruktúra-szolgáltatások terheléselosztása](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakörben talál további információt. Ha a terheléselosztó mögött több web- és alkalmazáskiszolgáló található, az biztosítja a bemutató és az üzleti szintek magas rendelkezésre állását.

![Horizontális felskálázás és magas rendelkezésre állás](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Az SQL HADR-t igénylő alkalmazásminták ra vonatkozó gyakorlati tanácsok
Ha az SQL Server magas rendelkezésre állású és vész-helyreállítási megoldásokat állít be az Azure virtuális gépeken, az [Azure Virtuális hálózat](../../../virtual-network/virtual-networks-overview.md) használatával kötelező virtuális hálózatot létrehozni a virtuális gépek számára.  A virtuális hálózaton belüli virtuális gépek stabil privát IP-címmel rendelkeznek még a szolgáltatás állásideje után is, így elkerülheti a DNS-névfeloldásához szükséges frissítési időt. Emellett a virtuális hálózat lehetővé teszi, hogy kiterjessze a helyszíni hálózat az Azure-ra, és létrehoz egy megbízható biztonsági határt. Ha például az alkalmazás vállalati tartománykorlátozásokkal rendelkezik (például Windows-hitelesítés, Active Directory), az [Azure Virtuális hálózat](../../../virtual-network/virtual-networks-overview.md) beállítása szükséges.

Az Azure-beli éles kódot futtató ügyfelek többsége elsődleges és másodlagos replikákat is megtart az Azure-ban.

A magas rendelkezésre állással és a vészhelyreállítással kapcsolatos technikákkal kapcsolatos átfogó információkért és oktatóanyagokért lásd: [Magas rendelkezésre állás ú és vész-helyreállítási az SQL Server hez az Azure virtuális gépekben.](virtual-machines-windows-sql-high-availability-dr.md)

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>Két- és 3rétegű az Azure virtuális gépek és a felhőszolgáltatások használatával
Ebben az alkalmazásmintában két- vagy háromrétegű alkalmazást telepít az Azure-ba az [Azure Felhőszolgáltatások](../../../cloud-services/cloud-services-choose-me.md) (webes és feldolgozói szerepkörök – Platform szolgáltatásként (PaaS)) és [az Azure virtuális gépek](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Infrastruktúra szolgáltatásként (IaaS) használatával. Az [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) használata a bemutatószint/üzleti szint és az SQL Server az Azure virtuális [gépek](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az adatréteg ben előnyös a legtöbb azure-ban futó alkalmazások. Ennek az az oka, hogy a Felhőszolgáltatásokon futó számítási példány egyszerű felügyeletet, üzembe helyezést, figyelést és horizontális felskálázást biztosít.

A Cloud Services segítségével az Azure fenntartja az ön számára az infrastruktúrát, elvégzi a rendszeres karbantartást, kijavítja az operációs rendszereket, és megkísérli a szerviz- és hardverhibák helyreállítását. Ha az alkalmazás nak horizontális felskálázási, automatikus és manuális horizontális felskálázási lehetőségek állnak rendelkezésre a felhőszolgáltatási projekt az alkalmazás által használt példányok vagy virtuális gépek számának növelésével vagy csökkentésével. Emellett a helyszíni Visual Studio segítségével üzembe helyezheti az alkalmazást egy felhőalapú szolgáltatásprojektaz Azure-ban.

Összefoglalva, ha nem szeretne kiterjedt felügyeleti feladatokat a bemutató/üzleti szint, és az alkalmazás nem igényel semmilyen összetett szoftver-konfiguráció vagy az operációs rendszer, használja az Azure Cloud Services. Ha az Azure SQL Database nem támogatja az összes keresett funkciót, használja az SQL Server egy Azure virtuális gép az adatréteg. Egy alkalmazás futtatása az Azure Cloud Services és az adatok tárolása az Azure virtuális gépek egyesíti a két szolgáltatás előnyeit. Részletes összehasonlítást ebben a témakörben a [fejlesztési stratégiák összehasonlítása az Azure-ban](#comparing-web-development-strategies-in-azure)című témakörben talál.

Ebben az alkalmazásmintában a bemutatószint tartalmaz egy webes szerepkört, amely az Azure végrehajtási környezetben futó Cloud Services-összetevő, és az IIS és a ASP.NET által támogatott webalkalmazás-programozáshoz van testreszabva. Az üzleti vagy háttérréteg tartalmaz egy feldolgozói szerepkört, amely az Azure végrehajtási környezetben futó Cloud Services-összetevő, és általános fejlesztés esetén hasznos, és háttérfeldolgozást végezhet egy webes szerepkörhöz. Az adatbázisréteg az Azure-beli SQL Server virtuális gépen található. A bemutatószint és az adatbázisszint közötti kommunikáció közvetlenül vagy az üzleti rétegen – feldolgozói szerepkör-összetevőkön keresztül történik.

Ez az alkalmazásminta akkor hasznos, ha:

* Az SQL Server magas rendelkezésre állású és vész-helyreállítási képességeinek megvalósításával nagyvállalati alkalmazásokat szeretne áthelyezni a helyszíni virtualizált platformokról az Azure-ba.
* Olyan infrastruktúra-környezetet szeretne birtokolni, amely igény szerint fel-le skálázható.
* Az Azure SQL Database nem támogatja az összes olyan szolgáltatást, amelyre az alkalmazásnak vagy az adatbázisnak szüksége van.
* A különböző munkaterhelési szinteken stressztesztelést szeretne végezni, de ugyanakkor nem szeretne sok fizikai gépet birtokolni és karbantartani.

Az alábbi ábrán egy helyszíni forgatókönyv és a felhőalapú megoldás látható. Ebben a forgatókönyvben a bemutató rétegwebes szerepkörökben, az üzleti szint a feldolgozói szerepkörökben, de az adatréteg az Azure-ban a virtuális gépek. A bemutatószint több példányának futtatása különböző webes szerepkörökben biztosítja a terheléselosztási kérelmek et. Ha az Azure Cloud Services t és az Azure virtuális gépeket kombinálja, azt javasoljuk, hogy az [Azure Virtuális Hálózatot](../../../virtual-network/virtual-networks-overview.md) is állítsa be. Az [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)segítségével stabil és állandó magánhálózati IP-címeket használhat ugyanazon a felhőszolgáltatáson belül a felhőben. Miután definiált egy virtuális hálózatot a virtuális gépek hez és a felhőszolgáltatásokhoz, elkezdhetnek kommunikálni egymás között a privát IP-címen keresztül. Emellett a virtuális gépek és az Azure web/feldolgozó szerepkörök ugyanabban az [Azure virtuális hálózatban](../../../virtual-network/virtual-networks-overview.md) alacsony késést és biztonságosabb kapcsolatot biztosít. További információ: [Mi a felhőszolgáltatás.](../../../cloud-services/cloud-services-choose-me.md)

Ahogy az ábrán látható, az Azure Load Balancer több virtuális gép közötti forgalmat oszt el, és azt is meghatározza, hogy melyik webkiszolgálóhoz vagy alkalmazáskiszolgálóhoz csatlakozzon. Ha a web- és alkalmazáskiszolgálók több példánya van a terheléselosztó mögött, az biztosítja a megjelenítési szint és az üzleti szint magas rendelkezésre állását. További információ: [Gyakorlati tanácsok az SQL HADR igénylő alkalmazásmintákhoz.](#best-practices-for-application-patterns-requiring-sql-hadr)

![Alkalmazásminták a Felhőszolgáltatásokkal](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Az alkalmazásminta megvalósításának egy másik megközelítése egy konszolidált webes szerepkör használata, amely a bemutatószint és az üzleti réteg összetevőit is tartalmazza, ahogy az az alábbi ábrán látható. Ez az alkalmazásminta olyan alkalmazások esetében hasznos, amelyek állapotalapú tervezést igényelnek. Mivel az Azure állapot nélküli számítási csomópontokat biztosít webes és feldolgozói szerepkörökön, azt javasoljuk, hogy valósítson meg egy logikát a munkamenet-állapot tárolására a következő technológiák egyikével: [Azure Caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) vagy Azure SQL [Database.](../../../sql-database/sql-database-technical-overview.md)

![Alkalmazásminták a Felhőszolgáltatásokkal](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Minta az Azure virtuális gépekkel, az Azure SQL-adatbázissal és az Azure App Service szolgáltatással (webalkalmazások)
Az alkalmazásminta elsődleges célja, hogy megmutassa, hogyan kombinálhatja az Azure-infrastruktúrát szolgáltatásként (IaaS) az Azure platform-szolgáltatás-összetevőivel (PaaS) a megoldásban. Ez a minta az Azure SQL Database-re összpontosít a relációs adattároláshoz. Nem tartalmazza az SQL Server egy Azure virtuális gép, amely része az Azure-infrastruktúra szolgáltatásajánlatként.

Ebben az alkalmazásmintában egy adatbázis-alkalmazást telepít az Azure-ba azáltal, hogy a bemutató és az üzleti szintek ugyanabban a virtuális gépben, és egy adatbázis elérése az Azure SQL Database (SQL Database) kiszolgálókon. A bemutatószint hagyományos IIS-alapú webes megoldásokkal valósítható meg. Vagy megvalósíthat egy kombinált bemutatót és üzleti szintet az [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)használatával.

Ez az alkalmazásminta akkor hasznos, ha:

* Már rendelkezik egy azure-ban konfigurált SQL Database-kiszolgálóval, és gyorsan szeretné tesztelni az alkalmazást.
* Az Azure-környezet képességeit szeretné tesztelni.
* Gyorsan szeretné kiépíteni a fejlesztési és tesztelési környezeteket rövid ideig.
* Az üzleti logika és az adatelérési összetevők önálló a webalkalmazáson belül.

Az alábbi ábrán egy helyszíni forgatókönyv és a felhőalapú megoldás látható. Ebben a forgatókönyvben az alkalmazásrétegeket egyetlen virtuális gépbe helyezi az Azure-ban, és az Azure SQL Database-ben fér hozzá az adatokhoz.

![Vegyes alkalmazási minta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Ha úgy dönt, hogy egy kombinált webes és alkalmazásréteget valósít meg az Azure Web Apps használatával, azt javasoljuk, hogy a középső réteget vagy az alkalmazásréteget dinamikus csatolású kódtárakként (DL) tartsa egy webalkalmazás környezetében.

Emellett tekintse át a cikk végén található [Webfejlesztési stratégiák összehasonlítása az Azure-ban](#comparing-web-development-strategies-in-azure) című szakaszban található javaslatokat, ha többet szeretne megtudni a programozási technikákról.

## <a name="n-tier-hybrid-application-pattern"></a>N-szintű hibrid alkalmazásminta
Az n-tier hibrid alkalmazásmintában valósíthatja meg az alkalmazást több rétegben a helyszíni és az Azure között elosztott. Ezért hozzon létre egy rugalmas és újrafelhasználható hibrid rendszert, amely et a többi szint módosítása nélkül módosíthat vagy hozzáadhat egy adott réteghez. A vállalati hálózat felhőre való kiterjesztéséhez használja az [Azure Virtuális hálózat](../../../virtual-network/virtual-networks-overview.md) szolgáltatást.

Ez a hibrid alkalmazásminta akkor hasznos, ha:

* Olyan alkalmazásokat szeretne készíteni, amelyek részben a felhőben, részben a helyszínen futnak.
* Egy meglévő helyszíni alkalmazás néhány vagy összes elemét szeretné áttelepíteni a felhőbe.
* A vállalati alkalmazásokat a helyszíni virtualizált platformokról az Azure-ba szeretné áthelyezni.
* Olyan infrastruktúra-környezetet szeretne birtokolni, amely igény szerint fel-le skálázható.
* Gyorsan szeretné kiépíteni a fejlesztési és tesztelési környezeteket rövid ideig.
* Költséghatékony módot szeretne a vállalati adatbázis-alkalmazások biztonsági másolatainak készítésére.

Az alábbi ábrán egy n-szintű hibrid alkalmazásminta, amely a helyszíni és az Azure-ban. Amint az az ábrán látható, a helyszíni infrastruktúra tartalmazza az [Active Directory tartományi szolgáltatások](https://technet.microsoft.com/library/hh831484.aspx) tartományvezérlőjét a felhasználói hitelesítés és engedélyezés támogatására. Vegye figyelembe, hogy az ábra bemutatja a forgatókönyv, amelyben az adatréteg egyes részei egy helyszíni adatközpontban, míg egyes részei az adatréteg él az Azure-ban. Az alkalmazás igényeitől függően számos más hibrid forgatókönyvek valósíthatók meg. Előfordulhat például, hogy a bemutatószintet és az üzleti szintet egy helyszíni környezetben, de az Azure-beli adatrétegben tartja.

![N szintű alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Az Azure-ban használhatja az Active Directoryt önálló felhőkönyvtárként a szervezet számára, vagy integrálhatja a meglévő helyszíni Active Directoryt az [Azure Active Directoryval.](https://azure.microsoft.com/documentation/services/active-directory/) Ahogy az ábrán látható, az üzleti réteg összetevői több adatforráshoz is hozzáférhetnek, például az [Azure-beli SQL Serverhez](virtual-machines-windows-sql-server-iaas-overview.md) egy privát belső IP-címen keresztül, a helyszíni SQL Serverhez az [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)hálózaton keresztül, vagy az SQL [Database-hez](../../../sql-database/sql-database-technical-overview.md) a . Ebben az ábrán az Azure SQL Database egy opcionális adattárolási szolgáltatás.

Az n-szintű hibrid alkalmazásmintában a következő munkafolyamatot a megadott sorrendben valósíthatja meg:

1. Azonosítsa azokat a vállalati adatbázis-alkalmazásokat, amelyeket a [Microsoft Assessment and Planning (MAP) toolkit](https://microsoft.com/map)segítségével fel kell helyezni a felhőbe. A MAP eszközkészlet leltár- és teljesítményadatokat gyűjt a virtualizációra tervezett számítógépekről, és javaslatokat tesz a kapacitásra és az értékelés tervezésére vonatkozóan.
2. Tervezze meg az Azure platformon szükséges erőforrásokat és konfigurációt, például a tárfiókokat és a virtuális gépeket.
3. Állítsa be a hálózati kapcsolatot a helyszíni vállalati hálózat és az [Azure Virtuális Hálózat](../../../virtual-network/virtual-networks-overview.md)között. A helyszíni vállalati hálózat és az Azure-beli virtuális gép közötti kapcsolat beállításához használja az alábbi két módszer egyikét:
   
   1. Kapcsolatot létesíthet a helyszíni és az Azure között nyilvános végpontokon keresztül az Azure-ban egy virtuális gépen. Ez a módszer egyszerű telepítést biztosít, és lehetővé teszi az SQL Server-hitelesítés használatát a virtuális gépen. Emellett állítsa be a hálózati biztonsági csoport szabályait a virtuális gép nyilvános forgalmának szabályozásához. További információ: [Külső hozzáférés engedélyezése a virtuális géphez az Azure Portalhasználatával.](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   2. Kapcsolatot létesíthet a helyszíni és az Azure között az Azure virtuális magánhálózati (VPN) alagúton keresztül. Ez a módszer lehetővé teszi a tartományi szabályzatok kiterjesztését egy virtuális gépre az Azure-ban. Ezenkívül tűzfalszabályokat is beállíthat, és windowsos hitelesítést használhat a virtuális gépben. Az Azure jelenleg támogatja a biztonságos helyek közötti VPN-kapcsolatokat és a helyek közötti VPN-kapcsolatokat:
      
      * A biztonságos helyek közötti kapcsolattal hálózati kapcsolatot létesíthet a helyszíni hálózat és az Azure-beli virtuális hálózat között. Ajánlott a helyszíni adatközponti környezet és az Azure csatlakoztatása.
      * A biztonságos pont-hely kapcsolattal hálózati kapcsolatot létesíthet az Azure-beli virtuális hálózat és a bárhol futó egyes számítógépek között. Ez többnyire ajánlott fejlesztési és vizsgálati célokra.
      
      Az Azure-beli SQL Server hez való csatlakozásról a [Csatlakozás az Azure-beli SQL Server virtuális géphez](virtual-machines-windows-sql-connect.md)című témakörben talál további információt.
4. Állítsa be az ütemezett feladatokat és riasztásokat, amelyek biztonsági másolatot ahelyszíni adatokról egy virtuálisgép-lemezen az Azure-ban. További információt az [SQL Server biztonsági másolat és visszaállítás az Azure Blob Storage Szolgáltatással,](https://msdn.microsoft.com/library/jj919148.aspx) [valamint az SQL Server biztonsági másolat és visszaállítás az Azure virtuális gépeken című témakörben talál.](virtual-machines-windows-sql-backup-recovery.md)
5. Az alkalmazás igényeitől függően az alábbi három gyakori forgatókönyv egyike valósítható meg:
   
   1. A webkiszolgálót, az alkalmazáskiszolgálót és az érzéketlen adatokat az Azure-beli adatbázis-kiszolgálón tarthatja, míg a bizalmas adatokat a helyszínen.
   2. A webkiszolgáló és az alkalmazáskiszolgáló a helyszínen maradhat, míg az adatbázis-kiszolgáló egy virtuális gép az Azure-ban.
   3. Az adatbázis-kiszolgálót, a webkiszolgálót és az alkalmazáskiszolgálót a helyszínen tarthatja, míg az adatbázis-replikákat az Azure-beli virtuális gépekben. Ez a beállítás lehetővé teszi, hogy a helyszíni webkiszolgálók vagy jelentéskészítő alkalmazások hozzáférjenek az azure-beli adatbázis-replikákhoz. Ezért a helyszíni adatbázis munkaterhelésének csökkentésére is. Azt javasoljuk, hogy ezt a forgatókönyvet a nagy olvasási számítási feladatok és fejlesztési célokra valósítja meg. Az Azure-beli adatbázis-replikák létrehozásáról az AlwaysOn rendelkezésre állási csoportok [magas rendelkezésre állási és vész-helyreállítási az Azure virtuális gépekben található SQL Server szolgáltatásban című témakörben](virtual-machines-windows-sql-high-availability-dr.md)talál további információt.

## <a name="comparing-web-development-strategies-in-azure"></a>Webfejlesztési stratégiák összehasonlítása az Azure-ban
Többrétegű SQL Server-alapú alkalmazás azure-beli megvalósításához és üzembe helyezéséhez az alábbi két programozási módszer egyikét használhatja:

* Hozzon létre egy hagyományos webkiszolgálót (IIS – Internet Information Services) az Azure-ban, és érje el az adatbázisokat az SQL Server ben az Azure virtuális gépeken.
* Felhőalapú szolgáltatást valósítson meg és telepítsen az Azure-ba. Ezután győződjön meg arról, hogy ez a felhőszolgáltatás hozzáférhet az Azure virtuális gépek SQL Server adatbázisaihoz. A felhőszolgáltatások több webes és feldolgozói szerepkört is tartalmazhatnak.

Az alábbi táblázat a hagyományos webfejlesztés és az Azure Cloud Services és az Azure Web Apps összehasonlítását tartalmazza az Azure virtuális gépek SQL Server szolgáltatásával kapcsolatban. A táblázat tartalmazza az Azure Web Apps, mivel lehetséges, hogy az SQL Server az Azure VM adatforrásaként az Azure Web Apps keresztül nyilvános virtuális IP-cím vagy DNS-név.

|  | Hagyományos webfejlesztés az Azure virtuális gépekben | Felhőszolgáltatások az Azure-ban | Webhosting az Azure Web Apps alkalmazásokkal |
| --- | --- | --- | --- |
| **Alkalmazás áttelepítése a helyszíni környezetből** |Meglévő alkalmazások, ahogy van. |Az alkalmazásoknak webes és feldolgozói szerepkörökre van szükségük. |A meglévő alkalmazások, de alkalmas önálló webes alkalmazások és webes szolgáltatások, amelyek megkövetelik a gyors méretezhetőség. |
| **Fejlesztés és telepítés** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS Manager, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Minden felhőszolgáltatás két környezetben, amelyre telepítheti a szolgáltatási csomag és a konfiguráció: átmeneti és éles. Üzembe helyezhet egy felhőszolgáltatást az átmeneti környezetben, hogy tesztelje azt, mielőtt éles környezetben előlépteti. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **Felügyelet és beállítás** |Ön felelős az alkalmazás, az adatok, a tűzfalszabályok, a virtuális hálózat és az operációs rendszer felügyeleti feladataiért. |Ön felelős az alkalmazás, az adatok, a tűzfalszabályok és a virtuális hálózat felügyeleti feladataiért. |Ön felelős a felügyeleti feladatok az alkalmazás és az adatok csak. |
| **Magas rendelkezésre állás és katasztrófa utáni helyreállítás (HADR)** |Azt javasoljuk, hogy helyezze a virtuális gépek ugyanabban a rendelkezésre állási csoportban, és ugyanabban a felhőszolgáltatásban. A virtuális gépek ugyanabban a rendelkezésre állási csoportban tartása lehetővé teszi az Azure számára, hogy a magas rendelkezésre állású csomópontokat külön tartalék tartományokba helyezze, és frissítse a tartományokat. Hasonlóképpen a virtuális gépek ugyanabban a felhőszolgáltatásban tartása lehetővé teszi a terheléselosztást, és a virtuális gépek közvetlenül kommunikálhatnak egymással a helyi hálózaton keresztül egy Azure-adatközponton belül.<br/><br/>Ön felelős a magas rendelkezésre állású és vész-helyreállítási megoldás az Azure virtuális gépek SQL Server az állásidő elkerülése érdekében. A támogatott HADR-technológiákról a [Magas rendelkezésre állású és vészhelyreállítás az Azure virtuális gépeksql Server szolgáltatásában című témakörben található.](virtual-machines-windows-sql-high-availability-dr.md)<br/><br/>Ön felelős a saját adatainak és alkalmazásának biztonsági mentésért.<br/><br/>Az Azure áthelyezheti a virtuális gépeket, ha az adatközpontban lévő gazdagép hardverproblémák miatt meghibásodik. Emellett előfordulhat, hogy a virtuális gép tervezett állásideje, amikor a gazdagép frissül a biztonsági vagy szoftverfrissítések. Ezért azt javasoljuk, hogy a folyamatos rendelkezésre állás érdekében minden alkalmazásszinten legalább két virtuális gépet tartson fenn. Az Azure nem biztosít SLA-t egyetlen virtuális géphez. |Az Azure kezeli az alapul szolgáló hardver vagy operációs rendszer szoftveréből eredő hibákat. Azt javasoljuk, hogy egy webes vagy feldolgozói szerepkör több példányát valósítsa meg az alkalmazás magas rendelkezésre állásának biztosítása érdekében. További információt a [Felhőszolgáltatások, a virtuális gépek és a virtuális hálózati szolgáltatásiszint-szerződés című témakörben talál.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)<br/><br/>Ön felelős a saját adatainak és alkalmazásának biztonsági mentésért.<br/><br/>Az Azure-beli virtuális gép SQL Server-adatbázisában található adatbázisok esetében ön felelős a magas rendelkezésre állású és vész-helyreállítási megoldás megvalósításáért az állásidő elkerülése érdekében. A támogatott HDAR-technológiákról a Magas rendelkezésre állású és vészhelyreállítás az Azure virtuális gépeken az SQL Server ben című témakörben található.<br/><br/>**SQL Server-adatbázis tükrözése:** Az Azure Cloud Services használatával (web-/feldolgozói szerepkörökkel) használható. SQL Server virtuális gépek és egy felhőalapú szolgáltatás projekt lehet ugyanabban az Azure virtuális hálózatban. Ha az SQL Server virtuális gép nem ugyanabban a virtuális hálózatban van, létre kell hoznia egy SQL Server Aliast, hogy a kommunikációt az SQL Server példányához irányítsa. Ezenkívül az aliasnévnek meg kell egyeznie az SQL Server nevével. |A magas rendelkezésre állás az Azure feldolgozói szerepkörökből, az Azure blob storage-ból és az Azure SQL Database-ből öröklődik. Például az Azure Storage az összes blob-, tábla- és várólista-adat három replikáját tartja karban. Az Azure SQL Database egy időben az adatok három replikáját – egy elsődleges replikát és két másodlagos replikát – futtat. További információ: [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) és [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Ha az SQL Server t használja az Azure VM-ben az Azure Web Apps adatforrásaként, ne feledje, hogy az Azure Web Apps nem támogatja az Azure Virtuális hálózatot. Más szóval az Azure Web Apps és az Azure-beli SQL Server virtuális gépek közötti összes kapcsolatot a virtuális gépek nyilvános végpontjain kell átnéznie. Ez bizonyos korlátozásokat okozhat a magas rendelkezésre állású és vész-helyreállítási forgatókönyvek. Például az azure-beli webalkalmazások, amelyek adatbázis-tükrözéssel csatlakoznak az SQL Server virtuális géphez, nem tudnak csatlakozni az új elsődleges kiszolgálóhoz, mivel az adatbázis-tükrözés megköveteli, hogy az Azure virtuális hálózatot állítsa be az Azure-beli SQL Server gazdagépei között. Ezért az **SQL Server adatbázis-tükrözés** az Azure Web Apps használatával jelenleg nem támogatott.<br/><br/>**SQL Server AlwaysOn rendelkezésre állási csoportok:** Beállíthatja AlwaysOn rendelkezésre állási csoportok használata esetén az Azure Web Apps SQL Server virtuális gépek az Azure-ban. De konfigurálnia kell az AlwaysOn availabilitycsoportfigyelőt, hogy a kommunikációt az elsődleges replika-hoz irányítsa nyilvános terheléselosztásos végpontokon keresztül. |
| **telephelyek közötti kapcsolat** |Az Azure Virtual Network segítségével csatlakozhat a helyszíni csatlakozáshoz. |Az Azure Virtual Network segítségével csatlakozhat a helyszíni csatlakozáshoz. |Az Azure virtuális hálózat támogatott. További információt a [Web Apps virtuális hálózati integráció című témakörben talál.](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| **Méretezhetőség** |A felskálázás a virtuális gép méretének növelésével vagy további lemezek hozzáadásával érhető el. A virtuális gépek méretéről további információt az [Azure virtuálisgép-méretei című témakörben talál.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br/><br/>**Adatbázis-kiszolgáló esetén:** A horizontális felskálázás adatbázis-particionálási technikákkal és az SQL Server AlwaysOn rendelkezésre állási csoportokkal érhető el.<br/><br/>A nagy olvasási számítási feladatok hoz, [használhatja AlwaysOn rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) több másodlagos csomópontok, valamint az SQL Server replikáció.<br/><br/>A nehéz írási számítási feladatok, horizontális particionálási adatokat valósíthat meg több fizikai kiszolgálókon, hogy az alkalmazás horizontális felskálázás.<br/><br/>Ezenkívül az [SQL Server adatfüggő útválasztással történő](https://technet.microsoft.com/library/cc966448.aspx)használatával is megvalósíthat egy horizontális felskálázást. Az adatfüggő útválasztás (DDR) segítségével az ügyfélalkalmazásban – általában az üzleti réteg rétegében – végre kell implementálnia a particionálási mechanizmust, hogy az adatbázis-kérelmeket több SQL Server-csomópontra irányítsa. Az üzleti szint az adatok particionálásának és az adatokat tartalmazó csomópontnak a hozzárendelését tartalmazza.<br/><br/>A virtuális gépeket futtató alkalmazások méretezése. További információ: [Hogyan méretezhető egy alkalmazás.](../../../cloud-services/cloud-services-how-to-scale-portal.md)<br/><br/>**Fontos megjegyzés:** Az **Azure automatikus skálázási** szolgáltatása lehetővé teszi az alkalmazás által használt virtuális gépek automatikus növelését vagy csökkentését. Ez a funkció garantálja, hogy a végfelhasználói élmény nem befolyásolja negatívan a csúcsidőszakokban, és a virtuális gépek leállnak, ha az igény alacsony. Javasoljuk, hogy ne állítsa be a felhőalapú szolgáltatás automatikus skálázási beállítását, ha az SQL Server virtuális gépeket is tartalmaz. Ennek az az oka, hogy az Automatikus skálázás funkció lehetővé teszi az Azure számára, hogy bekapcsolja a virtuális gépet, ha az adott virtuális gép processzorhasználata magasabb, mint egy küszöbérték, és kapcsolja ki a virtuális gépet, ha a CPU-használat alacsonyabb, mint azt. Az automatikus skálázási szolgáltatás olyan állapotnélküli alkalmazások, például webkiszolgálók számára hasznos, ahol bármely virtuális gép kezelheti a munkaterhelést anélkül, hogy bármilyen korábbi állapotra hivatkozna. Az Automatikus skálázás szolgáltatás azonban nem hasznos állapotalapú alkalmazások, például az SQL Server esetében, ahol csak egy példány engedélyezi az írást az adatbázisba. |A scale-up több webes és feldolgozói szerepkör használatával érhető el. A webes szerepkörök és a feldolgozói szerepkörök virtuálisgép-méreteiről a [Felhőszolgáltatások méreteikonfigurálása](../../../cloud-services/cloud-services-sizes-specs.md)című témakörben talál további információt.<br/><br/>A **Cloud Services**használatakor több szerepkört is definiálhat a feldolgozás elosztásához, és az alkalmazás rugalmas méretezését is elérheti. Minden felhőszolgáltatás egy vagy több webes szerepkört és/vagy feldolgozói szerepkört tartalmaz, amelyek mindegyike saját alkalmazásfájlokkal és konfigurációval van elsajátulva. A felhőszolgáltatások skálázásával növelheti a szerepkörhöz üzembe helyezett szerepkörpéldányok (virtuális gépek) számát, és a szerepkörpéldányok számának csökkentésével csökkentheti a felhőszolgáltatást. Részletes információt az Azure végrehajtási modellek című [témakörben talál.](../../../cloud-services/cloud-services-choose-me.md)<br/><br/>A horizontális felskálázás a beépített Azure magas rendelkezésre állási támogatással érhető el [a felhőszolgáltatásokon, a virtuális gépeken és a virtuális hálózati szolgáltatásiszint-szerződésen és](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) a terheléselosztón keresztül.<br/><br/>Többrétegű alkalmazás esetén azt javasoljuk, hogy csatlakozzon a web/feldolgozó szerepkörök alkalmazás adatbázis-kiszolgáló virtuális gépek az Azure virtuális hálózaton keresztül. Emellett az Azure terheléselosztást biztosít az azonos felhőszolgáltatásban lévő virtuális gépek számára, és a felhasználói kérelmeket szétosztja közöttük. Az ily módon csatlakoztatott virtuális gépek közvetlenül kommunikálhatnak egymással a helyi hálózaton keresztül egy Azure-adatközponton belül.<br/><br/>Az Automatikus **skálázást** az Azure Portalon, valamint az ütemezési időpontokat is beállíthatja. További információ: [Az automatikus skálázás konfigurálása a felhőszolgáltatáshoz a portálon](../../../cloud-services/cloud-services-how-to-scale-portal.md)című témakörben talál. |**Fel- és leskálázás:** Növelheti/csökkentheti a webhely számára fenntartott példány (VM) méretét.<br/><br/>Horizontális felskálázás: További fenntartott példányokat (virtuális gépeket) adhat hozzá a webhelyhez.<br/><br/>Beállíthatja **az automatikus skálázást** a portálon, valamint az ütemezési időpontokat. További információt a [Web Apps-ek méretezése című témakörben talál.](../../../app-service/manage-scale-up.md) |

A programozási módszerek közötti választásról további információt az [Azure Web Apps, a Felhőszolgáltatások és a virtuális gépek: Mikor kell használni.](/azure/architecture/guide/technology-choices/compute-decision-tree)

## <a name="next-steps"></a>Következő lépések
Az SQL Server Azure virtuális gépeken való futtatásáról az [SQL Server az Azure virtuális gépek áttekintése című témakörben olvashat bővebben.](virtual-machines-windows-sql-server-iaas-overview.md)


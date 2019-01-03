---
title: Az SQL Server – Alkalmazásminták virtuális gépeken |} A Microsoft Docs
description: Ez a cikk ismerteti a alkalmazásminták az SQL Server Azure virtuális gépeken. Szolgáltatás megoldástervezők és fejlesztők számára az alkalmazások jó architektúrájának és kialakítása.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: ninarn
ms.openlocfilehash: 988acec8d7044afe87523637e46c9a4deb92b55e
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719710"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Azure-beli virtuális gépeken futó SQL Server – alkalmazásminták és fejlesztési stratégiák
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Összefoglalás:
Amely meghatározza, hogy mely alkalmazásminta vagy használandó minták az SQL Server-alapú alkalmazásokhoz az Azure-ban környezet egy fontos tervezési döntés és hogyan működik együtt az SQL Server és az Azure minden infrastruktúra-összetevő alapos ismerete szükséges. Az SQL Server az Azure infrastruktúra-szolgáltatásokban megőrizheti egyszerűen áttelepítése, és beépített – a Windows Server az Azure-beli virtuális gépek a meglévő SQL Server-alkalmazások figyelésére.

Ez a cikk az a célja, hogy alapját megoldástervezők és fejlesztők számára az alkalmazások jó architektúrájának és tervezési, amelyeket követve a Azure, valamint az Azure-ban új alkalmazások fejlesztése a meglévő alkalmazások áttelepítéséhez.

Az egyes alkalmazásminta talál egy helyszíni forgatókönyv, a megfelelő felhőalapú megoldás és a kapcsolódó technikai javaslatokat tartalmaz. Emellett a cikk ismerteti az Azure-specifikus fejlesztési stratégiák, úgy, hogy az alkalmazások megfelelően is tervezhet. A számos lehetséges alkalmazásminták miatt ajánlott, tervezők és fejlesztők számára az alkalmazások és a felhasználók számára a leginkább megfelelő mintát kell választania.

**Technikai közreműködők:** A Luis Carlos Vargas hering, Madhan Arumugam Ramakrishnan

**Műszaki véleményezők:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, lengyel Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Bevezetés
N szintű alkalmazások számos különböző típusú megadhat különböző gépeken, valamint különálló összetevőkként hasonlóan a különböző alkalmazási rétegekben összetevői is fejleszthet. Például az ügyfélalkalmazás helyezheti, és üzleti szabályokat egy számítógépen, előtérbeli webes réteg és az adatelérési réteg összetevők egy másik gépen, és a egy másik gépen háttér-adatbázis-rétegből az összetevők. Az ilyen típusú strukturálja segít elkülöníteni egymástól az egyes szintek. Ha módosítja, ahol az adat származik, nem kell az ügyfél és a webes alkalmazások, de csak az adatelérési réteg összetevők módosítása.

Egy tipikus *n szintű* alkalmazás tartalmazza a bemutatási szint, az üzleti szint és az adatréteg:

| Szint | Leírás |
| --- | --- |
| **Bemutató** |A *bemutatási szint* (előtérréteg webes réteg), amely a, amelyben a felhasználók használják az alkalmazást. |
| **üzleti** |A *üzleti szint* (középső réteg) a réteget, a bemutatási szint és az adatréteg használatával kommunikálnak egymással, és a rendszer legfontosabb funkcióit tartalmazza. |
| **Adatok** |A *adatrétegbeli* alapvetően a kiszolgálót, amelyet egy alkalmazás adatokat tárolja (például egy SQL Servert futtató kiszolgáló). |

Alkalmazási rétegekben ismertetik a funkciók és -összetevők az alkalmazások; logikai csoportosításain mivel a rétegek külön fizikai kiszolgálók, számítógépek, hálózat vagy távoli helyeken az a funkciók és -összetevők a fizikai terjesztési ismertetik. A rétegeket egy alkalmazást, előfordulhat, hogy az azonos fizikai számítógépen (az azonos szintű) találhatók, vagy előfordulhat, hogy szétoszthatók külön számítógépeken (n szintű), és az egyes rétegek összetevői összetevők más rétegekben, jól definiált felületeken keresztül kommunikálnak. A fizikai terjesztési minták például n szintű, kétrétegű és háromrétegű hivatkozó kifejezés réteg is felfoghatók. A **2 szintű alkalmazásminta** tartalmaz két alkalmazásrétegek: application server és adatbázis-kiszolgáló. A közvetlen kommunikációt történik, a kiszolgáló és az adatbázis-kiszolgáló között. A kiszolgáló webes rétegbeli és az üzleti szintű összetevőket tartalmazza. A **3 szintű alkalmazás minta**, nincsenek három alkalmazásrétegek: web server, az alkalmazás-kiszolgálóra, amely tartalmazza az üzleti logikai rétegből és/vagy az üzleti szint az adatelérési összetevők, és az adatbázis-kiszolgáló. A webalkalmazás-kiszolgáló és az adatbázis-kiszolgáló közötti kommunikáció a kiszolgáló keresztül történik. Az alkalmazás rétegek és szintek részletes információkért lásd: [Microsoft architektúra – útmutató](https://msdn.microsoft.com/library/ff650706.aspx).

A cikk elolvasása előtt rendelkeznie kell a Tudásbázis az SQL Server és az Azure alapvető fogalmait. Információkért lásd: [SQL Server Online könyvekben](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-server-iaas-overview.md) és [Azure.com](https://azure.microsoft.com/).

Ez a cikk ismerteti, amely képes lehet megfelelő, az egyszerű alkalmazásokat, valamint a nagy mértékben összetett nagyvállalati alkalmazások több alkalmazásminták. Mindegyik minta részletező, mielőtt azt javasoljuk, hogy meg kell ismerkednie a rendelkezésre álló adatok tárolási szolgáltatásokat az Azure-ban, mint például [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md), és [SQL Kiszolgáló-beli virtuális gépen](virtual-machines-windows-sql-server-iaas-overview.md). Ahhoz, hogy a legjobb kialakításakor a gazdafürttagok számára az alkalmazások, ismerje meg, mely adatokat tároló szolgáltatás egyértelműen használata.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Válassza ki az SQL Server Azure virtuális gépen, ha:
* Vezérlő a SQL Server és a Windows van szüksége. Ez lehet például az SQL Server-verzió, speciális gyorsjavításokat, teljesítmény-konfigurációkat, stb.
* Szükséges egy helyszíni SQL Server teljes kompatibilitást, és szeretne áttérni a meglévő alkalmazások Azure-ba,-van.
* Az Azure-beli képességeit kihasználva szeretne, de az Azure SQL Database nem támogatja az alkalmazás számára szükséges összes funkciót. Többek között a következő területeken:
  
  * **Az adatbázis mérete**: Amikor ez a cikk frissült az SQL Database támogatja egy adatbázist az adatok legfeljebb 1 TB. Ha az alkalmazás több mint 1 TB adatot igényel, és nem kívánja a horizontális skálázás egyéni megoldások megvalósítását, ajánlott a használt SQL Server egy Azure virtuális gépet. A legfrissebb információkért lásd: [skálázás, Azure SQL Database](https://msdn.microsoft.com/library/azure/dn495641.aspx), [DTU-Based beszerzési modell](../../../sql-database/sql-database-service-tiers-dtu.md), és [megvásárlása Virtuálismag-alapú modell](../../../sql-database/sql-database-service-tiers-vcore.md)(előzetes verzió).
  * **A HIPAA-megfelelőségi**: Az egészségügyi és független szoftverszállítók (ISV-k) érdemes választania [SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-server-iaas-overview.md) helyett [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) mivel foglalkozik az SQL Server egy Azure virtuális gépen a HIPAA üzlettársi szerződés (BAA). A megfelelőségi információk: [Microsoft Azure Trust Center: Megfelelőségi](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Példányszintű funkciókat**: Jelenleg az SQL Database nem támogatja az élő kívül az adatbázis-jellemzőket (például a csatolt kiszolgálók, ügynök feladatok, FileStream, a Service Broker, stb.). További információkért lásd: [Azure SQL Database szolgáltatásra vonatkozó irányelvek és korlátozások](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1 szintű (egyszerű): egyetlen virtuális gép
Ez alkalmazásminta helyezi üzembe az SQL Server alkalmazáshoz, és az adatbázis egy különálló virtuális géphez az Azure-ban. Ugyanaz a virtuális gép tartalmazza az ügyfél-/ webalkalmazás, az üzleti összetevők, adatelérési réteg és az adatbázis-kiszolgáló. A bemutató, az üzleti és adat-hozzáférési kód logikailag el egymástól, de egyetlen kiszolgálóból álló gépen fizikailag. A legtöbb ügyfél a alkalmazásminta kezdődhet, és ezután azok horizontális felskálázáshoz adjon hozzá további webes szerepkörökben vagy virtual machines, a rendszer.

Ez a minta alkalmazás a következő esetekben hasznos:

* Egyszerű áttelepítés az Azure-platform-e a platform vagy nem ad választ az alkalmazás követelményeinek kiértékelése végrehajtására vonatkozó szándékát.
* Meg szeretné tartani a ugyanazt a virtuális gépet a rétegek közötti késés csökkentése érdekében egyazon Azure adatközponton lévő üzemeltetett alkalmazás szinten.
* Szeretné gyorsan üzembe helyezése fejlesztési és tesztelési környezetek rövid ideig.
* Terhelési tesztelés a különböző számítási feladatok szintű végrehajtására vonatkozó szándékát, de egyszerre nem szeretné, hogy a saját és karbantartása sok fizikai gépek folyamatosan.

A következő ábra azt mutatja be egy egyszerű helyi forgatókönyv, és hogyan telepítheti az engedélyezett felhőalapú megoldás, az Azure-ban egyetlen virtuális gépen.

![1 szintű alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Üzembe helyezése az üzleti réteg (üzleti logikát és adatokat kell hozzáférnie, összetevők), a megjelenítési réteggel ugyanabban a fizikai rétegben maximalizálhatja a alkalmazásteljesítmény megtekintéséhez, kivéve, ha a skálázhatóság vagy a biztonsági szempontok miatt külön szinten kell használnia.

Mivel ez egy nagyon gyakori minta a következővel kell kezdődnie, hasznosak lehetnek a következő cikk az áttelepítés áthelyezi adatait az SQL Server virtuális gép számára: [Adatbázis áttelepítése az SQL Server egy Azure virtuális Gépen futó](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 rétegű (egyszerű): több virtuális gép
Ez alkalmazásminta üzembe helyezi a 3 szintű alkalmazás az Azure-ban úgy, hogy az egyes alkalmazásrétegek egy másik virtuális gépen. Ez egy egyszerű vertikális és horizontális felskálázási forgatókönyveket rugalmas környezetet biztosít. Ha egy virtuális gépet tartalmaz, az ügyfél-/ webalkalmazás, másikra futtatja az üzleti összetevője, és az egyéb egy adatbázis-kiszolgálót futtató.

Ez a minta alkalmazás a következő esetekben hasznos:

* Egy összetett adatbázis-alkalmazások az Azure Virtual Machines migrálásának végrehajtására vonatkozó szándékát.
* Egyes alkalmazási rétegekben, különböző régiókban lehet üzemeltetni szeretné. Például előfordulhat, hogy megosztott adatbázisok számára a jelentéskészítéshez több régióban üzembe helyezett.
* Vállalati alkalmazások áthelyezését a helyszíni virtualizált platformokról az Azure Virtual Machines szeretné. A vállalati alkalmazások részletes tárgyalását lásd: [Mi a vállalati alkalmazás](https://msdn.microsoft.com/library/aa267045.aspx).
* Szeretné gyorsan üzembe helyezése fejlesztési és tesztelési környezetek rövid ideig.
* Terhelési tesztelés a különböző számítási feladatok szintű végrehajtására vonatkozó szándékát, de egyszerre nem szeretné, hogy a saját és karbantartása sok fizikai gépek folyamatosan.

A következő ábra azt mutatja be, hogy hogyan helyezhető egy egyszerű 3 szintű alkalmazás az Azure-ban úgy, hogy az egyes alkalmazásrétegek egy másik virtuális gépen.

![3 szintű alkalmazás minta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Az alkalmazás ebben a mintában van csak egy virtuális gép (VM) az egyes szintek. Ha több virtuális gép az Azure-ban, javasoljuk, hogy állítsa be a virtuális hálózat. [Az Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) megbízható biztonsági korlátot, és lehetővé teszi virtuális gépek kommunikálhassanak egymással a magánhálózati IP-cím. Ezenkívül mindig győződjön meg arról, hogy minden internetes kapcsolatok csak nyissa meg a bemutatási szint. A következő alkalmazás ezt a mintát, ha a hálózati biztonsági csoport szabályai a hozzáférésének kezelése. További információkért lásd: [a virtuális gép az Azure Portallal való külső hozzáférés engedélyezése](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

A diagram az internetes protokollok a TCP, UDP, HTTP vagy HTTPS lehet.

> [!NOTE]
> Az Azure-beli virtuális hálózat beállítása az ingyenesen elérhető. Viszont díjkötelesek, amely kapcsolódik a helyszíni VPN-átjáró. Ez a díj alapja az, hogy mennyi ideig kapcsolat üzemképes.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2 – és 3-réteget a bemutatási szint horizontális felskálázás
Alkalmazás ebben a mintában a csomag 2 vagy 3 szintű adatbázis-alkalmazás az Azure Virtual Machines úgy, hogy az egyes alkalmazásrétegek lévő másik virtuális gépet telepít. Emellett horizontális felskálázása a bemutatási szint a bejövő ügyfélkérelmek megnövekedett mennyisége miatt.

Ez a minta alkalmazás a következő esetekben hasznos:

* Vállalati alkalmazások áthelyezését a helyszíni virtualizált platformokról az Azure Virtual Machines szeretné.
* Szeretne horizontális felskálázása a bemutatási szint a bejövő ügyfélkérelmek megnövekedett mennyisége miatt.
* Szeretné gyorsan üzembe helyezése fejlesztési és tesztelési környezetek rövid ideig.
* Terhelési tesztelés a különböző számítási feladatok szintű végrehajtására vonatkozó szándékát, de egyszerre nem szeretné, hogy a saját és karbantartása sok fizikai gépek folyamatosan.
* Szeretne saját felfelé vagy lefelé méretezheti igény szerinti infrastruktúrát környezetben.

A következő ábra azt mutatja be, hogy hogyan helyezhető alkalmazásrétegek több virtuális gép az Azure-ban a bejövő ügyfélkérelmek megnövekedett mennyisége miatt a bemutatási szint horizontális felskálázásával. Ahogy az ábrán látható, az Azure Load Balancer felelős adatforgalom elosztása több virtuális gép között, és mely webkiszolgálón való csatlakozáshoz is meghatározása. Egy terheléselosztó mögé a webkiszolgálók több példányát biztosítja a magas rendelkezésre állásának a bemutatási szint.

![Alkalmazásminta - bemutatási szint horizontális felskálázás](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>2-csomag, 3 szintű vagy n szintű mintákat, amelyek több virtuális gép egy szinten ajánlott eljárásai
Ajánlott elhelyezni a virtuális gépek tartozó megegyező szinten ugyanazon a felhőszolgáltatáson, és ugyanabban a rendelkezésre állási beállítása. Például helyezze el a webalkalmazás-kiszolgálók egy csoportja **CloudService1** és **AvailabilitySet1** és az adatbázis-kiszolgálók egy csoportja **CloudService2** és  **AvailabilitySet2**. Egy rendelkezésre állási csoportot az Azure-ban lehetővé teszi a magas rendelkezésre állású csomópontok helyezze külön tartalék tartományokban és frissítési tartományokban tárolja.

Használja ki a réteg több Virtuálisgép-példányok, az Azure Load Balancer konfigurálása az alkalmazásrétegek közötti kell. Load Balancer konfigurálása az egyes szintek, elosztott terhelésű végpont létrehozásához minden egyes réteg virtuális gépeken külön-külön. Egy konkrét csomagot kiválasztani először hozzon létre virtuális gépek az ugyanazon felhőszolgáltatásban. Ez biztosítja, hogy rendelkeznek-e az azonos nyilvános virtuális IP-cím. Ezután hozzon létre egy végpontot egy adott réteg virtuális gépein. Ezután rendeljen a más virtuális gépek az adott réteg terheléselosztásra egyazon végpont. Hozzon létre egy elosztott terhelésű készlet, több virtuális gép között osztja el a forgalmat, és azt is, hogy melyik csomópont való csatlakozáshoz, ha egy háttérbeli virtuális gép csomópontot nem sikerült meghatározni a terheléselosztó. Például egy terheléselosztó mögé a webkiszolgálók több példányát biztosítja a magas rendelkezésre állásának a bemutatási szint.

Ajánlott eljárásként mindig ellenőrizze, hogy az összes internetes kapcsolatok először nyissa meg a bemutatási szint. A megjelenítési réteg az üzleti szint fér hozzá, és ezután az üzleti szint hozzáfér-e az adatréteg. A megjelenítési réteggel való hozzáférés engedélyezése a további információkért lásd: [a virtuális gép az Azure Portallal való külső hozzáférés engedélyezése](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Vegye figyelembe, hogy a terheléselosztó az Azure-ban működik hasonló terheléselosztók a helyszíni környezetben. További információkért lásd: [terheléselosztás az Azure infrastruktúra-szolgáltatásoknak](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Emellett javasoljuk, hogy beállította egy magánhálózaton a virtuális gépek az Azure Virtual Network használatával. Ez lehetővé teszi őket a privát IP-cím egymás közötti kommunikációra. További információkért lásd: [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2-réteg és a 3 szintű az üzleti szint horizontális felskálázás
Alkalmazás ebben a mintában a csomag 2 vagy 3 szintű adatbázis-alkalmazás az Azure Virtual Machines úgy, hogy az egyes alkalmazásrétegek lévő másik virtuális gépet telepít. Emellett előfordulhat, hogy kívánja terjeszteni az alkalmazás összetettsége miatt több virtuális gép alkalmazás kiszolgáló-összetevőket.

Ez a minta alkalmazás a következő esetekben hasznos:

* Vállalati alkalmazások áthelyezését a helyszíni virtualizált platformokról az Azure Virtual Machines szeretné.
* Az alkalmazás kiszolgálói összetevőket az alkalmazás összetettsége miatt több virtuális gép terjeszteni szeretné.
* Szeretné helyezni az üzleti logikát (nagy erőforrásigényű) a helyszíni LOB-(üzleti-) alkalmazások az Azure Virtual Machines. LOB-alkalmazások, amelyek kritikus fontosságú, vállalati, például a számviteli, emberi erőforrások (HR), Bérszámfejtés, ellátásilánc-kezelési és erőforrás-alkalmazások tervezési futó kritikus fontosságú számítógépes alkalmazások.
* Szeretné gyorsan üzembe helyezése fejlesztési és tesztelési környezetek rövid ideig.
* Terhelési tesztelés a különböző számítási feladatok szintű végrehajtására vonatkozó szándékát, de egyszerre nem szeretné, hogy a saját és karbantartása sok fizikai gépek folyamatosan.
* Szeretne saját felfelé vagy lefelé méretezheti igény szerinti infrastruktúrát környezetben.

A következő ábra mutatja be, egy helyszíni forgatókönyvet és annak engedélyezve felhőalapú megoldás. Ebben a forgatókönyvben, az üzleti szint, amely tartalmazza az üzleti logikai rétegből és az adatelérési összetevők horizontális felskálázásával több virtuális gép az Azure-ban az alkalmazásrétegek helyezze el. Ahogy az ábrán látható, az Azure Load Balancer felelős adatforgalom elosztása több virtuális gép között, és mely webkiszolgálón való csatlakozáshoz is meghatározása. Egy terheléselosztó mögött az alkalmazás-kiszolgálók több példányára biztosítja a magas rendelkezésre állásának az üzleti szint. További információkért lásd: [gyakorlati tanácsok a 2-csomag, 3 szintű vagy n szintű alkalmazás mintákat, amelyek több virtuális gép egy szinten](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Az üzleti szint horizontális felskálázási alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 – és 3-réteget a megjelenítési és üzleti szintek horizontális felskálázást és HADR
Alkalmazás ebben a mintában a csomag 2 vagy 3 szintű adatbázis-alkalmazás az Azure Virtual Machines osztja meg a bemutatási szint (webkiszolgáló) és az üzleti szint (alkalmazáskiszolgáló) összetevő több virtuális gépet telepít. Emellett végrehajtja a magas rendelkezésre állású és vész-helyreállítási megoldások az adatbázisok az Azure-beli virtuális gépeken.

Ez a minta alkalmazás a következő esetekben hasznos:

* Vállalati alkalmazások áthelyezését a helyszíni virtuális platformokra az Azure-bA az SQL Server magas rendelkezésre állású és vész-helyreállítási lehetőségei az életbe léptetésével szeretné.
* Szeretne horizontális felskálázása a bemutatási szint és az üzleti szint a bejövő ügyfélkérelmek megnövekedett mennyisége és az alkalmazás összetettsége miatt.
* Szeretné gyorsan üzembe helyezése fejlesztési és tesztelési környezetek rövid ideig.
* Terhelési tesztelés a különböző számítási feladatok szintű végrehajtására vonatkozó szándékát, de egyszerre nem szeretné, hogy a saját és karbantartása sok fizikai gépek folyamatosan.
* Szeretne saját felfelé vagy lefelé méretezheti igény szerinti infrastruktúrát környezetben.

A következő ábra mutatja be, egy helyszíni forgatókönyvet és annak engedélyezve felhőalapú megoldás. Ebben a forgatókönyvben horizontális felskálázása a bemutatási szint és az üzleti szint összetevőket több virtuális gép az Azure-ban. Emellett végrehajtja a magas rendelkezésre állás és vészhelyreállítás helyreállítási (HADR) technikák SQL Server-adatbázisok az Azure-ban.

Egy alkalmazás több példányának futtatása a különböző virtuális gépek győződjön meg arról, hogy-e elosztja a kérelmeket azok. Ha több virtuális gép, ügyeljen arra, hogy a virtuális gépek elérhetők maradnak, és a egy ponton futó időben szeretne. Terheléselosztás beállítása, ha az Azure Load Balancer követi nyomon a virtuális gépek állapotát, és megfelelően irányítja a bejövő hívások a kifogástalan állapotú működő Virtuálisgép-csomópontok. A virtuális gépek terheléselosztás beállítása a további információkért lásd: [terheléselosztás az Azure infrastruktúra-szolgáltatásoknak](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Egy terheléselosztó mögé web- és kiszolgálók több példányára biztosítja a magas rendelkezésre állásának a megjelenítés és üzleti szintek.

![Horizontális felskálázás és magas rendelkezésre állás](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Ajánlott eljárások az SQL HADR igénylő alkalmazásminták
Az SQL Server magas rendelkezésre állású és vész-helyreállítási megoldások az Azure Virtual machines gépeken beállításakor beállítása egy virtuális hálózatot használó virtuális gépeiről [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) megadása kötelező.  Egy virtuális hálózaton belüli virtuális gépek stabil magánhálózati IP-cím lesz a szolgáltatás leállás után is, így elkerülheti a DNS-névfeloldás szükséges frissítési időpontja. A virtuális hálózat továbbá lehetővé teszi, hogy a helyszíni hálózat kiterjesztése az Azure-ba, és megbízható biztonsági korlátot. Például, ha az alkalmazás (mint például a Windows-hitelesítés, Active Directory), vállalati tartománynév-korlátozások beállítása [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) szükség.

A vásárlók, akik futnak az Azure-ban éles kódban, a legtöbb elsődleges és másodlagos replikák megakadályozzák az Azure-ban.

Átfogó információk és a magas rendelkezésre állású és vész-helyreállítással kapcsolatos oktatóanyagok: [magas rendelkezésre állású és vész-helyreállítási az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>2-réteg és a 3 szintű Azure-beli virtuális gépek és Cloud Services használatával
Alkalmazás ebben a mintában az Azure-bA csomag 2 vagy 3 szintű alkalmazás segítségével telepítheti mindkét [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (webes és feldolgozói szerepkörök - Platformszolgáltatás (PaaS)) és [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) () Szolgáltatott infrastruktúra (IaaS)). Használatával [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) a bemutatási szint/üzleti szint és az SQL Server [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az adatok szintje előnyös a legtöbb alkalmazás az Azure-ban. A hiba oka, hogy egy egyszerű felügyeleti, üzembe helyezés, figyelés és kibővített Felhőszolgáltatásokban futó számítási példányok kellene biztosít.

A Cloud Services Azure az infrastruktúra kezeli az Ön számára, rutinkarbantartási feladatokat hajt végre, az operációs rendszerek a javítások és próbál helyreállítani szolgáltatás és a hardveres hibák esetén. Amikor az alkalmazásnak kell horizontális felskálázás, automatikus és manuális kibővített beállítás érhető el a felhőszolgáltatási projektet a növelésével vagy csökkentésével a példányok vagy az alkalmazás által használt virtuális gépek számát. Emellett az a helyszínen a Visual Studio segítségével az alkalmazás telepítése az Azure-ban a felhőszolgáltatás-projekt.

Összefoglalva Ha nem szeretné a saját kiterjedt réteg megjelenítési és üzleti felügyeleti feladatok és az alkalmazás nem szükséges szoftver vagy az operációs rendszer minden olyan összetettebb konfigurációt, használja az Azure Cloud Services. Ha Azure SQL Database nem támogatja a keresett összes funkciót, használja az SQL Server egy Azure virtuális gép az adatréteg számára. Egy alkalmazást futtat az Azure Cloud Services és az adatok tárolása az Azure Virtual machines gépeken egyesíti a mindkét szolgáltatás előnyeit. A részletes összehasonlításáért lásd a szakasz ebben a témakörben a [összehasonlítása az Azure-ban fejlesztési stratégiák](#comparing-web-development-strategies-in-azure).

Alkalmazás ebben a mintában a bemutatási szint tartalmaz egy webes szerepkör, amely a környezetben az Azure Cloud Services összetevő fut, és azt testre szabott webes alkalmazás programozási, az IIS és ASP.NET támogatja. A háttér vagy a csomag tartalmaz egy feldolgozói szerepkör, amely a környezetben az Azure Cloud Services összetevő fut, és akkor hasznos, ha általános fejlesztési, és előfordulhat, hogy végrehajtani a háttérben történő feldolgozás a webes szerepkör. Az adatbázisszint egy SQL Server virtuális gépet az Azure-ban található. A bemutatási szint és az adatbázisszint közötti kommunikáció közvetlenül vagy az üzleti szint – a feldolgozói szerepkör összetevők keresztül történik.

Ez a minta alkalmazás a következő esetekben hasznos:

* Vállalati alkalmazások áthelyezését a helyszíni virtuális platformokra az Azure-bA az SQL Server magas rendelkezésre állású és vész-helyreállítási lehetőségei az életbe léptetésével szeretné.
* Szeretne saját felfelé vagy lefelé méretezheti igény szerinti infrastruktúrát környezetben.
* Az Azure SQL Database nem támogatja az alkalmazás vagy az adatbázis szükséges összes funkciót.
* Terhelési tesztelés a különböző számítási feladatok szintű végrehajtására vonatkozó szándékát, de egyszerre nem szeretné, hogy a saját és karbantartása sok fizikai gépek folyamatosan.

A következő ábra mutatja be, egy helyszíni forgatókönyvet és annak engedélyezve felhőalapú megoldás. Ebben a forgatókönyvben a bemutatási szinten a webes szerepkörök, feldolgozói szerepkörök az üzleti szint, de az Azure-beli virtuális gépeken az adatréteg helyezze el. A bemutatási szinten több példányának különböző webes szerepkörök futó érkező kérések elosztása elosztását biztosítja. Ha kombinálja az Azure Cloud Services az Azure Virtual Machines, azt javasoljuk, hogy állítsa be a [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) is. A [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), akkor is rendelkezik stabil és állandó magánhálózati IP-címek az előfizetésen belül a felhő szolgáltatás a felhőben. Adja meg a virtuális hálózat a virtuális gépek és felhőszolgáltatások után elindíthatják a magánhálózati IP-címmel kommunikáló egymás között. Emellett kellene a virtuális gépek és az Azure webes/feldolgozói szerepkörök ugyanazon [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) alacsony késleltetésű és biztonságosabb kapcsolat. További információkért lásd: [Mi a felhő alapú szolgáltatás](../../../cloud-services/cloud-services-choose-me.md).

Ahogy az ábrán látható, Azure Load Balancer több virtuális gép között osztja el a forgalmat, és is meghatározza, mely webes vagy alkalmazás kiszolgálóhoz való csatlakozáshoz. Egy terheléselosztó mögé a web- és kiszolgálók több példányára biztosítja a magas rendelkezésre állásának a bemutatási szint és az üzleti szint. További információkért lásd: [ajánlott eljárásokat az alkalmazás mintáit igénylő SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![A Cloud Services alkalmazásminták](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

A kérelem minta megvalósítása egy másik módszere, hogy egy összevont webes szerepkör, amely tartalmazza a bemutatási szint és az üzleti szint összetevők a következő ábrán látható módon. Az alkalmazás minta akkor hasznos, állapot-nyilvántartó tervezési igénylő alkalmazásokhoz. Azure állapot nélküli számítási csomópontok kínál a webes és feldolgozói szerepkörök, mivel azt javasoljuk, hogy a következő technológiákat egyikével munkamenet-állapot tárolásához egy logikai megvalósítása: [Azure Caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [az Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) vagy [az Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).

![A Cloud Services alkalmazásminták](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Az Azure virtuális gépek, az Azure SQL Database és az Azure App Service (webalkalmazások) minta
Ez alkalmazásminta elsődleges célja, hogy bemutatják, hogyan kombinálhatja az Azure infrastruktúra-szolgáltatás (IaaS) összetevővel az Azure platform –-szolgáltatásként (PaaS) a megoldás összetevőként. Ez a minta relációs adatok tárolása az Azure SQL Database összpontosít. Tartalmazzák az SQL Server Azure virtuális gépen, amely az szolgáltatásajánlatként Azure-infrastruktúra része.

Az alkalmazás ebben a mintában alkalmazást telepít központilag az adatbázis az Azure-bA helyezi el a megjelenítés és üzleti szintek ugyanahhoz a virtuális géphez, és az Azure SQL Database (SQL Database) kiszolgálók egy adatbázis elérése közben. A bemutatási szint hagyományos az IIS-alapú webes megoldások használatával is alkalmazható. Másik lehetőségként használatával Megvalósíthat egy együttes megjelenítés és üzleti szint [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/).

Ez a minta alkalmazás a következő esetekben hasznos:

* Már rendelkezik egy meglévő SQL Database-kiszolgáló konfigurálása az Azure-ban, és szeretne gyorsan tesztelheti az alkalmazást.
* Szeretné tesztelni az Azure-környezet képességeit.
* Szeretné gyorsan üzembe helyezése fejlesztési és tesztelési környezetek rövid ideig.
* Lehet, hogy az üzleti logikát és adatokat hozzáférés összetevők önálló rámci webové aplikace.

A következő ábra mutatja be, egy helyszíni forgatókönyvet és annak engedélyezve felhőalapú megoldás. Ebben a forgatókönyvben az Azure és a hozzáférési adatokat az Azure SQL Database az egyetlen virtuális gép alkalmazásrétegek helyezze el.

![Vegyes alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Ha az Azure Web Apps segítségével megvalósítható egy egyesített webes és az alkalmazásrétegek választja, azt javasoljuk, hogy őrizze meg a középső rétegű vagy alkalmazás szint, dinamikus csatolású kódtárai (dll) egy webalkalmazás kontextusában.

Emellett tekintse át a ajánlottak a [összehasonlítása az Azure-beli webes fejlesztési stratégiák](#comparing-web-development-strategies-in-azure) szakaszban tudhat meg többet a programozási technikákat Ez a cikk végén található.

## <a name="n-tier-hybrid-application-pattern"></a>N szintű hibrid alkalmazásminta
Az n szintű hibrid alkalmazásminta megvalósításához több szint között a helyszíni és az Azure-ban az alkalmazás. Ezért, hozzon létre egy rugalmas és újrahasználható hibrid rendszer, amely módosíthatja, vagy adjon hozzá egy konkrét csomagot kiválasztani a többi szint módosítása nélkül. A vállalati hálózathoz a felhőbe való kiterjesztéséhez használja [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) szolgáltatás.

Ez a hibrid alkalmazás minta a következő esetekben hasznos:

* Szeretne létrehozni alkalmazásokat, amelyek részben a felhőben futtassa, és részben a helyszínen.
* Szeretne áttelepíteni egy meglévő helyszíni alkalmazások felhőbe néhány vagy összes elemét.
* Szeretné helyezni a vállalati alkalmazások a helyszíni virtualizált platformokról az Azure-bA.
* Szeretne saját felfelé vagy lefelé méretezheti igény szerinti infrastruktúrát környezetben.
* Szeretné gyorsan üzembe helyezése fejlesztési és tesztelési környezetek rövid ideig.
* Azt szeretné, hogy az adatbázis-alkalmazások vállalati biztonsági mentés időpontjának költséghatékony módon.

A következő ábra azt mutatja be egy n szintű hibrid alkalmazásminta, amely a helyszíni és az Azure átfogóan. Ahogy az ábrán látható, a helyszíni infrastruktúrát a [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) tartományvezérlő felhasználó hitelesítésének és engedélyezésének támogatásához. Vegye figyelembe, hogy a diagram bemutatja egy forgatókönyvet, ahol egyes részei az adatréteg élő egy helyszíni adatközpontban mivel egyes részei az adatréteg élő Azure-ban. Az alkalmazás szükségleteinek számos egyéb hibrid forgatókönyvek is alkalmazható. Például előfordulhat, hogy tárolja a bemutatási szint és az üzleti szint egy a helyszíni környezetben, de az adatréteg az Azure-ban.

![N szintű alkalmazás minta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Az Azure-ban, használhatja az Active Directory egy önálló a felhőalapú címtárat a szervezet számára, vagy is integrálhatók meglévő helyszíni Active Directory- [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Ahogy az ábrán látható, az üzleti szint összetevők el tud érni több adatforráshoz, mint például az [SQL Server az Azure-ban](virtual-machines-windows-sql-server-iaas-overview.md) keresztül egy privát belső IP-címet a helyszíni SQL Server-n keresztül [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), vagy [SQL Database](../../../sql-database/sql-database-technical-overview.md) a .NET-keretrendszer data provider technológiák használatával. Ez az ábra az Azure SQL Database egy nem kötelező storage szolgáltatás.

Az n szintű hibrid alkalmazásmintát valósítható meg az alábbi munkafolyamat a megadott sorrendben:

1. Szeretné áthelyezni a felhőbe enterprise adatbázis-alkalmazások azonosítása a [Microsoft Assessment és tervezés (Leképezés) eszközkészlet](https://microsoft.com/map). A MAP eszközkészlet leltár-és teljesítményadatokat gyűjt a számítógépek számára releváns, és javaslatokat tartalmaz a kapacitás és a tervezési értékelés.
2. Tervezze meg az erőforrásokat és az Azure platformon, például a storage-fiókok és a virtuális gépek szükséges konfiguráció.
3. Állítsa be a vállalati hálózaton helyszíni közötti hálózati kapcsolatot és [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). A vállalati hálózathoz a helyszínen és az Azure-beli virtuális gép között a kapcsolat beállításához, használja az alábbi két módszer egyikét:
   
   1. Nyilvános végpontok egy virtuális gépen keresztül a helyszíni és az Azure közötti kapcsolatot létesíteni az Azure-ban. Ezt a módszert biztosít egy könnyű beállítás, és lehetővé teszi az SQL Server-hitelesítés használatát a virtuális gépen. Ezenkívül állítsa be a hálózati biztonsági csoport szabályai virtuális gép nyilvános forgalom szabályozása. További információkért lásd: [a virtuális gép az Azure Portallal való külső hozzáférés engedélyezése](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Azure virtuális magánhálózati (VPN) hálózat alagúton keresztül a helyszíni és az Azure közötti kapcsolatot létesíteni. Ez a módszer lehetővé teszi, hogy terjessze ki az Azure-beli virtuális géphez tartományi szabályzatok. Emellett tűzfalszabályok beállítása, és a Windows-hitelesítés használatára a virtuális gépen. Jelenleg az Azure támogatja a biztonságos helyek közötti VPN- és pont – hely VPN-kapcsolatok:
      
      * A biztonságos helyek közötti kapcsolat hozható létre hálózati kapcsolat a helyszíni hálózat és a virtuális hálózat között az Azure-ban. Kapcsolódás a helyi adatközponti környezetben az Azure-bA ajánlott.
      * A pont – hely kapcsolat biztonságos az Azure-ban a virtuális hálózat és az egyes számítógépek bármely közötti hálózati kapcsolatot is létrehozhat. Leginkább fejlesztési és tesztelési célra ajánlott.
      
      Csatlakozás az SQL Serverhez az Azure-ban való információkért lásd: [Csatlakozás SQL Server virtuális gépekhez az Azure-ban](virtual-machines-windows-sql-connect.md).
4. Állítsa be az ütemezett feladatok és a riasztásokat, amelyek egy Azure-beli virtuális gép lemezét a helyszíni adatok biztonsági mentéséhez. További információkért lásd: [SQL Server biztonsági mentése és visszaállítása az Azure Blob Storage szolgáltatás](https://msdn.microsoft.com/library/jj919148.aspx) és [biztonsági mentése és visszaállítása az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-backup-recovery.md).
5. Az alkalmazás szükségleteinek valósítható meg a következő három általános forgatókönyv közül:
   
   1. Tárolhatja a webkiszolgáló, az alkalmazáskiszolgáló és és nagybetű nincs megkülönböztetve adatok egy adatbázis-kiszolgáló, az Azure-ban, mivel a bizalmas adatokat a helyszínen tárolja.
   2. Megtarthatja a webalkalmazás-kiszolgáló és az alkalmazáskiszolgáló helyszíni mivel az adatbázis-kiszolgáló Azure-beli virtuális gépen.
   3. Megtarthatja az adatbázis-kiszolgáló, webkiszolgáló, és a kiszolgáló helyszíni, mivel a virtuális gépek az Azure-ban tárolja az adatbázis-replikákat. Ez a beállítás lehetővé teszi, hogy a helyszíni webkiszolgálók vagy jelentéskészítési alkalmazásokat az Azure-ban az adatbázis-replikákat. Ezért akkor érhető el, csökkentheti a számítási feladat a helyi adatbázisban. Azt javasoljuk, hogy végrehajtja-e ebben a forgatókönyvben (nagy erőforrásigényű) olvassa el a számítási feladatok és fejlesztési célokra. Adatbázis-replikák létrehozásával az Azure-ban információkért lásd: AlwaysOn rendelkezésre állási csoportok: [magas rendelkezésre állású és vész-helyreállítási az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Az Azure-beli webes fejlesztési stratégiák összehasonlítása
Megvalósítása, és a egy többszintes Azure-ban az SQL Server-alapú alkalmazás üzembe helyezése, a következő két programozási módszerek egyikét használhatja:

* Egy hagyományos webkiszolgáló (IIS - Internet Information Services) beállítása az Azure- és hozzáférés adatbázisok az SQL Server Azure Virtual Machines szolgáltatásban.
* Megvalósítása, és helyezze üzembe az Azure egy felhőalapú szolgáltatás. Ezt követően győződjön meg arról, hogy ezt a felhőszolgáltatást érhessék el az Azure-beli virtuális gépeken az SQL Server adatbázisok. Egy felhőalapú szolgáltatás több webes és feldolgozói szerepkörök lehetnek.

Az alábbi táblázat összehasonlítja a hagyományos webes fejlesztés az Azure Cloud Services és az Azure Web Apps vonatkozóan az SQL Server Azure Virtual Machines szolgáltatásban. A táblázat az Azure Web Apps tartalmaz, mivel előfordulhat használandó SQL Server Azure-beli virtuális gépen egy adatforrást az Azure Web Apps keresztül a nyilvános virtuális IP-cím vagy a DNS-neve.

|  | Hagyományos webes fejlesztés az Azure Virtual machines szolgáltatásban | A cloud Services, Azure-ban | Az Azure Web Apps szolgáltatással a webszolgáltatási |
| --- | --- | --- | --- |
| **A helyszíni alkalmazások áttelepítése** |Meglévő alkalmazások, például-van. |Alkalmazások a webes és feldolgozói szerepkörök kell. |Meglévő alkalmazások, például – de olyan önálló webes alkalmazások és webszolgáltatások gyors méretezhetőséget igénylő. |
| **Fejlesztés és üzembe helyezés** |A Visual Studio, WebMatrix, a Visual Web Developerben, WebDeploy, FTP, TFS, IIS-kezelő PowerShell. |Visual Studio, TFS és az Azure SDK PowerShell. Minden felhőszolgáltatás két környezetet, amelyre telepítheti a service-csomagot és a konfigurációs van: átmeneti és éles környezetben. Telepíthet egy felhőalapú szolgáltatás tesztelése, mielőtt éles környezetbe való előléptetése átmeneti környezetben. |A Visual Studio, WebMatrix, a Visual Web Developerben, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, webes üzembe helyezéséhez PowerShell. |
| **Felügyeleti és beállítási** |Ön felelős az alkalmazások, adatok, tűzfalszabályok, virtuális hálózati és operációs rendszer a felügyeleti feladatokat. |Ön felelős az alkalmazások, adatok, tűzfalszabályokat és virtuális hálózati a felügyeleti feladatokat. |Ön felelős az alkalmazás és az adatok csak a felügyeleti feladatokat. |
| **Magas rendelkezésre állású és vész-helyreállítási (HADR)** |Azt javasoljuk, hogy ugyanabban a rendelkezésre állási csoportban, és ugyanazon a felhőszolgáltatáson a virtuális gépet helyez. Tárolja a virtuális gépek ugyanazt a rendelkezésre állási csoport helyezi a magas rendelkezésre állású csomópontok külön tartalék tartományokban és frissítési tartományokba, az Azure lehetővé teszi. Ehhez hasonlóan tárolja a virtuális gépek ugyanazon a felhőszolgáltatáson lehetővé teszi a terheléselosztást, és a virtuális gépek közvetlenül tudnak kommunikálni egy másik az Azure adatközponton belül a helyi hálózaton keresztül.<br/><br/>Ön felelős végrehajtási egy magas rendelkezésre állású és vészhelyreállítási megoldás az SQL Server Azure virtuális gépek állásidő elkerülése érdekében. Támogatott HADR technológiák, lásd: [magas rendelkezésre állású és vész-helyreállítási az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Ön felelős a saját adatok és alkalmazások biztonsági másolatának.<br/><br/>Azure helyezheti át a virtuális gépek, hogy a gazdagépen az adatközpontban hardverproblémák miatt meghiúsul. Emellett lehetséges, hogy a virtuális gép a tervezett üzemkimaradás gazdaszámítógépen frissítésekor biztonsági vagy szoftverek frissítéseit. Ezért azt javasoljuk, hogy továbbra is a folyamatos rendelkezésre állás biztosítása érdekében az egyes alkalmazásrétegek legalább két virtuális gépet. Az Azure nem biztosít SLA-t egyetlen virtuális gép. |Az Azure kezeli a a mögöttes hardver vagy operációs rendszer szoftver eredő hibák. Azt javasoljuk, hogy végrehajtja-e az alkalmazás magas rendelkezésre állásának biztosításához egy webes vagy feldolgozói szerepkör több példányát. További információ: [Cloud Services, a virtuális gépek és a virtuális hálózati szolgáltatási szerződést](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Ön felelős a saját adatok és alkalmazások biztonsági másolatának.<br/><br/>Az SQL Server-adatbázis egy Azure-beli virtuális gépen levő adatbázisok esetében Ön felelős végrehajtási egy magas rendelkezésre állási és vészhelyreállítási megoldás állásidő elkerülése érdekében. A támogatott HDAR technológiákhoz az SQL Server Azure virtuális gépek magas rendelkezésre állású és vész-helyreállítási találja.<br/><br/>**SQL Server-adatbázis-tükrözés**: Használja az Azure Cloud Services (webes/feldolgozói szerepkörök). SQL Server virtuális gépek és a egy felhőszolgáltatás-projekt az azonos Azure virtuális hálózaton is lehet. Ha az SQL Server rendszerű virtuális gép nem ugyanazon a virtuális hálózaton van, hozzon létre egy SQL-kiszolgálói aliast irányíthatja a kommunikációt az SQL Server-példányra szeretné. Emellett az alias nevének egyeznie kell az SQL Server nevét. |Magas rendelkezésre állású Azure feldolgozói szerepköröket, az Azure blob storage és Azure SQL Database öröklődik. Például az Azure Storage fenntart minden blob, table és queue adatok három replikával. Egy időben, Azure SQL Database tartja a futó adatok három replika készül – egy elsődleges és két másodlagos replikákon. További információkért lásd: [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) és [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Használata esetén az SQL Server Azure-beli virtuális gépen adatforrásként az Azure Web Appshez, ne feledje, hogy az Azure Web Apps Azure Virtual Network nem támogatja. Más szóval az SQL Server virtuális gépen az Azure-ban az Azure Web Apps minden kapcsolat haladjon végig a virtuális gépek nyilvános végpontok. Emiatt előfordulhat, hogy bizonyos korlátozások a magas rendelkezésre állású és vész-helyreállítási helyzetekben. Például az ügyfélalkalmazás az Azure Web Apps, az SQL Server rendszerű virtuális gép csatlakozik, az adatbázis-tükrözés nem tudná szeretne csatlakozni az új elsődleges kiszolgáló, adatbázis-tükrözés van szükség, állítsa be az Azure Virtual Networkhöz az Azure-beli SQL Server gazdagép virtuális gépek között. Ezért használatával **SQL Server adatbázis-tükrözés** az Azure Web Apps jelenleg nem támogatott.<br/><br/>**Az SQL Server AlwaysOn rendelkezésre állási csoportok**: AlwaysOn rendelkezésre állási csoportokat állíthat SQL Server virtuális gépek az Azure-ban az Azure Web Apps használata esetén. De be kell állítania az AlwaysOn rendelkezésre állási csoport figyelője az elsődleges másodpéldány, nyilvános kiegyenlített terhelésű végpontok keresztül irányíthatja a a kommunikációt. |
| **létesítmények közötti kapcsolat** |Azure Virtual Network használatával csatlakozhat a helyszíni. |Azure Virtual Network használatával csatlakozhat a helyszíni. |Az Azure Virtual Network használata támogatott. További információkért lásd: [Web Apps virtuális hálózati integráció](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Méretezhetőség** |Vertikális felskálázás a virtuálisgép-méretek növelésével, vagy lemezeket érhető el. Virtuálisgép-méretekkel kapcsolatos további információkért lásd: [az Azure virtuálisgép-méretek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Adatbázis-kiszolgáló**: Horizontális felskálázás adatbázis particionálási technikák és az SQL Server AlwaysOn rendelkezésre állási csoportok keresztül érhető el.<br/><br/>Olvasási terhelés használhatja [AlwaysOn rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) több másodlagos csomópontot, valamint az SQL Server-replikáció.<br/><br/>A nagy írási számítási feladatok horizontális particionálási adatok biztosít az alkalmazás horizontális felskálázás több fizikai kiszolgálón valósíthat meg.<br/><br/>Emellett végrehajtja a horizontális felskálázás használatával [Adatfüggő útválasztás az SQL Server](https://technet.microsoft.com/library/cc966448.aspx). Az adatok függő útválasztás (DDR), kell megvalósítani a particionálási mechanizmust az ügyfélalkalmazásban, általában az az üzleti szint réteg az adatbázis-kérés irányíthatja a több SQL Server-csomóponton. Az üzleti szint leképezését az adatok particionálása hogyan és milyen csomópont tartalmazza az adatokat tartalmazza.<br/><br/>A virtuális gépeket futtató alkalmazások egyaránt méretezhetők. További információkért lásd: [hogyan méretezzünk át egy alkalmazást](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Fontos megjegyzés**: A **automatikus skálázási** funkció az Azure-ban lehetővé teszi, hogy automatikusan növelheti vagy csökkentheti a virtuális gépek, az alkalmazás által használt. Ez a funkció garantálja, hogy a végfelhasználói élmény nem befolyásolja negatívan csúcsidőszakok alatt, és a virtuális gépek állítsa le, amikor iránti igény alacsony. Javasoljuk, hogy nem kell beállítani az automatikus skálázási beállítás a felhőszolgáltatásokhoz tartozó SQL Server virtuális gépek találhatók benne. A hiba oka, hogy az automatikus méretezési funkció lehetővé teszi, hogy az Azure virtuális gép bekapcsolása, néhány küszöbértéknél a virtuális gép CPU-használat esetén, és kapcsolja ki egy virtuális gépet, ha a Processzor kihasználtsága alacsonyabb, mint amennyit fel. Az automatikus skálázási funkció hasznos állapotmentes alkalmazások, például webkiszolgálók, ahol a virtuális Gépeket kezelhetik a számítási feladat nélkül bármely korábbi állapotára mutató hivatkozásokat. Az automatikus méretezési funkciója viszont nem hasznos azon állapotalapú alkalmazásokhoz, például az SQL Server, ahol csak egy példány lehetővé teszi, hogy az adatbázisba írás. |Vertikális felskálázás több webes és feldolgozói szerepkörök használatával érhető el. A webes és feldolgozói szerepkörök virtuálisgép-méretekkel kapcsolatos további információkért lásd: [méretek konfigurálása a Cloud Services](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Használata esetén **Cloud Services**, meghatározhatja a feldolgozást, és emellett a rugalmas méretezés az alkalmazás eléréséhez több szerepkört. Minden felhőszolgáltatás egy vagy több webes szerepkörök és/vagy feldolgozói szerepkörök, mindegyik a saját alkalmazásfájlok és -konfigurációt tartalmaz. Meg is növelheti cloud service által üzembe helyezett egy szerepkörhöz szerepkörpéldányokat (virtuális gépek) számának növelése és a skálázási egy felhőalapú szolgáltatás szerepkör-példányok számának csökkentésével. Részletes információkért lásd: [Azure üzemeltetési modelljei](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Horizontális felskálázás beépített Azure magas rendelkezésre állás támogatását keresztül érhető el [Cloud Services, a virtuális gépek és a virtuális hálózati szolgáltatási szerződést](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) és a Load Balancer.<br/><br/>Többrétegű alkalmazás esetében azt javasoljuk, hogy a webes/feldolgozói szerepkörök alkalmazás adatbázis-kiszolgáló virtuális gépek az Azure virtuális hálózaton keresztül kapcsolódik. Emellett Azure terheléselosztást biztosít a virtuális gépek ugyanazon a felhőszolgáltatáson, a felhasználói kérések terjedhetnek azok között. Ezzel a módszerrel csatlakoztatott virtuális gépek közvetlenül tudnak kommunikálni egy másik az Azure adatközponton belül a helyi hálózaton keresztül.<br/><br/>Beállíthat **automatikus skálázási** az Azure Portalon, valamint az ütemezett idő. További információkért lásd: [konfigurálása az automatikus skálázást a portál egy felhőalapú szolgáltatás](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Skálázható felfelé és lefelé**: Akkor is növelheti/csökkentheti a webhely számára fenntartott példány (VM) mérete.<br/><br/>Horizontális felskálázás: Több fenntartott példányai (VM) adhat hozzá a webhelyhez.<br/><br/>Beállíthat **automatikus skálázási** a portálon, valamint az ütemezett idő. További információkért lásd: [méretezhető webes alkalmazások hogyan](../../../app-service/web-sites-scale.md). |

Ezen programozási módszerek közötti választáshoz további információkért lásd: [Azure Web Apps, a Cloud Services és a virtuális gépek: Mikor érdemes használni, amely](../../../app-service/overview-compare.md).

## <a name="next-steps"></a>További lépések
További információ az Azure-beli virtuális gépeken futó SQL Server: [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).


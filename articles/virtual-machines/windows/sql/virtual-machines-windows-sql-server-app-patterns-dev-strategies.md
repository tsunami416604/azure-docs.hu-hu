---
title: SQL Server alkalmazás mintákat a virtuális gépek |} Microsoft Docs
description: Ez a cikk az Azure virtuális gépeken futó SQL Server alkalmazás mintákat ismerteti. Az megoldás mérnökök és a fejlesztők alapokat nyújt jó alkalmazás felépítéséről és kialakításáról.
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
ms.openlocfilehash: 6bfb3092d6b4776499f957183613c951174b1a50
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754515"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Azure-beli virtuális gépeken futó SQL Server – alkalmazásminták és fejlesztési stratégiák
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Összefoglalás:
Annak meghatározása, mely alkalmazásminta és minták használatával az SQL Server-alapú alkalmazások az Azure-ban környezet egy fontos tervezési döntés és hogy az SQL Server és Azure infrastruktúra-összetevőkhöz együttműködni alapos ismerete szükséges. Az SQL Server az Azure infrastruktúra-szolgáltatásokat ezt könnyen áttelepítése, karbantartása, és figyelheti a meglévő SQL Server-alkalmazások beépített-kiszolgálón a Windows Azure virtuális gépekkel.

Ez a cikk célja megoldás mérnökök és a fejlesztők alapot nyújt a megfelelő alkalmazás felépítéséről és kialakításáról, amely azt a meglévő alkalmazások Azure, valamint az Azure-ban új alkalmazások fejlesztése áttelepítésekor követve.

Minden alkalmazás minta megkeresi, hogy egy helyszíni forgatókönyvet, a megfelelő a felhőalapú megoldás és a kapcsolódó technikai javaslatokat tartalmaz. Emellett a cikk ismerteti, amelyek Azure-specifikus fejlesztési stratégiák, hogy az alkalmazások helyesen kialakítani. A sok lehetséges alkalmazás minták miatt ajánlott mérnökök és a fejlesztők leginkább megfelelő minta az alkalmazásaikat és a felhasználók kell kiválasztani.

**Műszaki közreműködők:** Luis Carlos Vargas hering Madhan Arumugam Ramakrishnan

**Műszaki véleményezők:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, lengyel Schackow Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Bevezetés
A másik alkalmazási rétegek hasonlóan külön összetevők különböző gépeken, valamint az összetevők elválasztva fejleszthet számos különböző típusú n szintű alkalmazásokhoz. Például az ügyfélalkalmazás elhelyezheti, és üzleti szabályok egy számítógépen, az előtérbeli webes réteg és az adatelérési réteg összetevők egy másik gépen, és egy másik gépen háttér-adatbázis-rétegből összetevőket. Az ilyen szerkezetének kialakítása segít elkülöníteni az egyes rétegek egymástól. Ha módosítja az adatok eredetének, nem módosítani szeretné az ügyfél vagy a webes alkalmazás, de a hozzáférési réteg adatösszetevők.

Egy tipikus *n szintű* alkalmazás tartalmazza a bemutatási szint, az üzleti szint és az adatréteg:

| Szint | Leírás |
| --- | --- |
| **Bemutató** |A *bemutatási szint* (webes réteg, előtér-réteg), amelyben a felhasználók interakcióba az alkalmazás a réteg. |
| **Üzleti** |A *üzleti szint* (középső réteg) a réteget, a bemutatási szinten, és az adatszint egymással kommunikálnak, és a rendszer a fő funkciókat tartalmazza. |
| **Adatok** |A *adatszinten* alapjában véve a kiszolgáló, amely az alkalmazás adatait (például egy SQL Servert futtató kiszolgáló) tárolja. |

Alkalmazás rétegek ismertetik a logikai csoportosítás a működőképesség és összetevők alkalmazás; mivel a rétegek ismertetik a működőképesség és összetevők fizikai terjesztési külön fizikai kiszolgálók, számítógépek, hálózatok és a távoli helyeken. Az alkalmazások rétegek előfordulhat, hogy egy fizikai számítógépen (az azonos tartozó) elhelyezve, vagy előfordulhat, hogy külön számítógépeken (n szintű) kell elosztva, és az egyes rétegekhez összetevői összetevőket más rétegeiből jól meghatározott felületeken keresztül folytatott kommunikációhoz. Az eltolásokat tekintheti a kifejezés réteg n szintű, kétrétegű és háromrétegű például fizikai terjesztési mintákat nézve. A **2 szintű alkalmazásminta** két alkalmazásrétegek tartalmazza: application server és adatbázis-kiszolgáló. A közvetlen kommunikációt történik, a kiszolgáló és az adatbázis-kiszolgáló között. A kiszolgáló web-réteg és a vállalati szintű összetevőket tartalmaz. A **3 szintű alkalmazás mintát**, nincsenek az alkalmazás három réteg: a webkiszolgálók, alkalmazáskiszolgáló, amely tartalmazza az üzleti logikai rétegből és/vagy az üzleti szint adat-hozzáférési összetevőket, és az adatbázis-kiszolgáló. A webalkalmazás-kiszolgáló és az adatbázis-kiszolgáló közötti kommunikáció a kiszolgáló keresztül történik. Alkalmazás rétegeken és rétegek részletes információkért lásd: [Microsoft alkalmazás Referenciaarchitektúra útmutatója](https://msdn.microsoft.com/library/ff650706.aspx).

A cikk elolvasása előtt rendelkeznie kell az SQL Server és Azure alapvető fogalmait a Tudásbázis. További információ: [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server Azure virtuális gépek](virtual-machines-windows-sql-server-iaas-overview.md) és [Azure.com webhelyre](https://azure.microsoft.com/).

A cikk több alkalmazás mintázatokat lehet, hogy megfelelő-e az egyszerű alkalmazások, valamint a nagyon összetett vállalati alkalmazások. Minden minta részletező, mielőtt azt javasoljuk, hogy tanulmányozza át az Azure-ban rendelkezésre álló adatok tárolási szolgáltatásokkal, mint [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md), és [SQL Egy Azure virtuális gépen Server](virtual-machines-windows-sql-server-iaas-overview.md). Ahhoz, hogy a legjobb kialakításakor a gazdafürttagok számára az alkalmazások, ismerje meg, mely adatokat tároló szolgáltatás egyértelműen használatával.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Válassza ki az SQL Server Azure virtuális gépre, ha:
* Vezérlő SQL Server és a Windows van szüksége. Például Ebbe beletartozhatnak az SQL Server-verzió, különleges gyorsjavítások, teljesítmény konfigurációs, stb.
* Kell egy teljes körű kompatibilitásra van a helyszíni SQL Server és a meglévő alkalmazások Azure-ba való áthelyezéséhez-van.
* Az Azure környezetbe lehetőségeinek kihasználásához szeretne, de az Azure SQL Database nem támogatja az alkalmazás által igényelt összes funkcióját. Ez magában foglalhatja a következő területeken:
  
  * **Adatbázis mérete**: időpontjában a cikk frissült, SQL-adatbázis egy adatbázis, akár 1 TB-os adatok támogatja. Ha az alkalmazás által igényelt több mint 1 TB adatot, és nem kívánja az egyéni horizontális megoldások megvalósítását, ajánlott használt SQL Server egy Azure virtuális gépen. A legfrissebb információkat lásd: [Scaling Out Azure SQL-adatbázisok](https://msdn.microsoft.com/library/azure/dn495641.aspx), [DTU-Based vásárlási modell](../../../sql-database/sql-database-service-tiers-dtu.md), és [vCore-alapú beszerzés modell](../../../sql-database/sql-database-service-tiers-vcore.md)(előzetes verzió).
  * **HIPAA megfelelőségi**: egészségügyi ügyfelek, valamint a független szoftverszállítók (ISV) érdemes választania [SQL Server Azure virtuális gépek](virtual-machines-windows-sql-server-iaas-overview.md) helyett [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) mert az SQL Server az Azure virtuális gép által HIPAA üzleti társítsa a szerződés (BAA) vonatkozik. A megfelelőségi információk: [Microsoft Azure Trust Center: megfelelőségi](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Példányszintű szolgáltatások**: jelenleg SQL-adatbázis nem támogatja a funkciókat, amelyek az adatbázison kívül live (például a csatolt kiszolgálók, ügynök feladatok, FileStream, Service Broker, stb.). További információkért lásd: [Azure SQL adatbázis tudnivalók és korlátozások](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1-réteg (egyszerű): egyetlen virtuális gép
Az alkalmazás a mintában telepít az SQL Server alkalmazás és az adatbázis egy különálló virtuális gépet az Azure-ban. Ugyanaz a virtuális gép tartalmazza az ügyfél vagy webes alkalmazás, az üzleti összetevők, az adatelérési réteg és az adatbázis-kiszolgáló. A bemutatási, üzleti és adat-hozzáférési kód logikailag el egymástól, de fizikailag egy egyetlen kiszolgálós számítógép. A legtöbb ügyfél a alkalmazásminta kezdődnie, és majd, hogy a horizontális felskálázáshoz további webes szerepkörök vagy virtuális gépeket ad hozzá a rendszer.

A alkalmazásminta akkor hasznos, ha:

* Szeretne Azure platformon, hogy a platform vagy nem ad választ az alkalmazás követelményeinek kiértékelése egyszerű áttelepítést végezni.
* Meg szeretné tartani az Azure adatközpontba rétegek közötti késés csökkentése érdekében az azonos virtuális gépen található összes alkalmazás rétege.
* A gyors kiépítése fejlesztési és tesztelési környezetben rövid időszakokra, érdemes.
* Szeretne végezni a különböző munkaterhelési szintek tesztelési hibanaplóit, de egyszerre nem szeretné, hogy saját, és több fizikai gép folyamatosan karbantartása.

A következő ábra azt mutatja be egy egyszerű helyszíni forgatókönyv, és hogyan telepítheti az engedélyezett felhőalapú megoldás egyetlen virtuális gépre az Azure-ban.

![1 szintű alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Az üzleti réteg (üzleti logikát és adatokat érik el a összetevőket) telepítése a bemutató rétegként ugyanabban a fizikai rétegben maximalizálhatja alkalmazás teljesítményét, kivéve, ha egy külön réteget méretezhetőség és a biztonsági okok miatt kell használnia.

Mivel ez egy nagyon gyakori mintát kezdődnie, hasznosak lehetnek a következő cikk az áttelepítési áthelyezi adatait az SQL Server virtuális gép számára: [adatbázis Migrálása az SQL Server egy Azure virtuális gépen](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 rétegű (egyszerű): több virtuális gép
Az alkalmazás a mintában alkalmazást telepít központilag 3 szintű az Azure-ban úgy, hogy minden egyes alkalmazásszinten jelentkezett a másik virtuális gépet. Ez egy egyszerű méretezett és kibővített forgatókönyvek rugalmas környezetet biztosít. Ha egy virtuális gépet tartalmaz az ügyfél vagy webes alkalmazás, egy másikra a üzleti összetevőit üzemelteti, és az adatbázis-kiszolgáló futtatja egy másikra.

A alkalmazásminta akkor hasznos, ha:

* Azt szeretné, az összetett adatbázis-alkalmazások az Azure virtuális gépek áttelepítését.
* Azt szeretné, hogy másik alkalmazás rétegek különböző régiókban is működtetnek. Például előfordulhat, hogy megosztott adatbázisok, több régióba jelentéskészítési célból van telepítve.
* Vállalati alkalmazások áthelyezése a helyszíni virtualizált platformok Azure virtuális gépek szeretné. A vállalati alkalmazások részletes tárgyalását lásd: [Mi az vállalati alkalmazás](https://msdn.microsoft.com/library/aa267045.aspx).
* A gyors kiépítése fejlesztési és tesztelési környezetben rövid időszakokra, érdemes.
* Szeretne végezni a különböző munkaterhelési szintek tesztelési hibanaplóit, de egyszerre nem szeretné, hogy saját, és több fizikai gép folyamatosan karbantartása.

A következő ábra azt mutatja be, hogy hogyan helyezhető egy egyszerű 3 szintű alkalmazás az Azure-ban úgy, hogy minden egyes alkalmazásszinten jelentkezett a másik virtuális gépet.

![3 rétegű alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Az alkalmazás ebben a mintában van csak egy virtuális gép (VM) minden egyes rétegben. Ha több virtuális gép az Azure-ban, azt javasoljuk, hogy hozza létre a virtuális hálózatot. [Azure-beli virtuális hálózat](../../../virtual-network/virtual-networks-overview.md) hoz létre a megbízható biztonsági határ, és lehetővé teszi virtuális gépek kommunikálhassanak egymással a magánhálózati IP-cím. Ezenkívül mindig győződjön meg arról, hogy az összes internetes kapcsolatok csak Ugrás a bemutatási szint. Ha az alkalmazás mintát a következő kezelése a hálózati biztonsági csoportszabályok való hozzáférést. További információkért lásd: [külső férhessenek hozzá a virtuális Gépet az Azure portál használatával](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

A diagramon internetes protokollokban TCP, UDP, HTTP vagy HTTPS lehet.

> [!NOTE]
> Az Azure virtuális hálózat létrehozása az ingyenesen elérhető. Azonban a VPN-átjáró, amely a helyszíni van szó. Ez a díj a kapcsolat számára kiosztott és rendelkezésre álló időn alapul.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2 rétegbeli és 3 szintű a bemutatási szint kibővített
Az alkalmazás a mintában a rétegének 2 vagy 3 szintű adatbázis-alkalmazás az Azure virtuális gépek úgy, hogy minden egyes alkalmazásszinten jelentkezett a másik virtuális gépet telepít. Emellett a megnövekedett mennyiségű bejövő ügyfélkérelmeket miatt bemutatási szint kiterjesztése.

A alkalmazásminta akkor hasznos, ha:

* Vállalati alkalmazások áthelyezése a helyszíni virtualizált platformok Azure virtuális gépek szeretné.
* A bemutató réteg miatt nagyobb mennyiségű bejövő ügyfélkérelmeket szeretné.
* A gyors kiépítése fejlesztési és tesztelési környezetben rövid időszakokra, érdemes.
* Szeretne végezni a különböző munkaterhelési szintek tesztelési hibanaplóit, de egyszerre nem szeretné, hogy saját, és több fizikai gép folyamatosan karbantartása.
* Be kíván állítani az igény szerinti fel és le is méretezhető hálózatiinfrastruktúra-környezeten.

A következő ábra bemutatja, hogyan elhelyezheti az alkalmazásrétegek az Azure-ban több virtuális gép által a bemutatási szint nagyobb mennyiségű bejövő ügyfélkérelmeket miatt kiterjesztése. Az ábrán látható, Azure Load Balancer felelős forgalom terjesztésére el több virtuális gépre, és melyik webkiszolgálón való csatlakozáshoz is meghatározása. Egy terheléselosztó mögött a webes kiszolgálók több példányára biztosítja a magas rendelkezésre állását a bemutatási szint.

![Alkalmazásminta - bemutatási szint kibővítési](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Ajánlott eljárások az alacsonyabb szintű 2, 3 szintű vagy n szintű mintákat, amelyek több virtuális gép egy rétegben
Javasoljuk, hogy hová kívánja helyezni a virtuális gépek, amelyek ugyanabban a rétegben az azonos felhőalapú szolgáltatás, és ugyanabban a rendelkezésre állási beállítása. Például a webkiszolgálók olyan készlete helyezze **CloudService1** és **AvailabilitySet1** és az adatbázis-kiszolgálók egy csoportja **CloudService2** és  **AvailabilitySet2**. Rendelkezésre állási készlet Azure lehetővé teszi a magas rendelkezésre állású csomópontok helyezzen külön tartalék tartományok és frissítési tartományt.

Használja ki a virtuális gép több példányára olyan réteghez, konfigurálja az Azure Load Balancer alkalmazásrétegek közötti kell. Konfigurálja a terheléselosztó minden egyes rétegben, hozzon létre egy elosztott terhelésű végpont az egyes rétegek virtuális gépeken futó külön-külön. Egy konkrét csomagot kiválasztani először hozzon létre virtuális gépek ugyanazt a felhőszolgáltatásban található. Ez biztosítja, hogy rendelkeznek-e az azonos nyilvános virtuális IP-cím. Következő lépésként hozzon létre egy végpontot a virtuális gépek az adott réteg sablonbeállításait egyik. Majd rendelje hozzá a azonos végpont más virtuális gépeket az adott réteg sablonbeállításait terheléselosztás. Hozzon létre egy elosztott terhelésű készlet, forgalom szét több virtuális gép, és azt is, hogy a terheléselosztó meghatározni, melyik csomópontján való csatlakozás, ha a háttérkiszolgáló VM csomópont meghibásodik. Például egy terheléselosztó mögött a webes kiszolgálók több példányára biztosítja a magas rendelkezésre állását a bemutatási szint.

Ajánlott eljárásként mindig győződjön meg arról, hogy minden hálózati kapcsolatok használata esetén nyissa meg a bemutatási szint. A bemutató réteg fér hozzá az üzleti szint, és ezután az üzleti szint hozzáfér-e az adatréteg. Engedélyezi a hozzáférést a bemutató réteghez kapcsolatos további információkért lásd: [külső férhessenek hozzá a virtuális Gépet az Azure portál használatával](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Vegye figyelembe, hogy a helyszíni környezetben terheléselosztókhoz hasonlóan működik-e a terheléselosztó az Azure-ban. További információkért lásd: [Azure infrastruktúra-szolgáltatásokat a terheléselosztás](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ezenkívül azt javasoljuk, állítsa be a magánhálózati a virtuális gépek számára az Azure Virtual Network használatával. Ez lehetővé teszi őket kommunikálhassanak egymással a magánhálózati IP-címet. További információkért lásd: [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2 rétegbeli és 3 szintű az üzleti szint kibővített
Az alkalmazás a mintában a rétegének 2 vagy 3 szintű adatbázis-alkalmazás az Azure virtuális gépek úgy, hogy minden egyes alkalmazásszinten jelentkezett a másik virtuális gépet telepít. Emellett érdemes terjesztheti a több virtuális gép az alkalmazás összetettsége miatt alkalmazás kiszolgáló-összetevők.

A alkalmazásminta akkor hasznos, ha:

* Vállalati alkalmazások áthelyezése a helyszíni virtualizált platformok Azure virtuális gépek szeretné.
* Az alkalmazás kiszolgáló-összetevők az alkalmazás összetettsége miatt több virtuális gép terjeszteni szeretné.
* Szeretné helyezni az üzleti logika gyakori helyszíni LOB-(üzleti-) alkalmazások az Azure virtuális gépeken. LOB-alkalmazások olyan kritikus fontosságú számítógépes alkalmazások, amelyek fontos vállalati, például a számviteli, emberi erőforrások (HR), bérlista, ellátásilánc és erőforrás-tervező alkalmazások futtatása.
* A gyors kiépítése fejlesztési és tesztelési környezetben rövid időszakokra, érdemes.
* Szeretne végezni a különböző munkaterhelési szintek tesztelési hibanaplóit, de egyszerre nem szeretné, hogy saját, és több fizikai gép folyamatosan karbantartása.
* Be kíván állítani az igény szerinti fel és le is méretezhető hálózatiinfrastruktúra-környezeten.

A következő ábra azt mutatja be, egy helyszíni forgatókönyv és annak engedélyezve felhőalapú megoldás. Ebben a forgatókönyvben a több virtuális gép az Azure-ban az alkalmazásrétegek által az üzleti szint, amely tartalmazza az üzleti logikai rétegből és az access összetevők kiterjesztése elhelyezni. Az ábrán látható, Azure Load Balancer felelős forgalom terjesztésére el több virtuális gépre, és melyik webkiszolgálón való csatlakozáshoz is meghatározása. Egy terheléselosztó mögött az alkalmazás-kiszolgálók több példányára biztosítja az üzleti szint magas rendelkezésre állását. További információkért lásd: [ajánlott eljárások, amelyek több virtuális gép egy rétegben rétegének 2, 3 szintű vagy n szintű alkalmazás mintára](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Az üzleti szint kibővítési alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 rétegbeli és 3 szintű megjelenítési és üzleti rétegek kibővített és HADR
Az alkalmazás a mintában a rétegének 2 vagy 3 szintű adatbázis-alkalmazás az Azure virtuális gépek a bemutatási szint (webkiszolgáló) és az üzleti szint (kiszolgáló) összetevők több virtuális gép elosztásával telepít. Magas rendelkezésre állású és vész-helyreállítási megoldások emellett az adatbázisokat az Azure virtuális gépeken a megvalósítása.

A alkalmazásminta akkor hasznos, ha:

* Áthelyezni kívánt vállalati alkalmazások a helyszíni virtualizált platformon az Azure SQL Server magas rendelkezésre állású és vész-helyreállítási funkciók alkalmazásával.
* Szeretné kiterjesztése a bemutatási szint és az üzleti szint nagyobb mennyiségű bejövő ügyfélkérelmek és az alkalmazás összetettsége miatt.
* A gyors kiépítése fejlesztési és tesztelési környezetben rövid időszakokra, érdemes.
* Szeretne végezni a különböző munkaterhelési szintek tesztelési hibanaplóit, de egyszerre nem szeretné, hogy saját, és több fizikai gép folyamatosan karbantartása.
* Be kíván állítani az igény szerinti fel és le is méretezhető hálózatiinfrastruktúra-környezeten.

A következő ábra azt mutatja be, egy helyszíni forgatókönyv és annak engedélyezve felhőalapú megoldás. Ebben a forgatókönyvben a horizontális a bemutatási szint és az üzleti szint összetevőket a több virtuális gép az Azure-ban. Ezenkívül megvalósításához a magas rendelkezésre állási és vészhelyreállítási (HADR) helyreállítással az SQL Server-adatbázisok Azure-ban.

Egy alkalmazás több példánya különböző futó virtuális gépek ellenőrizze, hogy meg terheléselosztásának kérelmek őket. Ha több virtuális gép van, győződjön meg arról, hogy a virtuális gépek elérhető és egy pontján futó időben kell. Konfigurálnia kell a terheléselosztást, ha az Azure terheléselosztó nyomon követi a virtuális gépek állapotát, és megfelelően irányítja a bejövő hívások kifogástalan működő virtuális gép csomópontra. Állítsa be a virtuális gépek terheléselosztás információkért lásd: [Azure infrastruktúra-szolgáltatásokat a terheléselosztás](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Egy terheléselosztó mögött a web- és alkalmazáskiszolgálók kiszolgálók több példányára biztosítja a magas rendelkezésre állású, hogy a megjelenítési és üzleti rétegek.

![A kibővített és magas rendelkezésre állás](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Ajánlott eljárások az SQL HADR igénylő alkalmazás-minták
SQL Server magas rendelkezésre állású és vész-helyreállítási megoldások Azure virtuális gépek beállításakor használata a virtuális gépek virtuális hálózat beállítása [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) megadása kötelező.  A virtuális hálózaton belüli virtuális gépek egy stabil magánhálózati IP-cím lesz a szolgáltatás leállás után is, így elkerülheti a DNS-név feloldása szükséges frissítés időpontja. A virtuális hálózat továbbá lehetővé teszi a helyszíni hálózat kiterjesztése az Azure-ba, és hoz létre a megbízható biztonsági határ. Ha az alkalmazás rendelkezik vállalati tartomány korlátozása (mint például a Windows-hitelesítést, az Active Directory), például beállítása [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) szükséges.

A legtöbb, rendszert futtató ügyfelek éles kódban az Azure-on, elsődleges és másodlagos replikák megakadályozzák az Azure-ban.

Átfogó információkat és a magas rendelkezésre állású és vész-helyreállítással kapcsolatos bemutatók, [magas rendelkezésre álláshoz és Vészhelyreállításhoz az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>2 rétegbeli és 3 szintű Azure virtuális gépek és Felhőszolgáltatások
Az alkalmazás a mintában használatával kíván üzembe helyezni az Azure-bA rétegének 2 vagy 3 szintű alkalmazás mindkét [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (webes és feldolgozói szerepkörök - platformok (PaaS)) és [Azure virtuális gépek](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) () Infrastruktúrák (IaaS)). Használatával [Azure Felhőszolgáltatások](https://azure.microsoft.com/documentation/services/cloud-services/) a bemutatási szint/üzleti szint és az SQL Server [Azure virtuális gépek](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az adatok érvényben lévő korlát miatt a legtöbb alkalmazás Azure-on futó előnyös. A hiba oka, hogy egy, a Cloud Services számítási példány összevonása egy egyszerűen kezelhető, telepítési, figyelés és kibővített.

A Cloud Serviceshez Azure az infrastruktúra az Ön kezeli, ütemezett karbantartás hajt végre, az operációs rendszerek javításokkal és megpróbálja helyreállítani a szolgáltatás és a hardveres hibák. Ha az alkalmazás kibővített, automatikus és manuális kibővített lehetőség áll rendelkezésre a felhőszolgáltatás-projekt növelésével vagy csökkentésével példányok vagy az alkalmazás által használt virtuális gépek számát. Emellett a helyszíni Visual Studio segítségével az alkalmazás az Azure-ban a felhőszolgáltatás-projekt telepítése.

Összefoglalva Ha nem kívánja saját széles skáláját bemutató/üzleti felügyeleti feladatok réteg és az alkalmazás nem kell a szoftver vagy az operációs rendszer összetett konfigurálni, használja az Azure Cloud Services. Ha az Azure SQL Database nem támogatja a keresett összes funkcióját, használja az SQL Server egy Azure virtuális gépen az adatréteg. Azure Cloud Services egy alkalmazást futtat, és az Azure virtuális gépek adatainak tárolásához egyesíti a mindkét szolgáltatás előnyeit. Részletes összehasonlítását, lásd a témakör [összehasonlítása az Azure-ban fejlesztési stratégiák](#comparing-web-development-strategies-in-azure).

Alkalmazás ebben a mintában a bemutatási szint tartalmaz egy webes szerepkör, amely Cloud Services összetevőt az Azure végrehajtási környezetben fut, és azt testreszabott webes alkalmazásprogramozási IIS és ASP.NET támogatja. A vállalat vagy a háttér-réteg a feldolgozói szerepkör, amely Cloud Services összetevőt az Azure végrehajtási környezetben fut, és akkor hasznos, ha általánosított fejlesztési és hajthat végre a háttérben történő feldolgozás a webes szerepkör magában foglalja. Az adatbázis-rétegből egy SQL Server virtuális gép az Azure-ban található. A bemutatási szint és az adatbázis-rétegből közötti kommunikáció közvetlenül, vagy az üzleti szint – a feldolgozói szerepkör összetevők keresztül történik.

A alkalmazásminta akkor hasznos, ha:

* Áthelyezni kívánt vállalati alkalmazások a helyszíni virtualizált platformon az Azure SQL Server magas rendelkezésre állású és vész-helyreállítási funkciók alkalmazásával.
* Be kíván állítani az igény szerinti fel és le is méretezhető hálózatiinfrastruktúra-környezeten.
* Az Azure SQL-adatbázis nem támogatja, amelyet az alkalmazás vagy az adatbázis összes funkcióját.
* Szeretne végezni a különböző munkaterhelési szintek tesztelési hibanaplóit, de egyszerre nem szeretné, hogy saját, és több fizikai gép folyamatosan karbantartása.

A következő ábra azt mutatja be, egy helyszíni forgatókönyv és annak engedélyezve felhőalapú megoldás. Ebben a forgatókönyvben a bemutatási szint webes szerepkör, a feldolgozói szerepkörök az üzleti szint, de a virtuális gépek Azure-ban az adatréteg helyezze el. A bemutatási szint több példányát futtató különböző webes szerepkörök biztosítja, hogy ezek között érkező kérések elosztása betölteni. Azure Cloud Services Azure virtuális gépekkel kombinálásával, azt javasoljuk, hogy [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) is. A [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), akkor is rendelkezik stabil, és ugyanahhoz az állandó magánhálózati IP-címek a felhőalapú szolgáltatás a felhőben. Adja meg a virtuális hálózat a virtuális gépek számára, és a felhőalapú szolgáltatások, miután indításához egymással kommunikáló a magánhálózati IP-címet. Ezenkívül rendelkező virtuális gépek és az Azure webes vagy feldolgozói szerepköröket ugyanazon [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) alacsony késéssel és további biztonságos kapcsolatot nyújt. További információkért lásd: [Mi az a cloud service](../../../cloud-services/cloud-services-choose-me.md).

Az ábrán látható, az Azure Load Balancer osztja el a forgalmat több virtuális gép között, és is meghatározza, mely webkiszolgáló vagy -alkalmazáskiszolgáló csatlakozzon. Egy terheléselosztó mögött a web- és alkalmazáskiszolgálók kiszolgálók több példányára biztosítja a magas rendelkezésre állás a bemutatási szint és az üzleti szint. További információkért lásd: [ajánlott eljárásokat, az alkalmazás mintákra igénylő SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Alkalmazás-minták a Felhőszolgáltatásokkal.](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

A alkalmazásminta végrehajtásához egy másik megoldás, a használatára egy összevont webes szerepkör, amely a bemutatási szint és az üzleti szint összetevők a következő ábrán látható módon is tartalmaz. A alkalmazásminta akkor hasznos, állapot-nyilvántartó tervezési igénylő alkalmazások. Mivel Azure állapot nélküli számítási csomópontok Ez a webes és feldolgozói szerepkörök, azt javasoljuk, hogy egy programot az alábbi technológiák valamelyikével munkamenet-állapot tárolásához megvalósítása: [Azure gyorsítótárazás](https://azure.microsoft.com/documentation/services/redis-cache/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) vagy [Azure SQL adatbázis](../../../sql-database/sql-database-technical-overview.md).

![Alkalmazás-minták a Felhőszolgáltatásokkal.](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Minta Azure virtuális gépek, az Azure SQL Database és Azure App Service (webalkalmazások)
A alkalmazásminta elsődleges célja mutatjuk be, hogyan kombinálhatók az Azure-infrastruktúra az Azure platform,--szolgáltatás-összetevőivel (PaaS) a megoldásban a szolgáltatási (IaaS) összetevőként. Ebben a mintában arra irányul, hogy az Azure SQL Database relációs adatok tárolására. Nem tartalmazza az SQL Server Azure virtuális gépre, amely a szolgáltatásajánlat Azure infrastruktúra része.

Az alkalmazás a mintában alkalmazást telepít központilag az adatbázis az Azure-bA helyezi el a megjelenítési és üzleti rétegek ugyanahhoz a virtuális géphez, és az Azure SQL Database (SQL-adatbázis) kiszolgálók adatbázis eléréséhez. A bemutatási szint a hagyományos IIS-alapú webes megoldások segítségével valósíthatja meg. Vagy segítségével Megvalósíthat egy kombinált megjelenítési és üzleti szint [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

A alkalmazásminta akkor hasznos, ha:

* Már van egy meglévő SQL adatbázis-kiszolgáló konfigurálva az Azure-ban, és az alkalmazás gyors teszteléséhez.
* Az Azure-alapú környezetben képességeit vizsgálni kívánt.
* A gyors kiépítése fejlesztési és tesztelési környezetben rövid időszakokra, érdemes.
* Lehet, hogy az üzleti logikát és adatokat hozzáférés összetevői önálló webalkalmazásból.

A következő ábra azt mutatja be, egy helyszíni forgatókönyv és annak engedélyezve felhőalapú megoldás. Ebben a forgatókönyvben helyezze el az alkalmazás szinteket az Azure SQL adatbázis Azure és a hozzáférési adatok egyetlen virtuális gépen.

![Vegyes alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Ha egy kombinált web- és alkalmazásszinten jelentkezett Azure Web Apps segítségével megvalósítható választja, azt javasoljuk, hogy őrizze meg a középső rétegbeli vagy alkalmazás szint, dinamikus kötésű kódtárakon (dll) webalkalmazás kontextusában.

Emellett tekintse át a ajánlottak a [összehasonlítása az Azure-ban webes fejlesztési stratégiák](#comparing-web-development-strategies-in-azure) szakasz tudhat meg többet programozási módszerek a cikk végén.

## <a name="n-tier-hybrid-application-pattern"></a>N szintű hibrid alkalmazásminta
N szintű hibrid alkalmazás mintában valósítja meg az alkalmazást a helyszíni és az Azure között megoszlanak a többrétegű konfigurációk. Ezért, hozzon létre egy rugalmas és újrafelhasználható hibrid rendszer, amely módosítja vagy egy konkrét csomagot kiválasztani hozzáadása a más rétegek módosítása nélkül. Kiterjeszti a felhőbe a vállalati hálózathoz, a szoftver használatára [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) szolgáltatás.

A hibrid alkalmazásminta akkor hasznos, ha:

* Használni kívánt alkalmazásokat, amelyek részben a felhőben futtatható és részben helyszínen.
* A felhő egy meglévő helyszíni alkalmazást néhány vagy az összes elemeinek áttelepíteni kívánt.
* Vállalati alkalmazások áthelyezése a helyszíni virtualizált platformok Azure szeretné.
* Be kíván állítani az igény szerinti fel és le is méretezhető hálózatiinfrastruktúra-környezeten.
* A gyors kiépítése fejlesztési és tesztelési környezetben rövid időszakokra, érdemes.
* Azt szeretné, hogy a vállalati adatbázis-alkalmazások biztonsági másolatok készítése költséghatékony módon.

A következő ábra azt mutatja be, az n szintű hibrid alkalmazásminta, amely a helyszíni és az Azure módon történjen. Ahogy az ábrán is látható, a helyszíni infrastruktúrát a [Active Directory tartományi szolgáltatások](https://technet.microsoft.com/library/hh831484.aspx) tartományvezérlő felhasználó hitelesítési és engedélyezési támogatásához. Vegye figyelembe, hogy az ábra azt mutatja be, a forgatókönyv az adatréteg egyes részei lakhelyétől egy helyszíni adatközpontban mivel egyes részei az adatréteg élő Azure-ban. Attól függően, hogy az alkalmazás igényeinek több más hibrid környezetekben is létrehozható. Például előfordulhat, hogy tárolja a bemutatási szint és az üzleti szint egy helyszíni környezetben, de az adatréteg az Azure-ban.

![N szintű alkalmazásminta](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Az Azure-ban is használhatja az Active Directory önálló címtárak a szervezet számára, vagy is integrálhatja a meglévő helyszíni Active Directoryról szinkronizálva [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Az ábrán látható, az üzleti szint összetevők férhetnek hozzá több adatforrást, többek között a [SQL Server az Azure-ban](virtual-machines-windows-sql-server-iaas-overview.md) keresztül egy saját belső IP-cím, helyi SQL Server kiszolgálóhoz [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), vagy [SQL-adatbázis](../../../sql-database/sql-database-technical-overview.md) a .NET-keretrendszer data provider technológiák használata. Ezen a diagramon az Azure SQL Database egy nem kötelező adatokat tároló szolgáltatás.

N szintű hibrid alkalmazásminta az alábbi munkafolyamat a megadott sorrendben valósíthatja meg:

1. Azonosítsa a vállalati adatbázis-alkalmazások, amelyek használatával a felhőalapú kell helyezni a [Microsoft Assessment és tervezési (Leképezés) eszközkészlet](http://microsoft.com/map). A MAP eszközkészlet leltár-és teljesítményadatokat gyűjt a számítógépről, virtualizálási tervezi, és javaslatokat nyújt kapacitás és a assessment tervezési.
2. Tervezze meg az erőforrások és az Azure platformon, például a storage-fiókok és a virtuális gépek a szükséges konfiguráció.
3. Állítsa be a vállalati hálózathoz a helyszíni közötti hálózati kapcsolat és [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). A vállalati hálózathoz a helyszíni és az Azure virtuális gép közötti kapcsolat beállításához használja az alábbi két módszer egyikét:
   
   1. Nyilvános végpontok egy virtuális gépen keresztül a helyszíni és az Azure közötti kapcsolatot létesíteni az Azure-ban. Ez a módszer egy egyszerű telepítő, és lehetővé teszi az SQL Server-hitelesítés használatát a virtuális gépen. Ezenkívül a hálózati biztonsági csoport szabályok beállítása nyilvános forgalmat a virtuális gép. További információkért lásd: [külső férhessenek hozzá a virtuális Gépet az Azure portál használatával](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. A kapcsolatot a helyszíni és az Azure közötti Azure virtuális magánhálózatot (VPN-t) alagúton keresztül. Ez a módszer lehetővé teszi, hogy a tartományi házirendek az Azure virtuális géphez bővítése. Ezenkívül állítson be tűzfalszabályokat, és a virtuális gép Windows-hitelesítés használata. Azure jelenleg, biztonságos telephelyek közötti VPN és a pont-pont VPN-kapcsolatokat támogatja:
      
      * Biztonságos webhelyek kapcsolattal, a hálózati kapcsolat létrehozhat a helyszíni hálózat és a virtuális hálózat között az Azure-ban. A helyszíni adatközpont-környezetekben való kapcsolódáshoz Azure ajánlott.
      * Biztonságos pont – hely kapcsolatot, a hálózati kapcsolatot létesíthet a virtuális hálózat az Azure-ban és a bárhol futtató önálló számítógép között. Leginkább ajánlott fejlesztési és tesztelési célokra.
      
      Kapcsolódás Azure SQL-kiszolgálót a további információkért lásd: [csatlakozás az SQL Server virtuális gép Azure](virtual-machines-windows-sql-connect.md).
4. Ütemezett feladatok és a riasztásokat, amelyek a virtuális gép lemezének az Azure-ban a helyszíni adatok biztonsági mentését beállítása. További információkért lásd: [SQL Server biztonsági mentése és visszaállítása az Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx) és [biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-backup-recovery.md).
5. Attól függően, hogy az alkalmazás igényeinek valósíthatja meg a következő három gyakori forgatókönyvek közül:
   
   1. Tárolhatja a webkiszolgáló, a kiszolgáló és a kis-és nagybetűket adatok egy adatbázis-kiszolgálót az Azure-ban, míg a bizalmas adatokat a helyszínen tárolja.
   2. Hálózati adaptere esetében megtarthatja a webkiszolgáló és a kiszolgáló helyszíni mivel az adatbázis-kiszolgáló, az Azure virtuális gépen.
   3. Megtarthatja az adatbázis-kiszolgáló, webkiszolgáló, és a kiszolgáló helyszíni mivel a virtuális gépek Azure-ban tárolja az adatbázis-replikákat. Ez a beállítás lehetővé teszi, hogy a helyszíni kiszolgálók vagy az adatbázis-replikákat az Azure-jelentéskészítési alkalmazásokat. Így csökkenthető a helyi adatbázisban munkaterhelés érhet el. Azt javasoljuk, hogy ez a forgatókönyv gyakori az megvalósítása olvassa el a munkaterhelések és fejlesztési célokra. Információ az adatbázis-replikák létrehozása az Azure-ban: AlwaysOn rendelkezésre állási csoportok: [magas rendelkezésre álláshoz és Vészhelyreállításhoz az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Az Azure-ban webes fejlesztési stratégiák összehasonlítása
Valósítja meg, és egy többszintes Azure SQL Server-alapú alkalmazást telepít, használhatja a következő két programozási módszerek egyikét:

* Az SQL Server Azure virtuális gépek Azure- és hozzáférés adatbázisok hagyományos webkiszolgáló (IIS - Internet Information Services) beállítása.
* Alkalmazza, és egy felhőalapú szolgáltatás telepítése az Azure-bA. Ezt követően győződjön meg arról, hogy a felhőalapú szolgáltatás hozzáférhet-e az SQL Server adatbázisok Azure virtuális gépeken. Egy felhőalapú szolgáltatás több webes és feldolgozói szerepkörök tartalmazhatnak.

A következő táblázat a hagyományos webalkalmazás fejlesztése a Azure felhőalapú szolgáltatásairól és az Azure Web Apps SQL Server Azure virtuális gépek tekintetében összehasonlítása. A tábla tartalmazza az Azure Web Apps, mert lehetséges használandó SQL Server Azure virtuális gép adatforrás az Azure Web Apps keresztül a nyilvános virtuális IP-cím vagy a DNS-nevét.

|  | Hagyományos webes fejlesztése az Azure virtuális gépek | Azure cloud Services | Az Azure Web Apps webszolgáltatási |
| --- | --- | --- | --- |
| **A helyszíni alkalmazások áttelepítése** |Meglévő alkalmazások, például-van. |Alkalmazások webes és feldolgozói szerepkörök kell. |Meglévő alkalmazások, például-önálló webes alkalmazások és webszolgáltatások gyors méretezhetőség igénylő de illeszkedik. |
| **Fejlesztési és üzembe helyezés** |A Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS-kezelő PowerShell. |Visual Studio, az Azure SDK-val TFS, PowerShell. Minden felhőalapú szolgáltatás a szolgáltatás és konfigurációs telepítése két környezet rendelkezik: átmeneti és üzemi. Az átmeneti tesztelje azt, mielőtt előléptetés éles üzemhez egy felhőalapú szolgáltatás telepítése. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, Codeplex webhelyen, dropbox-ba, a GitHub, Mercurial, TFS, webes telepíti, PowerShell. |
| **Felügyelet és a telepítő** |Ön felelősséggel tartozik a alkalmazás, az adatokat, a tűzfalszabályok, a virtuális hálózati és az operációs rendszer a felügyeleti feladatokat. |Ön felelősséggel tartozik a az alkalmazás, az adatok, a tűzfalszabályok és a virtuális hálózati rendszergazdai feladatok. |Ön felelősséggel tartozik az alkalmazás és az adatok csak a felügyeleti feladatokat. |
| **Magas rendelkezésre állású és vész-helyreállítási (HADR)** |Azt javasoljuk, hogy az azonos rendelkezésre állási csoport és a felhő szolgáltatásának ugyanazt a virtuális gépek helyezze. Megőrzi a virtuális gépek ugyanabban a rendelkezésre állási csoport lehetővé teszi, hogy Azure helyezzen el a magas rendelkezésre állású csomópontok külön tartalék tartományok és frissítési tartományt. Ehhez hasonlóan tartani a virtuális gépek ugyanazt a felhőszolgáltatásban található lehetővé teszi a terheléselosztást, és virtuális gépek közvetlenül kommunikálhatnak egymással a helyi hálózaton belül egy Azure-adatközpont.<br/><br/>Felelőssége végrehajtásához egy magas rendelkezésre állású és vészhelyreállítási megoldás az SQL Server Azure virtuális gépek állásidő elkerülése érdekében. A támogatott HADR technológiákhoz, lásd: [magas rendelkezésre álláshoz és Vészhelyreállításhoz az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Ön felelősséggel tartozik a saját adatok és az alkalmazás biztonsági mentése.<br/><br/>Azure áthelyezheti a virtuális gépek, ha az adatközpontban lévő gazdaszámítógépen hardverproblémák miatt meghiúsul. Ezenkívül lehetnek a virtuális gép tervezett leállások gazdaszámítógépen biztonsági vagy szoftverek frissítésekor frissítések. Ezért azt javasoljuk, hogy legalább két virtuális gépek minden egyes alkalmazás rétegben a folyamatos rendelkezésre állás biztosításához megmaradjanak. Azure által nyújtott nem SLA-t egyetlen virtuális gépre. |Azure kezeli a mögöttes hardver vagy operációs rendszer szoftver eredő hibák. Azt javasoljuk, hogy az alkalmazás a magas rendelkezésre állásának biztosításához egy webes vagy feldolgozói szerepkör több példánya megvalósítása. További információ: [Felhőszolgáltatások, virtuális gépek és virtuális hálózati szolgáltatásiszint-megállapodás](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Ön felelősséggel tartozik a saját adatok és az alkalmazás biztonsági mentése.<br/><br/>Az adatbázisok SQL Server-adatbázisban egy Azure virtuális gép található Ön felelősséggel tartozik egy magas rendelkezésre állási és vészhelyreállítási helyreállítási megoldás megvalósításának állásidő elkerülése érdekében. Támogatott HDAR technológiákkal tekintse meg a magas rendelkezésre álláshoz és Vészhelyreállításhoz az SQL Server Azure virtuális gépeken.<br/><br/>**SQL Server adatbázis-tükrözés**: az Azure Cloud Services (webes/munkavégző szerepkörök) használja. SQL Server virtuális gépek és a felhőszolgáltatás-projekt lehet az azonos Azure virtuális hálózatban. Ha SQL Server virtuális gép nem ugyanazon a virtuális hálózaton, hozzon létre egy SQL Server-aliast kommunikációs útvonalat az SQL Server-példány szüksége. Emellett a alias nevének egyeznie kell az SQL Server neve. |Magas rendelkezésre állású Azure feldolgozói szerepköröket, az Azure blob storage és az Azure SQL Database öröklődik. Például az Azure Storage összes blob, table és várólista adatok három replikáit tárolja. Egy időben, az Azure SQL Database tartja a három futtató adatok replikáinak – egy elsődleges másodpéldány és két másodlagos replika. További információkért lásd: [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) és [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Ha SQL Server Azure virtuális gép adatforrásként a Azure Web Apps, vegye figyelembe, hogy az Azure Web Apps nem támogatja az Azure Virtual Network. Ez azt jelenti az SQL Server virtuális gépen, az Azure-ban az Azure Web Apps minden kapcsolat haladjon végig a virtuális gépek nyilvános végpontja. Ez a magas rendelkezésre állású és vész-helyreállítási eljárással korlátozások okozhatja. Például az ügyfélalkalmazást Azure-webalkalmazásokban SQL Server virtuális gép csatlakozik, az adatbázis-tükrözés nem tudná kapcsolódni az új elsődleges kiszolgáló, adatbázis-tükrözésre van szüksége, állítsa be az Azure virtuális hálózat között az SQL Server gazdagépen virtuális gépeket az Azure-ban. Ezért használatával **SQL Server adatbázis-tükrözés** az Azure Web Apps nem támogatott jelenleg.<br/><br/>**SQL Server AlwaysOn rendelkezésre állási csoportok**: állíthat be AlwaysOn rendelkezésre állási csoportok használata az Azure Web Apps SQL Server virtuális gépek Azure-ban. De kell konfigurálnia az AlwaysOn rendelkezésre állási csoport figyelőjének irányítja a kommunikációt az elsődleges másodpéldányhoz nyilvános elosztott terhelésű végpont keresztül. |
| **létesítmények közötti kapcsolat** |Azure-beli virtuális hálózathoz való kapcsolódáshoz a helyszíni használhatja. |Azure-beli virtuális hálózathoz való kapcsolódáshoz a helyszíni használhatja. |Azure-beli virtuális hálózat esetén támogatott. További információkért lásd: [Web Apps virtuális hálózati integráció](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Méretezhetőség** |A virtuális gépek méretét növelve, vagy további lemezek hozzáadása a méretezett áll rendelkezésre. További információ a virtuálisgép-méretek: [Azure virtuálisgép-méretek](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Adatbázis-kiszolgáló**: kibővített adatbázis particionálási technikák és az SQL Server AlwaysOn rendelkezésre állási csoportok keresztül érhető el.<br/><br/>Az olvasási munkaterhelést használhatja [AlwaysOn rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) több másodlagos csomópontot, valamint az SQL Server-replikációt.<br/><br/>A nagy írási munkaterhelések adja meg az alkalmazás kibővített több fizikai kiszolgálón vízszintes particionálási adatok valósíthat meg.<br/><br/>Továbbá használatával Megvalósíthat egy kibővített [SQL Server-adatok függő útválasztási](https://technet.microsoft.com/library/cc966448.aspx). Az adatok függő útválasztás (DDR), kell megvalósítani a particionálási mechanizmus az ügyfélalkalmazásban, általában a rétegben üzleti szint, az adatbázis-kérelmek több SQL Server-csomópontok útvonalat. Az üzleti szint leképezéseit az adatok particionálásáról, és melyik csomópont tartalmazza az adatokat tartalmazza.<br/><br/>Méretezhető virtuális gépeken futó alkalmazások. További információkért lásd: [alkalmazás méretezése](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Fontos megjegyzés**: A **automatikus skálázás** az Azure-szolgáltatás lehetővé teszi automatikusan növeli vagy csökkenti a virtuális gépek, az alkalmazás által használt. Ez a szolgáltatás biztosítja, hogy a végfelhasználói élmény nem érinti hátrányosan csúcsidőben, és a virtuális gépek állítsa le, amikor az igény szerinti alacsony. Javasoljuk, hogy nem állítja be az automatikus skálázási beállítás a felhőalapú szolgáltatás, ha az SQL Server virtuális gépeket tartalmaz. A hiba oka, hogy az automatikus skálázás funkció lehetővé teszi, hogy a virtuális gép bekapcsolása, ha ezt a virtuális Gépet a CPU-használat magasabb néhány küszöbértéknél, és kapcsolja ki a virtuális gép, ha a Processzor kihasználtsága alacsonyabb, mint az Azure. Az automatikus skálázás rendkívül hasznos állapot nélküli alkalmazások, például a webkiszolgálók, ahol minden virtuális gép kezelhetik az alkalmazások és szolgáltatások nélkül bármely korábbi állapotába mutató hivatkozásokat. Az automatikus skálázási funkciót azonban nem akkor hasznos, állapotalapú alkalmazásokhoz, például az SQL Server, ahol csak egy példány lehetővé teszi, hogy az adatbázisba írás. |Méretezett több webes és feldolgozói szerepkörök használatával érhető el. A webes és feldolgozói szerepkörök virtuálisgép-méretek kapcsolatos további információkért lásd: [Felhőszolgáltatások mérete konfigurálása](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Használata esetén **Felhőszolgáltatások**, megadhatja a több szerepkört ügyfélregisztrációs és is a rugalmas méretezést az alkalmazás eléréséhez. Minden felhőalapú szolgáltatás tartalmaz egy vagy több webes szerepkörök és/vagy feldolgozói szerepköröket, mindegyiket a saját fájljainak és a konfiguráció. Is méretezett egy felhőalapú szolgáltatás által növelje meg a szerepkörpéldányok (virtuális gépek) szerepkör telepítése és lefelé méretezéshez egy felhőalapú szolgáltatás csökkentik a szerepkörpéldányok számát. Részletes információkért lásd: [Azure végrehajtási modell](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Kibővített Azure magas rendelkezésre állást támogatását keresztül érhető el [Felhőszolgáltatások, virtuális gépek és virtuális hálózati szolgáltatásiszint-megállapodás](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) és a terheléselosztó hasonló adataival.<br/><br/>Egy többrétegű alkalmazást javasoljuk, hogy a webes vagy feldolgozói szerepkörök alkalmazás adatbázis-kiszolgáló virtuális gépek Azure virtuális hálózaton keresztül csatlakozni. Emellett Azure terheléselosztást biztosít virtuális gépek ugyanazt a felhőalapú szolgáltatást, mindegyik felhasználói kérelmek terjednek. Ezzel a módszerrel csatlakoztatott virtuális gépek közvetlenül kommunikálhatnak egymással a helyi hálózaton belül egy Azure-adatközpont.<br/><br/>Beállíthat **automatikus skálázás** az Azure-portálon, valamint az ütemezés. További információkért lásd: [konfigurálása az automatikus skálázás egy felhőalapú szolgáltatás, a portál](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Felfelé és lefelé méretezési**: növelése vagy csökkentése a példány (VM), a webhely fenntartott méretétől is.<br/><br/>A kibővítéshez: a webhely (VM) több fenntartott példányok is hozzáadhat.<br/><br/>Beállíthat **automatikus skálázás** a portálon, valamint az ütemezés. További információkért lásd: [méretezési webalkalmazások hogyan](../../../app-service/web-sites-scale.md). |

Ezen programozási módszerek közötti kiválasztásáról további információkért lásd: [Azure Web Apps, a Cloud Services és a virtuális gépek: mikor érdemes használni, amely](../../../app-service/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>További lépések
További információ az Azure virtuális gépeken futó SQL Server: [SQL Server Azure virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).


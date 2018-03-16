---
title: "Az Azure SQL adatbázis Azure esettanulmány - Daxko/CSI |} Microsoft Docs"
description: "További tudnivalók: hogyan Daxko/CSI SQL adatbázist használ annak érdekében, a fejlesztési ciklus, és az ügyfélszolgálat és a teljesítmény javítása érdekében"
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 4726a82628d56b068d28b555567bda9dc22b5abb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI Azure használni, annak érdekében, a fejlesztési ciklus, valamint az ügyfélszolgálat és a teljesítmény javítása érdekében
![Daxko/CSI Logo](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI szoftver egy ellenőrző tapasztalt: alkalmasságra és újbóli központok az ügyfél alapja gyorsan, növekszik az környezetnek köszönhetően az átfogó vállalati-szoftveres megoldás, sikeres volt, de tartja az IT-infrastruktúra igényekkel növekvő vevő Alap lett tesztelése a vállalat informatikai személyzetet tart fenn. A vállalat egyre növekvő műveletek terhelést jelenthet, különösen a növekvő adatbázisok kezelése által lett korlátozott. Adott műveletek terhelés rosszabb, fejlesztői erőforrások a új kezdeményezések, például a vállalati szoftver új mobilitási szolgáltatásai be lett kivágja.

David Molina, igazgató a fejlesztés, Daxko/CSI, megfelelően Azure által biztosított CSI szoftver, amely egyszerűbbé teheti a adatbázis kezelését, méretezhetőség javítása és szoftver összpontosíthat erőforrásokat szabadítson fel kellett platform,--szolgáltatás (PaaS) modell helyett ops. "Az azure SQL Database volt az USA nagyszerű lehetőséget. Nem rendelkezik egy SQL Server, a feladatátvevő fürt és az egyéb infrastruktúra szükséges összes fenntartása foglalkoznia volt ideális az USA."

Az áttelepítést az Azure-ba, mert CSI szoftver kell egy műveleti személyzet csak két kezelheti több mint 600 felhasználói adatbázisokat. A vállalat mérete alapján felhasználói adatbázisok áthelyezése az Azure SQL Database rugalmas készletek használ, és szükséges.

Molina továbbra is fennáll, "ügyfeleink nemez a változás azonnal. Rugalmas készletek előtt alkalmanként rendelkeztek időtúllépések és más olyan problémák kapacitásnövelés időszakokban. Az Azure rugalmas készletek hogy igény szerint kapacitásnövelés és hibák nélkül a szoftver."

Ügyfelek, az új szolgáltatásokat és lehetőségeket, műveletek és a felügyeleti kezelésével helyett CSI szoftver erőforrások felszabadulnak Azure rugalmas készletek esetén a teljesítmény fokozása mellett. Ezen informatikai erőforrások segített CSI szoftver növelheti a vállalati szoftverek kínál, SpectrumNG Fitnessklub tagok megszólítása, a munkatársak hatékonyság növelése érdekében, és hozzáférést a munkatársak és tagok mobil interaktív feladatok és a valós idejű értesítések.

Azure is hozzájárult CSI szoftver érdekében, és a fejlesztői és minőségi ág (QA) ciklus automatizálási lehetőségek engedélyezésével. A vállalat az Azure-megvalósításban a build kezelők is becsomagolhatja a egy gombra történő kattintás-összetevők. Leírtak Molina, "a kiadási ciklus részeként QA már tudja telepíteni a egy tesztkörnyezetben, amely a termelési verem szorosan utánozza az Azure-ban. Azt is üzembe helyezheti buildek azonnal a fejlesztői környezet betöltő módosításokat. Ez egy nagy win nekünk, mert nem rendelkeztünk tesztelése előtt, a paritásos."

## <a name="offloading-to-the-cloud"></a>A felhő-feladatkiszervezést
Áthelyezését a felhőbe, mielőtt CSI szoftver kellett sikeresen létrehozott egy helyi adatközpontban Houston a saját több-bérlős infrastruktúra. A vállalat kibontva, mivel azt tapasztalt növekvő növő fáradságot megvásárlásáról kiépítés és karbantartása a hardver- és az ügyfelek támogatásához szükséges összes. Informatikai egy másik új erőforrások kiépítése és az ügyfelek új szolgáltatások végrehajtaná lassulást vezető szűk műveletek kezelésére személyzeti vált.

CSI szoftver vizsgálni felhő beállításait, hogy a terhelést, így kiküszöböli, hogy sikerült összpontosítson, a kód helyett a műveleteket. A vállalat észlelhető, hogy a felső szolgáltatók számos csak infrastruktúra,--szolgáltatás (IaaS)-megoldásokat nyújtsanak a nagy informatikai munkatársak kezelheti az infrastruktúra-szolgáltatási verem továbbra is szükséges. A végén CSI megállapította, hogy volt-e az Azure PaaS megoldás a legmegfelelőbb az igényeinek. Molina ismerteti, "Azure kap a bevezetés, a hardver- és -szoftver, a szoftver ajánlatokkal közben csökkenhet az informatikai részleg terhelését összpontosíthatunk."

## <a name="making-the-transition-to-azure"></a>Az átmenet tétele az Azure-bA
A PaaS megoldás kiválasztása az Azure, után CSI szoftver megkezdte a háttér-infrastruktúra és az adatbázisok áttelepítése a felhőbe. Azure, mielőtt SpectrumNG ügyfelek a háttérben futó Windows Communication Foundation (WCF) szolgáltatással közölt ügyfélalkalmazás telepítéséhez szükséges. Megfelelően Molina "bár egyes ügyfelek üzemeltetett mindent saját adatközpontokban azt beépített kell lennie a több-bérlős a terméket. Azt üzemeltetett mindent az adatközpontban Houston, az SQL Server használja adattárként.

"A termék is kínál a tag felé néző webes portálon (írt ASP.net), amely úgy lett kialakítva, hogy fehér címkével kell egyeznie az ügyfél webszolgáltatás és az online lapok és bármely harmadik fél integrációs támogatásához egy SOAP API része."

A felhőre való áttérést nem vette hosszú architektúrájának. Megfelelően Molina, "Többségének részéről az erőfeszítés, módosítja, hogy találtunk konfigurációs fájl adatainak, központosított kapcsolat-karakterlánc módosítását, és a burkolás automatizálása, feltöltése és a megjelent foglalkozik."

A build automation elkészítéséhez CSI szoftverfejlesztő használt Azure PowerShell és a REST API-csomagokat hozzon létre, és feltölti azokat egy éjszakánként kiadás átmeneti környezete.
Az Azure felhőalapú központi telepítésének általános átmenet a CSI szoftver IT-részleg gyorsan és problémamentesen lezajlott merült fel. Molina ismerteti, "az összes, azt kellett a béta-környezetekben a felhőben a projekt a három-négy héten belül. Amely nem egy meglepő win nekünk."

Konfigurálása és tesztelése a környezetben, után CSI szoftver megkezdte áttelepítése ügyfelek. A már használja a CSI szoftvert futtató ügyfelek átmenet majdnem zökkenőmentes volt. A felhasználó egy helyi központi telepítéséből áttelepítése esetén a felhőre való áttérést néhány további időt vett, de továbbra is elsősorban problémás szabad ügyfelek és a CSI szoftver.

Új ügyfelek számára, CSI szoftver tartozó informatikai személyzetet tart fenn a következő folyamat a helyi használni, azokat az Azure-bA:

1. Az Azure PowerShell-parancsfájlok használatával az ügyfelek; az új adatbázis léptetési összes ügyfél számára a prémium szintű rétegen átmenet elég kezdeti átviteli sebesség biztosításához indítsa el.
2. Ha lehetséges, CSI szoftver az Azure SQL áttelepítése varázsló használatával meglévő adatok áthelyezése az Azure SQL Database-példányt.
3. Microsoft SQL Server Integration Services (SSIS) használt végül egyeztetni az adatok közötti eltéréseket, vagy szükség szerint bármely adatok karbantartást végez.

Ma körülbelül 99 százalékához kiindulási CSI szoftver ügyfelek tárolt Azure-ban (északi központi, Dél központi, kelet és Nyugat) négy regionális üzemeltetésében. Azzal, hogy az adatközpontok egyes ügyfelek földrajzi régióban, késés minimális marad.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Szabadítson fel informatikai erőforrásra lenne szükség Azure rugalmas készletek
Az Azure számos szolgáltatást segítettek CSI szoftver shift infrastruktúrájának és műveleteinek arra irányul, hogy a szolgáltatás és a fejlesztői arra irányul, hogy nem. Lehet, hogy a legnagyobb előnye a rugalmas készletek volt.

CSI szoftver készül 550 adatbázisok jelenleg biztosít az ügyfelek. Rugalmas készletek előtt réteg struktúrán belül ennyi adatbázisok kezelése bonyolult volt. Rendelje hozzá az ügyfelek, amely jelentős IT-erőforrás terhet szükséges kapacitásnövelés igények alapján teljesítmény rétegek kellett OPS kezelők. A rugalmas készletek kezelők rendelhet bérlők egy premium vagy a szabványos készlet megfelelő, és majd helyezze át az ügyfelek mérete alapján és kell. Az ügyfelek nemez eredő a rugalmas készletek szinte azonnal; rugalmas készletek előtt ügyfelek kellett időtúllépések és más olyan problémák kapacitásnövelés kihasználtságú időszakok alatt, de rugalmas készletek ügyfél tevékenység felszakadásáig igény szerint is tapasztalhat, és továbbra is használja a SpectrumNG hibák nélkül.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Az Azure aktív georeplikáció felgyorsítják reporting
Több CSI szoftver ügyfelek Azure aktív georeplikáció előnyeit is tart. Az aktív georeplikáció akár négy olvasható másodlagos adatbázis konfigurálható az ugyanazon vagy másik adatközpont-régiókban. CSI szoftverfrissítési kétféle módon aktív georeplikáció használ: először a másodlagos adatbázisok elérhetők esetén egy adatközpontban szolgáltatáskimaradás vagy a nem lehet kapcsolódni a az elsődleges adatbázisban. és a második, a másodlagos adatbázisok olvasható, és csak olvasható munkaterhelésekkel, például jelentéskészítéssel feladatok kiszervezéséhez használható. Egyes CSI szoftver ügyfelek a juttatás igénybe annak érdekében, jelentéskészítési munkafolyamatok.

## <a name="csi-software-application-logic-and-architecture"></a>CSI szoftver úgy az alkalmazáslogikát és architektúra
SpectrumNG webes szerepkörök használja. Mivel az alkalmazás több-bérlős, WCF-szolgáltatások a kezdeti kapcsolati kérelmet a ügyfelek kezelésére használt. Molina szerint, mert "a kérelem azonosítja mindegyik ügyfél, amely lehetővé teszi a us létrehozása egy kapcsolati karakterláncot, a hozzájuk tartozó adatbázisok bármit tennie kell tennie."

A szolgáltatás webes réteghez CSI szoftver kihasználja a Azure automatikus skálázás, alapján napját és időpontját. Rendelkezésre álló erőforrások magasabb használati olyan munkaidőben, automatikusan nagyobb a minden egyes regionális adatközpontok időzónájának megfelelően. Erőforrás is csökkentheti a hétvégekre, amikor az ügyfelek igényeinek megfelelően alacsonyabbak van beállítva.

![Daxko/CSI architektúrája](./media/sql-database-implementation-daxko/figure1.png)

1. ábra. A felhőalapú szolgáltatások feldolgozói szerepkör Azure SQL Database és félig strukturált adatokból a table storage a strukturált adatok megrajzolja. SpectrumNG felhasználók használják, hogy a felhő-adatszolgáltatások összetevőjének Webes szerepkör.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Webalkalmazások és webes-terv réteget használó mobilalkalmazásokhoz
Egy Azure-webalkalmazásokban üzemeltetett egyéni API alapján Azure SQL Database CSI szoftver erőforrások felszabadulnak használatával új kezdeményezéseket, beleértve a teljes mobilplatformot engedélyezéséhez. A platform lehetővé teszi a Fitnessklub tagok és a alkalmazottak mobileszközöket használnak a ütemezések ellenőrzi, osztályok foglalható le, és üzeneteket fogadni.

A platform szolgáltatásorientált architektúra (SOA) használja, egy adott összetevőt érvénybe – például egy POS (POS) vagy egy értékesítési rendszer – parancsprogramok áthelyezése egy másik webes tervet, és majd lépett fel, hogy az összetevő támogatásához úgy, hogy minden más, a szolgáltatás a eredeti webes terv. Ez a lehetőség CSI szoftver rengeteg rugalmasságot biztosít, és segít költséges alacsonyan tartása.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure lehetővé teszi, hogy a szoftver CSI fejlesztők arra utalnak, alkalmazások és szolgáltatások
Azure SQL-adatbázis nem csupán az SpectrumNG ügyfél számára, akiknek a a gyors és megbízható szolgáltatás egy boon, egyúttal egy nagy win CSI szoftver informatikai szakemberek és fejlesztők számára. Ops Azure felhőben történő kiszervezésével CSI szoftver csökken a terhelés, az erőforrások és infrastruktúra, nagy mértékben az elérését gyorsítja fel a fejlesztési ciklus, és már nem kell micromanage adatbázisokhoz a bérlők a teljesítmény optimalizálása érdekében.

## <a name="more-information"></a>További információ
* Az Azure rugalmas készletek kapcsolatos további információkért lásd: [rugalmas készletek](sql-database-elastic-pool.md).
* Adatbázis-eszközök és a rugalmas méretezést kapcsolatos további információkért lásd: [skálázáshoz rugalmas adatbáziseszközöket és a rugalmas méretezést](sql-database-elastic-scale-get-started.md).
* SQL Server-adatbázis migrálása kapcsolatos további információkért lásd: lásd: [egy SQL Server-adatbázis áttelepítése az Azure-bA](sql-database-cloud-migrate.md).
* Aktív georeplikáció kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md).
* Azure Service Bus kapcsolatos további információkért lásd: [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Automatikus méretezése kapcsolatos további információkért lásd: [felhőszolgáltatások skálázás](../cloud-services/cloud-services-how-to-scale-portal.md).


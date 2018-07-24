---
title: Az Azure SQL Database Azure esettanulmány - Daxko/CSI |} A Microsoft Docs
description: Ismerje meg hogyan Daxko/CSI használja-e az SQL Database lerövidítsék a szoftverfejlesztési ciklusokat, és az ügyfélszolgálat és a teljesítmény javítása érdekében
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: ee06039242afe5b5e92cf2fecc4c3fa9bff0b78b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214369"
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI használt Azure lerövidítsék a szoftverfejlesztési ciklusokat, és az ügyfélszolgálat és a teljesítmény javítása érdekében
![Daxko/CSI-embléma](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI-szoftver megmérkőzött kihívást: annak rátába mentességre és újbóli létrehozásával adatközpontok, a gyorsan növekvő köszönhetően az átfogó vállalati – szoftver megoldás sikeres volt, de tartja az adott egyre bővülő ügyfél informatikai infrastruktúrához Alapszintű volt tesztelése a vállalat informatikai személyzetet tart fenn. A vállalat egyre növekvő műveletek terhelést, különösen az kezelése az egyre növekvő adatbázisokhoz által lett korlátozott. A műveletek terhelést rosszabb, fejlesztői erőforrások új kezdeményezések, például a vállalati szoftverek új mobilitási szolgáltatások be lett darabolás.

Megfelelően David Molina, igazgató, termékfejlesztéshez Daxko/CSI, Azure által biztosított CSI-szoftver és a platform--szolgáltatásként (PaaS) modellje, amely az adatbázis kezelésének leegyszerűsítése érdekében és erőforrásokat szabadíthat fel, összpontosíthat a szoftver szükséges helyett ops. "Az azure SQL Database volt számunkra nagyszerű lehetőséget. Nem rendelkezik egy SQL Server feladatátvevő fürt és az egyéb infrastruktúrát kell az összes karbantartásával foglalkoznia nem ideális a számunkra."

Áttelepítés az Azure-ba, mivel CSI-szoftver-kezelői létszámmal kezelheti a több mint 600 ügyféladatbázis csak két van szüksége. A vállalat használja az Azure SQL Database rugalmas készletei mérete alapján az ügyfél-adatbázist, és van szükség.

Molina továbbra is fennáll, "ügyfeleink korábban úgy tűnt a változás azonnal. Rugalmas készletek, mielőtt alkalmanként rendelkeztek időtúllépések és más jellegű problémákat adatlöketek időszakokban. Az Azure rugalmas készletekkel azok igény és használhassák a szoftver problémák nélkül."

Azon ügyfeleink esetében, az Azure rugalmas készletek az új szolgáltatásokat és funkciókat többé vesződnie a sérült a műveletek és kezelése helyett fejlesztésére CSI szoftveres erőforrások felszabadulnak teljesítményének növelése mellett. Informatikai erőforrások segítségével javíthatja a nagyvállalati szoftverek kínál, és SpectrumNG Fitnessklub tagok léphet, a személyzet hatékonyságát, növelése érdekében, és hozzáférést személyzet és a tagok mobil interaktív feladatok és a valós idejű értesítések CSI szoftverrel.

Az Azure is segített CSI szoftver gyorsabban, és növelheti a fejlesztési és minőségbiztosítási (Tesztelés) ciklus automatizálási beállítások engedélyezése. A vállalat Azure végrehajtására, a build-kezelők egyetlen gombnyomással-összetevők is csomagolnia. Molina leírt, "a kiadási ciklus részeként a QA mostantól képes az Azure-ban, amely szorosan utánozza éles környezetünket egy tesztelési környezetben való üzembe helyezéséhez. Buildek azonnal azt is telepíthetők a vet változások fejlesztési környezetünk. Ez a big Data típusú win nekünk, mert nem tesztelése előtt, a paritásos."

## <a name="offloading-to-the-cloud"></a>A felhőbe-kiürítés
A felhőbe való áthelyezés előtt CSI-szoftver sikeresen kellett kiépíteni egy helyi adatközpontban, a Houston saját több-bérlős infrastruktúráját. A vállalat kibontva, ahogy azt növekvő növő fáradságot vásárol, kiépítés és karbantartása a hardver- és az ügyfelek támogatásához szükséges összes szembesülnek. Informatikai volt szükség létszámnövelésre műveletek kezelésére egy másik szűk keresztmetszetté az új erőforrások kiépítése és az ügyfelek számára az új szolgáltatások bevezetéséről lassulást vezettek vált.

CSI-szoftver kiküszöbölésével a terhelés a felhőalapú lehetőségei vizsgálni, úgy, hogy azt szempontból fontos a kódok helyett a műveleteit. A vállalat felderített, hogy a felső felhőszolgáltatók számos csak ajánlat infrastruktúra--szolgáltatásként (IaaS) megoldásokat, továbbra is egy nagy informatikai munkatársak az IaaS-verem kezeléséhez. A végén a CSI-szoftver határozza meg, hogy az Azure PaaS-megoldás a legmegfelelőbb az igényeinek megfelelő volt-e. Molina ismerteti az "Azure lekérdezi a bevezetés, a hardver- és -szoftver, miközben csökkenti az IT-részlegre szoftver ajánlataink is kiszűrhessük."

## <a name="making-the-transition-to-azure"></a>Így a Váltás az Azure-bA
Miután az Azure a PaaS-megoldás, CSI-szoftver már a háttér-infrastruktúrát és az adatbázisokat a felhőbe való migrálás. Azure-ba, mielőtt SpectrumNG ügyfelek egy Windows Communication Foundation (WCF) szolgáltatást a háttérkiszolgálón közölt ügyfélalkalmazást telepítéséhez szükséges. Megfelelően Molina "bár egyes ügyfeleknek saját adatközpontjaik mindent üzemeltetett hoztunk több-bérlős kell a termék. Hogy üzemeltetett mindent az egy kínai adatközpont Houston, az SQL Server használata az adatok tárolását.

"A termék, ajánlat is tag felé néző webes portálon (az ASP.net-ben írt), amely úgy lett kialakítva, lehet, hogy az ügyfél webhelye és a egy SOAP API-t támogatja az online lapokat és bármely harmadik féltől származó integrációs fehér címkével ellátott."

A felhőbe való migrálás nem vette hosszú az architektúrához. Megfelelően Molina, "Többsége részéről az erőfeszítés, módosítja az, hogy tudjuk olvasni a konfigurációs fájl adatokat, egy központi kapcsolati karakterlánc módosítása és a csomagolás automatizálása, feltöltése és központi telepítését a kiadások foglalkozni."

A build automation fejlesztéséhez, CSI szoftverfejlesztő használt Azure PowerShell és REST API-k csomagokat hozzon létre és feltöltheti őket a kiadás minden éjjel átmeneti környezetet.
Az Azure-felhőbeli telepítés teljes Váltás a CSI-szoftver informatikai csapat gyorsan és problémamentesen ért célba. Molina ismerteti, "az összes, azt kellett a béta-környezetet a felhőben véve a projekt három-négy héten belül. Volt egy knál win számunkra."

Konfigurálás és tesztelés a környezet után CSI-szoftver kezdődött áttelepítése ügyfelek. Már használja a CSI-szoftvereket futtató ügyfeleknek például a zökkenőmentes volt az átállás. Az ügyfelek áttelepítését egy helyi központi telepítéséből, a migrálás a felhőbe néhány további időt vett igénybe, de továbbra is elsősorban volt az ügyfelek és a CSI-szoftvert is problémás ingyenes.

Új ügyfeleinknek CSI-szoftvereket az informatikai részleg a következő eljárással venni őket az Azure-bA:

1. Az Azure PowerShell-parancsfájlok segítségével az ügyfél; új adatbázis üzembe helyezése minden ügyfél kezd, a prémium szintű csomagban elegendő az átállás kezdeti átviteli sebesség biztosítása érdekében.
2. Ha lehetséges, CSI szoftvert az SQL Azure áttelepítési varázsló meglévő adatok áthelyezése az Azure SQL Database-példány használja.
3. Végül a Microsoft SQL Server Integration Services (SSIS) használt egyeztethetők össze az adatokat az esetleges eltéréseket vagy után megmaradó fölösleges adatok szükség szerint.

Még ma mintegy 99 % CSI-szoftvert használó ügyfelek üzemel az Azure-ban (USA északi középső, déli középső régiója, Kelet-, és Nyugat-) négy regionális adatközpontok között. Adatközpontok kellene az egyes ügyfelek földrajzi régióban, a késése minimális szinten tartani.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Az Azure rugalmas készletek informatikai erőforrásokat szabadíthat fel
Az Azure számos funkciót segítették CSI-szoftver shift infrastruktúrájának és műveleteinek a fókuszban lévő funkció és a fókuszban lévő fejlesztés alatt áll. A legnagyobb előny talán az a rugalmas készletek lett.

CSI-szoftver készül 550 adatbázisok jelenleg ügyfeleknek kínál. Rugalmas készletek, mielőtt belül egy szint struktúra, amely számos adatbázis kezelése bonyolult volt. Az OPS-kezelők kellett hozzárendelése teljesítményszintek az ügyfelek, amely jelentős IT-erőforrás többletterhelést szükséges adatlöketek igényeinek megfelelően. A rugalmas készletekkel kezelők is rendelje hozzá a bérlők egy prémium vagy standard készlet megfelelő, majd mozgassa mérete alapján ügyfelek és kell. Ügyfelek korábban úgy tűnt a rugalmas készletek hatásait szinte azonnal; rugalmas készletek, mielőtt ügyfelek időtúllépések és más jellegű problémákat adatlöketek kihasználtságú időszakokban rendelkezett, de rugalmas készletek ügyfelek tapasztalhatnak kapacitásnál igény szerint, és továbbra is problémamentesen SpectrumNG használandó.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Az Azure aktív georeplikáció gyorsítja reporting
Ügyfél CSI-szoftverek vannak is kihasználhatja az Azure aktív georeplikációt. Az aktív georeplikáció legfeljebb négy olvasható másodlagos adatbázis konfigurálható az azonos vagy eltérő adatközpont-régiókban. CSI-szoftvert használnak az aktív georeplikáció kétféleképpen: először a másodlagos adatbázisok érhetők el egy adatközpont-meghibásodás után vagy a nem lehet kapcsolódni a az elsődleges adatbázis; és a második, a másodlagos adatbázisok olvashatók legyenek, és csak olvasható munkaterhelésekkel, például jelentéskészítéssel feladatok kiszervezése használható. Néhány CSI-szoftvert használó ügyfelek ezen juttatás használatával gyorsítsa fel a jelentési munkafolyamatok.

## <a name="csi-software-application-logic-and-architecture"></a>CSI-szoftver alkalmazáslogika és architektúra
SpectrumNG használ a webes szerepkörök. Mivel az alkalmazás több-bérlős, a WCF-szolgáltatások kezelni a kezdeti kapcsolat kérést az ügyfelektől származó szolgál. Molina-állapotokat, "a kérelem azonosítja minden ügyfél, amely lehetővé teszi a velünk a kapcsolatot hozhat létre egy kapcsolati karakterláncot meg azok adatbázisokhoz, amit tennünk kell tennie."

A szolgáltatás a webes szint CSI-szoftver kihasználja az Azure automatikus méretezés, nap és időpont alapján. Rendelkezésre álló erőforrások magasabb használati befogadásához munkaidőben, automatikusan növelni a mindkét regionális adatközpont időzónájának megfelelően. Erőforrás is vertikális leskálázás hétvégén, amikor ügyféligények alacsonyabbak van beállítva.

![Daxko/CSI-architektúra](./media/sql-database-implementation-daxko/figure1.png)

1. ábra. A cloud services feldolgozói szerepkör rajzol tárolt részben strukturált adatokat az Azure SQL Database és a table storage szolgáltatásban tárolt részben strukturált adatokat. SpectrumNG a felhasználók használják, hogy a felhőn keresztül data services, webes szerepkör.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>A web apps és a egy webes csomagszint használatával mobilalkalmazásokhoz
Az Azure web apps szolgáltatásban üzemeltetett egyéni API-k alapján Azure SQL Database szabadulnak fel erőforrásokat CSI-szoftverek használatával új kezdeményezések, beleértve a teljes mobilplatformra engedélyezéséhez. A platform lehetővé teszi, hogy Fitnessklub tagjai és a személyzet mobileszközök használandó ütemezéseket, osztályok repülőjáratra, és üzeneteket fogadni.

A platform szolgáltatásorientált architektúra (SOA) segítségével egyetlen összetevővel is – például egy pénztári (POS) vagy egy értékesítési rendszer – működés közbeni áthelyezése egy másik webes tarifacsomag, és támogatja az adott összetevő eszközeiről a minden más, a szolgáltatás üzembe helyezése majd a eredeti webes tarifacsomag. Ez a képesség CSI-szoftver áttekintse rugalmasságot biztosít, és tarthatja a költségeket, segít.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Az Azure lehetővé teszi a CSI-szoftver fejlesztőknek az alkalmazások és szolgáltatások
Az Azure SQL-adatbázis nem csupán egy SpectrumNG ügyfelek számára, akik a gyors és megbízható szolgáltatás igénybevételéhez boon, egyben a big Data típusú win CSI-szoftverek informatikai szakemberek és fejlesztők számára. Ops a felhőben Azure-ba történő kiszervezésével, CSI-szoftvereket csökken a terhelés, az erőforrások és az infrastruktúra, jelentősen gyorsítani a fejlesztési ciklusokat, és már nem kell micromanage adatbázisok a bérlők számára a teljesítmény optimalizálása.

## <a name="more-information"></a>További információ
* Az Azure rugalmas készletek kapcsolatos további információkért lásd: [rugalmas készletek](sql-database-elastic-pool.md).
* Adatbázis-eszközök és a rugalmas méretezhetőség kapcsolatos további tudnivalókért lásd: [elastic database-eszközökkel és a rugalmas méretezhetőség](sql-database-elastic-scale-get-started.md).
* SQL Server-adatbázisok áttelepítésével kapcsolatos további információkért lásd: [SQL Server-adatbázis Migrálása az Azure-bA](sql-database-cloud-migrate.md).
* Aktív georeplikáció kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md).
* Azure Service Bus kapcsolatos további információkért lásd: [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Automatikus skálázás kapcsolatos további információkért lásd: [felhőszolgáltatások méretezéséről](../cloud-services/cloud-services-how-to-scale-portal.md).


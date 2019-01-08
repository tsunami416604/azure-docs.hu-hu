---
title: Contoso – méretezési csoport a migrálás az Azure-bA |} A Microsoft Docs
description: Ismerje meg, hogyan kezeli a Contoso a méretezett áttelepítés az Azure-bA.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: ca7d565c020e8ac4510c7a65745a9e052b69551d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063180"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso – méretezési csoport egy áttelepítés az Azure-bA

Ez a cikk a Contoso nagy mennyiségű Azure-bA migrálást hajt végre. Gondolja át, hogyan tervezheti meg és több mint 3000 számítási feladatok, a 8000-es adatbázisok és a több mint 10 000 virtuális gépek áttelepítésének végrehajtásához.


Ez a cikk egy sorozat, amely dokumentálja a fiktív Contoso hogyan a helyszíni erőforrásokkal áttelepíti a Microsoft Azure felhőbe egyike. A sorozat tartalmazza a háttérben, és tervezési információkat és a központi telepítési forgatókönyvek, amelyek bemutatják, hogyan állíthat be egy áttelepítési infrastruktúra felmérheti a a helyszíni erőforrások migrálásra való alkalmasságát, és futtassa a különböző típusú áttelepítéseket. Forgatókönyvek egyre összetettebbé válnak. Az adatsorozat cikkek idővel kell hozzáadni.


**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: – Áttekintés](contoso-migration-overview.md) | A cikk sorozat, a Contoso-áttelepítési stratégia és az adatsorozathoz használt mintaalkalmazások áttekintése. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Érhető el.
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md)  | Contoso fut, annak a helyszíni SmartHotel360 app VMware-en futó értékelését. Contoso értékeli az alkalmazás virtuális gépek az Azure Migrate szolgáltatás és a Data Migration Assistant szolgáltatást használó alkalmazás SQL Server-adatbázis használatával. | Elérhető
[4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel360 alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése egy Azure SQL Database felügyelt példánya a a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető   
[5. cikk: Az Azure virtuális gépein egy alkalmazás újratárolása](contoso-migration-rehost-vm.md) | Contoso a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással az Azure virtuális gépekhez. | Elérhető
[6. cikk: Egy Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazás újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti az alkalmazás számára, hogy az alkalmazás virtuális gépek és a Database Migration Service az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt a Site Recovery használatával. | Elérhető
[7. cikk: Egy Azure-beli virtuális gépeken Linux alkalmazás újratárolása](contoso-migration-rehost-linux-vm.md) | Contoso az Azure virtuális gépek, a Site Recovery szolgáltatás használatával Linux osTicket alkalmazás lift-and-shift áttelepítés befejeződött. | Elérhető
[8. cikk: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure Database for MySQL-hez](contoso-migration-rehost-linux-vm-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure virtuális gépek Site Recovery használatával. Az áttelepítése az alkalmazás-adatbázis az Azure Database for MySQL-hez a MySQL Workbench használatával. | Elérhető
[9. cikk: Az Azure web App alkalmazásban és az Azure SQL Database alkalmazás újrabontása](contoso-migration-refactor-web-app-sql.md) | Contoso a SmartHotel360 alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése egy Azure SQL Server-példány az a Database Migration Assistant szolgáltatást. | Elérhető    
[10. cikk: Refaktorovat egy Linux-alkalmazást egy Azure-webalkalmazást és az Azure Database for MySQL-hez](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure-webalkalmazás több helyen is előfordul. A webalkalmazás folyamatos készregyártás a GitHub van integrálva. Az alkalmazás-adatbázis, áttelepíti egy Azure Database for MySQL-példányt. | Elérhető
[11. cikk: Refaktorovat a Team Foundation Serverrel az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi Azure DevOps-szolgáltatásokkal az Azure-ban. | Elérhető
[12. cikk: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-bA. Ezután azt rearchitects az alkalmazás webes réteg az Azure Service Fabric és az alkalmazás-adatbázis az Azure SQL Database-ban futó Windows tárolójaként. | Elérhető    
[13. cikk: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service (AKS), az Azure Functions, Azure Cognitive Services és az Azure Cosmos DB használatával. | Elérhető 
14. cikk: Áttelepítés az Azure-bA méretezése | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. | Ez a cikk

## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik üzleti partnerek megértéséhez, amit szeretnének való áttérés eléréséhez:

- **Üzleti növekedés cím**: Contoso növekvő nyomás okoz a helyszíni rendszerek és infrastruktúra.
- **Növelheti a hatékonyságot**: Contoso cégnek szüksége van, távolítsa el a felesleges eljárásokat, és egyszerűsíthetők a folyamatok a fejlesztők és a felhasználók számára. Az üzleti igények informatikai gyorsan, és nem Hulladékmennyiség idő vagy költséget takaríthat meg, így gyorsabban továbbítása az ügyfelek igényei.
- **A gyorsaság növeléséhez**: Contoso IT kell lennie a rugalmasabb ügyfélkapcsolatok kialakítását teszi az üzleti igényeinek. Reagáljon gyorsabban a Marketplace-en, a versenyképes sikeres engedélyezése a módosításokat, képesnek kell lennie. Azt nem a módon, vagy egy üzleti blocker válnak.
- **Méretezési csoport**: A vállalat növekedésével sikeres, a Contoso informatikai csapat rendszerek, amelyek képesek a ugyanolyan ütemben nő kell megadnia.
- **Javíthatja a költségmodellek**: A Contoso biztosítani szeretné, így csökkentheti az informatikai költségvetés befektetési követelményeinek.  A Contoso biztosítani szeretné a felhőalapú képességek használatára és költséges hardver csökkentése érdekében.
- **Lower licencelési költségeit**: A Contoso biztosítani szeretné a felhőbeli költségek csökkentése érdekében.


## <a name="migration-goals"></a>Migrálási célok

A Contoso felhőalapú csapat az áttelepítés célok le van rögzítve. Ezen célok a leginkább megfelelő áttelepítési módszer meghatározásához használt.

**Követelmények** | **Részletek**
--- | --- 
**Gyorsan helyezze át az Azure-bA** | A Contoso biztosítani szeretné az alkalmazásokat és a virtuális gépek az Azure-bA áthelyezni, a lehető leggyorsabban.
**Fordítsa le egy teljes leltár** | A Contoso biztosítani szeretné, hogy a szervezet összes alkalmazások, adatbázisok és virtuális gépek teljes leltár.
**Mérje fel, és az alkalmazások besorolása** | A Contoso biztosítani szeretné, hogy teljes mértékben kihasználhatja a felhőben. Alapértelmezés szerint a Contoso azt feltételezi, hogy a PaaS-ként futtatja az összes szolgáltatás. IaaS fogja használni, ahol a PaaS nem megfelelő. 
**Betanítása és DevOps-re** | A Contoso biztosítani szeretné áthelyezni a fejlesztési és üzemeltetési modell. Contoso adja meg az Azure-ban és a DevOps-képzés, és szükség szerint csapatok átrendezéséhez. 


Után rögzíthet célokat és követelményeket állapította meg, a Contoso áttekinti az informatikai rendszer működését, és azonosítja az áttelepítési folyamat.

## <a name="current-deployment"></a>Aktuális üzemelő példány

Megtervezése és beállítása után egy [Azure-infrastruktúra](contoso-migration-infrastructure.md) , és próbálja ki a fenti táblázatban leírtaknak megfelelően a különböző proof-of-concept (POC) áttelepítési kombinációit, Contoso készen áll a folyamatokról a teljes migrálás az Azure-bA ipari méretekben. Íme, mi a Contoso biztosítani szeretné áttelepíteni.

**Elem** | **kötet** | **Részletek**
--- | --- | ---
**Számítási feladatok** | Több mint 3000 alkalmazások | Alkalmazások futtatása virtuális gépeken.<br/><br/>  Alkalmazások Windows, az SQL-alapú és nyílt Forráskódú LAMP olyan.
**Adatbázisok** | Körülbelül 8,500 | Adatbázisok SQL Server, MySQL, PostgreSQL tartalmazza.
**Virtuális gépek** | Több mint 10 000 | Virtuális gépek futtatása a VMware-gazdagépeken, és felügyeli a vCenter-kiszolgálók.


## <a name="migration-process"></a>Áttelepítési folyamat

Most, hogy a Contoso le a stratégiai és áttelepítési célok van rögzítve, az áttelepítési folyamat a négy pillérre támaszkodó megközelítésre határozza meg:

- **1. fázis – felmérheti**: Az aktuális eszközök felderítése, és döntse el, attól még az Azure-ba való migrálásra alkalmas.
- **2. fázis – áttelepítése**: Az eszközök áthelyezése az Azure-bA. Azok az alkalmazások áthelyezése és objektumokat az Azure-ban az alkalmazás attól függ, hogyan lehet, és amit szeretnének eléréséhez.
- **3. fázis – optimalizálása**: Erőforrások áthelyezése az Azure-ba, miután Contoso cégnek szüksége van, és a maximális teljesítmény és a hatékonyság leegyszerűsítheti őket.
- **Biztonságos 4. fázis & Manage**: Minden helyen, a Contoso most már használja az Azure biztonsági és felügyeleti erőforrások és szolgáltatások szabályozzák, az Azure-ban a felhőbeli alkalmazások figyeléséhez és védelméhez.


Ezeket a fázisokat nem soros a szervezeten belül. A Contoso migrálási projekt egyes adathordozónevek egy másik időpontban az elemzésekhez és migráláshoz folyamat lesz. Optimalizálás, biztonsági és felügyeleti lesz folyamatos idővel.


## <a name="phase-1-assess"></a>1. fázis: Értékelés

Contoso elindít a folyamat által felderítésével és értékelésével, helyszíni alkalmazásait, adatait és infrastruktúráját. Íme, mi Contoso tegye:

- Contoso alkalmazások felderítésére van szükség, vannak leképezve a függőségek alkalmazások között, és döntse el áttelepítési sorrendje és prioritás.
- Mivel felméri a Contoso, az egy átfogó leltár az alkalmazások és erőforrások ki fog létrehozni. Az új készlet, valamint a Contoso használja, és frissítse a meglévő Configuration Management adatbázis (CMDB) és a Szolgáltatáskatalógusban.
    - A CMDB Contoso alkalmazások technikai konfigurációi tárolja.
    - A szolgáltatáskatalógus dokumentumok a műveleti adatokat az alkalmazások, beleértve a kapcsolódó üzleti partnerek és a szolgáltatásiszint-szerződések (SLA)

### <a name="discover-apps"></a>Alkalmazások felderítése

Contoso alkalmazások ezrei futtatja számtalan kiszolgálón keresztül. A CMDB-és szolgáltatáskatalógus kívül a Contoso cégnek szüksége van a felderítési és felmérési eszközök. 

- Az eszközök biztosítania kell egy mechanizmust, amely képes megfelelőségvizsgálati adatai a migrálási folyamatba.
- Értékelési eszközök kell megadnia, amely segít a Contoso fizikai és virtuális erőforrásokat intelligens leltára állíthatja össze adatokat. Adatok tartalmaznia kell a profil adatait, és a teljesítmény-mérőszámon.
- Felderítés befejeződése után a Contoso az eszközök és a hozzájuk társított metaadatok teljes leltár kell rendelkeznie. Ez a készlet a migrálási terv meghatározásához használható.

### <a name="identify-classifications"></a>Besorolások azonosítása

Contoso azonosítja az eszközök a leltárban szereplő néhány gyakori kategóriák. Ezek a besorolások létfontosságúak a Contoso döntéshozatalt az áttelepítéshez. A problémabesorolások listája segít a migrálás prioritásait, és összetett problémák azonosításához.

**Kategória** | **Hozzárendelt értéket** | **Részletek**
--- | --- | ---
Üzleti csoport | Üzleti csoportnevek listája | Melyik csoport felelős a leltári tétel?
POC-jelölt | I/N | Az alkalmazás használható POC vagy korai felhasználója a migrálás a felhőbe?
Műszaki adósságot | Egyik sem vagy néhány/súlyos | A leltári tétel futó vagy egy termék out támogatása, platform vagy operációs rendszer használatával?
Tűzfal következmények | I/N | Az alkalmazás az internettel/forgalom kívüli kommunikációhoz?  Integrálható a tűzfalat?
Biztonsági problémák | I/N | Nincs biztonsági előforduló ismert problémákat olvashatja az alkalmazással?  Az alkalmazás a titkosítatlan adatok vagy elavult platformok használni?


### <a name="discover-app-dependencies"></a>Alkalmazásfüggőségek felderítése

Az értékelési folyamat részeként a Contoso cégnek szüksége van, azonosíthatja, ahol az alkalmazások futnak, és döntse el, a függőségeket és a kiszolgálók közötti kapcsolatok. Contoso leképezi a környezetet a lépéseket.

1. Első lépésként a Contoso deríti fel, hogyan kiszolgálók és gépek leképezése az egyes alkalmazások, a hálózati helyek és a csoportok.
2. Ezekkel az információkkal a Contoso egyértelműen azonosítani alkalmazásokat, amelyek néhány függőségekkel rendelkeznek, és így alkalmasak a gyors áttelepítés.
3. Contoso a leképezés használata jelzi az összetett függőségek és kiszolgálók közötti kommunikációt. Contoso Ezután ezek a kiszolgálók logikailag jelölik az alkalmazások csoport, és ezen csoportok alapján áttelepítési stratégiájának tervezése.


Leképezés befejeződött, a Contoso is győződjön meg arról, hogy az összes alkalmazás-összetevők és azonosított elszámolni, az áttelepítési terv készítése során. 

![Függőségi leképezés](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>Alkalmazás értékelése

Utolsó lépésként a felderítés és értékelés folyamatban a Contoso kiértékelheti értékelése és a hozzárendelés eredmények döntse el, hogyan telepítheti át az egyes alkalmazások a szolgáltatáskatalógusba. 

A kiértékelési folyamat rögzítéséhez a leltárhoz adnak hozzá további besorolások néhány.

**Kategória** | **Hozzárendelt értéket** | **Részletek**
--- | --- | ---
Üzleti csoport | Üzleti csoportnevek listája | Melyik csoport felelős a leltári tétel?
POC-jelölt | I/N | Az alkalmazás használható POC vagy korai felhasználója a migrálás a felhőbe?
Műszaki adósságot | Egyik sem vagy néhány/súlyos | A leltári tétel futó vagy egy termék out támogatása, platform vagy operációs rendszer használatával?
Tűzfal következmények | I/N | Az alkalmazás az internettel/forgalom kívüli kommunikációhoz?  Integrálható a tűzfalat?
Biztonsági problémák | I/N | Nincs biztonsági előforduló ismert problémákat olvashatja az alkalmazással?  Az alkalmazás a titkosítatlan adatok vagy elavult platformok használni?
Migrálási stratégia | Újraépítés áthelyezési/újrabontása/újratervezése | Milyen típusú áttelepítési van szükség az alkalmazás? Hogyan fogja az alkalmazás telepítve az Azure-ban? [További információk](contoso-migration-overview.md#migration-strategies).
Műszaki bonyolultsága | 1 – 5 | Milyen bonyolult az a migrálás? Ezt az értéket meg kell határozni a Contoso DevOps és a megfelelő partnerek által.
Üzleti kritikusság | 1 – 5 | Mennyire fontos az alkalmazást a vállalati? Például egy kis munkacsoport-alkalmazást hozzárendelni egy pontszám egy, míg egy kritikus fontosságú alkalmazást használ, amelyek a szervezet egy pontszám öt. Ezt az értéket a migrálás prioritási szintet negatív hatással lesz.
Áttelepítési prioritás | 1/2/3 | Milyen áttelepítési prioritását az alkalmazást?
Migrálás kockázata  | 1 – 5 | Mi az az alkalmazás migrálása a kockázati szintjét? Ezt az értéket kell egyeztetett Contoso DevOps és a megfelelő partnerek.




### <a name="figure-out-costs"></a>Döntse el, költségek

Döntse el, a költségek és a lehetséges megtakarítás az Azure-migrálás, Contoso használható a [teljes költség a tulajdonjog (TCO) Számológép](https://azure.microsoft.com/pricing/tco/calculator/) alapján számítja ki, és hasonlítsa össze a teljes bekerülési Költséget, az Azure-hoz egy összehasonlítható a helyszíni telepítéshez.

### <a name="identify-assessment-tools"></a>Értékelési eszközök azonosítása

Contoso úgy dönt, hogy melyik eszköz felderítési, felmérési és a készlet létrehozásához használandó. Contoso azonosítja az Azure-eszközök és a szolgáltatások, a natív alkalmazás eszközök és a parancsfájlok és a partnerek eszközei vegyesen. Különösen Contoso van érdekelné hogyan Azure Migrate segítségével felmérheti, ipari méretekben.


#### <a name="azure-migrate"></a>Azure Migrate


Az Azure Migrate szolgáltatás felderítéséhez és értékeléséhez, a helyszíni VMware virtuális gépeket, az Azure-ba való migrálásra való felkészüléskor segít. Itt látható az Azure Migrate leírása:

1. Fedezze fel: Fedezze fel a helyszíni VMware virtuális gépeket.
    - Az Azure Migrate felderítési származó több vCenter-kiszolgálók (tárolókonfigurációhoz) támogatja, és futtathatja a felderítések külön Azure Migrate projektben.
    - Az Azure Migrate felderítési azt jelenti, hogy a VMware virtuális gép fut a Migrate Collector végez. Az azonos gyűjtő virtuális gépek felderítése a különböző vCenter-kiszolgálók, és adatokat küldeni a különböző projektek.
1. Készen állásának felmérése: Annak ellenőrzéséhez, hogy a helyszíni számítógépek alkalmasak az Azure-ban futó. Értékelés tartalmazza:
    - Javaslatok a méretekkel kapcsolatban: Méretezési javaslatokat kaphat az Azure virtuális gépekhez, a helyszíni virtuális gépek korábbi teljesítménye alapján.
    - Becsült havi költségek: Becsült költségek a helyszíni gépek Azure-ban futó beolvasása.
2. Függőségek azonosításához:  Elemzésekhez és migráláshoz optimális gépek csoportok létrehozása a helyszíni gépek függőségeit, jelenítheti meg.


![Azure Migrate](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>Migrálás nagy méretekben

Contoso kell az Azure Migrate használata helyesen adja meg a méretezési csoport a migrálás. 

- Contoso elvégzi az alkalmazás által az Azure Migrate értékelését. Ez biztosítja, hogy az Azure Migrate vissza naprakész adatai az Azure Portalon.
- Olvassa el a rendszergazdák contoso [üzembe helyezése az Azure Migrate ipari méretekben](how-to-scale-assessment.md)
- Contoso feljegyzi az Azure Migrate korlátok, a következő táblázat foglalja össze.


**Művelet** | **Korlát**
--- | ---
Az Azure Migrate-projekt létrehozása | 1500 virtuális gépet
Detektálás | 1500 virtuális gépet
Értékelés | 1500 virtuális gépet

Contoso fogja használni az Azure Migrate a következő:

- A vCenter Contoso mappákba rendezheti a virtuális gépek lesz. Ez lesz megkönnyítik számukra értékelés futtatása egy adott mappában lévő virtuális gépek ellen, mint a fókusz.
- Az Azure Migrate a Service Map az Azure segítségével felmérheti a gépek közötti függőségek. Ehhez az értékelendő virtuális gépeken telepítendő ügynökök. 
    - Contoso automatizált parancsprogramok használatával telepítse a szükséges Windows vagy Linux-ügynökök.
    - Alkalmazott, Contoso is leküldéssel telepíteni a virtuális gépek egy vCenter mappában található.


#### <a name="database-tools"></a>Adatbáziseszközök

Mellett az Azure Migrate Contoso középpontjában eszközökkel kifejezetten az adatbázis-értékeléshez. Eszközök, mint például a [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) segítségével felmérheti a migrálás az SQL Server-adatbázisok.

A Data Migration Assistant (DMA) kompatibilis egy az Azure database-megoldások, például az Azure SQL Database, SQL Server rendszert futtató Azure IaaS virtuális gépek, illetve az Azure SQL felügyelt példánya a-e a helyszíni adatbázisok azonosítani a Contoso segíthetnek. 

A DMS Contoso most néhány parancsprogramot, felderítésére használnak, és az SQL Server-adatbázisok. Ezek a GitHub-adattárban találhatók.

#### <a name="partner-tools"></a>Partnerek eszközei

Nincsenek más partneri számos olyan eszközt, amelyek Contoso segíthetnek felméréséhez, a helyszíni környezetben, az Azure-ba való migrálásra. [További](https://azure.microsoft.com/migration/partners/) Azure Migration-partnerek kapcsolatban.  

## <a name="phase-2-migrate"></a>2. fázis: Migrate (Áttelepítés)

Az értékelést a teljes Contoso cégnek szüksége van, az alkalmazásokat, adatokat és infrastruktúra áthelyezése az Azure-ban eszközök azonosításához. 




### <a name="migration-strategies"></a>Migrálási stratégiák

Nincsenek négy széles körű migrációs stratégiákat / Contoso figyelembe. 

**Stratégia** | **Részletek** | **Használat**
--- | --- | ---
**Áthelyezési**  | Gyakran nevezik "lift and shift" típusú migrálás, ez a beállítás kódot nem a áttelepítése a meglévő alkalmazások Azure-ban gyorsan.<br/><br/> Egy alkalmazás migrálása, mert a rendszer-, a kockázatokat és a kódmódosításokat társított költségek nélkül a felhő nyújtotta előnyök használatával. | Contoso áthelyezési is kevesebb stratégiai alkalmazások kódmódosítás nélkül megkövetelése.
**Újrabontása** |  Más néven "újracsomagolás", ez a stratégia szükséges minimális alkalmazáskód vagy konfigurációs módosításokat kell az alkalmazás csatlakoztatása az Azure PaaS, és a felhőalapú képességek jobb kihasználásához. | Contoso újrabontása is megőrizheti az ugyanazon alapvető funkciókat, de helyezze át őket az Azure platformon, például az Azure App Services futtatásához stratégiai alkalmazásokat.<br/><br/> Ehhez szükséges minimális kód módosítása.<br/><br/> Másrészről Contoso kell egy virtuális Gépet platform karbantartása, mivel nem Microsoft által felügyelt ez.
**Újratervezése** | Ez a stratégia módosítja vagy kibővíti az alkalmazás kódbázis optimalizálhatja a felhő képességeit és a méretezési csoport alkalmazásarchitektúra.<br/><br/> Egy alkalmazást, egy rugalmas, hatékonyan méretezhető, függetlenül üzembe helyezhető architektúra modernizálja azt.<br/><br/> Azure-szolgáltatások gyorsítsa fel a folyamatot, magabiztosan méretezhet az alkalmazásokat, és alkalmazások egyszerű kezelése.
**Újraépítése** | Ez a stratégia natív felhőalapú technológiákat használó előzmények nélküli alkalmazást újraépíti.<br/><br/> Az Azure platformszolgáltatás (PaaS) kínál teljes körű fejlesztési és üzembehelyezési környezetet a felhőben. Néhány járó költségeket és bonyodalmakat szoftverlicencek kiküszöböli, és kiküszöböli az egy alkalmazás alapul szolgáló infrastruktúra, a közbenső szoftver és az egyéb erőforrások. | Contoso is írja át egészen az alapoktól kritikus fontosságú alkalmazások, például kiszolgáló nélküli számítógép vagy a mikroszolgáltatások a felhőalapú technológiák előnyeit.<br/><br/> Contoso fogja kezelni, az alkalmazás és a szolgáltatás által, és az Azure minden más kezeli.


Adatok is figyelembe veendő, különösen a Contoso rendelkező adatbázisok mennyisége. A Contoso alapértelmezés szerinti megoldás, felhőalapú szolgáltatások teljes körű kihasználása a PaaS-szolgáltatások, például az Azure SQL Database használatával. Helyezi át az adatbázisok egy PaaS szolgáltatás, Contoso csak el adatokat, az alapul szolgáló platform és a Microsoftnak.

### <a name="evaluate-migration-tools"></a>Áttelepítési eszközök kiértékelése

Contoso elsősorban az áttelepítés több Azure-szolgáltatásokat és eszközöket használ:

- [Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview): Vész-helyreállítási koordinálja, és a helyszíni virtuális gépek áttelepítése az Azure-bA.
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview): Az Azure-bA telepíti át a helyszíni adatbázisok, például az SQL Server, a MySQL és az Oracle.


#### <a name="azure-site-recovery"></a>Azure Site Recovery

Az Azure Site Recovery az elsődleges Azure-szolgáltatás a vezénylésre vész-helyreállítási és migrálás az Azure-ban, és a helyszíni helyről az Azure-bA.

1. A Site Recovery lehetővé teszi, koordinálja a replikációt a helyszíni helyről az Azure-bA.
2. Amikor replikáció be van állítva és fut, a helyszíni gépek Azure-ban, a migrálás befejezése feladatátvétele.

Már a Contoso [egy megvalósíthatósági vizsgálat befejeződött](contoso-migration-rehost-vm.md) megtekintéséhez, hogyan a Site Recovery segítségével való migrálása a felhőbe azokat.

##### <a name="using-site-recovery-at-scale"></a>Site Recovery használatával ipari méretekben

Contoso csomagok több lift-and-shift-áttelepítések futtatásáról. Annak érdekében, hogy ez működik, a Site Recovery replikálni váró körülbelül 100 virtuális gép egyszerre. Döntse el, hogyan működik ez, Contoso kell Kapacitástervezés a javasolt a Site Recovery az áttelepítéshez.

- Contoso cégnek szüksége van, a forgalommal kapcsolatos információk összegyűjtéséhez. Ebben az esetben:
    - Contoso cégnek szüksége van a virtuális gépek szeretné replikálni a változási gyakoriság meghatározása.
    - Contoso is szüksége van hálózati kapcsolat a helyszíni helyről az Azure-bA figyelembe kell venni.
- Kapacitás és a kötetre vonatkozó követelményeket reagálva a Contoso kell megfelelni a helyreállításipont-célkitűzés (RPO) a szükséges virtuális gépek napi adatváltozási alapján elegendő sávszélességet kell kiosztania.
- Végül van szükségük, döntse el, hogy hány kiszolgálóra, amely szükséges ahhoz, hogy az üzembe helyezés a Site Recovery-összetevők futtatásához szükséges.

###### <a name="gather-on-premises-information"></a>A helyszíni adatainak összegyűjtése
Contoso használhatja a [Site Recovery Deployment Planner](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner) eszköz a lépések elvégzéséhez:

- Contoso az eszköz használatával távolról anélkül, hogy az hatással van az éles környezetben a virtuális gépek profilját. Ennek segítségével azonosíthatja a replikáció és feladatátvételi sávszélesség és a tárolási követelményeket.
- Contoso bármely Site Recovery helyszíni összetevőit telepítése nélkül futtathatja az eszközt.
- Az eszköz kompatibilis és nem kompatibilis virtuális gépek, lemezek, virtuális gépenként adatokat gyűjt, és az adatváltozás lemezenként. Sávszélességre van szükség, és az Azure-infrastruktúra szükséges a sikeres replikálás és feladatátvételi is azonosítja.
- Contoso meg kell győződnie arról, hogy futtassa a planner eszköz, amely megfelel a minimális követelményei a Site Recovery konfigurációs kiszolgálónak Windows Server-gépek. A konfigurációs kiszolgáló egy, a szükséges ahhoz, hogy a helyszíni VMware virtuális gépek replikálása esetén a Site Recovery a gépet.


###### <a name="identify-site-recovery-requirements"></a>A Site Recovery-követelményeinek azonosítása

A replikált virtuális gépek mellett a Site Recovery számos összetevőnek igényel VMware-migrálás.

**Összetevő** | **Részletek**
--- | ---
**Konfigurációs kiszolgáló** | Általában egy VMware virtuális gép beállítása egy OVF-sablon használatával.<br/><br/> A konfigurációs kiszolgáló összetevő koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
**Folyamatkiszolgáló** | Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón.<br/><br/> A folyamatkiszolgáló összetevőt fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítással optimalizálja őket és elküldi azt az Azure-tárolóba.<br/><br/> A folyamatkiszolgáló Ezenfelül telepíti az Azure Site Recovery mobilitási szolgáltatást a virtuális gépeket szeretne replikálni, és elvégzi a helyszíni gépek automatikus felderítését.<br/><br/> Méretezett központi telepítések szükség további, önálló folyamatkiszolgálók replikációs forgalom nagy mennyiségben.
**A mobilitási szolgáltatás** | A Mobilitásiszolgáltatás-ügynök telepítve van az egyes VMware virtuális Gépeken, amelyek áttelepíthetők a Site Recoveryvel.  

Döntse el, hogyan helyezhet üzembe ezeket az összetevőket, a kapacitás szempontok alapján a Contoso cégnek szüksége van.

**Összetevő** | **A kapacitásra vonatkozó követelmények**
--- | ---
**Maximális napi adatváltozási sebesség** | A folyamat egyetlen kiszolgáló képes kezelni a napi módosítási gyakorisága 2 TB-ig. Mivel egy virtuális Gépet csak használhat egy folyamatkiszolgáló, a maximális napi adatváltozási sebesség replikált virtuális gép esetén támogatott, 2 TB-os.
**Maximális átviteli sebesség** | Standard szintű Azure storage-fiók képes kezelni másodpercenként 20 000 kérelem legfeljebb, és ezt a korlátot bemeneti/kimeneti műveletek száma másodpercenként (IOPS) egy replikáló virtuális gép között kell lennie. Például ha egy virtuális gép 5 lemezzel rendelkezik, és egyes lemezek 120 IOPS (8 KB a mérete) a virtuális gépen hoz létre, majd lesz a lemez IOPS-korlát az 500-as Azure-ban.<br/><br/> Vegye figyelembe, hogy a szükséges tárfiókok száma megegyezik-e osztva a 20 000 teljes forrásgépen IOPS. A replikált gép csak egyetlen tárfiókra az Azure-ban is tartozhat.
**Konfigurációs kiszolgáló** | A Contoso 100 = 200 replikálása becslése alapján együtt, virtuális gépek és a [konfigurációs kiszolgáló méretezési követelmények](../site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-along-with-in-built-process-server), Contoso-becslésére igényeinek megfelelően a konfigurációs kiszolgáló gép a következőképpen történik:<br/><br/> CPU: 16 vcpu-k (2 sockets * 8 mag, 2,5 GHz-es @)<br/><br/> Memória: 32 GB<br/><br/> Gyorsítótárlemez: 1 TB<br/><br/> A módosult adatok aránya: 1 TB-os 2 TB-ig.<br/><br/> A méretezési követelmények mellett Contoso kell ügyeljen arra, hogy a konfigurációs kiszolgáló optimális található, az ugyanazon a hálózaton és a LAN-szegmens, mert a virtuális gépek, amely át lesz telepítve.
**Folyamatkiszolgáló** | Contoso 100 – 200 – virtuális gépek replikálhatók a különálló dedikált folyamatkiszolgáló üzembe helyezése:<br/><br/> CPU: 16 vcpu-k (2 sockets * 8 mag, 2,5 GHz-es @)<br/><br/> Memória: 32 GB<br/><br/> Gyorsítótárlemez: 1 TB<br/><br/> A módosult adatok aránya: 1 TB-os 2 TB-ig.<br/><br/> A folyamatkiszolgáló munkát nehéz lesz, és mint ilyen kell elhelyezni, az ESXi-gazdagépen, amely képes kezelni, a lemez i/o, a hálózati forgalom és a CPU, a replikációhoz szükséges. Contoso erre a célra egy dedikált gazdagéppel figyelembe veszi. 
**Hálózat** | Contoso tekintse át az aktuális site-to-site VPN-infrastruktúrát, és úgy döntött, hogy az Azure ExpressRoute megvalósításához. A megvalósítás fontos, mivel csökkentheti a késést, és növelheti a sávszélességet a Contoso elsődleges East US 2 Azure-régió.<br/><br/> **Figyelés**: Contoso kell kísérje figyelemmel az adatoknak a folyamatkiszolgálóról. Ha az adatok beáll-e a hálózati sávszélesség figyelembe veszi a Contoso [a folyamat a kiszolgáló sávszélesség szabályozása](../site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
**Azure Storage** | Az áttelepítéshez a Contoso azonosítani kell a megfelelő típusú és számú cél Azure-tárfiókot.  A Site Recovery az Azure storage virtuális gépek adatait replikálja.<br/><br/> A Site Recovery standard vagy prémium szintű (SSD) tárfiókok bA végezhet replikálást.<br/><br/> Annak eldöntéséhez, tárolás, a Contoso át kell néznie [tárhelykorlátok](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage), és idővel várható növekedésének és a használat során megnövekedett vennie. Adja meg a sebességet és áttelepítések prioritását, Contoso döntött, hogy prémium szintű storage-fiókok használatához.<br/><br/> Akkor hozzon létre, és több tárfiókot újra az áttelepítési folyamat során.
Contoso hajtott végre a felügyelt lemezek használata az összes Azure-ban üzembe helyezett virtuális gép mellett.  A szükséges iops-érték határozza meg, ha a standard szintű (HDD) vagy a prémium (SSD) lemezeket lesz.<br/>.<br/>


#### <a name="data-migration-service"></a>Data Migration Service 

Az Azure Database Migration Service (DMS) egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi az Azure-beli adat-platformokra, minimális állásidővel adatbázis több forrásból származó zökkenőmentes áttelepítések.

- A DMS egyesíti a meglévő eszközökkel és a szolgáltatások funkcióit. A Data Migration Assistant (DMA), használ, amely a rögzítési ponthoz az adatbázis-kompatibilitási vonatkozó javaslatokat és a szükséges módosításokat elemzéseket és jelentéseket létrehozni.
- A DMS egy egyszerű, önálló irányítású migrálási folyamat, amellyel a potenciális problémák megoldására az áttelepítés előtt intelligens értékelés használ.
- A DMS ipari méretekben több forrásból származó a cél Azure-adatbázis is áttelepíthet.
- A DMS támogatást nyújt az SQL Server 2005 az SQL Server 2017.

A DMS nem az egyetlen Microsoft adatbázis-áttelepítési eszköz. Get- [eszközök és szolgáltatások összehasonlítása](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

###### <a name="using-dms-at-scale"></a>A DMS használatával ipari méretekben

Contoso DMS fogja használni, az SQL Server-ről való áttelepítés esetén.  

- A DMS kiépítésekor Contoso meg kell győződnie arról, hogy ez megfelelően méretezve, pedig adatmigrálások-teljesítményének optimalizálásához állítsa be. Contoso fog válassza a "4 virtuális magot kapnak az üzleti szempontból kritikus fontosságú rétegként", ezzel lehetővé téve a szolgáltatás több vcpu-k a párhuzamos feldolgozás és a gyorsabb adatátvitel előnyeit.

    ![A DMS méretezése](./media/contoso-migration-scale/dms.png)

- A Contoso egy másik skálázási ügyfélkódos ideiglenesen le vertikális felskálázási az Azure SQL vagy MySQL-adatbázis cél-példányt a prémium szintű Termékváltozat az adatáttelepítés során. Ez minimalizálja az adatbázis-szabályozás, az adatátviteli tevékenységeket ronthatja az alacsonyabb szintű termékváltozatok használata esetén.



##### <a name="using-other-tools"></a>Más eszközök használatával

DMS, mellett Contoso segítségével más eszközök és szolgáltatások azonosítása a virtuális gép.

- Parancsfájlokat, amelyek segítségével a manuális áttelepítést rendelkeznek. Ezek a GitHub-tárházban érhető el.
- Számos [eszközök partneri](https://azure.microsoft.com/migration/partners/) migráláshoz is használható.


## <a name="phase-3-optimize"></a>3. fázis: Optimalizálás

Contoso erőforrásokat helyez át az Azure-ba, miután szükségük leegyszerűsítheti őket javíthatja a teljesítményt, és a befektetési megtérülés maximalizálásához a költségkezelési eszközökkel. Adja meg, hogy az Azure szolgáltatás használatalapú esetében használható, rendkívül fontos tudni, hogyan működnek a rendszerek, és győződjön meg arról, megfelelően van méretezni contoso.


### <a name="azure-cost-management"></a>Az Azure cost managementbe

Ahhoz, hogy a felhőbefektetések maximális kihasználása, Contoso ingyenes Azure Cost Management eszközt használja.

- A Cloudyn, a Microsoft egyik leányvállalata által készített licencelt megoldás lehetővé teszi, hogy a Contoso a felhő költségeit, szintű átláthatósággal és precizitással kezelheti.  Figyelheti, lefoglalni, és a felhőköltségek trim eszközöket biztosít.
- Az Azure Cost Management segítségével a költséglefoglalási, visszacsatolásban és költséghelyi elszámolási műveleteket az egyszerű Irányítópulti jelentések biztosít.
- A Cost Management optimalizálhatja a felhő költségeit, amely a Contoso is kezelhet és módosíthat kihasználatlan erőforrások azonosítása.
- [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe.

    
![Költségkezelés](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>Natív eszközök

Contoso is fogja használni a parancsfájlokat a fel nem használt erőforrások megkereséséhez.

- Nagy migrálások során gyakran előfordulnak akkorról darab, például virtuális merevlemezeket (VHD), amely költségek léphetnek fel, de nincs érték biztosíthat a vállalati adatok. Szkripteket a GitHub-tárházban érhető el.
- Használja a Microsoft által végzett munka contoso IT-részleg, és az Azure erőforrás-optimalizálás (ARO) eszközkészlet vegye fontolóra.
- Contoso üzembe helyezése Azure Automation-fiók, az előre konfigurált runbookok és ütemezések előfizetést, és pénzt. Az Azure erőforrás-optimalizálás automatikusan történik egy adott előfizetés, miután egy ütemezés engedélyezve van-e létre, beleértve az új erőforrások optimalizálása.
- Ez biztosítja, hogy a költségek csökkentése érdekében decentralizált automatizálási képességeivel. Funkciók:
    - Automatikus – késleltetés Azure virtuális gépek alacsony Processzorhasználat alapján.
    - Az Azure-beli virtuális gépek késleltetés és unsnooze ütemezése.
    - Késleltetés vagy unsnooze növekvő és csökkenő sorba Azure címkék használatával az Azure virtuális gépek ütemezése.
    - Tömeges csoportok igény szerinti erőforrások törlését.
- Első lépések a jelen ARO eszközkészlete [GitHub-adattárat](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit).

### <a name="partner-tools"></a>Partnerek eszközei

Partner-eszközökben, például [Hanu](https://hanu.com/insight/) és [Scalr]( https://www.scalr.com/cost-optimization/) is javítható.


## <a name="phase-4-secure--manage"></a>4. fázis: Biztonságos & kezelése

Ebben a fázisban a Contoso Azure biztonsági és felügyeleti erőforrásokat használ szabályozzák, az Azure-ban a felhőbeli alkalmazások figyeléséhez és védelméhez. Ezeket az erőforrásokat segítségével biztonságos és megfelelően felügyelt környezetet futtatása az Azure Portalon elérhető termékek használata során. Contoso kezdődik, használja ezeket a szolgáltatásokat a migrálás során, és az Azure hibrid támogatásával, továbbra is a hibrid felhőmegoldás használatával ezek közül számos egy egységes felhasználói élményt.


### <a name="security"></a>Biztonság
Contoso hagyatkoznak az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet a hibrid felhőalapú számítási feladatokhoz.

- A Security Center itt, teljes átláthatóságot és, ha az Azure-ban a felhőalkalmazások biztonságát.
- Contoso gyorsan észlelheti és reagálhat a fenyegetésekre adott válaszként, és adaptív fenyegetésvédelmet engedélyezésével csökkentheti a biztonsági kockázatokat.

[További](https://azure.microsoft.com/services/security-center/) a Security Centerrel kapcsolatos. 


### <a name="monitoring"></a>Figyelés

Contoso cégnek szüksége van a láthatósági állapotát és teljesítményét az újonnan áttelepített alkalmazásokat, az infrastruktúra és az adatok most már fut az Azure-bA. Contoso monitorozási eszközökkel, például az Azure Monitor, a Log Analytics és az Application Insights beépített Azure-felhőben használja.
 
- Ezek az eszközök Contoso egyszerűen forrásból gyűjthet adatokat, és részletes információkhoz juthat. Például Contoso mérőműszert tartalmazó lemez- és processzorhasználat a virtuális gépek esetében az alkalmazás- és hálózati függőségeket, több virtuális gépen, és nyomon követheti az alkalmazás teljesítményét.
- Contoso ezek felhőalapú figyelési eszközök használatával művelet végrehajtása és a szolgáltatási megoldások integrálása.
- [További](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) Azure figyelésével kapcsolatos.

### <a name="bcdr"></a>BCDR 

Contoso az Azure-erőforrások egy üzleti folytonossági és vészhelyreállítási (BCDR) stratégiára van szükség.

- Az Azure biztosít [beépített BCDR-funkcióival](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) való bízhatja biztonságos adatokhoz és alkalmazásokhoz, szolgáltatásokhoz. 
- A beépített funkciók mellett Contoso biztosítani szeretné, hogy azt is helyreálljon a hibák után, költséges kimaradásait, megfelelnek a megfelelőségi célkitűzéseket és adatvédelem a zsarolóprogramok és az emberi hibák ellen. Ehhez
    - Contoso költséghatékony megoldás az Azure-erőforrások biztonsági mentése Azure Backup telepíti. Mivel a szolgáltatás beépített, Contoso felhőbeli biztonsági mentést, néhány egyszerű lépésben lehet beállítani.
    - Contoso fog vészhelyreállítás beállítása az Azure virtuális gépek replikálása, feladatátvétele és feladat-visszavétel Azure-régióban, amely azt adja meg az Azure Site Recovery használatával.  Ez biztosítja, hogy az Azure virtuális gépeken futó alkalmazások továbbra is elérhető marad a Contoso kiválasztása az elsődleges régióban leállás esetén egy másodlagos régióban. [További információk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

 
## <a name="conclusion"></a>Összegzés

Ez a cikk egy Azure-migrálás ipari méretekben Contoso tervezett.  Az áttelepítési folyamat akkor felosztva négy szinten. Az elemzésekhez és migráláshoz keresztül optimalizálás, biztonsági és felügyeleti áttelepítés után hajtsa végre. Leginkább fontos a migrálási projekt, mint a teljes folyamat megtervezéséhez, de egy szervezeten belül rendszerek áttelepítése csoportok bontásához besorolások és számokat, a vállalkozás számára jelentéssel bírnak. Adatok felmérése, és besorolásokat, és a projekt kisebb migrálás esetén, amely gyorsan és biztonságosan futtatható sorozatára bonthatók.  Ezek a kisebb áttelepítések összege gyorsan alakítja át nagy sikeres migráláshoz kell az Azure-bA.

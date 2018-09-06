---
title: A Contoso-migrálás az Azure-bA – áttekintés |} A Microsoft Docs
description: A migrálás stratégiájával és a forgatókönyvek az Azure-bA migrálhatja helyszíni adatközpontját a Contoso által használt áttekintést nyújt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: raynew
ms.openlocfilehash: 10ec3bbe1174b8c38eb87276941f678841092e15
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782773"
---
# <a name="contoso-migration-overview"></a>Contoso áttelepítési: áttekintés


Ez a cikk bemutatja, hogyan a áttelepíti a Contoso nevű fiktív szervezet helyszíni infrastruktúrát a [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) felhő. 

Ez a dokumentum a cikkeket, amelyek megmutatják, hogyan telepíti át a fiktív Contoso az Azure-bA az első. A sorozat adatait és forgatókönyveit, amelyek bemutatják, hogyan infrastruktúra áttelepítését, és futtatjuk a különböző típusú migrálások tartalmazza. Forgatókönyvek egyre összetettebbé válnak, és idővel további cikkek adunk hozzá. A cikkek bemutatják, hogyan a Contoso vállalat befejezi a migrálási feladatát, de általános olvasásra és konkrét utasításokat mutatók egész rendelkeznek.

## <a name="introduction"></a>Bevezetés

Az Azure felhőszolgáltatások átfogó készletét hozzáférést biztosít. A fejlesztők és informatikai szakemberek számára ezek a szolgáltatások használatával elkészítheti, telepítheti és kezelheti az alkalmazások különféle eszközökön és keretrendszereken adatközpontokból álló globális hálózaton keresztül. Ahogy a digitális átállás különféle kihívások elé állítja vállalkozását, az Azure-felhő segít az erőforrások és műveletek optimalizálásának, az ügyfelekkel és alkalmazottakkal való kapcsolatteremtésnek és a termékek átalakításának a megtervezésében.

A felhő számos előnyt nyújt a rugalmasság, a költségcsökkentés, a teljesítmény és a megbízhatóság terén, de az Azure tisztában van azzal, hogy mindezek ellenére számos cégnek egy ideig még helyszíni adatközpontokat is futtatnia kell. A felhőre való átállás akadályainak elhárítása érdekében az Azure egy hibrid felhőstratégiát biztosít, amely áthidalja a helyszíni adatközpontok és az Azure-beli nyilvános felhő közötti távolságot. Például használatával az Azure felhőbeli erőforrások, például az Azure biztonsági mentése védelmet nyújt a helyszíni erőforrások, vagy az Azure analytics segítségével betekintést nyerhet a helyszíni számítási feladatokat. 

A hibridfelhős stratégia részeként az Azure egyre bővülő megoldásokat kínál a helyszíni alkalmazások és számítási feladatok a felhőbe való áttelepítését. Egyszerű lépésekkel átfogó értékelést készítheti a helyszíni erőforrásairól, és kiszámíthatja, hogyan fognak működni az Azure-felhőben. Ezután a részletes értékeléssel már magabiztosan migrálhatja erőforrásait az Azure-ba. Ha az erőforrások megfelelően működnek az Azure-ban, optimalizálhatja őket a hozzáférés, a rugalmasság, a biztonság és a megbízhatóság fenntartása és javítása érdekében.

## <a name="migration-strategies"></a>Migrálási stratégiák

A felhőbe való migrálásra stratégiák négy tág kategóriába sorolhatók: újratárolása, újrabontása, újratervezése vagy újbóli létrehozása. Ön elfogadja a stratégia az üzleti élet és áttelepítési célok függ. Különböző stratégiákat előfordulhat, hogy fogadja el. Például, lehetett váltani, áthelyezési (lift-and-shift) egyszerű, vagy alkalmazásokkal, amelyek az üzleti szempontból nem, de azokat, amelyek összetett, és üzleti szempontból kritikus fontosságú újratervezése. Tekintsük át azokat a stratégiákat.


**Stratégia** | **Definíció** | **Mikor érdemes használni?** 
--- | --- | --- 
**Áthelyezési** | Gyakran nevezik "lift-and-shift" áttelepítés. Ezt a beállítást nem szükséges módosítania a kódot, és tekintsük át meglévő alkalmazásait az Azure-ban gyorsan. Egyes alkalmazások áttelepítése, amíg az, a kockázata és költsége társított a programkód módosítása nélkül, a felhő előnyeinek. | Ha szeretne gyorsan helyezhet alkalmazásokat a felhőbe.<br/><br/> Ha szeretné helyezni az alkalmazás azt módosítása nélkül.<br/><br/> Amikor kihasználására tervezett alkalmazások, hogy használhatnak olyan [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) méretezhetőség az áttelepítés után.<br/><br/> Ha alkalmazások az Ön számára fontos, de Alkalmazásfunkciók azonnali módosításai nem szükséges.
**Újrabontása** | Más néven "újracsomagolás", újrabontás minimális módosítását igényli az alkalmazásokhoz, úgy, hogy csatlakozhassanak [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/), és a felhőalapú megoldások használata.<br/><br/> Például áttelepítheti a meglévő alkalmazások Azure App Service vagy az Azure Kubernetes Service (AKS).<br/><br/> Vagy beállítások, például Azure SQL Database felügyelt példányába, Azure Database for MySQL, Azure Database for PostgreSQL, és az Azure Cosmos DB-be lehetett refaktorovat a relációs és nem relációs adatbázisok. | Ha az alkalmazás egyszerűen lehet újracsomagolják működjön az Azure-ban.<br/><br/> Ha a alkalmazni kívánt innovatív DevOps-eljárások az Azure által biztosított, vagy szeretne áttérni a információ a Devopsról stratégiát használ a számítási feladatokhoz.<br/><br/> A újrabontás, gondolja át a meglévő kódbázis, és a rendelkezésre álló fejlesztési szakismeretek hordozhatóságát kell.
**Újratervezése** | Az áttelepítéshez átszervezése módosításával, és kiterjeszti a alkalmazás funkcióit és optimalizálhatja a felhő méretezhetőségére vonatkozó alkalmazásarchitektúra kódbázis összpontosít.<br/><br/> Ha például érvénytelenítheti a monolitikus alkalmazásokat felbonthatja egy csoportja, amelyek együttműködése az könnyedén méretezheti a rendszert le.<br/><br/> Vagy egy teljes körűen felügyelt DBaaS megoldások, például az Azure SQL Database felügyelt példányába, Azure Database for MySQL, Azure Database for PostgreSQL, és az Azure Cosmos DB a relációs és nem relációs adatbázisok, sikerült újratervezése. | Amikor az alkalmazásokat kell főbb verziók új lehetőségeket építhetnek be, vagy egy felhőalapú platform hatékonyan dolgozhat.<br/><br/> Ha bármit alkalmazás használni kívánt, méretezhetőségi követelményeinek, innovatív Azure DevOps-eljárások vonatkoznak, és kis méretű virtuális gépek használata.
**Újraépítése** | Építse újra a lépés egy dolgot tovább csökkenthetők az Azure felhőalapú technológiákat használó teljesen új alkalmazás újraépítését vesz igénybe.<br/><br/> Ha például is létrehozhatja zöldmezős natív felhőalapú technológiákkal, mint például az Azure Functions, Azure mesterséges Intelligencia, Azure SQL Database felügyelt példányába és Azure Cosmos DB-alkalmazások. | Ha azt szeretné, hogy a gyors fejlesztést, és a meglévő alkalmazások korlátozottan funkcionalitása és élettartama.<br/><br/> Amikor elkészült, gyorsíthatja fel az üzleti innováció (beleértve az Azure által biztosított fejlesztési és üzemeltetési eljárások), natív felhőalapú technológiákat használó új alkalmazásokat hozhat létre, és előnyökön mesterséges Intelligencia, a Blokkláncok és az IoT előnyeit.

## <a name="migration-articles"></a>Migrálásról szóló cikksorozat

A sorozat a cikkek az alábbi táblázat foglalja össze.  

- Minden egyes áttelepítési forgatókönyv határozzák meg, amelyek meghatározzák az áttelepítési stratégia némileg eltérő üzleti céljainak megvalósítását.
- Egyes üzembe helyezési forgatókönyvekben biztosítunk a stratégiai és a célokat, javasolt architektúra, a lépéseket hajtsa végre az áttelepítést, és javaslatot karbantartása és a további lépésekről a migrálás befejezése után kapcsolatos információk.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
1. cikk: áttekintés | A cikk sorozat, a Contoso-áttelepítési stratégia és az adatsorozathoz használt mintaalkalmazások áttekintése. | Ez a cikk
[2. cikk: Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md)  | Contoso fut, annak a helyszíni SmartHotel360 app VMware-en futó értékelését. Contoso értékeli az alkalmazás virtuális gépek az Azure Migrate szolgáltatás és a Data Migration Assistant szolgáltatást használó alkalmazás SQL Server-adatbázis használatával. | Elérhető
[4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel360 alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése egy Azure SQL Database felügyelt példánya a a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető   
[5. cikk: Áthelyezési egy alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-vm.md) | Contoso Azure virtuális gépekre a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással. | Rendelkezésre álló [5. cikk: az Azure virtuális gépein egy alkalmazás Újratárolása](contoso-migration-rehost-vm.md) | Contoso a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással az Azure virtuális gépekhez. | Elérhető
[Cikk 6: Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazások Újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti a SmartHotel360 alkalmazást. Contoso Site Recovery használatával az alkalmazás virtuális gépek áttelepítéséhez. A Database Migration Service használatával az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt. | Rendelkezésre álló [7. cikk: egy Azure-beli virtuális gépeken Linux alkalmazás Újratárolása](contoso-migration-rehost-linux-vm.md) | Contoso az Azure virtuális gépek, a Site Recovery szolgáltatás használatával Linux osTicket alkalmazás lift-and-shift áttelepítés befejeződött. | Elérhető
[Cikk 8: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure Database for MySQL-hez](contoso-migration-rehost-linux-vm-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure virtuális gépek Site Recovery használatával. Az áttelepítése az alkalmazás-adatbázis az Azure Database for MySQL-hez a MySQL Workbench használatával. | Elérhető
[9. cikk: Újrabontás egy alkalmazást az Azure web App alkalmazásban és az Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso a SmartHotel360 alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése egy Azure SQL Server-példány az a Database Migration Assistant szolgáltatást. | Elérhető    
[Cikk 10: Újrabontás egy Linux-alkalmazást egy Azure-webalkalmazást és az Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás egy Azure-webalkalmazást az Azure Traffic Managerrel, a folyamatos készregyártás a GitHub integrált több Azure-régióban található. Contoso áttelepíti az alkalmazás-adatbázis egy Azure Database for MySQL-példányt. | Elérhető
[11. cikk: Újrabontás a Team Foundation Server, a Visual Studio Team Servicesben](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi Visual Studio Team Services az Azure-ban. | Elérhető
[A cikk 12: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Contoso a SmartHotel360 alkalmazást áttelepíti az Azure-bA. Ezután azt rearchitects az alkalmazás webes réteg az Azure Service Fabric és az adatbázis az Azure SQL Database-ban futó Windows tárolójaként. | Elérhető 
[Cikk 13: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel360 alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service (AKS), az Azure Functions, Azure Cognitive Services és az Azure Cosmos DB használatával. | Elérhető  


Ebben a cikkben Contoso állítja be az infrastruktúra-elemeket kell minden áttelepítési forgatókönyveinek végrehajtásához. 







### <a name="demo-apps"></a>Bemutató alkalmazások

A két bemutatóalkalmazás - SmartHotel360 és osTicket használja.

- **SmartHotel360**: Ez az alkalmazás a Microsoft fejlesztette, alkalmazás, amelyet használhat az Azure használata során. Nyílt forráskódú nyújtja, és töltheti le a [GitHub](https://github.com/Microsoft/SmartHotel360). Egy ASP.NET-alkalmazás csatlakozik az SQL Server-adatbázis. Az alkalmazás jelenleg a Windows Server 2008 R2 és az SQL Server 2008 R2 rendszert futtató két VMware virtuális gépeken. Az alkalmazás virtuális gép üzemeltethető a helyszínen és vCenter-kiszolgáló felügyeli.
- **osTicket**: egy nyílt forráskódú Linux rendszeren futtatható, webalkalmazásra mutató jegykiadás ügyfélszolgálat. Letöltheti a [GitHub](https://github.com/osTicket/osTicket). Aktuális az alkalmazás nem VMware virtuális gépeken két Ubuntu 16.04 LTS rendszert futtató, az Apache 2, a PHP 7.0 és a MySQL 5.7-es
    

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso állít be egy helyszíni és Azure-infrastruktúra előkészítése az áttelepítéshez.




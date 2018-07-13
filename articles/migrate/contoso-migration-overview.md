---
title: A Contoso-migrálás az Azure-bA – áttekintés |} A Microsoft Docs
description: A migrálás stratégiájával és a forgatókönyvek az Azure-bA migrálhatja helyszíni adatközpontját a Contoso által használt áttekintést nyújt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 4cad84d1cabd140f4846cb823e8c4b7ce11bc1f6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008246"
---
# <a name="contoso-migration-overview"></a>Contoso áttelepítési: áttekintés


Ez a cikk bemutatja, hogyan a Contoso nevű fiktív szervezet telepíti át a helyszíni infrastruktúra a [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) felhő. 

Ez a dokumentum a cikkeket, amelyek megmutatják, hogyan telepíti át a fiktív Contoso az Azure-bA az első. A sorozat adatait és forgatókönyveit, amelyek bemutatják, hogyan egy áttelepítési infrastruktúra beállítását, és futtassa a különböző típusú migrálások tartalmazza. Forgatókönyvek egyre összetettebbé válnak, és idővel további cikkek adunk hozzá. Cikkek bemutatják, hogyan a Contoso vállalat befejezi a migrálási feladatát, de általános olvasásra és konkrét utasításokat mutatók egész rendelkeznek.

## <a name="introduction"></a>Bevezetés

Az Azure felhőszolgáltatások átfogó készletét hozzáférést biztosít. A fejlesztők és informatikai szakemberek számára ezek a szolgáltatások használatával elkészítheti, telepítheti és kezelheti az alkalmazások különféle eszközökön és keretrendszereken adatközpontokból álló globális hálózaton keresztül. Ahogy a digitális átállás különféle kihívások elé állítja vállalkozását, az Azure-felhő segít az erőforrások és műveletek optimalizálásának, az ügyfelekkel és alkalmazottakkal való kapcsolatteremtésnek és a termékek átalakításának a megtervezésében.

A felhő számos előnyt nyújt a rugalmasság, a költségcsökkentés, a teljesítmény és a megbízhatóság terén, de az Azure tisztában van azzal, hogy mindezek ellenére számos cégnek egy ideig még helyszíni adatközpontokat is futtatnia kell. A felhőre való átállás akadályainak elhárítása érdekében az Azure egy hibrid felhőstratégiát biztosít, amely áthidalja a helyszíni adatközpontok és az Azure-beli nyilvános felhő közötti távolságot. Ilyen például az Azure-felhő erőforrásainak, például a biztonsági mentésnek a használata a helyszíni erőforrások védelméhez, illetve az Azure Analytics használata a helyszíni számítási feladatok elemzéséhez. 

A hibridfelhős stratégia részeként az Azure egyre bővülő megoldásokat kínál a helyszíni alkalmazások és számítási feladatok a felhőbe való áttelepítését. Egyszerű lépésekkel átfogó értékelést készítheti a helyszíni erőforrásairól, és kiszámíthatja, hogyan fognak működni az Azure-felhőben. Ezután a részletes értékeléssel már magabiztosan migrálhatja erőforrásait az Azure-ba. Ha az erőforrások megfelelően működnek az Azure-ban, optimalizálhatja őket a hozzáférés, a rugalmasság, a biztonság és a megbízhatóság fenntartása és javítása érdekében.

## <a name="migration-strategies"></a>Migrálási stratégiák

A felhőbe való migrálásra stratégiák négy tág kategóriába sorolhatók: újratárolása, újrabontása, újratervezése vagy újbóli létrehozása. Ön elfogadja a stratégia az üzleti élet és áttelepítési célok függ. Különböző stratégiákat előfordulhat, hogy fogadja el. Például, lehetett váltani, áthelyezési (lift-and-shift) egyszerű, vagy alkalmazásokkal, amelyek az üzleti szempontból nem, de azokat, amelyek összetett, és üzleti szempontból kritikus fontosságú újratervezése. Tekintsük át azokat a stratégiákat.


**Stratégia** | **Definíció** | **Mikor érdemes használni?** 
--- | --- | --- 
**Áthelyezési** | Gyakran nevezik "lift-and-shift" áttelepítés. Ezt a beállítást nem szükséges módosítania a kódot, és tekintsük át meglévő alkalmazásait az Azure-ban gyorsan. Egyes alkalmazások áttelepítése, amíg az, a kockázata és költsége társított a programkód módosítása nélkül, a felhő előnyeinek. | Ha szeretne gyorsan helyezhet alkalmazásokat a felhőbe.<br/><br/> Ha szeretné helyezni az alkalmazás azt módosítása nélkül.<br/><br/> Amikor kihasználására tervezett alkalmazások, hogy használhatnak olyan [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) méretezhetőség az áttelepítés után.<br/><br/> Ha alkalmazások az Ön számára fontos, de Alkalmazásfunkciók azonnali módosításai nem szükséges.
**Újrabontása** | Más néven "újracsomagolás", újrabontás minimális módosítását igényli az alkalmazásokhoz, úgy, hogy csatlakozhassanak [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/), és a felhőalapú megoldások használata.<br/><br/> Például áttelepítheti a meglévő alkalmazások Azure App Service vagy az Azure Kubernetes Service (AKS).<br/><br/> Vagy beállítások, például Azure SQL Database felügyelt példányába, Azure Database for MySQL, Azure Database for PostgreSQL, és az Azure Cosmos DB-be lehetett refaktorovat a relációs és nem relációs adatbázisok. | Ha az alkalmazás egyszerűen lehet újracsomagolják működjön az Azure-ban.<br/><br/> Ha a alkalmazni kívánt innovatív DevOps-eljárások az Azure által biztosított, vagy szeretne áttérni a információ a Devopsról stratégiát használ a számítási feladatokhoz.<br/><br/> A újrabontás, gondolja át a meglévő kódbázis, és a rendelkezésre álló fejlesztési szakismeretek hordozhatóságát kell.
**Újratervezése** | Az áttelepítéshez átszervezése módosításával, és kiterjeszti a alkalmazás funkcióit és optimalizálhatja a felhő méretezhetőségére vonatkozó alkalmazásarchitektúra základ kódu v összpontosít.<br/><br/> Ha például érvénytelenítheti a monolitikus alkalmazásokat felbonthatja egy csoportja, amelyek együttműködése az könnyedén méretezheti a rendszert le.<br/><br/> Vagy sikerült újratervezése relációs és nem relációs adatbázisok teljes körűen felügyelt DBaaS megoldások, például az Azure SQL Database felügyelt példányába, Azure Database for MySQL, Azure Database for PostgreSQL, és az Azure Cosmos DB. | Amikor az alkalmazásokat kell főbb verziók új lehetőségeket építhetnek be, vagy egy felhőalapú platform hatékonyan dolgozhat.<br/><br/> Ha bármit alkalmazás használni kívánt, méretezhetőségi követelményeinek, innovatív Azure DevOps-eljárások vonatkoznak, és kis méretű virtuális gépek használata.
**Újraépítése** | Építse újra a lépés egy dolgot tovább csökkenthetők az Azure felhőalapú technológiákat használó teljesen új alkalmazás újraépítését vesz igénybe.<br/><br/> Ha például is létrehozhatja zöldmezős natív felhőalapú technológiákkal, mint például a kiszolgáló nélküli, Azure mesterséges Intelligencia, Azure SQL Database felügyelt példányába és Azure Cosmos DB-alkalmazások. | Ha azt szeretné, hogy a gyors fejlesztést, és a meglévő alkalmazások korlátozottan funkcionalitása és élettartama.<br/><br/> Amikor elkészült, gyorsíthatja fel az üzleti innováció (beleértve az Azure által biztosított fejlesztési és üzemeltetési eljárások), natív felhőalapú technológiákat használó új alkalmazásokat hozhat létre, és előnyökön mesterséges Intelligencia, a blokkláncok és az IoT előnyeit.

## <a name="migration-articles"></a>Migrálásról szóló cikksorozat

A sorozat a cikkek az alábbi táblázat foglalja össze.  

- Minden egyes áttelepítési forgatókönyv határozzák meg, amelyek meghatározzák az áttelepítési stratégia némileg eltérő üzleti céljainak megvalósítását.
- Egyes üzembe helyezési forgatókönyvekben biztosítunk a stratégiai és a célokat, javasolt architektúra, a lépéseket hajtsa végre az áttelepítést, és javaslatot karbantartása és a további lépésekről a migrálás befejezése után kapcsolatos információk.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
1. cikk: áttekintés | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Ez a cikk.
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Contoso alkalmazás rendelkező virtuális gépek értékeli a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési egy alkalmazást az Azure virtuális gépeken és a egy felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso fut egy lift-and-shift-migrálás az Azure-bA a helyszíni SmartHotel alkalmazás. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás-adatbázis SQL felügyelt példányra, használja a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető
[5. cikk: Áthelyezési egy alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-vm.md) | Bemutatja, hogyan Contoso migrálása az Azure virtuális gépeire, a Site Recovery szolgáltatással az SmartHotel alkalmazás virtuális gépeit. | Elérhető
[A cikk 6: Egy alkalmazást az Azure virtuális gépek és az SQL Server Always On rendelkezésre állási csoport áthelyezési](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. Contoso számára, hogy az alkalmazás virtuális gépeit és a Database Migration service, az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt a Site Recovery használja. | Elérhető
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso hajtja végre a Linux osTicket alkalmazás lift-and-shift áttelepítés Azure virtuális gépekre, a Site Recovery | Elérhető
[A cikk 8: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Elérhető
[9. cikk: Újrabontás egy alkalmazást az Azure Web Apps és az Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Bemutatja, hogyan Contoso a SmartHotel alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány | Elérhető
[10. cikk: Újrabontás egy Linux-alkalmazás Azure Web Apps és az Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure Web Apps több helyen, a folyamatos készregyártás a GitHub integrálva. Az alkalmazás-adatbázis nekik át egy Azure-beli MySQL-példányt. | Elérhető
[11. cikk: Újrabontás a TFS-t a vsts-ben](contoso-migration-tfs-vsts.md) | Bemutatja, hogyan telepíti át a Contoso a saját helyi Team Foundation Server (TFS) központi, migrálás, a Visual Studio Team Services (VSTS) az Azure-ban. | Elérhető
[A cikk 12: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Bemutatja, hogyan Contoso áttelepíti, és rearchitects SmartHotel alkalmazás az Azure-bA. Az alkalmazás webes réteg egy Windows-tárolót, és a egy Azure SQL Database-ben az alkalmazás-adatbázis újratervezése azokat. | Elérhető
[Cikk 13: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Bemutatja, hogyan építse újra a Contoso SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az App Services, Azure-beli Kubernetes, az Azure Functions, a Cognitive services és a Cosmos DB használatával. | Elérhető






### <a name="demo-apps"></a>Bemutató alkalmazások

A két bemutatóalkalmazás - SmartHotel, és osTicket használja.

- **SmartHotel360**: Ez az alkalmazás a Microsoft fejlesztette, alkalmazás, amelyet használhat az Azure használata során. Nyílt forráskódú nyújtja, és töltheti le a [GitHub](https://github.com/Microsoft/SmartHotel360). Egy ASP.NET-alkalmazás csatlakozik az SQL Server-adatbázis. Az alkalmazás jelenleg a Windows Server 2008 R2 és az SQL Server 2008 R2 rendszert futtató két VMware virtuális gépeken. Az alkalmazás virtuális gép üzemeltethető a helyszínen és vCenter-kiszolgáló felügyeli.
- **osTicket**: egy nyílt forráskódú Linux rendszeren futtatható, webalkalmazásra mutató jegykiadás ügyfélszolgálat. Letöltheti a [GitHub](https://github.com/osTicket/osTicket). Aktuális az alkalmazás nem az Apache 2, a PHP 7.0 és a MySQL 5.7-es 16.04LTS, Ubuntu rendszert futtató két VMware virtuális gépeken
    

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso állít be egy helyszíni és Azure-infrastruktúra előkészítése az áttelepítéshez.




---
title: Útmutató az első lépésekhez a fejlesztők az Azure-ban |} A Microsoft Docs
description: Ez a témakör a fejlesztők a kezdéshez a Microsoft Azure platform használatát a fejlesztési igények kielégítéséhez alapvető információkat biztosít.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: f4dfc376e49ac26f0c054282994a29d9a7d2b54b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442171"
---
# <a name="get-started-guide-for-azure-developers"></a>Első lépésekhez készült útmutató Azure-fejlesztőknek

## <a name="what-is-azure"></a>Mi az Azure?

Az Azure el egy átfogó felhőalapú platform, meglévő alkalmazásait, leegyszerűsítheti az új alkalmazások fejlesztését, és a helyszíni alkalmazások is javíthatja. Az Azure együttműködik a felhőszolgáltatásokat, fejlesztéséhez, teszteléséhez, üzembe helyezése és kezelése az alkalmazások kell – miközben kihasználhatja a felhő hatékonysága révén számítástechnika.

Az alkalmazások az Azure-ban üzemelteti, kezdhetik, és az ügyfelek igényei szerint növekedésével könnyedén méretezheti a az alkalmazás. Az Azure továbbá a magas rendelkezésre állású alkalmazások, még akkor is beleértve a különböző régiók közötti feladatátvétel szükséges megbízhatóságot biztosít. A [az Azure portal](https://portal.azure.com) könnyen kezelheti az összes Azure-szolgáltatások teszi lehetővé. A szolgáltatások programozott módon is kezelhetők szolgáltatásspecifikus API-k és a sablonok használatával.

**Ez célközönsége**: Ez az útmutató olyan az alkalmazásfejlesztők számára az Azure platform bemutatása. Útmutatás és új alkalmazások az Azure-ban vagy a meglévő alkalmazások Azure-ba való migrálás megkezdéséhez szükséges iránya biztosít.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

Az összes szolgáltatás Azure által kínált mely szolgáltatásokat támogatja a megoldásarchitektúra kell gondolnia infrastruktúraeszközök lehet. Ez a szakasz kiemeli az Azure-szolgáltatások, amelyek gyakran használják a fejlesztők. Az összes Azure-szolgáltatások listáját lásd: a [Azure dokumentációja](../../index.md).

Először meg kell határoznia bemutatja, hogyan működtethető az Azure-ban az alkalmazás. Van szüksége egy virtuális gépet (VM), a teljes infrastruktúra kezeléséhez. Használhatja a platform Azure által biztosított felügyeleti eszközökkel? Talán egy kiszolgáló nélküli keretrendszer csak a gazdagép programkód van szüksége?

Az alkalmazásnak szüksége van a felhőalapú tárolást, amely az Azure számos lehetőséget kínál. Használja ki az Azure enterprise-hitelesítés is igénybe vehet. Is találhatók eszközök számára a felhőalapú fejlesztés és a figyelés, és a legtöbb üzemeltetési szolgáltatásokat kínálnak a DevOps-integráció.

Most nézzük meg, amely azt javasoljuk, vizsgálja meg az alkalmazások adott szolgáltatások.

### <a name="application-hosting"></a>Alkalmazások üzemeltetése

Az Azure biztosít több felhőalapú számítási ajánlatok az alkalmazás futtatásához, így nem kell aggódnia az infrastruktúra részleteit. Egyszerűen vertikális vagy horizontális felskálázása az erőforrások, az alkalmazás használatának növekedésével.

Az Azure az alkalmazások fejlesztéséhez és az üzemeltetési igények támogató szolgáltatásokat kínál. Az Azure szolgáltatás (IaaS), hogy teljes körűen felügyelve az alkalmazások üzemeltetése infrastruktúrát biztosít. A Platformszolgáltatás (PaaS) ajánlatok az Azure Platform adja meg a teljes körűen felügyelt szolgáltatások, az alkalmazások szükséges. Nincs még akkor is igaz a kiszolgáló nélküli üzemeltetés az Azure-ban hol ehhez szüksége a kód írása.

![Azure-üzemeltetési lehetőségeinek alkalmazás](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Ha azt szeretné, hogy a webes projektek közzététele leggyorsabb elérési útját, fontolja meg az Azure App Service-ben. App Service-ben megkönnyíti a webalkalmazások a mobilos ügyfeleket támogatja, és könnyedén felhasznált REST API-k közzétételét bővítheti. A platform használatával a közösségi szolgáltatók, forgalom-alapú automatikus méretezés, éles környezetben, és a folyamatos és tárolókon alapuló üzemelő példányok hitelesítést nyújt.

Webalkalmazások, mobilalkalmazások háttérkomponenseit és API-alkalmazásokat hozhat létre.

Mivel minden három alkalmazástípus az App Service-modul, egy webhely üzemeltetéséhez, mobilos ügyfeleket támogatja, és teszi közzé az API-k az Azure-ban, az ugyanazon a projekten, vagy a megoldás egy. App Service-ben kapcsolatos további információkért lásd: [Mi az Azure Web Apps](../../app-service/app-service-web-overview.md).

App Service-ben úgy tervezték, a fejlesztési és üzemeltetési szem előtt. Közzététel és a folyamatos integráció környezetekhez, például a GitHub webhookok, a Jenkins, a Visual Studio Team Services, TeamCity és mások különböző eszközöket támogatja.

Áttelepítheti a meglévő alkalmazásokkal, az App Service használatával a [online migrálási eszköz](https://www.migratetoazure.net/).

>**Mikor érdemes használni**: használata App Service-ben való migráláshoz meglévő webes alkalmazások az Azure-ba, és amikor szüksége van egy teljes körűen felügyelt üzemeltetési platformot a web apps számára. Használhatja az App Service-ben, amikor szüksége van mobilügyfelek támogatásához, vagy a REST API-k elérhetővé az alkalmazást.

>**Első lépések**: App Service használatával egyszerűen létrehozhat és üzembe helyezése az első [webalkalmazás](../../app-service/app-service-web-get-started-dotnet.md), [mobilalkalmazás](../../app-service-mobile/app-service-mobile-ios-get-started.md), vagy [API-alkalmazás](../../app-service/app-service-web-tutorial-rest-api.md).

>**Próbálja ki most**: App Service lehetővé teszi a platform kipróbálásához regisztráljon az Azure-fiók nélkül egy rövid élettartamú alkalmazás üzembe helyezése. Próbálja ki a platformot és [az Azure App Service-alkalmazás létrehozása](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

Egy infrastruktúra-szolgáltató (IaaS), mint az Azure lehetővé teszi üzembe helyezése vagy az alkalmazás Windows vagy Linux rendszerű virtuális gépek migrálása. És az Azure Virtual Network szolgáltatás együttes Azure Virtual Machines támogatja a központi Windows- vagy Linux rendszerű virtuális gépek az Azure-bA. A virtuális gépek, a gép konfigurációjának teljes hozzáféréssel rendelkeznek. Virtuális gépek használatakor az Ön felelős minden kiszolgáló szoftver telepítési, konfigurációs, karbantartási és operációs rendszer javításait.

Miatt mértékű, amely a virtuális gépeken server számítási feladatok széles skáláját futtathatja az Azure-ban nem illő PaaS modellbe. Ilyen számítási feladat például az adatbázis-kiszolgálók, a Windows Server Active Directory és a Microsoft SharePoint. További információkért tekintse meg a Virtual Machines dokumentációja a [Linux](/azure/virtual-machines/linux/) vagy [Windows](/azure/virtual-machines/windows/).

>**Mikor érdemes használni**: használata virtuális gépek, ha azt szeretné, teljes ellenőrzés vagy a helyszíni alkalmazások és szolgáltatások áttelepítése az Azure-bA anélkül módosíthatja az alkalmazás-infrastruktúra keresztül.

>**Első lépések**: hozzon létre egy [Linux rendszerű virtuális gép](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) vagy [Windows virtuális gép](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) az Azure Portalról.

#### <a name="azure-functions-serverless"></a>Az Azure Functions (a kiszolgáló nélküli)

Aggódni ki felépítésére és kezelésére, a kód futtatásához egy egész alkalmazással vagy az infrastruktúra helyett. Mi történik, ha Ön sikerült csak a kód írása és azok válaszként az események vagy ütemezés szerint futtatni?  [Az Azure Functions](../../azure-functions/functions-overview.md) van egy "kiszolgáló nélküli"-stílus ajánlat, amely lehetővé teszi, hogy csak a kód írását, van szüksége. A Functions kódfuttatás HTTP-kérelmekre, a webhookok, a cloud service-események vagy ütemezés szerint aktiválódik. A fejlesztési nyelvet választott, például C nyelven is programozhat\#, F\#, Node.js, Python vagy PHP. A fogyasztás alapú számlázáshoz kell fizetnie csak az idő a kódot, és az Azure méretezhető, igény szerint.

>**Mikor érdemes használni**: az Azure Functions használata a kód más Azure-szolgáltatások által meghívott webes események vagy ütemezés szerint, ha. Is használhatja a Functions, ha már nincs szükség a terhelést a teljes üzemeltetett projekt, vagy csak szeretné a kódot futtató időért kell fizetnie. További tudnivalókért lásd: [Azure Functions áttekintése](../../azure-functions/functions-overview.md).

>**Első lépések**: kövesse a Functions gyorsútmutatóban történő [az első függvény létrehozása](../../azure-functions/functions-create-first-azure-function.md) a portálról.

>**Próbálja ki most**: az Azure Functions lehetővé teszi a kód, regisztráljon az Azure-fiók nélkül futtathat. Próbálja ki most, és [az első Azure-függvény létrehozása](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Az Azure Service Fabric egy elosztott rendszerplatform, amellyel egyszerűen készíthet, csomagolását, üzembe helyezése és kezelése a skálázható és megbízható mikroszolgáltatások a. Kiépítés, az üzembe helyezés, figyelés, frissítés/javítása is biztosít átfogó alkalmazásfelügyeleti képességeket, és törölni a telepített alkalmazások. Gépek egy megosztott készletén futó, alkalmazások is kezdhetik, és akár több ezer gépet szükség szerint méretezheti.

A Service Fabric támogatja a WebAPI az Open Web Interface .NET (OWIN) és az ASP.NET Core. SDK-kat biztosít a Linux, a .NET Core és a Java-szolgáltatások létrehozásához. A Service Fabric kapcsolatos további információkért tekintse meg a [Service Fabric képzési](https://azure.microsoft.com/documentation/learning-paths/service-fabric/).

>**Mikor érdemes használni:** Service Fabric akkor hasznos, amikor az alkalmazás létrehozása vagy egy meglévő alkalmazás használatához a mikroszolgáltatási architektúrát újraírását. A Service Fabric használja, amikor szüksége van további felett, vagy közvetlen hozzáférést, az alapul szolgáló infrastruktúra.

>**Első lépések:** [az első Azure Service Fabric-alkalmazás létrehozása](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Javíthatja az alkalmazások az Azure-szolgáltatásokkal

Alkalmazások üzemeltetése, mellett az Azure szolgáltatási ajánlatok, amelyek javíthatják a funkciót, fejlesztési és az alkalmazások, mind a felhőben és helyszíni karbantartási biztosít.

#### <a name="hosted-storage-and-data-access"></a>Kihelyezett tárolási és hozzáférési

A legtöbb alkalmazást úgy kell tárolja az adatokat, függetlenül attól, hogyan kívánja futtatni az alkalmazást az Azure-ban, fontolja meg legalább egy, a következő tároló- és adatszolgáltatásokat.

-   **Az Azure Cosmos DB**: egy globálisan elosztott, többmodelles adatbázis-szolgáltatás, amely lehetővé teszi, hogy rugalmasan skálázhatja a teljesítményt és tárolókapacitást között tetszőleges számú földrajzi régió egy átfogó SLA-val. 
    >**Mikor érdemes használni:** során az alkalmazásnak szüksége van a dokumentum, táblázat vagy gráf típusú adatbázisokat, beleértve a MongoDB-adatbázisokat, a többszörös, jól definiált konzisztenciamodellekkel. 

    >**Első lépések**: [egy Azure Cosmos DB-webalkalmazás létrehozása](../../cosmos-db/create-sql-api-dotnet.md). Ha Ön a MongoDB fejlesztő, lásd: [egy MongoDB-webalkalmazás létrehozása az Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

-   **Az Azure Storage**: biztosít a tartós, magas rendelkezésre állású tárolási blobok, üzenetsorok, fájlok és más típusú nem relációs adatok esetében. Storage-beli virtuális gépek tárolási alapokat biztosít.

    >**Mikor érdemes használni**: Ha az alkalmazás tárolja a nem relációs adatok, például a kulcs-érték párokat (táblák), blobok, fájlok, megosztások vagy üzenetek (üzenetsorok).

    >**Első lépések**: ezek a típusok tárolás közül választhat: [blobok](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [táblák](../../cosmos-db/table-storage-how-to-use-dotnet.md), [üzenetsorok](../../storage/queues/storage-dotnet-how-to-use-queues.md), vagy [fájlok](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Az Azure SQL Database**: a Microsoft SQL Server motoron relációs táblázatos adatok felhőbeli tárolása az Azure-alapú verzióját. SQL Database kiszámítható teljesítmény, méretezhetőség, nincs állásidő, üzletmenet-folytonosságot és adatvédelmet biztosít.

    >**Mikor érdemes használni**: Ha az alkalmazás által kért adattárolás a hivatkozási integritás tranzakciótámogatást és támogatás az TSQL-lekérdezésekhez.

    >**Első lépések**: [SQL-adatbázis létrehozása percek alatt az Azure portal használatával](../../sql-database/sql-database-get-started.md).


Használhat [Azure Data Factory](../../data-factory/introduction.md) meglévő helyszíni adatokat az Azure-bA áthelyezni. Ha még nem áll készen az adatok áthelyezése a felhőbe, [hibrid kapcsolatok](../../biztalk-services/integration-hybrid-connection-overview.md) a BizTalk Services lehetővé teszi, hogy csatlakozik az App Service üzemeltetett alkalmazásnak, hogy a helyszíni erőforrásokhoz. Is csatlakozhat az Azure-beli adat- és tárolási szolgáltatások a helyszíni alkalmazásokat.

#### <a name="docker-support"></a>Docker-támogatás

Docker-tárolók, az operációs rendszer virtualizálás, az űrlap segítségével hatékony és kiszámítható módon alkalmazásokat telepíthet. Egy tárolóba helyezett alkalmazást éles környezetben ugyanúgy működik a lépésig, a fejlesztési és tesztelési rendszereit. Tárolók Docker-eszközök használata kezelheti. A meglévő ismereteit és népszerű nyílt forráskódú eszközök használatával üzembe helyezése és kezelése az Azure-ban tárolóalapú alkalmazásokat.

Az Azure tárolók használata az alkalmazásokban több módszert is biztosít.

-   **Az Azure Docker VM bővítmény**: lehetővé teszi a virtuális gép konfigurálását a Docker-eszközök a Docker-gazdagép-kiszolgálóként.

    >**Mikor érdemes használni**: Ha szeretné létrehozni a virtuális gép az alkalmazások konzisztens tárolópéldányok, vagy ha a használni kívánt [Docker Compose](https://docs.docker.com/compose/overview/).

    >**Első lépések**: [egy Docker-környezet létrehozása az Azure-ban a Docker VM bővítmény használatával](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Az Azure Container Service**: lehetővé teszi, hogy hozzon létre, konfigurálhatja és tárolóalapú alkalmazások futtatására előre konfigurált virtuálisgép-fürt kezelése. Container Service kapcsolatos további információkért lásd: [Azure Container Service bemutatása](../../container-service/container-service-intro.md).

    >**Mikor érdemes használni**: amikor éles használatra kész, méretezhető környezetek, amely további ütemezési és felügyeleti eszközöket biztosít, vagy ha telepít egy Docker Swarm-fürt létrehozásához szükséges.

    >**Első lépések**: [egy Tárolószolgáltatás-fürt üzembe helyezése](../../container-service/dcos-swarm/container-service-deployment.md).

-   **A docker Machine**: telepítése és a egy Docker-motor a virtuális gazdagépek kezelése a docker-machine paranccsal teszi lehetővé.

    >**Mikor érdemes használni**: kell tennie, gyorsan prototípus-alkalmazást hozzon létre egy Docker-gazdagép.

-   **Az App Service egyéni Docker-rendszerkép**: engedélyezi, hogy használja a Docker-tárolók egy tárolóregisztrációs adatbázisból vagy egy ügyfél-tárolót, a linuxon futó webalkalmazás központi telepítésekor.

    >**Mikor érdemes használni**: amikor üzembe helyezése Linuxon futó webalkalmazás egy Docker-rendszerképet.

    >**Első lépések**: [egyéni Docker-rendszerkép használata az App Service linuxon](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Hitelesítés

Nagyon fontos tudni, hogy nem csak az alkalmazásokat használó, hanem az erőforrásokhoz való jogosulatlan hozzáférés elkerülése érdekében. Az Azure az alkalmazás-ügyfelek hitelesítésére több módszert is biztosít.

-   **Az Azure Active Directory (Azure AD)**: A Microsoft több-bérlős, felhőalapú identitás- és hozzáférés felügyeleti szolgáltatás. Hozzáadhat egyszeri bejelentkezést (SSO) az alkalmazások és az Azure AD integrálásával. Könyvtár tulajdonságai elérheti, ha közvetlenül az Azure AD Graph API vagy a Microsoft Graph API használatával. Azure AD-támogatás a OAuth2.0 engedélyezési keretrendszer és az Open ID Connect integrálható a natív HTTP, illetve a REST-végpontok és a multiplatform az Azure AD-hitelesítési kódtárak használatával.

    >**Mikor érdemes használni**: szeretne biztosítani egy egyszeri Bejelentkezéses felhasználói élmény, Graph-alapú adatok, vagy tartományi felhasználók hitelesítésére.

    >**Első lépések**: további információkért tekintse meg a [Azure Active Directory fejlesztői útmutatója](../../active-directory/develop/active-directory-developers-guide.md).

-   **App Service-hitelesítés**: App Service-ben az alkalmazás futtatására választásakor érhet el az Azure AD, valamint a közösségi identitásszolgáltatókat beépített hitelesítés támogatása – beleértve a Facebook, Google, Microsoft vagy Twitter.

    >**Mikor érdemes használni**: Ha engedélyezi a hitelesítést az App Service-alkalmazások Azure AD-vel szeretne közösségi identitásszolgáltatókat, vagy mindkettőt.

    >**Első lépések**: App Service-ben a hitelesítéssel kapcsolatos további tudnivalókért lásd: [hitelesítése és engedélyezése Azure App Service-ben](../../app-service/app-service-authentication-overview.md).

Ajánlott biztonsági eljárások az Azure-ban kapcsolatos további információkért lásd: [Azure ajánlott biztonsági eljárások és minták](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Figyelés

Az alkalmazás mentése és az Azure-ban fut, a, kell figyelje a teljesítményét, a problémák esetén tekintse meg, és tekintse meg, ügyfeleink miként használják alkalmazását. Az Azure számos monitorozási lehetőségeket biztosít.

-   **A Visual Studio Application Insights**: az Azure-ban üzemeltetett bővíthető elemzési szolgáltatás, amely integrálható a Visual Studio segítségével az élő webalkalmazások figyeléséhez. Biztosít az adatok, amelyek kell folyamatosan javíthassa a teljesítményt és a használhatóságot alkalmazását, hogy azok már üzemel az Azure-ban, vagy nem.

    >**Első lépések**: kövesse a [Application Insights oktatóanyag](../../application-insights/app-insights-overview.md).

-   **Az Azure Monitor**: egy szolgáltatás, amely segít a megjelenítése, a lekérdezések, irányíthatja a, archív tárolási szint, és reagálhat rájuk a metrikák és az Azure-infrastruktúra és az erőforrások által létrehozott naplók. A figyelő az adatnézetek, tekintse meg az Azure Portalon, és a egy Azure-erőforrások figyelése egyetlen forrás biztosít.
 
    >**Első lépések**: [Ismerkedés az Azure Monitor szolgáltatással](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps-integráció

Virtuális gépek kiépítése, vagy a web apps, a folyamatos közzététel, az Azure együttműködik a népszerű DevOps-eszközök a legtöbb. A Jenkins, GitHub, Puppet, Chef, TeamCity, az Ansible, VSTS és más hasonló eszközökkel való támogatását használhatja az eszközöket, hogy már rendelkezik, és maximalizálja a már meglévő felhasználói élményt.

>**Kipróbálás:** [próbálja ki a DevOps-integrációkkal számos](https://azure.microsoft.com/try/devops/).

>**Első lépések**: kapcsolatban lásd az App Service-alkalmazások fejlesztési és üzemeltetési lehetőségek [folyamatos üzembe helyezés az Azure App Service-](../../app-service/app-service-continuous-deployment.md).


## <a name="azure-regions"></a>Azure-régiók

Az Azure világszerte számos régióban általánosan elérhető globális felhő platformon el. Egy szolgáltatás, alkalmazás vagy az Azure-beli virtuális gép üzembe helyezésekor, a rendszer felkéri válassza ki a régiót, amely egy adott adatközpont, ahol az alkalmazás fut, vagy az adatok tárolására. Ezek a régiók tartoznak az adott helyen, a közzétett a [Azure-régiók](https://azure.microsoft.com/regions/) lapot.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Válassza ki a legjobb régióban, az alkalmazások és adatok

Az Azure használatának előnyei egyik helyezheti üzembe az alkalmazásokat a világ különböző pontjain. Az Ön által választott régióban hatással lehet az alkalmazás teljesítményét. Ha például célszerűbb válasszon, amely az ügyfelek hálózati kéréseket a késés csökkentése érdekében a legtöbb közelebb régiót. Érdemes azt is, válassza ki a régiót az egyes országokban az alkalmazás terjesztése a jogi követelmények teljesítéséhez. Mindig az alkalmazások adatainak tárolására, ugyanabban az adatközpontban vagy adatközpontba, közel az adatközpont, amelyen az alkalmazás a lehető legjobb.

### <a name="multi-region-apps"></a>Többrégiós alkalmazás

Noha valószínűtlen, akkor sem lehetetlen teljes adatközpontot a egy eseményt, például természeti katasztrófa vagy Internet-hiba miatt offline állapotba. Tanácsos a létfontosságú üzleti alkalmazások üzemeltetéséhez egynél több adatközpont maximális rendelkezésre állást biztosít. Több régióban használatával is csökkenthető a globális érzékelhető késést és a rugalmasság érdekében további lehetőségek alkalmazások frissítésekor.

Egyes szolgáltatások, virtuális gép és az App Services, például a [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) több régió támogatása a magas rendelkezésre állású vállalati alkalmazások támogatásához régiók közötti feladatátvétel engedélyezéséhez. Egy vonatkozó példáért lásd: [Azure-referenciaarchitektúra: webalkalmazás futtatása több régióban](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Mikor érdemes használni**: Ha rendelkezik a vállalati és a magas rendelkezésre állású alkalmazások, amelyek feladatátvétel és a replikáció.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hogyan kezelhetem a saját és a projektjeimet?

Az Azure-funkciókat hozhat létre és kezelheti az Azure-erőforrások, alkalmazások és projektek széles skáláját biztosítja – mind programozott módon, és a a [az Azure portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Parancssori felületen és a PowerShell használatával

Az Azure az alkalmazások és szolgáltatások kezelése parancssori Bash, terminál, a parancssort vagy a választott parancssori eszköz használatával kétféle módszert biztosít. Általában végrehajtani a feladatot a parancssorból, mint az Azure portal – például a létrehozása és konfigurálása a virtuális gépek, virtuális hálózatok, webalkalmazások és egyéb szolgáltatásokat.

-   [Az Azure parancssori felület (CLI)](../../xplat-cli-install.md): lehetővé teszi a csatlakozás az Azure-előfizetéshez és a program különböző feladatokat az Azure-erőforrásokon a parancssorból.

-   [Az Azure PowerShell](../../powershell-install-configure.md): biztosít modulok a parancsmagok, amelyek lehetővé teszik, hogy az Azure-erőforrások kezelése a Windows PowerShell használatával.

### <a name="azure-portal"></a>Azure Portal

Az Azure Portalon egy webalapú alkalmazás, amely segítségével létrehozása, kezelése és Azure-erőforrások és szolgáltatások eltávolítása. Az Azure Portalon a következő helyen található <https://portal.azure.com>. Ez magában foglalja egy testreszabható irányítópulton, az Azure-erőforrások kezelésére szolgáló eszközök és előfizetés-beállításnak, és számlázási adatokat. További információkért lásd: a [az Azure portal áttekintése](../../azure-portal-overview.md).

### <a name="rest-apis"></a>REST API-k

Azure REST API-k, amelyek támogatják az Azure portal felhasználói felületén egy készletét épül. A legtöbb REST API-k lehetővé teszi, hogy programozott módon üzembe helyezése és kezelése az Azure-erőforrások és alkalmazások az Internet használatára képes bármely eszközről is támogatottak. A teljes REST API-dokumentáció, lásd: a [Azure REST SDK-referenciában](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>API-k

REST API-k, valamint számos Azure-szolgáltatások is lehetővé teszik programozott módon kezelheti erőforrásokat az alkalmazások platformspecifikus Azure SDK-k használatával, beleértve az SDK-k a következő fejlesztői platformokhoz:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

Szolgáltatásokkal – például [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) és [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) ügyféloldali SDK-k lehetővé teszi, hogy adja meg a webes és mobil ügyfélalkalmazás-szolgáltatások eléréséhez.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Az alkalmazás futtatásához az Azure-ban valószínűleg magában foglalja a több Azure-szolgáltatások mindegyike hajtsa végre az azonos életciklus és a egy logikai egységként tekinthető használatának. Webes alkalmazás használhat például Web Apps, az SQL Database, Storage, Azure Redis Cache és Azure Content Delivery Network szolgáltatások. [Az Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) lehetővé teszi, hogy az erőforrásokkal való munka megkezdése egy csoportként az alkalmazásban. Központi telepítése, frissítése vagy törlése az összes erőforrását egyetlen, koordinált műveletben.

Logikailag csoportosítás, és a kapcsolódó erőforrások kezelése, mellett az Azure Resource Manager üzembe helyezési lehetőségeket, amelyekkel testre szabhatja a központi telepítési és a kapcsolódó erőforrások tartalmazza. Ha például Resource Manager használatával telepítheti és egy alkalmazást, amely több virtuális gépet egy terheléselosztó és az Azure SQL database egyetlen egységként áll.

Ezeket az üzemelő példányokat az Azure Resource Manager-sablon, amely egy JSON-formátumú dokumentum fejleszt. Sablonok lehetővé teszik a központi telepítés alkalmazásokat meghatározni és kezelni a szkriptek helyett deklaratív sablonok segítségével. A sablonok különböző környezetekben, például tesztelési, átmeneti és éles környezetben is képes működni. Például sablonok használatával adhat hozzá egy gombot, amely üzembe helyezi a kódot a tárház számára egy Azure-szolgáltatások egyetlen kattintással GitHub-tárházba.

>**Mikor érdemes használni**: használja a Resource Manager-sablonok számára, ha szeretne egy sablonalapú üzembe helyezése az alkalmazáshoz, amely a REST API-k, az Azure CLI-vel és az Azure PowerShell használatával programozott módon kezelheti.

>**Első lépések**: első lépések a sablonok használatával, lásd: [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Understanding fiókok, előfizetések és számlázás

Fejlesztők számára nyújtanak hogy például megvásárolná be a kódot, majd próbálja meg a lehető tétele az alkalmazások futtatása a gyors kezdéshez. Természetesen szeretnénk javasoljuk, hogy az Azure-ban könnyedén, a lehető használatának megkezdéséhez. Könnyen kezelhető, az Azure-ajánlatok, hogy egy [az ingyenes próbaidőszak](https://azure.microsoft.com/free/). Egyes szolgáltatások is, például egy "Ingyenes kipróbálás" funkciót, hogy [Azure App Service](https://tryappservice.azure.com/), amely nem igényel, hogy akkor hozzon létre egy fiókot. Szórakoztató, hogy megismerkedjen a kódolási és az Azure-ba, az alkalmazás üzembe helyezése fontos is eltarthat egy ideig a felhasználói fiókok, előfizetések és számlázási szempontból az Azure működésének megismerése.

### <a name="what-is-an-azure-account"></a>Mi az Azure-fiókkal?

Tudják létrehozása és használata Azure-előfizetéssel, Azure-fiókkal kell rendelkeznie. Az Azure-fiók az identitás egyszerűen az Azure ad-ben vagy egy címtárban, például egy munkahelyi vagy iskolai szervezet, amely megbízható Azure AD által. Ha nincs ilyen egy szervezethez tartozik, a Microsoft Account, amely az Azure AD által megbízhatónak tartott bármikor létrehozhat egy előfizetést. A helyszíni Windows Server Active Directory integrálása az Azure ad-vel kapcsolatos további információkért lásd: [a helyszíni identitások integrálása az Azure Active Directory](../../active-directory/active-directory-aadconnect.md).

Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. További tudnivalókért lásd: [kapcsolódnak az Azure-előfizetések az Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Mellett definiálása az egyes Azure-fiók identitásokat, más néven *felhasználók*, azt is megadhatja, *csoportok* az Azure ad-ben. Felhasználói csoportok létrehozásához megfelelő módja egy előfizetésben lévő erőforrásokhoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlés (RBAC) használatával. Csoportok létrehozásával kapcsolatban lásd: [hozzon létre egy csoportot az Azure Active Directory előzetes kiadásában](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Ön is létrehozhatja és kezelheti a csoportok szerint [PowerShell-lel](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Saját előfizetések kezelése

Egy előfizetés az Azure-szolgáltatások egy logikai egységet, amely egy Azure-fiókra van csatolva. Minden társított fiók rendelkezik egy szerepkörhöz az előfizetéshez. Azure-szolgáltatások használati díjának felszámolása előfizetésenként alapon történik. A rendelkezésre álló előfizetési ajánlatok típus szerint listáját lásd: [a Microsoft Azure-ajánlat részletei](https://azure.microsoft.com/support/legal/offer-details/).

#### <a name="administrator-roles"></a>Rendszergazdai szerepkörök

Azure-előfizetéssel rendelkezik, több fiók rendszergazdai szerepkör, amely bármikor hozzárendelhet.

-   **A fiók rendszergazdája**: ezt a szerepkört az előfizetés feletti teljes körű vezérléssel rendelkezik, és a fiókot, amely felelős a számlázásért.

-   **Szolgáltatás-rendszergazda**: Ez a szerepkör rendelkezik összes szolgáltatását szabályozhatja az előfizetésben. Alapértelmezés szerint ez a fiók rendszergazdaként ugyanazzal a fiókkal.

-   **Társ-rendszergazdaként**: Ez a szerepkör, szolgáltatás-rendszergazda azonos hozzáféréssel rendelkezik, azzal a különbséggel, hogy azt a társítást az előfizetés nem módosítható egy Azure-címtárhoz.

További tudnivalók a rendszergazdai szerepkörökről, lásd: [hozzáadása vagy módosítása az Azure-rendszergazdai szerepkörök](../../billing/billing-add-change-azure-subscription-administrator.md#add-an-admin-for-a-subscription).

#### <a name="resource-groups"></a>Erőforráscsoportok

Amikor üzembe helyezi az új Azure-szolgáltatásokat, hogy egy adott előfizetésben megteheti. Az egyes Azure-szolgáltatások, más néven erőforrásokat, az erőforráscsoport környezetében jönnek létre. Erőforráscsoportok megkönnyítik az alkalmazás-erőforrások telepítéséhez és kezeléséhez. Egy erőforráscsoportot kell tartalmaznia, amelyet szeretne dolgozni egy egységként az alkalmazás összes erőforrását. Továbbléphet az erőforrások erőforráscsoportok között, és akár eltérő előfizetésekben. Erőforrások áthelyezése kapcsolatos további információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).

Az Azure erőforrás-kezelő használatával megjelenítheti az erőforrásokat, az előfizetésben már létrehozott egy remek eszköz. További tudnivalókért lásd: [az erőforrások megtekintése és módosítása az Azure Resource Explorer](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Erőforrásokhoz való hozzáférés engedélyezése

Ha engedélyezi a hozzáférést az Azure-erőforrásokkal, mindig ajánlott az egy adott feladat végrehajtásához szükséges minimális jogosultságokkal rendelkező felhasználók számára.

-   **Szerepköralapú hozzáférés-vezérlés (RBAC)**: az Azure-ban, is hozzáférést biztosít a felhasználói fiókok (egyszerű) a megadott hatókörben: előfizetés, erőforráscsoport vagy egyes erőforrásokat. Az RBAC lehetővé teszi az erőforráscsoport üzembe helyezzen egy erőforráscsoportban, és engedélyek egy adott felhasználó vagy csoport számára. Azt is lehetővé teszik csak a célként megadott erőforráscsoportja tartozó erőforrásokhoz való hozzáférés korlátozásához. A hozzáférést egy egyetlen erőforrást, például a virtuális gép vagy a virtuális hálózat. Hozzáférés biztosításához szerepkör a felhasználó, csoport vagy egyszerű szolgáltatás hozzárendelése. Számos előre definiált szerepkörök állnak rendelkezésre, és azt is megadhatja a saját egyéni szerepkörökkel.

    >**Mikor érdemes használni**: amikor a felhasználók és csoportok részletes hozzáférés-vezérlést kell.

    >**Első lépések**: további információkért lásd: [hozzáférés-kezelés az Azure portal – első lépések](../../role-based-access-control/overview.md).

-   **Szolgáltatás egyszerű objektumok**: hozzáférés biztosítása mellett az egyszerű felhasználói névnek és a csoportok, is hozzáférést biztosít az ugyanazon a szolgáltatásnév.

    > **Mikor érdemes használni**: Ha programozott módon az Azure-erőforrások kezelése, vagy alkalmazásokhoz hozzáférést biztosít. További információkért lásd: [hozzon létre egy Active Directory-alkalmazás és egyszerű szolgáltatás](../../resource-group-create-service-principal-portal.md).

#### <a name="tags"></a>Címkék

Az Azure Resource Manager lehetővé teszi egyéni címkék hozzárendelése az egyes erőforrásokhoz. Címkék olyan kulcs-érték párok, akkor lehet hasznos, amikor szüksége van a számlázási vagy figyelési erőforrások rendezéséhez. A címkék nyomon követésére erőforrások több erőforráscsoporthoz lehetőséget biztosíthat. A címkék a portálon, az Azure Resource Manager-sablon, vagy programozott módon, a REST API-t, az Azure CLI vagy a PowerShell használatával rendelhet hozzá. Több címkét rendelhet az egyes erőforrások. További tudnivalókért lásd: [az Azure-erőforrások rendszerezése címkék használatával](../../resource-group-using-tags.md).

### <a name="billing"></a>Számlázás

Az áthelyezés a helyszíni számítási felhőben üzemeltetett szolgáltatások, a nyomon követése, és a szolgáltatás használat és a kapcsolódó költségek becslése is jelentős problémák. Fontos tudni becsült költség új erőforrások Futtatás havonta. Is vissza kell tudni projektre, hogyan néz ki a számlázás az adott hónapban az aktuális költségeket alapján.

#### <a name="get-resource-usage-data"></a>Erőforrás-használati adatok lekérése

Az Azure számlázási REST API-erőforrás-használat és az Azure-előfizetésekhez tartozó metaadat-információkat biztosít. Számlázási API-k lehetővé teszik, jobban előre jelezni, és Azure-költségek kezeléséhez. Nyomon követheti és óránkénti növekményekben elemzi, költségkeret riasztásokat hozhat létre, és előre jelezni a jövőbeli számlázása a jelenlegi használati trendek alapján.

>**Első lépések**: a számlázási API-k használatával kapcsolatos további tudnivalókért lásd: [Azure számlázási használat és RateCard API-k áttekintése](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Előre jelezni a jövőbeli költségeit

Alapján előre kihívást jelent, bár az Azure rendelkezik olyan [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) amikor költségeket megbecsülheti használható üzembe az erőforrásokat. A számlázás panel a portálon és a számlázási REST API-k segítségével is megbecsülheti a jövőbeli költségeit, aktuális használat alapján.

>**Első lépések**: lásd: [Azure számlázási használat és RateCard API-k áttekintése](../../billing-usage-rate-card-overview.md).

#### <a name="set-up-billing-alerts"></a>Számlázási értesítések beállítása

Miután telepítette az alkalmazáshoz vagy megoldáshoz az Azure-ban, riasztásokat küld, e-mail küldése, ha a riasztásban szereplő definiált a költségkeretek módszerrel érdemes hozhat létre.

>**Első lépések**: további információkért lásd: [elszámolási értesítések a Microsoft Azure-előfizetések beállítása](../../billing-set-up-alerts.md).

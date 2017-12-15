---
title: "Get lépésekről szóló útmutatót a fejlesztők számára az Azure-on |} Microsoft Docs"
description: "Ez a témakör alapvető információkat szeretné használatának megkezdésében a Microsoft Azure platform fejlesztési igényeiknek a fejlesztők számára."
services: 
cloud: 
documentationcenter: 
author: ggailey777
manager: erikre
ms.assetid: 
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: b54b806aad1e15702d2167dcf2870ba19c4708df
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="get-started-guide-for-azure-developers"></a>Első lépésekhez készült útmutató Azure-fejlesztőknek

## <a name="what-is-azure"></a>Mi az Azure?

Azure a is a meglévő alkalmazásokat egyszerűsítésére új alkalmazások fejlesztése és akkor javíthatja a helyszíni alkalmazások teljes felhőalapú platformot. Azure integrálja a felhőszolgáltatások fejlesztéséhez, tesztelése, telepítéséhez, és az alkalmazások kezeléséhez szükséges – közben a hatékonyság a felhőalapú informatika.

Az alkalmazások az Azure-ban üzemeltet, kezdje kis lépésekkel, és könnyedén méretezhető az alkalmazást, a kereslet növekedésének megfelelően. Az Azure is kínál a megbízhatóságáról, magas rendelkezésre állású alkalmazások, még akkor is beleértve különböző régiókban közötti feladatátvétel van szükség. A [Azure-portálon](https://portal.azure.com) lehetővé teszi, hogy könnyedén kezelhető az összes Azure-szolgáltatásokhoz. A szolgáltatások programozott módon is kezelhetők szolgáltatásspecifikus API-k és sablonok használatával.

**Ez célközönsége**: Ez az útmutató bevezetést alkalmazásfejlesztők Azure platformon is. Útmutatás és kell elindítania az új alkalmazások az Azure-ban vagy az Azure-bA a meglévő alkalmazások áttelepítése irányát biztosít.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

Az összes szolgáltatás, amely az Azure kínál azok terhet mérje fel, a megoldás architektúrája támogatásához szükséges szolgáltatásokat. Ez a szakasz kiemeli a fejlesztők a gyakran használt Azure-szolgáltatásokhoz. Az összes Azure-szolgáltatások listáját lásd: a [Azure dokumentációja](../../index.md).

Először meg kell határoznia az alkalmazás az Azure üzemeltetése. A teljes infrastruktúra kezelése (VM) virtuális gépként kell. Használhatja a platform felügyeleti eszközökkel Azure által biztosított? Lehet, hogy egy kiszolgáló nélküli keretrendszert, csak a gazdagép programkód van szüksége?

Az alkalmazás számos lehetőséget biztosít Azure felhőalapú tárolást kell. Kihasználhatja az Azure vállalati hitelesítési. A rendszer eszközeivel a felhőalapú fejlesztési és a figyelés és a legtöbb üzemeltetési szolgáltatásokat kínál a DevOps integráció.

Most egyes, javasoljuk, hogy vizsgálja meg az alkalmazások adott szolgáltatások vizsgáljuk meg.

### <a name="application-hosting"></a>Alkalmazásszolgáltatás

Azure biztosít több felhőalapú számítási ajánlatokat az alkalmazás futtatásához, így nem kell foglalkoznia az infrastruktúra részleteit. Könnyedén növelheti vagy horizontális felskálázás az erőforrások, az alkalmazás használatának növekedésével.

Az Azure az alkalmazások fejlesztése és az üzemeltetési igények támogató szolgáltatásokat kínál. Azure infrastruktúra,--szolgáltatás (IaaS) az alkalmazás-gazdaszolgáltatás teljes szabályozható biztosít. Azure platform,--szolgáltatás (PaaS) ajánlatok adja meg a teljes körűen felügyelt, az alkalmazások bekapcsolására szükséges szolgáltatásokat. Nincs még akkor is igaz kiszolgáló nélküli üzemeltető az Azure-ban ahol csak annyit kell tennie a kód írása.

![Azure-alkalmazást üzemeltető beállítások](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Ha azt szeretné, hogy a leggyorsabb elérési útját a webes projektek közzététele, fontolja meg az Azure App Service szolgáltatásban. App Service megkönnyíti, hogy a mobil ügyfelek támogatása, és könnyen felhasznált REST API-k közzététele a webalkalmazások kiterjeszteni. Ezen a platformon közösségi szolgáltatók közül, automatikus skálázás forgalom-alapú üzemi, és folyamatos és tároló-alapú telepítések használatával biztosítja a hitelesítést.

Webalkalmazások, mobilalkalmazásokból és API-alkalmazásokat hozhat létre.

Három alkalmazástípus megosztani az App Service-futtatókörnyezet, mert egy webhely üzemeltetéséhez, mobilos ügyfeleket támogatja, és teszi közzé az API-kat, az Azure-ban minden ugyanabban a projektben vagy megoldás a. App Service kapcsolatos további információkért lásd: [Mi az Azure Web Apps](../../app-service/app-service-web-overview.md).

App Service úgy tervezték, a DevOps szem előtt. Támogatja a különböző eszközök közzétételi és folyamatos integráció környezetekhez, például a GitHub webhook, Jenkins, a Visual Studio Team Services, TeamCity és mások számára.

A meglévő alkalmazások App Service segítségével telepíthet át a [online áttelepítési eszköz](https://www.migratetoazure.net/).

>**Mikor érdemes használni**: használja az App Service amikor, amelybe migrálna meglévő webes alkalmazások az Azure-ba, ha a webalkalmazások egy teljes körűen felügyelt gazdaplatform szükséges. App Service is használható, ha mobil ügyfelek támogatásához, vagy teszi közzé a REST API-k alkalmazása van szüksége.

>**Első lépések**: az App Service segítségével egyszerűen létrehozása és telepítése az első [webalkalmazás](../../app-service/app-service-web-get-started-dotnet.md), [mobilalkalmazás](../../app-service-mobile/app-service-mobile-ios-get-started.md), vagy [API-alkalmazás](../../app-service/app-service-web-tutorial-rest-api.md).

>**Próbálja ki most**: App Service lehetővé teszi egy rövid élettartamú alkalmazásnak, hogy a platform iratkozzon fel az Azure-fiók nélkül próbálja telepíteni. Próbálja meg a platform és [Azure App Service-alkalmazás létrehozása](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

Egy infrastruktúra--a-szolgáltatásként (IaaS) szolgáltatói Azure lehetővé teszi, hogy telepíteni, vagy az alkalmazás Windows vagy Linux rendszerű virtuális gépek áttelepítéséhez. Azure-beli virtuális hálózatra, és Azure virtuális gépek támogatja a központi telepítés Windows vagy Linux rendszerű virtuális gépek Azure-bA. Virtuális gépek befolyásolni teljes a gép konfigurációját. Virtuális gépek használatakor Ön felelős az összes kiszolgáló telepítéshez, konfiguráláshoz, karbantartási és operációs rendszer szoftverjavítások.

Vezérlő, amely a virtuális gépeken, mivel számos különböző kiszolgáló-munkaterhelések futtathatja, amelyek nem felelnek meg Azure egy PaaS-modellbe. Az ilyen terhelések közé tartozik az adatbázis-kiszolgálók, a Windows Server Active Directory és a Microsoft SharePoint. További információ a dokumentációban a virtuális gépek vagy [Linux](/azure/virtual-machines/linux/) vagy [Windows](/azure/virtual-machines/windows/).

>**Mikor érdemes használni**: használata virtuális gépek Ha azt szeretné, teljes ellenőrzés vagy a helyszíni alkalmazások és szolgáltatások áttelepítése az Azure-bA anélkül, hogy a módosítások az alkalmazás-infrastruktúra keresztül.

>**Első lépések**: hozzon létre egy [Linux virtuális gép](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) vagy [Windows virtuális gép](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) Azure-portálról.

#### <a name="azure-functions-serverless"></a>Az Azure Functions (kiszolgáló nélküli)

Nem pedig a kimenő felépítését és kezelését az Ön kódjának futtatásához egy egész alkalmazással vagy az infrastruktúra aggódni. Mi történik, ha Ön volt ebben az esetben a kód írása és azt válaszként események vagy ütemezés szerint?  [Az Azure Functions](../../azure-functions/functions-overview.md) van a "kiszolgáló"nélküli-stílus ajánlat, amely lehetővé teszi, hogy csak a kód írását, van szüksége. Azokat a funkciókat kód aktiválja a végrehajtást, vagy ütemezés szerint HTTP-kérelmekre, webhookokkal, felhőalapú szolgáltatás eseményeit. A fejlesztési nyelven szerkesztőprogramban, például C is kódaláírással\#, F\#, Node.js, Python vagy a PHP. A fogyasztás alapján történő számlázáshoz, csak fizetnie végrehajtja a kódot, és Azure méretezi igény szerint.

>**Mikor érdemes használni**: használja az Azure Functions ha kódot, amely elindítja a sématelepítési más Azure-szolgáltatásokkal, webes események, vagy ütemezés szerint. Funkciókat is használhatja, ha már nincs szükség a terhelést a teljes üzemeltetett projekt, vagy ha szeretné az idő, amennyit a kódja fut. díj ellenében. További tudnivalókért lásd: [Azure Functions áttekintése](../../azure-functions/functions-overview.md).

>**Első lépések**: a funkciók gyors üzembe helyezési oktatóanyag [hozzon létre az első függvényét](../../azure-functions/functions-create-first-azure-function.md) a portálról.

>**Próbálja ki most**: az Azure Functions lehetővé teszi, hogy Ön kódjának futtatásához anélkül, hogy az Azure-fiókot regisztrálni. Próbálja ki most, és [az első Azure-függvény létrehozása](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Az Azure Service Fabric egy elosztottrendszer platform, amely segítségével egyszerűen felépítéséhez, csomag, telepítéséhez és felügyeletéhez a méretezhető és megbízható mikroszolgáltatások létrehozására. Kiépítés, üzembe helyezés, figyelés, frissítése/javítását is biztosít átfogó alkalmazás-kezelési képességei, és törlésekor az alkalmazások telepítését. Alkalmazások, egy megosztott készletéhez gépeken futtatja, amely kisebb és méretezhető, több száz vagy ezer gépek, igény szerint.

A Service Fabric WebAPI az Open Web Interface .NET (OWIN) és az ASP.NET Core támogatja. Szolgáltatások létrehozása Linux a .NET Core és a Java SDK-k kínál. A Service Fabric kapcsolatos további tudnivalókért tekintse meg a [Service Fabric képzési](https://azure.microsoft.com/documentation/learning-paths/service-fabric/).

>**Mikor érdemes használni:** Service Fabric akkor hasznos, amikor az alkalmazás létrehozása vagy egy meglévő alkalmazást, hogy az egy mikroszolgáltatási architektúra újraírását. Felhasználhatja a Service Fabric további szabályozhatják, vagy közvetlen hozzáférést, az alkalmazás mögötti infrastruktúra szükséges.

>**Első lépések:** [az első Azure Service Fabric-alkalmazás létrehozása](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Az alkalmazások az Azure-szolgáltatásokkal javíthatja.

Mellett alkalmazásszolgáltatás, az Azure szolgáltatásra vonatkozó ajánlattal kapcsolatban, amelyek a funkciót, fejlesztési és az alkalmazások, mind a felhőben és helyszíni karbantartási fokozott biztosít.

#### <a name="hosted-storage-and-data-access"></a>Központi tároló és az adatok hozzáférését

A legtöbb alkalmazást úgy kell tárolnia adatok, függetlenül attól, milyen úgy dönt, hogy az alkalmazás az Azure-ban futtatni, fontolja meg egy vagy több, a következő tárolási és adatokat tartalmazza.

-   **Az Azure Cosmos DB**: egy globálisan elosztott, több modellre dokumentumadatbázis-szolgáltatás, amely lehetővé teszi, hogy rugalmasan méretezhető átviteli sebesség és tárterület tetszőleges számú egy átfogó SLA földrajzi régiók között. 
    >**Mikor érdemes használni:** Ha az alkalmazás a dokumentum, táblázat vagy graph-adatbázisok, beleértve a MongoDB-adatbázisok esetén több jól meghatározott konzisztencia modellekkel. 

    >**Első lépések**: [egy Azure Cosmos DB web app Build](../../cosmos-db/create-sql-api-dotnet.md). Ha Ön a MongoDB fejlesztő, lásd: [Azure Cosmos DB a MongoDB webalkalmazás összeállítása](../../cosmos-db/create-mongodb-dotnet.md).

-   **Az Azure Storage**: BLOB, üzenetsorok, fájlok és más típusú nonrelational adatok tartós, magas rendelkezésre állású tároló kínál. Tárolási a tárolási alapokat nyújt a virtuális gépek.

    >**Mikor érdemes használni**: Ha az alkalmazás eltárolja nonrelational, például a kulcs-érték pár (táblák), blobok, megosztások, valamint a küldött üzenetek (várólisták).

    >**Első lépések**: ezek a típusok tárolási közül választhat: [blobok](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [táblák](../../cosmos-db/table-storage-how-to-use-dotnet.md), [várólisták](../../storage/queues/storage-dotnet-how-to-use-queues.md), vagy [fájlok](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Az Azure SQL Database**: a Microsoft SQL Server adatbázismotor felhőalapú relációs adatok tárolásához Azure-alapú verzióját. SQL-adatbázis kiszámítható teljesítmény, méretezhetőség nincs állásidő, az üzletmenet folytonosságának és az adatvédelem biztosítja.

    >**Mikor érdemes használni**: Ha az alkalmazás által igényelt adattárolás hivatkozási integritási, a tranzakciós támogatása és a támogatás a TSQL-lekérdezések.

    >**Első lépések**: [percben SQL-adatbázis létrehozása az Azure portál használatával](../../sql-database/sql-database-get-started.md).


Használhat [Azure Data Factory](../../data-factory/introduction.md) helyezhető át a meglévő helyszíni adatok Azure-bA. Ha nem szeretne adatokat a felhőbe, [hibrid kapcsolatok](../../biztalk-services/integration-hybrid-connection-overview.md) csatlakozás BizTalk szolgáltatás lehetővé teszi az App Service alkalmazásnak, hogy a helyszíni erőforrások üzemeltet. Keresztül is csatlakozhat az Azure data- és tárolási szolgáltatások a helyszíni alkalmazásokból.

#### <a name="docker-support"></a>Docker-támogatás

Docker-tároló, egy formája, amelyet az operációs rendszer virtualizálási lehetővé teszik, hogy az alkalmazások központi telepítése hatékonyabb és kiszámítható módon. A tárolóalapú alkalmazás működik, éles környezetben eltérő módon történik a fejlesztési és tesztelési rendszereit. Tárolók szabványos Docker-eszközök segítségével kezelheti. Használhatja a meglévő ismeretei és népszerű nyílt forráskódú eszközök telepítése és kezelése az Azure-tároló-alapú alkalmazások.

Azure többféleképpen is tárolók használata az alkalmazásokban.

-   **Az Azure Docker Virtuálisgép-bővítmény**: lehetővé teszi, hogy a virtuális gép konfigurálása Docker eszközök egy Docker-állomás nevében járhasson el.

    >**Mikor érdemes használni**: Ha kíván létrehozni a virtuális gép az alkalmazások konzisztens tároló telepítések, vagy ha a használni kívánt [Docker Compose](https://docs.docker.com/compose/overview/).

    >**Első lépések**: [hozzon létre egy Docker-környezetet az Azure-ban a Docker Virtuálisgép-bővítmény használatával](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Az Azure Tárolószolgáltatás**: lehetővé teszi, hogy létrehozására, konfigurálására és egy fürt indexelése alkalmazások futtatásához az előre konfigurált virtuális gépek kezeléséhez. Tárolószolgáltatás kapcsolatos további információkért lásd: [Azure Tárolószolgáltatás bemutatása](../../container-service/container-service-intro.md).

    >**Mikor érdemes használni**: kell tennie, éles használatra kész, méretezhető környezetben, amely további ütemezés és a felügyeleti eszközöket biztosít, vagy ha telepít egy Docker Swarm-fürt létrehozása.

    >**Első lépések**: [Tárolószolgáltatás-fürt üzembe](../../container-service/dcos-swarm/container-service-deployment.md).

-   **Docker gép**: lehetővé teszi, hogy telepítse és egy Docker-motorhoz virtuális gazdagépek kezelése a docker-gép parancsok használatával.

    >**Mikor érdemes használni**: amikor kell gyorsan prototípus alkalmazást hozzon létre egy Docker-állomáshoz.

-   **Az App Service egyéni Docker-lemezkép**: lehetővé teszi, hogy egy tároló beállításjegyzékből Docker-tároló vagy egy felhasználói tároló Linux webes alkalmazás központi telepítésekor.

    >**Mikor érdemes használni**: Docker-lemezképhez Linux webes alkalmazás telepítésekor.

    >**Első lépések**: [Docker egyéni lemezképet használ az App Service Linux](../../app-service/containers/quickstart-custom-docker-image.md).

### <a name="authentication"></a>Authentication

Rendkívül fontos tudni, hogy nem csak az alkalmazásokat használó, hanem a jogosulatlan hozzáférés elkerülése érdekében az erőforrások. Azure többféleképpen is, hogy az alkalmazás ügyfelek hitelesítése.

-   **Azure Active Directory (Azure AD)**: A Microsoft több-bérlős, felhőalapú identitás- és hozzáférés felügyeleti szolgáltatás. Az alkalmazások egyszeri bejelentkezést (SSO) adhat hozzá integrálja az Azure AD-val. Directory tulajdonságok közvetlenül az Azure AD Graph API vagy a Microsoft Graph API segítségével végezheti el. A OAuth2.0 hitelesítési keretrendszer és az Open ID Connect Azure AD támogatása natív HTTP/REST-végpontok és a multiplatform az Azure AD hitelesítési könyvtárak segítségével integrálható.

    >**Mikor érdemes használni**: szeretne biztosítani egy egyszeri Bejelentkezéses felhasználói élményt, amikor Graph-alapú adatokkal dolgozni, vagy a tartományi felhasználók hitelesítésére.

    >**Első lépések**: további információért tekintse meg a [Azure Active Directory fejlesztői útmutatója](../../active-directory/develop/active-directory-developers-guide.md).

-   **App Service hitelesítés**: Ha úgy dönt, hogy az alkalmazás futtatásához az App Service, akkor is beépített hitelesítési támogatás kérése az Azure AD, valamint közösségi identitás-szolgáltatóktól – beleértve a Facebook, a Google, a Microsoft és a Twitter.

    >**Mikor érdemes használni**: Ha engedélyezi a hitelesítést egy App Service-alkalmazást az Azure AD használatával szeretne közösségi Identitásszolgáltatók, vagy mindkettőt.

    >**Első lépések**: az App Service hitelesítés kapcsolatos további információkért lásd: [hitelesítési és engedélyezési az Azure App Service](../../app-service/app-service-authentication-overview.md).

Ajánlott biztonsági eljárások az Azure-ban kapcsolatos további információkért lásd: [Azure biztonsági ajánlott eljárásairól és mintáiról](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Figyelés

Az alkalmazások és az Azure-ban futó meg kell tennie figyelemmel kísérni a teljesítményét, figyelje kapcsolatos problémák, és tekintse meg a felhasználók hogyan használják az alkalmazást. Azure számos felügyeleti lehetőséget biztosít.

-   **A Visual Studio Application Insights**: bővíthető analytics egy Azure által üzemeltetett szolgáltatás, amely integrálható a Visual Studio, a működés közbeni webalkalmazások figyeléséhez. Ez lehetővé teszi az adatok kell folyamatosan fejleszteni a teljesítményük és használhatóságuk az alkalmazásokat, hogy az Azure által üzemeltetése, vagy nem.

    >**Első lépések**: kövesse a [Application Insights oktatóanyag](../../application-insights/app-insights-overview.md).

-   **Az Azure figyelő**: egy szolgáltatás, amellyel megjelenítése, lekérdezésére, irányíthatja, archiválására és a metrikák és az Azure-infrastruktúra és az erőforrások által létrehozott naplók intézkedjen. A figyelő biztosítja az adatnézetek, tekintse meg az Azure portálon, és az Azure-erőforrások figyelése egyetlen helyről.
 
    >**Első lépések**: [Ismerkedés az Azure-figyelő](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps-integráció

Virtuális gépek kiépítése, vagy a folyamatos integrációt webalkalmazások közzétételéhez, Azure integrálható a népszerű DevOps-eszközök többsége. Támogatja a Jenkins, GitHub, Puppet, Chef, TeamCity, Ansible, VSTS és más hasonló eszközök az eszközöket, hogy már rendelkezik, és maximalizálhatja a meglévő felhasználói élmény is dolgozhat.

>**Próbálja ki most:** [próbálja ki a DevOps Integrációk számos](https://azure.microsoft.com/try/devops/).

>**Első lépések**: DevOps beállításait egy App Service-alkalmazást, olvassa el [folyamatos üzembe helyezés az Azure App Service](../../app-service/app-service-continuous-deployment.md).


## <a name="azure-regions"></a>Azure-régiók

Azure a világszerte több régióban általánosan elérhető globális felhőalapú platformot. Ha egy szolgáltatás, alkalmazás vagy egy Azure-ban, meg kell adnia válasszon ki egy régiót, amely egy adott adatközpont, ahol az alkalmazás fut, vagy az adatok tárolására. Ezek a területek adott helyeken, amelyek közzé felel meg a [Azure-régiók](https://azure.microsoft.com/regions/) lap.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Válassza ki a legjobb régiót az alkalmazás és adatok

Az Azure használatának előnyei egyike, hogy az alkalmazások különböző adatközpontokban a világ minden táján telepítheti. A választott régió hatással lehet az alkalmazás teljesítményét. Például érdemes választania egy régiót, amely a hálózati kérelmek késés csökkentése érdekében az ügyfelek többsége közelebb van. Érdemes azt is, válassza ki a régiót, az alkalmazás egyes országokban terjesztéséhez a jogi követelmények teljesítéséhez. Azt a rendszer mindig az alkalmazás adatainak tárolásához ugyanabban az adatközpontban, vagy az adatközpontban, közel az adatközpont, amelyen az alkalmazás a lehető legjobb.

### <a name="multi-region-apps"></a>Több területi alkalmazások

Bár nem valószínű, továbbra is nem kizárt, hogy az egész adatközpont például természeti katasztrófa vagy Internet-hiba esemény miatt offline állapotba. Fontos üzleti alkalmazások üzemeltetését egynél több adatközpontban maximális rendelkezésre állás biztosításához célszerű. Használatával több régióba is globális felhasználók késés csökkentésére, és adja meg a további lehetőségek rugalmasságot biztosít alkalmazások frissítésekor.

Egyes szolgáltatások, például a virtuális gépek és alkalmazásszolgáltatások, használjon [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) a magas rendelkezésre állású vállalati alkalmazások támogatásához régiók közötti feladatátvétel több területi támogatásának engedélyezése. Egy vonatkozó példáért lásd: [referenciaarchitektúra Azure: webes alkalmazás magas rendelkezésre állású](../../guidance/guidance-web-apps-multi-region.md).

>**Mikor érdemes használni**: Ha a vállalati és a magas rendelkezésre állású, feladatátvevő és replikációs előnyeit kihasználó alkalmazások rendelkezik.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hogyan kezelheti az alkalmazások és a projektek?

Azure hozhat létre és kezeli az Azure-erőforrások, az alkalmazások és a projektek feladatait széles skáláját biztosítja – mind szoftveres és a a [Azure-portálon](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Parancssori felületen és a PowerShell

Azure az alkalmazások és szolgáltatások kezelése a parancssorból Bash, terminált, a parancssor vagy a választott parancssori eszköz használatával két lehetőséget biztosít. Általában, feladatokat végezheti el az ugyanabban a parancssorból, mint az Azure-portál – például a létrehozása és konfigurálása a virtuális gépek, virtuális hálózatok, a webalkalmazások és egyéb szolgáltatásokat.

-   [Az Azure parancssori felület (CLI)](../../xplat-cli-install.md): lehetővé teszi a csatlakozás az Azure-előfizetéshez, és különböző feladatokat a parancssorból Azure erőforrásokon programot.

-   [Az Azure PowerShell](../../powershell-install-configure.md): modulok biztosít, amelyek lehetővé teszik, hogy az Azure-erőforrások kezelése a Windows PowerShell-parancsmagokkal.

### <a name="azure-portal"></a>Azure Portal

Az Azure portál egy webes alkalmazás, amely hozhat létre, kezelhetnek és távolítsa el az Azure-erőforrások és szolgáltatások. Az Azure-portál itt található: <https://portal.azure.com>. Ez magában foglalja egy testre szabható irányítópultot, az Azure-erőforrások kezelésére szolgáló eszközök és hozzáférés az előfizetési beállítások, és a számlázási adatokat. További információkért lásd: a [az Azure portál áttekintése](../../azure-portal-overview.md).

### <a name="rest-apis"></a>REST API-k

Azure épül, amely támogatja az Azure portál felhasználói felületének REST API-készlet. A REST API-k a legtöbb lehetővé teszi, hogy programozott módon kiépíteni, és az Azure-erőforrások és alkalmazások kezelése az Internet használatára képes bármely eszközről is támogatottak. A REST API-dokumentáció teljes készletét, tekintse meg a [Azure REST SDK referenciáit](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>API-k

REST API-k, mellett számos Azure-szolgáltatások is lehetővé teszik szoftveres erőforrások kezelése az alkalmazásaiból platform-specifikus Azure SDK-k, beleértve a következő Fejlesztőplatformok SDK-k használatával:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](http://azure.github.io/azure-sdk-for-node/)
-   [Java](https://docs.microsoft.com/java/api/)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)

Szolgáltatásokkal – például [Mobile Apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) és [Azure Media Services](../../media-services/media-services-dotnet-how-to-use.md) lehetővé teszi, hogy az ügyféloldali SDK-k biztosítanak a webes és mobil ügyfélalkalmazások hozzáférjen a szolgáltatásokhoz.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Az alkalmazás futó Azure valószínűleg magában foglalja a működő több Azure-szolgáltatásokkal, amelyek azonos életciklusának követése és -re logikai egységet. A webes alkalmazás használhatja például a Web Apps, az SQL-adatbázis, tárolási, Azure Redis Cache és Azure Content Delivery Network szolgáltatások. [Az Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) dolgozunk lévő erőforrásokat csoportként az alkalmazás segítségével. Telepítéséhez, frissítéséhez, vagy törli az összes erőforrást egyetlen, koordinált műveletben.

Logikai csoportosítás, és a kapcsolódó erőforrások kezelése kívül Azure Resource Manager kínálja telepítési, amelyek lehetővé teszik az üzembe helyezés és a kapcsolódó erőforrás testreszabása. Például erőforrás-kezelő használatával telepítheti és konfigurálhatja olyan alkalmazás, amely több virtuális gép, egy adott terheléselosztóhoz, és egyetlen egységként Azure SQL-adatbázis áll.

Az Azure Resource Manager sablon JSON-formátumú dokumentumok használatával történő központi telepítését fejleszt. Sablonok lehetővé teszik, hogy a központi telepítés meghatározhatja és kezelheti azokat az alkalmazásokat deklaratív sablonok, nem pedig parancsprogramok használatával. A sablonok különböző környezetekben, például tesztelési, átmeneti és üzemi is képes működni. Például sablonok használatával adhat hozzá egy gombot a GitHub-tárház, amely telepíti az adattárban lévő kódot az Azure szolgáltatások egyetlen kattintással.

>**Mikor érdemes használni**: erőforrás-kezelő sablonok, ha azt szeretné, egy sablon-alapú telepítést az alkalmazáshoz, amely a REST API-k, az Azure CLI és az Azure PowerShell használatával programozott módon kezelheti.

>**Első lépések**: első lépések a sablonok használatával, lásd: [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Understanding fiókok, előfizetések és számlázási

A fejlesztők számára, mint hangvételünk jobb alaposabban tanulmányozhatja a kódot, majd próbálja meg a kezdéshez a lehető legnagyobb, amellyel az alkalmazások futtatása. Biztosan szeretnénk ösztönözzék a lehető legkönnyebben dolgozni az Azure-ban. Annak érdekében, egyszerű, Azure-ajánlatok egy [ingyenes próbaverzió](https://azure.microsoft.com/free/). Egyes szolgáltatások van arra is, a "Próbálja ki ingyen" funkciót, például [Azure App Service](https://tryappservice.azure.com/), amely nem igényel, hogy akkor hozzon létre egy fiókot. Visszatöltött, úgy, ahogyan kódolása és az Azure-ba, az alkalmazás telepítéséhez alaposabban fontos is időigényes lehet, hogy a felhasználói fiókok, előfizetések és számlázási szempontból Azure működésének megismerése.

### <a name="what-is-an-azure-account"></a>Mi az Azure-fiókra?

Kell létrehozni, és együttműködik az Azure-előfizetéssel, Azure-fiókkal kell rendelkeznie. Az Azure-fiók identitás egyszerűen az Azure ad-ben, vagy egy könyvtárban, például a munkahelyi vagy iskolai szervezet, amely megbízható Azure ad. Ha a szervezet nem tartozik, a Microsoft Account, amely az Azure AD megbízik bármikor létrehozhat egy előfizetést. A helyi Windows Server Active Directory integrálása az Azure ad-vel kapcsolatos további információkért lásd: [a helyszíni identitások integrálása az Azure Active Directoryval](../../active-directory/active-directory-aadconnect.md).

Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. További tudnivalókért lásd: [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directory](../../active-directory/active-directory-how-subscriptions-associated-directory.md).

Egyes Azure meghatározása mellett fiók az identitások, más néven *felhasználók*, azt is megadhatja, *csoportok* Azure AD-ben. Felhasználói csoportok létrehozásához megfelelő módja egy előfizetésben található erőforrásokhoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlést (RBAC) használatával. Csoportok létrehozásával kapcsolatban lásd: [létrehoz egy csoportot az Azure Active Directory előzetes](../../active-directory/active-directory-groups-create-azure-portal.md). Is hozhat létre, és kezelhet a csoportok által [PowerShell-lel](../../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Saját előfizetések kezelése

Előfizetés az Azure-szolgáltatások egy logikai egységet, amely csatolva van egy Azure-fiókra. Minden társított rendelkezik egy szerepkört az előfizetés. Az Azure szolgáltatások számlázási előfizetésenként alapon történik. A rendelkezésre álló előfizetési ajánlatok típus szerint listájáért lásd: [Microsoft Azure-ajánlat részletek](https://azure.microsoft.com/support/legal/offer-details/).

#### <a name="administrator-roles"></a>Rendszergazdai szerepkörök

Azure-előfizetéssel rendelkezik, több fiók rendszergazdai szerepkör, amely bármikor rendelhet.

-   **Fiókadminisztrátort**: ezt a szerepkört az előfizetés teljes hozzáféréssel rendelkezik, és a fiók számlázási felelős.

-   **Szolgáltatás-rendszergazda**: ezt a szerepkört az előfizetés ellenőrzése alatt tartja a szolgáltatások rendelkezik. Alapértelmezés szerint ez a fiók rendszergazdaként ugyanazt a fiókot.

-   **Társadminisztrátoraként**: Ez a szerepkör, szolgáltatás-rendszergazda azonos hozzáféréssel rendelkezik, azzal a különbséggel, hogy ezért nem módosítható az előfizetés társítása Azure-címtár.

További tudnivalók a rendszergazdai szerepkörökről, [hozzáadása vagy módosítása az Azure-rendszergazdai szerepkörök](../../billing/billing-add-change-azure-subscription-administrator.md#add-an-admin-for-a-subscription).

#### <a name="resource-groups"></a>Erőforráscsoportok

Ha új Azure-szolgáltatások, az elemben megadott előfizetés. Egyes Azure-szolgáltatásokkal, más néven erőforrásokat, a környezet erőforráscsoport jönnek létre. Az erőforráscsoportok révén könnyebben telepítéséhez és kezeléséhez az alkalmazás-erőforrásokat. Erőforráscsoport tartalmaznia kell egy egységként együttműködve kívánt alkalmazás összes erőforrást. Erőforrások erőforráscsoportok közötti és még a különböző előfizetések áthelyezheti. Az erőforrások, lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../resource-group-move-resources.md).

Az Azure erőforrás-kezelő rendszer egy kiváló eszköz megjelenítése az erőforrásokat, az előfizetésben már létrehozott. További tudnivalókért lásd: [az erőforrások megtekintése és módosítása az Azure Resource Explorer](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Hozzáférés engedélyezése az erőforrások

Amikor engedélyezi a hozzáférést az Azure-erőforrások, nem mindig ajánlott a legalacsonyabb jogosultsági szint, amely egy adott feladat végrehajtásához szükséges módszereket biztosítsanak a felhasználóknak.

-   **Szerepköralapú hozzáférés-vezérlést (RBAC)**: az Azure, a felhasználói fiókok (résztvevők) a megadott hatókörben hozzáférést biztosíthat: előfizetés, erőforráscsoportból vagy az egyes erőforrások. Az RBAC lehetővé teszi a erőforráscsoport üzembe helyezés erőforráscsoport, és a hozzáférési jogot egy adott felhasználó vagy csoport. Azt is lehetővé teszik csak a célként megadott erőforráscsoportja tartozó erőforrásokhoz való hozzáférés korlátozására. A hozzáférést egyetlen erőforrásra, például a virtuális gép vagy a virtuális hálózaton. Hozzáférés biztosításához meg rendelhet hozzá egy szerepkört a felhasználó, csoport vagy egyszerű szolgáltatás. Számos előre meghatározott szerepkör is létezik, és azt is megadhatja a saját egyéni szerepkörök.

    >**Mikor érdemes használni**: amikor a felhasználók és csoportok részletes hozzáféréskezelést kell.

    >**Első lépések**: további információkért lásd: [Ismerkedés az Azure-portálon kezelési](../../active-directory/role-based-access-control-what-is.md).

-   **Szolgáltatás egyszerű objektumok**: hozzáférés biztosítása a felhasználó rendszerbiztonsági tagok és a csoportok, mellett biztosíthat ugyanolyan szintű hozzáférése egy egyszerű szolgáltatást.

    > **Mikor érdemes használni**: amikor a rendszer programozott módon Azure-erőforrások kezelése vagy alkalmazásokhoz hozzáférést a. További információkért lásd: [létrehozása az Active Directory-alkalmazás és szolgáltatás egyszerű](../../resource-group-create-service-principal-portal.md).

#### <a name="tags"></a>Címkék

Az Azure Resource Manager lehetővé teszi egyéni címkék hozzárendelése az egyes erőforrások. Címkék, amelyek kulcs-érték párok, akkor lehet hasznos, ha az erőforrások számlázási vagy figyelési rendszerezéséhez szüksége. Címkék biztosít át történő nyomon követéséhez erőforrások több erőforráscsoportok módot. Címkék a portálon, az Azure Resource Manager-sablon, illetve programozott módon, a REST API-t, az Azure CLI vagy a PowerShell használatával rendelhet hozzá. Az egyes erőforrások több címkét rendelhet. További tudnivalókért lásd: [az Azure-erőforrások rendszerezése címkék használatával](../../resource-group-using-tags.md).

### <a name="billing"></a>Számlázás

A helyszíni számítási felhőben üzemeltetett szolgáltatásokhoz való áthelyezést nyomon követése és szolgáltatás használati és a kapcsolódó költségeket megbecsülheti jelentős vonatkozik. Fontos tudni becsült költség új erőforrások havonta futtatni. Is kell tennie a számlázási megjelenésének alapján a jelenlegi költségeik adott hónap a projekt.

#### <a name="get-resource-usage-data"></a>Erőforrás-használati adatok beolvasása

Azure számlázás REST API-k, amelyek hozzáférést erőforrás-felhasználás és az Azure-előfizetések tartozó metaadat-információkat biztosít. Ezek számlázási API-k biztosítanak jobban előre jelezni, és az Azure költségek kezelésére képes. Nyomon követheti és óránkénti növekményekben beszállítói költségeit elemzi, költségkeret figyelmeztetések létrehozása, és előre jelezni jövőbeli számlázási aktuális használati trendeket alapján.

>**Első lépések**: számlázási API-val kapcsolatos további információkért lásd: [Azure számlázási használati és RateCard API-k – áttekintés](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Előre jelezni jövőbeli költségek

Bár időben költségek becsléséhez nehéz, Azure rendelkezik egy [árképzési Számológép](https://azure.microsoft.com/pricing/calculator/) használható amikor költségét becslései telepített erőforrásokhoz. A számlázási panelt a portál és a számlázási REST API-k segítségével is felbecsülni a jövőbeli költségek, a jelenlegi fogyasztás alapján történik.

>**Első lépések**: lásd: [Azure számlázási használati és RateCard API-k – áttekintés](../../billing-usage-rate-card-overview.md).

#### <a name="set-up-billing-alerts"></a>Számlázási értesítések beállítása

Után megoldás az Azure vagy az alkalmazás telepítése után, által küldött, amikor Ön megközelíti a költségkeret korlátai, a riasztás definiált e-mail értesítéseket is létrehozhat.

>**Első lépések**: további információkért lásd: [beállítása a Microsoft Azure-előfizetések riasztások számlázási](../../billing-set-up-alerts.md).

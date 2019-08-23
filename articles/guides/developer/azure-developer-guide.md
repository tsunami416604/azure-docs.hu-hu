---
title: Első lépések útmutató az Azure-beli fejlesztőknek | Microsoft Docs
description: Ez a témakör alapvető információkat biztosít a fejlesztők számára, akik a Microsoft Azure platform fejlesztési igényeiknek való használatának megkezdéséhez nyújtanak segítséget.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: 78c126559c4b919ebc81b49ed5567dd0d0196039
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971583"
---
# <a name="get-started-guide-for-azure-developers"></a>Első lépésekhez készült útmutató Azure-fejlesztőknek

## <a name="what-is-azure"></a>Mi az Azure?

Az Azure egy teljes körű felhőalapú platform, amely képes a meglévő alkalmazások tárolására, egyszerűsíteni az új alkalmazások fejlesztését, és akár a helyszíni alkalmazások továbbfejlesztését is. Az Azure integrálja az alkalmazások fejlesztéséhez, teszteléséhez, üzembe helyezéséhez és kezeléséhez szükséges felhőalapú szolgáltatásokat, miközben kihasználja a felhő-számítástechnika hatékonyságát.

Az alkalmazások az Azure-ban való üzemeltetésével kisméretű és könnyen méretezhető alkalmazást használhat az ügyfelek igényei szerint. Az Azure a magas rendelkezésre állású alkalmazásokhoz szükséges megbízhatóságot is biztosítja, még a különböző régiók közötti feladatátvételt is beleértve. A [Azure Portal](https://portal.azure.com) segítségével könnyedén kezelheti az összes Azure-szolgáltatást. A szolgáltatásokat programozott módon is kezelheti a szolgáltatás-specifikus API-k és sablonok használatával.

**Ki olvassa el ezt a következőt**: Ez az útmutató az alkalmazások fejlesztői számára készült Azure platform bemutatása. Útmutatást és irányt biztosít az új alkalmazások Azure-ban való létrehozásához vagy a meglévő alkalmazások Azure-ba való áttelepítéséhez.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

Az Azure által kínált összes szolgáltatással ijesztő feladat lehet a megoldás architektúrájának támogatásához szükséges szolgáltatások megállapítása. Ez a szakasz a fejlesztők által gyakran használt Azure-szolgáltatásokat emeli ki. Az összes Azure-szolgáltatás listáját az [Azure dokumentációjában](../../index.md)találja.

Először is el kell döntenie, hogyan üzemelteti az alkalmazást az Azure-ban. A teljes infrastruktúrát virtuális gépnek (VM) kell kezelnie. Használhatja az Azure által biztosított platform-felügyeleti szolgáltatásokat? Lehet, hogy kiszolgáló nélküli keretrendszerre van szüksége a kód futtatásához?

Az alkalmazásnak Felhőbeli tárhelyre van szüksége, amelyet az Azure számos lehetőséget kínál. Igénybe veheti az Azure vállalati hitelesítését. A felhőalapú fejlesztéshez és monitorozáshoz is vannak eszközök, és a legtöbb üzemeltetési szolgáltatás DevOps-integrációt biztosít.

Most nézzük meg, hogy milyen szolgáltatásokat ajánlunk az alkalmazások vizsgálatához.

### <a name="application-hosting"></a>Alkalmazás üzemeltetése

Az Azure számos felhőalapú számítási ajánlatot biztosít az alkalmazás futtatásához, így nem kell aggódnia az infrastruktúra részleteivel kapcsolatban. Az alkalmazások használatának növekedésével egyszerűen méretezheti vagy bővítheti erőforrásait.

Az Azure olyan szolgáltatásokat kínál, amelyek támogatják az alkalmazás fejlesztési és üzemeltetési igényeit. Az Azure biztosítja az infrastruktúra-szolgáltatást (IaaS), így teljes körűen szabályozhatja az alkalmazás üzemeltetését. Az Azure platform as Service (Pásti) ajánlatai biztosítják az alkalmazások működtetéséhez szükséges teljes körűen felügyelt szolgáltatásokat. Az Azure-ban még igaz kiszolgáló nélküli üzemeltetés is van, ahol mindössze annyit kell tennie, hogy megírja a kódját.

![Azure-alkalmazás üzemeltetési lehetőségei](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service 

Ha azt szeretné, hogy a leggyorsabb elérési út a webalapú projektek közzétételéhez, érdemes Azure App Service. A App Service megkönnyíti a webalkalmazások bővítését a mobil ügyfelek támogatásához és a könnyen felhasznált REST API-k közzétételéhez. Ez a platform a közösségi szolgáltatók, a forgalomon alapuló automatikus skálázás, az éles környezetben végzett tesztelés, valamint a folyamatos és a tároló alapú üzembe helyezések révén nyújt hitelesítést.

Létrehozhat webalkalmazásokat, Mobile App back-és API-alkalmazásokat.

Mivel mindhárom alkalmazás típusa megosztja a App Service futtatókörnyezetet, üzemeltetheti a webhelyeket, támogatja a mobil ügyfeleket, és elérhetővé teheti az API-kat az Azure-ban, mindezt ugyanabból a projektből vagy megoldásból. Ha többet szeretne megtudni a App Serviceről, tekintse meg [Az Azure Web Apps](../../app-service/overview.md)ismertetése című témakört.

App Service a DevOps szem előtt tartva lett tervezve. Különböző eszközöket támogat a közzétételhez és a folyamatos integrációs üzembe helyezésekhez, beleértve a GitHub-webhookokat, a Jenkinst, az Azure DevOps, a TeamCity és egyebeket.

A meglévő alkalmazásokat áttelepítheti App Servicera az [Online áttelepítési eszköz](https://www.migratetoazure.net/)használatával.

> **Mikor kell használni**: App Service használata a meglévő webalkalmazások Azure-ba való áttelepítésekor, és ha teljes körűen felügyelt üzemeltetési platformra van szüksége a Web Apps számára. App Service akkor is használhatja, ha a mobileszközök támogatásához vagy a REST API-k az alkalmazással való eléréséhez szükséges.
> 
> Első **lépések**: A App Service megkönnyíti az első webalkalmazás, a [](../../app-service/app-service-web-get-started-dotnet.md) [Mobile App](../../app-service-mobile/app-service-mobile-ios-get-started.md)vagy az [API-alkalmazás](../../app-service/app-service-web-tutorial-rest-api.md)létrehozását és üzembe helyezését.
> 
> **Próbálja ki most**: App Service lehetővé teszi egy rövid élettartamú alkalmazás kiépítését a platform kipróbálásához anélkül, hogy regisztrálnia kellene egy Azure-fiókra. Próbálja ki a platformot, és [hozza létre a Azure app Service alkalmazást](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Azure Virtual Machines

A IaaS-szolgáltatóként az Azure lehetővé teszi az alkalmazás üzembe helyezését vagy áttelepítését Windows vagy Linux rendszerű virtuális gépekre. Az Azure Virtual Network együtt az Azure Virtual Machines támogatja a Windows vagy Linux rendszerű virtuális gépek Azure-ba való telepítését. A virtuális gépekkel teljes mértékben vezérelheti a gép konfigurációját. Virtuális gépek használatakor az összes kiszolgáló szoftver telepítésére, konfigurálására, karbantartására és az operációs rendszerre vonatkozó javítások felelősek.

A virtuális gépekhez tartozó szabályozás szintje miatt a kiszolgálói munkaterhelések széles körét futtathatja az Azure-ban, amelyek nem illeszkednek a Pásti-modellbe. Ezek a számítási feladatok közé tartoznak az adatbázis-kiszolgálók, a Windows Server Active Directory és a Microsoft SharePoint. További információkért tekintse meg a [Linux](/azure/virtual-machines/linux/) vagy a [Windows rendszerhez](/azure/virtual-machines/windows/)készült Virtual Machines dokumentációját.

> **Mikor kell használni**: A Virtual Machines akkor használja, ha teljes hozzáférést szeretne alkalmazni az alkalmazás-infrastruktúrához, vagy a helyszíni alkalmazások számítási feladatait az Azure-ba szeretné áttelepíteni anélkül, hogy módosításokat kellene végeznie.
> 
> Első **lépések**: Hozzon létre egy [linuxos virtuális gépet](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) vagy egy [Windows rendszerű virtuális gépet](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) a Azure Portalból.

#### <a name="azure-functions-serverless"></a>Azure Functions (kiszolgáló nélküli)

Nem kell aggódnia a teljes alkalmazás vagy infrastruktúra kiépítésével és kezelésével a kód futtatásához. Mi a teendő, ha megírhatja a kódot, és az eseményekre vagy ütemtervekre adott válaszként fut?  [Azure functions](../../azure-functions/functions-overview.md) egy "kiszolgáló nélküli" stílusú ajánlat, amely lehetővé teszi, hogy csak a szükséges kódot írja. A függvények esetében a kód végrehajtását HTTP-kérések, webhookok, Cloud Service-események vagy ütemezett feladatok aktiválják. Megadhatja az Ön által választott fejlesztői nyelvet, például C\#, F\#, Node. js, Python vagy php. A fogyasztáson alapuló számlázással csak a kód végrehajtásának idejére kell fizetnie, az Azure pedig igény szerint méretezhető.

> **Mikor kell használni**: Azure Functions használata, ha olyan kódot használ, amelyet más Azure-szolgáltatások, webalapú események vagy ütemterv alapján aktiválnak. A függvényeket akkor is használhatja, ha nincs szüksége egy teljes körűen üzemeltetett projekt terhelésére, vagy ha csak a kód futási idejét szeretné kifizetni. További információ: [Azure functions Overview (áttekintés](../../azure-functions/functions-overview.md)).
> 
> Első **lépések**: Kövesse a functions gyors oktatóanyagot az [első függvény létrehozásához](../../azure-functions/functions-create-first-azure-function.md) a portálon.
> 
> **Próbálja ki most**: Azure Functions lehetővé teszi a kód futtatását anélkül, hogy regisztrálnia kellene egy Azure-fiókra. Próbálja ki most, és [hozza létre első Azure](https://tryappservice.azure.com/)-függvényét.

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a méretezhető és megbízható szolgáltatások kiépítését, becsomagolását, üzembe helyezését és felügyeletét. Emellett átfogó, alkalmazás-felügyeleti képességeket biztosít a üzembe helyezéshez, az üzembe helyezéshez, a figyeléshez, a frissítéshez/javításhoz és a telepített alkalmazások törléséhez. Az olyan alkalmazások, amelyek egy megosztott készleten futnak, kis méretűek és akár több száz vagy akár több ezer gép számára is elindíthatók.

Service Fabric támogatja a WebAPI-t a .NET (OWIN) és a ASP.NET Core nyílt webes felületével. SDK-kat biztosít a .NET Core és a Java platformon futó Linux-szolgáltatások létrehozásához. Ha többet szeretne megtudni a Service Fabricről, tekintse meg a [Service Fabric dokumentációját](https://docs.microsoft.com/azure/service-fabric/).

> **Mikor kell használni:** A Service Fabric jó választás, ha egy alkalmazást hoz létre, vagy egy meglévő alkalmazást telepít át egy Service-architektúra használatára. A Service Fabric akkor érdemes használni, ha a mögöttes infrastruktúrát nagyobb mértékben kell megadnia, vagy közvetlenül hozzá kell férnie.
> 
> **Első lépések:** [Hozza létre első Azure Service Fabric alkalmazását](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Alkalmazások fejlesztése az Azure-szolgáltatásokkal

Az alkalmazások üzemeltetése mellett az Azure olyan szolgáltatás-ajánlatokat is biztosít, amelyek a felhőben és a helyszínen egyaránt növelhetik az alkalmazások funkcióit, fejlesztését és karbantartását.

#### <a name="hosted-storage-and-data-access"></a>Üzemeltetett tár és adathozzáférés

A legtöbb alkalmazásnak tárolnia kell az adatait, függetlenül attól, hogy miként szeretné üzemeltetni az alkalmazást az Azure-ban, vegye figyelembe a következő tárolási és adatszolgáltatások egyikét.

- **Azure Cosmos db**: Egy globálisan elosztott, többmodelles adatbázis-szolgáltatás, amely lehetővé teszi az átviteli sebesség és a tárterület rugalmas méretezését tetszőleges számú földrajzi régióban, átfogó SLA-val. 
  > **Mikor kell használni:** Ha az alkalmazásnak dokumentum-, tábla-vagy Graph-adatbázisokra, például MongoDB-adatbázisokra van szüksége, több jól meghatározott konzisztencia-modellel. 
  > 
  > Első **lépések**: [Hozzon létre egy Azure Cosmos db](../../cosmos-db/create-sql-api-dotnet.md)webalkalmazást. Ha Ön MongoDB-fejlesztő, tekintse meg [a MongoDB](../../cosmos-db/create-mongodb-dotnet.md)-Webalkalmazás létrehozása a Azure Cosmos DBkal című témakört.

- **Azure Storage**: Tartós, magasan elérhető tárhelyet kínál a blobokhoz, a várólistákhoz, a fájlokhoz és más típusú nem kapcsolódó adattípusokhoz. A Storage biztosítja a virtuális gépek tárolási alapjait.

  > **Mikor kell használni**: Ha az alkalmazás nem kapcsolódó adattípusokat, például kulcs-érték párokat (táblákat), blobokat, fájlokat, megosztásokat vagy üzeneteket (várólistákat) tárol.
  > 
  > Első **lépések**: Válasszon az alábbi típusok közül: Blobok [](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [táblák](../../cosmos-db/table-storage-how-to-use-dotnet.md), [várólisták](../../storage/queues/storage-dotnet-how-to-use-queues.md)vagy [fájlok](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Azure SQL Database**: A Microsoft SQL Server motor Azure-alapú verziója, amely a felhőben található, a kapcsolódó táblázatos adatokat tárolja. SQL Database az állásidő, az üzletmenet folytonossága és az adatvédelem nélkül biztosít kiszámítható teljesítményt, méretezhetőséget.

  > **Mikor kell használni**: Ha az alkalmazás hivatkozási integritással, tranzakciós támogatással és TSQL-lekérdezések támogatásával igényli az adattárolást.
  > 
  > Első **lépések**: [Hozzon létre egy SQL-adatbázist percek alatt a Azure Portal használatával](../../sql-database/sql-database-get-started.md).


A meglévő helyszíni és az Azure-ba történő áthelyezéséhez [Azure Data Factory](../../data-factory/introduction.md) is használhat. Ha nem áll készen az adatáthelyezésre a felhőbe, [hibrid kapcsolatok](../../biztalk-services/integration-hybrid-connection-overview.md) a BizTalk Services lehetővé teszi a app Service üzemeltetett alkalmazás helyszíni erőforrásokhoz való összekapcsolását. A helyszíni alkalmazásokból is kapcsolódhat az Azure-beli adatokhoz és a tárolási szolgáltatásokhoz.

#### <a name="docker-support"></a>Docker-támogatás

A Docker-tárolók, az operációs rendszer virtualizálása, lehetővé teszik az alkalmazások hatékonyabb és kiszámítható módon való üzembe helyezését. A tárolóban lévő alkalmazások ugyanúgy működnek éles környezetben, mint a fejlesztési és tesztelési rendszereken. A tárolók a standard Docker-eszközök használatával kezelhetők. Az Azure-beli tároló-alapú alkalmazások üzembe helyezéséhez és kezeléséhez használhatja meglévő ismereteit és népszerű nyílt forráskódú eszközeit.

Az Azure számos lehetőséget kínál a tárolók használatára az alkalmazásokban.

- **Azure Docker VM-bővítmény**: Lehetővé teszi a virtuális gép Docker-gazdagépként való megadását a Docker-eszközök használatával.

  > **Mikor kell használni**: Ha egységes tároló-üzembe helyezést szeretne készíteni az alkalmazásaihoz egy virtuális gépen, vagy ha a Docker- [összeállítást](https://docs.docker.com/compose/overview/)szeretné használni.
  > 
  > Első **lépések**: [Docker-környezet létrehozása az Azure-ban a Docker VM bővítmény használatával](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Azure Container Service**: Lehetővé teszi olyan virtuális gépek fürtjének létrehozását, konfigurálását és kezelését, amelyek a tároló alkalmazások futtatására vannak konfigurálva. További információ a Container Service-ről: [Azure Container Service bevezető](../../container-service/container-service-intro.md).

  > **Mikor kell használni**: Ha olyan éles használatra kész, méretezhető környezeteket kell létrehoznia, amelyek további ütemezési és felügyeleti eszközöket biztosítanak, vagy amikor üzembe helyez egy Docker Swarm-fürtöt.
  > 
  > Első **lépések**: [Container Service-fürt üzembe helyezése](../../container-service/dcos-swarm/container-service-deployment.md).

- **Docker-gép**: Lehetővé teszi a Docker-motor telepítését és kezelését a virtuális gazdagépeken a Docker-Machine parancsok használatával.

  >**Mikor kell használni**: Ha egy Docker-gazdagép létrehozásával gyorsan kell prototípust készítenie az alkalmazáshoz.

- **Egyéni Docker-rendszerkép a app Servicehoz**: Lehetővé teszi a Docker-tárolók használatát egy tároló-beállításjegyzékből vagy egy ügyfél-tárolóból egy, a Linuxon futó webalkalmazás telepítésekor.

  > **Mikor kell használni**: A Linux rendszerű webalkalmazások Docker-rendszerképekre való telepítésekor.
  > 
  > Első **lépések**: [Egyéni Docker-rendszerképet használhat](../../app-service/containers/quickstart-docker-go.md)a linuxon futó app Servicehoz.

### <a name="authentication"></a>Authentication

Rendkívül fontos, hogy ne csak tudja, ki használja az alkalmazásokat, hanem megakadályozza az erőforrások jogosulatlan elérését is. Az Azure számos módszert kínál az alkalmazás-ügyfelek hitelesítésére.

- **Azure Active Directory (Azure ad)** : A Microsoft több-bérlős, felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. Az Azure AD-val való integrálással hozzáadhat egyszeri bejelentkezést (SSO) az alkalmazásaihoz. A címtár tulajdonságai az Azure AD Graph API közvetlenül vagy a Microsoft Graph API használatával érhetők el. Integrálhatja az Azure AD-támogatást a OAuth 2.0 engedélyezési keretrendszeréhez, és az ID-t a natív HTTP/REST-végpontok és a többplatformos Azure AD-hitelesítési kódtárak használatával is megnyithatja.

  > **Mikor kell használni**: Ha egyszeri bejelentkezéses felhasználói élményt szeretne biztosítani, végezzen gráf-alapú adatmennyiséget, vagy hitelesítse a tartományalapú felhasználókat.
  > 
  > Első **lépések**: További tudnivalókat a [Azure Active Directory fejlesztői útmutatójában](../../active-directory/develop/v1-overview.md)talál.

- **App Service hitelesítés**: Ha úgy dönt, App Service az alkalmazás üzemeltetéséhez, az Azure AD beépített hitelesítési támogatását, valamint a közösségi identitás-szolgáltatók, például a Facebook, a Google, a Microsoft és a Twitter használatát is lehetővé teszi.

  > **Mikor kell használni**: Ha engedélyezni szeretné a hitelesítést egy App Service alkalmazásban az Azure AD-vel, a közösségi identitás-szolgáltatókkal vagy mindkettővel.
  > 
  > Első **lépések**: Ha többet szeretne megtudni a App Service hitelesítéséről, tekintse meg [a hitelesítés és engedélyezés a Azure app Serviceban](../../app-service/overview-authentication-authorization.md)című témakört.

Az Azure-ban ajánlott biztonsági eljárásokkal kapcsolatos további információkért lásd az [Azure biztonsági eljárásai és mintái](../../security/fundamentals/best-practices-and-patterns.md)című témakört.

### <a name="monitoring"></a>Figyelés

Az Azure-ban futó alkalmazásokkal nyomon követheti a teljesítményt, figyelheti a problémákat, és megtudhatja, hogyan használják az ügyfelek az alkalmazást. Az Azure számos figyelési lehetőséget biztosít.

-   **Visual Studio Application Insights**: Egy Azure által üzemeltetett bővíthető elemzési szolgáltatás, amely integrálható a Visual Studióval az élő webalkalmazások figyeléséhez. A szolgáltatás biztosítja az alkalmazások teljesítményének és használhatóságának folyamatos javításához szükséges adatmennyiséget, függetlenül attól, hogy azok az Azure-ban futnak-e.

    >Első **lépések**: Kövesse a [Application Insights oktatóanyagot](../../azure-monitor/app/app-insights-overview.md).

-   **Azure monitor**: Olyan szolgáltatás, amely megkönnyíti az Azure-infrastruktúra és-erőforrások által generált mérőszámok és naplók megjelenítését, lekérdezését, továbbítását, archiválását és az azokkal kapcsolatos műveletek elvégzését. A figyelő biztosítja a Azure Portalban látható adatnézeteket, és egyetlen forrás az Azure-erőforrások monitorozásához.
 
    >Első **lépések**: [A Azure monitor első lépései](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>DevOps-integráció

Függetlenül attól, hogy a virtuális gépek kiépítése vagy a webalkalmazások folyamatos integrációval való közzététele, az Azure integrálható a legtöbb népszerű DevOps-eszközzel. A Jenkins, a GitHub, a Puppet, a Chef, a TeamCity, a Ansible, az Azure DevOps és mások eszközeinek támogatásával a már meglévő eszközökkel dolgozhat, és maximalizálhatja a meglévő felhasználói élményt.

> **Próbálja ki most:** [Próbálja ki több DevOps](https://azure.microsoft.com/try/devops/)-integrációt.
> 
> Első **lépések**: App Service alkalmazás DevOps-beállításainak megtekintéséhez tekintse meg a [Azure app Service folyamatos üzembe helyezését](../../app-service/deploy-continuous-deployment.md)ismertető témakört.


## <a name="azure-regions"></a>Azure-régiók

Az Azure egy globális felhőalapú platform, amely világszerte számos régióban általánosan elérhető. Amikor kiépít egy szolgáltatást, alkalmazást vagy virtuális gépet az Azure-ban, a rendszer arra kéri, hogy válasszon ki egy régiót, amely egy adott adatközpontot jelöl, amelyben az alkalmazás fut, vagy ahol az adatait tárolja. Ezek a régiók az [Azure-régiók](https://azure.microsoft.com/regions/) lapon közzétett bizonyos helyeknek felelnek meg.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Válassza ki az alkalmazás és az adatai számára legmegfelelőbb régiót

Az Azure használatának egyik előnye, hogy alkalmazásai különböző adatközpontokban üzembe helyezhetők világszerte. Az Ön által választott régió befolyásolhatja az alkalmazás teljesítményét. Érdemes lehet például olyan régiót választani, amely közelebb van az ügyfelek többségéhez, hogy csökkentse a hálózati kérelmek késését. Előfordulhat, hogy ki kell választania a régiót, hogy megfeleljen az alkalmazás egyes országokban/régiókban való terjesztésének jogi követelményeinek. Mindig ajánlott eljárás az alkalmazásadatok ugyanabban az adatközpontban vagy egy adatközpontban való tárolására, mint amennyire a lehető legközelebb az alkalmazást üzemeltető adatközponthoz.

### <a name="multi-region-apps"></a>Többrégiós alkalmazások

Habár nem valószínű, hogy egy teljes adatközpont offline állapotba kerül egy esemény, például egy természeti katasztrófa vagy Internet meghibásodása miatt. Az ajánlott eljárás a létfontosságú üzleti alkalmazások üzemeltetése több adatközpontban a maximális rendelkezésre állás biztosítása érdekében. Több régió használata is csökkentheti a globális felhasználók késését, és további lehetőségeket biztosít a rugalmassághoz az alkalmazások frissítésekor.

Egyes szolgáltatások, például a virtuális gépek és a App Services az [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) használatával lehetővé teszik a régiók közötti feladatátvételt a régiókban a magas rendelkezésre állású Nagyvállalati alkalmazások támogatásához. Példaként tekintse [meg az Azure Reference Architecture: Futtasson egy webalkalmazást több](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)régióban.

>**Mikor kell használni**: Ha olyan nagyvállalati és magas rendelkezésre állású alkalmazásokat használ, amelyek kihasználják a feladatátvételt és a replikálást.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hogyan az alkalmazások és a projektek kezelése?

Az Azure számos tapasztalatot biztosít az Azure-erőforrások,-alkalmazások és-projektek létrehozásához és kezeléséhez, akár programozott módon, akár a [Azure Portal](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Parancssori felületek és PowerShell

Az Azure két lehetőséget biztosít az alkalmazások és szolgáltatások parancssorból történő felügyeletére a bash, a Terminal, a parancssor vagy a választható parancssori eszköz használatával. Általában ugyanazokat a feladatokat a parancssorból is végrehajthatja, mint a Azure Portalban – például virtuális gépek, virtuális hálózatok, webalkalmazások és egyéb szolgáltatások létrehozása és konfigurálása.

-   [Azure parancssori felület (CLI)](../../xplat-cli-install.md): Lehetővé teszi az Azure-előfizetésekhez való kapcsolódást és a parancssorból származó Azure-erőforrásokkal kapcsolatos különböző feladatok elvégzését.

-   [Azure PowerShell](../../powershell-install-configure.md): A modulokat tartalmaz olyan parancsmagokkal, amelyek lehetővé teszik az Azure-erőforrások kezelését a Windows PowerShell használatával.

### <a name="azure-portal"></a>Azure Portal

A Azure Portal egy webalapú alkalmazás, amely Azure-erőforrások és-szolgáltatások létrehozásához, kezeléséhez és eltávolításához használható. A Azure Portal a következő helyen <https://portal.azure.com>található:. Tartalmaz egy testreszabható irányítópultot, az Azure-erőforrások kezelésére szolgáló eszközöket, valamint az előfizetési beállításokhoz és a számlázási információkhoz való hozzáférést. További információ: [Azure Portal áttekintése](../../azure-portal-overview.md).

### <a name="rest-apis"></a>REST API-k

Az Azure a Azure Portal felhasználói felületét támogató REST API-k készletére épül. A REST API-k többsége támogatja az Azure-erőforrások és-alkalmazások programozott módon történő üzembe helyezését és kezelését bármely internetalapú eszközről. Az REST API dokumentációjának teljes készletét az [Azure Rest SDK](https://docs.microsoft.com/rest/api/)-referenciában találja.

### <a name="apis"></a>API-k

A REST API-kon kívül számos Azure-szolgáltatás is lehetővé teszi, hogy programozott módon kezelhesse az alkalmazások erőforrásait a platform-specifikus Azure SDK-k használatával, beleértve az alábbi fejlesztői platformokhoz készült SDK-kat:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

Az olyan szolgáltatások, mint például a [Mobile apps](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) és a [Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) ügyféloldali SDK-kat biztosítanak, amelyek lehetővé teszik a webes és mobil ügyfélalkalmazások szolgáltatásainak elérését.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Az alkalmazás Azure-beli futtatásának valószínűsége több Azure-szolgáltatással is működik, amelyek mindegyike ugyanazt az életciklust követi, és logikai egységként is használható. Előfordulhat például, hogy egy webalkalmazás használhatja a Web Apps, a SQL Database, a Storage, az Azure cache for Redis és az Azure Content Delivery Network Services szolgáltatást. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) lehetővé teszi, hogy az alkalmazásban lévő erőforrásokat csoportként működjön. Egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti az összes erőforrást.

A kapcsolódó erőforrások logikai csoportosítása és kezelése mellett Azure Resource Manager olyan központi telepítési képességeket is tartalmaz, amelyek lehetővé teszik a kapcsolódó erőforrások üzembe helyezésének és konfigurálásának testreszabását. A Resource Manager használatával például telepítheti és konfigurálhatja azt az alkalmazást, amely több virtuális gépről, egy terheléselosztóből és egy Azure SQL Database-ből áll egyetlen egységként.

Ezeket az üzembe helyezéseket egy Azure Resource Manager sablon használatával fejlesztheti, amely egy JSON-formátumú dokumentum. A sablonok lehetővé teszik, hogy a parancsfájlok helyett deklaratív sablonok használatával definiálja az alkalmazásokat, és kezelje az alkalmazásait. A sablonok különböző környezetekben, például tesztelési, átmeneti és éles környezetben is működhetnek. A sablonok használatával például hozzáadhat egy gombot egy GitHub-tárházhoz, amely egyetlen kattintással üzembe helyezi a tárházban lévő kódot egy Azure-szolgáltatásba.

> **Mikor kell használni**: Használjon Resource Manager-sablonokat, ha olyan sablon alapú üzembe helyezést szeretne alkalmazni az alkalmazáshoz, amelyet a REST API-k, az Azure CLI és a Azure PowerShell használatával kezelhet programozás útján.
> 
> Első **lépések**: A sablonok használatának megkezdéséhez lásd: [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>A fiókok, az előfizetések és a számlázás ismertetése

A fejlesztőknek jobb, ha a kódot vesszük figyelembe, és a lehető leggyorsabban igyekszünk elindítani az alkalmazásokat. Javasoljuk, hogy a lehető legkönnyebben megkezdje az Azure-ban való munkát. Az Azure [ingyenes próbaidőszakot](https://azure.microsoft.com/free/)biztosít a könnyebb kezelhetőség érdekében. Egyes szolgáltatások esetében a "kipróbálás ingyenes" funkciót is használhatja, például a [Azure app Servicet](https://tryappservice.azure.com/), amelyhez még nem kell fiókot létrehoznia. Olyan szórakozást, mint a kódolás és az alkalmazás üzembe helyezése az Azure-ban, fontos, hogy megértse, hogyan működik az Azure a felhasználói fiókok, előfizetések és a számlázás szempontjából.

### <a name="what-is-an-azure-account"></a>Mi az Azure-fiók?

Azure-előfizetések létrehozásához vagy használatához Azure-fiókkal kell rendelkeznie. Az Azure-fiókok egyszerűen az Azure ad-ben vagy egy címtárban, például munkahelyi vagy iskolai szervezetben lévő identitások, amelyeket az Azure AD megbízhatónak tekint. Ha nem tartozik ilyen szervezethez, bármikor létrehozhat egy előfizetést az Azure AD által megbízhatóként használt Microsoft-fiókkal. Ha többet szeretne megtudni a helyszíni Windows Server Active Directory Azure AD-vel való integrálásáról, tekintse [meg a](../../active-directory/hybrid/whatis-hybrid-identity.md)helyszíni identitások integrálása a Azure Active Directorykal című témakört.

Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. További információkért lásd: [Az Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)-előfizetések társítása Azure Active Directoryhoz.

Az egyes Azure-fiókok identitásai (más néven *felhasználók*) definiálásán kívül *csoportokat* is meghatározhat az Azure ad-ben. A felhasználói csoportok létrehozása jó módja a szerepköralapú hozzáférés-vezérlés (RBAC) használatával az előfizetések erőforrásaihoz való hozzáférés kezelésére. A csoportok létrehozásával kapcsolatos további információkért lásd: [csoport létrehozása Azure Active Directory előzetes](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)verzióban. Csoportokat a [PowerShell használatával](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)is létrehozhat és kezelhet.

### <a name="manage-your-subscriptions"></a>Előfizetések kezelése

Az előfizetés egy Azure-fiókhoz kapcsolódó Azure-szolgáltatások logikai csoportosítása. Egyetlen Azure-fiók több előfizetést is tartalmazhat. Az Azure-szolgáltatások számlázása előfizetési alapon történik. Az elérhető előfizetési ajánlatok listáját a következő típus szerint tekintheti meg: [Microsoft Azure ajánlat részletei](https://azure.microsoft.com/support/legal/offer-details/). Az Azure-előfizetések rendelkeznek egy fiók rendszergazdájával, aki teljes körűen szabályozhatja az előfizetést, valamint egy szolgáltatás-rendszergazdát, aki az előfizetés összes szolgáltatását felügyeli. További információ a klasszikus előfizetési rendszergazdákról: Azure-előfizetések [rendszergazdáinak hozzáadása vagy módosítása](../../billing/billing-add-change-azure-subscription-administrator.md). A rendszergazdákon kívül az egyes fiókok a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)segítségével az Azure-erőforrások részletes ellenőrzését is megadhatják.

#### <a name="resource-groups"></a>Erőforráscsoportok

Új Azure-szolgáltatások kiépítésekor ezt egy adott előfizetésben teheti meg. Az egyes Azure-szolgáltatások, amelyek más néven erőforrások, egy erőforráscsoport kontextusában jönnek létre. Az erőforráscsoportok megkönnyítik az alkalmazás erőforrásainak üzembe helyezését és kezelését. Az erőforráscsoport az alkalmazás összes olyan erőforrását tartalmazza, amelyet egységként szeretne használni. Az erőforrásokat az erőforráscsoportok és a különböző előfizetések között is áthelyezheti. További információ az erőforrások áthelyezéséről: [erőforrások áthelyezése új erőforráscsoporthoz vagy](../../resource-group-move-resources.md)előfizetésbe.

A Azure Erőforrás-kezelő nagyszerű eszköz az előfizetésben már létrehozott erőforrások megjelenítéséhez. További információ: az [erőforrások megtekintése és módosítása a Azure erőforrás-kezelő használatával](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Erőforrásokhoz való hozzáférés biztosítása

Ha engedélyezi az Azure-erőforrásokhoz való hozzáférést, az ajánlott eljárás az, hogy az adott feladat végrehajtásához szükséges legalacsonyabb jogosultsággal lássa el a felhasználókat.

- **Szerepköralapú hozzáférés-vezérlés (RBAC)** : Az Azure-ban hozzáférést biztosíthat a felhasználói fiókokhoz (rendszerbiztonsági tag) egy adott hatókörben: előfizetés, erőforráscsoport vagy egyedi erőforrás. A RBAC lehetővé teszi, hogy erőforrás-készletet telepítsen egy erőforráscsoporthoz, és engedélyeket adjon egy adott felhasználónak vagy csoportnak. Azt is lehetővé teszi, hogy korlátozza a hozzáférést csak a célként megadott erőforráscsoporthoz tartozó erőforrásokhoz. Hozzáférést biztosíthat egyetlen erőforráshoz, például virtuális géphez vagy virtuális hálózathoz is. A hozzáférés engedélyezéséhez hozzá kell rendelnie egy szerepkört a felhasználóhoz, a csoporthoz vagy az egyszerű szolgáltatáshoz. Számos előre definiált szerepkör létezik, és saját egyéni szerepkörök is megadhatók. További információ: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md).

  > **Mikor kell használni**: Ha a felhasználók és csoportok részletes hozzáférés-vezérlésére van szükség, vagy ha a felhasználónak egy előfizetés tulajdonosát kell tennie.
  > 
  > Első **lépések**: További információ: [a hozzáférés kezelése a RBAC és a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

- **Egyszerű szolgáltatások objektumai**: A felhasználói rendszerbiztonsági tag és a csoportok hozzáférésének biztosításán kívül ugyanezt a hozzáférést is megadhatja egy egyszerű szolgáltatáshoz.

  > **Mikor kell használni**: Ha programozott módon kezeli az Azure-erőforrásokat, vagy hozzáférést biztosít az alkalmazásokhoz. További információ: [Create Active Directory Application and szolgáltatásnév](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Tags

Azure Resource Manager lehetővé teszi, hogy egyéni címkéket rendeljen az egyes erőforrásokhoz. A kulcs-érték párokat tartalmazó címkék hasznosak lehetnek, ha erőforrásokat kell szerveznie a számlázáshoz vagy a figyeléshez. A címkék lehetővé teszik az erőforrások több erőforráscsoport közötti nyomon követését. A címkék a portálon, a Azure Resource Manager sablonban vagy programozott módon, a REST API, az Azure CLI vagy a PowerShell használatával rendelhetők hozzá. Az egyes erőforrásokhoz több címkét is hozzárendelhet. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](../../resource-group-using-tags.md).

### <a name="billing"></a>Számlázás

A helyszíni számítástechnika és a felhőben üzemeltetett szolgáltatások között a szolgáltatás használatának nyomon követése és becslése, valamint a kapcsolódó költségek jelentős jelentőséggel bír. Fontos, hogy megbecsülhető legyen az új erőforrások havi rendszerességgel történő futtatásának költsége. Azt is meg kell tudnia, hogy a számlázás hogyan néz ki egy adott hónapban a jelenlegi kiadások alapján.

#### <a name="get-resource-usage-data"></a>Erőforrás-használati adatok beolvasása

Az Azure olyan számlázási REST API-k készletét biztosítja, amelyek hozzáférést biztosítanak az Azure-előfizetések erőforrás-felhasználási és metaadat-adataihoz. Ezek a számlázási API-k lehetővé teszi az Azure-költségek jobb előrejelzését és kezelését. Az órákon belül nyomon követheti és elemezheti a kiadásokat, megteremtheti a kiadási riasztásokat, és megjósolhatja a jövőbeli számlázást a jelenlegi használati trendek alapján.

>Első **lépések**: Ha többet szeretne megtudni a számlázási API-k használatáról, tekintse meg az [Azure számlázási használat és a RateCard API](../../billing-usage-rate-card-overview.md)-k áttekintése című témakört.

#### <a name="predict-future-costs"></a>Jövőbeli költségek előrejelzése

Bár a költségek előre megbecsülve vannak, az Azure [díjszabási kalkulátora](https://azure.microsoft.com/pricing/calculator/) a telepített erőforrások költségeinek becslése alapján is felhasználható. A portál számlázási paneljén és a számlázási REST API-kon is felhasználhatja a jövőbeli költségek becslését a jelenlegi fogyasztás alapján.

>Első **lépések**: Lásd: [Az Azure számlázási használati és RateCard API-k áttekintése](../../billing-usage-rate-card-overview.md).

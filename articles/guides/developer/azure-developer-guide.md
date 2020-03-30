---
title: Útmutató az Azure fejlesztőinek | Microsoft dokumentumok
description: Ez a cikk alapvető információkat nyújt azoknak a fejlesztőknek, akik a Microsoft Azure platformot szeretnék használni fejlesztési igényeiknek megfelelően.
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
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: d74fd2e3f6b5cc090c6313aba67a1e139c713b85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245004"
---
# <a name="get-started-guide-for-azure-developers"></a>Azure fejlesztői útmutató az első lépésekhez

## <a name="what-is-azure"></a>Mi az Azure?

Az Azure egy teljes felhőplatform, amely képes a meglévő alkalmazások üzemeltetésére és az új alkalmazások fejlesztésének egyszerűsítésére. Az Azure még a helyszíni alkalmazásokat is javíthatja. Az Azure integrálja az alkalmazások fejlesztéséhez, teszteléséhez, üzembe helyezéséhez és kezeléséhez szükséges felhőszolgáltatásokat, miközben kihasználja a felhőalapú számítástechnika hatékonyságát.

Az alkalmazások Azure-beli üzemeltetésével az ügyfelek igényeinek növekedésével kis méretűre skálázhatja az alkalmazást. Az Azure a magas rendelkezésre állású alkalmazásokhoz szükséges megbízhatóságot is kínálja, még a különböző régiók közötti feladatátvételt is beleértve. Az [Azure Portal](https://portal.azure.com) segítségével könnyedén kezelheti az összes Azure-szolgáltatást. A szolgáltatásokat programozott módon is kezelheti szolgáltatásspecifikus API-k és sablonok használatával.

Ez az útmutató az Azure platform alkalmazásfejlesztőknek bemutatása. Útmutatást és útmutatást nyújt, amelyekkel új alkalmazásokat kell kezdenie az Azure-ban, vagy át kell migrálnia a meglévő alkalmazásokat az Azure-ba.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

Az Azure által nyújtott összes szolgáltatással megfélemlítő feladat lehet kitalálni, hogy mely szolgáltatásokra van szüksége a megoldásarchitektúrájának támogatásához. Ez a szakasz kiemeli az Azure-szolgáltatásokat, amelyeket a fejlesztők gyakran használnak. Az összes Azure-szolgáltatás listáját az [Azure dokumentációjában](../../index.yml)találja.

Először el kell döntenie, hogyan üzemeltetheti az alkalmazást az Azure-ban. Szüksége van a teljes infrastruktúra virtuális gépként (VM) kezelése szükséges. Használhatja az Azure által biztosított platformfelügyeleti létesítményeket? Lehet, hogy szükség van egy kiszolgáló nélküli keretrendszer a fogadó kód végrehajtása csak?

Az alkalmazásnak felhőalapú tárhelyre van szüksége, amelyhez az Azure számos lehetőséget biztosít. Kihasználhatja az Azure vállalati hitelesítését. Vannak olyan eszközök is, amelyek felhőalapú fejlesztéshez és figyeléshez, és a legtöbb hosting szolgáltatás kínál DevOps-integrációt.

Most nézzük meg azokat a konkrét szolgáltatásokat, amelyeket az alkalmazások vizsgálatának javasoljuk.

### <a name="application-hosting"></a>Alkalmazás üzemeltetése

Az Azure számos felhőalapú számítási ajánlatot kínál az alkalmazás futtatásához, így nem kell aggódnia az infrastruktúra részletei miatt. Az alkalmazások használatának növekedésével könnyedén felskálázhatja vagy kiskálázhatja az erőforrásokat.

Az Azure olyan szolgáltatásokat kínál, amelyek támogatják az alkalmazásfejlesztési és üzemeltetési igényeket. Az Azure infrastruktúra szolgáltatásként (IaaS) biztosít, hogy teljes mértékben szabályozhatja az alkalmazásüzemeltetését. Az Azure platform szolgáltatásként (PaaS) ajánlatai biztosítják az alkalmazások áramellátásához szükséges, teljes körűen felügyelt szolgáltatásokat. Az Azure-ban még valódi kiszolgáló nélküli üzemeltetés is létezik, ahol mindössze annyit kell tennie, hogy megírja a kódot.

![Az Azure-alkalmazások üzemeltetési beállításai](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Azure App Service

Ha azt szeretné, hogy a leggyorsabb elérési út tegye közzé a webalapú projekteket, fontolja meg az Azure App Service. Az App Service megkönnyíti a webalkalmazások kiterjesztését a mobilügyfelek támogatására és a könnyen felhasználható REST API-k közzétételére. Ez a platform biztosítja a hitelesítést a közösségi szolgáltatók, a forgalom-alapú automatikus skálázás, tesztelés éles környezetben, valamint a folyamatos és a tárolóalapú központi telepítések.

Létrehozhat webalkalmazásokat, mobilalkalmazás-háttérrendszereket és API-alkalmazásokat.

Mivel mindhárom alkalmazástípus osztozik az App Service-futásidőben, üzemeltethet egy webhelyet, támogathatja a mobilügyfeleket, és elérhetővé teheti API-jait az Azure-ban, mindezt ugyanabból a projektből vagy megoldásból. Ha többet szeretne megtudni az App Service szolgáltatásról, olvassa el [a Mi az Azure Web Apps.](../../app-service/overview.md)

Az App Service a DevOps-t szem előtt tartva lett kialakítva. Támogatja a különböző eszközök közzétételére és folyamatos integrációs központi telepítések. Ezek az eszközök közé tartozik a GitHub webhooks, Jenkins, Azure DevOps, TeamCity és mások.

Meglévő alkalmazásait áttelepítheti az App Service szolgáltatásba az [online áttelepítési eszköz segítségével.](https://appmigration.microsoft.com/)

> **Mikor kell használni:** Használja az App Service-t, amikor meglévő webalkalmazásokat telepít át az Azure-ba, és amikor teljes körűen felügyelt üzemeltetési platformra van szüksége a webalkalmazásaihoz. Az App Service-t akkor is használhatja, ha támogatnia kell a mobilügyfeleket, vagy rest API-kat szeretne elérhetővé tenni az alkalmazással.
>
> **Első lépések:** Az App Service megkönnyíti az első [webalkalmazás,](../../app-service/app-service-web-get-started-dotnet.md) [a mobilalkalmazás](../../app-service-mobile/app-service-mobile-ios-get-started.md)vagy az API-alkalmazás létrehozását és üzembe [helyezését.](../../app-service/app-service-web-tutorial-rest-api.md)
>
> **Próbálja ki most:** Az App Service lehetővé teszi egy rövid élettartamú alkalmazás kiépítését a platform kipróbálásához anélkül, hogy regisztrálna egy Azure-fiókra. Próbálja ki a [platformot, és hozza létre az Azure App Service-alkalmazást.](https://tryappservice.azure.com/)

#### <a name="azure-virtual-machines"></a>Azure-alapú virtuális gépek

Infrastruktúra szolgáltatásként (IaaS) szolgáltatóként az Azure lehetővé teszi az alkalmazás windowsos vagy Linuxos virtuális gépekre történő telepítését vagy áttelepítését. Az Azure Virtual Network használatával együtt az Azure virtuális gépek támogatják a Windows vagy Linux virtuális gépek azure-ba való telepítését. A virtuális gépek, teljes mértékben szabályozhatja a számítógép konfigurációját. A virtuális gépek használata esetén Ön a felelős a kiszolgálószoftver telepítéséért, konfigurálásáért, karbantartásáért és az operációs rendszer összes javításáért.

A virtuális gépeken lévő vezérlőszint miatt számos olyan kiszolgálói számítási feladatot futtathat az Azure-ban, amelyek nem férnek bele a PaaS-modellbe. Ezek a munkaterhelések közé tartoznak az adatbázis-kiszolgálók, a Windows Server Active Directory és a Microsoft SharePoint. További információt a Virtuális gépek [Linux](/azure/virtual-machines/linux/) vagy Windows rendszerhez tartozó dokumentációjában [talál.](/azure/virtual-machines/windows/)

> **Mikor érdemes használni:** Használja a virtuális gépeket, ha teljes körű en szeretné irányítani az alkalmazás-infrastruktúrát, vagy módosítások nélkül át szeretné telepíteni a helyszíni alkalmazás-számítási feladatokat az Azure-ba.
>
> **Első lépések:** Hozzon létre egy [Linux virtuális gép](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) vagy Windows virtuális [gép](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) az Azure Portalon.

#### <a name="azure-functions-serverless"></a>Azure Functions (kiszolgáló nélküli)

Ahelyett, hogy aggódni épület ki és kezelése egy egész alkalmazás vagy az infrastruktúra futtatásához a kódot, mi lenne, ha egyszerűen csak írni a kódot, és azt futtatni válaszul események vagy ütemezés szerint?  [Az Azure Functions](../../azure-functions/functions-overview.md) egy "kiszolgáló nélküli" stílusú ajánlat, amely lehetővé teszi, hogy csak a szükséges kódot írja meg. A Functions segítségével http-kérelmekkel, webhookokkal, felhőszolgáltatási eseményekkel vagy ütemezés szerint aktiválhatja a kódfuttatást. A kívánt fejlesztési nyelven is kódolhat, például C,\#F,\#Node.js, Python vagy PHP. A fogyasztásalapú számlázással csak a kód végrehajtásának idejéért kell fizetnie, és szükség szerint az Azure-skálázást.

> **Mikor érdemes használni:** Használja az Azure Functions, ha a kód, amely által aktivált más Azure-szolgáltatások, webalapú események, vagy ütemezés szerint. A Függvények akkor is használhatók, ha nincs szüksége egy teljes üzemeltetett projekt terhelésére, vagy ha csak a kód futási idejéért szeretne fizetni. További információ: [Azure Functions Overview](../../azure-functions/functions-overview.md).
>
> **Első lépések:** Kövesse a Funkciók rövid útmutatót [az első függvény létrehozásához](../../azure-functions/functions-create-first-azure-function.md) a portálról.
>
> **Próbálja ki most:** Az Azure Functions lehetővé teszi a kód futtatását anélkül, hogy regisztrálna egy Azure-fiókra. Próbálja ki most, és [hozza létre az első Azure-függvényt.](https://tryappservice.azure.com/)

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Az Azure Service Fabric egy elosztott rendszerplatform. Ez a platform megkönnyíti a méretezhető és megbízható mikroszolgáltatások létrehozásának, csomagolásának, üzembe helyezésének és kezelésének. Átfogó alkalmazáskezelési képességeket is biztosít, például:

* Kiépítés
* Telepítése
* Figyelés
* Frissítés/javítás
* Törlés

A gépek megosztott készletén futó alkalmazások kis méretűek lehetnek, és szükség szerint több száz vagy több ezer gépre méretezhetők.

A Service Fabric támogatja a WebAPI-t a .NET (OWIN) és ASP.NET Core webes felülettel. SDK-kat biztosít a Linuxon való szolgáltatások kiépítéséhez a .NET Core és a Java nyelven egyaránt. A Service Fabric ről a [Service Fabric dokumentációjában](https://docs.microsoft.com/azure/service-fabric/)olvashat bővebben.

> **Mikor kell használni:** A Service Fabric jó választás, ha egy alkalmazást hoz létre, vagy egy meglévő alkalmazás mikroszolgáltatási architektúra használatával újraírása. Használja a Service Fabric, ha több ellenőrzést, vagy közvetlen hozzáférést biztosít az alapul szolgáló infrastruktúra.
>
> **Első lépések:** [Hozza létre az első Azure Service Fabric-alkalmazást.](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md)

### <a name="enhance-your-applications-with-azure-services"></a>Bővítse alkalmazásait az Azure-szolgáltatásokkal

Az alkalmazásüzemeltetéssel együtt az Azure olyan szolgáltatási ajánlatokat is kínál, amelyek javíthatják a funkcionalitást. Az Azure javíthatja az alkalmazások fejlesztését és karbantartását is, mind a felhőben, mind a helyszínen.

#### <a name="hosted-storage-and-data-access"></a>Üzemeltetett tárhely és adathozzáférés

A legtöbb alkalmazásnak adatokat kell tárolnia, ezért azonban úgy dönt, hogy az alkalmazást az Azure-ban tárolja, fontolja meg az alábbi tárolási és adatszolgáltatások egyikét vagy több.

- **Azure Cosmos DB**: Globálisan elosztott, többmodelles adatbázis-szolgáltatás. Ez az adatbázis lehetővé teszi, hogy rugalmasan skálázhatja az átviteli és tárolási átviteli kapacitást és a tárolást tetszőleges számú földrajzi régióban egy átfogó SLA-val.

  > **Mikor kell használni:** Ha az alkalmazásnak több jól definiált konzisztenciamodellel rendelkező dokumentum-, tábla- vagy gráf-adatbázisokra van szüksége, beleértve a MongoDB-adatbázisokat is.
  >
  > **Első lépések:** [Azure Cosmos DB webalkalmazás létrehozása.](../../cosmos-db/create-sql-api-dotnet.md) Ha MongoDB-fejlesztő, olvassa [el a MongoDB webalkalmazás létrehozása az Azure Cosmos DB-vel.](../../cosmos-db/create-mongodb-dotnet.md)

- **Azure Storage:** Tartós, magas rendelkezésre állású tárhelyet kínál blobok, várólisták, fájlok és egyéb nem kapcsolati adatok számára. A tárolás biztosítja a virtuális gépek tárolási alapját.

  > **Mikor használja**a következőt: Ha az alkalmazás nem relációs adatokat, például kulcsérték-párokat (táblákat), blobokat, fájlmegosztásokat vagy üzeneteket (várólistákat) tárol.
  >
  > **Első lépések**: Válasszon az alábbi típusú tárolók közül: [blobok,](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) [táblák,](../../cosmos-db/table-storage-how-to-use-dotnet.md) [várólisták](../../storage/queues/storage-dotnet-how-to-use-queues.md)vagy [fájlok.](../../storage/files/storage-dotnet-how-to-use-files.md)

- **Azure SQL Database:** A Microsoft SQL Server motor Azure-alapú verziója relációs táblázatos adatok tárolására a felhőben. Az SQL Database kiszámítható teljesítményt, méretezhetőséget és állásidő nélküli méretezhetőséget, üzletmenet-folytonosságot és adatvédelmet biztosít.

  > **Mikor kell használni:** Ha az alkalmazás hivatkozási integritással, tranzakciós támogatással és TSQL-lekérdezések támogatásával rendelkező adattárolást igényel.
  >
  > **Első lépések**: [Sql-adatbázis létrehozása percek alatt az Azure Portal használatával.](../../sql-database/sql-database-get-started.md)


Az [Azure Data Factory](../../data-factory/introduction.md) segítségével áthelyezheti a meglévő helyszíni adatokat az Azure-ba. Ha még nem áll készen az adatok felhőbe való áthelyezésére, az Azure App Service [hibrid kapcsolatok](../../app-service/app-service-hybrid-connections.md) lehetővé teszi az App Service üzemeltetett alkalmazásának helyszíni erőforrásokhoz való csatlakoztatását. Az Azure-adatokhoz és -tárolási szolgáltatásokhoz is csatlakozhat a helyszíni alkalmazásokból.

#### <a name="docker-support"></a>Docker-támogatás

Az operációs rendszer virtualizálásának egy formája, a Docker-tárolók hatékonyabb és kiszámíthatóbb módon telepítheti az alkalmazásokat. A konténeres alkalmazások ugyanúgy működnek a termelésben, mint a fejlesztési és tesztelési rendszerek. A tárolókat a szabványos Docker-eszközök használatával kezelheti. Meglévő készségeit és népszerű nyílt forráskódú eszközeit használhatja a tárolóalapú alkalmazások azure-beli üzembe helyezéséhez és kezeléséhez.

Az Azure számos lehetőséget kínál a tárolók használatára az alkalmazásokban.

- **Azure Docker virtuálisgép-bővítmény:** Lehetővé teszi, hogy konfigurálja a virtuális gép Docker-eszközökkel, hogy egy Docker-gazdagépként működjön.

  > **Mikor érdemes használni:** Ha konzisztens tárolóközponti telepítéseket szeretne létrehozni az alkalmazásokhoz egy virtuális gépen, vagy ha [a Docker Compose-t](https://docs.docker.com/compose/overview/)szeretné használni.
  >
  > **Első lépések:** [Hozzon létre egy Docker-környezetet az Azure-ban a Docker virtuálisgép-bővítmény használatával.](../../virtual-machines/virtual-machines-linux-dockerextension.md)

- **Azure Kubernetes szolgáltatás:** Lehetővé teszi, hogy hozzon létre, konfigurálja és kezelje a fürt a virtuális gépek, amelyek előre konfigurálva a tárolóalapú alkalmazások futtatásához. Ha többet szeretne megtudni az Azure Kubernetes szolgáltatásról, olvassa el az [Azure Kubernetes-szolgáltatás bevezetés című témakörét.](../../aks/intro-kubernetes.md)

  > **Mikor kell használni:** Ha termelésre kész, méretezhető környezeteket kell készítenie, amelyek további ütemezési és felügyeleti eszközöket biztosítanak, vagy ha docker-fürtöt telepít.
  >
  > **Első lépések**: [Kubernetes-szolgáltatásfürt telepítése](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Docker-gép:** Lehetővé teszi a Docker-motor virtuális gazdagépeken való telepítését és kezelését a docker-machine parancsok használatával.

  >**Mikor kell használni:** Ha gyorsan prototípus egy alkalmazást egyetlen Docker-gazdagép létrehozásával.

- **Egyéni Docker-rendszerkép az App Service:** Lehetővé teszi, hogy a Docker-tárolók egy tároló rendszerleíró adatbázisból vagy egy ügyfél tárolóból, amikor egy webalkalmazás linuxos üzembe helyezése.

  > **Mikor kell használni:** Ha egy webalkalmazás linuxos üzembe helyezése a Docker-rendszerkép.
  >
  > **Első lépések:** [Egyéni Docker-rendszerképet használjon az App Service linuxos szolgáltatásához.](../../app-service/containers/quickstart-docker-go.md)

### <a name="authentication"></a>Hitelesítés

Rendkívül fontos, hogy ne csak tudja, ki használja az alkalmazásokat, hanem hogy megakadályozza az erőforrásokhoz való jogosulatlan hozzáférést. Az Azure számos lehetőséget kínál az alkalmazásügyfelek hitelesítésére.

- **Azure Active Directory (Azure AD)**: A Microsoft több-bérlős, felhőalapú identitás- és hozzáférés-kezelési szolgáltatás. Egyszeri bejelentkezéssel (SSO) adhat hozzá az alkalmazásokhoz az Azure AD-vel való integrációval. A címtár-tulajdonságok közvetlenül az Azure AD Graph API használatával vagy a Microsoft Graph API használatával érhető el. Az Azure AD-támogatással integrálható az OAuth2.0 engedélyezési keretrendszer és az Open ID Connect natív HTTP/REST-végpontok és a többplatformos Azure AD hitelesítési kódtárak használatával.

  > **Mikor érdemes használni:** Ha egyszeri használatot szeretne biztosítani, dolgozzon a Graph-alapú adatokkal, vagy hitelesítse a tartományalapú felhasználókat.
  >
  > **Első lépések**: További információaz [Azure Active Directory fejlesztői útmutatójában](../../active-directory/develop/v2-overview.md)olvashat.

- **App Service-hitelesítés:** Ha az App Service-t választja az alkalmazás üzemeltetéséhez, az Azure AD-hez beépített hitelesítési támogatást is kap, valamint közösségi identitásszolgáltatókat – például a Facebookot, a Google-t, a Microsoftot és a Twittert.

  > **Mikor érdemes használni:** Ha engedélyezni szeretné a hitelesítést egy App Service-alkalmazásban az Azure AD, a közösségi identitásszolgáltatók vagy mindkettő használatával.
  >
  > **Első lépések**: Ha többet szeretne megtudni az App Service-hitelesítésről, olvassa el [a Hitelesítés és engedélyezés az Azure App Service-ben című témakört.](../../app-service/overview-authentication-authorization.md)

Ha többet szeretne megtudni az Azure-ban a biztonsági gyakorlati tanácsokról, olvassa el az [Azure biztonsági gyakorlati tanácsait és mintáit.](../../security/fundamentals/best-practices-and-patterns.md)

### <a name="monitoring"></a>Figyelés

Az Azure-ban futó alkalmazással figyelemmel kell kísérnie a teljesítményt, figyelnie kell a problémákat, és meg kell néznie, hogy az ügyfelek hogyan használják az alkalmazást. Az Azure számos figyelési lehetőséget biztosít.

-   **Application Insights:** Egy Azure által üzemeltetett bővíthető elemzési szolgáltatás, amely integrálható a Visual Studióval az élő webalkalmazások figyeléséhez. Ez biztosítja azokat az adatokat, amelyek az alkalmazások teljesítményének és használhatóságának folyamatos javításához szükséges. Ez a javulás akkor jelentkezik, hogy az alkalmazások az Azure-ban, vagy sem.

    >**Első lépések**: Kövesse az [Application Insights oktatóanyagát.](../../azure-monitor/app/app-insights-overview.md)

-   **Azure Monitor:** Olyan szolgáltatás, amely segít viziviziálni, lekérdezés, útvonal, archiválás, és az Azure-infrastruktúrával és -erőforrásokkal létrehozott metrikák és naplók megjelenítését. A Figyelő egyetlen forrás az Azure-erőforrások figyeléséhez, és az Azure Portalon látható adatnézeteket biztosítja.

    >**Első lépések**: [Ismerkedés az Azure Monitorral.](../../monitoring-and-diagnostics/monitoring-get-started.md)

### <a name="devops-integration"></a>DevOps-integráció

Akár virtuális gépek et létesít, akár folyamatos integrációval teszi közzé a webalkalmazásokat, az Azure integrálja a legtöbb népszerű DevOps-eszközzel. A már meglévő eszközökkel dolgozhat, és az olyan eszközök támogatásával maximalizálhatja a meglévő élményét, mint például:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity között
* Ansible
* Azure DevOps

> **Első lépések**: Az App Service-alkalmazások DevOps-beállításainak megtekintéséhez olvassa el [az Azure App Service folyamatos üzembe helyezésének témakört.](../../app-service/deploy-continuous-deployment.md)
>
> **Próbálja ki most:** [Próbálja ki a DevOps-integrációk közül néhányat.](https://azure.microsoft.com/try/devops/)


## <a name="azure-regions"></a>Azure-régiók

Az Azure egy globális felhőplatform, amely a világ számos régiójában általánosan elérhető. Amikor üzembe létesít egy szolgáltatást, alkalmazást vagy virtuális gép az Azure-ban, a rendszer kéri, hogy válasszon ki egy régiót. Ez a régió egy adott adatközpontot jelöl, ahol az alkalmazás fut, vagy ahol az adatok tárolódnak. Ezek a régiók megfelelnek az [Azure-régiók](https://azure.microsoft.com/regions/) oldalon közzétett meghatározott helyeknek.

### <a name="choose-the-best-region-for-your-application-and-data"></a>Válassza ki az alkalmazásés az adatok számára legmegfelelőbb régiót

Az Azure használatának egyik előnye, hogy az alkalmazásokat világszerte különböző adatközpontokba telepítheti. A választott régió hatással lehet az alkalmazás teljesítményére. Ha például olyan régiót választ, amely közelebb áll a legtöbb ügyfélhez, akkor csökkentheti a hálózati kérelmek késését. Előfordulhat, hogy azt is szeretné kiválasztani a régiót, hogy megfeleljen az alkalmazás bizonyos országokban/régiókban történő terjesztésére vonatkozó jogi követelményeknek. Mindig ajánlott az alkalmazásadatok at ugyanabban az adatközpontban vagy egy adatközpontban tárolni a lehető legközelebb az alkalmazást üzemeltető adatközponthoz.

### <a name="multi-region-apps"></a>Többrégiós alkalmazások

Bár valószínűtlen, nem lehetetlen, hogy egy teljes adatközpont offline állapotba kerüljön egy természeti katasztrófa vagy internetes hiba miatt. Ajánlott eljárás a létfontosságú üzleti alkalmazások több adatközpontban való üzemeltetéséhez a maximális rendelkezésre állás biztosítása érdekében. Több régió használata is csökkentheti a késést a globális felhasználók számára, és további rugalmasságot biztosít az alkalmazások frissítésekor.

Egyes szolgáltatások, például a Virtuálisgép és az App Services, az [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) használatával engedélyezheti a többrégiós támogatást a régiók közötti feladatátvételsel a magas rendelkezésre állású vállalati alkalmazások támogatásához. Például az [Azure referenciaarchitektúra: Webalkalmazás futtatása több régióban.](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

>**Mikor kell használni:** Ha a vállalati és magas rendelkezésre állású alkalmazások, amelyek részesülnek a feladatátvétel és a replikáció előnyeit.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Hogyan kezelhetem az alkalmazásaimat és a projektjeimet?

Az Azure számos élményt nyújt az Azure-erőforrások, alkalmazások és projektek létrehozásához és kezeléséhez – mind programozott módon, mind az [Azure Portalon.](https://portal.azure.com/)

### <a name="command-line-interfaces-and-powershell"></a>Parancssori összeköttetések és PowerShell

Az Azure kétféleképpen kezelheti alkalmazásait és szolgáltatásait a parancssorból. Használhatja az olyan eszközöket, mint a Bash, a Terminal, a parancssori parancssori parancssori eszköz, vagy a választott parancssori eszköz. Általában ugyanazokat a feladatokat a parancssorból is eltudja végezni, mint az Azure Portalon – például virtuális gépek, virtuális hálózatok, webalkalmazások és egyéb szolgáltatások létrehozását és konfigurálását.

-   [Azure parancssori felület (CLI)](../../xplat-cli-install.md): Lehetővé teszi, hogy csatlakozzon egy Azure-előfizetéshez, és a parancssorból különböző feladatokat programozzon az Azure-erőforrásokkal szemben.

-   [Azure PowerShell:](../../powershell-install-configure.md)Modulok készletét biztosítja parancsmagokkal, amelyek lehetővé teszik az Azure-erőforrások windowsos PowerShell használatával történő kezelését.

### <a name="azure-portal"></a>Azure portál

Az [Azure Portal](https://portal.azure.com) egy webalapú alkalmazás. Az Azure Portal segítségével azure-erőforrásokat és -szolgáltatásokat hozhat létre, kezelhet és távolíthat el. A következőket tartalmazza:

* Konfigurálható irányítópult
* Azure erőforrás-kezelő eszközök
* Hozzáférés az előfizetési beállításokhoz és a számlázási információkhoz. További információt az [Azure Portal áttekintése című témakörben talál.](../../azure-portal-overview.md)

### <a name="rest-apis"></a>REST API-k

Az Azure rest API-k készletére épül, amelyek támogatják az Azure portal felhasználói felületét. A REST API-k többsége is támogatott, hogy lehetővé tegye az Azure-erőforrások és -alkalmazások programozással történő kiépítését és kezelését bármely internetes eszközről. A REST API-dokumentáció teljes készletét az [Azure REST SDK-hivatkozásban találja.](https://docs.microsoft.com/rest/api/)

### <a name="apis"></a>API-k

A REST API-k mellett számos Azure-szolgáltatás lehetővé teszi az alkalmazások erőforrásainak programozott kezelését platformspecifikus Azure SDK-k használatával, beleértve a következő fejlesztési platformok SDK-it is:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [Php](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Ugrás](https://docs.microsoft.com/azure/go)

Az olyan szolgáltatások, mint [a Mobilalkalmazások](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) és [az Azure Media Services](../../media-services/previous/media-services-dotnet-how-to-use.md) ügyféloldali SDK-kat biztosítanak, hogy a webes és mobilügyfél-alkalmazásokból származó szolgáltatások hozhassák hozzá.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Az alkalmazás Azure-on való futtatása valószínűleg több Azure-szolgáltatással való munkát is magában foglal. Ezek a szolgáltatások ugyanazt az életciklust követik, és logikus egységként tekinthetők. Egy webalkalmazás például használhat webalkalmazásokat, SQL-adatbázist, storageot, Azure Cache for Redis és Azure Content Delivery Network szolgáltatásokat. [Az Azure Resource Manager](../../azure-resource-manager/management/overview.md) lehetővé teszi, hogy az alkalmazás erőforrásait csoportként dolgozzon. Egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti az összes erőforrást.

A kapcsolódó erőforrások logikai csoportosítása és kezelése mellett az Azure Resource Manager olyan üzembe helyezési lehetőségeket is tartalmaz, amelyek lehetővé teszik a kapcsolódó erőforrások üzembe helyezésének és konfigurálásának testreszabását. Használhatja például az Erőforrás-kezelő központi telepítését és konfigurálását. Ez az alkalmazás állhat több virtuális gépek, a terheléselosztó, és egy Azure SQL-adatbázis egyetlen egységként.

Ezeket a központi telepítéseket egy Azure Resource Manager-sablon használatával fejlesztheti, amely egy JSON-formátumú dokumentum. A sablonok segítségével definiálhatja a központi telepítést, és parancsfájlok helyett deklaratív sablonok használatával kezelheti az alkalmazásokat. A sablonok működhetnek a különböző környezetekben, például a tesztelés, az előkészítés és az éles környezetben. Például sablonok segítségével hozzáadhat egy gombot egy GitHub-tárlaphoz, amely egyetlen kattintással telepíti a kódot a tárházban az Azure-szolgáltatások egy készletére.

> **Mikor érdemes használni:** Erőforrás-kezelő sablonok használata, ha szeretne egy sablon-alapú központi telepítés az alkalmazáshoz, amely programozott módon kezelheti a REST API-k, az Azure CLI és az Azure PowerShell használatával.
>
> **Első lépések**: A sablonok használatának megkezdéséhez olvassa el [az Azure Resource Manager-sablonok készítése című témakört.](../../resource-group-authoring-templates.md)

## <a name="understanding-accounts-subscriptions-and-billing"></a>A fiókok, az előfizetések és a számlázás ismertetése

Mint fejlesztők, szeretjük belemerülni a kódot, és próbálja meg az induláshoz, amilyen gyorsan csak lehetséges, hogy alkalmazásaink futnak. Természetesen azt szeretnénk, hogy a lehető legegyszerűbben kezdjen el dolgozni az Azure-ban. A könnyebbik egyszerűsítés érdekében az Azure [ingyenes próbaverziót](https://azure.microsoft.com/free/)kínál. Egyes szolgáltatások rendelkeznek egy "Próbálja ki ingyen" funkcióval is, például [az Azure App Service,](https://tryappservice.azure.com/)amely nem igényel még egy fiókot sem. Bármennyire is szórakoztató az alkalmazás azure-beli kódolása és üzembe helyezése, fontos, hogy az Azure működésének megértéséhez is szánjon egy kis időt. Pontosabban, meg kell értenie, hogyan működik a felhasználói fiókok, előfizetések és számlázási szempontból.

### <a name="what-is-an-azure-account"></a>Mi az az Azure-fiók?

Azure-előfizetés létrehozásához vagy az azzal való munkához Azure-fiókkal kell rendelkeznie. Az Azure-fiók egyszerűen egy identitás az Azure AD-ben vagy egy címtárban, például egy munkahelyi vagy iskolai szervezetben, amely az Azure AD bízik. Ha nem tartozik egy ilyen szervezethez, bármikor létrehozhat egy előfizetést a Microsoft-fiókjával, amelyet az Azure AD megbízhatónak tart. Ha többet szeretne tudni a helyszíni Windows Server Active Directory és az Azure AD integrálásáról, olvassa el [a helyszíni identitások integrálása az Azure Active Directoryval.](../../active-directory/hybrid/whatis-hybrid-identity.md)

Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. További információ: [Hogyan társítva vannak az Azure-előfizetések az Azure Active Directoryhoz.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

Az egyes Azure-fiókidentitások, más néven *felhasználók*meghatározása mellett *csoportokat* is definiálhat az Azure AD-ben. A felhasználói csoportok létrehozása jó módszer az erőforrásokhoz való hozzáférés kezelésére egy előfizetésben szerepköralapú hozzáférés-vezérlés (RBAC) használatával. A csoportok létrehozásáról a [Csoport létrehozása az Azure Active Directory előzetes verziójában című témakörben](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)olvashat. Csoportokat is létrehozhat és [kezelhet a PowerShell használatával.](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)

### <a name="manage-your-subscriptions"></a>Feliratkozások kezelése

Az előfizetés az Azure-szolgáltatások logikai csoportosítása, amely egy Azure-fiókhoz kapcsolódik. Egyetlen Azure-fiók több előfizetést is tartalmazhat. Az Azure-szolgáltatások számlázása előfizetésenként történik. Az elérhető előfizetési ajánlatok típus onkénti listáját a [Microsoft Azure ajánlat részletei című témakörben találja.](https://azure.microsoft.com/support/legal/offer-details/) Az Azure-előfizetések rendelkeznek egy fiókrendszergazdával, aki teljes hozzáféréssel rendelkezik az előfizetés felett. Emellett egy szolgáltatás-rendszergazdával is rendelkeznek, aki az előfizetésben lévő összes szolgáltatást felügyeli. A klasszikus előfizetés-rendszergazdákról az [Azure-előfizetés-rendszergazdák hozzáadása vagy módosítása](../../cost-management-billing/manage/add-change-subscription-administrator.md)című témakörben talál további információt. Az egyes fiókok [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)használatával részletesen szabályozhatók az Azure-erőforrásokfelett.

#### <a name="resource-groups"></a>Erőforráscsoportok

Amikor új Azure-szolgáltatásokat hoz ki, ezt egy adott előfizetésben teszi. Egyes Azure-szolgáltatások, amelyeket erőforrásoknak is neveznek, egy erőforráscsoport környezetében jönnek létre. Az erőforráscsoportok megkönnyítik az alkalmazás erőforrásainak üzembe helyezését és kezelését. Az erőforráscsoportnak tartalmaznia kell az alkalmazás hoz a di. Erőforrások at helyezhet át az erőforráscsoportok között, sőt a különböző előfizetések között. Az erőforrások áthelyezéséről az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe (Erőforrások áthelyezése)](../../azure-resource-manager/management/move-resource-group-and-subscription.md)témakörben olvashat.

Az Azure Resource Explorer egy nagyszerű eszköz az előfizetésben már létrehozott erőforrások megjelenítéséhez. További információ: [Az Azure Resource Explorer használata az erőforrások megtekintéséhez és módosításához](../../resource-manager-resource-explorer.md)című témakörben olvashat.

#### <a name="grant-access-to-resources"></a>Hozzáférést biztosít az erőforrásokhoz

Ha engedélyezi az Azure-erőforrásokhoz való hozzáférést, mindig ajánlott biztosítani a felhasználók számára az adott feladat elvégzéséhez szükséges legkevesebb jogosultságot.

- **Szerepköralapú hozzáférés-vezérlés (RBAC)**: Az Azure-ban hozzáférést biztosíthat a felhasználói fiókokhoz (egyszerű) egy adott hatókörben: előfizetés, erőforráscsoport vagy az egyes erőforrások. Az RBAC lehetővé teszi az erőforrások erőforráscsoportba való üzembe helyezését, és engedélyek et adhat meg egy adott felhasználónak vagy csoportnak. Azt is lehetővé teszi, hogy korlátozza a hozzáférést csak a cél erőforráscsoporthoz tartozó erőforrások. Egyetlen erőforráshoz, például virtuális géphez vagy virtuális hálózathoz is hozzáférést adhat. A hozzáférés megadásához szerepkört kell rendelnie a felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz. Számos előre definiált szerepkör létezik, és saját egyéni szerepköröket is definiálhat. További információ: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md)

  > **Mikor kell használni:** Ha részletes hozzáférés-kezelésre van szüksége a felhasználók és csoportok számára, vagy ha egy felhasználót egy előfizetés tulajdonosának kell tennie.
  >
  > **Első lépések:** További információ: [Hozzáférés kezelése az RBAC használatával és az Azure Portalon.](../../role-based-access-control/role-assignments-portal.md)

- **Egyszerű szolgáltatásobjektumok:** A felhasználó egyszerű szolsongése és csoportjai számára való hozzáférés biztosítása mellett ugyanazt a hozzáférést biztosíthatja egy egyszerű szolgáltatáshoz.

  > **Mikor kell használni:** Ha programozott módon kezeli az Azure-erőforrásokat, vagy hozzáférést biztosít az alkalmazásokhoz. További információt az Active Directory alkalmazás és egyszerű szolgáltatás létrehozása című [témakörben talál.](../../active-directory/develop/howto-create-service-principal-portal.md)

#### <a name="tags"></a>Címkék

Az Azure Resource Manager lehetővé teszi, hogy egyéni címkéket rendeljen az egyes erőforrásokhoz. Címkék, amelyek kulcs-érték párok, hasznos lehet, ha kell szervezni a számlázási vagy figyelési erőforrásokat. A címkék lehetővé teszik az erőforrások több erőforráscsoport közötti nyomon követését. A címkéket a következő módokon rendelheti hozzá:

* A portálon
* Az Azure Resource Manager sablonban
* A REST API használata
* Az Azure parancssori felületének használata
* A PowerShell használata

Minden erőforráshoz több címkét is hozzárendelhet. További információ: [Címkék használata az Azure-erőforrások rendszerezéséhez.](../../resource-group-using-tags.md)

### <a name="billing"></a>Számlázás

A helyszíni számítástechnikáról a felhőalapú szolgáltatásokra való áttérés során a szolgáltatáshasználat és a kapcsolódó költségek nyomon követése és becslése jelentős problémát jelent. Fontos megbecsülni, hogy milyen új erőforrások költsége fut havi rendszerességgel. Azt is kivetítheti, hogy a számlázás hogyan néz ki egy adott hónapra az aktuális kiadások alapján.

#### <a name="get-resource-usage-data"></a>Erőforrás-használati adatok beszereznie

Az Azure számlázási REST API-k készletét biztosítja, amelyek hozzáférést biztosítanak az Azure-előfizetések erőforrás-felhasználásához és metaadat-információihoz. Ezek a számlázási API-k lehetővé teszik az Azure-költségek jobb előrejelzését és kezelését. A kiadásokat óránkénti lépésekben követheti nyomon és elemezheti, és költési riasztásokat hozhat létre. A jövőbeli számlázást az aktuális használati trendek alapján is előre jelezheti.

>**Első lépések:** Ha többet szeretne megtudni a számlázási API-k használatáról, olvassa el az [Azure számlázási használati és RateCard API-k áttekintése című témakört.](../../cost-management-billing/manage/usage-rate-card-overview.md)

#### <a name="predict-future-costs"></a>A jövőbeli költségek előrejelzése

Bár a költségek idő előtti becslése kihívást jelent, az Azure olyan eszközökkel rendelkezik, amelyek segíthetnek. Ez egy [árképzési kalkulátor,](https://azure.microsoft.com/pricing/calculator/) amely segít megbecsülni a telepített erőforrások költségét. A portál számlázási erőforrásait és a Számlázási REST API-kat is használhatja a jövőbeli költségek becsléséhez az aktuális felhasználás alapján.

>**Első lépések**: Tekintse meg [az Azure számlázási használatát és a RateCard API-k áttekintését.](../../cost-management-billing/manage/usage-rate-card-overview.md)

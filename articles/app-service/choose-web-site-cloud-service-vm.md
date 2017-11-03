---
title: "Az Azure App Service, a virtuális gépek, a Service Fabric és a Cloud Services összehasonlítása |} Microsoft Docs"
description: "Megtudhatja, hogyan válassza az Azure App Service, a virtuális gépek, a Service Fabric és a Felhőszolgáltatások webalkalmazások üzemeltetéséhez."
services: app-service\web, virtual-machines, cloud-services
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: edd5099d2804fdb5867b4be5b11a361004db1665
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Az Azure App Service, a virtuális gépek, a Service Fabric és a Cloud Services összehasonlítása
## <a name="overview"></a>Áttekintés
Azure gazdagép webhelyek számos lehetőséget kínál: [Azure App Service][Azure App Service], [virtuális gépek][Virtual Machines], [Service Fabric][Service Fabric], és [Felhőszolgáltatások][Cloud Services]. Ez a cikk segít ismerni a lehetőségeket, és a webalkalmazás a megfelelő választás.

Az Azure App Service legtöbb webalkalmazások a legjobb választás. Üzembe helyezési és kezelési integrálva vannak a platform, helyek gyorsan kezelésére méretezhetők nagy forgalomterhelés és a beépített terhelés és a forgalom-kezelő magas rendelkezésre állás biztosításához. Meglévő helyek egyszerűen az Azure App Service áthelyezheti egy [online áttelepítési eszköz](https://www.migratetoazure.net/), nyílt forráskódú alkalmazás használja a webes alkalmazás gyűjteményből, vagy hozzon létre egy új helyet, a keretrendszer és a kiválasztott eszközök használatával. A [WebJobs] [ WebJobs] funkcióval egyszerűen háttérfeldolgozása feladat hozzáadása az App Service webalkalmazás.

A Service Fabric akkor hasznos, ha létrehoz egy új alkalmazást vagy újraírása mikroszolgáltatási architektúrák használatára, egy meglévő alkalmazást. Alkalmazásokat, egy megosztott készletéhez gépeket futtathatnak, elindíthatja a kis és nagy méretezéssel, több száz vagy ezer gépek igény szerint milyen mértékben. Állapotalapú szolgáltatások könnyen következetesen és megbízhatóan tárolja az alkalmazás állapota, és automatikusan kezeli a Service Fabric szolgáltatás particionálás, a méretezés és a rendelkezésre állási meg.  A Service Fabric is támogatja WebAPI az Open Web Interface .NET (OWIN) és az ASP.NET Core.  App Service képest, a Service Fabric is biztosít további szabályozni, és közvetlen hozzáférést, az alkalmazás mögötti infrastruktúra. Azokat a kiszolgálók távoli is, vagy konfigurálja a kiszolgáló indítása feladatokat. Felhőszolgáltatások szabályozására könnyű használat, illetve a Service Fabric hasonló, de most már egy régebbi szolgáltatás és az új fejlesztési érdemes a Service Fabric.

Ha egy meglévő alkalmazást, futtassa az App Service vagy a Service Fabric jelentős módosításai újraindexelést igénylő, megadhatja, virtuális gépek áttelepítése a felhőbe egyszerűsítése érdekében. Azonban megfelelő konfigurálása, biztonságossá tétele és karbantartása virtuális gépek csak sokkal több időt és Azure App Service és a Service Fabric képest informatikai segítséget. Ha tervezi az Azure virtuális gépek, győződjön meg arról, vegye figyelembe a javítás, frissítése és a virtuális gép környezete kezeléséhez szükséges folyamatos karbantartási munkát. Az Azure virtuális gépek infrastruktúra,--szolgáltatás (IaaS), míg az App Service és a Service Fabric Platform,--szolgáltatás (Paas). 

## <a name="features"></a>Szolgáltatások összehasonlítása
Az alábbi táblázat összehasonlítja az App Service, a Cloud Services, a virtuális gépek és a Service Fabric képességeit segít meghozni a legjobb választás. Az SLA-t az egyes lehetőségek kapcsolatos aktuális információkért lásd: [Azure szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

| Szolgáltatás | App Service (webalkalmazások) | Cloud Services (webes szerepkörök) | Virtuális gépek | Service Fabric | Megjegyzések |
| --- | --- | --- | --- | --- | --- |
| Szinte azonnali telepítés |X | | |X |Egy alkalmazás vagy egy alkalmazás frissítése üzembe egy felhőalapú szolgáltatás, vagy egy virtuális gép létrehozása több percig legalább; egy alkalmazás központi telepítése másodpercet vesz igénybe. |
| Vertikális felskálázás nagyobb gépekre helyezze üzembe újra nélkül |X | | |X | |
| Web server-példány osztozik az tartalmát és beállításait, ami azt jelenti, hogy nem kell telepíteni, vagy konfigurálja újra, méretezni. |X | | |X | |
| Több központi telepítési környezetekben (üzemi és átmeneti) |X |X | |X |A Service Fabric lehetővé teszi az alkalmazások több környezet vagy az alkalmazás-párhuzamos különböző verzióinak központi telepítése. |
| Az operációs rendszer automatikus frissítéseit |X |X | | |Részlegesen keresztül javítás Vezénylési alkalmazás (POA) és teljes mértékben a jövőben. |
| Váltás zökkenőmentes platform (32 bites és 64 bites közötti egyszerű váltást) |X |X | | | |
| Kód a GIT-, FTP telepítése |X | |X | | |
| Kód a Web Deploy telepítése |X | |X | |Cloud Services támogatja a Web Deploy segítségével telepítheti a frissítéseket az egyes szerepkörpéldányokat. Azonban nem használható egy szerepkör kezdeti telepítését, és a frissítési használatakor a Web Deploy rendelkezik-e a szerepkör minden példányára külön-külön telepíteni. Több példánya van szükség ahhoz, hogy a felhőalapú szolgáltatás garantált szolgáltatási szintje, éles környezetben. |
| WebMatrix támogatása |X | |X | | |
| Például a Service Bus, a tárolás, az SQL-adatbázis-szolgáltatásokhoz való hozzáférés |X |X |X |X | |
| Állomás web vagy webes szolgáltatások réteg többrétegű-architektúra |X |X |X |X | |
| Állomás középső réteg többrétegű-architektúra |X |X |X |X |App Service web apps könnyen tárolhatja, a REST API középső réteggel, és a [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) szolgáltatás tárolhatja, háttérben történő feldolgozási feladatot. Webjobs-feladatok egy külön webhelyen elérhető független méretezhetősége a réteghez való futtatása. |
| Integrált MySQL,--szolgáltatás támogatása |X |X |X | |Felhőszolgáltatások integrálhatja MySQL,--szolgáltatás ClearDB tartozó ajánlatok keresztül, de nem az Azure portál munkafolyamat részeként. |
| Támogatja az ASP.NET, a klasszikus ASP, Node.js, PHP, Python |X |X |X |X |A Service Fabric támogatja a létrehozását a webes előtér-használatával [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) vagy bármilyen (Node.js, Java, stb.) telepítheti egy [Vendég végrehajtható](../service-fabric/service-fabric-deploy-existing-app.md). |
| Helyezze üzembe újra nélkül több példány horizontálisan |X |X |X |X |Virtuális gépek több példányt lehet horizontálisan, de a rajtuk futó szolgáltatások kell írni a kibővített kezelni. Be kell állítania egy terheléselosztót történő átirányításához kérelmek gépein, és a karbantartás vagy hardveres hibák miatt összes példány egyidejű újraindítást megelőzése érdekében Affinitáscsoport létrehozásához. |
| SSL támogatása |X |X |X |X |App Service web Apps az egyéni tartománynevek SSL csak Basic és Standard módban támogatott. A webalkalmazásokkal SSL használatával kapcsolatos információkért lásd: [konfigurálása az Azure-webhely SSL-tanúsítvány](app-service-web-tutorial-custom-ssl.md). |
| Visual Studio-integráció |X |X |X |X | |
| Távoli hibakeresés |X |X |X | | |
| A TFS-sel kód telepítése |X |X |X |X | |
| A hálózatelkülönítés [Azure-beli virtuális hálózathoz](/azure/virtual-network/) |X |X |X |X |Lásd még: [Azure-webhelyek virtuális hálózati integráció](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| Támogatja az [az Azure Traffic Manager](/azure/traffic-manager/) |X |X |X |X | |
| Integrált Végpontmonitoring kijelző |X |X |X | | |
| Kiszolgálók távoli asztal eléréséhez | |X |X |X | |
| Minden egyéni MSI telepítése | |X |X |X |A Service Fabric lehetővé teszi a végrehajtható fájlt a [Vendég végrehajtható](../service-fabric/service-fabric-deploy-existing-app.md) vagy bármely alkalmazásba telepítheti a virtuális gépeken. |
| Kezdeti feladatok meghatározásához és végrehajtási képessége | |X |X |X | |
| Is ETW-események figyelésére | |X |X |X | |

## <a name="scenarios"></a>Forgatókönyvek és javaslatok
Az alábbiakban néhány gyakori alkalmazás helyzetek a javaslatok, hogy melyik Azure webszolgáltatási beállítás lehet, hogy minden leginkább megfelelő.

* [Egy előtér-webkiszolgáló háttérben történő feldolgozási és az adatbázis-fájlok integrálva a helyszíni eszközök üzleti alkalmazások futtatásához szükséges.](#onprem)
* [A vállalati webhely, amely jól méretezi gazdagép megbízható módot kell, és globális ajánlatok elérni.](#corp)
* [Windows Server 2003 rendszeren futó IIS 6 alkalmazás van.](#iis6)
* [A kisméretű vállalkozások tulajdonosa vagyok, és a webhely üzemeltetésére egy alacsony költségű módja van szükség, de vegye figyelembe a jövőbeli növekedésre.](#smallbusiness)
* [Egy webes vagy egy grafikus tervező vagyok, és szeretnék tervezése és készítése a webhelyek a felhasználó.](#designer)
* [A többrétegű alkalmazást egy előtér-webkiszolgáló vagyok történő a felhőben](#multitier)
* [Alkalmazás függ a nagy mértékben testre szabott Windows, illetve Linux környezetek és I szeretné helyezze át a felhőbe.](#custom)
* [A hely nyílt forráskódú szoftvereket használ, és kívánt gazdagép azt az Azure-ban.](#oss)
* [Van egy sor üzleti alkalmazás, amely a vállalati hálózathoz való kapcsolódáshoz szükséges.](#lob)
* [Szeretnék REST API-t vagy a mobil ügyfelek webszolgáltatás tárolására szolgál.](#mobile)

### <a id="onprem"></a>Egy előtér-webkiszolgáló háttérben történő feldolgozási és az adatbázis-fájlok integrálva a helyszíni eszközök üzleti alkalmazások futtatásához szükséges.
Az Azure App Service egy olyan kiváló megoldás az összetett üzleti alkalmazások. Lehetővé teszi, hogy hogyan fejleszthetnek alkalmazásokat, amelyek automatikusan méretezése terhelés kiegyensúlyozott platformon, és az Active Directory védett és a helyszíni erőforrásokhoz. Lehetővé teszi az alkalmazások könnyen egy világszínvonalú portál és API-k kezelése, és lehetővé teszi, hogy hogyan használják az ügyfelek a őket az alkalmazás insight eszközök betekintést. A [Webjobs] [ Webjobs] funkció lehetővé teszi a háttérfolyamatot futtatni, és a webes réteg, amíg a hibrid kapcsolat és a virtuális hálózat szolgáltatások könnyebben csatlakozhatnak részeként feladatokat a helyszíni erőforrások biztonsági. Az Azure App Service web Apps három 9 SLA biztosít, és lehetővé teszi:

* Az alkalmazások futtatásához megbízhatóan öngyógyító, az automatikus javítás felhőalapú platformot.
* Méretezhető automatikusan adatközpontok globális hálózaton keresztül.
* Készítsen biztonsági másolatot, és állítsa vissza az adatok helyreállítását.
* Lehet, ISO SOC2 és PCI szabványoknak.
* Integráció az Active Directoryval

### <a id="corp"></a>A vállalati webhely, amely jól méretezi gazdagép megbízható módot kell, és globális ajánlatok elérni.
Az Azure App Service kiválóan alkalmas a vállalati webhely üzemeltetéséhez. Ez lehetővé teszi a webalkalmazások méretezési gyorsan és egyszerűen igényeknek adatközpontok globális hálózaton keresztül. Helyi reach hibatűrést és intelligens forgalom kezeléséhez biztosít. Minden platformon, amely világszínvonalú felügyeleti eszközöket biztosít lehetővé téve betekintést hely állapotának és a webhelyek forgalmát gyorsan és egyszerűen. Az Azure App Service web Apps három 9 SLA biztosít, és lehetővé teszi:

* A webhelyek futtatható megbízhatóan öngyógyító, az automatikus javítás felhő platform.
* Méretezhető automatikusan adatközpontok globális hálózaton keresztül.
* Készítsen biztonsági másolatot, és állítsa vissza az adatok helyreállítását.
* Naplóinak kezeléséhez és a forgalom integrált eszközökkel.
* Lehet, ISO SOC2 és PCI szabványoknak.
* Integráció az Active Directoryval

### <a id="iis6"></a>Windows Server 2003 rendszeren futó IIS 6 alkalmazás van.
Az Azure App Service segítségével egyszerűen áttelepítése régebbi IIS6 alkalmazások társított infrastrukturális költségek elkerülése érdekében. A Microsoft létrehozott [részletes áttelepítési útmutató és könnyen használható áttelepítési eszközök](https://www.movemetowebsites.net/) , amelyek lehetővé teszik a kompatibilitás-ellenőrzés, és azonosítja a módosításokat kell végezni. Integráció a Visual Studio, a TFS és a közös CMS eszközök egyszerűen IIS6 alkalmazások közvetlenül a felhőben való központi telepítése. Amennyiben telepített, az Azure portálban talál hatékony eszközök, amelyek lehetővé teszik a költségek kezelésére, és legfeljebb megfelel igény szerint szükséges csökkentheti. Az áttelepítési eszköz segítségével:

* Gyorsan és egyszerűen telepítse át a korábbi Windows Server 2003 webes alkalmazás a felhőbe.
* Választhat, hogy a csatolt SQL adatbázis helyszíni egy hibrid alkalmazás létrehozásához.
* Automatikusan helyezze át az SQL-adatbázis és az örökölt alkalmazást.

### <a id="smallbusiness"></a>A kisméretű vállalkozások tulajdonosa vagyok, és a webhely üzemeltetésére egy alacsony költségű módja van szükség, de vegye figyelembe a jövőbeli növekedésre.
Az Azure App Service nem kiválóan alkalmas ebben a forgatókönyvben, mert használatba szabad, és hozzáadhatja további képességeket, ha szüksége van rájuk. Minden egyes szabad webalkalmazás tartalmaz egy Azure által biztosított tartományt (*your_company*. azurewebsites.net), és a platform integrált üzembe helyezési és kezelési eszközök, valamint az alkalmazás gyűjteménye, amelyek megkönnyítik a kezdéshez. Nem találhatók sok más szolgáltatások és skálázási lehetőségeket, amelyek lehetővé teszik a helyet, hogy az nagyobb felhasználói igényeknek fejleszteni. Az Azure App Service a következőket teheti:

* Ingyenes szint kezdődnie, és szükség szerint majd növelheti.
* Az Application Gallery segítségével gyorsan be lehessen állítani a népszerű webes alkalmazásokhoz, például a WordPress.
* Hozzá további Azure-szolgáltatások és funkciók az alkalmazáshoz szükség szerint.
* Tegye biztonságossá webalkalmazását, a HTTPS.

### <a id="designer"></a>Egy webes vagy egy grafikus tervező vagyok, és szeretnék tervezése és készítése a webhelyek a felhasználó
Webes fejlesztők és tervezők körében Azure App Service számos keretrendszerek és eszközök könnyen integrálható, központi telepítés támogatja a Git és az FTP és eszközöket és szolgáltatásokat, például a Visual Studio és az SQL-adatbázis szoros integrációt nyújt. Az App Service-szel a következőket teheti:

* A parancssori eszközök [automatizált feladatokat][scripting].
* Például együttműködve népszerű nyelvhez [.Net][dotnet], [PHP][PHP], [Node.js][nodejs], és [Python][Python].
* Jelölje ki a nagyon nagy kapacitások való vertikális felskálázásával három különböző méretezési szintjét.
* Integrálása más Azure-szolgáltatásokkal, például a [SQL-adatbázis][sqldatabase], [Service Bus] [ servicebus] és [tárolási][Storage], vagy az ajánlatok partnerek a [Azure tárolási][azurestore], például a MySQL és MongoDB.
* Olyan eszközöket, például a Visual Studio, a Git, a WebMatrix, a WebDeploy, a TFS és az FTP integrálja.

### <a id="multitier"></a>A többrétegű alkalmazást egy webes előtér-I vagyok áttelepítése a felhőbe
Ha futtatja a többrétegű alkalmazások, például webkiszolgálót, amely összeköti az adatbázisba, Azure App Service, amely az Azure SQL Database szoros integrációt nyújt jó választás. És a webjobs-feladatok szolgáltatással futó háttéralkalmazás folyamatokhoz.

Válassza a Service Fabric egy vagy több, hogy a rétegek, ha több kell, például távoli az a kiszolgálóra a kiszolgálói környezet felett ellenőrzést, vagy konfigurálja a kiszolgáló indítása feladatokat.

Ha azt szeretné, hogy a saját gép lemezképére, vagy futtassa a szoftver vagy szolgáltatásokhoz, amelyek nem lehet konfigurálni a Service Fabric válassza ki a virtuális gépek egy vagy több, hogy a rétegek.

### <a id="custom"></a>Alkalmazás függ a nagy mértékben testre szabott Windows, illetve Linux környezetek és I szeretné helyezze át a felhőbe.
Ha az alkalmazás összetett telepítési vagy a szoftver- és az operációs rendszer konfigurációjához, virtuális gépek oka lehet a legjobb megoldás. A virtuális gépek a következőket teheti:

* A virtuális gép galéria segítségével indítsa el a régebbi operációs rendszerrel, például a Windows vagy Linux, és azután testre szabhatják alkalmazás igényeinek.
* Hozzon létre, és töltse fel az Azure virtuális gépen futtatandó meglévő helyszíni kiszolgáló egyéni lemezképet.

### <a id="oss"></a>Helyemről nyílt forráskódú szoftvert használ, és kívánt gazdagép azt az Azure-ban
Ha a nyílt forráskódú keretrendszer támogatott az App Service, a nyelv és az alkalmazás által igényelt keretrendszer konfigurálása meg automatikusan. App Service lehetővé teszi:

* Számos népszerű nyitott forrás nyelven, használjon például [.NET][dotnet], [PHP][PHP], [Node.js][nodejs], és [Python][Python].
* Állítsa be a WordPress, Drupal, Umbraco, DNN és sok más külső webes alkalmazásokhoz.
* Telepítse át egy meglévő alkalmazást, vagy hozzon létre egy újat az alkalmazás gyűjteményből.

A nyílt forráskódú keretrendszer nem támogatott az App Service, a többi Azure webszolgáltatási beállítások egyik futtathatja. Virtuális gépekkel, telepítése és konfigurálása a szoftver az a számítógép-lemezképen, amely lehet a Windows vagy Linux-alapú.

### <a id="lob"></a>Egy sor üzleti alkalmazás, amely a vállalati hálózathoz való kapcsolódáshoz szükséges van
Ha azt szeretné, üzleti alkalmazás létrehozása, a webhely-szolgáltatások vagy a vállalati hálózaton lévő adatok közvetlen hozzáférést lehet szükség. Ez az App Service, a Service Fabric és a virtuális gépek lehetséges a [Azure Virtual Network szolgáltatás](/azure/virtual-network/). Az App Service használhatja a [VNET integrációs szolgáltatás](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), amely lehetővé teszi, hogy az Azure-alkalmazások futtatása, mintha a vállalati hálózaton.

### <a id="mobile"></a>Szeretném REST API-t és a mobil ügyfelek webszolgáltatás
HTTP-alapú webes szolgáltatások lehetővé teszik az ügyfelek, beleértve a mobil ügyfelek széles választékát támogatja. Keretrendszerek, például az ASP.NET Web API integrálása a Visual Studio segítségével könnyebben létrehozását és felhasználását a REST-szolgáltatások.  Ezeket a szolgáltatásokat ki vannak téve a egy webszolgáltatási végpontot, így a bármely webszolgáltatási az eljárást a Azure használni a forgatókönyv támogatása. App Service azonban REST API-k tárolásához kiváló választás. Az App Service-szel a következőket teheti:

* Gyorsan létrehozhat egy [mobilalkalmazás](../app-service-mobile/app-service-mobile-value-prop.md) vagy API-alkalmazás futtatására a HTTP webes egy Azure globálisan elosztott adatközpontokban.
* Telepítse át a meglévő szolgáltatások, vagy hozzon létre újakat.
* Szolgáltatásiszint-szerződés megvalósítása a rendelkezésre állás érdekében, ha a egyetlen példánya, vagy több dedikált gépekre kiterjesztése.
* A közzétett webhely használatával REST API-k biztosít bármely HTTP-ügyfelek számára, beleértve a mobil ügyfelek.

> [!NOTE]
> Ha szeretne egy olyan fiók regisztrálása előtt Ismerkedés az Azure App Service, folytassa a <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, ahol azonnal létrehozhat egy rövid élettartamú alapszintű alkalmazást az Azure App Service szabad. Nincs szükség bankkártyára, nem jár kötelezettségekkel.
> 
> 

## <a id="nextsteps"></a>További lépések
További információ a három webes üzemeltetési beállításai című [Introducing Azure](../fundamentals-introduction-to-azure.md).

Első lépésként az alkalmazás a választott beállításokkal, lásd a következőket:

* [Azure App Service](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Az Azure virtuális gépek](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png

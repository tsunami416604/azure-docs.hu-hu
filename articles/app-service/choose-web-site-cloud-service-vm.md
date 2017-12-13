---
title: "App Service, Virtual Machines, Service Fabric és Cloud Services – Összehasonlítás | Microsoft Docs"
description: "Ismerje meg, mi alapján választhat az Azure App Service, a Virtual Machines, a Service Fabric és a Cloud Services szolgáltatások között, ha webalkalmazásokat szeretne üzemeltetni."
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
ms.openlocfilehash: bac9169bc41927ef8cf88aee256b2e057ccad4e9
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Azure App Service, Virtual Machines, Service Fabric és Cloud Services – Összehasonlítás
## <a name="overview"></a>Áttekintés
Az Azure számos lehetőséget nyújt a webhelyek üzemeltetéséhez: [Azure App Service][Azure App Service], [Virtual Machines][Virtual Machines], [Service Fabric][Service Fabric] és [Cloud Services][Cloud Services]. Ez a cikk segít Önnek megérteni a különbségeket az egyes lehetőségek között, így a webalkalmazása számára leginkább megfelelő döntést hozhatja meg.

A legtöbb webalkalmazás esetében az Azure App Service a legjobb választás. Az üzembe helyezés és a kezelés integrálva van a platformba, a webhelyek gyorsan méretezhetők a nagyobb adatforgalom kezeléséhez, a beépített terheléselosztás és az adatforgalom-kezelő pedig biztosítja a magas rendelkezésre állást. Egyszerűen áthelyezheti a meglévő helyeket az Azure App Service szolgáltatásba egy [online migrálási eszköz](https://www.migratetoazure.net/) segítségével, nyílt forráskódú alkalmazásokat használhat a Web Application Gallery-ből, vagy akár létrehozhat egy új webhelyet is tetszőleges keretrendszer és eszközök használatával. A [WebJobs][WebJobs] funkció megkönnyíti a háttérfeladatok feldolgozását az App Service webalkalmazásba.

A Service Fabric jó választás, ha új alkalmazást hoz létre, vagy egy meglévő alkalmazást ír át mikroszolgáltatási architektúra használatára. A számítógépek egy megosztott készletén futó alkalmazások kis méretben indíthatóak el, és igény szerint nagy léptékűre skálázhatók akár több száz vagy több ezer géppel. Az állapotalapú szolgáltatások megkönnyítik az alkalmazás állapotának következetes és megbízható tárolását, a Service Fabric pedig automatikusan kezeli a szolgáltatások particionálását, méretezését és rendelkezésre állását.  A Service Fabric támogatja a WebAPI, valamint az Open Web Interface for .NET (OWIN) és az ASP.NET Core együttes használatát.  Az App Service szolgáltatáshoz képest a Service Fabric nagyobb mértékben teszi lehetővé az alapul szolgáló infrastruktúra felügyeletét, illetve közvetlen hozzáférést biztosít hozzá. Távolról is bejelentkezhet kiszolgálóiba, vagy konfigurálhatja azok indítási folyamatait. A Cloud Services a Service Fabric szolgáltatáshoz hasonlóan a könnyű használat mellett magas fokú felügyeletet tesz lehetővé, azonban most már örökölt szolgáltatásnak számít, ezért az új fejlesztések esetében a Service Fabric javasolt.

Ha van olyan meglévő alkalmazása, amelyet nagy mértékben kellene módosítani ahhoz, hogy fusson az App Service vagy Service Fabric szolgáltatásban, akkor a Virtual Machines segítségével egyszerűbbé teheti a felhőbe való migrálás folyamatát. Azonban a virtuális gépek megfelelő konfigurálása, biztonságossá tétele és karbantartása sokkal több időt és informatikai jártasságot igényel, mint az Azure App Service és a Service Fabric. Ha az Azure Virtual Machines használatát fontolgatja, mindenképpen vegye figyelembe a folyamatos karbantartással – a javítások, frissítések és a virtuálisgép-környezet kezelése – járó erőfeszítéseket is. Az Azure Virtual Machines egy szolgáltatott infrastruktúra (IaaS), míg az App Service és a Service Fabric szolgáltatásként nyújtott platformok (PaaS). 

## <a name="features"></a>Szolgáltatások összehasonlítása
A következő táblázatban az App Service, a Cloud Services, a Virtual Machines és a Service Fabric képességeinek összehasonlítása látható, amely alapján eldöntheti, melyik a legjobb választás az Ön számára. Az egyes lehetőségekre vonatkozó aktuális SLA információiért lásd az [Azure szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/) című részt.

| Szolgáltatás | App Service (webalkalmazások) | Cloud Services (webes szerepkörök) | Virtuális gépek | Service Fabric | Megjegyzések |
| --- | --- | --- | --- | --- | --- |
| Közel azonnali üzembe helyezés |X | | |X |Egy alkalmazás vagy alkalmazásfrissítés felhőszolgáltatásba történő telepítése vagy egy virtuális gép létrehozása percekig is eltart; egy alkalmazás webalkalmazásban történő telepítése csupán néhány másodpercet vesz igénybe. |
| Vertikális felskálázás nagyobb gépekre ismételt üzembe helyezés nélkül |X | | |X | |
| A webkiszolgálói példányok megosztják a tartalmukat és a konfigurációjukat, ezért skálázás közben nem szükséges újból üzembe helyeznie vagy konfigurálnia azokat. |X | | |X | |
| Több üzembehelyezési környezet (éles környezet és előkészítés) |X |X | |X |A Service Fabric több környezetet is biztosít alkalmazásai számára, illetve lehetővé teszi az alkalmazás különböző verzióinak párhuzamos üzembe helyezését. |
| Az operációs rendszer frissítésének automatikus kezelése |X |X | | |Részben a Patch Orchestration Application (POA) alkalmazáson keresztül (a jövőben teljes mértékben) |
| Zökkenőmentes platformváltás (egyszerű váltás 32 és 64 bites rendszerek között) |X |X | | | |
| Kód telepítése GIT, FTP használatával |X | |X | | |
| Kód telepítése a Web Deploy használatával |X | |X | |A Cloud Services támogatja a Web Deploy használatát az egyes szerepkörpéldányok frissítéseinek telepítéséhez. A Web Deploy szolgáltatást azonban nem használhatja a szerepkörök első telepítéséhez, és ha frissítéshez használja, akkor az egyes szerepkörök összes példánya esetében külön kell telepítést végeznie. Több példányra van szükség ahhoz, hogy megfeleljen a felhőszolgáltatás éles környezetekre vonatkozó SLA-jának. |
| WebMatrix támogatás |X | |X | | |
| Hozzáférés a Service Bus, Storage, SQL Database és egyéb szolgáltatásokhoz |X |X |X |X | |
| Egy többrétegű architektúra webes vagy webszolgáltatási szintjének üzemeltetése |X |X |X |X | |
| Egy többrétegű architektúra középső szintjének üzemeltetése |X |X |X |X |Az App Service Web Apps segítségével könnyedén üzemeltethető egy REST API középső szintje, a [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) szolgáltatás pedig háttérben futó feldolgozási feladatok üzemeltetésére képes. A WebJobs dedikált webhelyen is futtatható a szint független skálázhatóságának érdekében. |
| Integrált támogatás a MySQL szolgáltatásként való használatához |X |X | | | |
| Az ASP.NET, a klasszikus ASP, a Node.js, a PHP és a Python támogatása |X |X |X |X |A Service Fabric támogatja a webes kezelőfelületek [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) használatával történő létrehozását, de bármilyen típusú alkalmazást (Node.js, Java stb.) telepíthet [vendégalkalmazásként](../service-fabric/service-fabric-deploy-existing-app.md). |
| Horizontális felskálázás több példányra ismételt üzembe helyezés nélkül |X |X |X |X |A virtuális gépek képesek horizontálisan felskálázni több példányra, de a rajtuk futó szolgáltatásokat úgy kell megírni, hogy kezeljék a felskálázást. A terheléselosztót úgy kell konfigurálnia, hogy irányítsa a kérelmeket a gépek között, és egy olyan affinitáscsoportot hozzon létre, amely a karbantartás vagy a hardveres hibák esetén megakadályozza az összes példány egyidejű újraindulását. |
| SSL-támogatás |X |X |X |X |App Service webalkalmazások esetén az egyéni tartományneveknél az SSL csak Alapszintű és Standard módban támogatott. További információkért az SSL webalkalmazásokkal történő használatáról lásd az [Azure webhelyek SSL-tanúsítványának konfigurálását](app-service-web-tutorial-custom-ssl.md) ismertető cikket. |
| Visual Studio integráció |X |X |X |X | |
| Távoli hibakeresés |X |X |X | | |
| Kód telepítése TFS használatával |X |X |X |X | |
| Hálózatelkülönítés az [Azure Virtual Network](/azure/virtual-network/) használatával |X |X |X |X |Lásd még [a Virtual Network és az Azure Websites integrációját](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) ismertető cikket |
| [Azure Traffic Manager](/azure/traffic-manager/) támogatása |X |X |X |X | |
| Integrált végpontmonitoring |X |X |X | | |
| Távoli asztali hozzáférés a szerverekhez | |X |X |X | |
| Bármely egyéni MSI telepítése | |X |X |X |A Service Fabric lehetővé teszi a végrehajtható fájlok [futtatható vendégszolgáltatásként](../service-fabric/service-fabric-deploy-existing-app.md) üzemeltetését vagy bármilyen alkalmazás telepítését a virtuális gépre. |
| Indítási feladatok meghatározása/végrehajtása | |X |X |X | |
| ETW-események figyelése | |X |X |X | |

## <a name="scenarios"></a>Forgatókönyvek és javaslatok
Íme néhány gyakori alkalmazási helyzet a legalkalmasabb Azure webes üzemeltetési ajánlatokkal együtt.

* [Olyan webes kezelőfelületre van szükségem, amely rendelkezik a helyszíni adategységekkel integrált üzleti alkalmazások futtatásához szükséges háttérfeldolgozással és adatbázis-háttérrendszerrel.](#onprem)
* [Jól skálázható és globális elérhetőséget kínáló megbízható módszerre van szükségem céges webhelyem üzemeltetésére.](#corp)
* [Windows Server 2003 alatt futó IIS6 alkalmazással rendelkezem.](#iis6)
* [Kisvállalkozás tulajdonosa vagyok, és a jövőbeli növekedést is lehetővé tevő olcsó webhely-üzemeltetési módszerre van szükségem.](#smallbusiness)
* [Webes vagy grafikai tervező vagyok, és ügyfeleim számára szeretnék webhelyeket tervezni és építeni.](#designer)
* [Többszintű, webes kezelőfelülettel rendelkező alkalmazásomat migrálom a felhőbe.](#multitier)
* [Az alkalmazásom nagymértékben testre szabott Windows vagy Linux környezetektől függ, és át szeretném telepíteni a felhőbe.](#custom)
* [A webhelyem nyílt forráskódú szoftvert használ, és az Azure-ban szeretném üzemeltetni.](#oss)
* [Olyan vállalati alkalmazásom van, amelynek a vállalati hálózatra kell csatlakoznia.](#lob)
* [Mobilügyfelek részére szeretnék REST API-t vagy webszolgáltatást üzemeltetni.](#mobile)

### <a id="onprem"></a> Olyan webes kezelőfelületre van szükségem, amely rendelkezik a helyszíni adategységekkel integrált üzleti alkalmazások futtatásához szükséges háttérfeldolgozással és adatbázis-háttérrendszerrel.
Az Azure App Service remek megoldás összetett üzleti alkalmazások számára. Lehetővé teszi az olyan alkalmazások fejlesztését, amelyek automatikusan skálázódnak egy terheléselosztásos platformon, az Active Directory által védettek, és a helyszíni erőforrásokhoz csatlakoznak. A világszínvonalú portál és API-k segítségével egyszerűvé teszi az alkalmazások kezelését, az App Insights eszközök révén pedig lehetővé teszi, hogy betekintést nyerjen, ügyfelei hogyan használják azokat. A [WebJobs][Webjobs] szolgáltatás lehetővé teszi a háttérfolyamatok és a feladatok futtatását a webes szint részeként, míg a hibrid kapcsolat és VNET funkciók egyszerűvé teszik a visszacsatlakozást a helyszíni erőforrásokra. Az Azure App Service három „9-es” SLA-t biztosít a webes alkalmazásoknak, és lehetővé teszi:

* Az alkalmazások önjavító, automatikusan frissülő felhőplatformon történő futtatását.
* Az automatikus skálázást egy globális adatközpont-hálózaton keresztül.
* A biztonsági mentést és visszaállítást vészhelyreállítás esetén.
* Az ISO, SOC2 és PCI szabványoknak való megfelelést.
* Az integrációt az Active Directoryval.

### <a id="corp"></a> Jól skálázható és globális elérhetőséget kínáló megbízható módszerre van szükségem céges webhelyem üzemeltetésére.
Az Azure App Service remek megoldás vállalati webhelyek üzemeltetésére. Lehetővé teszi a webalkalmazások gyors és könnyű skálázását, hogy egy globális adatközpont-hálózaton keresztül feleljenek meg az igényeknek. Helyi elérhetőséget, hibatűrést és intelligens forgalomkezelést kínál. Mindezt egy világszínvonalú felügyeleti eszközöket kínáló platformon, amely lehetővé teszi a gyors és könnyű betekintést a webhely állapotába és adatforgalmába. Az Azure App Service három „9-es” SLA-t biztosít a webes alkalmazásoknak, és lehetővé teszi:

* Az webhelyek önjavító, automatikusan frissülő felhőplatformon történő futtatását.
* Az automatikus skálázást egy globális adatközpont-hálózaton keresztül.
* A biztonsági mentést és visszaállítást vészhelyreállítás esetén.
* A naplók és az adatforgalom beépített eszközökkel történő kezelését.
* Az ISO, SOC2 és PCI szabványoknak való megfelelést.
* Az integrációt az Active Directoryval.

### <a id="iis6"></a> Windows Server 2003 alatt futó IIS6 alkalmazással rendelkezem.
Az Azure App Service egyszerűvé teszi az IIS6 alkalmazások migrálásával társított infrastrukturális költségek elkerülését. A Microsoft [könnyen használható migrálási eszközöket és útmutatást](https://www.migratetoazure.net/) hozott létre, lehetővé téve a kompatibilitás ellenőrzését és az elvégzendő változtatások felismerését. A Visual Studio-val, TFS-el és a gyakori CMS-eszközökkel történő integráció egyszerűvé teszi az IIS6 alkalmazások üzembe helyezését a felhőben. Az üzembe helyezést követően az Azure Portal robusztus felügyeleti eszközöket biztosít, amelyek lehetővé teszik az igény szerinti vertikális leskálázást a költségek felügyelete, illetve a vertikális felskálázást az igényeknek való megfelelés érdekében. A migrálási eszközzel a következőket teheti:

* Az örökölt Windows Server 2003 webalkalmazásának gyors és könnyű felhőre migrálását.
* Az összekapcsolt SQL-adatbázis helyszínen hagyását hibrid alkalmazások készítéséhez.
* Az SQL-adatbázis automatikus mozgatását az örökölt alkalmazással együtt.

### <a id="smallbusiness"></a>Kisvállalkozás tulajdonosa vagyok, és a jövőbeli növekedést is lehetővé tevő olcsó webhely-üzemeltetési módszerre van szükségem.
Az Azure App Service remek megoldás erre az esetre, mert ingyenes elkezdheti használni, és később igény szerint további funkciókat adhat hozzá. Az összes ingyenes webalkalmazáshoz egy, az Azure által biztosított tartomány tartozik (*az_on_cege*.azurewebsites.net), és a platformhoz az egyszerű kezdést lehetővé tévő beépített telepítési és kezelési eszközök, valamint egy alkalmazáskatalógus is tartozik. Számos további szolgáltatás és skálázási lehetőség teszi lehetővé a webhely fejlődését a megemelkedett felhasználói igényhez igazodva. Az Azure App Service-szel a következőket teheti:

* Kezdhet az ingyenes szinten, majd igény szerint vertikálisan felskálázhat.
* Gyorsan beállíthat a Wordpresshez hasonló népszerű webalkalmazásokat az Alkalmazáskatalógus használatával.
* Igény szerint további Azure-szolgáltatásokat és funkciókat adhat hozzá alkalmazásához.
* Biztonságossá teheti webalkalmazását a HTTPS segítségével.

### <a id="designer"></a> Webes vagy grafikai tervező vagyok és ügyfeleim számára szeretnék webhelyeket tervezni és építeni
A webfejlesztők és tervezők számára az Azure App Service egyszerűen együttműködik számos különböző eszközzel és keretrendszerrel, rendelkezik Git és FTP telepítési támogatással, és szoros integrációt kínál a Visual Studióhoz és az SQL Database-hez hasonló eszközökkel és szolgáltatásokkal. Az App Service-szel a következőket teheti:

* Parancssori eszközök használata [automatizált feladatok][scripting] elvégzésére.
* Olyan népszerű programozási nyelvekkel dolgozhat, mint például a [.Net][dotnet], a [PHP][PHP], a [Node.js][nodejs] és a [Python][Python].
* Három különböző skálázási szint közül választhat a nagyon magas kapacitások elérése érdekében.
* Együttműködhet más Azure-szolgáltatásokkal, például az [SQL Database-zel][sqldatabase], [Service Busszal][servicebus] és a [Storage-dzsel][Storage] vagy az [Azure Store][azurestore] partnerajánlataival, például a MySQL-lel vagy a MongoDB-vel.
* Együttműködhet olyan eszközökkel, mint a Visual Studio, a Git, a WebMatrix, a WebDeploy, a TFS és az FTP.

### <a id="multitier"></a>Többszintű, webes kezelőfelülettel rendelkező alkalmazásomat migrálom a felhőbe
Többszintű alkalmazások, például egy adatbázishoz kapcsolódó webkiszolgáló futtatása esetén az Azure App Service jó megoldás, amely szoros együttműködést biztosít az Azure SQL Database-zel. És a háttérfolyamatok futtatására használhatja a WebJobs funkciót.

Ha nagyobb mértékű vezérlésre van szüksége, például a kiszolgáló távoli vezérlésére vagy indítási folyamatainak beállítására, válassza a Service Fabricot egy vagy több szinten.

Ha saját rendszerképet vagy a Service Fabricban nem konfigurálható szoftvereket vagy szolgáltatásokat szeretne futtatni, válassza a Virtual Machinest egy vagy több szinten.

### <a id="custom"></a>Az alkalmazásom nagymértékben testre szabott Windows vagy Linux környezetektől függ, és át szeretném telepíteni a felhőbe.
Ha az alkalmazás összetett telepítést vagy a szoftverek és az operációs rendszer konfigurációját igényli, a Virtual Machines valószínűleg a legjobb megoldás. A Virtual Machines használatával a következőket teheti:

* A virtuálisgép-katalógus használatával kiindulhat egy operációs rendszerből, például a Windowsból vagy a Linuxból, majd testre szabhatja azt az alkalmazáskövetelmények szerint.
* Létrehozhat és feltölthet egy helyi kiszolgálóról készült egyéni rendszerképet, majd futtathatja azt egy virtuális gépen az Azure-ban.

### <a id="oss"></a>A webhelyem nyílt forráskódú szoftvert használ, és az Azure-ban szeretném üzemeltetni
Ha az App Service támogatja nyílt forráskódú keretrendszerét, az alkalmazás által igényelt nyelvek és keretrendszerek konfigurálása automatikusan megtörténik. Az App Service használatával a következőket teheti:

* Olyan népszerű nyílt forráskódú programozási nyelveket használhat, mint például a [.Net][dotnet], a [PHP][PHP], a [Node.js][nodejs] és a [Python][Python].
* Beállíthatja a WordPresst, Drupalt, Umbracót, a DNN-t és sok más hasonló külső webalkalmazást.
* Migrálhatja a meglévő alkalmazásokat, vagy újat hozhat létre az alkalmazáskatalógusból.

Ha az App Service nem támogatja nyílt forráskódú keretrendszerét, a többi Azure webes üzemeltetési lehetőség egyikével futtathatja azt. A Virtual Machines használatával a gép rendszerképén telepítheti és állíthatja be a szoftvert, amely lehet Windows- vagy Linux-alapú.

### <a id="lob"></a>Olyan vállalati alkalmazásom van, amelynek a vállalati hálózatra kell csatlakoznia
Ha üzleti alkalmazást szeretne létrehozni, a webhelyének közvetlen hozzáférésre lehet szüksége a vállalati hálózat szolgáltatásaihoz vagy adataihoz. Az [Azure Virtual Network szolgáltatás](/azure/virtual-network/) használatával ez lehetséges az App Service, a Service Fabric és a Virtual Machines esetében. Az App Service-ben használhatja a [VNET-integráció funkciót](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), amely lehetővé teszi az Azure-alkalmazások futtatását úgy, mintha azok a vállalati hálózatán lennének.

### <a id="mobile"></a>Mobilügyfelek részére szeretnék REST API-t vagy webszolgáltatást üzemeltetni
A HTTP-alapú webes szolgáltatások lehetővé teszik az ügyfelek széles választékának támogatását, amibe beletartoznak a mobilügyfelek is. Az ASP.NET webes API-hoz hasonló keretrendszerek együttműködnek a Visual Studióval, egyszerűvé téve a REST szolgáltatások létrehozását és felhasználását.  Ezeket a szolgáltatásokat egy webes végpont teszi közzé, így ebben az esetben lehetséges bármely Azure webüzemeltetési módszer használata. Azonban az App Service remek választás a REST API-k üzemeltetésére. Az App Service-szel a következőket teheti:

* Gyorsan létrehozhat [mobilalkalmazásokat](../app-service-mobile/app-service-mobile-value-prop.md) vagy API-alkalmazásokat a HTTP webszolgáltatás üzemeltetésére az Azure globálisan elosztott adatközpontjainak egyikében.
* Migrálhatja meglévő szolgáltatásait, vagy újakat hozhat létre.
* SLA-t érhet el a rendelkezésre álláshoz egyetlen példánnyal, vagy horizontálisan felskálázhat több dedikált gépre.
* Használhatja a közzétett oldalt, hogy bármely HTTP-ügyfél számára biztosítsa a REST API-kat, a mobilügyfeleket is beleértve.

> [!NOTE]
> Ha nem szeretne regisztrálni fiókot az Azure App Service megismerése előtt, lépjen a <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a> oldalra, ahol azonnal ingyenesen létrehozhat egy rövid élettartamú alapszintű alkalmazást az Azure App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a id="nextsteps"></a> Következő lépések
További információk a három webes üzemeltetési lehetőségről: [Az Azure bemutatása](../fundamentals-introduction-to-azure.md).

Az első lépések megtételét az alkalmazása számára kiválasztott lehetőségekkel a következő források segítik:

* [Azure App Service](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Azure Virtual Machines](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
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

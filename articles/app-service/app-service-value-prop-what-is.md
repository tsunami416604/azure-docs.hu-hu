<properties 
    pageTitle="Azure App Service web- és mobilalkalmazásokhoz | Microsoft Azure" 
    description="Ismerje meg, hogyan segíthet az Azure App Service web- és mobilalkalmazások fejlesztésében, telepítésében és kezelésében." 
    keywords="app service, azure app service, app service cost, scale, scalable, app deployment, azure app deployment, paas, platform-as-a-service"
    services="app-service" 
    documentationCenter="" 
    authors="omarkmsft" 
    manager="dwrede" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/25/2016" 
    ms.author="omark"/>

# Az Azure App Service-ről

Az *App Service* a Microsoft Azure [szolgáltatásként nyújtott platformja](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS). Web- és mobilalkalmazásokat hozhat létre bármely platformra és eszközre. Alkalmazásait integrálhatja SaaS megoldásokkal, csatlakozhat helyszíni alkalmazásokhoz és automatizálhatja üzleti folyamatait. Az alkalmazásokat az Azure teljes körűen felügyelt virtuális gépeken futtatja, az Ön választása szerint vagy közös virtuálisgép-erőforrásokkal, vagy dedikált virtuális gépekkel. 

Az App Service rendelkezik azon webes és mobil funkciókkal, amelyeket korábban az Azure Websites és az Azure Mobile Services külön-külön tartalmazott.  Ezenkívül új lehetőségek is elérhetők az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként az App Service lehetővé teszi egyetlen megoldás összeállítását különféle összetevőkből (webhelyekből, mobilalkalmazásokból, REST API-kból és üzleti folyamatokból).

Az alábbi 4 perces videó röviden bemutatja, hogy milyen kapcsolatban áll az App Service a korábbi Azure-szolgáltatásokkal, és milyen újdonságokat kínál.

+[AZURE.VIDEO app-service-history-lesson] 

## Miért előnyös az App Service használata?

Az App Service legfontosabb funkciói és képességei többek között az alábbiak: 

- **Több nyelv és keretrendszer** – Az App Service kiváló támogatást nyújt az ASP.NET, Node.js, Java, PHP és Python nyelvekhez. Az App Service virtuális gépeken futtathat [Windows PowerShell és egyéb parancsfájlokat vagy végrehajtható fájlokat](../app-service-web/web-sites-create-web-jobs.md) is.

- **DevOps optimalizálás** - Beállíthat [folyamatos integrációt és üzembe helyezést](../app-service-web/app-service-continous-deployment.md) a Visual Studio Team Services, GitHub vagy BitBucket szolgáltatásokhoz. [Teszt- és átmeneti környezetek](../app-service-web/web-sites-staged-publishing.md) segítségével küldheti ki a frissítéseket. [A/B tesztelést](../app-service-web/app-service-web-test-in-production-get-start.md) végezhet. Alkalmazásait az App Service-ben az [Azure PowerShell](../powershell-install-configure.md) vagy a [többplatformos parancssori felület (CLI)](../xplat-cli-install.md) segítségével felügyelheti.
 
- **Globális méret magas rendelkezésre állással** - Manuálisan vagy automatikusan is végezhet [felfelé skálázást](../app-service/app-service-scale.md) és [horizontális skálázást](../azure-portal/insights-how-to-scale.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.

- **Csatlakozás SaaS platformokhoz és helyszíni adatokhoz** - Több mint 50 [összekötő](../connectors/apis-list.md) közül választhat nagyvállalati rendszerekhez (például SAP, Siebel vagy Oracle), SaaS-szolgáltatásokhoz (például Salesforce vagy Office 365), valamint internetes szolgáltatásokhoz (többek között a Facebookhoz és a Twitterhez). Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](../biztalk-services/integration-hybrid-connection-overview.md) és az [Azure virtuális hálózatok](../app-service-web/web-sites-integrate-with-vnet.md) segítségével.

- **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/TrustCenter/).

- **Alkalmazássablonok** - Az [Azure Piactér](https://azure.microsoft.com/marketplace/) kiterjedt alkalmazássablon-listájáról választhat, ami lehetővé teszi, hogy egy varázsló segítségével telepítsen népszerű nyílt forráskódú szoftvereket, mint a WordPress, a Joomla vagy a Drupal.

- **Visual Studio-integráció** - A Visual Studio dedikált eszközei leegyszerűsítik a létrehozás, telepítés és hibakeresés folyamatát.

## Alkalmazástípusok az App Service-ben

Az App Service számos *alkalmazástípust* kínál, amelyek mindegyike egy adott számítási feladatot szolgálhat ki.

- [**Web Apps**](../app-service-web/app-service-web-overview.md) - webhelyek és webalkalmazások üzemeltetéséhez

- [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md) Mobilalkalmazások háttérkomponenseinek üzemeltetéséhez
   
- [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) - Felhő API-k üzemeltetéséhez 
 
- [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - Adatok felhők közötti elérésének és használatának  programozás nélküli automatizálásához

Az *alkalmazás* itt a számítási feladatok futtatására elkülönített üzemeltetési erőforrásokat jelenti. A „webalkalmazás” alatt például általában mind a számítási erőforrásokat, mind az alkalmazáskódot szokták érteni, amelyek a böngészőben együttesen hoznak létre működő funkciót. Az App Service-ben azonban a *webalkalmazás* az Azure által biztosított, az alkalmazás futtatásához szükséges számítási erőforrásokat jelenti. Ha az alkalmazás webes kezelőfelületből és REST-API logikájú háttérkomponensből áll, akkor választása szerint mindkettőt üzembe helyezheti egy webalkalmazásba, vagy pedig a kezelőfelület kódját egy webalkalmazásba, a háttérkomponenseket pedig egy API-alkalmazásba. Egy alkalmazás több, különféle típusú App Service-alkalmazásból is állhat.

## App Service-csomagok

Az [App Service-csomag](azure-web-sites-web-hosting-plans-in-depth-overview.md) határozza meg azokat a számítási erőforrásokat, amelyekkel az alkalmazás fut. Ha alacsony szintű forgalomterhelés várható, használhat közös virtuális gépeket. Magasabb terhelés esetén különféle méretű dedikált virtuális gépek közül választhat. Ugyanabban a csomagban több App Service-alkalmazás is lehet, vertikális felskálázásuk pedig a csomaggal együtt történik.

Ha  nagyobb méretezhetőségre van szüksége, az alkalmazásokat az [App Service-környezetben](../app-service-web/app-service-app-service-environment-intro.md) is futtathatja. 

## Díjszabás

Az App Service díjairól további információkat talál az [App Service díjszabásban](https://azure.microsoft.com/pricing/details/app-service/). 

## App Service első lépések

[Hozzon létre most egy ideiglenes webalkalmazást, mobilalkalmazást vagy logikai alkalmazást](http://go.microsoft.com/fwlink/?LinkId=523751) ingyenesen. Nincs szükség hitelkártyára, nincs elkötelezettség, egyszerűen elvégezhető.

Vagy hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/), és tekintse meg az első lépéseket bemutató oktatóanyagainkat:

* [Oktatóanyag: webalkalmazás létrehozása](../app-service-web/app-service-web-get-started.md)
* [Oktatóanyag: mobilalkalmazás létrehozása](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Oktatóanyag: API-alkalmazás létrehozása](../app-service-api/app-service-api-dotnet-get-started.md)
* [Oktatóanyag: logikai alkalmazás létrehozása](../app-service-logic/app-service-logic-create-a-logic-app.md)



<!--HONumber=Jun16_HO2-->



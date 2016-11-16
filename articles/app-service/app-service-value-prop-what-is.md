---
title: "Azure App Service web-, mobil- és API-alkalmazásokhoz | Microsoft Docs"
description: "Ismerje meg, hogyan segíthet az Azure App Service web- és mobilalkalmazások fejlesztésében, telepítésében és kezelésében."
keywords: app service, azure app service, app service cost, scale, scalable, app deployment, azure app deployment, paas, platform-as-a-service, website, web site, web, azure mobile
services: app-service
documentationcenter: 
author: omarkmsft
manager: erikre
editor: cephalin
ms.assetid: 979cafa8-eeb6-4d3b-87cf-764a821c3e4f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/26/2016
ms.author: omark
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 042da7ac46fd20bd0e9af33f449f8ea4a6a42089


---
# <a name="what-is-azure-app-service"></a>Az Azure App Service-ről
Az *App Service* a Microsoft Azure [szolgáltatásként nyújtott platformja](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS). Web- és mobilalkalmazásokat hozhat létre bármely platformra és eszközre. Alkalmazásait integrálhatja SaaS megoldásokkal, csatlakozhat helyszíni alkalmazásokhoz és automatizálhatja üzleti folyamatait. Az alkalmazásokat az Azure teljes körűen felügyelt virtuális gépeken futtatja, az Ön választása szerint vagy közös virtuálisgép-erőforrásokkal, vagy dedikált virtuális gépekkel.

Az App Service rendelkezik azon webes és mobil funkciókkal, amelyeket korábban az Azure Websites és az Azure Mobile Services külön-külön tartalmazott. Ezenkívül új lehetőségek is elérhetők az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként az App Service lehetővé teszi egyetlen megoldás összeállítását különféle összetevőkből (webhelyekből, mobilalkalmazásokból, REST API-kból és üzleti folyamatokból).

Az alábbi 4 perces videó röviden bemutatja, hogy milyen kapcsolatban áll az App Service a korábbi Azure-szolgáltatásokkal, és milyen újdonságokat kínál.

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/app-service-history-lesson/player]
> 
> 

## <a name="why-use-app-service"></a>Miért előnyös az App Service használata?
Az App Service legfontosabb funkciói és képességei többek között az alábbiak:

* **Több nyelv és keretrendszer** – Az App Service kiváló támogatást nyújt az ASP.NET, Node.js, Java, PHP és Python nyelvekhez. Az App Service virtuális gépeken futtathat [Windows PowerShell és egyéb parancsfájlokat vagy végrehajtható fájlokat](../app-service-web/web-sites-create-web-jobs.md) is.
* **DevOps optimalizálás** – Beállíthat [folyamatos integrációt és üzembe helyezést](../app-service-web/app-service-continuous-deployment.md) a Visual Studio Team Services, GitHub vagy BitBucket szolgáltatásokhoz. [Teszt- és átmeneti környezetek](../app-service-web/web-sites-staged-publishing.md) segítségével küldheti ki a frissítéseket. [A/B tesztelést](../app-service-web/app-service-web-test-in-production-get-start.md) végezhet. Alkalmazásait az App Service-ben az [Azure PowerShell](../powershell-install-configure.md) vagy a [többplatformos parancssori felület (CLI)](../xplat-cli-install.md) segítségével felügyelheti.
* **Globális méret magas rendelkezésre állással** – Manuálisan vagy automatikusan is végezhet [vertikális skálázást](../app-service-web/web-sites-scale.md) és [horizontális skálázást](../monitoring-and-diagnostics/insights-how-to-scale.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
* **Csatlakozás SaaS platformokhoz és helyszíni adatokhoz** - Több mint 50 [összekötő](../connectors/apis-list.md) közül választhat nagyvállalati rendszerekhez (például SAP, Siebel vagy Oracle), SaaS-szolgáltatásokhoz (például Salesforce vagy Office 365), valamint internetes szolgáltatásokhoz (többek között a Facebookhoz és a Twitterhez). Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](../biztalk-services/integration-hybrid-connection-overview.md) és az [Azure virtuális hálózatok](../app-service-web/web-sites-integrate-with-vnet.md) segítségével.
* **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/TrustCenter/).
* **Alkalmazássablonok** - Az [Azure Piactér](https://azure.microsoft.com/marketplace/) kiterjedt alkalmazássablon-listájáról választhat, ami lehetővé teszi, hogy egy varázsló segítségével telepítsen népszerű nyílt forráskódú szoftvereket, mint a WordPress, a Joomla vagy a Drupal.
* **Visual Studio-integráció** - A Visual Studio dedikált eszközei leegyszerűsítik a létrehozás, telepítés és hibakeresés folyamatát.

## <a name="app-types-in-app-service"></a>Alkalmazástípusok az App Service-ben
Az App Service számos *alkalmazástípust* kínál, amelyek mindegyike egy adott számítási feladatot szolgálhat ki:

* [**Web Apps**](../app-service-web/app-service-web-overview.md) - webhelyek és webalkalmazások üzemeltetéséhez
* [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md) Mobilalkalmazások háttérkomponenseinek üzemeltetéséhez
* [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) – RESTful API-k üzemeltetéséhez.
* [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) – Az üzleti folyamatok automatizálásához, rendszerek és adatok felhők közötti integrálásához – mindez kódírás nélkül.

Az *alkalmazás* itt a számítási feladatok futtatására elkülönített üzemeltetési erőforrásokat jelenti. A „webalkalmazás” alatt például általában mind a számítási erőforrásokat, mind az alkalmazáskódot szokták érteni, amelyek a böngészőben együttesen hoznak létre működő funkciót. Az App Service-ben azonban a *webalkalmazás* az Azure által biztosított, az alkalmazás futtatásához szükséges számítási erőforrásokat jelenti. Ha az alkalmazás webes kezelőfelületből és REST-API logikájú háttérkomponensből áll, akkor választása szerint mindkettőt üzembe helyezheti egy webalkalmazásban, vagy pedig a kezelőfelület kódját egy webalkalmazásban, a háttérkomponenseket pedig egy API-alkalmazásban. Egy alkalmazás több, különféle típusú App Service-alkalmazásból is állhat.

## <a name="app-service-plans"></a>App Service-csomagok
Az [App Service-csomag](azure-web-sites-web-hosting-plans-in-depth-overview.md) határozza meg azokat a számítási erőforrásokat, amelyekkel az alkalmazás fut. Ha alacsony szintű forgalomterhelés várható, használhat közös virtuális gépeket (**Ingyenes** és **Közös** tarifacsomag). Magasabb terhelés esetén különféle méretű dedikált virtuális gépek közül választhat (**Alapszintű**, **Standard** és **Prémium** tarifacsomag). Ugyanazon a csomagon több App Service-alkalmazás is osztozhat, amely esetben a tarifacsomagok skálázása együtt történik.

Ha  nagyobb méretezhetőségre van szüksége, az alkalmazásokat az [App Service-környezetben](../app-service-web/app-service-app-service-environment-intro.md) is futtathatja.

## <a name="pricing"></a>Díjszabás
Az App Service díjairól további információkat talál az [App Service díjszabásban](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="testdrive-app-service"></a>Az App Service kipróbálása
[Hozzon létre egy minta-webalkalmazást, -mobilalkalmazást vagy -logikaialkalmazást](http://go.microsoft.com/fwlink/?LinkId=523751), és játsszon vele 1 órát. Nincs szükség hitelkártyára, nincs elkötelezettség, egyszerűen elvégezhető.

Vagy hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/), és tekintse meg az első lépéseket bemutató oktatóanyagainkat:

* [Oktatóanyag: webalkalmazás létrehozása](../app-service-web/app-service-web-get-started.md)
* [Oktatóanyag: mobilalkalmazás létrehozása](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Oktatóanyag: API-alkalmazás létrehozása](../app-service-api/app-service-api-dotnet-get-started.md)
* [Oktatóanyag: logikai alkalmazás létrehozása](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO2-->



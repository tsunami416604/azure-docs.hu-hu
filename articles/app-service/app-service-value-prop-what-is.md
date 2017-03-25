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
ms.date: 12/02/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a773e43b28b144dd8341b276eee3fa504d4f1080
ms.lasthandoff: 03/21/2017


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
* **DevOps optimalizálás** – Beállíthat [folyamatos integrációt és üzembe helyezést](../app-service-web/app-service-continuous-deployment.md) a Visual Studio Team Services, GitHub vagy BitBucket szolgáltatásokhoz. [Teszt- és átmeneti környezetek](../app-service-web/web-sites-staged-publishing.md) segítségével küldheti ki a frissítéseket. [A/B tesztelést](../app-service-web/app-service-web-test-in-production-get-start.md) végezhet. Alkalmazásait az App Service-ben az [Azure PowerShell](/powershell/azureps-cmdlets-docs) vagy a [többplatformos parancssori felület (CLI)](../cli-install-nodejs.md) segítségével felügyelheti.
* **Globális méret magas rendelkezésre állással** – Manuálisan vagy automatikusan is végezhet [vertikális skálázást](../app-service-web/web-sites-scale.md) és [horizontális skálázást](../monitoring-and-diagnostics/insights-how-to-scale.md). A Microsoft globális adatközpont infrastruktúrájában bárhol üzemeltetheti az alkalmazásait, az App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pedig magas rendelkezésre állást biztosít.
* **Csatlakozás SaaS platformokhoz és helyszíni adatokhoz** - Több mint 50 [összekötő](../connectors/apis-list.md) közül választhat nagyvállalati rendszerekhez (például SAP, Siebel vagy Oracle), SaaS-szolgáltatásokhoz (például Salesforce vagy Office 365), valamint internetes szolgáltatásokhoz (többek között a Facebookhoz és a Twitterhez). Hozzáférhet helyszíni adatokhoz a [Hibrid kapcsolatok](../biztalk-services/integration-hybrid-connection-overview.md) és az [Azure virtuális hálózatok](../app-service-web/web-sites-integrate-with-vnet.md) segítségével.
* **Biztonság és megfelelőség** - Az App Service megfelel az [ISO, SOC és PCI szabványoknak](https://www.microsoft.com/TrustCenter/).
* **Alkalmazássablonok** – Az [Azure Marketplace](https://azure.microsoft.com/marketplace/) kiterjedt sablonlistájáról választhat, amelyek lehetővé teszik, hogy varázsló segítségével telepítsen olyan népszerű nyílt forráskódú szoftvereket, mint például a WordPress, a Joomla vagy a Drupal.
* **Visual Studio-integráció** – A Visual Studio dedikált eszközei leegyszerűsítik a létrehozás, telepítés és hibakeresés folyamatát.

## <a name="app-types-in-app-service"></a>Alkalmazástípusok az App Service-ben
Az App Service számos *alkalmazástípust* kínál, amelyek mindegyike adott számítási feladatot szolgál ki:

* [**Web Apps**](../app-service-web/app-service-web-overview.md) - webhelyek és webalkalmazások üzemeltetéséhez
* [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md) Mobilalkalmazások háttérkomponenseinek üzemeltetéséhez
* [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) – RESTful API-k üzemeltetéséhez.
* [**Logic Apps**](../logic-apps/logic-apps-what-are-logic-apps.md) – Az üzleti folyamatok automatizálásához, rendszerek és adatok felhők közötti integrálásához – mindez kódírás nélkül.

Az *alkalmazás* itt a számítási feladatok futtatására elkülönített üzemeltetési erőforrásokat jelenti. A „webalkalmazás” alatt például általában mind a számítási erőforrásokat, mind az alkalmazáskódot szokták érteni, amelyek a böngészőben együttesen hoznak létre működő funkciót. Az App Service-ben azonban a *webalkalmazás* az Azure által biztosított, az alkalmazás futtatásához szükséges számítási erőforrásokat jelenti. 

Egy alkalmazás több, különféle típusú App Service-alkalmazásból is állhat. Ha például az alkalmazás webes kezelőfelületből és RESTful API-háttérrendszerből áll, akkor:

- mindkettőt (a kezelőfelületet és az api-t is) üzembe helyezheti egyetlen webalkalmazásban;  
- a kezelőfelület kódját üzembe helyezheti egy webalkalmazásban, a háttérrendszerét pedig egy API-alkalmazásban. 



## <a name="app-service-plans"></a>App Service-csomagok
Az [App Service-csomagok](azure-web-sites-web-hosting-plans-in-depth-overview.md) az alkalmazások üzemeltetéséhez használt fizikai erőforrások gyűjteményét képviselik.

Az App Service-csomagok a következőket határozzák meg:

- **Régió** (USA nyugati régiója, USA keleti régiója stb.)
- **Méretezési szám** (egy, kettő, három példány, stb.)
- **Példányméret** (kicsi, közepes, nagy)
- **Termékváltozat** (Ingyenes, Közös, Alapszintű, Standard, Prémium)

Egy **App Service-csomaghoz** rendelt összes alkalmazás ugyanazokat az általa meghatározott erőforrásokat használja, így több alkalmazás üzemeltetése esetén is csökkenthetők a költségek.

Az **App Service-csomagok** szintje az **Ingyenes** és **Közös** termékváltozatoktól az **Alapszintű**, **Standard** és **Prémium** változatokig terjed. A magasabb szintű változatok több erőforrást és szolgáltatást biztosítanak. Ha App Service-csomagja **Alapszintű** vagy magasabb, a virtuális gépek **méretét** és méretezési számát is szabályozhatja.

Az App Service-csomag **Termékváltozat** és **Méretezés** tulajdonsága a költségeket befolyásolja, nem a csomagban üzemeltetett alkalmazások számát. 

Ha  nagyobb méretezhetőségre van szüksége, az alkalmazásokat az [App Service-környezetben](../app-service-web/app-service-app-service-environment-intro.md) is futtathatja.

## <a name="pricing"></a>Díjszabás
Az App Service díjairól további információkat talál az [App Service díjszabásban](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Az App Service kipróbálása
[Hozzon létre egy mintául szolgáló web-, -mobil- vagy logikai alkalmazást](https://azure.microsoft.com/try/app-service/), és kísérletezzen vele egy órát. Nincs szükség hitelkártyára, nincs elkötelezettség, nincsenek felesleges bonyodalmak.

Vagy hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/), és tekintse meg az első lépéseket bemutató oktatóanyagainkat:

* [Oktatóanyag: webalkalmazás létrehozása](../app-service-web/app-service-web-get-started.md)
* [Oktatóanyag: mobilalkalmazás létrehozása](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Oktatóanyag: API-alkalmazás létrehozása](../app-service-api/app-service-api-dotnet-get-started.md)
* [Oktatóanyag: logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md)



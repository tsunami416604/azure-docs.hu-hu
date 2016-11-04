---
title: Webalkalmazások létrehozása az Azure Piactérről | Microsoft Docs
description: Megtudhatja, hogy az Azure portál segítségével hogyan hozhat létre új WordPress webalkalmazásokat az Azure Piactérről.
services: app-service\web
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/20/2016
ms.author: robmcm

---
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# Webalkalmazások létrehozása a Azure Piactérről
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Az Azure Piactér a Microsoft, külső vállalatok és nyílt forrású szoftver-kezdeményezések által fejlesztett népszerű webalkalmazások széles skáláját teszi elérhetővé. Például: WordPress, Umbraco CMS, Drupal stb. Ezek a webalkalmazások a legkülönbözőbb népszerű keretrendszerekre épülnek, mint a [PHP] ebben a WordPress példában, vagy a [.NET], a [Node.js], a [Java] és a [Python], csak hogy néhányat említsünk. A webalkalmazások Azure Piactérről való létrehozásához az [Azure Portal] eléréséhez használt böngésző az egyetlen szoftver, amelyre szüksége van.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

* Az Azure piactér egyik sablonján alapuló webalkalmazás keresése és létrehozása az Azure App Service szolgáltatásban.
* Az Azure App Service szolgáltatás beállításainak konfigurálása az új webalkalmazás számára.
* A webalkalmazás elindítása és kezelése.

A jelen oktatóanyag esetében egy WordPress blogwebhelyet fog üzembe helyezni az Azure Piactérről. Ha végrehajtja a jelen oktatóanyag lépéseit, egy saját, a felhőben futó WordPress webhellyel fog rendelkezni.

![A példaként használt WordPress webalkalmazás irányítópultja][WordPressDashboard1]

Az oktatóanyag keretein belül üzembe helyezett WordPress webhely MySQL adatbázist használ. Ha ehelyett SQL Database szolgáltatást használna az adatbázishoz, akkor tekintse meg a [Project Nami] nevű alkalmazást, amely szintén elérhető az Azure Piactéren keresztül.

> [!NOTE]
> Az oktatóanyag elvégzéséhez Microsoft Azure-fiókra lesz szüksége. Ha nincs fiókja, [aktiválhatja a Visual Studio előfizetői előnyeit][aktiválás], vagy [regisztráljon egy ingyenes próbaverzióra][ingyenes próba].
> 
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, menjen [Az App Service kipróbálása] oldalra. Innen azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben; ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## Webalkalmazás keresése és létrehozása az Azure App Service használatával
1. Jelentkezzen be az [Azure Portal].
2. Kattintson az **Új** lehetőségre.
   
    ![Új Azure-erőforrás létrehozása][MarketplaceStart]
3. Keresse meg a **WordPress** alkalmazást, majd kattintson a **WordPress** lehetőségre. (Ha a MySQL helyett inkább SQL Database szolgáltatást használna, akkor keresse meg a **Project Nami** alkalmazást.)
   
    ![A WordPress alkalmazás keresése a Piactéren][MarketplaceSearch]
4. A WordPress alkalmazás leírásának elolvasása után kattintson a **Létrehozás** gombra.
   
    ![A WordPress webalkalmazás létrehozása][MarketplaceCreate]

## Az Azure App Service szolgáltatás beállításainak konfigurálása az új webalkalmazás számára
1. Miután létrehozott egy új webalkalmazást, megjelenik a WordPress beállításpanelje, amelynek segítségével elvégezheti a következő lépéseket:
   
    ![A WordPress webalkalmazás beállításainak konfigurálása][ConfigStart]
2. A **Webalkalmazás** mezőben adjon meg egy nevet a webalkalmazás számára.
   
    Ennek a névnek egyedinek kell lennie az azurewebsites.net tartományban, mert a webalkalmazás URL-címe *{név}*.azurewebsites.net lesz. Ha a megadott név nem egyedi, egy piros felkiáltójel jelenik meg a szövegmezőben.
   
    ![A WordPress webalkalmazás nevének konfigurálása][ConfigAppName]
3. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni kívánja. 
   
    ![A webalkalmazáshoz tartozó előfizetés konfigurálása][ConfigSubscription]
4. Válasszon egy **erőforráscsoportot**, vagy hozzon létre egy újat.
   
    További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése][ResourceGroups].
   
    ![A webalkalmazáshoz tartozó erőforráscsoport konfigurálása][ConfigResourceGroup]
5. Válasszon ki egy **App Service-csomagot/-helyet**, vagy hozzon létre egy újat.
   
    További információk az App Service-csomagokról: [Azure App Service plans overview][AzureAppServicePlans] (Az Azure App Service-csomagok áttekintése). 
   
    ![A webalkalmazáshoz tartozó szolgáltatáscsomag konfigurálása][ConfigServicePlan]
6. Kattintson az **Adatbázis**elemre, majd az **Új MySQL Adatbázis** panelen adja meg a MySQL adatbázis konfigurálásához szükséges értékeket.
   
    a. Írjon be egy új nevet, vagy hagyja meg az alapértelmezett nevet.
   
    b. A **Database Type** (Adatbázistípus) mezőt hagyja **Shared** (Megosztott) értéken.
   
    c. Válassza ki ugyanazt a helyet, amelyet a webalkalmazás számára is választott.
   
    d. Válasszon egy tarifacsomagot. **Merkúr** – ingyenes, minimális kapcsolódással és lemezterülettel – ideális ehhez az oktatóanyaghoz.
   
    e. Az **Új MySQL-adatbázis** panelen fogadja el a jogi feltételeket, majd kattintson az **OK** gombra. 
   
    ![A webalkalmazáshoz tartozó adatbázis-beállítások konfigurálása][ConfigDatabase]
7. A **WordPress** panelen fogadja el a jogi feltételeket, majd kattintson a **Létrehozás** gombra. 
   
    ![Fejezze be a webalkalmazás beállítását, és kattintson az OK gombra][ConfigFinished]
   
    Az Azure App Service általában kevesebb, mint egy perc alatt létrehozza a webalkalmazást. A portáloldal tetején lévő harang ikonra kattintva figyelemmel követheti a folyamatot.
   
    ![Folyamatjelző][ConfigProgress]

## A WordPress-webalkalmazás elindítása és kezelése
1. Ha a webalkalmazás létrehozása befejeződött, navigáljon az Azure Portalon ahhoz az erőforráscsoporthoz, amelyben létrehozta az alkalmazást, és tekintse meg a webalkalmazást és az adatbázist.
   
    A villanykörte ikonnal rendelkező további erőforrás az [Application Insights][ApplicationInsights], amely figyelési szolgáltatásokat biztosít a webalkalmazás számára.
2. Az **Erőforráscsoport** panelen kattintson a webalkalmazás sorra.
   
    ![A WordPress webalkalmazás kiválasztása][WordPressSelect]
3. A Webalkalmazás panelen kattintson a **Tallózás** gombra.
   
    ![Keresse meg a WordPress webalkalmazást][WordPressBrowse]
4. Ha a rendszer kéri, hogy válasszon nyelvet a WordPress bloghoz, válassza ki a kívánt nyelvet, majd kattintson a **Folytatás** gombra.
   
    ![A WordPress webalkalmazás nyelvének konfigurálása][WordPressLanguage]
5. A WordPress **Kezdőlapján** adja meg a WordPress által kért konfigurációs információkat, majd kattintson a **WordPress telepítése** gombra.
   
    ![A WordPress webalkalmazás beállításainak konfigurálása][WordPressConfigure]
6. Jelentkezzen be a **Kezdőlapon** létrehozott hitelesítő adatok segítségével.  
7. A webhely irányítópult-oldala nyílik meg, és megjeleníti az Ön által megadott információkat.    
   
    ![A WordPress irányítópult megtekintése][WordPressDashboard2]

## Következő lépések
Ebben az oktatóanyagban megtudhatta, hogyan helyezhet üzembe egy példa-webalkalmazást az Azure Piactérről.

Az App Service Web Apps használatával kapcsolatos további információért lásd a lap bal oldalán (széles böngészőablakok esetén) vagy a lap tetején (keskeny böngészőablakok esetén) lévő hivatkozásokat.

WordPress webalkalmazások Azure platformon való fejlesztésével kapcsolatos további információ: [Developing WordPress on Azure App Service][WordPressOnAzure] (WordPress webalkalmazások fejlesztése az Azure App Service szolgáltatásban). 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[aktiválás]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[ingyenes próba]: https://azure.microsoft.com/pricing/free-trial/
[Az App Service kipróbálása]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure Portal]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png



<!--HONumber=Sep16_HO4-->



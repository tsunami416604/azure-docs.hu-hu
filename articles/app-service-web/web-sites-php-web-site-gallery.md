---
title: WordPress-webalkalmazás létrehozása az Azure App Service szolgáltatásban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy új Azure-webalkalmazást egy WordPress-bloghoz az Azure Portal használatával.
services: app-service\web
documentationcenter: php
author: rmcmurray
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: hero-article
ms.date: 08/11/2016
ms.author: robmcm

---
# WordPress-webalkalmazás létrehozása az Azure App Service szolgáltatásban
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

A jelen oktatóanyag egy Azure Piactérről származó WordPress-blogwebhely üzembe helyezését mutatja be.

Az oktatóanyag befejezésekor saját WordPress-blogja lesz, amely a felhőben fut.

![WordPress-webhely](./media/web-sites-php-web-site-gallery/wpdashboard.png)

Az oktatóanyagból a következőket sajátíthatja el:

* Alkalmazássablon megkeresése az Azure Piactéren.
* Megtudhatja, hogyan hozhat létre sablonalapú webalkalmazást az Azure App Service-ben.
* Megtudhatja, hogyan végezheti el az új webalkalmazáshoz és adatbázishoz tartozó Azure App Service-beállítások konfigurálását.

Az Azure Piactér a Microsoft, külső vállalatok és nyílt forrású szoftver-kezdeményezések által fejlesztett népszerű webalkalmazások széles skáláját teszi elérhetővé. A webalkalmazások a legkülönbözőbb népszerű keretrendszerekre épülnek, mint a [PHP](/develop/nodejs/) ebben a WordPress példában, vagy a [.NET](/develop/net/), a [Node.js](/develop/nodejs/), a [Java](/develop/java/) és a [Python](/develop/python/), csak hogy néhányat említsünk. A webalkalmazások Azure Piactérről való létrehozásához az [Azure Portal](https://portal.azure.com/) eléréséhez használt böngésző az egyetlen szoftver, amelyre szüksége van. 

Az oktatóanyag keretein belül üzembe helyezett WordPress webhely MySQL adatbázist használ. Ha ehelyett SQL Database szolgáltatást használna az adatbázishoz, akkor tekintse meg a [Project Nami](http://projectnami.org/) nevű alkalmazást. A **Project Nami** alkalmazás szintén elérhető a Piactéren keresztül.

> [!NOTE]
> Az oktatóanyag elvégzéséhez Microsoft Azure-fiókra lesz szüksége. Ha nincs fiókja, [aktiválhatja a Visual Studio előfizetői előnyeit](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), vagy [regisztráljon egy ingyenes próbaverzióra](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, menjen [Az App Service kipróbálása](http://go.microsoft.com/fwlink/?LinkId=523751) oldalra. Itt azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## Válassza ki a WordPresst, és konfigurálja azt az Azure App Service-hez
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson az **Új** lehetőségre.
   
    ![Új létrehozása][5]
3. Keresse meg a **WordPress**-alkalmazást, majd kattintson a **WordPress** lehetőségre. Ha a MySQL helyett inkább SQL Database szolgáltatást használna, akkor keresse meg a **Project Nami** alkalmazást.
   
    ![A listában szereplő WordPress][7]
4. A WordPress-alkalmazás leírásának elolvasása után kattintson a **Create** (Létrehozás) gombra.
   
    ![Létrehozás](./media/web-sites-php-web-site-gallery/create.png)
5. A **Web app** (Webalkalmazás) mezőben adjon meg egy nevet a webalkalmazás számára.
   
    Ennek a névnek egyedinek kell lennie az azurewebsites.net tartományban, mert a webalkalmazás URL-címe {név}.azurewebsites.net lesz. Ha a megadott név nem egyedi, egy piros felkiáltójel jelenik meg a szövegmezőben.
6. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni kívánja. 
7. Válasszon egy **erőforráscsoportot**, vagy hozzon létre egy újat.
   
    További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../resource-group-overview.md).
8. Válasszon ki egy **App Service-csomagot/-helyet**, vagy hozzon létre egy újat.
   
    További információk az App Service-csomagokról: [Az Azure App Service-csomagok áttekintése](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 
9. Kattintson a **Database**(Adatbázis) elemre, majd az **New MySQL Database** (Új MySQL-adatbázis) panelen adja meg a MySQL-adatbázis konfigurálásához szükséges értékeket.
   
    a. Írjon be egy új nevet, vagy hagyja meg az alapértelmezett nevet.
   
    b. A **Database Type** (Adatbázistípus) mezőt hagyja **Shared** (Megosztott) értéken.
   
    c. Válassza ki ugyanazt a helyet, amelyet a webalkalmazás számára is választott.
   
    d. Válasszon egy tarifacsomagot. A Merkúr (ingyenes tarifacsomag, minimális megengedett kapcsolódással és lemezterülettel) ideális ehhez az oktatóanyaghoz.
10. A **New MySQL Database** (Új MySQL-adatbázis) panelen kattintson az **OK** gombra. 
11. A **WordPress** panelen fogadja el a jogi feltételeket, majd kattintson a **Create** (Létrehozás) gombra. 
    
     ![A webalkalmazás konfigurálása](./media/web-sites-php-web-site-gallery/configure.png)
    
     Az Azure App Service általában kevesebb, mint egy perc alatt létrehozza a webalkalmazást. A portáloldal tetején lévő harang ikonra kattintva figyelemmel követheti a folyamatot.
    
     ![Folyamatjelző](./media/web-sites-php-web-site-gallery/progress.png)

## A WordPress-webalkalmazás elindítása és kezelése
1. Ha a webalkalmazás létrehozása befejeződött, navigáljon az Azure Portalon ahhoz az erőforráscsoporthoz, amelyben létrehozta az alkalmazást, és tekintse meg a webalkalmazást és az adatbázist.
   
    A villanykörte ikonnal rendelkező további erőforrás az [Application Insights](/services/application-insights/), amely figyelési szolgáltatásokat biztosít a webalkalmazás számára.
2. A **Resource group** (Erőforráscsoport) panelen kattintson a webalkalmazás sorra.
   
    ![A webalkalmazás konfigurálása](./media/web-sites-php-web-site-gallery/resourcegroup.png)
3. A Web app (Webalkalmazás) panelen kattintson a **Browse** (Tallózás) lehetőségre.
   
    ![webhely URL-címe][browse]
4. A WordPress **Kezdőlapján** adja meg a WordPress által kért konfigurációs információkat, majd kattintson az **Install WordPress** (WordPress telepítése) gombra.
   
    ![A WordPress konfigurálása](./media/web-sites-php-web-site-gallery/wpconfigure.png)
5. Jelentkezzen be a **Kezdőlapon** létrehozott hitelesítő adatok segítségével.  
6. Megnyílik a webhelye irányítópultja.    
   
    ![WordPress-webhely](./media/web-sites-php-web-site-gallery/wpdashboard.png)

## Következő lépések
Megtudhatta, hogyan hozhat létre és helyezhet üzembe egy PHP webalkalmazást a katalógusból. A PHP az Azure-ban való használatáról további információkat a következő témakörben talál: [PHP fejlesztői központ](/develop/php/).

Az App Service Web Apps használatával kapcsolatos további információért lásd a lap bal oldalán (széles böngészőablakok esetén) vagy a lap tetején (keskeny böngészőablakok esetén) lévő hivatkozásokat. 

## A változások
* Információk a Websites szolgáltatásról az App Service-re való váltásról: [Az Azure App Service és a hatása a meglévő Azure-szolgáltatásokra](http://go.microsoft.com/fwlink/?LinkId=529714).

[5]: ./media/web-sites-php-web-site-gallery/startmarketplace.png
[7]: ./media/web-sites-php-web-site-gallery/search-web-app.png
[tallózás]: ./media/web-sites-php-web-site-gallery/browse-web.png



<!--HONumber=Sep16_HO4-->



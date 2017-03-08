---
title: "WordPress-alkalmazás üzembe helyezése az Azure Portalon öt perc alatt | Microsoft Docs"
description: "Egy WordPress-alkalmazás üzembe helyezésével megtudhatja, mennyire egyszerű a webappok futtatása az App Service-ben. Az eredmények azonnal láthatók."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/13/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.lasthandoff: 02/28/2017


---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>WordPress-alkalmazás üzembe helyezése az Azure Portalon öt perc alatt

Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe az első alkalommal egy [WordPress](https://wordpress.org/)-webappot percek alatt az [Azure App Service](../app-service/app-service-value-prop-what-is.md) szolgáltatásban.

![WordPress-webhely](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>Előfeltételek
Szüksége van egy Microsoft Azure-fiókra. Ha nincs fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Az [App Service kipróbálása](https://azure.microsoft.com/try/app-service/) Azure-fiók nélkül is lehetséges. Hozzon létre egy kezdő szintű alkalmazást, amellyel legfeljebb egy óráig foglalkozhat – ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="deploy-the-wordpress-app"></a>A WordPress-alkalmazás üzembe helyezése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Nyissa meg a [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress) webhelyet.

    Ez a hivatkozás egy új WordPress-alkalmazás Azure Portalon való azonnali konfigurálására szolgál.

3. Az **Alkalmazás neve** mezőben adja meg a webapp nevét. Ha a név egyedi az `azurewebsites.net` tartományban, megjelenik egy zöld pipa a jelölőnégyzetben.
   
5. Az **Erőforráscsoport** menüben kattintson az **Új létrehozása** elemre egy új [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) létrehozásához, majd nevezze el.

6. Az **Adatbázis-szolgáltató** területen válassza a **CleaDB** elemet.

7. Kattintson az **App Service-csomag/Hely** > **Új létrehozása** lehetőségre. Konfigurálja az [App Service-csomagot](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) az alábbiak szerint:

    - Az **App Service-csomag** mezőbe írja be a kívánt nevet.
    - A **Hely** mezőben válasszon ki egy helyet a csomag üzemeltetésére.
    - Kattintson a **Tarifacsomag** lehetőségre, majd válassza az **F1 – Ingyenes** elemet vagy egy másik megfelelő szintet, majd kattintson a **Kiválasztás** lehetőségre.
    - Kattintson az **OK** gombra.

8. Kattintson az **Adatbázis** > **Új létrehozása** elemre. Állítsa be az SQL Database adatbázist az alábbiak szerint:

    - Az **Adatbázis neve** mezőben adjon meg egy adatbázisnevet. 
    - A **Hely** mezőben válassza ki ugyanazt a helyet, amelyet az App Service-csomag számára is választott.
    - Kattintson a **Tarifacsomag** lehetőségre, majd válassza a **Merkúr** elemet vagy egy másik megfelelő szintet, majd kattintson a **Kiválasztás** lehetőségre.
    - Válassza ki a **Jogi feltételek** elemet, és kattintson a **Vásárlás** lehetőségre.
    - Kattintson az **OK** gombra.

9. Kattintson a **Létrehozás** gombra.

    Az Azure ekkor létrehozza a WordPress-alkalmazást a konfiguráció alapján. A **Központi telepítés elindítva...** értesítést kell látnia

    ![Az üzembe helyezés megkezdődött – az első WordPress az Azure App Service-ben](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>A WordPress-webalkalmazás elindítása és kezelése

Ha az Azure befejezte az alkalmazás üzembe helyezését, megjelenik egy újabb értesítés.

![Az üzembe helyezés sikerült– az első WordPress az Azure App Service-ben](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. Kattintson az értesítésre. Ha lekéste, az értesítési csengőre kattintva mindig elérheti (![Értesítési csengő – az első WordPress az Azure App Service-ben](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Ekkor meg kell jelennie a webapp felügyeleti [paneljének](../azure-resource-manager/resource-group-portal.md#manage-resources) (*panel*: egy vízszintesen megnyíló portáloldal).

3. Az Áttekintés oldal tetején kattintson a **Tallózás** lehetőségre.
   
    ![Tallózás – az első WordPress az Azure App Service-ben](./media/app-service-web-get-started-php-portal/browse.png)

    Ekkor megjelenik a WordPress **Kezdőlapja**. Konfigurálja a WordPress-telepítést, és kísérletezzen vele!

    ![A Wordpress konfigurálása – az első WordPress az Azure App Service-ben](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>Következő lépések
* [Egy Laravel-webapp létrehozása, konfigurálása és üzembe helyezése az Azure-on](app-service-web-php-get-started.md) – Szert tehet a bármely PHP-webapp Azure-ban való futtatásához szükséges alapszintű készségekre, többek között az alábbiakra:

    * Alkalmazások létrehozása és konfigurálása az Azure-ban Powershellből/Bashből.
    * A PHP-verzió beállítása.
    * Egy nem a gyökér-alkalmazáskönyvtárban található indítófájl használata.
    * Composer-automatizálás engedélyezése.
    * Hozzáférés környezetspecifikus változókhoz.
    * Gyakori hibák elhárítása.

* [A kód üzembe helyezése az Azure App Service-ben](web-sites-deploy.md) – Elsajátíthatja az FTP-ről vagy forráskezelő tárházból való üzembe helyezés készségét.
* [Funkciók hozzáadása az első webapphoz](app-service-web-get-started-2.md) – Új szintre emelheti Azure alkalmazását. Hitelesítheti felhasználóit. Igény szerint méretezheti. Beállíthat a teljesítménnyel kapcsolatos riasztásokat. Mindezt csupán néhány kattintással.


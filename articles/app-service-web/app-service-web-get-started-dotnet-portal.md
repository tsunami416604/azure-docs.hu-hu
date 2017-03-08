---
title: "Umbraco-webapp üzembe helyezése az Azure Portalon öt perc alatt | Microsoft Docs"
description: "Egy ASP.NET-mintaalkalmazás üzembe helyezésével megtudhatja, mennyire egyszerű a webalkalmazások futtatása az App Service-ben. Az eredmények azonnal láthatók."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: fa3f31cdd708729071876ffad707bea70567da83
ms.lasthandoff: 02/28/2017


---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>Umbraco-webapp üzembe helyezése az Azure Portalon öt perc alatt

Ez az oktatóanyag segítséget nyújt az [Umbraco](https://our.umbraco.org/)-webalkalmazás gyors üzembe helyezéséhez az [Azure App Service-ben](../app-service/app-service-value-prop-what-is.md).

![Umbraco-alkalmazás](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>Előfeltételek
Szüksége van egy Microsoft Azure-fiókra. Ha nincs fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Az [App Service kipróbálása](https://azure.microsoft.com/try/app-service/) Azure-fiók nélkül is lehetséges. Hozzon létre egy kezdő szintű alkalmazást, amellyel legfeljebb egy óráig foglalkozhat – ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="deploy-the-aspnet-app"></a>Az ASP.NET-alkalmazás üzembe helyezése
1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

2. Nyissa meg a [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS) webhelyet.

    Ez a hivatkozás egy új Umbraco-alkalmazás Azure Portalon való azonnali konfigurálására szolgál.

3. Az **Alkalmazás neve** mezőben adja meg a webapp nevét. Ha a név egyedi az `azurewebsites.net` tartományban, megjelenik egy zöld pipa a jelölőnégyzetben.
   
5. Az **Erőforráscsoport** menüben kattintson az **Új létrehozása** elemre egy új [erőforráscsoport](../azure-resource-manager/resource-group-overview.md) létrehozásához, majd nevezze el.

7. Kattintson az **App Service-csomag/Hely** > **Új létrehozása** lehetőségre. Konfigurálja az [App Service-csomagot](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) az alábbiak szerint:

    - Az **App Service-csomag** mezőbe írja be a kívánt nevet.
    - A **Hely** mezőben válasszon ki egy helyet a csomag üzemeltetésére.
    - Kattintson a **Tarifacsomag** lehetőségre, majd válassza az **F1 – Ingyenes** elemet vagy egy másik megfelelő szintet, majd kattintson a **Kiválasztás** lehetőségre.
    - Kattintson az **OK** gombra.

    Umbraco CMS-konfigurációjának az alábbi képernyőképhez hasonlóan kell kinéznie:

    ![A konfiguráció folyamatban – az első Umbraco-alkalmazás az Azure App Service-ben](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. Kattintson az **SQL Database** > **Új adatbázis létrehozása** lehetőségre. Állítsa be az SQL Database adatbázist az alábbiak szerint:

    - A **Név** mezőben írjon be egy nevet, például a **myDB**-t.
    - Kattintson a **Tarifacsomag** lehetőségre, majd válassza az **F – Ingyenes** elemet vagy egy másik megfelelő szintet, majd kattintson a **Kiválasztás** lehetőségre.
    - Kattintson a **Célkiszogáló** > **Új kiszolgáló létrehozása** lehetőségre. Állítsa be az adatbázist az alábbiak szerint:

        - A **Kiszolgálónév** mezőben adjon meg egy kiszolgálónevet. Ha a név egyedi az `.database.windows.net` tartományban, megjelenik egy zöld pipa a jelölőnégyzetben.
        - A **Kiszolgáló-rendszergazdai bejelentkezés** szövegbeviteli mezőben írja be a kívánt rendszergazdai felhasználónevet.
        - A **Jelszó** és a **Jelszó megerősítése** mezőkbe írja be a kívánt jelszót.
        - A Hely mezőben válassza ki ugyanazt a helyet, amelyet a webappban is használ.
        - Győződjön meg róla, hogy az **Azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** jelölőnégyzet ki van választva.
        - Kattintson a **Kiválasztás** gombra.
    
    - Kattintson a **Kiválasztás** gombra.

13. Kattintson a **Webapp beállításai** lehetőségre, adja meg az adatbázishoz tartozó felhasználónevet és jelszót, majd kattintson az **OK** gombra.

    Umbraco CMS-konfigurációjának az alábbi képernyőképhez hasonlóan kell kinéznie:

    ![A konfiguráció kész – az első Umbraco-alkalmazás az Azure App Service-ben](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. Kattintson a ** Create** (Létrehozás) gombra.
    
    Az Azure ekkor létrehozza az Umbraco-alkalmazást a konfiguráció alapján. A **Központi telepítés elindítva...** értesítést kell látnia

    ![Az üzembe helyezés sikerült– az első Umbraco-alkalmazás az Azure App Service-ben](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Az Umbraco-alkalmazás elindítása és kezelése

Ha az Azure befejezte az alkalmazás üzembe helyezését, megjelenik egy újabb értesítés.

![Az üzembe helyezés sikerült– az első Umbraco-alkalmazás az Azure App Service-ben](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. Kattintson az értesítésre. Ha lekéste, az értesítési csengőre kattintva mindig elérheti (![Értesítési csengő – az első Umbraco az Azure App Service-ben](./media/app-service-web-get-started-dotnet-portal/notification.png)).

    Ekkor meg kell jelennie a webapp felügyeleti [paneljének](../azure-resource-manager/resource-group-portal.md#manage-resources) (*panel*: egy vízszintesen megnyíló portáloldal).

3. Az Áttekintés oldal tetején kattintson a **Tallózás** lehetőségre.
   
    ![Tallózás – az első Umbraco-alkalmazás az Azure App Service-ben](./media/app-service-web-get-started-dotnet-portal/browse.png)

    Ekkor megjelenik az Umbraco **kezdőlapja**. Konfigurálja az Umbraco-telepítést, és kísérletezzen vele!

    ![Az Umbraco konfigurálása – az első Umbraco-alkalmazás az Azure App Service-ben](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>Következő lépések
* [ASP.NET webapp telepítése az Azure App Service szolgáltatásba a Visual Studio használatával](web-sites-dotnet-get-started.md) – Megtudhatja, hogyan hozhat létre új Azure-webappot a Visual Studióból a mellékelt alkalmazássablonok használatával.
* [A kód üzembe helyezése az Azure App Service-ben](web-sites-deploy.md) – Elsajátíthatja az FTP-ről vagy forráskezelő tárházból való üzembe helyezés készségét.
* [Funkciók hozzáadása az első webapphoz](app-service-web-get-started-2.md) – Új szintre emelheti Azure alkalmazását. Hitelesítheti felhasználóit. Igény szerint méretezheti. Beállíthat a teljesítménnyel kapcsolatos riasztásokat. Mindezt csupán néhány kattintással.


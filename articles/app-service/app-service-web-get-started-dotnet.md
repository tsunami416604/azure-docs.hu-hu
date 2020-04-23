---
title: 'Rövid útmutató: C# ASP.NET Core alkalmazás létrehozása'
description: Ismerje meg, hogyan futtathat webalkalmazásokat az Azure App Service-ben az alapértelmezett C# ASP.NET Core webalkalmazás-sablon visual studióból történő telepítésével.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 6c7ab1290033792cf356c4882811670011df5efe
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086045"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rövid útmutató: Hozzon létre egy ASP.NET Core webalkalmazást az Azure-ban

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre és helyezhet üzembe első ASP.NET Core webalkalmazását az [Azure App Service szolgáltatásba.](overview.md) 

Ha elkészült, egy Azure-erőforráscsoportot fog, amely egy App Service-üzemeltetési csomagból és egy telepített webalkalmazással rendelkező App Service-ből áll.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/dotnet/).
- Ez a rövid útmutató telepíti az alkalmazást a Windows App Service szolgáltatásba. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](./containers/quickstart-dotnetcore.md).
- Telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019-et</a> a **ASP.NET és a webfejlesztési** munkaterheléssel.

  Ha már telepítette a Visual Studio 2019-et:

  - Telepítse a legújabb frissítéseket a Visual Studióban a **Súgó** > **frissítések keresése**lehetőséget választva.
  - Adja hozzá a munkaterhelést az **Eszközök** > **be- és szolgáltatások**lehetőséget választva.


## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Hozzon létre egy ASP.NET Core webalkalmazást a Visual Studióban az alábbi lépésekkel:

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.

1. Az **Új projekt létrehozása**csoportban jelölje be ASP.NET Core Web **Application** alkalmazást, és ellenőrizze, hogy a **C#** szerepel-e az adott választás nyelvein, majd válassza a **Tovább**gombot.

1. Az **Új projekt konfigurálása**területen nevezze el a webalkalmazás-projektet *a myFirstAzureWebApp*névre, és válassza a **Létrehozás lehetőséget.**

   ![A webalkalmazás-projekt konfigurálása](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Bármilyen típusú ASP.NET Core webalkalmazást telepíthet az Azure-ba, de ehhez a rövid útmutatóhoz válassza a **Webapplication** sablont. Győződjön meg **arról,** hogy a hitelesítés beállítása **Nincs hitelesítés**, és nincs más lehetőség bejelölve. Ezután kattintson a **Létrehozás** elemre.

   ![Új ASP.NET Core webalkalmazás létrehozása](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. A Visual Studio menüjében válassza a **Hibakeresés hibakeresés** > nélküli hibakeresés**parancsát** a webalkalmazás helyi futtatásához.

   ![Helyileg futó webalkalmazás](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>A webalkalmazás közzététele

A webalkalmazás közzétételéhez először létre kell hoznia és konfigurálnia kell egy új App Service szolgáltatást, amelyen közzéteheti az alkalmazást. 

Az App Service beállításának részeként a következőket hozza létre:

- Egy új [erőforráscsoport,](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) amely tartalmazza a szolgáltatás összes Azure-erőforrását.
- Új [tárhelycsomag,](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) amely megadja az alkalmazást tároló webkiszolgáló-farm helyét, méretét és szolgáltatásait.

Az alábbi lépésekkel hozhatja létre az App Service szolgáltatást, és közzéteheti a webalkalmazást:

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza **a Közzététel parancsot.** Ha még nem jelentkezett be azure-fiókjába a Visual Studióból, válassza **a Fiók hozzáadása** vagy a **Bejelentkezés**lehetőséget. Ingyenes Azure-fiókot is létrehozhat.

1. A **Közzétételi cél kiválasztása** párbeszédpanelen válassza az **App Service**, majd az **Új létrehozása**lehetőséget, majd a Profil **létrehozása lehetőséget.**

   ![Közzétételi cél kiválasztása](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. Az **App Service: Hozzon létre új** párbeszédpanelt, adjon meg egy globálisan egyedi **nevet** az alkalmazásnak az alapértelmezett név elfogadásával vagy egy új név megadásával. Az érvényes `a-z`karakterek `A-Z` `0-9`a `-`következők: , , , és . Ez a **név** a webalkalmazás URL-előtagja `http://<app_name>.azurewebsites.net`a formátumban.

1. **Előfizetés esetén**fogadja el a felsorolt előfizetést, vagy válasszon egy újat a legördülő listából.

1. Az **Erőforrás csoportban**válassza az **Új**lehetőséget. Az **Új erőforráscsoport neve mezőbe**írja be a *myResourceGroup parancsot,* és válassza **az OK gombot.** 

1. A **Tárhelycsomag**esetén válassza az **Új**lehetőséget. 

1. Az **Üzemeltetési terv: Új** párbeszédpanel létrehozása mezőbe írja be az alábbi táblázatban megadott értékeket:

   | Beállítás  | Ajánlott érték | Leírás |
   | -------- | --------------- | ----------- |
   | **Szolgáltatási csomag**  | *myFirstAzureWebAppPlan* | Az App Service-csomag neve. |
   | **Hely**      | *Nyugat-Európa* | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
   | **Méret**          | *Ingyenes* | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |
   
   ![Új tárhelycsomag létrehozása](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Hagyja, hogy **az Application Insights** *nincs.*

1. Az **App Service: Hozzon létre új** párbeszédpanelen válassza **a Létrehozás** lehetőséget az Azure-erőforrások létrehozásának megkezdéséhez.

   ![Új alkalmazásszolgáltatás létrehozása](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. A varázsló befejezése után válassza a **Közzététel**gombot.

   ![Webalkalmazás közzététele az Azure-ban](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   A Visual Studio közzéteszi a ASP.NET Core webalkalmazást az Azure-ban, és elindítja az alkalmazást az alapértelmezett böngészőben. 

   ![Közzétett ASP.NET webalkalmazás fut az Azure-ban](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Gratulálok!** A ASP.NET Core webalkalmazás élőben fut az Azure App Service-ben.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A webalkalmazás frissítéséhez és újratelepítéséhez kövesse az alábbi lépéseket:

1. A **Megoldáskezelőben**a projekt alatt nyissa meg a **Pages** > **Index.cshtml webhelyet.**

1. Cserélje le `<div>` a teljes címkét a következő kódra:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

1. Az Összegzés **közzététele** lapon válassza a **Közzététel**lehetőséget.

   ![Frissítés közzététele a webalkalmazásban](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

![Frissült ASP.NET Azure-ban futó webalkalmazás](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A webalkalmazás kezeléséhez nyissa meg az [Azure Portalt,](https://portal.azure.com)és keresse meg és válassza az **App Services**lehetőséget.

![Alkalmazásszolgáltatások kiválasztása](./media/app-service-web-get-started-dotnet/app-services.png)

Az **App Services** lapon válassza ki a webalkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-dotnet/select-app-service.png)

A webalkalmazás **Áttekintő** lapja az alapvető felügyeleti lehetőségeket tartalmazza, például a tallózást, a leállítást, az indítást, az újraindítást és a törlést. A bal oldali menü további oldalakat biztosít az alkalmazás konfigurálásához.

![Alkalmazásszolgáltatás az Azure Portalon](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a Visual Studiósegítségével hozhat létre és helyezhet üzembe egy ASP.NET Core webalkalmazást az Azure App Service szolgáltatásba.

Lépjen tovább a következő cikkhez, amelyből megtudhatja, hogyan hozhat létre .NET Core alkalmazást, és hogyan csatlakoztathatja azt egy SQL-adatbázishoz:

> [!div class="nextstepaction"]
> [ASP.NET Core és SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

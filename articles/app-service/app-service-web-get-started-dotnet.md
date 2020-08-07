---
title: 'Rövid útmutató: C# ASP.NET Core-alkalmazás létrehozása'
description: Ismerje meg, hogyan futtathat webalkalmazásokat Azure App Service az alapértelmezett C# ASP.NET Core Web App-sablon üzembe helyezésével a Visual studióból.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/06/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 77ff1e907b15b129ef03d7ce799631d6d0a9671d
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986185"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rövid útmutató: ASP.NET Core Webalkalmazás létrehozása az Azure-ban

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre és helyezheti üzembe az első ASP.NET Core webalkalmazását [Azure app Service](overview.md). 

Ha elkészült, egy App Service üzemeltetési csomagból és egy üzembe helyezett webalkalmazással rendelkező App Service álló Azure-erőforráscsoport lesz.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).
- Ez a rövid útmutató üzembe helyez egy alkalmazást a Windows App Service. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](./containers/quickstart-dotnetcore.md).
- Telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

  Ha már telepítette a Visual Studio 2019-et:

  - A **Help**  >  **frissítések keresése**lehetőség kiválasztásával telepítse a legújabb frissítéseket a Visual Studióban.
  - A számítási feladat hozzáadásához **válassza az eszközök**  >  **beolvasása eszközök és szolgáltatások**lehetőséget.


## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Hozzon létre egy ASP.NET Core webalkalmazást a Visual Studióban a következő lépések végrehajtásával:

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása**lehetőséget.

1. Az **új projekt létrehozása**területen válassza ki **ASP.net Core webalkalmazás** elemet, és győződjön meg arról, hogy a **C#** szerepel a választott nyelveken, majd válassza a **tovább**lehetőséget.

1. Az **új projekt konfigurálása**lapon nevezze el a webalkalmazás-projekt *myFirstAzureWebApp*, és válassza a **Létrehozás**lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Bármilyen típusú ASP.NET Core webalkalmazást üzembe helyezhet az Azure-ban, de ebben a rövid útmutatóban a **webalkalmazás** sablont is kiválaszthatja. Győződjön meg arról, hogy a **hitelesítés** **Nincs hitelesítés**, és nincs más lehetőség kiválasztva. Ezután kattintson a **Létrehozás** elemre.

   ![Új ASP.NET Core Webalkalmazás létrehozása](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Helyileg futó webalkalmazás](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Webalkalmazás közzététele

A webalkalmazás közzétételéhez először létre kell hoznia és konfigurálnia kell egy új App Service, amelyre közzé teheti az alkalmazást. 

A App Service beállításának részeként a következőket fogja létrehozni:

- Egy új [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) , amely a szolgáltatáshoz tartozó összes Azure-erőforrást tartalmazza.
- Új [üzemeltetési csomag](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) , amely meghatározza az alkalmazást futtató webkiszolgáló-Farm helyét, méretét és funkcióit.

Kövesse az alábbi lépéseket a App Service létrehozásához és a webalkalmazás közzétételéhez:

1. A **megoldáskezelő**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Közzététel**lehetőséget. Ha még nem jelentkezett be Azure-fiókjába a Visual studióból, válassza **a fiók hozzáadása** vagy a **Bejelentkezés**lehetőséget. Létrehozhat egy ingyenes Azure-fiókot is.

1. A **Közzététel célhelyének** kiválasztása párbeszédpanelen válassza a **app Service**lehetőséget, válassza az **új létrehozása**lehetőséget, majd válassza a **profil létrehozása**lehetőséget.

   ![Közzétételi cél kiválasztása](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. A **app Service: hozzon létre új** párbeszédpanelt, adjon meg egy globálisan egyedi **nevet** az alkalmazásnak az alapértelmezett név elfogadásával vagy egy új név megadásával. Az érvényes karakterek a következők: `a-z` ,, `A-Z` `0-9` és `-` . A rendszer ezt a **nevet** használja a webalkalmazáshoz tartozó URL-előtagként a következő formátumban: `http://<app_name>.azurewebsites.net` .

1. Az **előfizetés**mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.

1. Az **erőforráscsoport**területen válassza az **új**lehetőséget. Az **Új erőforráscsoport neve**mezőbe írja be a *myResourceGroup* nevet, majd kattintson **az OK gombra**. 

1. **Üzemeltetési csomag**esetén válassza az **új**lehetőséget. 

1. A **üzemeltetési tervben: hozzon létre új** párbeszédpanelt, és adja meg a következő táblázatban megadott értékeket:

   | Beállítás  | Ajánlott érték | Leírás |
   | -------- | --------------- | ----------- |
   | **Szolgáltatási csomag**  | *myFirstAzureWebAppPlan* | Az App Service-csomag neve. |
   | **Hely**      | *Nyugat-Európa* | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
   | **Méret**          | *Ingyenes* | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |
   
   ![Új üzemeltetési csomag létrehozása](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Hagyja **Application Insights** beállítást a *none*értékre.

1. Az **app Service: új létrehozása** párbeszédpanelen válassza a **Létrehozás** lehetőséget az Azure-erőforrások létrehozásának megkezdéséhez.

   ![Új App Service létrehozása](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. A varázsló befejezése után válassza a **Közzététel**lehetőséget.

   ![Webalkalmazás közzététele az Azure-ban](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   A Visual Studio közzéteszi ASP.NET Core webalkalmazását az Azure-ban, és elindítja az alkalmazást az alapértelmezett böngészőben. 

   ![Az Azure-ban futó közzétett ASP.NET-webalkalmazás](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Gratulálunk!** A ASP.NET Core webalkalmazása Azure App Serviceon fut.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A webalkalmazás frissítéséhez és újbóli üzembe helyezéséhez kövesse az alábbi lépéseket:

1. **Megoldáskezelő**a projekt alatt nyissa meg a **Pages**  >  **index. cshtml**lapot.

1. Cserélje le a teljes `<div>` címkét a következő kódra:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

1. Az összefoglalás **közzététele** lapon válassza a **Közzététel**lehetőséget.

   ![Frissítés közzététele a webalkalmazásban](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

![Az Azure-ban futó ASP.NET-webalkalmazás frissítve](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A webalkalmazás kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com), és keresse meg, majd válassza a **app Services**lehetőséget.

![App Services kiválasztása](./media/app-service-web-get-started-dotnet/app-services.png)

A **app Services** lapon válassza ki a webalkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-dotnet/select-app-service.png)

A webalkalmazás **áttekintő** lapja olyan alapszintű felügyeleti lehetőségeket tartalmaz, mint a Tallózás, Leállítás, indítás, újraindítás és törlés. A bal oldali menü további lapokat biztosít az alkalmazás konfigurálásához.

![App Service a Azure Portal](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a Visual studiót használta egy ASP.NET Core webalkalmazás létrehozásához és üzembe helyezéséhez Azure App Service.

A következő cikkből megtudhatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztatható SQL Databasehoz:

> [!div class="nextstepaction"]
> [ASP.NET Core és SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

> [!div class="nextstepaction"]
> [ASP.NET Core alkalmazás konfigurálása](configure-language-dotnetcore.md)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

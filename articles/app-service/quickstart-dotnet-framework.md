---
title: 'Rövid útmutató: C# ASP.NET-alkalmazás létrehozása'
description: Megtudhatja, hogyan futtathat webalkalmazásokat Azure App Service az alapértelmezett C# ASP.NET Web App-sablon üzembe helyezésével a Visual studióból.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 08/06/2020
ms.custom: devx-track-csharp, mvc, devcenter, seodec18
ms.openlocfilehash: 5bf774f2b5cf4a6275dc04248aadfd9a94f5122d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212561"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>ASP.NET Framework webalkalmazás létrehozása az Azure-ban

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe az első ASP.NET-webalkalmazását Azure App Service. Ha elkészült, App Service-csomaggal fog rendelkezni. App Service alkalmazást egy üzembe helyezett webalkalmazással is elvégezheti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

Ha már telepítette a Visual Studio 2019-et:

- A **Help**  >  **frissítések keresése**lehetőség kiválasztásával telepítse a legújabb frissítéseket a Visual Studióban.
- A számítási feladat hozzáadásához **válassza az eszközök**  >  **beolvasása eszközök és szolgáltatások**lehetőséget.

## <a name="create-an-aspnet-web-app"></a>ASP.NET-Webalkalmazás létrehozása <a name="create-and-publish-the-web-app"></a>

Hozzon létre egy ASP.NET-webalkalmazást a következő lépések végrehajtásával:

1. Nyissa meg a Visual studiót, majd válassza **az új projekt létrehozása**lehetőséget.

2. Az **új projekt létrehozása**lapon keresse meg és válassza a **ASP.net webalkalmazás (.NET-keretrendszer)** elemet, majd kattintson a **tovább**gombra.

3. Az **új projekt konfigurálása**lapon nevezze el az alkalmazás _myFirstAzureWebApp_, majd válassza a **Létrehozás**lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/quickstart-dotnet-framework/configure-web-app-project-framework.png)

4. Bármilyen ASP.NET-webappot üzembe helyezhet az Azure-ban. Ebben a rövid útmutatóban válassza az **MVC** sablont.

5. Győződjön meg arról, hogy a hitelesítés beállítása **Nincs hitelesítés**. Kattintson a **Létrehozás** gombra.

   ![ASP.NET-Webalkalmazás létrehozása](./media/quickstart-dotnet-framework/select-mvc-template-vs2019.png)

6. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Az alkalmazás futtatása helyileg](./media/quickstart-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Webalkalmazás közzététele <a name="launch-the-publish-wizard"></a>

1. A **megoldáskezelő**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Közzététel**lehetőséget.

1. Válassza a **app Service** lehetőséget, majd válassza a **profil létrehozása**lehetőséget.

   ![Közzététel a projekt áttekintő oldaláról](./media/quickstart-dotnet-framework/publish-app-framework-vs2019.png)

1. **App Service új létrehozásakor**a lehetőségei attól függnek, hogy már bejelentkezett-e az Azure-ba, és hogy van-e Azure-fiókhoz társított Visual Studio-fiókja. Válassza a **fiók hozzáadása** lehetőséget, vagy **Jelentkezzen** be az Azure-előfizetésbe való bejelentkezéshez. Ha már bejelentkezett, válassza ki a kívánt fiókot.

   > [!NOTE]
   > Ha már be van jelentkezve, akkor még ne válassza a **Create** (Létrehozás) lehetőséget.
   >
   >

   ![Bejelentkezés az Azure-ba](./media/quickstart-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Az **erőforráscsoport**területen válassza az **új**lehetőséget.

1. Az **Új erőforráscsoport neve**mezőbe írja be a *myResourceGroup* nevet, majd kattintson **az OK gombra**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. A **csomag üzemeltetéséhez**válassza az **új**lehetőséget.

1. A **üzemeltetési terv konfigurálása** párbeszédpanelen adja meg a következő táblázat értékeit, majd kattintson az **OK gombra**.

   | Beállítás | Ajánlott érték | Description |
   |-|-|-|
   | Szolgáltatási csomag| myAppServicePlan | Az App Service-csomag neve. |
   | Hely | Nyugat-Európa | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
   | Méret | Ingyenes | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |

   ![App Service-csomag létrehozása](./media/quickstart-dotnet-framework/app-service-plan-framework-vs2019.png)

1. A **név**mezőben adjon meg egy egyedi nevet, amely csak az érvényes karaktereket tartalmazza:,, `a-z` `A-Z` `0-9` és `-` . Elfogadhatja az automatikusan létrehozott egyedi nevet is. A webalkalmazás URL-címe `http://<app-name>.azurewebsites.net`, amelyben az `<app-name>` az alkalmazás neve.

2. A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

   ![Az alkalmazás nevének konfigurálása](./media/quickstart-dotnet-framework/web-app-name-framework-vs2019.png)

    A varázsló befejezése után létrejön az Azure-erőforrások, és készen áll a közzétételre.

3. A **közzétételi** lapon kattintson a **Közzététel**elemre. A Visual Studio létrehozza, becsomagolja és közzéteszi az alkalmazást az Azure-ban, majd elindítja az alkalmazást az alapértelmezett böngészőben.

    ![Közzétett ASP.NET-webapp az Azure-ban](./media/quickstart-dotnet-framework/published-azure-web-app.png)

A (z) **app Service új lap létrehozása** lapon megadott alkalmazásnév URL-előtagként lesz megadva a (z `http://<app-name>.azurewebsites.net` ) formátumban.

**Gratulálunk!** A ASP.NET webalkalmazása Azure App Service fut.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

1. A **megoldáskezelő**a projekt alatt nyissa meg a **views**  >  **Home**  >  **index. cshtml**nézetet.

1. Keresse meg a `<div class="jumbotron">` HTML-címkét felül, és cserélje le az egész elemet az alábbi kódra:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet. Ezután válassza a **Közzététel**lehetőséget.

    Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

    ![Frissített ASP.NET-webapp az Azure-ban](./media/quickstart-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

1. A webalkalmazás kezeléséhez lépjen a [Azure Portalra](https://portal.azure.com), és keresse meg, majd válassza a **app Services**lehetőséget.

   ![App Services kiválasztása](./media/quickstart-dotnet-framework/app-services.png)

2. A **app Services** lapon válassza ki a webalkalmazás nevét.

   ![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-dotnet-framework/access-portal-framework-vs2019.png)

   Megtekintheti a webalkalmazás Áttekintés oldalát. Itt olyan alapszintű felügyeletet hajthat végre, mint a Tallózás, a Leállítás, az indítás, az újraindítás és a törlés.

   ![App Service áttekintése Azure Portal](./media/quickstart-dotnet-framework/web-app-general-framework-vs2019.png)

   A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET-alkalmazás és SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [ASP.NET-alkalmazás konfigurálása](configure-language-dotnet-framework.md)

---
title: 'Gyorsindítás: C# ASP.NET alkalmazás létrehozása'
description: Ismerje meg, hogyan futtathat webalkalmazásokat az Azure App Service-ben az alapértelmezett C# ASP.NET webalkalmazás-sablon visual studióból történő telepítésével.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 10/21/2019
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: 4688cc358ec6ff792be58254b0607f5416422a21
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047658"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>ASP.NET Framework webalkalmazás létrehozása az Azure-ban

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.

Ez a rövid útmutató bemutatja, hogyan telepítheti első ASP.NET webalkalmazását az Azure App Service-be. Ha végzett, lesz egy App Service-csomag. Egy telepített webalkalmazással rendelkező App Service-alkalmazással is rendelkezni fog.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019-et</a> a **ASP.NET és a webfejlesztési** munkaterheléssel.

Ha már telepítette a Visual Studio 2019-et:

- Telepítse a legújabb frissítéseket a Visual Studióban a **Súgó** > **frissítések keresése**lehetőséget választva.
- Adja hozzá a munkaterhelést az **Eszközök** > **be- és szolgáltatások**lehetőséget választva.

## <a name="create-an-aspnet-web-app"></a>ASP.NET webalkalmazás létrehozása<a name="create-and-publish-the-web-app"></a>

Hozzon létre egy ASP.NET webalkalmazást az alábbi lépésekkel:

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.

2. Az **Új projekt létrehozása**csoportban keresse meg és válassza ASP.NET **webalkalmazás (.NET Framework)** lehetőséget, majd válassza a **Tovább**gombot.

3. Az **Új projekt konfigurálása**területen nevezze el az alkalmazást a _myFirstAzureWebApp_alkalmazásnak, majd válassza a **Létrehozás lehetőséget.**

   ![A webalkalmazás-projekt konfigurálása](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. Bármilyen ASP.NET-webappot üzembe helyezhet az Azure-ban. Ehhez a rövid útmutatóhoz válassza az **MVC** sablont.

5. Győződjön meg arról, hogy a hitelesítés beállítása **Nincs hitelesítés**. Kattintson a **Létrehozás** gombra.

   ![ASP.NET webalkalmazás létrehozása](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. A Visual Studio menüjében válassza a **Hibakeresés hibakeresés** > nélküli hibakeresés**parancsát** a webalkalmazás helyi futtatásához.

   ![Az alkalmazás futtatása helyileg](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>A webalkalmazás közzététele<a name="launch-the-publish-wizard"></a>

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza **a Közzététel parancsot.**

1. Válassza **az App Service** lehetőséget, majd módosítsa a Profil létrehozása **lehetőséget** **közzétételre.**

   ![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. Az **App Service Új létrehozása**szolgáltatásban a beállítások attól függnek, hogy már be van-e jelentkezve az Azure-ba, és hogy rendelkezik-e Egy Azure-fiókhoz kapcsolódó Visual Studio-fiókkal. Válassza a **Fiók hozzáadása** vagy **a Bejelentkezés** lehetőséget az Azure-előfizetésbe való bejelentkezéshez. Ha már be van jelentkezve, válassza ki a kívánt fiókot.

   > [!NOTE]
   > Ha már be van jelentkezve, akkor még ne válassza a **Create** (Létrehozás) lehetőséget.
   >
   >

   ![Bejelentkezés az Azure-ba](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Az **Erőforrás csoport csoportban**válassza az **Új**lehetőséget.

1. Az **Új erőforráscsoport neve mezőbe**írja be a *myResourceGroup parancsot,* és válassza **az OK gombot.**

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. A **tárhelycsomag esetén**válassza az **Új**lehetőséget.

1. Az **Üzemeltetési terv konfigurálása** párbeszédpanelen adja meg az alábbi táblázat értékeit, majd kattintson az **OK gombra.**

   | Beállítás | Ajánlott érték | Leírás |
   |-|-|-|
   |App Service-csomag| myAppServicePlan | Az App Service-csomag neve. |
   | Hely | Nyugat-Európa | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
   | Méret | Ingyenes | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |

   ![App Service-csomag létrehozása](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. A **Név**mezőbe írjon be egy egyedi `a-z`alkalmazásnevet, amely csak a , `A-Z`, `0-9`és a `-`. Elfogadhatja az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe `http://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

2. A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

   ![Az alkalmazás nevének konfigurálása](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

Miután a varázsló befejeződött, közzéteszi az ASP.NET webalkalmazást az Azure-on, majd elindítja azt az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Az **App Service Új** lap létrehozásá című lapon megadott alkalmazásnév lesz `http://<app_name>.azurewebsites.net`az URL-előtag a formátumban.

**Gratulálok!** A ASP.NET webalkalmazás élőben fut az Azure App Service-ben.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

1. A **Megoldáskezelőben**a projekt alatt nyissa meg a **Nézetek** > **kezdőlapja** > **Index.cshtml fájlt.**

1. Keresse meg a `<div class="jumbotron">` HTML-címkét felül, és cserélje le az egész elemet az alábbi kódra:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet. Ezután válassza **a Közzététel**lehetőséget.

Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

![Frissített ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

1. A webalkalmazás kezeléséhez nyissa meg az [Azure Portalt,](https://portal.azure.com)és keresse meg és válassza **az App Services**lehetőséget.

   ![Alkalmazásszolgáltatások kiválasztása](./media/app-service-web-get-started-dotnet-framework/app-services.png)

2. Az **App Services** lapon válassza ki a webalkalmazás nevét.

   ![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Megtekintheti a webalkalmazás Áttekintés oldalát. Itt olyan alapvető felügyelettel is elvégezheti, mint a tallózás, a leállítás, a kezdés, az újraindítás és a törlés.

   ![Az App Service áttekintése az Azure Portalon](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET-alkalmazás és SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

---
title: ASP.NET C# -keretrendszer Webalkalmazás létrehozása – Azure app Service | Microsoft Docs
description: Az alapértelmezett C# ASP.NET-webalkalmazás üzembe helyezésével megtudhatja, hogy miként futtathat webalkalmazásokat az Azure App Service-ben.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 10/21/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 258e547c58016cb449c74b058d02f2a2e4d7d683
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792708"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>ASP.NET Framework webalkalmazás létrehozása az Azure-ban

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe az első ASP.NET-webalkalmazását Azure App Service. Ha elkészült, App Service-csomaggal fog rendelkezni. App Service alkalmazást egy üzembe helyezett webalkalmazással is elvégezheti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

Ha már telepítette a Visual Studio 2019-et:

- Telepítse a legújabb frissítéseket a Visual Studióban a **súgó** > **frissítések keresése**lehetőség kiválasztásával.
- A számítási feladatok hozzáadásához **válassza az eszközök > ** **eszközök és szolgáltatások beolvasása**lehetőséget.

## ASP.NET-Webalkalmazás létrehozása<a name="create-and-publish-the-web-app"></a>

Hozzon létre egy ASP.NET-webalkalmazást a következő lépések végrehajtásával:

1. Nyissa meg a Visual studiót, majd válassza **az új projekt létrehozása**lehetőséget.

2. Az **új projekt létrehozása**lapon keresse meg és válassza a **ASP.net webalkalmazás (.NET-keretrendszer)** elemet, majd kattintson a **tovább**gombra.

3. Az **új projekt konfigurálása**lapon nevezze el az alkalmazás _myFirstAzureWebApp_, majd válassza a **Létrehozás**lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. Bármilyen ASP.NET-webappot üzembe helyezhet az Azure-ban. Ebben a rövid útmutatóban válassza az **MVC** sablont.

5. Győződjön meg arról, hogy a hitelesítés beállítása **Nincs hitelesítés**. Kattintson a **Létrehozás** gombra.

   ![ASP.NET-Webalkalmazás létrehozása](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. A Visual Studio menüjében válassza a **hibakeresés** > **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Az alkalmazás futtatása helyileg](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Webalkalmazás közzététele<a name="launch-the-publish-wizard"></a>

1. A **megoldáskezelő**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Közzététel**lehetőséget.

1. Válassza a **app Service** lehetőséget, majd a **közzétételhez**módosítsa a **profil létrehozása** elemet.

   ![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. **App Service új létrehozásakor**a lehetőségei attól függnek, hogy már bejelentkezett-e az Azure-ba, és hogy van-e Azure-fiókhoz társított Visual Studio-fiókja. Válassza a **fiók hozzáadása** lehetőséget, vagy **Jelentkezzen** be az Azure-előfizetésbe való bejelentkezéshez. Ha már bejelentkezett, válassza ki a kívánt fiókot.

   > [!NOTE]
   > Ha már be van jelentkezve, akkor még ne válassza a **Create** (Létrehozás) lehetőséget.
   >
   >

   ![Bejelentkezés az Azure-ba](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Az **erőforráscsoport**területen válassza az **új**lehetőséget.

1. Az **Új erőforráscsoport neve**mezőbe írja be a *myResourceGroup* nevet, majd kattintson **az OK gombra**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. A **csomag üzemeltetéséhez**válassza az **új**lehetőséget.

1. A **üzemeltetési terv konfigurálása** párbeszédpanelen adja meg a következő táblázat értékeit, majd kattintson az **OK gombra**.

   | Beállítás | Ajánlott érték | Leírás |
   |-|-|-|
   |App Service-csomag| myAppServicePlan | Az App Service-csomag neve. |
   | Földrajzi egység | Nyugat-Európa | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
   | Méret | Ingyenes | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |

   ![App Service-csomag létrehozása](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. A **név**mezőben adjon meg egy egyedi nevet, amely csak az érvényes karaktereket tartalmazza `a-z`, `A-Z`, `0-9`és `-`. Elfogadhatja az automatikusan létrehozott egyedi nevet is. A webalkalmazás URL-címe `http://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

2. A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

   ![Az alkalmazás nevének konfigurálása](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

Miután a varázsló befejeződött, közzéteszi az ASP.NET webalkalmazást az Azure-on, majd elindítja azt az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

A **app Service új oldal létrehozása** lapon megadott alkalmazásnév URL-előtagként van megadva a (z) `http://<app_name>.azurewebsites.net`formátumban.

**Gratulálunk!** A ASP.NET webalkalmazása Azure App Service fut.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

1. **Megoldáskezelő**a projekt alatt nyissa meg a **nézetek** > **Home** > **index. cshtml**.

1. Keresse meg a `<div class="jumbotron">` HTML-címkét felül, és cserélje le az egész elemet az alábbi kódra:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet. Ezután válassza a **Közzététel**lehetőséget.

Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

![Frissített ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

1. Ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>, és felügyelje a létrehozott webalkalmazást.

2. A bal oldali menüben válassza a **app Services**lehetőséget, majd válassza ki az Azure-alkalmazás nevét.

   ![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Megtekintheti a webalkalmazás Áttekintés oldalát. Itt olyan alapszintű felügyeletet hajthat végre, mint a Tallózás, a Leállítás, az indítás, az újraindítás és a törlés.

   ![App Service áttekintése Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET-alkalmazás és SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

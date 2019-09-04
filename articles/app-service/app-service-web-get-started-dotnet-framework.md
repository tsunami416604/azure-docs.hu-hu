---
title: ASP.NET C# -keretrendszer Webalkalmazás létrehozása – Azure app Service | Microsoft Docs
description: Az alapértelmezett C# ASP.NET-webalkalmazás üzembe helyezésével megtudhatja, hogy miként futtathat webalkalmazásokat az Azure App Service-ben.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4f479ad60d74f1c7381b5fb776c5508aaa0785f1
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242278"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>ASP.NET Framework webalkalmazás létrehozása az Azure-ban

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe az első ASP.NET-webalkalmazását Azure App Service. Ha elkészült, App Service-csomaggal fog rendelkezni. App Service alkalmazást egy üzembe helyezett webalkalmazással is elvégezheti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

Ha már telepítette a Visual Studio 2019-et:

- A**frissítések keresése**lehetőség **kiválasztásával** > telepítse a legújabb frissítéseket a Visual Studióban.
- A számítási feladat hozzáadásához **válassza az eszközök** > **beolvasása eszközök és szolgáltatások**lehetőséget.

## ASP.NET-Webalkalmazás létrehozása<a name="create-and-publish-the-web-app"></a>

Hozzon létre egy ASP.NET-webalkalmazást a következő lépések végrehajtásával:

1. Nyissa meg a Visual studiót, majd válassza **az új projekt létrehozása**lehetőséget.

1. Az **új projekt létrehozása**lapon keresse meg és válassza a **ASP.net webalkalmazás (.NET-keretrendszer)** elemet, majd kattintson a C# **tovább**gombra.

1. Az **új projekt konfigurálása**lapon nevezze el az alkalmazás _myFirstAzureWebApp_, majd válassza a **Létrehozás**lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

1. Bármilyen ASP.NET-webappot üzembe helyezhet az Azure-ban. Ebben a rövid útmutatóban válassza az **MVC** sablont. 

1. Győződjön meg arról, hogy a hitelesítés beállítása **Nincs hitelesítés**. Kattintson a **Létrehozás** gombra.

   ![ASP.NET-Webalkalmazás létrehozása](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

1. A Visual Studio menüjében válassza a **hibakeresés** > **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Az alkalmazás futtatása helyileg](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Webalkalmazás közzététele<a name="launch-the-publish-wizard"></a>

1. A **megoldáskezelő**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Közzététel**lehetőséget.

1. Válassza a **app Service** lehetőséget, majd válassza a **Közzététel**lehetőséget.

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
   | Location | Nyugat-Európa | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
   | Size | Ingyenes | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |

   ![App Service-csomag létrehozása](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. A **név**mezőben adjon meg egy `a-z`egyedi nevet, amely csak az érvényes karaktereket `A-Z` `0-9`tartalmazza:,, és `-`. Elfogadhatja az automatikusan létrehozott egyedi nevet is. A webalkalmazás URL-címe `http://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

   ![Az alkalmazás nevének konfigurálása](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

1. A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

Miután a varázsló befejeződött, közzéteszi az ASP.NET webalkalmazást az Azure-on, majd elindítja azt az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

A (z) **app Service új lap létrehozása** lapon megadott alkalmazásnév URL-előtagként lesz megadva a ( `http://<app_name>.azurewebsites.net`z) formátumban.

**Gratulálunk!** A ASP.NET webalkalmazása Azure App Service fut.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

1. A **megoldáskezelő**a projekt alatt nyissa meg a **views** > **Home** > **index. cshtml**nézetet.

1. Keresse meg a `<div class="jumbotron">` HTML-címkét felül, és cserélje le az egész elemet az alábbi kódra:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

1. Az összefoglalás **közzététele** lapon válassza a **Közzététel**lehetőséget.

   ![A Visual Studio összefoglaló lapja közzétételre](./media/app-service-web-get-started-dotnet-framework/publish-summary-page-framework-vs2019.png)

Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

![Frissített ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

1. Ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>, és felügyelje a létrehozott webalkalmazást.

1. A bal oldali menüben válassza a **app Services**lehetőséget, majd válassza ki az Azure-alkalmazás nevét.

   ![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Megtekintheti a webalkalmazás Áttekintés oldalát. Itt olyan alapszintű felügyeletet hajthat végre, mint a Tallózás, a Leállítás, az indítás, az újraindítás és a törlés.

   ![App Service áttekintése Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

## <a name="video"></a>Videó

Tekintse meg a videót, amelyben működés közben láthatja ezt a gyorsútmutatót, majd kövesse végig a lépéseket az első .NET-alkalmazása közzétételéhez az Azure-on.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET-alkalmazás és SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

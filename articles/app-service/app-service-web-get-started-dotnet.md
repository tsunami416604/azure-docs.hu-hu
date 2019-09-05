---
title: ASP.NET Core C# Webalkalmazás létrehozása – Azure app Service | Microsoft Docs
description: Megtudhatja, hogyan futtathat webalkalmazásokat Azure App Service az alapértelmezett C# ASP.net Core-webalkalmazás üzembe helyezésével.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e8c6c21efd1ea406c8293819a2889e198e8455bd
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242133"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>ASP.NET Core-webalkalmazás létrehozása az Azure-ban

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](./containers/quickstart-dotnetcore.md).
>

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe az első ASP.NET Core webalkalmazását Azure App Service. Ha elkészült, egy olyan erőforráscsoporthoz fog rendelkezni, amely egy App Service csomagot és egy App Service alkalmazást tartalmaz egy telepített webalkalmazással.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

Ha már telepítette a Visual Studio 2019-et:

- A**frissítések keresése**lehetőség **kiválasztásával** > telepítse a legújabb frissítéseket a Visual Studióban.
- A számítási feladat hozzáadásához **válassza az eszközök** > **beolvasása eszközök és szolgáltatások**lehetőséget.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Hozzon létre egy ASP.NET Core webalkalmazást a következő lépések végrehajtásával:

1. Nyissa meg a Visual studiót, majd válassza **az új projekt létrehozása**lehetőséget.

1. Az **új projekt létrehozása**lapon keresse meg és válassza ki **ASP.net Core webalkalmazást** , majd kattintson a C# **tovább**gombra.

1. Az **új projekt konfigurálása**lapon nevezze el az alkalmazás _myFirstAzureWebApp_, majd válassza a **Létrehozás**lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Ebben a rövid útmutatóban válassza a **webalkalmazás** sablont. Győződjön meg arról, hogy a hitelesítés **Nincs hitelesítés** , és nincs másik lehetőség kiválasztva. Kattintson a **Létrehozás** gombra.

   ![Az oktatóanyaghoz tartozó ASP.NET Core Razor Pages kiválasztása](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    Bármilyen típusú ASP.NET Core-webalkalmazást üzembe helyezhet az Azure-ban.

1. A Visual Studio menüjében válassza a **hibakeresés** > **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Az alkalmazás futtatása helyileg](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Webalkalmazás közzététele

1. A **megoldáskezelő**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Közzététel**lehetőséget.

1. Válassza a **app Service** lehetőséget, majd válassza a **Közzététel**lehetőséget.

   ![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. **App Service új létrehozásakor**a lehetőségei attól függnek, hogy már bejelentkezett-e az Azure-ba, és hogy van-e Azure-fiókhoz társított Visual Studio-fiókja. Válassza a **fiók hozzáadása** lehetőséget, vagy **Jelentkezzen** be az Azure-előfizetésbe való bejelentkezéshez. Ha már bejelentkezett, válassza ki a kívánt fiókot.

   > [!NOTE]
   > Ha már be van jelentkezve, akkor még ne válassza a **Create** (Létrehozás) lehetőséget.
   >

   ![Bejelentkezés az Azure-ba](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

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

   ![App Service-csomag létrehozása](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. A **név**mezőben adjon meg egy `a-z`egyedi nevet, amely csak az érvényes karaktereket `A-Z` `0-9`tartalmazza:,, és `-`. Elfogadhatja az automatikusan létrehozott egyedi nevet is. A webalkalmazás URL-címe `http://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

   ![Az alkalmazás nevének konfigurálása](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

Miután a varázsló befejeződött, közzéteszi az ASP.NET Core-webalkalmazást az Azure-ban, majd elindítja azt az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

A (z) **app Service új lap létrehozása** lapon megadott alkalmazásnév URL-előtagként lesz megadva a ( `http://<app_name>.azurewebsites.net`z) formátumban.

**Gratulálunk!** A ASP.NET Core webalkalmazása Azure App Serviceon fut.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

1. **Megoldáskezelő**a projekt alatt nyissa meg a **Pages** > **index. cshtml**lapot.

1. Cserélje le a két `<div>` címkét az alábbi kódra:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

1. Az összefoglalás **közzététele** lapon válassza a **Közzététel**lehetőséget.

   ![A Visual Studio Publishing összefoglaló lapja](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

![Frissített ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

1. Ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>, és felügyelje a létrehozott webalkalmazást.

1. A bal oldali menüben válassza a **app Services**lehetőséget, majd válassza ki az Azure-alkalmazás nevét.

   ![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

   Megtekintheti a webalkalmazás Áttekintés oldalát. Itt olyan alapszintű felügyeletet hajthat végre, mint a Tallózás, a Leállítás, az indítás, az újraindítás és a törlés.

   ![App Service a Azure Portal](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

   A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET Core és SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

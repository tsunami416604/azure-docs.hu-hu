---
title: Hozzon létre C# ASP.NET Framework webalkalmazás – az Azure App Service |} A Microsoft Docs
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
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: df87983e6a56ac4e37c65f66e3408cb3296f24d8
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597528"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>ASP.NET Framework webalkalmazás létrehozása az Azure-ban

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  A rövid útmutató bemutatja, hogyan helyezhet üzembe az első ASP.NET-webalkalmazását az Azure App Service-ben. Az oktatóanyag végére egy olyan erőforráscsoport lesz elérhető, amely egy App Service-csomagból és egy üzembe helyezett webalkalmazással rendelkező Azure webalkalmazásból áll.

![](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017-et</a> az **ASP.NET és webfejlesztési** számítási feladattal.

Ha már telepítette a Visual Studio 2017-et:

- Telepítse a legújabb frissítéseket a Visual Studióban a **Help** > **Check for Updates** (Súgó, Frissítések keresése) lehetőségre kattintva.
- Adja hozzá a számítási feladatokat a **Tools** > **Get Tools and Features** (Eszközök, Eszközök és funkciók beszerzése) elemre kattintva.

## <a name="create-an-aspnet-web-app"></a>ASP.NET-webapp létrehozása

Hozzon létre egy projektet a Visual Studióban a **File > New > Project** (Fájl > Új > Projekt) lehetőség kiválasztásával. 

A **New Project** (Új projekt) párbeszédpanelen válassza a **Visual C# > Web > ASP.NET Web Application (.NET Framework)** (ASP.NET-webalkalmazás (.NET-keretrendszer)) elemet.

Nevezze el az alkalmazást _myFirstAzureWebApp_ néven, majd kattintson az **OK** gombra.
   
![A New Project (Új projekt) párbeszédpanel](./media/app-service-web-get-started-dotnet-framework/new-project.png)

Bármilyen ASP.NET-webappot üzembe helyezhet az Azure-ban. Ennél a gyorsútmutatónál válassza az **MVC** sablont, és ügyeljen arra, hogy a hitelesítés beállítása **No Authentication** (Nincs hitelesítés) legyen.
      
Kattintson az **OK** gombra.

![A New ASP.NET Project (Új ASP.NET-projekt) párbeszédpanel](./media/app-service-web-get-started-dotnet-framework/select-mvc-template.png)

A menüből válassza a **Debug > Start without Debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a webalkalmazás helyi futtatásához.

![Az alkalmazás futtatása helyileg](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="launch-the-publish-wizard"></a>A közzétételi varázsló elindítása

A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Publish** (Közzététel) elemet.

![Közzététel a Megoldáskezelőből](./media/app-service-web-get-started-dotnet-framework/solution-explorer-publish.png)

A közzétételi varázsló automatikusan elindul. Válassza az **App Service** > **Publish** (App Service, Közzététel) lehetőséget a **Create App Service** (App Service létrehozása) párbeszédpanel megnyitásához.

![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-dotnet-framework/publish-to-app-service.png)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A **Create App Service** (App Service létrehozása) párbeszédpanelen kattintson az **Add an account** (Fiók hozzáadása) gombra, majd jelentkezzen be az Azure-előfizetésébe. Ha már bejelentkezett, válassza ki a kívánt előfizetést tartalmazó fiókot a legördülő listából.

> [!NOTE]
> Ha már be van jelentkezve, akkor még ne válassza a **Create** (Létrehozás) lehetőséget.
>
>
   
![Bejelentkezés az Azure-ba](./media/app-service-web-get-started-dotnet-framework/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Az **Erőforráscsoport** mellett válassza az **Új** elemet.

Nevezze el a **myResourceGroup** erőforráscsoportot, majd kattintson az **OK** gombra.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

A **Szolgáltatási csomag** mellett válassza az **Új** elemet. 

A **Szolgáltatási csomag konfigurálása** párbeszédpanelen a képernyőképet követve használja a táblázatban szereplő beállításokat.

![App Service-csomag létrehozása](./media/app-service-web-get-started-dotnet-framework/configure-app-service-plan.png)

| Beállítás | Ajánlott érték | Leírás |
|-|-|-|
|App Service-csomag| myAppServicePlan | Az App Service-csomag neve. |
| Hely | Nyugat-Európa | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Méret | Ingyenes | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |

Kattintson az **OK** gombra.

## <a name="create-and-publish-the-web-app"></a>A webapp létrehozása és közzététele

Az **Alkalmazás neve** mezőben adjon meg egy egyedi nevet az alkalmazás számára (érvényes karakterek: `a-z`, `0-9` és `-`), vagy fogadja el az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe `http://<app_name>.azurewebsites.net`, amelyben az `<app_name>` az alkalmazás neve.

A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

![Az alkalmazás nevének konfigurálása](./media/app-service-web-get-started-dotnet-framework/web-app-name.png)

Miután a varázsló befejeződött, közzéteszi az ASP.NET webalkalmazást az Azure-on, majd elindítja azt az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

A [létrehozási és közzétételi lépésben](#create-and-publish-the-web-app) megadott alkalmazásnév lesz az URL-előtag a következő formátumban: `http://<app_name>.azurewebsites.net`.

Gratulálunk, az ASP.NET-webalkalmazás immáron elérhető az Azure App Service-ben.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A **Solution Explorer** (Megoldáskezelő) lapon nyissa meg a következőt: _Views\Home\Index.cshtml_.

Keresse meg a `<div class="jumbotron">` HTML-címkét felül, és cserélje le az egész elemet az alábbi kódra:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

A közzétételi oldalon válassza a **Publish** (Közzététel) elemet.
![A Visual Studio összefoglaló közzétételi oldala](./media/app-service-web-get-started-dotnet-framework/publish-summary-page.png)

Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

![Frissített ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-web-app"></a>Az Azure webalkalmazás felügyelete

Ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>, és felügyelje a létrehozott webalkalmazást.

A baloldali menüben válassza az **App Services** lehetőséget, majd az Azure-webalkalmazás nevét.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-get-started-dotnet-framework/access-portal.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service panel az Azure Portalon](./media/app-service-web-get-started-dotnet-framework/web-app-blade.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

## <a name="video"></a>Videó

Tekintse meg a videót, amelyben működés közben láthatja ezt a gyorsútmutatót, majd kövesse végig a lépéseket az első .NET-alkalmazása közzétételéhez az Azure-on.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET-alkalmazás és SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

---
title: "Hozza létre az első ASP.NET-webalkalmazását öt perc alatt az Azure-ban | Microsoft Docs"
description: "Egy egyszerű ASP.NET-alkalmazás üzembe helyezésével megtudhatja, mennyire egyszerű a webappok futtatása az App Service-ben."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 24ddf0d81e52960bd582861d324585b8ba0ca357
ms.lasthandoff: 04/18/2017


---
# <a name="create-your-first-aspnet-web-app-in-azure-in-five-minutes"></a>Hozza létre az első ASP.NET-webalkalmazását öt perc alatt az Azure-ban

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

A rövid útmutató segítségével csupán pár perc alatt üzembe helyezheti az első ASP.NET-webalkalmazását az [Azure App Service-ben](../app-service/app-service-value-prop-what-is.md). Az oktatóanyag végére egy egyszerű, működő webapp lesz elérhető a felhőben.

![ASP.NET-webapp az Azure App Service-ben](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag bemutatja, hogyan hozhatja létre és helyezheti üzembe az ASP.NET-webappot az Azure-ban a Visual Studio 2017 használatával Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-aspnet-web-app"></a>ASP.NET-webapp létrehozása

Hozzon létre egy új projektet a Visual Studióban a `Ctrl`+`Shift`+`N` billentyűparancs lenyomásával.

A **New Project** (Új projekt) párbeszédpanelen válassza a **Visual C# > Web > ASP.NET Web Application (.NET Framework)** (ASP.NET-webapp (.NET-keretrendszer)) elemet.

Nevezze el az alkalmazást **myFirstAzureWebApp** néven, majd kattintson az **OK** gombra.
   
![A New Project (Új projekt) párbeszédpanel](./media/app-service-web-get-started-dotnet/new-project.png)

Bármilyen ASP.NET-webappot üzembe helyezhet az Azure-ban. Ennél az oktatóanyagnál válassza az **MVC** sablont, és ügyeljen arra, hogy a hitelesítés beállítása **No Authentication** (Nincs hitelesítés) legyen.
      
Kattintson az **OK** gombra.

![A New ASP.NET Project (Új ASP.NET-projekt) párbeszédpanel](./media/app-service-web-get-started-dotnet/select-mvc-template.png)

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Publish** (Közzététel) elemet.

![Közzététel a Megoldáskezelőből](./media/app-service-web-get-started-dotnet/solution-explorer-publish.png)

Győződjön meg arról, hogy a **Microsoft Azure App Service** van kiválasztva, és kattintson a **Publish** (Közzététel) elemre.

![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

Ez megnyitja a **Create App Service** (App Service létrehozása) párbeszédpanelt, amely segítségével létrehozhatja az összes, az ASP.NET-webapp Azure-ban való futtatásához szükséges Azure-erőforrást.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A **Create App Service** (App Service létrehozása) párbeszédpanelen kattintson az **Add an account** (Fiók hozzáadása) gombra, majd jelentkezzen be az Azure-előfizetésébe. Ha már be van jelentkezve egy Microsoft-fiókba, győződjön meg arról, hogy abban a fiókban található az előfizetése. Ha nem abban a Microsoft-fiókban van az Azure-előfizetése, amelyikbe be van jelentkezve, kattintással adja hozzá a helyes fiókot.
   
![Bejelentkezés az Azure-ba](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

Ha már bejelentkezett, ezen a panelen már létre is hozhatja az összes, az Azure-webapphoz szükséges erőforrást.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Először is szüksége van egy _erőforráscsoportra_. 

> [!NOTE] 
> Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például webappokat, adatbázisokat és tárfiókokat).
>
>

A **Resource Group** (Erőforráscsoport) mellett kattintson a **New** (Új) elemre.

Adja az erőforráscsoportnak a **myResourceGroup** nevet, majd kattintson az **OK** gombra.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Az Azure-webappnak egy _App Service-csomagra_ is szüksége van. 

> [!NOTE]
> Az App Service-csomagok az alkalmazások üzemeltetéséhez használt fizikai erőforrások gyűjteményét jelölik. Az App Service-csomaghoz rendelt összes alkalmazás ugyanazokat az általa meghatározott erőforrásokat használja, így több alkalmazás üzemeltetése esetén csökkenthetők a költségek. 
>
> Az App Service-csomagok a következőket határozzák meg:
>
> - Régió (Észak-Európa, az USA keleti régiója, Délkelet-Ázsia)
> - Példányméret (kicsi, közepes, nagy)
> - Méretezési szám (egy, kettő vagy három példány stb.) 
> - Termékváltozat (Ingyenes, Közös, Alapszintű, Standard, Prémium)
>
>

Az **App Service Plan** (App Service-csomag) mellett kattintson a **New** (Új) elemre. 

A **Configure App Service Plan** (App Service-csomag konfigurálása) párbeszédpanelen konfigurálja az új App Service-csomagot a következő beállításokkal:

- **App Service Plan** (App Service-csomag): Írja be a következőt: **myAppServicePlan**. 
- **Location** (Hely): Válassza a **West Europe** (Nyugat-Európa) régiót, vagy bármelyik másik régiót.
- **Size** (Méret): Válassza a **Free** (Ingyenes) vagy bármely másik [tarifacsomagot](https://azure.microsoft.com/pricing/details/app-service/).

Kattintson az **OK** gombra.

![Új App Service-csomag létrehozása](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

## <a name="create-and-publish-the-web-app"></a>A webapp létrehozása és közzététele

Már csak a webapp elnevezése van hátra. A **Web App Name** (Webapp neve) mezőben adjon meg egy egyedi nevet az alkalmazáshoz. Ez a név az alkalmazás alapértelmezett DNS-nevének részeként lesz használatban (`<app_name>.azurewebsites.net`), ezért egyedinek kell lennie az Azure összes alkalmazása között. Később leképezhet egy egyéni tartománynevet az alkalmazásához, mielőtt elérhetővé teszi a felhasználók számára.

Elfogadhatja az automatikusan létrehozott nevet is, amely már egyedi.

A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

![A webapp nevének konfigurálása](./media/app-service-web-get-started-dotnet/web-app-name.png)

Ha a varázsló befejezte az Azure-erőforrások létrehozását, automatikusan közzéteszi első alkalommal az ASP.NET-webappot az Azure-ban, majd elindítja a közzétett Azure-webappot az alapértelmezett böngészőben.

![Közzétett ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet/published-azure-web-app.png)

Az URL-cím a webapp korábban megadott nevét használja, a következő formátummal: `http://<app_name>.azurewebsites.net`. 

Gratulálunk, az első ASP.NET-webalkalmazása immáron elérhető az Azure App Service-ben!

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A frissítés az Azure-ban rendkívül könnyen újra üzembe helyezhető. Frissítsük a kezdőlapot.

A **Solution Explorer** (Megoldáskezelő) lapon nyissa meg a következőt: **Views\Home\Index.cshtml**.

Keresse meg a `<div class="jumbotron">` HTML-címkét felül, és cserélje le az egész címkét az alábbi kódra:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

A közzétételi oldalon kattintson a **Publish** (Közzététel) elemre.

Amikor a Visual Studio elkészült, elindítja a frissített Azure-webappot a böngészőben.

![Frissített ASP.NET-webapp az Azure-ban](./media/app-service-web-get-started-dotnet/updated-azure-web-app.png)

## <a name="manage-your-new-azure-web-app"></a>Az új Azure-webapp kezelése

Az Azure Portalon tekintse meg a létrehozott webappot. 

Ehhez jelentkezzen be a következő címen: [https://portal.azure.com](https://portal.azure.com).

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-get-started-dotnet/access-portal.png)

Ekkor a webapp _paneljére_ (vízszintesen megnyíló portáloldalára) jut. 

Alapértelmezés szerint a webapp panelje az **Áttekintés** oldalt mutatja. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. A panel bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg. 

![Az App Service panel az Azure Portalon](./media/app-service-web-get-started-dotnet/web-app-blade.png)

A panel ezen lapja a webapphoz hozzáadható nagyszerű szolgáltatásokat jelenítik meg. Az alábbi lista csupán néhány lehetőséget sorol fel:

- Egyéni DNS-név leképezése
- Egyéni SSL-tanúsítvány kötése
- Folyamatos üzembe helyezés konfigurálása
- Vertikális felskálázás és kibővítés
- Felhasználói hitelesítés hozzáadása

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Első Azure-webappja törléséhez kattintson a **Törlés** parancsra az **Áttekintés** oldalon. Azonban hatékonyabb mód is létezik a gyors üzembe helyezés során létrehozott elemek törlésére. A webapp **Áttekintés** oldalán kattintson az erőforráscsoportra a panel megnyitásához. 

![Hozzáférés az erőforráscsoporthoz az App Service panelről](./media/app-service-web-get-started-dotnet/access-resource-group.png)

Az erőforráscsoport panelen láthatja a Visual Studio által létrehozott App Service-csomagot és App Service alkalmazást. 

A panel tetején kattintson a **Törlés** parancsra. 

<!--![Delete resource group in Azure portal](./media/app-service-web-get-started-dotnet/delete-resource-group.png)-->

A megerősítési panelen írja be az erőforráscsoport nevét (**myResourceGroup**) a szövegmezőbe, és kattintson a **Törlés** parancsra a megerősítéshez.

## <a name="next-steps"></a>Következő lépések

Az előre létrehozott [Web Apps PowerShell-szkriptek](app-service-powershell-samples.md) vizsgálata.


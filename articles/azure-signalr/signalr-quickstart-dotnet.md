---
title: Fejlesztés a ASP.NET - Azure SignalR szolgáltatás
description: Rövid útmutató az Azure SignalR-szolgáltatás használatával egy csevegőszoba létrehozásához ASP.NET keretrendszerrel.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: ec5b7a75bced4b7cd81a120925558b8c1be57818
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74158173"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Rövid útmutató: Csevegőszoba létrehozása ASP.NET és SignalR szolgáltatással

Az Azure SignalR szolgáltatás [a SignalR for ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction)szolgáltatáson alapul, amely **nem** 100%-ban kompatibilis ASP.NET SignalR-rel. Az Azure SignalR szolgáltatás újra megvalósította ASP.NET SignalR adatprotokollt a legújabb ASP.NET Core technológiák alapján. Az Azure SignalR Service for ASP.NET SignalR használatakor a SignalR egyes ASP.NET a SignalR-funkciók már nem támogatottak, például az Azure SignalR nem játssza le újra az üzeneteket, amikor az ügyfél újracsatlakozik. Emellett a Forever Frame átvitel és a JSONP nem támogatott. A ASP.NET SignalR alkalmazás működéséhez szükség van a kódmódosításokra és a függő kódtárak megfelelő verziójára. 

A ASP.NET SignalR és ASP.NET Core SignalR szolgáltatásösszehasonlításának teljes listáját a [dokumentum verziókülönbségei](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) között olvassa el.

Ebben a rövid útmutatóban megtudhatja, hogyan kezdheti el a ASP.NET és az Azure SignalR szolgáltatást egy hasonló [csevegőszoba-alkalmazáshoz.](./signalr-quickstart-dotnet-core.md)


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*A kiszolgáló nélküli* mód nem támogatott ASP.NET SignalR alkalmazásokban. Mindig az *Alapértelmezett* vagy *a Klasszikus* az Azure SignalR service-példány.

Ebben a rövid útmutatóban használt Azure-erőforrásokat is létrehozhat [a SignalR-szolgáltatás parancsfájl létrehozása segítségével.](scripts/signalr-cli-create-service.md)

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Amíg a szolgáltatás üzembe helyezése folyamatban van, térjünk át a kóddal folytatott munkavégzésre. Klónozza a [mintaalkalmazást a GitHubról](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), állítsa be a SignalR Service kapcsolati sztringjét, és futtassa helyileg az alkalmazást.

1. Nyisson meg egy git terminálablakot. Váltson át arra a mappára, ahová a mintaprojektet klónozni szeretné.

1. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>A Csevegőszoba webalkalmazás konfigurálása és futtatása

1. Indítsa el a Visual Studio alkalmazást, és nyissa meg a megoldást a klónozott tárház *aspnet-samples/ChatRoom/mappájában.*

1. Abban a böngészőben, ahol az Azure Portal meg van nyitva, keresse meg és válassza ki a létrehozott példányt.

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

1. Most állítsa be a kapcsolati karakterláncot a web.config fájlban.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. A *Startup.cs*a `MapSignalR()`hívás helyett meg `MapAzureSignalR({your_applicationName})` kell hívnia és át kell adnia a kapcsolati karakterláncot, hogy az alkalmazás a SignalR üzemeltetése helyett a szolgáltatáshoz csatlakozzon. Cserélje `{YourApplicationName}` le az alkalmazás nevét. Ez a név egy egyedi név, amely megkülönbözteti ezt az alkalmazást a többi alkalmazástól. Értékként `this.GetType().FullName` is használható.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Ezen API-k használata előtt is hivatkoznia kell az SDK szolgáltatásra. Az **eszközök megnyitása | NuGet csomagkezelő | A Csomagkezelő konzol és** a futtatás parancs:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Más, mint ezek a változások, minden más ugyanaz marad, akkor is használhatja a hub felület már ismeri az üzleti logika írásához.

    > [!NOTE]
    > A megvalósítás ban `/signalr/negotiate` egy végpont van elérhető az Azure SignalR Service SDK egyeztetésére. Speciális egyeztetési választ ad vissza, amikor az ügyfelek megpróbálnak csatlakozni, és átirányítani az ügyfeleket a kapcsolati karakterláncban meghatározott szolgáltatásvégpontra.

1. Nyomja **le az F5** billentyűt a projekt hibakeresési módban való futtatásához. Láthatja, hogy az alkalmazás helyileg fut. Ahelyett, hogy egy SignalR-futásidejű alkalmazás maga üzemeltetésében, most csatlakozik az Azure SignalR szolgáltatáshoz.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
> 
> 

Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. Ebben a rövid útmutatóban a *SignalRTestResources* nevű erőforráscsoportot használtuk. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** elemre.

   
![Törlés](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új Azure SignalR Service-erőforrást, és egy ASP.NET webalkalmazással használta. Ezután megtudhatja, hogyan fejleszthet valós idejű alkalmazásokat az Azure SignalR-szolgáltatás és a ASP.NET Core használatával.

> [!div class="nextstepaction"]
> [Azure SignalR szolgáltatás ASP.NET Core-nal](./signalr-quickstart-dotnet-core.md)

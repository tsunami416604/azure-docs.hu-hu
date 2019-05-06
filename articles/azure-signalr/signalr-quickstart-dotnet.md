---
title: 'Gyors útmutató: Azure SignalR Service használata az ASP.NET-tel'
description: Gyors üzembe helyezés az Azure SignalR Service segítségével csevegőszoba létrehozása az ASP.NET keretrendszer használatával.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: cda1eddc5fa40e97038274cf9b4c3c2cb9552871
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65081065"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Gyors útmutató: Az ASP.NET és a SignalR Service csevegőszoba létrehozása

Az Azure SignalR Service alapján [SignalR az ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), amely **nem** 100 %-os kompatibilis az ASP.NET SignalR. Az Azure SignalR Service újra végrehajtani az ASP.NET SignalR data protokoll a legújabb ASP.NET Core technoledges alapján. Az Azure SignalR Service használata az ASP.NET SignalR, egyes ASP.NET SignalR-funkciók már nem támogatottak, például az Azure SignalR nem visszajátszani üzeneteket az ügyfelek csatlakozásakor. Emellett a végtelen keret átviteli és JSNOP nem érhetők támogatása. SignalR Service használata az ASP.NET SignalR alkalmazások néhány kódmódosítás és a függő kódtárak megfelelő verziója szükséges. 

Tekintse meg a [verzió különbségek doc](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) ASP.NET SignalR és az ASP.NET Core SignalR összehasonlítását funkció teljes listájának.

Ebből a gyorsútmutatóból megtudhatja, hogyan való ismerkedés az ASP.NET és az Azure SignalR Service egy hasonló [csevegőszoba alkalmazás](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*Kiszolgáló nélküli* mód nem támogatott az ASP.NET SignalR-alkalmazásokhoz. Mindig használjon *alapértelmezett* vagy *klasszikus* az Azure SignalR Service-példány.

A rövid útmutatóban használt Azure-erőforrások is létrehozhat [SignalR Service parancsfájl létrehozása](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Amíg a szolgáltatás üzembe helyezése folyamatban van, térjünk át a kóddal folytatott munkavégzésre. Klónozza a [mintaalkalmazást a GitHubról](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), állítsa be a SignalR Service kapcsolati sztringjét, és futtassa helyileg az alkalmazást.

1. Nyisson meg egy git terminálablakot. Váltson át arra a mappára, ahová a mintaprojektet klónozni szeretné.

1. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Konfigurálja és csevegőszoba webalkalmazás futtatása

1. Indítsa el a Visual Studiót, és nyissa meg a megoldás a *aspnet-samples/ChatRoom/* a klónozott adattár mappát.

1. A böngészőben, ahol az Azure portal megnyitása keresse meg és válassza ki az Ön által létrehozott példányhoz.

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

1. Most beállítjuk a kapcsolati karakterláncot a web.config fájlban.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. A *Startup.cs*, meghívása nélkül `MapSignalR()`, meg kell hívnia `MapAzureSignalR({your_applicationName})` és továbbíthatja azt a kapcsolati karakterláncot, amellyel az alkalmazás csatlakozni a szolgáltatáshoz, SignalR üzemeltető a saját maga helyett. Cserélje le `{YourApplicationName}` az alkalmazás nevére. Ez a név különbséget tenni a más alkalmazások ezt az alkalmazást egy egyedi nevet. Használhat `this.GetType().FullName` értékeként.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Akkor is hivatkoznia kell az SDK szolgáltatás API-k használata előtt. Nyissa meg a **eszközök |} NuGet-Csomagkezelő |} Package Manager Console** , és futtassa a parancsot:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Ezek módosításokon kívül minden más változatlan marad, továbbra is használhatja az Ön már ismeri az üzleti logika írását a hub felületet.

    > [!NOTE]
    > A végpont végrehajtása `/signalr/negotiate` Azure SignalR Service SDK-t az egyeztetési tesz elérhetővé. Az ügyfelek próbál csatlakozhat, és a kapcsolati karakterláncban meghatározott szolgáltatásvégpont irányítani az ügyfeleket speciális egyeztetés választ ad vissza.

1. Nyomja meg **F5** a projekt hibakeresési módban fusson. Láthatja, hogy az alkalmazás futása helyileg. Alkalmazás saját maga által egy SignalR-futtatókörnyezet üzemeltetési helyett mostantól csatlakozik az Azure SignalR Service.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
> 
> 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. Ebben a rövid útmutatóban a *SignalRTestResources* nevű erőforráscsoportot használtuk. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** elemre.

   
![Törlés](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban létrehozott egy új Azure SignalR Service erőforrás, és használta az ASP.NET-webalkalmazás. Ezután megtudhatja, hogyan fejleszthet ASP.NET Core az Azure SignalR Service segítségével valós idejű alkalmazások.

> [!div class="nextstepaction"]
> [Az Azure SignalR Service ASP.NET Core használatával](./signalr-quickstart-dotnet-core.md)

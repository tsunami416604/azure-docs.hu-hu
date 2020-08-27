---
title: Útmutató az Azure Signaler szolgáltatás használatának megismeréséhez
description: Rövid útmutató, amelyből megtudhatja, hogyan hozhat létre az Azure SignalR szolgáltatással csevegőszobát ASP.NET Core MVC alkalmazásokkal.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: c3a482dc1fa63b997b948cc3bc5cacefc9c2ad0a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928513"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Rövid útmutató: csevegési hely létrehozása a Signaler szolgáltatás használatával


Az Azure SignalR szolgáltatás egy olyan Azure-szolgáltatás, amely segítségével a fejlesztők könnyen hozhatnak létre webalkalmazásokat valós idejű funkciókkal. Ez a szolgáltatás a [ASP.NET Core 2,1-es jelzőn](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1)alapul, de támogatja [a ASP.net Core 3,0 jelzőjét](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0)is.

Ez a cikk segítséget nyújt az első lépések megtételében az Azure SignalR szolgáltatás használatakor. Ebben a rövid útmutatóban egy ASP.NET Core MVC-webalkalmazás használatával hoz létre csevegési alkalmazást. Az alkalmazás kapcsolatot létesít az Azure SignalR szolgáltatási erőforrással a valós idejű tartalomfrissítések engedélyezéséhez. A webalkalmazást helyileg fogja üzemeltetni, és több böngésző-ügyféllel is csatlakozhat. Minden ügyfél képes lesz tartalomfrissítéseket küldeni a többi ügyfélnek. 

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. Az egyik lehetőség a [Visual Studio Code](https://code.visualstudio.com/), amely a Windows, MacOS és Linux platformokon érhető el.

Az oktatóanyag kódja letölthető az [AzureSignalR-minták GitHub-adattárjából](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Emellett az ebben a rövid útmutatóban használt Azure-erőforrásokat is létrehozhatja a [Signal Service-parancsfájl létrehozása](scripts/signalr-cli-create-service.md)című lépéssel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [.net Core SDK](https://www.microsoft.com/net/download/windows).
* Töltse le vagy klónozott [AzureSignalR – minta GitHub-](https://github.com/aspnet/AzureSignalR-samples) tárházat. 

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR-erőforrás létrehozása

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Ebben a szakaszban a [.net Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) használatával hoz létre egy ASP.net Core MVC webalkalmazás-projektet. A a .NET Core parancssori felülete a Visual Studióban való használatának előnye, hogy a Windows, macOS és Linux platformokon is elérhető. 

1. Hozzon létre egy mappát a projekt számára. Ez a rövid útmutató a *E:\Testing\chattest* mappát használja.

2. Az új mappában futtassa a következő parancsot a projekt létrehozásához:

    ```dotnetcli
    dotnet new mvc
    ```


## <a name="add-secret-manager-to-the-project"></a>A Secret Manager hozzáadása a projekthez

Ebben a szakaszban a [Secret Manager eszközt](https://docs.microsoft.com/aspnet/core/security/app-secrets) adja hozzá a projekthez. A Secret Manager eszköz bizalmas adatokat tárol a projekt fáján kívüli fejlesztési munkához. Ez a módszer segít megelőzni az alkalmazás titkos kódjának véletlen megosztását a forráskódban.

1. Nyissa meg a *.csproj* fájlt. Adjon hozzá egy `DotNetCliToolReference` elemet a *Microsoft.Extensions.SecretManager.Tools* belefoglalásához. Vegyen fel egy `UserSecretsId` elemet is, ahogy az a következő kódban látható a *csevegő. csproj*, és mentse a fájlt.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Az Azure SignalR hozzáadása a webalkalmazáshoz

1. Adja hozzá a NuGet- `Microsoft.Azure.SignalR` csomagra mutató hivatkozást a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Futtassa a következő parancsot a projekt csomagjainak visszaállításához:

    ```dotnetcli
    dotnet restore
    ```

3. Adjon hozzá egy *Azure:SignalR:ConnectionString* nevű titkos kódot a Secret Managerhez. 

    Ez a titkos kód tartalmazza a SignalR szolgáltatási erőforrás elérésére szolgáló kapcsolati sztringet. *Azure: szignáló: a ConnectionString* az az alapértelmezett konfigurációs kulcs, amelyet a jelző a kapcsolat létesítéséhez keres. Cserélje le a következő parancs értékét a Signaling Service-erőforráshoz tartozó kapcsolatok karakterláncára.

    Ezt a parancsot a *. csproj* fájllal megegyező könyvtárba kell futtatnia.

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    A Secret Manager csak a webalkalmazás helyi tesztelésére szolgál. Egy későbbi oktatóanyagban üzembe helyezheti a csevegési webalkalmazást az Azure-ban. Miután telepítette a webalkalmazást az Azure-ba, a kapcsolati sztring a Secret Managerrel való tárolása helyett egy alkalmazás-beállítást fog használni.

    Ez a titok a konfigurációs API-val érhető el. Egy kettőspont (:) a konfiguráció neve a konfigurációs API-val minden támogatott platformon használható. Lásd: [konfiguráció környezet alapján](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Nyissa meg a *Startup.cs* fájlt, és frissítse a `ConfigureServices` metódust úgy, hogy a `services.AddSignalR().AddAzureSignalR()` metódus meghívásával használja az Azure SignalR szolgáltatást:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Ha nem küldi el a paramétert a rendszernek `AddAzureSignalR()` , ez a kód a jelző szolgáltatás erőforrás-kapcsolódási karakterláncának alapértelmezett konfigurációs kulcsát használja. Az alapértelmezett konfigurációs kulcs az *Azure: signaler: ConnectionString*.

5. A *Startup.cs*-ben is frissítse a `Configure` metódust úgy, hogy lecseréli a hívást a `app.UseStaticFiles()` következő kódra, és elmenti a fájlt a csak ASP.net Core 2 értékre.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    ASP.NET Core 3 + esetében cserélje le a fenti kódot a következőre:

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>Központosztály hozzáadása

A jelzőben a hub egy olyan alapvető összetevő, amely az ügyféltől hívható metódusok készletét teszi lehetővé. Ebben a szakaszban meghatároz egy központosztályt két metódussal: 

* `Broadcast`: Ez a metódus üzenetet küld az összes ügyfélnek.
* `Echo`: Ez a metódus visszaküld egy üzenetet a hívónak.

Mindkét módszer a `Clients` ASP.net Core a signaler SDK által biztosított felületet használja. Ez az interfész hozzáférést biztosít az összes csatlakoztatott ügyfélhez, így tartalmat küldhet az ügyfeleknek.

1. A projektkönyvtárban adjon hozzá egy új, *Hub* nevű mappát. Az új mappához adjon hozzá egy új, *Chat.cs* nevű központkódfájlt.

2. Adja hozzá a következő kódot a *chat.cs* a hub osztály definiálásához és a fájl mentéséhez. 

    Ha nem a *chattest* projektnevet használta, frissítse az osztály névterét.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Az ügyfél felületének hozzáadása a webalkalmazáshoz

A csevegő alkalmazás ügyfél-felhasználói felülete HTML és JavaScript formátumú lesz egy *index.html* nevű fájlban a *wwwroot* könyvtárban.

Másolja a *index.html* fájlt, a *CSS* mappát és a *Scripts* mappát a [Samples adattár](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) *wwwroot* mappájából. Illessze be őket a projekt *wwwroot* mappájába.

*index.html*fő kódja: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

A *index.html* -ben található kód az `HubConnectionBuilder.build()` Azure signaler-erőforráshoz való http-kapcsolódást kezdeményez.

Ha a kapcsolat sikeresen létrejött, át lesz adva a `bindConnectionMessage` számára, amely eseménykezelőket ad a bejövő tartalomhoz, amely le lesz küldve az ügyfélnek. 

A `HubConnection.start()` kommunikálni kezd a központtal. Ezután `onConnected()` hozzáadja a Button eseménykezelőket. Ezek az eseménykezelők a kapcsolat segítségével engedélyezik, hogy ez az ügyfél tartalomfrissítéseket küldjön le az összes csatlakozott ügyfélnek.

## <a name="add-a-development-runtime-profile"></a>Fejlesztési futtatási profil hozzáadása

Ebben a szakaszban a ASP.NET Core fejlesztési futtatókörnyezeti környezetét fogja hozzáadni. További információ: [több környezet használata ASP.net Coreban](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Hozzon létre egy *Tulajdonságok* nevű mappát a projektben.

2. Adjon hozzá egy *launchSettings.js* nevű új fájlt a mappához a következő tartalommal, és mentse a fájlt.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

    ```dotnetcli
    dotnet build
    ```

2. A létrehozás sikeres befejeződése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```dotnetcli
    dotnet run
    ```

    Az alkalmazás az 5000-as porton, a fejlesztői futtatókörnyezeti profilban konfigurált módon lesz helyileg üzemeltetve:

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Development
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.    
    ```

3. Nyisson meg két böngészőablakot. A böngészőben nyissa meg a következőt: `http://localhost:5000` . A rendszer kéri, hogy adja meg a nevét. Adja meg az ügyfél nevét mindkét ügyfél számára, és tesztelje az üzenetek tartalmát mindkét ügyfél között a **Küldés** gomb használatával.

    ![Példa Azure-beli szignáló csoport csevegésére](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbbra is a következő oktatóanyagot használja, megtarthatja az ebben a rövid útmutatóban létrehozott erőforrásokat, és újból felhasználhatja őket.

Ha elkészült a gyors üzembe helyezési minta alkalmazással, törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat a díjak elkerülése érdekében. 

> [!IMPORTANT]
> Egy erőforráscsoport törlése nem vonható vissza, és tartalmazza az adott csoport összes erőforrását. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha a minta tárolására szolgáló erőforrásokat olyan meglévő erőforráscsoporthoz hozta létre, amely a megőrizni kívánt erőforrásokat tartalmazza, akkor az erőforráscsoport törlése helyett az egyes erőforrásokat egyenként törölheti a paneljéről.
> 
> 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

A **szűrés név alapján** szövegmezőbe írja be az erőforráscsoport nevét. Ebben a rövid útmutatóban a *SignalRTestResources* nevű erőforráscsoportot használtuk. Az erőforráscsoport az eredmény listán válassza a három pontot (**..**.) > az **erőforráscsoport törlése**elemet.

   
![Erőforráscsoport törlésének kiválasztása](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


A rendszer az erőforráscsoport törlésének megerősítését kéri. Adja meg a megerősíteni kívánt erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.
   
A rendszer néhány pillanaton belül törli az erőforráscsoportot és annak erőforrásait.



## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új Azure Signal Service-erőforrást. Ezután egy ASP.NET Core webalkalmazással használta a tartalom frissítéseinek valós idejű leküldését több csatlakoztatott ügyfélre. Ha többet szeretne megtudni az Azure Signaler szolgáltatás használatáról, folytassa az oktatóanyagot, amely bemutatja a hitelesítést.

> [!div class="nextstepaction"]
> [Azure SignalR szolgáltatás – hitelesítés](./signalr-concept-authenticate-oauth.md)



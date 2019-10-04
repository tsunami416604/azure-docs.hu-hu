---
title: 'Gyors útmutató: Azure SignalR Service használata'
description: Rövid útmutató, amelyből megtudhatja, hogyan hozhat létre az Azure SignalR szolgáltatással csevegőszobát ASP.NET Core MVC alkalmazásokkal.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 3dc893ea10e47e867110f674a458498a6bd24a4f
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560713"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Gyors útmutató: Csevegőszoba létrehozása SignalR Service használatával


Az Azure SignalR szolgáltatás egy olyan Azure-szolgáltatás, amely segítségével a fejlesztők könnyen hozhatnak létre webalkalmazásokat valós idejű funkciókkal. Ez a szolgáltatás a [SignalR for ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction)-n alapul.

Ez a cikk segítséget nyújt az első lépések megtételében az Azure SignalR szolgáltatás használatakor. Ebben a rövid útmutatóban egy csevegőalkalmazás használatával egy ASP.NET Core MVC-webalkalmazást fog létrehozni. Az alkalmazás kapcsolatot létesít az Azure SignalR szolgáltatási erőforrással a valós idejű tartalomfrissítések engedélyezéséhez. A webalkalmazás helyi gazdagép fog, és több webböngésző-ügyfelek számára a csatlakozás. Minden ügyfél képes lesz tartalomfrissítéseket küldeni a többi ügyfélnek. 

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. Az egyik lehetőség van [Visual Studio Code](https://code.visualstudio.com/), amely az érhető el a Windows, macOS és Linux platformokon.

Az oktatóanyag kódja letölthető az [AzureSignalR-minták GitHub-adattárjából](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Emellett hozhat létre a következő ebben a rövid útmutatóban használt Azure-erőforrások [SignalR Service parancsfájl létrehozása](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [a .NET Core SDK](https://www.microsoft.com/net/download/windows).
* Töltse le vagy klónozza a [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub-adattárban. 

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR-erőforrás létrehozása

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Ebben a szakaszban használhatja a [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) létrehozni egy ASP.NET Core MVC webalkalmazás-projektet. A .NET Core parancssori felület a Visual Studio-val előnye, hogy legyen elérhető a Windows, macOS és Linux rendszerek között. 

1. Hozzon létre egy mappát a projekt számára. Ez a rövid útmutató használja a *E:\Testing\chattest* mappát.

2. Az új mappára futtassa a következő parancsot a projekt létrehozásához:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>A Secret Manager hozzáadása a projekthez

Ebben a szakaszban hozzá szeretné adni a [Secret Manager eszköz](https://docs.microsoft.com/aspnet/core/security/app-secrets) a projekthez. A titkos kód kezelő eszköz fejlesztési munkálatok során, a projekt fa kívüli bizalmas adatait tárolja. Ez a megközelítés segít az alkalmazás titkos kódok véletlen megosztását a forráskódban.

1. Nyissa meg a *.csproj* fájlt. Adjon hozzá egy `DotNetCliToolReference` elemet a *Microsoft.Extensions.SecretManager.Tools* belefoglalásához. Is hozzáadhat egy `UserSecretsId` elem a következő kódban látható módon *chattest.csproj*, és mentse a fájlt.

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

1. Vegyen fel egy hivatkozást a `Microsoft.Azure.SignalR` NuGet-csomagot a következő parancs futtatásával:

        dotnet add package Microsoft.Azure.SignalR

2. Futtassa a következő parancsot a projekt-csomagok visszaállítására szolgáló:

        dotnet restore

3. Adjon hozzá egy *Azure:SignalR:ConnectionString* nevű titkos kódot a Secret Managerhez. 

    Ez a titkos kód tartalmazza a SignalR szolgáltatási erőforrás elérésére szolgáló kapcsolati sztringet. *Azure: SignalR:ConnectionString* SignalR keres kapcsolatot létesíteni az alapértelmezett konfiguráció kulcsa. Az érték a következő parancsban cserélje le a kapcsolati karakterláncot a SignalR-szolgáltatási erőforrások.

    Ez a parancs ugyanabban a könyvtárban, futtatnia kell a *.csproj* fájlt.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    SECRET Manager csak tesztelésre a webalkalmazás közben helyileg vannak tárolva lesz. Az egy későbbi oktatóanyag teheti elérhetővé telepítésre a csevegési webalkalmazást az Azure-bA. A webalkalmazás üzembe helyezése az Azure-ba, után alkalmazás-beállítás helyett a kapcsolati karakterlánc Secret Manager fogja használni.

    A titkos kód konfigurációs API-val érhető el. Egy kettőspontot (:) a konfiguráció nevét, az összes támogatott platformon konfigurációs API-val működik. Lásd: [környezet konfigurációjának](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Nyissa meg a *Startup.cs* fájlt, és frissítse a `ConfigureServices` metódust úgy, hogy a `services.AddSignalR().AddAzureSignalR()` metódus meghívásával használja az Azure SignalR szolgáltatást:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    A paraméter nem átadásával `AddAzureSignalR()`, ezt a kódot használja az alapértelmezett konfigurációs kulcsot a SignalR Service erőforrás-kapcsolati karakterlánc. Az alapértelmezett konfigurációs kulcs *Azure: SignalR:ConnectionString*.

5. Még a *Startup.cs* fájlban módosítsa a `Configure` metódust az `app.UseStaticFiles()` meghívása helyett az alábbi kódra, majd mentse a fájlt.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            

### <a name="add-a-hub-class"></a>Központosztály hozzáadása

A SignalR a központ egy alapvető fontosságú, amely elérhetővé teszi, hogy az ügyfél nem hívható olyan készletéhez. Ebben a szakaszban meghatároz egy központosztályt két metódussal: 

* `Broadcast`: Ez a módszer küld el egy üzenetet minden ügyfélnek.
* `Echo`: Ez a módszer egy üzenetet küld vissza a hívónak.

A két módszert használja a `Clients` -felület, amely az ASP.NET Core SignalR SDK-t. Ez az interfész hozzáférést biztosít az összes csatlakoztatott ügyfelek, így a tartalom leküldése az ügyfelek számára.

1. A projektkönyvtárban adjon hozzá egy új, *Hub* nevű mappát. Az új mappához adjon hozzá egy új, *Chat.cs* nevű központkódfájlt.

2. Adja hozzá a következő kódot a *Chat.cs* definiálására a hub osztályt, és mentse a fájlt. 

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

### <a name="add-the-client-interface-for-the-web-app"></a>Az ügyféloldali felületen, a webalkalmazás hozzáadása

HTML és JavaScript nevű fájlba állnak, az ügyfél felhasználói felülete az csevegőszoba alkalmazás *index.html* a a *wwwroot* könyvtár.

Másolás a *index.html* fájlt, a *css* mappát, és a *parancsfájlok* mappájában található a *wwwroot* mappában található a [minták tárház](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Illessze be őket a projekt *wwwroot* mappát.

Íme a fő kódjának *index.html*: 

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

A kód *index.html* hívások `HubConnectionBuilder.build()` egy HTTP-kapcsolatot az Azure SignalR-erőforráshoz.

Ha a kapcsolat sikeresen létrejött, át lesz adva a `bindConnectionMessage` számára, amely eseménykezelőket ad a bejövő tartalomhoz, amely le lesz küldve az ügyfélnek. 

A `HubConnection.start()` kommunikálni kezd a központtal. Ezt követően `onConnected()` hozzáadja a gomb eseménykezelők. Ezek az eseménykezelők a kapcsolat segítségével engedélyezik, hogy ez az ügyfél tartalomfrissítéseket küldjön le az összes csatlakozott ügyfélnek.

## <a name="add-a-development-runtime-profile"></a>Fejlesztési futtatási profil hozzáadása

Ez a szakasz az ASP.NET Core egy fejlesztői futtatókörnyezet fogja hozzáadni. További információkért lásd: [használata az ASP.NET Core több környezetet](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Hozza létre a *tulajdonságok* a projektben.

2. Adjon hozzá egy új fájlt *launchSettings.json* a mappába, az alábbi tartalommal, és mentse a fájlt.

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


## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. Az alkalmazás létrehozása a .NET Core-CLI-vel, a következő parancsot az parancs-rendszerhéjba:

        dotnet build

2. A build sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

    Az alkalmazás fogja üzemeltetni helyi 5000-es, porton a fejlesztői futtatókörnyezet profilban konfigurált módon:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Nyissa meg a két böngészőablakot. Minden böngésző, lépjen a `http://localhost:5000`. A nevének megadását kéri. Ügyfél-és vizsgálati üzenet tartalma között mindkét ügyfelek használatával hogyan lehet továbbítani rá egy ügyfél nevét adja meg a **küldése** gombra.

    ![Az Azure SignalR csoport Csevegés – példa](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Ön továbbra is a következő oktatóanyaggal, tartani az ebben a rövid útmutatóban létrehozott erőforrásokat, és használja fel őket.

Ha elkészült, a rövid útmutató mintaalkalmazás, törölheti az Azure-díjak elkerülése érdekében ebben a rövid útmutatóban létrehozott erőforrásokat. 

> [!IMPORTANT]
> Egy erőforráscsoport törlése nem vonható vissza, és adott csoportban található összes erőforrást magában. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Létrehozta az erőforrásokat egy meglévő erőforráscsoportot, amely tartalmazza az erőforrások meg szeretné tartani a jelen minta üzemeltetését, törölheti az egyes erőforrások egyenként a panelen az erőforráscsoport törlése helyett.
> 
> 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

Az a **Szűrés név alapján** szöveg írja be az erőforráscsoport nevét. Ebben a rövid útmutatóban a *SignalRTestResources* nevű erőforráscsoportot használtuk. Az erőforráscsoport, a találatok listájában, válassza a három pontra (**...** ) > **Erőforráscsoport törlése**.

   
![Egy erőforráscsoport törlésére szolgáló kiválasztások](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


A rendszer az erőforráscsoport törlésének megerősítését kéri. Adja meg a nevét, erősítse meg, és válassza ki az erőforráscsoport **törlése**.
   
A rendszer néhány pillanaton belül törli az erőforráscsoportot és annak erőforrásait.



## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új Azure SignalR Service-erőforrást. Ezután használt, az ASP.NET Core-webalkalmazás tartalmi frissítések valós idejű leküldése több csatlakoztatott ügyfelet. Az Azure SignalR Service használatával kapcsolatos további információkért folytassa az oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Azure SignalR szolgáltatás – hitelesítés](./signalr-concept-authenticate-oauth.md)



---
title: Rövid útmutató az Azure SignalR-szolgáltatás használatának megismeréséhez
description: Rövid útmutató, amelyből megtudhatja, hogyan hozhat létre az Azure SignalR szolgáltatással csevegőszobát ASP.NET Core MVC alkalmazásokkal.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: f87625fe4f56b369f2bf4aade3ef5424084b6fe8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254886"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Rövid útmutató: Csevegőszoba létrehozása a SignalR szolgáltatás használatával


Az Azure SignalR szolgáltatás egy olyan Azure-szolgáltatás, amely segítségével a fejlesztők könnyen hozhatnak létre webalkalmazásokat valós idejű funkciókkal. Ez a szolgáltatás a [SignalR for ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1)-en alapul , de támogatja [a SignalR ASP.NET Core 3.0-t](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0)is .

Ez a cikk segítséget nyújt az első lépések megtételében az Azure SignalR szolgáltatás használatakor. Ebben a rövid útmutatóban egy csevegőalkalmazást hozhat létre egy ASP.NET Core MVC webalkalmazás használatával. Az alkalmazás kapcsolatot létesít az Azure SignalR szolgáltatási erőforrással a valós idejű tartalomfrissítések engedélyezéséhez. A webalkalmazást helyileg fogja üzemeltetni, és több böngészőügyféllel fog kapcsolatba lépni. Minden ügyfél képes lesz tartalomfrissítéseket küldeni a többi ügyfélnek. 

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. Az egyik lehetőség a [Visual Studio Code](https://code.visualstudio.com/), amely windowsos, macOS és Linux platformokon érhető el.

Az oktatóanyag kódja letölthető az [AzureSignalR-minták GitHub-adattárjából](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). A rövid útmutatóban használt Azure-erőforrásokat is létrehozhatja a [SignalR service parancsfájl létrehozása](scripts/signalr-cli-create-service.md)című webhelyen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [.NET Core SDK -t](https://www.microsoft.com/net/download/windows).
* Töltse le vagy klónozza az [AzureSignalR-minta](https://github.com/aspnet/AzureSignalR-samples) GitHub-tárházat. 

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR-erőforrás létrehozása

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Ebben a szakaszban a [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) segítségével hozhat létre egy ASP.NET Core MVC webalkalmazás-projektet. A .NET Core CLI használatának előnye a Visual Studio-val szemben, hogy windowsos, macOS és Linux platformokon is elérhető. 

1. Hozzon létre egy mappát a projekthez. Ez a rövid útmutató az *E:\Tesztelés\csevegés* mappát használja.

2. Az új mappában futtassa a következő parancsot a projekt létrehozásához:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>A Secret Manager hozzáadása a projekthez

Ebben a szakaszban a [Secret Manager eszközt](https://docs.microsoft.com/aspnet/core/security/app-secrets) adja hozzá a projekthez. A Secret Manager eszköz a projektfán kívül tárolja a fejlesztési munka bizalmas adatait. Ez a megközelítés segít megakadályozni az alkalmazástitkos kulcsok véletlen megosztását a forráskódban.

1. Nyissa meg a *.csproj* fájlt. Adjon hozzá egy `DotNetCliToolReference` elemet a *Microsoft.Extensions.SecretManager.Tools* belefoglalásához. Is hozzá `UserSecretsId` egy elemet, ahogy az a következő kódot *chattest.csproj*, és mentse a fájlt.

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

1. Adjon hozzá hivatkozást a `Microsoft.Azure.SignalR` NuGet csomaghoz a következő parancs futtatásával:

        dotnet add package Microsoft.Azure.SignalR

2. A következő parancs futtatásával állítsa vissza a projekt csomagjait:

        dotnet restore

3. Adjon hozzá egy *Azure:SignalR:ConnectionString* nevű titkos kódot a Secret Managerhez. 

    Ez a titkos kód tartalmazza a SignalR szolgáltatási erőforrás elérésére szolgáló kapcsolati sztringet. *Az Azure:SignalR:ConnectionString* az alapértelmezett konfigurációs kulcs, amelyet a SignalR a kapcsolat létrehozásához keres. Cserélje le a következő parancs értékét a SignalR Service erőforrás kapcsolati karakterláncára.

    Ezt a parancsot a *.csproj* fájllal azonos könyvtárban kell futtatnia.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Secret Manager csak a webalkalmazás tesztelésére, miközben a helyi legeltetésű. Egy későbbi oktatóanyagban a csevegőweb-alkalmazást az Azure-ba telepíti. Miután a webalkalmazás telepítve van az Azure-ban, egy alkalmazásbeállítást fog használni a kapcsolati karakterlánc secret managerrel való tárolása helyett.

    Ez a titkos kulcs érhető el a konfigurációs API-t. A kettőspont (:) A konfigurációs névben működik a konfigurációs API-val az összes támogatott platformon. Lásd: [Környezet szerint beállítás.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0) 


4. Nyissa meg a *Startup.cs* fájlt, és frissítse a `ConfigureServices` metódust úgy, hogy a `services.AddSignalR().AddAzureSignalR()` metódus meghívásával használja az Azure SignalR szolgáltatást:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Ha nem ad `AddAzureSignalR()`át paramétert a programnak, ez a kód a SignalR Service erőforrás-kapcsolati karakterláncalapértelmezett konfigurációs kulcsát használja. Az alapértelmezett konfigurációs kulcs az *Azure:SignalR:ConnectionString*.

5. Szintén *Startup.cs*, `Configure` frissítse a módszert `app.UseStaticFiles()` a hívás lecserélésével a következő kódra, és mentse a fájlt, csak ASP.NET Core 2 számára.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    A core 3+ ASP.NET esetében cserélje ki a fenti kódot a következőre:

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

A SignalR-ben a hub egy alapvető összetevő, amely az ügyféltől hívható metódusokat teszi elérhetővé. Ebben a szakaszban meghatároz egy központosztályt két metódussal: 

* `Broadcast`: Ez a metódus üzenetet küld az összes ügyfélnek.
* `Echo`: Ez a metódus visszaküld egy üzenetet a hívónak.

Mindkét módszer `Clients` a ASP.NET Core SignalR SDK által biztosított felületet használja. Ez a felület hozzáférést biztosít az összes csatlakoztatott ügyfélhez, így a tartalmat lelökheti az ügyfeleknek.

1. A projektkönyvtárban adjon hozzá egy új, *Hub* nevű mappát. Az új mappához adjon hozzá egy új, *Chat.cs* nevű központkódfájlt.

2. Adja hozzá a következő kódot *a Chat.cs* a központi osztály definiálásához és a fájl mentéséhez. 

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

### <a name="add-the-client-interface-for-the-web-app"></a>A webalkalmazás ügyfélfelületének hozzáadása

A csevegőszoba alkalmazás ügyfélfelhasználói felülete HTML- és JavaScript-kódból áll a *wwwroot* könyvtárban található *index.html* nevű fájlban.

Másolja az *index.html* fájlt, a *css* mappát és a *parancsfájlok mappát* a [mintatár](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) *wwwroot* mappájából. Illessze be őket a projekt *wwwroot* mappájába.

Itt a fő kódja *index.html:* 

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

A kód *index.html* hívások, `HubConnectionBuilder.build()` hogy egy HTTP-kapcsolat az Azure SignalR erőforrás.

Ha a kapcsolat sikeresen létrejött, át lesz adva a `bindConnectionMessage` számára, amely eseménykezelőket ad a bejövő tartalomhoz, amely le lesz küldve az ügyfélnek. 

A `HubConnection.start()` kommunikálni kezd a központtal. Ezután `onConnected()` hozzáadja a gomb eseménykezelőit. Ezek az eseménykezelők a kapcsolat segítségével engedélyezik, hogy ez az ügyfél tartalomfrissítéseket küldjön le az összes csatlakozott ügyfélnek.

## <a name="add-a-development-runtime-profile"></a>Fejlesztési futtatási profil hozzáadása

Ebben a szakaszban egy fejlesztői futásidejű környezetet adhat hozzá ASP.NET Core számára. További információ: [Több környezet ASP.NET Core környezetben című témakörben található.](https://docs.microsoft.com/aspnet/core/fundamentals/environments)

1. *Hozzon* létre egy Tulajdonságok nevű mappát a projektben.

2. Adjon hozzá egy *launchSettings.json* nevű új fájlt a mappához a következő tartalommal, és mentse a fájlt.

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

1. Ha az alkalmazást a .NET Core CLI segítségével szeretné felépíteni, futtassa a következő parancsot a parancshéjban:

        dotnet build

2. Miután a build sikeresen befejeződött, futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

    Az alkalmazás helyileg lesz tárolva az 5000-es porton, a fejlesztési futásidejű profilunkban konfigurálva:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Nyisson meg két böngészőablakot. Minden böngészőben nyissa `http://localhost:5000`meg a . A rendszer kéri, hogy adja meg a nevét. A **Küldés** gombbal adja meg az ügyfelek ügyfélnevét és tesztelje a két ügyfél közötti üzenettartalmat.

    ![Példa egy Azure SignalR csoportos csevegésre](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja a következő oktatóanyagot, megtarthatja az ebben a rövid útmutatóban létrehozott erőforrásokat, és újra felhasználhatja őket.

Ha befejezte a rövid útmutató mintaalkalmazás, törölheti az Azure-erőforrások ebben a rövid útmutatóban létrehozott a költségek elkerülése érdekében. 

> [!IMPORTANT]
> Az erőforráscsoport törlése visszafordíthatatlan, és a csoport összes erőforrását tartalmazza. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha a minta üzemeltetéséhez szükséges erőforrásokat egy megtartani kívánt erőforrásokat tartalmazó meglévő erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett egyenként törölheti az egyes erőforrásokat a panelből.
> 
> 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

A **Név szerint szűrés** mezőbe írja be az erőforráscsoport nevét. Ebben a rövid útmutatóban a *SignalRTestResources* nevű erőforráscsoportot használtuk. Az eredménylista erőforráscsoportjában válassza a három pontot (**...**) > **Az erőforráscsoport törlése**lehetőséget.

   
![Kijelölés erőforráscsoport törléséhez](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


A rendszer az erőforráscsoport törlésének megerősítését kéri. Írja be a megerősítendő erőforráscsoport nevét, és válassza a **Törlés gombot.**
   
A rendszer néhány pillanaton belül törli az erőforráscsoportot és annak erőforrásait.



## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új Azure SignalR Service-erőforrást. Ezután egy ASP.NET Core webalkalmazással valós időben leküldéses tartalomfrissítéseket több csatlakoztatott ügyfélnek. Ha többet szeretne megtudni az Azure SignalR-szolgáltatás használatáról, folytassa a hitelesítést bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [Azure SignalR szolgáltatás – hitelesítés](./signalr-concept-authenticate-oauth.md)



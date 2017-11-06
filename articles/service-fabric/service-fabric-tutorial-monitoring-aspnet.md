---
title: "Megfigyelési és diagnosztikai Azure Service Fabric az ASP.NET Core Services |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthat be figyelése és diagnosztika Azure Service Fabric ASP.NET Core alkalmazáshoz."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 68788efffd27edf2813cf455490b651c2c7106a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>A Service Fabric az ASP.NET Core alkalmazás figyelése és diagnosztizálása
Ez az oktatóanyag négy egy sorozat része. Mert végig kell vinnie a figyelés és az ASP.NET Core alkalmazás fut a Service Fabric-fürt használata az Application Insights diagnosztikai beállításának lépéseit. Az oktatóanyag első részét létre az alkalmazásból telemetriai adatokat gyűjtünk [létre olyan .NET Service Fabric-alkalmazás](service-fabric-tutorial-create-dotnet-app.md). 

Az oktatóanyag sorozat négy része a megismerheti, hogyan:
> [!div class="checklist"]
> * Konfigurálhatja az Application Insights az alkalmazáshoz
> * Gyűjthet válasz nyomkövetéshez HTTP-alapú kommunikációs szolgáltatások között
> * Az Application Insights App térkép szolgáltatás használata
> * Az Application Insights API-val egyéni események hozzáadása

Az oktatóanyag adatsorozat elsajátíthatja, hogyan:
> [!div class="checklist"]
> * [A .NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-create-dotnet-app.md)
> * [Telepítse központilag az alkalmazást egy távoli fürthöz](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Konfigurálja a CI/CD Visual Studio Team Services használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Figyelés és diagnosztika az alkalmazás beállítása

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Telepítse a Visual Studio 2017](https://www.visualstudio.com/) és telepítse a **Azure fejlesztési** és **ASP.NET és a webes fejlesztési** munkaterhelések.
- [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Töltse le a szavazási mintaalkalmazást
Ha Ön nem összeállítása a szavazási mintaalkalmazást [rész az oktatóanyag adatsorozat](service-fabric-tutorial-create-dotnet-app.md), tölthető le. Egy parancsablakban vagy a Terminálszolgáltatások a következő parancsot a helyi számítógépen, a minta app tárház klónozása.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Az Application Insights-erőforrás beállítása
Az Application Insights Azure alkalmazás teljesítmény felügyeleti platformot, és a Service Fabric ajánlott platform az alkalmazás figyelése és a diagnosztika. Az Application Insights-erőforrás létrehozásához keresse meg [Azure-portálon](https://portal.azure.com). Kattintson a **új** a bal oldali navigációs menü megnyitása Azure piactéren. Kattintson a **figyelési + felügyeleti** , majd **az Application Insights**.

![Hozzon létre új AI erőforrást](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Most töltse ki a szükséges információt a létrehozandó erőforrás kell. Adjon meg egy megfelelő *neve*, *erőforráscsoport*, és *előfizetés*. Állítsa be a *hely* számára, amelyikkel szeretné központilag telepíteni a Service Fabric-fürt a jövőben. Ebben az oktatóanyagban fogjuk üzembe helyezni az alkalmazást a helyi fürthöz, a *hely* mező nem számít. A *alkalmazástípus* kell hagyni "ASP.NET webalkalmazásként való kezelése." 

![AI erőforrás-jellemzők](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Ha a szükséges adatokat kitöltött, kattintson **létrehozása** az erőforrás - kiépítése körülbelül egy perce kell vennie. 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Az Application Insights hozzáadása az alkalmazás szolgáltatásokhoz

Indítsa el a Visual Studio 2017 emelt szintű jogosultságokkal. Ehhez kattintson a jobb gombbal a Visual Studio ikonra a Start menüben, majd válassza a **Futtatás rendszergazdaként**. Kattintson a **fájl** > **nyitott** > **projekt/megoldás** , és keresse meg az (akár egy oktatóanyag vagy git részében létrehozott Voting alkalmazás Klónozott). Nyissa meg *Voting.sln* , és ha a rendszer kéri az alkalmazás NuGet-csomagok visszaállítására, kattintson a **Igen**.

A lépések végrehajtásával konfigurálhatja az Application Insights VotingWeb és az VotingData szolgáltatáshoz:
1. Kattintson a jobb gombbal a szolgáltatás nevét, és kattintson a **konfigurálja az Application Insights...** .

    ![AI konfigurálása](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. Kattintson a **Start szabad**.
3. Jelentkezzen be a fiókba (amelyhez is meg az Azure-előfizetéshez), és válassza ki az előfizetést, amelyben létrehozta az Application Insights-erőforrást. Az erőforrás található *meglévő Application Insights-erőforrás* meg a "Resource" legördülő listában. Kattintson a **regisztrálása** Application Insights hozzáadása a szolgáltatás.

    ![AI regisztrálása](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Kattintson a **Befejezés** jelenik meg, a megjelenő párbeszédpanel a művelet befejeződése után.
    
Ügyeljen arra, hogy fenti lépések **mindkét** a szolgáltatások, az alkalmazás az Application Insights az alkalmazáshoz konfigurálásának befejezéséhez. Application Insights-erőforrások szolgál, mind a szolgáltatások láthatók a bejövő és kimenő kérések és a szolgáltatások közötti kommunikáció.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>A Microsoft.ApplicationInsights.ServiceFabric.Native NuGet hozzáadása a szolgáltatásokhoz

Az Application Insights rendelkezik, amely használható a helyzettől függően két adott Service Fabric-NuGets. A Service Fabric natív szolgáltatásokkal, és a másik pedig a tárolók és a Vendég végrehajtható fájlok használt egyik. Ebben az esetben fogjuk használni a Microsoft.ApplicationInsights.ServiceFabric.Native NuGet kihasználhatják a feltétellel, hogy mindez service-környezet. További tudnivalók az Application Insights SDK és a Service Fabric adott NuGets, lásd: [Microsoft Application Insights szolgáltatás a háló](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/develop/README.md). 

A NuGet beállítása lépései a következők:
1. Kattintson a jobb gombbal a **megoldás "Szavazás"** a Megoldáskezelőben, majd kattintson a lap tetején **NuGet-csomagok kezelése megoldáshoz...** .
2. Kattintson **Tallózás** "NuGet – megoldás" ablak, és ellenőrizze a felső navigációs menü a **közé tartoznak az előzetes** keresősáv melletti jelölőnégyzetbe.
3. Keresse meg `Microsoft.ApplicationInsights.ServiceFabric.Native` , majd kattintson a megfelelő NuGet-csomagot.
4. A jobb oldalon kattintson a két négyzet jelölését, a két szolgáltatásokat, az alkalmazásban a **VotingWeb** és **VotingData** kattintson **telepítése**.
    ![AI regisztráció kész](./media/service-fabric-tutorial-monitoring-aspnet/aisdk-sf-nuget.png)
5. Kattintson a **OK** a a *változások áttekintése* párbeszédpanel jelenik meg, és fogadja el a *licenc elfogadása*. Ezzel elvégezte a szolgáltatások a NuGet hozzáadását.
6. Most kell beállítania a telemetriai adatok inicializáló, a két szolgáltatást. Ehhez nyissa meg *VotingWeb.cs* és *VotingData.cs*. Mindkettő tegye a következő két lépést:
    1. Adja hozzá ezek két *használatával* utasításokat a felső részen az egyes  *\<ServiceName > .cs*:
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. A beágyazott a *vissza* rendszerállapot-kimutatását *CreateServiceInstanceListeners()* vagy *CreateServiceReplicaListeners()*a *ConfigureServices*  >  *szolgáltatások*, a kettő között a két deklarálva, Egypéldányos szolgáltatások hozzáadása: `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`.
    Ez hozzáadja a *szolgáltatási környezet* , hogy a telemetriai adatokat, így jobban megismerheti az Application Insights a telemetriai forrását. A beágyazott *vissza* utasítás *VotingWeb.cs* kell kinéznie:
    
    ```csharp
    return new WebHostBuilder().UseWebListener()
        .ConfigureServices(
            services => services
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<HttpClient>())
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Hasonlóképpen, a *VotingData.cs*, rendelkeznie kell:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<IReliableStateManager>(this.StateManager))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Ekkor készen áll az alkalmazás telepítéséhez. Kattintson a **Start** felső (vagy **F5**), a Visual Studio elkészíti és az alkalmazás becsomagolása, állítsa be a helyi fürthöz, és azt az alkalmazást telepíti. 

Miután befejezte az alkalmazás központi telepítését, keresztül történő központi [localhost:8080](localhost:8080), ahol Ön képesnek kell lennie a szavazás mintaalkalmazás egyetlen lap megjelenítéséhez. A néhány eltérő konfigurációs elemeket az Ön által választott létrehozásához néhány példa adatok és a telemetria - I került a desszertek szavazati!

![AI minta szavazatok](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Nyugodtan *eltávolítása* is, ha végzett a szavazó beállítások némelyike néhány szavazat hozzáadásával.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Nézet telemetriai adatok és az Application Insights App térképre 

HEAD keresztül való az Application Insights-erőforrást az Azure portálon, és az erőforrás, a bal oldali navigációs sávon kattintson a **előzetes** alatt *konfigurálása*. Kapcsolja be **a** a *alkalmazás több szerepkör-hozzárendelés* érhető el az előzetes verziójú funkciók közül.

![AI engedélyezése AppMap](./media/service-fabric-tutorial-monitoring-aspnet/ai-appmap-enable.png)

Kattintson a **áttekintése** térhet vissza az erőforrás kezdőlapja. Kattintson a **keresési** felső várható nyomkövetés megjelenítéséhez. Nyomkövetések az Application Insights megjelenő néhány percet vesz igénybe. Abban az esetben, hogy nem jelennek meg, várjon egy percet, és elérte az **frissítése** gombra az oldal tetején.
![AI lásd a nyomkövetési adatokat](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

A lefelé görgetési a *keresési* ablakban jelennek meg a bejövő telemetriai out-of-az-box kap az Application insights szolgáltatással. Minden egyes művelethez, amely az Voting alkalmazásba megtette, nem kell egy kimenő PUT-kérelmet a *VotingWeb* (PUT szavazatok/Put [name]), egy bejövő PUT-kérelmet a *VotingData* (PUT VoteData/Put [neve]), a GET kérelmek jeleníti meg az Adatfrissítés két követ. Is lesz egy függőségi nyomkövetési HTTP – localhost, hiszen ezek HTTP-kérelmek. Ez szerepel-e meg a hogyan egy szavazattal példát: ![AI minta kérelem-nyomkövetés](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

Kattintson az egyik nyomkövetés részletes adatainak megtekintéséhez. Az Application Insights, beleértve a által biztosított kérelmekre vonatkozó hasznos információ a *válaszidő* és a *kérelem URL-címe*. Emellett a Service Fabric adott NuGet adott hozzá, mivel is elérhetővé válik adatokat, a Service Fabric-fürt környezetében az alkalmazással kapcsolatos a *egyéni adatok* az alábbi szakasz. Ez magában foglalja a szolgáltatási környezet, így a *PartitionID* és *Replikaazonosítójú* a forrás, a kérést, és jobb localize problémák diagnosztizálása az alkalmazásban szereplő hibák során.

![AI nyomkövetési részletei](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Emellett azóta jelenleg engedélyezve App leképezést a *áttekintése* lap, kattintson a a **App térkép** ikonja megjelenik a mindkét szolgáltatás csatlakoztatva.

![AI nyomkövetési részletei](./media/service-fabric-tutorial-monitoring-aspnet/app-map.png)

Az alkalmazás térkép azt segítenek megérteni az alkalmazás topológiát, különösen, mert több különböző szolgáltatások, amelyek együttműködése hozzáadása indítása. Emellett lehetővé teszi az alapszintű adatok a kérés sikerességéről, és segít megérteni, ahol dolog lehet, hogy elvégezték az helytelen sikertelen kérelmek diagnosztizálására. Az alkalmazás térkép használatával kapcsolatos további tudnivalókért lásd: [Application Insights az alkalmazás-hozzárendelés](../application-insights/app-insights-app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Egyéni instrumentation hozzá az alkalmazáshoz

Bár az Application Insights telemetria a kezdő verzióról számos, érdemes lehet további egyéni instrumentation hozzáadása. Ez alapján az üzleti igényeknek megfelelően, vagy a diagnosztika javítására problémák esetén az alkalmazás lehet. Az Application Insights API használatával viszi be egyéni eseményeket és metrikákat, amelyek olvashat további tudnivalókat tartalmaz [Itt](../application-insights/app-insights-api-custom-events-metrics.md).

Adjunk néhány egyéni események *VoteDataController.cs* (alatt *VotingData* > *tartományvezérlők*) nyomon követésére, amikor folyamatban van a szavazatok hozzáadni és törölni a az alapul szolgáló *votesDictionary*. 
1. Adja hozzá `using Microsoft.ApplicationInsights;` végén található egyéb utasítások segítségével.
2. Egy új deklarálható *TelemetryClient* elején. az osztály a létrehozását a *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. Az a *Put()* működik, vegye fel olyan esemény, amely megerősíti, hogy a szavazás hozzá lett adva. Adja hozzá `telemetry.TrackEvent($"Added a vote for {name}");` után a tranzakció befejeződött, a jobb gombbal a visszatérés *OkResult* utasítást.
4. A *Delete()*, van egy "if/else" alapján feltéve hogy a *votesDictionary* egy adott szavazó lehetőséget a szavazatok tartalmazza. 
    1. Adja hozzá egy eseményt, amely megerősíti, hogy a szavazás törlését a *Ha* utasítás, utána a *await tx.CommitAsync()*:`telemetry.TrackEvent($"Deleted votes for {name}");`
    2. A megjelenítése, hogy a törlés elmaradt esemény hozzáadása a *más* utasítás return utasítás előtt:`telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Íme egy példa a határozzák meg a *Put()* és *Delete()* funkciók hozzáadása az események után nézhet ki:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Miután végzett a fenti módosítások végrehajtása **Start** az alkalmazást úgy, hogy az informatikai hoz létre, és telepíti a legújabb verzióját. Ha végzett az alkalmazás központi telepítését, központi keresztül történő [localhost:8080](localhost:8080), és adja hozzá, és törölje az egyes szavazó beállítások. Ezután térjen vissza az Application Insights-erőforrás (az előtt, a nyomkövetési adatokat 1-2 perc alatt jelenik meg az Application Insights vehet igénybe) a legutóbbi futtatás a nyomkövetés megjelenítéséhez. Az összes szavazatok hozzáadni és törölni meg kell jelennie egy "Egyéni esemény * és a válasz telemetriai adatokat. 

![egyéni események](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Konfigurálhatja az Application Insights az alkalmazáshoz
> * Gyűjthet válasz nyomkövetéshez HTTP-alapú kommunikációs szolgáltatások között
> * Az Application Insights App térkép szolgáltatás használata
> * Az Application Insights API-val egyéni események hozzáadása

Most, hogy az ASP.NET alkalmazás figyelését és diagnosztikai beállításának befejeződött, próbálja meg a következőket:
- [Figyelés és diagnosztika a Service Fabric felfedezése](service-fabric-diagnostics-overview.md)
- [Az Application insights szolgáltatással a Service Fabric-esemény elemzése](service-fabric-diagnostics-event-analysis-appinsights.md)
- Application insights szolgáltatással kapcsolatos további információkért lásd: [Application Insights-dokumentáció](https://docs.microsoft.com/en-us/azure/application-insights/)

---
title: Az ASP.NET Core szolgáltatások monitorozása és diagnosztikája az Azure Service Fabricben | Microsoft Docs
description: Ez az oktatóanyag ismerteti, hogyan állíthat be monitorozást és diagnosztikát az Azure Service Fabric ASP.NET Core-alkalmazásokhoz.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: febeb2b7e6ada69db78cb0553b4fa90874f5f2eb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>Oktatóanyag: ASP.NET Core-alkalmazás monitorozása és diagnosztizálása a Service Fabric szolgáltatásban
Ez az oktatóanyag egy sorozat negyedik része. A Service Fabric-fürtön futó ASP.NET Core alkalmazás Application Insights használatával való monitorozása és diagnosztizálása beállításának lépéseit írja le. Telemetriát gyűjtünk az oktatóanyag [.NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-create-dotnet-app.md) című első részében kifejlesztett alkalmazásból. 

Az oktatóanyag-sorozat negyedik részében az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az Application Insights konfigurálása az alkalmazáshoz
> * Választelemetria begyűjtése a szolgáltatások közötti HTTP-alapú kommunikáció követéséhez
> * Az Alkalmazástérkép használata az Application Insightsban
> * Egyéni események hozzáadása az Application Insights API-val

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [.NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-create-dotnet-app.md)
> * [Az alkalmazás üzembe helyezése egy távoli fürtön](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [A CI/CD konfigurálása a Visual Studio Team Services használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Monitorozás és diagnosztika beállítása az alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdése előtt:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Telepítse a Visual Studio 2017-et](https://www.visualstudio.com/) az **Azure-fejlesztési**, valamint az **ASP.NET- és webfejlesztési** számítási feladatokkal.
- [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése
Ha nem hozta létre a mintául szolgáló szavazóalkalmazást [az oktatóanyag-sorozat első részében](service-fabric-tutorial-create-dotnet-app.md), akkor le is töltheti. Egy parancssori ablakban vagy terminálon futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Application Insights-erőforrás beállítása
Az Application Insights az Azure alkalmazásteljesítmény-felügyeleti platformja és a Service Fabric által az alkalmazások monitorozásához és diagnosztizálásához javasolt platform. Application Insights-erőforrás létrehozásához lépjen az [Azure Portalra](https://portal.azure.com). Kattintson a bal oldali navigációs menüben az **Erőforrás létrehozása** gombra az Azure Marketplace megnyitásához. Kattintson a **Monitorozás és kezelés** lehetőségre, majd az **Application Insights** lehetőségre.

![Új AI-erőforrás létrehozása](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Most ki kell töltenie a létrehozni kívánt erőforrás attribútumaival kapcsolatos kötelező információkat. Adjon meg egy megfelelő *nevet*, *erőforráscsoportot* és *előfizetést*. Állítsa be a *helyet*, ahová a jövőben üzembe szeretné helyezni a Service Fabric-fürtöt. Ebben az oktatóanyagban egy helyi fürtre helyezzük üzembe az alkalmazást, ezért a *Hely* mező irreleváns. Az *Alkalmazás típusa* mezőben meg kell hagyni az „ASP.NET-es webalkalmazás” értéket. 

![AI-erőforrás attribútumai](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Miután kitöltötte a kötelező információkat, kattintson a **Létrehozás** gombra az erőforrás kiépítéséhez – ez körülbelül egy percet vesz igénybe. 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Az Application Insights hozzáadása az alkalmazás szolgáltatásaihoz

Indítsa el a Visual Studio 2017 programot emelt szintű jogosultságokkal. Ehhez kattintson a jobb gombbal a Start menüben a Visual Studio ikonra, majd válassza a **Futtatás rendszergazdaként** lehetőséget. Kattintson a **Fájl** > **Megnyitás** > **Projekt/Megoldás** lehetőségre, és keresse meg a szavazóalkalmazást (az oktatóanyag első részében vagy Git-klónozással jött létre). Nyissa meg a *Voting.sln* fájlt, és ha a rendszer bekéri az alkalmazás NuGet-csomagjainak visszaállítását, kattintson az **Igen** gombra.

Kövesse az alábbi lépéseket az Application Insights konfigurálásához a VotingWeb és a VotingData szolgáltatásokhoz:
1. Kattintson a jobb gombbal a szolgáltatás nevére, és kattintson **Az Application Insights konfigurálása...** lehetőségre.

    ![Az AI konfigurálása](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. Kattintson az **Ingyenes próba megkezdése** gombra.
3. Jelentkezzen be a fiókjába (amellyel az Azure-előfizetést is beállította), és válassza ki azt az előfizetést, amelyben létrehozta az Application Insights-erőforrást. Keresse meg az erőforrást az „Erőforrás” legördülő lista *Meglévő Application Insights-erőforrás* eleme alatt. Kattintson a **Regisztrálás** gombra, hogy az Application Insightsot a szolgáltatáshoz adja.

    ![Az AI regisztrálása](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Amikor a felugró párbeszédpanel befejezi a műveletet, kattintson a **Befejezés** gombra.
    
Mindenképpen végezze el a fenti lépéseket az alkalmazásban lévő **mindkét** szolgáltatáshoz az Application Insights alkalmazáshoz végzett konfigurálásának befejezéséhez. A rendszer ugyanazt az Application Insights-erőforrást használja mindkét szolgáltatáshoz, hogy lássa a bejövő és a kimenő kéréseket, és kommunikáljon a szolgáltatások között.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>A Microsoft.ApplicationInsights.ServiceFabric.Native NuGet hozzáadása a szolgáltatásokhoz

Az Application Insights két Service Fabric NuGettel rendelkezik, amelyek a forgatókönyvtől függően használhatók. Az egyik a Service Fabric natív szolgáltatásaival, a másik a tárolókkal és vendégalkalmazásokkal használható. Ebben az esetben a Microsoft.ApplicationInsights.ServiceFabric.Native NuGetet használjuk az általa biztosított szolgáltatáskörnyezet megértése érdekében. Az Application Insights SDK-ról és a Service Fabric NuGetekről további információért lásd: [Microsoft Application Insights a Service Fabrichez](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md). 

A következő lépéseket kell végrehajtani a NuGet beállításához:
1. Kattintson a jobb gombbal a Megoldáskezelő tetején lévő **Solution 'Voting'** (Megoldásszavazás) elemre, majd kattintson a **Manage NuGet Packages for Solution...** (NuGet-csomagok kezelése a megoldáshoz) elemre.
2. Kattintson a „NuGet - Solution” (NuGet – Megoldás) ablak felső navigációs menüjében lévő **Browse** (Tallózás) gombra, és jelölje be a keresősáv melletti **Include prerelease** (Előzetes verzió is) jelölőnégyzetet.
3. Keresse meg a `Microsoft.ApplicationInsights.ServiceFabric.Native` elemet, és kattintson a megfelelő NuGet-csomagra.

>[!NOTE]
>Lehet, hogy hasonlóképpen telepítenie kell a Microsoft.ServiceFabric.Diagnostics.Internal csomagot, ha az nincs előre telepítve az Application Insights-csomag telepítése előtt.

4. A jobb oldalon kattintson az alkalmazásban lévő két szolgáltatás (a **VotingWeb** és a **VotingData**) melletti jelölőnégyzetekre, és kattintson az **Install** (Telepítés) gombra.
    ![AI sdk Nuget](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. Kattintson az **OK** gombra a felugró *Review Changes* (Módosítások áttekintése) párbeszédpanelen, és fogadja el a *License Acceptance* (Licenc elfogadása) lehetőséget. Ezzel a szolgáltatásokhoz adja a NuGetet.
6. Most be kell állítania a két szolgáltatásban a telemetriainicializálót. Ehhez nyissa meg a *VotingWeb.cs* és a *VotingData.cs* fájlokat. Mindkettőhöz végezze el a következő lépéseket:
    1. Adja hozzá ezt a két *using* utasítást mindegyik *\<ServiceName>.cs* tetején:
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. A *CreateServiceInstanceListeners()* vagy a *CreateServiceReplicaListeners()* beágyazott *return* utasításában, a *ConfigureServices* > *services* alatt a két meghatározott Singleton szolgáltatás közé adja hozzá a következőt: `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`.
    Ez hozzáadja a *szolgáltatáskörnyezetet* a telemetriához, így jobban megértheti a telemetria forrását az Application Insightsban. A *VotingWeb.cs* fájlban lévő beágyazott *return* utasításnak így kell kinéznie:
    
    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Ugyanígy, a *VotingData.cs* fájlban a következőknek kell lennie:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Ellenőrizze, hogy a `UseApplicationInsights()` metódus mindkét fájlban a fenti módon meg legyen hívva. 

>[!NOTE]
>Ez a mintaalkalmazás a http-t használja a szolgáltatások kommunikációjához. Ha a szolgáltatás távelérésének 2-es verziója használatával fejleszt ki egy alkalmazást, a következő kódsorokat is hozzá kell adnia a fentiekkel megegyező helyen.

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

Ekkor készen áll az alkalmazás üzembe helyezésére. Kattintson a felső **Start** gombra (vagy nyomja le az **F5** billentyűt), és a Visual Studio felépíti és becsomagolja az alkalmazást, beállítja a helyi fürtöt, és üzembe helyezi azon az alkalmazást. 

Az alkalmazás üzembe helyezésének befejezése után lépjen a [localhost:8080](localhost:8080) portra, ahol a Voting Sample egyoldalas alkalmazást kell látnia. Szavazzon néhány tetszőleges elemre, hogy mintaadatokat és telemetriát hozzon létre – én az édességeket választottam!

![AI mintaszavazatok](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Nyugodtan *el is távolíthat* néhány szavazati lehetőséget, miután hozzáadott néhány szavazatot.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Telemetria és az Alkalmazástérkép megtekintése az Application Insightsban 

Lépjen az Application Insights-erőforrásra az Azure Portalon.

Kattintson az **Overview** (Áttekintés) lehetőségre, hogy visszatérjen az erőforrás kezdőlapjára. Kattintson a felül lévő **Search** (Keresés) gombra a bejövő nyomok megtekintéséhez. Néhány percig tart, amíg a nyomok megjelennek az Application Insightsban. Ha nem lát nyomokat, várjon egy percet, és kattintson a felül lévő **Refresh** (Frissítés) gombra.
![AI nyomok megtekintése](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Ha lefelé görget a *Search* (Keresés) ablakban, láthatja az Application Insightsban a vásárláskor szereplő összes bejövő telemetriát. A szavazóalkalmazásban végzett összes művelethez szerepelnie kell egy kimenő PUT kérésnek a *VotingWeb* (PUT Votes/Put [név]) szolgáltatásból, egy bejövő PUT kérésnek a *VotingData* (PUT VoteData/Put [név]) alkalmazásból, amelyeket a GET kéréspár követ a megjelenített adatok frissítéséhez. A helyi gazdagépen egy HTTP-függőségnyom is szerepel, mert ezek HTTP-kérések. Az alábbi példa bemutatja, mit lát egy szavazat hozzáadásakor: ![AI mintakérés nyom](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

A nyomokra kattintva további részleteket tudhat meg azokról. Hasznos adatokat talál az Application Insights által nyújtott kéréssel kapcsolatban, beleértve a *Válaszidőt* és a *Kérés URL-címét*. Ezenkívül mivel hozzáadta a Service Fabric NuGetet, a Service Fabric-fürt környezetéből is megkapja az alkalmazással kapcsolatos adatokat az alul lévő *Custom Data* (Egyéni adatok) szakaszban. Ebbe beletartozik a szolgáltatáskörnyezet, így láthatja a kérés forrásának *PartitionID* és *ReplicaId* azonosítóit, és jobban honosíthatja a problémákat az alkalmazásban lévő hibák diagnosztizálásakor.

![AI nyomrészletek](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Ezenkívül az Áttekintés oldal bal oldali menüjében lévő *Alkalmazástérképre* vagy az **Alkalmazástérkép** ikonra kattintva a két csatlakoztatott szolgáltatást megjelenítő alkalmazástérképre léphet.

![AI nyomrészletek](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

Az Alkalmazástérkép segíthet az alkalmazástopológia jobb megértésében, különösen, amikor több együttműködő szolgáltatást kezd hozzáadni. A kérések sikerességi arányáról is alapvető adatokat nyújt, és segíthet a sikertelen kérések diagnosztizálásában, hogy jobban megértse, mi volt hiba. Az Alkalmazástérkép használatáról további információért lásd: [Alkalmazástérkép az Application Insightsban](../application-insights/app-insights-app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Egyéni kialakítás hozzáadása az alkalmazáshoz

Bár az Application Insights már a megvásárláskor számos telemetriát nyújt, érdemes lehet további egyéni kialakításokat hozzáadni. Ezek az üzleti igényeken alapulhatnak, vagy szolgálhatják a diagnosztika javítását, amikor hiba történik az alkalmazásban. Az Application Insights az egyéni eseményeket és mérőszámokat feldolgozó API-val rendelkezik, amelyről [itt](../application-insights/app-insights-api-custom-events-metrics.md) olvashat bővebben.

Adjunk néhány egyéni eseményt a *VoteDataController.cs* fájlhoz (a *VotingData* > *Controllers* (Vezérlők) területen) annak követéséhez, hogy mikor kerülnek szavazatok a mögöttes *votesDictionary* fájlba, és mikor vannak törölve onnan. 
1. Adja a következő elemet a másik végére utasításokkal: `using Microsoft.ApplicationInsights;`.
2. Az osztály elején deklaráljon egy új *TelemetryClient* elemet az *IReliableStateManager* létrehozása alatt: `private TelemetryClient telemetry = new TelemetryClient();`.
3. A *Put()* függvényben adjon hozzá egy új eseményt, amely megerősíti, hogy szavazat lett hozzáadva. Adja hozzá a `telemetry.TrackEvent($"Added a vote for {name}");` elemet a tranzakció befejezése után, közvetlenül a visszatérési *OkResult* utasítás elé.
4. A *Delete()* függvényben található egy „if/else” elem, amely azon alapul, hogy a *votesDictionary* tartalmaz-e szavazatokat egy adott választási lehetőséghez. 
    1. Adjon hozzá egy szavazat törlését megerősítő eseményt az *if* utasításhoz az *await tx.CommitAsync()* után: `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Az *else* utasításhoz adjon hozzá egy eseményt annak jelzéséhez, hogy a törlés nem történt meg, a visszatérési utasítás elé: `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Az események hozzáadásakor a *Put()* és a *Delete()* függvények olyanok lesznek, ahogy az alábbi példában látható:

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

Miután végzett ezekkel a módosításokkal, **indítsa el** az alkalmazást a legújabb verzió felépítéséhez és üzembe helyezéséhez. Az alkalmazás üzembe helyezésének befejezése után lépjen a [localhost:8080](localhost:8080) portra, és adjon hozzá és töröljön néhány szavazati lehetőséget. Ezután térjen vissza az Application Insights erőforrásra a legutóbbi futtatás nyomainak megtekintéséhez (a korábbiakhoz hasonlóan a nyomok megjelenítése 1-2 percbe telhet az Application Insightsban). Most minden hozzáadott és törölt szavazathoz egy „Egyéni eseményt” kell látnia az összes választelemetriával együtt. 

![egyéni események](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Az Application Insights konfigurálása az alkalmazáshoz
> * Választelemetria begyűjtése a szolgáltatások közötti HTTP-alapú kommunikáció követéséhez
> * Az Alkalmazástérkép használata az Application Insightsban
> * Egyéni események hozzáadása az Application Insights API-val

Most, hogy elvégezte az ASP.NET alkalmazás monitorozása és diagnosztizálása beállítását, megpróbálkozhat a következőkkel:
- [A monitorozás és diagnosztika részletesebb megismerése a Service Fabricben](service-fabric-diagnostics-overview.md)
- [Service Fabric eseményelemzés az Application Insights szolgáltatással](service-fabric-diagnostics-event-analysis-appinsights.md)
- Az Application Insightsról további információt az [Application Insights dokumentációban](https://docs.microsoft.com/azure/application-insights/) talál

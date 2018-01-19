---
title: ".NET-alkalmazás létrehozása a Service Fabric |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy alkalmazást az ASP.NET Core előtér- és egy megbízható szolgáltatás állapot-nyilvántartó háttér-alkalmazás és központi telepítését a fürthöz."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: f4b3c766ee46233cd4ec2d195e39d0b68516952f
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Hozzon létre és telepítsen egy alkalmazást az ASP.NET Core Web API előtér- és egy állapotalapú háttér-szolgáltatás
Ez az oktatóanyag egy sorozat része.  Megtudhatja, hogyan egy Azure Service Fabric-alkalmazás létrehozása az ASP.NET Core Web API előtér és állapot-nyilvántartó háttér-szolgáltatás az adatok tárolásához. Az útmutató elvégzése után rendelkezni fog egy ASP.NET Core webes kezelőfelületes szavazóalkalmazással, amely egy, a fürtben található állapotalapú háttérszolgáltatásba menti a szavazati adatokat. Ha nem szeretné manuálisan létrehozni a szavazóalkalmazást, akkor [töltse le a forráskód](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) a kész alkalmazás, és ugorjon előre [végezze el a szavazó mintaalkalmazás](#walkthrough_anchor).

![Alkalmazásdiagram](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

A rész az adatsorozatok megismerheti, hogyan:

> [!div class="checklist"]
> * Állapot-nyilvántartó megbízható szolgáltatásként az ASP.NET Core webes API-szolgáltatás létrehozása
> * Állapot nélküli webszolgáltatásként ASP.NET Core webalkalmazás-szolgáltatás létrehozása
> * A fordított proxy segítségével kommunikál az állapotalapú szolgáltatással

Az oktatóanyag adatsorozat elsajátíthatja, hogyan:
> [!div class="checklist"]
> * A .NET Service Fabric-alkalmazás létrehozása
> * [Telepítse központilag az alkalmazást egy távoli fürthöz](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Konfigurálja a CI/CD Visual Studio Team Services használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Figyelés és diagnosztika az alkalmazás beállítása](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Telepítse a Visual Studio 2017](https://www.visualstudio.com/) 15.3 vagy újabb verziója a **Azure fejlesztési** és **ASP.NET és a webes fejlesztési** munkaterhelések.
- [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Megbízható szolgáltatásként ASP.NET Web API-szolgáltatás létrehozása
Először hozza létre a webes előtér-, az ASP.NET Core segítségével szavazóalkalmazást. Az ASP.NET Core egy egyszerűsített, platformfüggetlen webes fejlesztési keretrendszer, amely a webes API-k és modern webes felhasználói felület létrehozása segítségével. Ahhoz, hogy a teljes ismertetése, hogy az ASP.NET Core hogyan integrálható a Service Fabric, mindenképpen olvassa a [ASP.NET Core a Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) cikk. Most hajtsa végre ezt az oktatóanyagot gyorsan. Az ASP.NET Core kapcsolatos további tudnivalókért tekintse meg a [ASP.NET Core dokumentációja](https://docs.microsoft.com/aspnet/core/).

1. Indítsa el a Visual Studiót **rendszergazdaként**.

2. A projekt létrehozása **fájl**->**új**->**projekt**

3. Az **Új projekt** párbeszédpanelen válassza a **Felhő > Service Fabric-alkalmazás** elemet.

4. Adjon nevet az alkalmazásnak **Voting** nyomja le az ENTER **OK**.

   ![A Visual Studio Új projekt párbeszédpanelje](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Az a **új Service Fabric-szolgáltatás** lapon, válassza ki **állapotmentes ASP.NET Core**, és a szolgáltatás **VotingWeb**.
   
   ![ASP.NET webszolgáltatás kiválasztása az új service párbeszédpanelen](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. A következő oldalon biztosít az ASP.NET Core projektsablonjai. A jelen oktatóanyag esetében válassza ki a **(Model-View-Controller) webalkalmazás**. 
   
   ![ASP.NET-projekt típusának kiválasztása](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio létrehoz egy alkalmazás és szolgáltatás projekt, és megjeleníti őket a Megoldáskezelőben.

   ![A megoldáskezelő ASP.NET alapvető szolgáltatás webes API-alkalmazás létrehozása]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>A VotingWeb szolgáltatás AngularJS hozzáadása
Adja hozzá [AngularJS](http://angularjs.org/) a szolgáltatás használatával [Bower támogatási](/aspnet/core/client-side/bower). Először adja hozzá a projekthez Bower konfigurációs fájlt.  A Megoldáskezelőben kattintson a jobb gombbal a **VotingWeb** válassza **Hozzáadás -> Új elem**. Válassza ki **webes** , majd **Bower konfigurációs fájl**.  A *bower.json* fájl jön létre.

Nyissa meg *bower.json* szögben kifejezett és szögben kifejezett rendszerindítási bejegyzés hozzáadása, majd mentse a módosításokat.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "3.2.1",
    "jquery-validation": "1.16.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.8",
    "angular-bootstrap": "v1.1.0"
  }
}
```
Mentéskor a *bower.json* fájl, Angular telepítve van a projekt *wwwroot/lib* mappát. Ezenkívül szerepel a listán belül a *függőségek/Bower* mappa.

### <a name="update-the-sitejs-file"></a>A site.js fájl frissítése
Nyissa meg a *wwwroot/js/site.js* fájlt.  Cserélje ki annak tartalmát a JavaScript a kezdőlap nézetek által használt:

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>A Index.cshtml fájl frissítése
Nyissa meg a *Views/Home/Index.cshtml* fájl, a nézet a kezdőlap vezérlő jellemző.  Cserélje ki annak tartalmát a következőt, majd a változtatások mentéséhez.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>A _Layout.cshtml fájl frissítése
Nyissa meg a *Views/Shared/_Layout.cshtml* fájlt, az ASP.NET-alkalmazás alapértelmezett elrendezését.  Cserélje ki annak tartalmát a következőt, majd a változtatások mentéséhez.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="~/lib/angular/angular.js"></script>
<script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>A VotingWeb.cs fájl frissítése
Nyissa meg a *VotingWeb.cs* fájlt, amely létrehozza az ASP.NET Core WebHost az állapot nélküli-szolgáltatást a WebListener webkiszolgáló belül.  

Adja hozzá a `using System.Net.Http;` irányelv a fájl elejéhez.  

Cserélje le a `CreateServiceInstanceListeners()` és a következő működni, akkor a módosítások mentéséhez.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Is hozzáadhat a `GetVotingDataServiceName` metódus, amely során kérdezi le azt a szolgáltatás nevét adja vissza:

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Adja hozzá a VotesController.cs
Vegyen fel egy vezérlőt, amely meghatározza a szavazó műveletek. Kattintson a jobb gombbal a a **tartományvezérlők** mappát, majd válassza ki **Hozzáadás -> Új elem -> osztály**.  A fájl neve "VotesController.cs", és kattintson a **Hozzáadás**.  

Cserélje ki a fájl tartalmát a következőt, majd a változtatások mentéséhez.  A későbbi [VotesController.cs fájl frissíthető](#updatevotecontroller_anchor), ezt a fájlt úgy módosul, hogy olvasási és írási szavazó adatok a háttér-szolgáltatás.  A lépést a tartományvezérlő nézetbe statikus karakterlánc adatokat ad vissza.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>A figyelő portja konfigurálása
A VotingWeb előtér-szolgáltatás létrehozásakor, a Visual Studio véletlenszerűen választ egy portot a figyelést a szolgáltatáshoz.  A VotingWeb szolgáltatás úgy működik, mint az előtér-alkalmazás, és elfogadja a külső forgalom, most, hogy a szolgáltatás kötése egy rögzített méretű és jól ismeri port.  A [szolgáltatás jegyzékfájl](service-fabric-application-and-service-manifests.md) szolgáltatásvégpontokra deklarál. A Solution Explorerben nyissa meg a *VotingWeb/PackageRoot/ServiceManifest.xml*.  Keresés a **végpont** erőforrás a **erőforrások** szakaszt, és módosítsa a **Port** értéke 80-as, vagy egy másik portra. Üzembe helyezését, és futtassa az alkalmazást helyileg, az alkalmazás figyelőportja nyitott és elérhető a számítógépen kell lennie.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Az alkalmazás URL-Címének tulajdonság értéke a szavazási projekt frissíteni, egy webböngészőben megnyílik a megfelelő porthoz hibakeresése "F5" használatával.  A Megoldáskezelőben, válassza ki a **Voting** projektet és a frissítés a **alkalmazás URL-Címének** tulajdonság.

![Alkalmazás URL-címe](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

### <a name="deploy-and-run-the-application-locally"></a>Telepítheti és futtathatja az alkalmazást helyileg
Most lépjen tovább, és futtassa az alkalmazást. Nyomja le az `F5` billentyűt a Visual Studióban, hogy üzembe helyezze az alkalmazást a hibakereséshez. `F5`sikertelen lesz, ha nem korábban nyissa meg a Visual Studióban **rendszergazda**.

> [!NOTE]
> Az alkalmazás első helyi történő üzembe helyezésekor a Visual Studio létrehoz egy helyi hibakeresési fürtöt.  A fürt létrehozása eltarthat egy ideig. A fürt létrehozási állapota a Visual Studio kimeneti ablakában jelenik meg.

Ezen a ponton a webalkalmazás kell kinéznie:

![ASP.NET Core front-end](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Az alkalmazás hibakeresését végzi leállításához vissza a Visual Studio, és nyomja le az **Shift + F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Állapot-nyilvántartó háttér-szolgáltatás hozzáadása az alkalmazáshoz
Most, hogy az alkalmazás egy ASP.NET Web API-szolgáltatás fut, lépjen tovább, és adja hozzá egy állapotalapú megbízható szolgáltatást, hogy néhány adat tárolása az alkalmazást.

A Service Fabric következetesen és megbízhatóan tárolja az adatok jobb belül a szolgáltatás megbízható gyűjtemények segítségével teszi lehetővé. Megbízható gyűjtemények olyan magas rendelkezésre állású és megbízható gyűjteményosztály, amelyek bárki, aki használta a C# gyűjtemények számára.

Ebben az oktatóanyagban létrehoz egy szolgáltatás, amely egy megbízható gyűjtemény egy számláló értékét tárolja.

1. A Megoldáskezelőben kattintson a jobb gombbal **szolgáltatások** az alkalmazásban le, és válassza a **Hozzáadás > új Service Fabric-szolgáltatás**.
    
2. Az a **új Service Fabric-szolgáltatás** párbeszédpanelen válasszon **állapotalapú alkalmazások és szolgáltatások ASP.NET Core**, és a szolgáltatás **VotingData** nyomja le az ENTER **OK**.

    ![A Visual Studio Új szolgáltatás párbeszédpanelje](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    A service-projekt létrehozása után a felhasználó két szolgáltatást az alkalmazásban. Továbbra is építenie az alkalmazást, mert a szolgáltatás ugyanúgy is hozzáadhat. Minden egyes lehet függetlenül rendszerverzióval ellátott és frissített.

3. A következő oldalon biztosít az ASP.NET Core projektsablonjai. A jelen oktatóanyag esetében válassza ki a **Web API**.

    ![ASP.NET-projekt típusának kiválasztása](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    A Visual Studio létrehoz egy szolgáltatási projektet, és megjeleníti őket a Megoldáskezelőben.

    ![Megoldáskezelő](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Adja hozzá a VoteDataController.cs

A a **VotingData** projekt kattintson a jobb gombbal a **tartományvezérlők** mappát, majd válassza ki **Hozzáadás -> Új elem -> osztály**. A fájl neve "VoteDataController.cs", és kattintson a **Hozzáadás**. Cserélje ki a fájl tartalmát a következőt, majd a változtatások mentéséhez.

```csharp
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>Csatlakozás a szolgáltatások
A következő lépésben csatlakozzon a két szolgáltatás, és ellenőrizze az előtér-webkiszolgálók alkalmazás beolvasása és beállítása a szavazás a háttér-szolgáltatás adatait.

A Service Fabric hogyan kommunikáljanak megbízható szolgáltatások teljes rugalmasságot biztosít. Egyetlen alkalmazásban lehetséges, hogy TCP-n keresztül elérhető szolgáltatások. Elképzelhető, hogy egy HTTP REST API-n keresztül érhető el, hogy más szolgáltatások és egyéb szolgáltatások továbbra is lehet webes szoftvercsatornák keresztül érhető el. A rendelkezésre álló lehetőségeket, és a mellékhatásokkal jár a háttérben, lásd: [szolgáltatások folytatott kommunikáció](service-fabric-connect-and-communicate-with-services.md).

Ebben az oktatóanyagban használja [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md).

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>A VotesController.cs fájl frissítése
Az a **VotingWeb** projektben nyissa meg a *Controllers/VotesController.cs* fájlt.  Cserélje le a `VotesController` definíciójának tartalma a következő osztályt, majd mentse a módosításokat.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás bemutatása
A szavazóalkalmazás két szolgáltatásból áll:
- Webes kezelőfelületi szolgáltatás (VotingWeb) – Olyan ASP.NET Core webes kezelőfelületi szolgáltatás, amely kiszolgálja a weboldalt, és webes API-kat biztosít a háttérszolgáltatással folytatott kommunikációhoz.
- Háttérszolgáltatás (VotingData) – Olyan ASP.NET Core webszolgáltatás, amely egy API-t biztosít a szavazási eredmények tárolásához egy megbízható, lemezen őrzött szótárban.

![Alkalmazásdiagram](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Amikor szavazatot adnak le az alkalmazásban, az alábbi eseményekre kerül sor:
1. A JavaScript HTTP PUT kérelemként elküldi a szavazati kérést a webes API-nak a webes kezelőfelületi szolgáltatásban.

2. A webes kezelőfelületi szolgáltatás egy proxy segítségével megkeresi és továbbítja a HTTP PUT kérelmet a háttérszolgáltatásnak.

3. A háttérszolgáltatás fogadja a bejövő kérelmet, és egy megbízható szótárban tárolja a frissített eredményt, amelyet a fürt több csomópontjára is replikál, és egy lemezen őriz. Az alkalmazás összes adata a fürtön tárolódik, így nincs szükség adatbázisra.

## <a name="debug-in-visual-studio"></a>Hibakeresés a Visual Studióban
A Visual Studióban történő hibakeresés során egy helyi Service Fabric fejlesztési fürtöt használ. Arra is lehetősége van, hogy a hibakeresési folyamatot a saját forgatókönyvéhez igazítsa. Ebben az alkalmazásban a háttér-szolgáltatásban egy megbízható szótár használatával adatok tárolására. A Visual Studio alapértelmezés szerint a hibakereső leállításakor eltávolítja az alkalmazást. Az alkalmazás eltávolításával a háttérszolgáltatásban tárolt adatok is el lesznek távolítva. Ha szeretné megtartani az adatokat a hibakeresési munkamenetek között, akkor módosítania kell az **Application Debug Mode** (Alkalmazás hibakeresési módja) tulajdonságot a **Voting** (Szavazás) projektben a Visual Studióban.

Ha szeretné megtekinteni, hogy mi történik a kódban, hajtsa végre a következő lépéseket:
1. Nyissa meg a **VotesController.cs** fájlt, és állítson be egy töréspontot a webes API **Put** metódus (sor 63) – a fájlt a Visual studióban a Solution Explorer kereshet.

2. Nyissa meg a **VoteDataController.cs** fájlt, és állítson be egy töréspontot ezen webes API **Put** metódus (53. sor).

3. Térjen vissza a böngészőhöz, és kattintson egy szavazási lehetőségre vagy adjon meg egy újat. Az első töréspont a webes kezelőfelület API-vezérlőjében jelentkezik.
    
    1. A böngészőben futó JavaScript ekkor küld kérelmet a webes kezelőfelületi szolgáltatás API-vezérlőjének.
    
    ![Szavazási kezelőfelületi szolgáltatás hozzáadása](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Először összeállítani a ReverseProxy a háttér-szolgáltatás URL-címe **(1)**.
    3. Majd küldje a HTTP PUT kérés a ReverseProxy **(2)**.
    4. Végül a térjen vissza az ügyfélnek a háttér-szolgáltatás válasza **(3)**.

4. A folytatáshoz nyomja le az **F5** billentyűt.
    1. Ezzel elérte a háttérszolgáltatás töréspontját.
    
    ![Szavazási háttérszolgáltatás hozzáadása](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. A metódus első sorában **(1)** használja a `StateManager` nevű megbízható szótár felvétele, illetve `counts`.
    3. A megbízható szótárakban tárolt értékekkel folytatott mindennemű interakcióhoz tranzakcióra van szükség, amelyet ez a using utasítás **(2)** hoz létre.
    4. A tranzakció, frissítse a szavazó beállítás értékének a megfelelő kulcs értékét, és véglegesíti a művelet **(3)**. Ha a véglegesítési metódus visszatért, az adatok frissülnek a szótárban, és a fürt egyéb csomópontjaira is replikálódnak. Az adatok ettől fogva biztonságosan tárolódnak a fürtön, és a háttérszolgáltatás feladatait más csomópontok is átvehetik, míg az adatok továbbra is elérhetők maradnak.
5. A folytatáshoz nyomja le az **F5** billentyűt.

A hibakeresési munkamenet leállításához nyomja le a **Shift+F5** billentyűkombinációt.


## <a name="next-steps"></a>További lépések
Az oktatóanyag ezen része megtanulta, hogyan:

> [!div class="checklist"]
> * Állapot-nyilvántartó megbízható szolgáltatásként az ASP.NET Core webes API-szolgáltatás létrehozása
> * Állapot nélküli webszolgáltatásként ASP.NET Core webalkalmazás-szolgáltatás létrehozása
> * A fordított proxy segítségével kommunikál az állapotalapú szolgáltatással

Előzetes következő oktatóanyagot:
> [!div class="nextstepaction"]
> [Telepítse központilag az alkalmazást az Azure-bA](service-fabric-tutorial-deploy-app-to-party-cluster.md)

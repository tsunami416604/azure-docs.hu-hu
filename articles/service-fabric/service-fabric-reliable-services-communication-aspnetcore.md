---
title: Kommunikáció az ASP.NET Core-szolgáltatás |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az ASP.NET Core az állapot nélküli és állapotalapú Reliable Services.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 5a4b7514005da9e9a998dba014fa0ea6c014397a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268517"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core a Service Fabric Reliable Services

Az ASP.NET Core egy új nyílt forráskódú és platformfüggetlen keretrendszer, amellyel modern felhőalapú internetkapcsolattal rendelkező alkalmazások, például a web apps, az IoT-alkalmazások és a mobilalkalmazások háttérkomponensei a. 

Ez a cikk egy részletes útmutató a Service Fabric Reliable Services használata az ASP.NET Core szolgáltatások üzemeltetése a **Microsoft.ServiceFabric.AspNetCore.** * a NuGet-csomagok gyűjteménye.

Egy bevezető oktatóanyag a Service Fabric ASP.NET Core-on és a fejlesztési környezet beállítása beszerzésével kapcsolatban lásd: [.NET-alkalmazás létrehozása](service-fabric-tutorial-create-dotnet-app.md).

Ez a cikk többi része feltételezi, hogy már ismeri az ASP.NET Core. Ha nem, javasoljuk, hogy olvassa a [ASP.NET Core alapjai](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core a Service Fabric-környezetben

ASP.NET Core és a Service Fabric-alkalmazásokat a .NET Core, valamint a teljes .NET-keretrendszert is futtathatja. ASP.NET Core a Service Fabric két különböző módon használható:
 - **A Vendég futtatható üzemeltetett**. Ez elsősorban meglévő ASP.NET Core-alkalmazások futtatását a Service Fabric programkód módosítása nélkül.
 - **A Reliable Services belül futó**. Ez lehetővé teszi, hogy a Service Fabric-futtatókörnyezet jobb integrációt, és lehetővé teszi, hogy a állapotalapú ASP.NET Core szolgáltatások.

Ez a cikk ismerteti, hogyan ASP.NET Core egy Reliable Services használata az ASP.NET Core-integrációs összetevők, amelyek terjesztésére a Service Fabric SDK-val belül. 

## <a name="service-fabric-service-hosting"></a>A Service Fabric-szolgáltatás üzemeltetéséhez

A Service Fabric, egy vagy több példány és/vagy a szolgáltatás replikák futtassa egy *gazdafolyamat szolgáltatás*, egy végrehajtható fájlt, amely a szolgáltatás-kódját. A szolgáltatás szerző saját a gazdagép-folyamat és a Service Fabric aktiválja, és monitorozza az Ön számára.

(Max. MVC 5) hagyományos ASP.NET szorosan összekapcsolt IIS System.Web.dll keresztül. ASP.NET Core egy a webkiszolgáló és a webes alkalmazások elkülönítése biztosít. Ez lehetővé teszi, hogy a webes alkalmazásokat, hogy a különböző webkiszolgálók közötti hordozható, és emellett lehetővé teszi a webkiszolgálók *saját üzemeltetésű*, ami azt jelenti, hogy egy webkiszolgáló megkezdheti a saját folyamatának helyett egy dedikált webes tulajdonában lévő folyamat szerverszoftver, például az IIS. 

Annak érdekében, hogy a Service Fabric-szolgáltatás és az ASP.NET, a Vendég végrehajtható fájlt vagy egy Reliable Service-ben kombinálhatja az ASP.NET elindíthatja a szolgáltatás gazdafolyamat belül kell lennie. ASP.NET Core, helyi üzemeltetés lehetővé teszi, hogy ez.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Az ASP.NET Core Reliable Service-ben üzemeltető
Általában helyi ASP.NET Core-alkalmazások létrehozása a WebHost a belépési pont egy alkalmazás, például a `static void Main()` metódus az `Program.cs`. Ebben az esetben a folyamat élettartama a WebHost életciklusának van kötve.

![Hosting ASP.NET Core in a process][0]

A belépési pontja viszont nem a megfelelő hely az hozzon létre egy Webhostot az egy Reliable Service-ben, mert az alkalmazás belépési pont csak szolgál egy bizonyos szolgáltatástípusként a Service Fabric-futtatókörnyezet, regisztrálja, hogy az adott szolgáltatás típusú példányok létrehozhat. A WebHost létre kell hozni egy Reliable Services magát. A szolgáltatás gazdagép folyamaton belül szolgáltatáspéldányok és/vagy a replikák végigveheti több életciklusának. 

A szolgáltatás osztály kapcsolatból származtatott kapcsolatot képviseli egy Reliable Services-példányt `StatelessService` vagy `StatefulService`. A kommunikációs verem szolgáltatás szerepel egy `ICommunicationListener` megvalósítása a service osztályban. A `Microsoft.ServiceFabric.AspNetCore.*` NuGet-csomagok megvalósítása tartalmaz `ICommunicationListener` , indítsa el, és az ASP.NET Core Webhostot kezelheti a Kestrel vagy a HttpSys Reliable Service-ben.

![Az ASP.NET Core Reliable Service-ben üzemeltető][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Az ASP.NET Core ICommunicationListeners
A `ICommunicationListener` Kestrel és a HttpSys megvalósítások az `Microsoft.ServiceFabric.AspNetCore.*` NuGet-csomagok hasonló felhasználási minták rendelkezik, de az adott minden webkiszolgálóhoz némileg eltérő műveletet végrehajtani. 

Mindkét kommunikációs figyelőket adjon meg egy konstruktort, amely a következő argumentumot:
 - **`ServiceContext serviceContext`**: A `ServiceContext` objektum, amely a futó szolgáltatással kapcsolatos információkat tartalmazza.
 - **`string endpointName`**: a nevét egy `Endpoint` ServiceManifest.xml a konfigurációt. Ez az elsősorban, ahol a két kommunikációs figyelőket eltérőek: HttpSys **igényel** egy `Endpoint` konfigurációs, míg a Kestrel nem.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: a lambda, amely hoz létre, és visszatérési megvalósító egy `IWebHost`. Ez lehetővé teszi, hogy konfigurálása `IWebHost` azt szokásosan tenné az ASP.NET Core alkalmazás módja. A lambda biztosít egy URL-címnek, ami akkor jön létre, a lehetőségek, attól függően, a Service Fabric-integráció és a `Endpoint` konfigurációs adnia. URL-cím majd lehet módosítani vagy használt –, hogy a webkiszolgáló elindításához.

## <a name="service-fabric-integration-middleware"></a>A Service Fabric-integráció közbenső
A `Microsoft.ServiceFabric.AspNetCore` NuGet-csomag tartalmazza a `UseServiceFabricIntegration` metódust a `IWebHostBuilder` , amely hozzáadja a Service Fabric-kompatibilis közbenső szoftverek. A közbenső szoftver konfigurálja a Kestrel vagy HttpSys `ICommunicationListener` egy egyedi URL-címe Regisztrálás a Service Fabric elnevezési szolgáltatásban, majd összeveti az ügyfélkérelmek annak biztosítása érdekében az ügyfelek csatlakoznak a megfelelő szolgáltatást. Erre azért szükség, például a Service Fabric, ahol több webalkalmazás az azonos fizikai vagy virtuális gépen futtatható, de ne használjon egyedi állomásnevek, hogy megakadályozza a véletlenül a nem megfelelő szolgáltatáshoz való csatlakozás megosztott gazdagép környezetben. Ebben a forgatókönyvben a következő szakaszban részletesen ismertetjük.

### <a name="a-case-of-mistaken-identity"></a>Egy esetet, tehát a helytelen identitás
Szolgáltatás replikákat, függetlenül attól, protokoll, egyedi IP:port együttes figyelése. Replika szolgáltatás megkezdte a figyelést IP:port a végpont, miután a végpont címe és jelentéseket küldeni a Service Fabric elnevezési szolgáltatásban, azt könnyen megtalálhatók legyenek az ügyfelek vagy más szolgáltatások. Szolgáltatások dinamikusan hozzárendelt alkalmazás portok használatára, ha a szolgáltatás replika webkiszolgálóikat felhasználhatja az egyazon IP:port végpont egy másik szolgáltatás, amely korábban az azonos fizikai vagy virtuális gépen. Emiatt egy ügyfél véletlenül szolgáltatáshoz való csatlakozáshoz a nem megfelelő. Ez akkor fordulhat elő, ha a következő eseményekre kerül sor:

 1. A szolgáltatás a 10.0.0.1:30000 figyeli, HTTP protokollon keresztül. 
 2. Oldja fel a szolgáltatás egy ügyfél és a cím 10.0.0.1:30000 beolvasása
 3. A szolgáltatás helyez át egy másik csomópont.
 4. B szolgáltatás el van helyezve 10.0.0.1 és webkiszolgálóikat használja ugyanazt a portot 30000.
 5. Ügyfél próbál kapcsolódni a szolgáltatáshoz A gyorsítótárazott cím 10.0.0.1:30000 használata.
 6. Ügyfél most már csatlakozik sikeresen service B nem működnek együtt, csatlakozik-e a megfelelő szolgáltatást.

Hibák diagnosztizálása nehezen véletlenszerű időpontokban léphetnek föl. 

### <a name="using-unique-service-urls"></a>Egyedi szolgáltatási URL-címek használata
Ennek megelőzése érdekében szolgáltatások is közzététele egy végpontot az elnevezési szolgáltatásban, az egyedi azonosítóját és, hogy egyedi azonosító érvényesítése során az ügyfelek kéréseit. Ez az egy együttműködési művelet megbízható környezetben nem megerősítve a rosszindulatú-bérlői szolgáltatások között. Ez nem nyújt biztonságos szolgáltatás hitelesítési megerősítve a rosszindulatú bérlős környezetben.

Megbízható környezetben, a közbenső szoftver által hozzáadott a `UseServiceFabricIntegration` metódus automatikusan hozzáfűzi a címet, amely az elnevezési szolgáltatásban való közzétételekor, és ellenőrzi, hogy minden kérelemnél azonosító egyedi azonosítója. Ha az azonosító nem egyezik, a közbenső szoftverek, az azonnal HTTP 410 Megszűnt választ adja vissza.

Szolgáltatásokat használó egy dinamikusan hozzárendelt portot kell, használja a közbenső szoftverek.

Egy rögzített egyedi portot használó szolgáltatások együttműködési környezetben nincs probléma. Egy rögzített egyedi port használatos általában kívülről elérhető szolgáltatásokról, amelyek egy jól ismert portot, az ügyfélalkalmazások számára, hogy csatlakozni kell. A legtöbb Internet felé néző webes alkalmazások például használja a 80-as vagy 443-as port webes böngésző kapcsolatokhoz. Ebben az esetben az egyedi azonosító nem lehet engedélyezni.

Az alábbi ábrán látható, a kérelem folyamat a közbenső szoftver engedélyezve:

![Service Fabric ASP.NET Core-integráció][2]

A Kestrel és a HttpSys `ICommunicationListener` pontosan ugyanúgy használják ezt a mechanizmust. Bár a HttpSys belsőleg tudja különböztetni kérelmek használatával az alapul szolgáló egyedi URL-cím elérési utak alapján *http.sys* port megosztása funkció, amely a funkció *nem* a HttpSys által használt `ICommunicationListener` végrehajtása, mert a korábban ismertetett forgatókönyvben HTTP 503-as és a HTTP 404-es hiba állapotkódok vezethet. Amely viszont azt nehezíti meg szándékának azonosítása a hiba, hogy az ügyfelek HTTP 503-as és a HTTP 404-es már általánosan használt egyéb hibákat. Ennélfogva a Kestrel és HttpSys `ICommunicationListener` megvalósításokhoz egységesen ezeket a közbenső szoftver által biztosított a `UseServiceFabricIntegration` metódust, hogy az ügyfelek csak kell végrehajtani egy szolgáltatásvégpont újra feloldani a HTTP 410 válaszok művelet.

## <a name="httpsys-in-reliable-services"></a>A Reliable Services HttpSys
HttpSys importálásával használható Reliable Service-ben a **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet-csomagot. Ez a csomag tartalmaz `HttpSysCommunicationListener`, megvalósítását `ICommunicationListener`, amely lehetővé teszi, hogy hozzon létre egy ASP.NET Core Webhostot belül egy Reliable Services HttpSys használja, mint a webkiszolgálón.

HttpSys épül a [Windows HTTP-kiszolgáló API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Ez a *http.sys* dolgozza fel a HTTP-kérelmekre, és irányíthatja a folyamatokat, amelyeken webalkalmazások futnak az IIS által használt kernel-illesztőprogram. Ez lehetővé teszi az ugyanazon fizikai vagy virtuális gépen a webalkalmazások üzemeltetéséhez ugyanazt a portot egy egyedi URL-cím vagy állomásnév használatát több folyamatot. Ezek a funkciók hasznosak lehetnek a Service Fabric ugyanazon fürt több webhely üzemeltetésére.

>[!NOTE]
>HttpSys végrehajtása csak Windows-platform működik.

A következő diagram azt ábrázolja, hogyan használja a HttpSys a *http.sys* kernel-illesztőprogram a Windows-port megosztása számára:

![a HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>Az állapotmentes szolgáltatás HttpSys
Használandó `HttpSys` az állapotmentes szolgáltatás, bírálja felül a `CreateServiceInstanceListeners` metódust, és lépjen vissza a `HttpSysCommunicationListener` példány:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>Az állapotalapú szolgáltatások HttpSys

`HttpSysCommunicationListener` jelenleg nem készült miatt komplikációk az alapul szolgáló az állapotalapú szolgáltatások használatát *http.sys* port megosztása funkció. További információkért tekintse meg a következő szakasz a dinamikus port foglalási httpsys. Az állapotalapú szolgáltatások esetében a Kestrel a javasolt webkiszolgáló.

### <a name="endpoint-configuration"></a>Végpont-konfiguráció

Egy `Endpoint` konfigurációra szükség a Windows HTTP Server API-t használó, beleértve a HttpSys webkiszolgálók számára. A Windows HTTP Server API-t használó webkiszolgálók először le kell az URL-CÍMÉT a *http.sys* (általában mindez a [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) eszközt). Ehhez a művelethez emelt szintű jogosultságokkal, amelyek a szolgáltatás alapértelmezés szerint nem rendelkeznek. A "http" vagy "https" beállításait a `Protocol` tulajdonságát a `Endpoint` konfigurációját a *ServiceManifest.xml* segítségével kifejezetten kérje meg a Service Fabric-futtatókörnyezet, az URL-cím regisztrálása  *a HTTP.sys* az Ön nevében használatával a [ *erős helyettesítő* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL-előtagot.

Foglaljon le például `http://+:80` egy szolgáltatást, a következő konfigurációt kell használni a ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

A végpont neve adható át, és a `HttpSysCommunicationListener` konstruktort:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Statikus port HttpSys használata
Httpsys statikus port használatára, adja meg a port számát, a `Endpoint` konfiguráció:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>A dinamikus port HttpSys használata
Httpsys dinamikusan hozzárendelt portot használ, hagyja ki ezt a `Port` tulajdonságot a `Endpoint` konfiguráció:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Foglalva dinamikus portot egy `Endpoint` a konfiguráció csak biztosít egy portot *gazdagép folyamatonként*. A Service Fabric aktuális üzemeltetési modell lehetővé teszi, hogy több szolgáltatáspéldányok és/vagy ugyanabban a folyamatban, ami azt jelenti, hogy mindegyik osztani, amikor keresztül kiosztott ugyanazt a portot kell üzemeltetett replikák a `Endpoint` konfigurációja. Több HttpSys példány megoszthat egy portot az alapul szolgáló használatával *http.sys* nem támogatja a funkciót, de, amelyek megosztása port `HttpSysCommunicationListener` miatt a komplikációk azt mutatja be, az ügyféli kérelmek részére. A dinamikus port használatra a Kestrel a javasolt webkiszolgáló.

## <a name="kestrel-in-reliable-services"></a>A Reliable Services kestrel
A kestrel importálásával használható Reliable Service-ben a **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet-csomagot. Ez a csomag tartalmaz `KestrelCommunicationListener`, megvalósítását `ICommunicationListener`, amely lehetővé teszi, hogy hozzon létre egy ASP.NET Core Webhostot belül egy Reliable Services Kestrel használja, mint a webkiszolgálón.

A kestrel, az ASP.NET Core platformfüggetlen webkiszolgáló libuv, a platformok közötti aszinkron i/o-tár alapján. HttpSys, ellentétben a Kestrel nem használ egy központi végponthoz vezető például *http.sys*. A HttpSys, ellentétben a Kestrel nepodporuje-port megosztása több folyamat között. A Kestrel minden példánya egy egyedi portot kell használnia.

![a kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>A kestrel stateless service-ben
Használandó `Kestrel` az állapotmentes szolgáltatás, bírálja felül a `CreateServiceInstanceListeners` metódust, és lépjen vissza a `KestrelCommunicationListener` példány:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>A kestrel egy állapotalapú szolgáltatásban
Használandó `Kestrel` egy állapotalapú szolgáltatásban bírálja felül a `CreateServiceReplicaListeners` metódust, és lépjen vissza a `KestrelCommunicationListener` példány:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

Ebben a példában egy egypéldányos példányát `IReliableStateManager` a WebHost függőségi injektálási tároló számára. Ez nem feltétlenül szükséges, de ez lehetővé teszi, hogy `IReliableStateManager` és az MVC-vezérlő műveletmetódusokhoz a Reliable Collections.

Egy `Endpoint` -konfiguráció nevét **nem** megadott `KestrelCommunicationListener` egy állapotalapú service-ben. Ennek a magyarázatát részletesebben a következő szakaszban.

### <a name="configure-kestrel-to-use-https"></a>A Kestrel konfigurálása HTTPS használatára
A szolgáltatás a Kestrel HTTPS engedélyezésekor kell több figyelési beállításainak megadása.  Frissítés a `ServiceInstanceListener` Végponthttps végpont használatát, és (például a 443-as porton) egy adott portot figyeljen. Ha a webes gazdagépet a Kestrel kiszolgáló használatára konfigurálja, konfigurálnia kell az összes hálózati adapter IPv6-címek figyelésére a Kestrel: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

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
```

A jelen oktatóanyagban használt teljes példa: [Kestrel konfigurálása HTTPS használatára](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Végpont-konfiguráció
Egy `Endpoint` Kestrel használatához nem szükséges konfiguráció. 

A kestrel egy olyan egyszerű, önálló webes kiszolgáló; ellentétben a HttpSys (vagy HttpListener), nem kell egy `Endpoint` konfigurációját a *ServiceManifest.xml* , mert nem szükséges URL-cím regisztráció megkezdése előtt. 

#### <a name="use-kestrel-with-a-static-port"></a>A Kestrel használata a statikus port
Statikus port konfigurálható a `Endpoint` ServiceManifest.xml konfigurációját a Kestrel való használatra. Bár ez nem feltétlenül szükséges, két lehetséges előnyt biztosít:
 1. Ha a port nem esik az alkalmazás porttartományából, nyitja meg, az operációs rendszer tűzfalon keresztül a Service Fabric.
 2. Az URL-cím keresztül biztosított `KestrelCommunicationListener` ezt a portot fogja használni.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Ha egy `Endpoint` van konfigurálva, a nevét kell kell megkérnie az `KestrelCommunicationListener` konstruktort: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Ha egy `Endpoint` konfiguráció nincs használatban, hagyja ki a nevét a a `KestrelCommunicationListener` konstruktor. Ebben az esetben egy dinamikus portot fogja használni. További információ a következő szakaszban talál.

#### <a name="use-kestrel-with-a-dynamic-port"></a>A Kestrel használata a dinamikus port
A kestrel nem használhatja az automatikus porthozzárendelés a a `Endpoint` konfigurációját a ServiceManifest.xml, mert automatikus porthozzárendelés a egy `Endpoint` konfigurációs rendeli hozzá egy egyedi portját *folyamaton*, és egyetlen gazdafolyamat Kestrel több példányt is tartalmazhatnak. A Kestrel-port megosztása nem támogatja, mert ez nem működik, a Kestrel mindegyiknek egy egyedi portot kell megnyitni.

A Kestrel dinamikus porthozzárendelés használni, hagyja ki ezt a `Endpoint` konfigurációját a ServiceManifest.xml teljesen, és ne adja át a végpont neve a `KestrelCommunicationListener` konstruktor:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Ebben a konfigurációban `KestrelCommunicationListener` automatikusan választja ki egy nem használt portot az alkalmazás porttartományából.

## <a name="service-fabric-configuration-provider"></a>Service Fabric Konfigurációszolgáltató
Az ASP.NET Core alkalmazás konfigurációja alapján állítja be a konfigurációszolgáltatók, olvassa el a kulcs-érték párok [konfiguráció az ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) további on általános ASP.NET Core konfigurációs támogatási megértéséhez.

Ez a szakasz ismerteti a Service Fabric Konfigurációszolgáltatót kell integrálása az ASP.NET Core konfigurációs importálásával a `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-csomagot.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration indítási bővítmények
Importálás után `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-csomagjára, regisztrálnia kell a Service Fabric konfiguráció forrása az ASP.NET Core-konfigurációjának API **AddServiceFabricConfiguration** bővítményei `Microsoft.ServiceFabric.AspNetCore.Configuration` névtér ellen `IConfigurationBuilder`

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Most már az ASP.NET Core-szolgáltatás is elérhető a Service Fabric konfigurációs beállítások, csakúgy, mint bármely más nastavení aplikace. A beállítások minta használatával például típusos objektumokba betölteni a beállításokat.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Alapértelmezett kulcsa leképezése
Alapértelmezés szerint a Service Fabric-konfigurációszolgáltató tartalmazza csomag neve, a szakasz nevét és a tulajdonságnév képez az asp.net core konfigurációs kulcsát a következő függvény használatával:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Például, ha egy konfigurációs csomagok nevű `MyConfigPackage` az alábbi tartalmat, majd a konfigurációs érték elérhető lesz az ASP.NET Core `IConfiguration` kulccsal *MyConfigPackage:MyConfigSection:MyParameter*
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric-konfigurációs beállítások
Service Fabric Konfigurációszolgáltató is támogatja a `ServiceFabricConfigurationOptions` módosíthatja a kulcs leképezés alapértelmezett viselkedését.

#### <a name="encrypted-settings"></a>Titkosított beállításai
A Service Fabric támogatja a beállítások titkosítására, Service Fabric Konfigurációszolgáltató ez is támogatja. Kövesse a biztonságos alapértelmezett alapelvet, az ASP.NET Core alapértelmezés szerint a titkosított beállításai are't descrypted által `IConfiguration`, a titkosított érték ott tárolt helyette. Azonban ha azt szeretné, visszafejteni az értéket tárolja az ASP.NET Core IConfiguration beállíthat DecryptValue jelző false értékűre `AddServiceFabricConfiguration` bővítményeket az alábbiak szerint:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Több konfigurációs csomag
Service Fabric támogatja a több konfigurációs csomagokat. Alapértelmezés szerint a csomag neve szerepel a konfigurációs kulcs. Sikerült beállítani a `IncludePackageName` jelzőt az alapértelmezett viselkedés módosításához.
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Egyéni kulcs leképezés értékének kinyerése és adatokkal való feltöltés
Módosításokon kívül a fenti 2 jelzők az alapértelmezett viselkedés módosításához, is támogatja a Service Fabric-Konfigurációszolgáltató speciális forgatókönyvek az egyéni keresztül a fő leképezés `ExtractKeyFunc` és az egyéni bontsa ki az értékek keresztül `ExtractValueFunc`. Ezt akkor is módosíthatja a teljes folyamat az ASP.NET Core konfigurációs keresztül a Service Fabric-konfigurációs adatok feltöltéséhez `ConfigAction`.

Az alábbi példák bemutatják, használandó `ConfigAction` szabhatja testre az adatokkal való feltöltés.
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```
### <a name="configuration-update"></a>Configuration Update
Service Fabric Konfigurációszolgáltatót a konfigurációjának frissítése is támogatja, és használhatja az ASP.NET Core `IOptionsMonitor` módosítási értesítések fogadásához és `IOptionsSnapshot` frissítse a konfigurációs adatokat. További információkért lásd: [ASP.NET Core beállítások](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options).

Ez alapértelmezés szerint támogatott, és a kódolás további konfigurációs frissítés engedélyezéséhez szükségesek.

## <a name="scenarios-and-configurations"></a>A forgatókönyvek és konfigurációk
Ez a szakasz ismerteti a következő esetekben, és biztosítja a webalkalmazás-kiszolgáló, port konfigurációja, a Service Fabric-integráció beállításai és egyéb beállítások egy megfelelően működő szolgáltatás elérése érdekében ajánlott kombinációja:
 - Kívülről elérhetővé tett ASP.NET Core állapotmentes szolgáltatás
 - Csak belső ASP.NET Core állapotmentes szolgáltatás
 - Csak belső ASP.NET Core állapotalapú szolgáltatás

Egy **kívülről elérhetővé tett** szolgáltatás, amely általában a terheléselosztón keresztül, a fürtön kívülről elérhető egy végponttal rendelkezik.

Egy **csak belső** szolgáltatás, amelynek végpont, csak a fürtön belül elérhető.

> [!NOTE]
> Állapot-nyilvántartó Szolgáltatásvégpontok általában nem láthatók az interneten. Fürtök deduplikálta a Service Fabric service megoldás, mint például az Azure Load Balancer terheléselosztók mögött található, nem lehet elérhetővé tenni az állapotalapú szolgáltatások, mert a terheléselosztó nem tudja megkeresni és irányíthatja a forgalmat a megfelelő állapotalapú szolgáltatás replika. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Kívülről elérhetővé tett ASP.NET Core állapotmentes szolgáltatások
A kestrel az előtérbeli szolgáltatásokhoz, amelyek külső, az internetre irányuló HTTP-végpontokat tesznek közzé a javasolt webkiszolgáló. Windows, a HttpSys biztosíthassuk port megosztási, amely lehetővé teszi több webszolgáltatás ugyanazt a portot, differenciált állomásnév vagy elérési útja, mindehhez nincs szüksége egy előtér-proxy vagy az átjáró a HTTP-útválasztást használó csomópontok ugyanahhoz az adatkészlethez használható.
 
Amikor az interneten vannak kitéve, állapotmentes szolgáltatás használja egy jól ismert és állandó végpontot, amely a terheléselosztón keresztül érhető el. Ez az Ön biztosítja a felhasználók számára az alkalmazás URL-CÍMÉT. Ajánlott a következő konfigurációt:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Kestrel | A kestrel az előnyben részesített webkiszolgáló, mert különböző Windows és Linux támogatott. |
| Port konfigurálása | statikus | A jól ismert statikus port lehet beállítani a `Endpoints` ServiceManifest.xml, például a 80-as HTTP vagy HTTPS-hez a 443-as konfigurációját. |
| ServiceFabricIntegrationOptions | None | A `ServiceFabricIntegrationOptions.None` beállítást kell használni, amikor a Service Fabric-integráció közbenső szoftver konfigurálása, hogy a szolgáltatás nem kísérli meg egy egyedi azonosítót a bejövő kérelmeket. Az alkalmazás külső felhasználók addig nem szerez tudomást az a közbenső szoftver által használt egyedi azonosító információkat. |
| Példányszám | -1 | A tipikus használati esetek a példányok száma beállítást meg kell "1", hogy az egy példány érhető el az összes csomóponton, hogy a forgalom fogadására a terheléselosztótól. |

Ha több kívülről elérhetővé tett szolgáltatások ugyanazokat a csomópontok, HttpSys egy egyedi, de stabil URL-cím is használható. Ez a IWebHost konfigurálásakor megadott URL-Címének módosításával is elvégezhető. Vegye figyelembe, hogy ez érvényes HttpSys csak.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Csak belső állapot nélküli ASP.NET Core-szolgáltatás
Állapotmentes szolgáltatások, amelyek a fürtön belül csak meghívni a egyedi URL-címeket kell használnia, és dinamikusan kiosztott porttartományt, hogy több szolgáltatások közötti együttműködést. Ajánlott a következő konfigurációt:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Kestrel | Bár HttpSys belső állapotmentes szolgáltatások esetében előfordulhat, hogy használható, a Kestrel a javasolt kiszolgálót úgy, hogy egy gazdagép megosztani több szolgáltatáspéldány.  |
| Port konfigurálása | dinamikusan kiosztott | Több replika egy állapotalapú szolgáltatás megoszthatja a gazdagép folyamat vagy a gazdagép operációs rendszere, és így kell egyedi portokat. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Dinamikus portok hozzárendelés Ez a beállítás megakadályozza, hogy a korábban ismertetett garanciafeltételeknek identitás probléma. |
| InstanceCount | bármilyen | A beállítás példányszám állítható bármilyen érték a szolgáltatás működtetéséhez szükséges. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Csak belső állapotalapú ASP.NET Core-szolgáltatás
Állapotalapú szolgáltatások, amelyek a fürtön belül csak meghívni a dinamikusan hozzárendelt portok segítségével biztosítja több szolgáltatások közötti együttműködést. Ajánlott a következő konfigurációt:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Kestrel | A `HttpSysCommunicationListener` , amelyben a replikák megosztani egy gazdafolyamaton állapotalapú szolgáltatások használatát nem készült. |
| Port konfigurálása | dinamikusan kiosztott | Több replika egy állapotalapú szolgáltatás megoszthatja a gazdagép folyamat vagy a gazdagép operációs rendszere, és így kell egyedi portokat. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Dinamikus portok hozzárendelés Ez a beállítás megakadályozza, hogy a korábban ismertetett garanciafeltételeknek identitás probléma. |

## <a name="next-steps"></a>További lépések
[Service Fabric-alkalmazás hibakeresése a Visual Studióval](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png

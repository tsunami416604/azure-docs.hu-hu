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
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939793"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Az ASP.NET Core, az Azure Service Fabric Reliable Services

ASP.NET Core egy olyan nyílt forráskódú és platformfüggetlen keretrendszer. Ezt a keretrendszert, amely felhőalapú, internetkapcsolattal rendelkező alkalmazások, például az IoT-alkalmazások, webalkalmazások,-mobil háttérrendszer.

Ez a cikk egy részletes útmutató ASP.NET Core való tárolása a Service Fabric Reliable Services használatával a rendszer a **Microsoft.ServiceFabric.AspNetCore.** NuGet-csomagok gyűjteménye.

Egy bevezető oktatóanyag a Service Fabric ASP.NET Core-on és a fejlesztési környezet beállítása beszerzésével kapcsolatban lásd: [oktatóanyag: Hozzon létre és telepíthet egy alkalmazást egy ASP.NET Core Web API kezelőfelületi szolgáltatás és a egy állapotalapú háttérszolgáltatás](service-fabric-tutorial-create-dotnet-app.md).

Ez a cikk többi része feltételezi, hogy Ön már ismeri az ASP.NET Core használatával. Ha nem, olvassa a [ASP.NET Core alapjai](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core a Service Fabric-környezetben

ASP.NET Core és a Service Fabric-alkalmazásokat a .NET Core vagy a teljes .NET-keretrendszer is futtathatja. ASP.NET Core a Service Fabric két különböző módon használhatja:
 - **A Vendég futtatható üzemeltetett**. Ezzel a módszerrel elsősorban meglévő ASP.NET Core-alkalmazások futtatását a Service Fabric programkód módosítása nélkül.
 - **A reliable Services belül futó**. Így lehetővé teszi, hogy a Service Fabric-futtatókörnyezet jobb integrációt, és lehetővé teszi az állapotalapú ASP.NET Core szolgáltatások.

Ez a cikk ismerteti, hogyan ASP.NET Core egy megbízható szolgáltatást, az ASP.NET Core a Service Fabric SDK integrációs összetevők keresztül belül.

## <a name="service-fabric-service-hosting"></a>A Service Fabric-szolgáltatás üzemeltetéséhez

A Service Fabric, egy vagy több példány és/vagy a szolgáltatás replikák futtassa egy *gazdafolyamat szolgáltatás*: egy végrehajtható fájlt, amely a szolgáltatás-kódját. A szolgáltatás szerző saját a gazdagép-folyamat, és a Service Fabric aktiválja, és monitorozza az Ön számára.

(Max. MVC 5) hagyományos ASP.NET szorosan összekapcsolt IIS System.Web.dll keresztül. ASP.NET Core egy a webkiszolgáló és a webes alkalmazások elkülönítése biztosít. Ez a fajta elkülönítés lehetővé teszi, hogy a webes alkalmazásokat, hogy a hordozható különböző webkiszolgálók között. Azt is lehetővé teszi a webkiszolgálók *saját üzemeltetésű*. Ez azt jelenti, hogy egy webkiszolgáló elkezdheti saját folyamat helyett egy dedikált web server szoftverek, például az IIS tulajdonában lévő folyamat.

Úgy, hogy a Service Fabric-szolgáltatást és az ASP.NET, vagy egy Vendég futtatható, vagy egy megbízható szolgáltatás az ASP.NET elindíthatja a szolgáltatás gazdafolyamat belül kell lennie. ASP.NET Core, helyi üzemeltetés lehetővé teszi, hogy ez.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Üzemeltetési ASP.NET Core reliable service-ben
Általában helyi ASP.NET Core-alkalmazások létrehozása a WebHost a belépési pont egy alkalmazás, például a `static void Main()` metódus az `Program.cs`. Ebben az esetben a WebHost életciklusának életciklusa alatt, a folyamat van kötve.

![Hosting ASP.NET Core in a process][0]

De az alkalmazás belépési pont nem hoz létre a WebHost egy megbízható szolgáltatásban jó helyen. Ennek oka az, a belépési pontja van csak való regisztrálásához használt egy bizonyos szolgáltatástípusként a Service Fabric-futtatókörnyezet, úgy, hogy az adott szolgáltatás típusú példányok tud létrehozni. A WebHost létre kell hozni egy megbízható szolgáltatás. A szolgáltatás gazdagép folyamaton belül szolgáltatáspéldányok és/vagy a replikák végigveheti több életciklusait. 

A szolgáltatás osztály kapcsolatból származtatott kapcsolatot képviseli egy Reliable Services-példányt `StatelessService` vagy `StatefulService`. A kommunikációs verem szolgáltatás szerepel egy `ICommunicationListener` megvalósítása a service osztályban. A `Microsoft.ServiceFabric.AspNetCore.*` NuGet-csomagok megvalósítása tartalmaz `ICommunicationListener` , indítsa el, és felügyelheti az ASP.NET Core Webhostot a Kestrel vagy a HTTP.sys egy reliable service-ben.

![Diagram üzemeltetéséhez az ASP.NET Core reliable service-ben][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Az ASP.NET Core ICommunicationListeners
A `ICommunicationListener` Kestrel és a HTTP.sys megvalósítások az `Microsoft.ServiceFabric.AspNetCore.*` NuGet-csomagok hasonló felhasználási minták rendelkezik. Azonban azok minden webkiszolgálóhoz adott némileg különböző műveleteket végezni. 

Mindkét kommunikációs figyelőket adjon meg egy konstruktort, amely a következő argumentumot:
 - **`ServiceContext serviceContext`** : Ez a `ServiceContext` objektum, amely a futó szolgáltatással kapcsolatos információkat tartalmazza.
 - **`string endpointName`** : Azt a nevet, egy `Endpoint` ServiceManifest.xml a konfigurációt. Azt az elsősorban, ahol a két kommunikációs figyelőket eltérőek. A HTTP.sys *igényel* egy `Endpoint` konfigurációs, míg a Kestrel nem.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : Ez az egy lambda, amely alkalmazza, a hozhatja létre, és adja vissza egy `IWebHost`. Ez lehetővé teszi, hogy konfigurálását `IWebHost` azt szokásosan tenné az ASP.NET Core alkalmazás módja. A lambda biztosít egy URL-címet, amely akkor jön létre az Ön számára, attól függően, a Service Fabric integrációs lehetőségeket használja, és a `Endpoint` konfigurációs adnia. Majd módosíthatja vagy adott URL-cím használatával a webkiszolgáló elindításához.

## <a name="service-fabric-integration-middleware"></a>A Service Fabric-integráció közbenső
A `Microsoft.ServiceFabric.AspNetCore` NuGet-csomag tartalmazza a `UseServiceFabricIntegration` metódust a `IWebHostBuilder` , amely hozzáadja a Service Fabric – figyelembe közbenső szoftverek. A közbenső szoftver konfigurálja a Kestrel vagy a HTTP.sys `ICommunicationListener` regisztrálni egy egyedi URL-címe a Service Fabric elnevezési szolgáltatásban. Ezután ellenőrzi a ügyfélkérések győződjön meg, hogy az ügyfelek csatlakoznak a megfelelő szolgáltatást. 

Ez a lépés szükség, hogy megakadályozza a véletlenül a nem megfelelő szolgáltatáshoz való csatlakozás. Amely azért, mert az állomás megosztott környezetekben, például a Service Fabric, több webalkalmazás az azonos fizikai vagy virtuális gépen futtatható, de ne használjon egyedi állomásneveket. Ebben a forgatókönyvben a következő szakaszban részletesen ismertetjük.

### <a name="a-case-of-mistaken-identity"></a>Egy esetet, tehát a helytelen identitás
Szolgáltatás replikákat, függetlenül attól, protokoll, egyedi IP:port együttes figyelése. Replika szolgáltatás megkezdte a figyelést IP:port a végpont, miután a végpont címe és jelentéseket küldeni a Service Fabric elnevezési szolgáltatásban. Itt ügyfelek vagy más szolgáltatások felderíthesse azt. Szolgáltatások dinamikusan hozzárendelt alkalmazás portok használatára, ha egy szolgáltatás replika webkiszolgálóikat ugyanazon IP:port végpontja: egy másik szolgáltatást korábban a azonos fizikai vagy virtuális gép használhatnak. Emiatt egy ügyfél véletlenül szolgáltatáshoz való csatlakozáshoz a nem megfelelő. Ebben a forgatókönyvben okozhat, ha a következő eseményekre kerül sor:

 1. A szolgáltatás a 10.0.0.1:30000 figyeli, HTTP protokollon keresztül. 
 2. Ügyfél oldja fel a szolgáltatás, valamint lekéri a cím 10.0.0.1:30000.
 3. A szolgáltatás helyez át egy másik csomópont.
 4. B szolgáltatás el van helyezve 10.0.0.1 és webkiszolgálóikat használja ugyanazt a portot 30000.
 5. Ügyfél próbál kapcsolódni a szolgáltatáshoz A gyorsítótárazott cím 10.0.0.1:30000 használata.
 6. Ügyfél most már sikeresen csatlakozott a B szolgáltatás, nem működnek együtt, csatlakozik-e a megfelelő szolgáltatást.

Hibák diagnosztizálása nehezen véletlenszerű időpontokban léphetnek föl.

### <a name="using-unique-service-urls"></a>Egyedi szolgáltatási URL-címek használata
Ezek a hibák megelőzése érdekében szolgáltatások is közzététele egy végpontot az elnevezési szolgáltatásban, az egyedi azonosítóját és, hogy egyedi azonosító érvényesítése során az ügyfelek kéréseit. Ez az egy együttműködési művelet megbízható környezetben nem megerősítve a rosszindulatú-bérlői szolgáltatások között. Ez nem biztosít a biztonságos szolgáltatás hitelesítési megerősítve a rosszindulatú bérlős környezetben.

Megbízható környezetben, a közbenső szoftver által hozzáadott a `UseServiceFabricIntegration` metódus automatikusan hozzáfűzi a cím az elnevezési szolgáltatásban közzétett egyedi azonosítója. Azt ellenőrzi, hogy minden kérelemnél azonosítója. Ha az azonosító nem egyezik, a közbenső szoftver, az azonnal HTTP 410 Megszűnt választ adja vissza.

Szolgáltatásokat használó egy dinamikusan hozzárendelt portot kell, használja a közbenső szoftverek.

Egy rögzített egyedi portot használó szolgáltatások a probléma nem szerepel egy együttműködési környezet. Egy rögzített egyedi port használatos általában kívülről elérhető szolgáltatásokról, amelyek egy jól ismert portot, az ügyfélalkalmazások számára, hogy csatlakozni kell. A legtöbb internet felé néző webes alkalmazások például használja a 80-as vagy 443-as port webes böngésző kapcsolatokhoz. Ebben az esetben nem lehet engedélyezni az egyedi azonosítója.

Az alábbi ábrán látható, a kérelem folyamat a közbenső szoftver engedélyezve:

![Service Fabric ASP.NET Core-integráció][2]

A Kestrel és a HTTP.sys `ICommunicationListener` pontosan ugyanúgy használják ezt a mechanizmust. Bár a HTTP.sys belsőleg tudja különböztetni kérelmek használatával az alapul szolgáló egyedi URL-cím elérési utak alapján **HTTP.sys** port megosztása funkció, amely a funkció *nem* a HTTP.sys általhasznált`ICommunicationListener`végrehajtására. Ez azt eredményezi, a korábban ismertetett forgatókönyvben HTTP 503-as és a HTTP 404-es hiba állapotkódok mert. Amely viszont azt nehezíti meg szándékának azonosítása a hiba, hogy az ügyfelek HTTP 503-as és a HTTP 404-es gyakran használt egyéb hibákat. 

Ennélfogva a Kestrel és a HTTP.sys `ICommunicationListener` implementáció által biztosított közbenső egységesen ezeket a `UseServiceFabricIntegration` metódust. Ezért az ügyfelek csak kell szolgáltatási végpont újra feloldani műveletet végrehajtani a HTTP 410 válaszokat.

## <a name="httpsys-in-reliable-services"></a>A Reliable Services HTTP.sys
Importálja a Reliable Services HTTP.sys is használhatja a **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet-csomagot. Ez a csomag tartalmaz `HttpSysCommunicationListener`, megvalósítását `ICommunicationListener`. `HttpSysCommunicationListener` lehetővé teszi, hogy hozzon létre egy ASP.NET Core Webhostot egy reliable Services belül a webkiszolgáló a HTTP.sys segítségével.

A HTTP.sys épül a [Windows HTTP-kiszolgáló API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Az API-t használ a **HTTP.sys** kernel-illesztőprogram dolgozza fel a HTTP-kérelmekre, és irányíthatja a webes alkalmazások futó folyamatok. Ez lehetővé teszi az ugyanazon fizikai vagy virtuális gépen a webalkalmazások üzemeltetéséhez ugyanazt a portot, vagy egy egyedi URL-cím elérési út vagy a gazdagép neve szerint használatát több folyamatot. Ezek a funkciók hasznosak lehetnek a Service Fabric ugyanazon fürt több webhely üzemeltetésére.

>[!NOTE]
>A HTTP.sys végrehajtása csak Windows-platformon működik.

A következő diagram azt ábrázolja, hogyan használja a HTTP.sys a **HTTP.sys** kernel-illesztőprogram a Windows-port megosztása számára:

![A HTTP.sys diagramja][3]

### <a name="httpsys-in-a-stateless-service"></a>A HTTP.sys az állapotmentes szolgáltatás
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

### <a name="httpsys-in-a-stateful-service"></a>A HTTP.sys egy állapotalapú szolgáltatásban

`HttpSysCommunicationListener` jelenleg nem arra készült, az állapotalapú szolgáltatások miatt a az alapul szolgáló komplikációk használatra **HTTP.sys** port megosztása funkció. További információkért tekintse meg a következő szakasz a HTTP.sys a dinamikus port lefoglalását. Az állapotalapú szolgáltatások esetében a Kestrel a javasolt webkiszolgáló.

### <a name="endpoint-configuration"></a>Végpont-konfiguráció

Egy `Endpoint` konfigurációra szükség a Windows HTTP Server API-t használó, beleértve a HTTP.sys webkiszolgálók számára. A Windows HTTP Server API-t használó webkiszolgálók először le kell az URL-CÍMÉT, a HTTP.sys (általában mindez a [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) eszközt). 

Ehhez a művelethez emelt szintű jogosultságokkal, amelyek a szolgáltatás alapértelmezés szerint nem rendelkeznek. A "http" vagy "https" beállításait a `Protocol` tulajdonságát a `Endpoint` konfigurációját a ServiceManifest.xml segítségével kifejezetten kérje meg a Service Fabric-futtatókörnyezet regisztrálni egy URL-címet a HTTP.sys az Ön nevében. Használatával hajtja végre ezt a [ *erős helyettesítő* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL-előtagot.

Foglaljon le például `http://+:80` egy szolgáltatáshoz, az alábbi konfigurációt használja a ServiceManifest.xml:

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

#### <a name="use-httpsys-with-a-static-port"></a>A HTTP.sys használata a statikus port
A HTTP.sys statikus port használatára, adja meg a port számát, a `Endpoint` konfiguráció:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>A HTTP.sys használata a dinamikus port
A HTTP.sys egy dinamikusan hozzárendelt port használatára, hagyja ki ezt a `Port` tulajdonságot a `Endpoint` konfiguráció:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Foglalva dinamikus portot egy `Endpoint` a konfiguráció csak egyetlen portot biztosít *gazdagép folyamatonként*. Az aktuális Service Fabric üzemeltetési modell lehetővé teszi, hogy több szolgáltatáspéldányok és/vagy replikák ugyanabban a folyamatban lehet üzemeltetni. Ez azt jelenti, hogy mindegyikhez meg fogja osztani ugyanazt a portot keresztül kiosztása során a `Endpoint` konfigurációja. Több **HTTP.sys** példányok megoszthat egy portot az alapul szolgáló használatával **HTTP.sys** port megosztása funkció. Nem érhető el, de `HttpSysCommunicationListener` miatt a komplikációk azt mutatja be, az ügyféli kérelmek részére. A dinamikus port használatra a Kestrel a javasolt webkiszolgáló.

## <a name="kestrel-in-reliable-services"></a>A Reliable Services kestrel
Importálja a Reliable Services Kestrel is használhatja a **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet-csomagot. Ez a csomag tartalmaz `KestrelCommunicationListener`, megvalósítását `ICommunicationListener`. `KestrelCommunicationListener` lehetővé teszi, hogy hozzon létre egy ASP.NET Core Webhostot belül egy reliable Services Kestrel használják, mint a webkiszolgálón.

A kestrel, az ASP.NET Core platformfüggetlen webkiszolgáló libuv, a platformok közötti aszinkron i/o-tár alapján. A HTTP.sys, ellentétben a Kestrel egy központosított endpoint-kezelő nem használ. Is HTTP.sys, ellentétben a Kestrel nem támogatja a-port megosztása több folyamat között. A Kestrel minden példánya egy egyedi portot kell használnia.

![A kestrel diagramja][4]

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

Egy `Endpoint` -konfiguráció nevét *nem* megadott `KestrelCommunicationListener` egy állapotalapú service-ben. Ennek a magyarázatát részletesebben a következő szakaszban.

### <a name="configure-kestrel-to-use-https"></a>A Kestrel konfigurálása HTTPS használatára
A szolgáltatás a Kestrel HTTPS engedélyezésekor szüksége több figyelési beállításainak megadása. Frissítés a `ServiceInstanceListener` használata egy *Végponthttps* végpont és a figyelő egy adott portot (például a 443-as porton). A webes állomást, hogy a Kestrel webkiszolgáló konfigurálásakor konfigurálnia kell az összes hálózati adapter IPv6-címek figyelésére a Kestrel: 

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

Az oktatóanyag egy teljes példa: [Kestrel konfigurálása HTTPS használatára](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Végpont-konfiguráció
Egy `Endpoint` konfigurációs Kestrel használatához nem szükséges. 

A kestrel egy egyszerű különálló webkiszolgálót. Ellentétben a HTTP.sys (vagy HttpListener), nincs szüksége egy `Endpoint` konfigurációját a ServiceManifest.xml mert megkezdése előtt regisztrációs URL-cím nincs szükség. 

#### <a name="use-kestrel-with-a-static-port"></a>A Kestrel használata a statikus port
Konfigurálhatja a statikus portot a `Endpoint` ServiceManifest.xml konfigurációját a Kestrel való használatra. Bár ez nem feltétlenül szükséges, két lehetséges előnyt kínál:
 - Ha a port nem esik az alkalmazás porttartományából, nyitja meg, az operációs rendszer tűzfalon keresztül a Service Fabric.
 - Az URL-cím keresztül biztosított `KestrelCommunicationListener` ezt a portot fogja használni.

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

Ha nem használja a ServiceManifest.xml egy `Endpoint` konfigurációs, hagyja ki a nevét a `KestrelCommunicationListener` konstruktor. Ebben az esetben dinamikus portot fogja használni. További információ a következő szakaszban talál.

#### <a name="use-kestrel-with-a-dynamic-port"></a>A Kestrel használata a dinamikus port
A kestrel munkafeladatait automatikus port nem használható a `Endpoint` konfigurációját a ServiceManifest.xml. Ez azért, az automatikus hozzárendelés a port egy `Endpoint` konfigurációs rendeli hozzá egy egyedi portját *folyamaton*, és a egy egyetlen gazdafolyamat Kestrel több példány is tartalmazhat. A Kestrel ez nem dolgozhat, mert nem támogatja a-port megosztása. Ezért a Kestrel mindegyiknek egy egyedi portot kell megnyitni.

A Kestrel dinamikus porthozzárendelés használni, hagyja ki ezt a `Endpoint` konfigurációját a ServiceManifest.xml teljesen, és a végpont neve nem adja át a `KestrelCommunicationListener` konstruktor, az alábbiak szerint:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Ebben a konfigurációban `KestrelCommunicationListener` automatikusan választja ki egy nem használt portot az alkalmazás porttartományából.

## <a name="service-fabric-configuration-provider"></a>A Service Fabric konfigurációszolgáltató
Alkalmazások konfigurálása az ASP.NET Core a konfigurációszolgáltató által létrehozott kulcs-érték párok alapján. Olvasási [konfiguráció az ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) további on általános ASP.NET Core konfigurációs támogatási megértéséhez.

Ez a szakasz ismerteti a Service Fabric-konfigurációszolgáltató együttműködéséről az ASP.NET Core konfigurációs importálásával a `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-csomagot.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration indítási bővítmények
Miután importálta a `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-csomagjára, regisztrálnia kell a Service Fabric konfigurálása forrás API ASP.NET Core-konfigurációval. Ezt az ellenőrzést **AddServiceFabricConfiguration** bővítményei a `Microsoft.ServiceFabric.AspNetCore.Configuration` elleni névtér `IConfigurationBuilder`.

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
### <a name="default-key-mapping"></a>Alapértelmezett kulcsok leképezés
Alapértelmezés szerint a Service Fabric-konfigurációszolgáltató tartalmaz, a csomag nevét, a szakasz nevét és a tulajdonság neve. Együtt ezek alkotják az ASP.NET Core konfigurációs kulcsot a következő:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Ha például van egy nevű csomagot `MyConfigPackage` az alábbi tartalommal, majd a konfigurációs érték elérhető lesz az ASP.NET Core `IConfiguration` keresztül *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>A Service Fabric-konfigurációs beállítások
A Service Fabric-konfigurációszolgáltató is támogatja a `ServiceFabricConfigurationOptions` módosíthatja a kulcs leképezés alapértelmezett viselkedését.

#### <a name="encrypted-settings"></a>Titkosított beállításai
A Service Fabric támogatja a titkosított beállításait, mint a Service Fabric konfigurációszolgáltatót. A titkosított beállításai nem visszafejteni az ASP.NET Core `IConfiguration` alapértelmezés szerint. A titkosított értékek ott tárolt helyette. De ha visszafejteni az értéket, az ASP.NET Core IConfiguration tárolni szeretné, beállíthatja a *DecryptValue* jelző false értékűre a `AddServiceFabricConfiguration` bővítmény, az alábbiak szerint:

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
Service Fabric támogatja a több konfigurációs csomagokat. Alapértelmezés szerint a csomag neve szerepel a konfigurációs kulcs. Beállíthatja, de a `IncludePackageName` jelző FALSE, a következő:
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
A Service Fabric-konfigurációszolgáltató is támogatja a legfontosabb leképezésénél testreszabása speciális forgatókönyvek `ExtractKeyFunc` és egyéni kinyerni az értékek `ExtractValueFunc`. A teljes folyamat az ASP.NET Core-konfiguráció a Service Fabric konfigurációs adatainak feltöltése használatával módosíthatja `ConfigAction`.

Az alábbi példák bemutatják, hogyan használható `ConfigAction` adatokkal való feltöltés testreszabása:
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

### <a name="configuration-updates"></a>Konfiguráció frissítéseit
A Service Fabric-konfigurációszolgáltató konfigurációfrissítések is támogatja. Használhatja az ASP.NET Core `IOptionsMonitor` a változási értesítéseket fogadni, és hogyan `IOptionsSnapshot` frissítse a konfigurációs adatokat. További információkért lásd: [ASP.NET Core-beállítások](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Ezek a beállítások alapértelmezés szerint támogatott. Kódolás további konfigurációs frissítések engedélyezéséhez szükséges.

## <a name="scenarios-and-configurations"></a>A forgatókönyvek és konfigurációk
Ez a szakasz tartalmazza a webalkalmazás-kiszolgáló, port konfigurációja, a Service Fabric-integráció beállításai és hibaelhárítás a következő esetekben javasoljuk, hogy egyéb beállítások kombinációja:
 - Kívülről elérhetővé tett ASP.NET Core állapotmentes szolgáltatások
 - Csak belső ASP.NET Core állapotmentes szolgáltatások
 - Csak belső ASP.NET Core állapotalapú szolgáltatások

Egy **kívülről elérhetővé tett szolgáltatás** , amelyik tesz elérhetővé egy végpontot, amely általában a terheléselosztón keresztül, a fürtön kívülről meghívni.

Egy **csak belső** szolgáltatás az egyik amelynek végpont csak meghívása a fürtön belül.

> [!NOTE]
> Állapot-nyilvántartó Szolgáltatásvégpontok általánosan nem közzétéve az interneten. Fürtök mögött, amelyek nem tudnak a Service Fabric service megoldás, például az Azure Load Balancer terheléselosztók nem tudja az állapotalapú szolgáltatások. Ennek oka az, a terheléselosztó nem tudják megkeresni és irányíthatja a forgalmat a megfelelő állapot-nyilvántartó szolgáltatási replikára. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Kívülről elérhetővé tett ASP.NET Core állapotmentes szolgáltatások
A kestrel az előtérbeli szolgáltatásokhoz, amelyek külső, az internetre irányuló HTTP-végpontokat tesznek közzé a javasolt webkiszolgáló. A Windows a HTTP.sys port megosztási képesség, amely lehetővé teszi ugyanazokat a csomópontok ugyanazt a portot használja a webes szolgáltatásokat biztosít. Ebben a forgatókönyvben a webes szolgáltatások különbözteti meg a gazdagép neve vagy elérési útja, mindehhez nincs szüksége egy előtér-proxy vagy az átjáró a HTTP-útválasztást.
 
Amikor az interneten vannak kitéve, állapotmentes szolgáltatás használja egy jól ismert és állandó végpontot, amely a terheléselosztón keresztül érhető el. Az URL-címet az alkalmazás felhasználók képzésben. Azt javasoljuk, hogy a következő konfigurációt:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Kestrel | A kestrel az előnyben részesített webkiszolgáló, mert különböző Windows és Linux támogatott. |
| Port konfigurálása | Statikus | A jól ismert statikus port lehet beállítani a `Endpoints` ServiceManifest.xml, például a 80-as HTTP vagy HTTPS-hez a 443-as konfigurációját. |
| ServiceFabricIntegrationOptions | None | Használja a `ServiceFabricIntegrationOptions.None` lehetőséget a Service Fabric-integráció közbenső szoftverek, konfigurálásakor, hogy a szolgáltatás nem próbálja meg egy egyedi azonosítót a bejövő kérelmeket. Az alkalmazás külső felhasználók nem fognak ismerni az egyedi azonosító adatokat, amely az közbenső szoftvert használ. |
| Példányszám | -1 | A tipikus használati esetek, a példányok száma beállítást kell megadni *-1*. Ez történik, hogy az egy példány érhető el az összes csomóponton, hogy a forgalom fogadására a terheléselosztótól. |

Ha több kívülről elérhetővé tett szolgáltatások ugyanazokat a csomópontok, a HTTP.sys egy egyedi, de stabil URL-cím használható. Ez a IWebHost konfigurálásakor megadott URL-Címének módosításával végezheti el. Vegye figyelembe, hogy ez vonatkozik a HTTP.sys csak.

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
Állapotmentes szolgáltatások, amelyek a fürtön belül csak meghívni a egyedi URL-címeket kell használnia, és dinamikusan kiosztott porttartományt, hogy több szolgáltatások közötti együttműködést. Azt javasoljuk, hogy a következő konfigurációt:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Kestrel | Bár a HTTP.sys belső állapotmentes szolgáltatások esetében, a Kestrel az a legjobb kiszolgáló, hogy egy gazdagép megosztani több szolgáltatáspéldány.  |
| Port konfigurálása | dinamikusan kiosztott | Állapotalapú szolgáltatás több replika előfordulhat, hogy ossza meg egy gazdafolyamat vagy a gazda operációs rendszer, és így kell egyedi portokat. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Dinamikus portok hozzárendelés Ez a beállítás megakadályozza, hogy a korábban ismertetett garanciafeltételeknek identitás probléma. |
| InstanceCount | Bármely | A beállítás példányszám állítható bármilyen érték a szolgáltatás működtetéséhez szükséges. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Csak belső állapotalapú ASP.NET Core-szolgáltatás
Állapotalapú szolgáltatások, amelyek a fürtön belül csak meghívni a dinamikusan hozzárendelt portok segítségével biztosítja több szolgáltatások közötti együttműködést. Azt javasoljuk, hogy a következő konfigurációt:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Kestrel | A `HttpSysCommunicationListener` nem az állapotalapú szolgáltatások, amelyben a replikák megosztani egy gazdafolyamaton használatra terveztek. |
| Port konfigurálása | dinamikusan kiosztott | Állapotalapú szolgáltatás több replika előfordulhat, hogy ossza meg egy gazdafolyamat vagy a gazda operációs rendszer, és így kell egyedi portokat. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Dinamikus portok hozzárendelés Ez a beállítás megakadályozza, hogy a korábban ismertetett garanciafeltételeknek identitás probléma. |

## <a name="next-steps"></a>További lépések
[Service Fabric-alkalmazás hibakeresése a Visual Studióval](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png

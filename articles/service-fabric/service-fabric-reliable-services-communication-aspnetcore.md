---
title: Szolgáltatási kommunikáció a ASP.NET Core
description: Ismerje meg, hogyan használhatja ASP.NET Core állapot nélküli és állapotalapú Azure Service Fabric reliable services alkalmazásokban.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639632"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core az Azure Service Fabric megbízható szolgáltatásaiban

ASP.NET Core egy nyílt forráskódú és platformfüggetlen keretrendszer. Ez a keretrendszer felhőalapú, internetkapcsolattal rendelkező alkalmazások, például webalkalmazások, IoT-alkalmazások és mobil háttérrendszerek készítésére készült.

Ez a cikk egy részletes útmutató a Service Fabric reliable services ASP.NET alapvető szolgáltatásainak üzemeltetéséhez a **Microsoft.ServiceFabric.AspNetCore használatával.** nuget csomagok készletét.

A Service Fabric ASP.NET Core-ról szóló bevezető oktatóanyagról és a fejlesztői környezet beállítására vonatkozó utasításokról [az Oktatóanyag: Alkalmazás létrehozása és üzembe helyezése ASP.NET Core Web API előtér-szolgáltatással és egy állapotalapú háttérszolgáltatással](service-fabric-tutorial-create-dotnet-app.md)című témakörben található.

A cikk többi része feltételezi, hogy már ismeri ASP.NET Core-t. Ha nem, kérjük, olvassa el a [ASP.NET Core alapjait](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core a Service Fabric környezetben

A core és a Service Fabric alkalmazások ASP.NET is futtathatók .NET Core vagy teljes . A ASP.NET Core két különböző módon használható a Service Fabricben:
 - **Házigazdája, mint egy vendég futtatható**. Így elsősorban a meglévő ASP.NET Core alkalmazások futtatásához a Service Fabric kódmódosítások nélkül.
 - **Fuss egy megbízható szolgáltatáson belül.** Így lehetővé teszi a jobb integrációt a Service Fabric futásidejű, és lehetővé teszi az állapotalapú ASP.NET Core szolgáltatások.

A cikk további ismerteti, hogyan használhatja ASP.NET Core egy megbízható szolgáltatás, a ASP.NET Core integrációs összetevők, amelyek a Service Fabric SDK-val szállított.

## <a name="service-fabric-service-hosting"></a>Service Fabric szolgáltatás üzemeltetése

A Service Fabric egy vagy több példányai és/vagy replikái a szolgáltatás *gazdafolyamatában*futnak: a szolgáltatáskódot futtató végrehajtható fájl. Ön, mint a szolgáltatás szerzője, a szolgáltatás gazdagép-folyamat, és a Service Fabric aktiválja és figyeli azt az Ön számára.

A hagyományos ASP.NET (legfeljebb MVC 5) szorosan kapcsolódik az IIS-hez a System.Web.dll fájlon keresztül. ASP.NET Core a webkiszolgáló és a webalkalmazás közötti szétválasztást biztosít. Ez a szétválasztás lehetővé teszi, hogy a webalkalmazások hordozhatóak legyenek a különböző webkiszolgálók között. Azt is lehetővé teszi, hogy a webszerverek, hogy *saját üzemeltetett*. Ez azt jelenti, hogy saját folyamatában indíthat webkiszolgálót, szemben egy olyan folyamattal, amely dedikált webkiszolgáló-szoftverek, például az IIS tulajdonában van.

A Service Fabric-szolgáltatás és ASP.NET kombinálása, akár vendég végrehajtható, akár egy megbízható szolgáltatás, képesnek kell lennie arra, hogy ASP.NET a szolgáltatás gazdafolyamatán belül. ASP.NET Core önkiszolgáló lehetővé teszi, hogy ezt.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>A Core ASP.NET megbízható szolgáltatásban való üzemeltetése
A saját üzemeltetésű ASP.NET Core alkalmazások általában webhostot hoznak létre `static void Main()` az `Program.cs`alkalmazás belépési pontján, például a metódust. Ebben az esetben a WebHost életciklusa a folyamat életciklusához kötődik.

![A ASP.NET Core üzemeltetése egy folyamatban][0]

De az alkalmazás belépési pont nem a megfelelő hely, hogy hozzon létre egy WebHost egy megbízható szolgáltatást. Ennek az az oka, hogy az alkalmazás belépési pontja csak a Service Fabric futásidejű szolgáltatástípus regisztrálására szolgál, így az adott szolgáltatástípus példányait hozhatja létre. A WebHost kell létrehozni egy megbízható szolgáltatást is. A szolgáltatásgazda-folyamaton belül a szolgáltatáspéldányok és/vagy replikák több életcikluson is átmehetnek. 

A Megbízható szolgáltatás példányt a vagyból `StatelessService` `StatefulService`származó szolgáltatásosztály képviseli. A szolgáltatás kommunikációs vermét `ICommunicationListener` egy szolgáltatásosztály ban egy implementáció tartalmazza. A `Microsoft.ServiceFabric.AspNetCore.*` NuGet csomagok tartalmazzák `ICommunicationListener` a kestrel vagy http.sys ASP.NET core webhost indítására és kezelésére szolgáló implementációkat egy megbízható szolgáltatásban.

![Diagram ASP.NET Core megbízható szolgáltatásban való üzemeltetéséhez][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
A `ICommunicationListener` NuGet csomagokban a Kestrel és `Microsoft.ServiceFabric.AspNetCore.*` a HTTP.sys implementációi hasonló használati mintákkal rendelkeznek. De az egyes webkiszolgálókra jellemző, kissé eltérő műveleteket hajtanak végre. 

Mindkét kommunikációs figyelő konstruktort biztosít, amely a következő argumentumokat veszi:
 - **`ServiceContext serviceContext`**: Ez `ServiceContext` az objektum a futó szolgáltatással kapcsolatos információkat tartalmazza.
 - **`string endpointName`**: Ez a ServiceManifest.xml fájl ban lévő `Endpoint` konfiguráció neve. Ez elsősorban, ahol a két kommunikációs hallgatók különböznek. A HTTP.sys `Endpoint` *hitelesítést igényel,* míg a Kestrel nem.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Ez egy lambda, amelyet ön valósít `IWebHost`meg, amelyben létrehoz és visszaad egy . Ez lehetővé `IWebHost` teszi, hogy konfigurálja, ahogy általában egy ASP.NET Core alkalmazás. A lambda egy URL-címet biztosít, amely az Ön számára létrehozott, attól függően, hogy a Service Fabric integrációs beállításokat használ, és a `Endpoint` megadott konfiguráció. Ezután módosíthatja vagy használhatja ezt az URL-címet a webkiszolgáló elindításához.

## <a name="service-fabric-integration-middleware"></a>Service Fabric integrációs köztes szoftver
A `Microsoft.ServiceFabric.AspNetCore` NuGet-csomag `UseServiceFabricIntegration` tartalmazza `IWebHostBuilder` a bővítmény metódust, amely hozzáadja a Service Fabric-aware köztes szoftver. Ez a köztes szoftver konfigurálja a Kestrel vagy HTTP.sys `ICommunicationListener` regisztrálni egy egyedi szolgáltatás URL-címét a Service Fabric naming service. Ezután ellenőrzi az ügyfélkérelmeket, hogy megbizonyosodjon arról, hogy az ügyfelek a megfelelő szolgáltatáshoz csatlakoznak. 

Ez a lépés azért szükséges, hogy az ügyfelek ne csatlakoztassanak tévesen a rossz szolgáltatáshoz. Ennek az az oka, hogy egy megosztott gazdagép környezetben, például a Service Fabric, több webalkalmazások futtathatók ugyanazon a fizikai vagy virtuális gépen, de nem használ egyedi állomásneveket. Ezt a forgatókönyvet a következő szakasz részletesebben ismerteti.

### <a name="a-case-of-mistaken-identity"></a>Téves személyazonosság esete
A szolgáltatásreplikák a protokolltól függetlenül egy egyedi IP:port kombinációt figyelnek. Miután egy szolgáltatásreplika megkezdte az IP:port végpont figyelését, jelenti a végpont címét a Service Fabric naming service.Once a service replika has started listening on an IP:port endpoint, it reports that endpoint address to the Service Fabric Naming Service. Ott az ügyfelek vagy más szolgáltatások felfedezhetik azt. Ha a szolgáltatások dinamikusan hozzárendelt alkalmazásportokat használnak, a szolgáltatásreplika véletlenül használhatja egy másik szolgáltatás ip:port végpontját, amely korábban ugyanazon a fizikai vagy virtuális gépen volt. Ez azt eredményezheti, hogy az ügyfél tévesen rossz szolgáltatáshoz csatlakozik. Ez a forgatókönyv akkor fordulhat elő, ha a következő eseménysorozat következik be:

 1. Az A szolgáltatás http-n keresztül 10.0.0.1:30000 http-n keresztül figyel. 
 2. Az ügyfél feloldja az A szolgáltatást, és megkapja a 10.0.0.1:30000 címet.
 3. Az A szolgáltatás egy másik csomópontra kerül.
 4. A B szolgáltatás a 10.0.0.1-re van elhelyezve, és véletlenül ugyanazt a 30000-es portot használja.
 5. Az ügyfél a 10.0.0.1:30000 gyorsítótáras címmel próbál csatlakozni az A szolgáltatáshoz.
 6. Az ügyfél most már sikeresen csatlakozik a B szolgáltatáshoz, és nem veszi észre, hogy nem a megfelelő szolgáltatáshoz csatlakozik.

Ez véletlenszerűen olyan hibákat okozhat, amelyeket nehéz lehet diagnosztizálni.

### <a name="using-unique-service-urls"></a>Egyedi szolgáltatásURL-ek használata
A hibák megelőzése érdekében a szolgáltatások egy végpontot könyvelhetnek az elnevezési szolgáltatásba egy egyedi azonosítóval, majd ellenőrizhetik az egyedi azonosítót az ügyfélkérelmek során. Ez egy együttműködési művelet a szolgáltatások között egy nem ellenséges-bérlőmegbízható környezetben. Nem biztosít biztonságos szolgáltatáshitelesítést ellenséges-bérlős környezetben.

Megbízható környezetben a `UseServiceFabricIntegration` metódus által hozzáadott köztes szoftver automatikusan hozzáfűz i egy egyedi azonosítót az elnevezési szolgáltatásnak feladott címhez. Minden kéréskor ellenőrzi ezt az azonosítót. Ha az azonosító nem egyezik, a köztes szoftver azonnal egy HTTP 410 Gone választ ad vissza.

A dinamikusan hozzárendelt portot használó szolgáltatásoknak ezt a köztes szoftvert kell használniuk.

A rögzített egyedi portot használó szolgáltatások együttműködési környezetben nem rendelkeznek ezzel a problémával. A rögzített egyedi portot általában olyan külső szolgáltatásokhoz használják, amelyekhez jól ismert portra van szükség az ügyfélalkalmazások számára. A legtöbb internetes webalkalmazás például a 80-as vagy a 443-as portot használja a webböngésző-kapcsolatokhoz. Ebben az esetben az egyedi azonosítót nem szabad engedélyezni.

Az alábbi ábra a kérelemfolyamot mutatja be, amelyen engedélyezve van a köztes szoftver:

![Service Fabric ASP.NET Core integráció][2]

Mind a Kestrel, mind `ICommunicationListener` a HTTP.sys implementációk pontosan ugyanúgy használják ezt a mechanizmust. Bár a HTTP.sys az alapul szolgáló **HTTP.sys** portmegosztási szolgáltatás használatával belsőleg megkülönböztetheti a kérelmeket az `ICommunicationListener` egyedi URL-útvonalak alapján, ezt a funkciót a HTTP.sys implementáció *nem* használja. Ennek az az oka, hogy http 503-as és HTTP 404-es hibaállapot-kódokat eredményez a korábban ismertetett forgatókönyvben. Ez viszont megnehezíti az ügyfelek számára a hiba szándékának meghatározását, mivel a HTTP 503 és a HTTP 404 protokollt gyakran használják más hibák jelzésére. 

Így mind a Kestrel, mind `ICommunicationListener` a HTTP.sys implementációk szabványosítják a köztes szoftvert, amelyet a `UseServiceFabricIntegration` kiterjesztési módszer biztosít. Ezért az ügyfeleknek csak egy szolgáltatásvégpont újbóli feloldási műveletet kell végrehajtaniuk a HTTP 410-es válaszokon.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys a megbízható szolgáltatásokban
A HTTP.sys fájl megbízható szolgáltatásokban a **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet csomag importálásával használható. Ez a `HttpSysCommunicationListener`csomag tartalmazza `ICommunicationListener`a . `HttpSysCommunicationListener`lehetővé teszi, hogy hozzon létre egy ASP.NET Core WebHost belül egy megbízható szolgáltatás segítségével HTTP.sys, mint a webszerver.

A HTTP.sys a [Windows HTTP Server API-ra](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)épül. Ez az API a **HTTP.sys** kernel-illesztőprogramot használja a HTTP-kérelmek feldolgozásához és a webalkalmazásokat futtató folyamatokhoz való továbbításához. Ez lehetővé teszi, hogy ugyanazon a fizikai vagy virtuális gépen több folyamat is üzemeltethet webalkalmazásokat ugyanazon a porton, amelyeket egy egyedi URL-útvonal vagy állomásnév nem tartalmaz. Ezek a szolgáltatások hasznosak a Service Fabric több webhely üzemeltetése ugyanabban a fürtben.

>[!NOTE]
>A HTTP.sys implementáció csak a Windows platformon működik.

Az alábbi ábra azt szemlélteti, hogy a HTTP.sys hogyan használja a **HTTP.sys** kernel-illesztőprogramot a Windows rendszeren a portmegosztáshoz:

![HTTP.sys diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys egy állapotmentes szolgáltatásban
Állapotmentes `HttpSys` szolgáltatásban való használathoz `CreateServiceInstanceListeners` felülbírálja a `HttpSysCommunicationListener` metódust, és visszaad egy példányt:

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

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys egy állapotalapú szolgáltatás

`HttpSysCommunicationListener`jelenleg nem tervezték használható állapotalapú szolgáltatások komplikációk miatt az alapul szolgáló **HTTP.sys** port megosztási funkció. További információt a HTTP.sys dinamikus portfoglalásról szóló következő szakaszban talál. Az állapotalapú szolgáltatások, Kestrel a javasolt webszerver.

### <a name="endpoint-configuration"></a>Végpont konfigurációja

A `Endpoint` Windows HTTP Server API-t használó webkiszolgálókhoz , beleértve a HTTP.sys-t is, konfigurációszükséges. A Windows HTTP Server API-t használó webkiszolgálóknak először le kell foglalniuk az URL-címüket a HTTP.sys webhelyen (ez általában a [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) eszközzel történik). 

Ez a művelet olyan emelt szintű jogosultságokat igényel, amelyekkel a szolgáltatások alapértelmezés szerint nem rendelkeznek. A ServiceManifest.xml `Protocol` `Endpoint` konfigurációjának "http" vagy "https" beállításai tetsző en kifejezetten arra szolgálnak, hogy utasítsa a Service Fabric futásidejűt egy URL-cím regisztrálására az Ön nevében a HTTP.sys fájllal. Ezt az erős [*helyettesítő*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL-előtag használatával teszi.

Ha például `http://+:80` egy szolgáltatásra szeretne foglalni, használja a servicemanifest.xml fájl következő konfigurációját:

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

És a végpont nevét át `HttpSysCommunicationListener` kell adni a konstruktornak:

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

#### <a name="use-httpsys-with-a-static-port"></a>HTTP.sys használata statikus porttal
Ha http.sys fájllal szeretne statikus portot `Endpoint` használni, adja meg a portszámát a konfigurációban:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>HTTP.sys használata dinamikus porttal
Ha dinamikusan hozzárendelt portot szeretne használni a HTTP.sys protokollal, hagyja ki a `Port` tulajdonságot a `Endpoint` konfigurációban:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

A `Endpoint` konfiguráció által lefoglalt dinamikus port *állomásfolyamatonként*csak egy portot biztosít. Az aktuális Service Fabric üzemeltetési modell lehetővé teszi, hogy több szolgáltatáspéldányok és/vagy replikák üzemeltetése ugyanabban a folyamatban. Ez azt jelenti, hogy mindegyik ugyanazt `Endpoint` a portot fogja megosztani, amikor a konfiguráción keresztül lefoglalja. Az alapul szolgáló **HTTP.sys** portmegosztási szolgáltatás használatával több **HTTP.sys** példány is megoszthatja a portokat. De ez nem támogatja `HttpSysCommunicationListener` a komplikációk általa bevezetett ügyfél kérelmek. A dinamikus porthasználat, Kestrel a javasolt webszerver.

## <a name="kestrel-in-reliable-services"></a>Kestrel a megbízható szolgáltatások
A Kestrel a Reliable Services ben a **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet csomag importálásával használható. Ez a `KestrelCommunicationListener`csomag tartalmazza `ICommunicationListener`a . `KestrelCommunicationListener`lehetővé teszi, hogy hozzon létre egy ASP.NET Core WebHost belül egy megbízható szolgáltatás segítségével Kestrel, mint a webszerver.

A Kestrel egy platformfüggetlen webszerver ASP.NET Core számára. A HTTP.sys alkalmazással ellentétben a Kestrel nem használ központosított végpontkezelőt. A HTTP.sys-től eltérően a Kestrel nem támogatja a portmegosztást több folyamat között. A Kestrel minden példányának egyedi portot kell használnia. További információk a Kestrel, lásd a [végrehajtás részletei](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Kesztezid ábra][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel egy állapotmentes szolgáltatás
Állapotmentes `Kestrel` szolgáltatásban való használathoz `CreateServiceInstanceListeners` felülbírálja a `KestrelCommunicationListener` metódust, és visszaad egy példányt:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel egy állami szolgáltatás
Ha `Kestrel` állapotalapú szolgáltatásban szeretné használni, felülbírálja a `CreateServiceReplicaListeners` metódust, és visszaad egy példányt: `KestrelCommunicationListener`

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

Ebben a példában egy singleton példány a `IReliableStateManager` WebHost függőségi injektálási tároló. Ez nem feltétlenül szükséges, de lehetővé teszi az MVC-vezérlő műveletmódszereiben való használatát `IReliableStateManager` és megbízható gyűjteményeit.

A `Endpoint` konfigurációs *not* név `KestrelCommunicationListener` nem érhető el egy állapotalapú szolgáltatásban. Ezt részletesebben a következő szakasz ismerteti.

### <a name="configure-kestrel-to-use-https"></a>A Kestrel konfigurálása HTTPS használatára
Ha engedélyezi a HTTPS protokollt a Kestrel szolgáltatásában, több figyelési beállítást kell beállítania. Frissítse `ServiceInstanceListener` a *végpontothttps* végpont használatával, és figyelje egy adott porton (például a 443-as porton). Amikor a webállomást a Kestrel webkiszolgáló használatára konfigurálja, a Kestrel-t úgy kell konfigurálnia, hogy minden hálózati csatolón figyelje az IPv6-címeket: 

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

Az oktatóanyag teljes példáját a [Kestrel konfigurálása https használatára](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)című témakörben található.


### <a name="endpoint-configuration"></a>Végpont konfigurációja
A `Endpoint` Kestrel használatához nincs szükség konfigurációra. 

Kestrel egy egyszerű önálló webszerver. A HTTP.sys(vagy HttpListener) fájltól `Endpoint` eltérően nincs szükség konfigurációra a ServiceManifest.xml fájlban, mert nem igényel URL-regisztrációt az indítás előtt. 

#### <a name="use-kestrel-with-a-static-port"></a>A Kestrel használata statikus porttal
A ServiceManifest.xml `Endpoint` fájl konfigurációjában a Kestrel-rel való használatra statikus portot konfigurálhat. Bár ez nem feltétlenül szükséges, két lehetséges előnyt kínál:
 - Ha a port nem esik az alkalmazásport tartományban, a Service Fabric az operációs rendszer tűzfalán keresztül nyílik meg.
 - Az URL-címet `KestrelCommunicationListener` megadott, hogy keresztül fogja használni ezt a portot.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Ha `Endpoint` egy van beállítva, a `KestrelCommunicationListener` nevét át kell adni a konstruktornak: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Ha a ServiceManifest.xml fájl `Endpoint` nem használ konfigurációt, `KestrelCommunicationListener` hagyja ki a nevet a konstruktorból. Ebben az esetben dinamikus portot fog használni. Erről a következő szakaszban további információt talál.

#### <a name="use-kestrel-with-a-dynamic-port"></a>A Kestrel használata dinamikus porttal
A Kestrel nem tudja használni az `Endpoint` automatikus port-hozzárendelést a ServiceManifest.xml fájl konfigurációjából. Ennek az az oka, `Endpoint` hogy egy konfigurációból származó automatikus port-hozzárendelés *állomásfolyamatonként*egyedi portot rendel hozzá, és egyetlen állomásfolyamat több Kestrel-példányt is tartalmazhat. Ez nem működik a Kestrel, mert nem támogatja a port megosztását. Ezért minden Egyes Kestrel példányt meg kell nyitni egy egyedi porton.

Ha dinamikus porthozzárendelést szeretne használni a `Endpoint` Kestrel-lel, hagyja ki teljesen a konfigurációt `KestrelCommunicationListener` a ServiceManifest.xml fájlból, és ne adja át a végpont nevét a konstruktornak az alábbiak szerint:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Ebben a `KestrelCommunicationListener` konfigurációban automatikusan kiválaszt egy nem használt portot az alkalmazásport-tartományból.

HTTPS esetén a végpontot HTTPS protokollal konfigurálva kell lennie a ServiceManifest.xml fájlban megadott port nélkül, és át kell adnia a végpont nevét a KestrelCommunicationListener konstruktornak.


## <a name="service-fabric-configuration-provider"></a>Service Fabric konfigurációs szolgáltató
Az alkalmazáskonfiguráció ASP.NET Core a konfigurációszolgáltató által létrehozott kulcs-érték párokon alapul. Olvassa el [a konfiguráció ASP.NET Core-ban,](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) hogy jobban megismerjen ASP.NET Core konfigurációs támogatásról.

Ez a szakasz azt ismerteti, hogy a Service Fabric konfigurációs szolgáltató hogyan integrálható ASP.NET Core konfigurációval a `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet csomag importálásával.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration indítási bővítményei
A `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-csomag importálása után regisztrálnia kell a Service Fabric konfigurációs forrását ASP.NET Core konfigurációs API-val. Ezt úgy teheti meg, hogy a `Microsoft.ServiceFabric.AspNetCore.Configuration` névtérben az `IConfigurationBuilder` **AddServiceFabricConfiguration** bővítményeket a.

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

Most a ASP.NET Core szolgáltatás elérheti a Service Fabric konfigurációs beállításait, mint bármely más alkalmazás beállításait. A beállítások mintázatával például erősen gépelt objektumokba töltheti be a beállításokat.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Alapértelmezett kulcsleképezés
Alapértelmezés szerint a Service Fabric konfigurációs szolgáltató tartalmazza a csomag nevét, a szakasz nevét és a tulajdonság nevét. Ezek együttesen alkotják a ASP.NET Core konfigurációs kulcsot, az alábbiak szerint:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Ha például a következő tartalommal ellátott `MyConfigPackage` konfigurációs csomaggal rendelkezik, akkor `IConfiguration` a konfigurációs érték a *MyConfigPackage:MyConfigSection:MyParameter (A)* ASP.NET Core szolgáltatásban lesz elérhető.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>A Service Fabric konfigurációs beállításai
A Service Fabric konfigurációs `ServiceFabricConfigurationOptions` szolgáltató is támogatja a kulcsleképezés alapértelmezett viselkedésének módosítását.

#### <a name="encrypted-settings"></a>Titkosított beállítások
A Service Fabric támogatja a titkosított beállításokat, csakúgy, mint a Service Fabric konfigurációs szolgáltató. A titkosított beállítások at alapértelmezés szerint `IConfiguration` nem fejti vissza ASP.NET Core.The encrypted settings aren't decrypted to ASP.NET Core by default. A titkosított értékek et a rendszer tárolja. De ha vissza szeretné fejteni a Core IConfiguration ASP.NET tárolandó értéket, beállíthatja `AddServiceFabricConfiguration` a *DecryptValue* jelzőt hamisra a bővítményben, az alábbiak szerint:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Több konfigurációs csomag
A Service Fabric több konfigurációs csomagot támogat. Alapértelmezés szerint a csomag neve szerepel a konfigurációs kulcsban. De beállíthatja `IncludePackageName` a zászló hamis, az alábbiak szerint:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Egyéni kulcsleképezés, értékkinyerés és adatsokság
A Service Fabric konfigurációs szolgáltató is támogatja a fejlettebb forgatókönyvek testre szabni a kulcs leképezése, `ExtractKeyFunc` és egyéni kibontása az `ExtractValueFunc`értékeket. Akár azt is módosíthatja a teljes folyamat feltöltési adatok Service `ConfigAction`Fabric konfiguráció ASP.NET Core konfiguráció használatával.

Az alábbi példák `ConfigAction` bemutatják, hogyan lehet az adatpopuláció testreszabását használni:
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

### <a name="configuration-updates"></a>Konfigurációs frissítések
A Service Fabric konfigurációs szolgáltató is támogatja a konfigurációs frissítéseket. A ASP.NET Core `IOptionsMonitor` használatával módosíthatja a `IOptionsSnapshot` módosítási értesítéseket, majd újratöltheti a konfigurációs adatokat. További információt [az ASP.NET Alapbeállítások című](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)témakörben talál.

Ezek a beállítások alapértelmezés szerint támogatottak. A konfigurációs frissítések engedélyezéséhez nincs szükség további kódolásra.

## <a name="scenarios-and-configurations"></a>Forgatókönyvek és konfigurációk
Ez a szakasz a webkiszolgáló, a portkonfiguráció, a Service Fabric-integrációs beállítások és a következő esetek hibáinak elhárításához javasolt egyéb beállítások kombinációját tartalmazza:
 - Külsőleg kitett ASP.NET alapvető állapotmentes szolgáltatások
 - Csak belső ASP.NET Alapvető állapotmentes szolgáltatások
 - Csak belső ASP.NET Alapvető állapotalapú szolgáltatások

A **külső legelérhetővétett szolgáltatás** olyan szolgáltatás, amely a fürtön kívülről, általában egy terheléselosztón keresztül hívott végpontot tesz elérhetővé.

A **csak belső** szintű szolgáltatás olyan szolgáltatás, amelynek végpontját csak a fürtből hívják meg.

> [!NOTE]
> Állapotalapú szolgáltatás végpontok általában nem kell kitéve az interneten. A SzolgáltatásFabric szolgáltatásfelbontásáról nem tudó terheléselosztók mögött imázsos szolgáltatásokat nem tudnak elérhetővé tenni. Ennek az az oka, hogy a terheléselosztó nem fogja tudni megtalálni és a forgalmat a megfelelő állapotalapú szolgáltatás replikájára irányítani. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Külsőleg kitett ASP.NET alapvető állapotmentes szolgáltatások
A Kestrel a külső, internetre néző HTTP-végpontokat felfedő előtér-szolgáltatások javasolt webkiszolgálója. Windows rendszerben a HTTP.sys portmegosztási lehetőséget biztosít, amely lehetővé teszi, hogy ugyanazon a porton több webszolgáltatást is üzemeltetjen ugyanazon a csomóponton. Ebben a forgatókönyvben a webszolgáltatások állomásnév vagy elérési út szerint vannak megkülönböztetve anélkül, hogy előtér-proxyra vagy átjáróra támaszkodnának a HTTP-útválasztás biztosításához.
 
Az internetnek kitéve egy állapotmentes szolgáltatás nak egy jól ismert és stabil végpontot kell használnia, amely egy terheléselosztón keresztül érhető el. Ezt az URL-címet meg fogja adni az alkalmazás felhasználóinak. A következő konfigurációt javasoljuk:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Vércse | A Kestrel az előnyben részesített webszerver, mivel windows os és Linux rendszeren támogatott. |
| Port konfigurációja | Statikus | Egy jól ismert statikus portot `Endpoints` kell konfigurálni a ServiceManifest.xml konfigurációjában, például 80 HTTP-hez vagy 443 HTTPS-hez. |
| ServiceFabricIntegrationOptions | None | Használja `ServiceFabricIntegrationOptions.None` ezt a beállítást a Service Fabric integrációs köztes szoftver konfigurálásakor, hogy a szolgáltatás ne kísérelje meg az egyedi azonosító bejövő kérelmeinek érvényesítését. Az alkalmazás külső felhasználói nem ismerik a köztes szoftver által használt egyedi azonosító adatokat. |
| Példányszám | -1 | Tipikus használati esetekben a példányszám-beállítást *-1-re*kell állítani. Ez úgy történik, hogy egy példány elérhető minden csomóponton, amely fogadásra forgalmat egy terheléselosztó. |

Ha több külső elérhető szolgáltatás ugyanazt a csomópontkészletet használja, a HTTP.sys egy egyedi, de stabil URL-elérési úttal használható. Ezt az IWebHost konfigurálásakor megadott URL módosításával teheti meg. Ne feledje, hogy ez csak a HTTP.sys protokollra vonatkozik.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Csak belső állapot nélküli ASP.NET Core szolgáltatás
A csak a fürtön belül meghívott állapotmentes szolgáltatásoknak egyedi URL-címeket és dinamikusan hozzárendelt portokat kell használniuk a több szolgáltatás közötti együttműködés biztosításához. A következő konfigurációt javasoljuk:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Vércse | Bár a HTTP.sys belső állapotmentes szolgáltatásokhoz is használható, a Kestrel a legjobb kiszolgáló, amely lehetővé teszi, hogy több szolgáltatáspéldány osszon meg egy állomást.  |
| Port konfigurációja | dinamikusan hozzárendelve | Egy állapotalapú szolgáltatás több replikája megoszthatja a gazdafolyamatot vagy a gazdaoperációs rendszert, ezért egyedi portokra lesz szüksége. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | A dinamikus porthozzárendeléssel ez a beállítás megakadályozza a korábban ismertetett téves azonosítási problémát. |
| InstanceCount (Példányszám) | bármely | A példányszám beállítása a szolgáltatás működtetéséhez szükséges bármely értékre beállítható. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Csak belső állapotalapú ASP.NET Core szolgáltatás
Azoknak az állapotalapú szolgáltatásoknak, amelyeket csak a fürtön belül hívnak meg, dinamikusan hozzárendelt portokat kell használniuk a több szolgáltatás közötti együttműködés biztosításához. A következő konfigurációt javasoljuk:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Vércse | A `HttpSysCommunicationListener` nem állapotalapú szolgáltatások, amelyek replikák osztoznak a gazdagép folyamat. |
| Port konfigurációja | dinamikusan hozzárendelve | Egy állapotalapú szolgáltatás több replikája megoszthatja a gazdafolyamatot vagy a gazdaoperációs rendszert, ezért egyedi portokra lesz szüksége. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | A dinamikus porthozzárendeléssel ez a beállítás megakadályozza a korábban ismertetett téves azonosítási problémát. |

## <a name="next-steps"></a>További lépések
[Service Fabric-alkalmazás hibakeresése a Visual Studióval](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png

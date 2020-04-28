---
title: Szolgáltatásokkal folytatott kommunikáció a ASP.NET Core
description: Megtudhatja, hogyan használhatja a ASP.NET Coret állapot nélküli és állapot-nyilvántartó Azure-Service Fabric Reliable Services-alkalmazásokban.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75639632"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core az Azure Service Fabric Reliable Services

A ASP.NET Core egy nyílt forráskódú és platformfüggetlen keretrendszer. Ez a keretrendszer felhőalapú, internetkapcsolattal rendelkező alkalmazások, például webalkalmazások, IoT-alkalmazások és mobil-háttérrendszer létrehozására szolgál.

Ez a cikk részletesen ismerteti, hogy a **Microsoft. ServiceFabric. AspNetCore** segítségével hogyan ASP.net Core üzemeltetheti a Service Fabric Reliable Services-szolgáltatásait. NuGet-csomagok készlete.

Az Service Fabric és a fejlesztői környezet beszerzésével kapcsolatos útmutatásért lásd [: oktatóanyag: alkalmazás létrehozása és üzembe helyezése egy ASP.net Core webes API előtér-szolgáltatással és egy állapot-visszaállítási szolgáltatással](service-fabric-tutorial-create-dotnet-app.md). asp.net Core

A cikk további része feltételezi, hogy már ismeri a ASP.NET Core. Ha nem, olvassa el a [ASP.net Core alapjaival](https://docs.microsoft.com/aspnet/core/fundamentals/index)kapcsolatos tudnivalókat.

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core a Service Fabric környezetben

A ASP.NET Core és Service Fabric alkalmazások is futtathatók a .NET Core-ban vagy a teljes .NET-keretrendszerben. A ASP.NET Core a Service Fabric két különböző módon használható:
 - **Vendég végrehajtható fájlként futtatva**. Így elsősorban a meglévő ASP.NET Core-alkalmazások futtatására használatos Service Fabric a kód módosítása nélkül.
 - **Megbízható szolgáltatáson belül fut**. Így jobb integrációt tesz lehetővé a Service Fabric futtatókörnyezettel, és lehetővé teszi az állapot-nyilvántartó ASP.NET Core szolgáltatások használatát.

A cikk további része azt ismerteti, hogyan használható a ASP.NET Core egy megbízható szolgáltatáson belül az Service Fabric SDK-val szállított ASP.NET Core integrációs összetevőkkel.

## <a name="service-fabric-service-hosting"></a>Service Fabric szolgáltatás üzemeltetése

Service Fabric a szolgáltatás egy vagy több példánya és/vagy replikája egy *Service Host-folyamaton*fut: egy végrehajtható fájl, amely a szolgáltatási kódot futtatja. Ön, mint szolgáltatás szerzője, a szolgáltatás gazdagépének tulajdonosa, és Service Fabric aktiválja és figyeli.

A hagyományos ASP.NET (az MVC 5-ig) szorosan kapcsolódik az IIS-hez a System. Web. dll fájlon keresztül. ASP.NET Core a webkiszolgáló és a webalkalmazás közötti elkülönítést biztosít. Ez a szétválasztás lehetővé teszi, hogy a webalkalmazások a különböző webkiszolgálók között legyenek hordozhatóek. Azt is lehetővé teszi, hogy a webkiszolgálók *önállóan üzemeltetve*legyenek. Ez azt jelenti, hogy elindíthat egy webkiszolgálót a saját folyamatában, nem pedig egy dedikált webkiszolgáló szoftver (például az IIS) tulajdonában álló folyamattal szemben.

A Service Fabric szolgáltatás és a ASP.NET összevonásához vendég végrehajtható fájlként vagy megbízható szolgáltatásként el kell tudnia indítani a ASP.NET a szolgáltatás-gazdagépen belül. ASP.NET Core az önálló üzemeltetés lehetővé teszi ezt.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>ASP.NET Core üzemeltetése megbízható szolgáltatásban
A saját üzemeltetésű ASP.NET Core alkalmazások általában az alkalmazás belépési pontjaiban hoznak létre egy jogcímet, `static void Main()` például a `Program.cs`metódusban. Ebben az esetben az WebHost életciklusa a folyamat életciklusához van kötve.

![ASP.NET Core üzemeltetése folyamatban][0]

Az alkalmazás belépési pontja azonban nem a megfelelő hely ahhoz, hogy megbízható szolgáltatásként hozzon létre egy jogcímet. Ennek az az oka, hogy az alkalmazás belépési pontja csak a szolgáltatás típusának a Service Fabric futtatókörnyezettel való regisztrálására szolgál, így az adott szolgáltatástípus példányai is létrehozhatók. A webszolgáltatást egy megbízható szolgáltatásban kell létrehozni. A Service Host folyamaton belül a szolgáltatás példányai és/vagy replikái több életcikluson keresztül is eltérhetnek. 

A szolgáltatási osztály a `StatelessService` vagy `StatefulService`a szolgáltatásból származó megbízható szolgáltatási példányt képvisel. A szolgáltatás kommunikációs veremét a szolgáltatási osztály egy `ICommunicationListener` implementációja tárolja. A `Microsoft.ServiceFabric.AspNetCore.*` NuGet-csomagok olyan implementációkat `ICommunicationListener` tartalmaznak, amelyek elindítják és felügyelik az ASP.net Core webhost-t a vércse vagy a http. sys számára egy megbízható szolgáltatásban.

![A megbízható szolgáltatásban ASP.NET Core üzemeltetésének diagramja][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
A `ICommunicationListener` `Microsoft.ServiceFabric.AspNetCore.*` NuGet-csomagokban a vércse és a http. sys implementációja hasonló használati mintákat tartalmaz. Azonban az egyes webkiszolgálókon némileg eltérő műveleteket hajtanak végre. 

Mindkét kommunikációs figyelő olyan konstruktort biztosít, amely a következő argumentumokat veszi igénybe:
 - **`ServiceContext serviceContext`**: Ez az az `ServiceContext` objektum, amely a futó szolgáltatással kapcsolatos információkat tartalmaz.
 - **`string endpointName`**: Ez a ServiceManifest. xml fájlban `Endpoint` található konfiguráció neve. Elsősorban a két kommunikációs figyelő különbözik. A `Endpoint` http. sys konfigurációt *igényel* , míg a vércse nem.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Ez az Ön által megvalósított lambda, amelyben létrehoz és visszaad egy `IWebHost`. Lehetővé teszi, hogy a `IWebHost` szokásos módon konfigurálja a ASP.net Core alkalmazásban. A lambda olyan URL-címet biztosít Önnek, amely az Ön által használt Service Fabric integrációs lehetőségektől és az `Endpoint` Ön által megadott konfigurációtól függ. Ezt követően módosíthatja vagy használhatja ezt az URL-címet a webkiszolgáló elindításához.

## <a name="service-fabric-integration-middleware"></a>Service Fabric Integration middleware
A `Microsoft.ServiceFabric.AspNetCore` NuGet csomag tartalmazza a `UseServiceFabricIntegration` bővítmény metódusát `IWebHostBuilder` , amely a Service Fabric-Aware köztes middleware-t adja meg. Ez a middleware úgy konfigurálja a vércse vagy a HTTP `ICommunicationListener` . sys-t, hogy egy egyedi szolgáltatás URL-címét regisztrálja a Service Fabric elnevezési szolgáltatás. Ezután ellenőrzi, hogy az ügyfelek csatlakoznak-e a megfelelő szolgáltatáshoz. 

Ez a lépés azért szükséges, hogy megakadályozza, hogy az ügyfelek véletlenül csatlakozzanak a helytelen szolgáltatáshoz. Ennek oka, hogy egy megosztott gazdagépi környezetben, például a Service Fabric-ben több webalkalmazás futhat ugyanazon a fizikai vagy virtuális gépen, de nem használhat egyedi állomásnevet. Ezt a forgatókönyvet részletesebben a következő szakaszban ismertetjük.

### <a name="a-case-of-mistaken-identity"></a>A téves identitás esete
A szolgáltatás replikái – a protokolltól függetlenül – figyeljen egy egyedi IP-címet: Port kombinációt. Miután egy szolgáltatási replika megkezdte a figyelést egy IP: Port végponton, a Service Fabric elnevezési szolgáltatás a végponti címet jelenti. Ott az ügyfelek vagy más szolgáltatások is felfedezhetik. Ha a szolgáltatások dinamikusan hozzárendelt alkalmazás-portokat használnak, akkor a szolgáltatás replikái ugyanazon a fizikai vagy virtuális gépen lévő másik szolgáltatás ugyanazon IP: Port végpontját használhatják. Ez azt eredményezheti, hogy az ügyfél véletlenül nem tud kapcsolódni a helytelen szolgáltatáshoz. Ez a forgatókönyv a következő eseménysorozat esetén eredményezhet:

 1. A szolgáltatás figyeli a 10.0.0.1:30000-et HTTP-n keresztül. 
 2. A-ügyfél feloldja az A szolgáltatást, és lekéri A 10.0.0.1:30000-es címeket.
 3. Az A szolgáltatás egy másik csomópontra lép.
 4. A B szolgáltatás a 10.0.0.1-re van helyezve, és egyidejűleg ugyanazt a 30000-es portot használja.
 5. Az ügyfél megpróbál csatlakozni az A szolgáltatáshoz a gyorsítótárazott 10.0.0.1:30000.
 6. Az ügyfél sikeresen csatlakozott a B szolgáltatáshoz, nem pedig a nem megfelelő szolgáltatáshoz csatlakozik.

Ez véletlenszerű időpontokban is okozhat hibákat, amelyeket nehéz lehet diagnosztizálni.

### <a name="using-unique-service-urls"></a>Egyedi szolgáltatás URL-címeinek használata
A hibák megelőzése érdekében a szolgáltatások egy egyedi azonosítóval küldhetnek végpontot a elnevezési szolgáltatásra, majd az ügyfél kérésére ellenőrizhetik az egyedi azonosítót. Ez egy együttműködési művelet a szolgáltatások között egy nem ellenséges – bérlői megbízható környezetben. Nem biztosít biztonságos szolgáltatás-hitelesítést egy ellenséges bérlői környezetben.

Megbízható környezetben a `UseServiceFabricIntegration` metódus által hozzáadott middleware automatikusan hozzáfűz egy egyedi azonosítót a elnevezési szolgáltatás közzétett címnek. Ellenőrzi, hogy az azonosító minden kérelemre érvényes-e. Ha az azonosító nem egyezik, a middleware azonnal visszaadja a HTTP 410-es válaszát.

A dinamikusan hozzárendelt portot használó szolgáltatásoknak ezt a middleware-t kell használniuk.

A rögzített egyedi portot használó szolgáltatások nem rendelkeznek ezzel a problémával egy együttműködési környezetben. A kifelé irányuló olyan szolgáltatások esetében, amelyeknek jól ismert portra van szükségük ahhoz, hogy az ügyfélalkalmazások csatlakozni tudjanak, egy rögzített egyedi portot használnak. Például a legtöbb internetre irányuló webalkalmazás a 80-as vagy a 443-es portot fogja használni a webböngésző kapcsolataihoz. Ebben az esetben az egyedi azonosítót nem szabad engedélyezni.

Az alábbi ábrán a kérelmek folyamata látható a middleware-mel engedélyezve:

![Service Fabric ASP.NET Core integráció][2]

A vércse és a HTTP. `ICommunicationListener` sys implementációja pontosan ugyanúgy használja ezt a mechanizmust. Bár a HTTP. sys belső módon megkülönböztetheti a kérelmeket az egyedi URL-elérési utak alapján, a mögöttes **http. sys** port megosztási funkciójával, a http. sys `ICommunicationListener` implementáció *nem* használja ezt a funkciót. Ennek oka az, hogy a HTTP 503 és a HTTP 404 hibakódokat a korábban ismertetett forgatókönyvben eredményezi. Ezzel megnehezíti az ügyfelek számára a hiba szándékának meghatározását, mivel a HTTP 503 és a HTTP 404 általában más hibák jelzésére szolgál. 

Így a vércse és a HTTP. sys `ICommunicationListener` implementációk a `UseServiceFabricIntegration` kiterjesztési módszer által biztosított middleware-ben is szabványosítva vannak. Ezért az ügyfeleknek csak egy szolgáltatási végpontot kell végrehajtaniuk a HTTP 410-válaszokon.

## <a name="httpsys-in-reliable-services"></a>HTTP. sys a Reliable Servicesban
A HTTP. sys Reliable Services a **Microsoft. ServiceFabric. AspNetCore. httpsys kiszolgálón** NuGet csomag importálásával is használható. Ez a csomag `HttpSysCommunicationListener`a következő implementációját `ICommunicationListener`tartalmazza:. `HttpSysCommunicationListener`lehetővé teszi, hogy a HTTP. sys használatával webkiszolgálóként hozzon létre egy ASP.NET Coret a megbízható szolgáltatáson belül.

A HTTP. sys a [Windows HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)-ra épül. Ez az API a **http. sys** kernel-illesztőprogram használatával dolgozza fel a http-kérelmeket, és továbbítja azokat a webalkalmazásokat futtató folyamatoknak. Ez lehetővé teszi, hogy ugyanazon a fizikai vagy virtuális gépen több folyamat is üzemelteti a webalkalmazásokat ugyanazon a porton, disambiguated egyedi URL-cím vagy állomásnév alapján. Ezek a funkciók hasznosak lehetnek Service Fabric több webhely üzemeltetéséhez ugyanabban a fürtben.

>[!NOTE]
>A HTTP. sys implementációja csak a Windows platformon működik.

A következő ábra azt szemlélteti, hogyan használja a HTTP. sys a **http. sys** kernel-illesztőprogramot a Windowsban a portok megosztásához:

![HTTP. sys diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP. sys egy állapot nélküli szolgáltatásban
Állapot nélküli `HttpSys` szolgáltatásban való használathoz bírálja felül `CreateServiceInstanceListeners` a metódust, `HttpSysCommunicationListener` és adja vissza a példányt:

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

### <a name="httpsys-in-a-stateful-service"></a>HTTP. sys egy állapot-nyilvántartó szolgáltatásban

`HttpSysCommunicationListener`jelenleg nem használható állapot-nyilvántartó szolgáltatásokban a mögöttes **http. sys** port megosztási funkciójával kapcsolatos szövődmények miatt. További információkért lásd a dinamikus portok kiosztása a HTTP. sys-sel című szakaszt. Az állapot-nyilvántartó szolgáltatások esetében a vércse a javasolt webkiszolgáló.

### <a name="endpoint-configuration"></a>Végpont konfigurációja

`Endpoint` Konfiguráció szükséges a Windows HTTP Server API-t használó webkiszolgálók számára, beleértve a http. sys-t is. A Windows HTTP Server API-t használó webkiszolgálóknak először le kell foglalniuk az URL-címet a HTTP. sys használatával (ezt általában a [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) eszközzel lehet elérni). 

Ehhez a művelethez olyan emelt szintű jogosultságok szükségesek, amelyeket a szolgáltatásai alapértelmezés szerint nem rendelkeznek. A ServiceManifest. xml fájlban található `Protocol` `Endpoint` konfiguráció tulajdonságának "http" vagy "https" beállításai kifejezetten arra szolgálnak, hogy a Service Fabric futtatókörnyezetet az Ön nevében regisztrálják a http. sys-ben. Ezt az [*erős helyettesítő karakteres*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL-előtag használatával végezheti el.

Egy szolgáltatás foglalásához `http://+:80` például használja a következő konfigurációt a ServiceManifest. xml fájlban:

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

És a végpont nevét át kell adni a `HttpSysCommunicationListener` konstruktornak:

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

#### <a name="use-httpsys-with-a-static-port"></a>HTTP. sys használata statikus porttal
Ha a HTTP. sys használatával statikus portot szeretne használni, adja meg a portszámot `Endpoint` a konfigurációban:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>A HTTP. sys használata dinamikus porttal
Ha dinamikusan hozzárendelt portot szeretne használni a HTTP. sys használatával, `Port` hagyja ki a `Endpoint` tulajdonságot a konfigurációban:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Egy `Endpoint` konfiguráció által lefoglalt dinamikus port csak egy portot biztosít a *gazdagépek*számára. A jelenlegi Service Fabric üzemeltetési modell lehetővé teszi, hogy több szolgáltatás példánya és/vagy replikája ugyanabban a folyamatban legyen tárolva. Ez azt jelenti, hogy mindegyik ugyanazt a portot fogja megosztani, amikor `Endpoint` a konfiguráción keresztül lefoglalja őket. Több **http. sys** -példány is megoszthat egy portot a mögöttes **http. sys** port megosztási szolgáltatás használatával. Ez azonban nem támogatott az ügyfélalkalmazások `HttpSysCommunicationListener` által bevezetett szövődmények miatt. A dinamikus port használata esetén a vércse a javasolt webkiszolgáló.

## <a name="kestrel-in-reliable-services"></a>Vércse Reliable Services
A Reliable Services a a **Microsoft. ServiceFabric. AspNetCore. vércse** NuGet-csomag importálásával használhatja. Ez a csomag `KestrelCommunicationListener`a következő implementációját `ICommunicationListener`tartalmazza:. `KestrelCommunicationListener`lehetővé teszi, hogy egy ASP.NET Core webkiszolgálót hozzon létre egy megbízható szolgáltatáson belül a vércse használatával webkiszolgálóként.

A vércse egy platformfüggetlen webkiszolgáló a ASP.NET Corehoz. A HTTP. sys-től eltérően a vércse nem használ központosított Endpoint Managert. A HTTP. sys-vel ellentétben a vércse nem támogatja a portok több folyamat közötti megosztását. A vércse minden példányának egyedi portot kell használnia. A vércse szolgáltatással kapcsolatos további információkért tekintse meg a [megvalósítás részleteit](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Vércse-diagram][4]

### <a name="kestrel-in-a-stateless-service"></a>A vércse egy állapot nélküli szolgáltatásban
Állapot nélküli `Kestrel` szolgáltatásban való használathoz bírálja felül `CreateServiceInstanceListeners` a metódust, `KestrelCommunicationListener` és adja vissza a példányt:

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

### <a name="kestrel-in-a-stateful-service"></a>A vércse egy állapot-nyilvántartó szolgáltatásban
Ha állapot `Kestrel` -nyilvántartó szolgáltatásban szeretné használni, bírálja felül a `CreateServiceReplicaListeners` metódust, és adja vissza a `KestrelCommunicationListener` példányt:

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

Ebben a példában egy egyedi példányt `IReliableStateManager` adnak meg a webhost függőségi injektáló tárolóhoz. Ez nem feltétlenül szükséges, de lehetővé teszi a és a `IReliableStateManager` megbízható gyűjtemények használatát az MVC-vezérlő műveleti módszereiben.

Az `Endpoint` állapot `KestrelCommunicationListener` -nyilvántartó szolgáltatás *nem* biztosít konfigurációs nevet. Ezt részletesebben a következő szakasz ismerteti.

### <a name="configure-kestrel-to-use-https"></a>A Kestrel konfigurálása HTTPS használatára
Ha a szolgáltatásban a HTTPS-t a vércse használatával engedélyezi, több figyelési beállítást is be kell állítania. Frissítse a `ServiceInstanceListener` t *végponthttps* -végpont használatára, és figyelje egy adott portot (például 443-es port). Ha úgy konfigurálja a webállomást, hogy a vércse webkiszolgálót használja, a vércse-t úgy kell konfigurálnia, hogy figyelje az IPv6-címeket az összes hálózati adapteren: 

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

Az oktatóanyagban megjelenő teljes példa: a [vércse konfigurálása HTTPS használatára](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Végpont konfigurációja
A `Endpoint` vércse használatához nincs szükség konfigurációra. 

A vércse egy egyszerű, különálló webkiszolgáló. A HTTP. sys (vagy a HttpListener) szolgáltatástól eltérően nem `Endpoint` szükséges konfigurálni a ServiceManifest. xml fájlt, mert a Kezdés előtt nem szükséges az URL-regisztráció. 

#### <a name="use-kestrel-with-a-static-port"></a>A vércse használata statikus porttal
A ServiceManifest. XML `Endpoint` konfigurációjában statikus portot is beállíthat a vércse használatával való használatra. Bár ez nem feltétlenül szükséges, a két lehetséges előnyt kínál:
 - Ha a port nem az alkalmazás portszáma alá esik, a Service Fabric az operációs rendszer tűzfalán keresztül nyitja meg.
 - Az Ön számára megadott URL- `KestrelCommunicationListener` cím ezt a portot fogja használni.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Ha be `Endpoint` van állítva, a nevet át kell adni a `KestrelCommunicationListener` konstruktornak: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Ha a ServiceManifest. xml fájl nem `Endpoint` használ konfigurációt, hagyja ki a `KestrelCommunicationListener` nevet a konstruktorban. Ebben az esetben dinamikus portot fog használni. Erről további információt a következő szakaszban talál.

#### <a name="use-kestrel-with-a-dynamic-port"></a>A vércse használata dinamikus porttal
A vércse nem használhatja az automatikus port-hozzárendelést a `Endpoint` ServiceManifest. xml fájlban található konfigurációból. Ennek az az oka, hogy az automatikus `Endpoint` port-hozzárendelés egy konfiguráció alapján egyedi portot rendel a *gazdagéphez*, és egyetlen gazdagép-folyamat több vércse-példányt is tartalmazhat. Ez nem működik a vércse szolgáltatással, mert nem támogatja a portok megosztását. Ezért az egyes vércse-példányokat egyedi porton kell megnyitni.

Ha a dinamikus port-hozzárendelést a vércse használatával `Endpoint` szeretné használni, hagyja ki teljesen a ServiceManifest. xml fájlban a konfigurációt, `KestrelCommunicationListener` és ne adja át a végpont nevét a konstruktornak, a következőképpen:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Ebben a konfigurációban `KestrelCommunicationListener` a automatikusan kijelöl egy nem használt portot az alkalmazás portszáma alapján.

HTTPS esetén a végpontnak a ServiceManifest. xml fájlban megadott port nélkül kell HTTPS protokollal konfigurálva lennie, és át kell adnia a végpont nevét a KestrelCommunicationListener konstruktornak.


## <a name="service-fabric-configuration-provider"></a>Service Fabric konfigurációs szolgáltató
ASP.NET Core alkalmazás-konfigurációja a konfigurációs szolgáltató által létesített kulcs-érték párokon alapul. Az általános ASP.NET Core-konfiguráció támogatásával kapcsolatos további információkért olvassa el [ASP.net Core konfigurációját](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) .

Ez a szakasz azt ismerteti, hogyan integrálható a Service Fabric konfigurációs szolgáltató ASP.NET Core- `Microsoft.ServiceFabric.AspNetCore.Configuration` konfigurációval a NuGet-csomag importálásával.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration indítási bővítmények
A `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-csomag importálása után regisztrálnia kell a Service Fabric konfigurációs forrását ASP.net Core Configuration API-val. Ezt a **AddServiceFabricConfiguration** -bővítmények a `Microsoft.ServiceFabric.AspNetCore.Configuration` névtérben való ellenőrzésével végezheti el `IConfigurationBuilder`.

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

Most a ASP.NET Core szolgáltatás elérheti a Service Fabric konfigurációs beállításait, ugyanúgy, mint bármely más Alkalmazásbeállítások. A beállítások minta használatával például a beállításokat erősen gépelt objektumokká töltheti be.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Alapértelmezett kulcs leképezése
Alapértelmezés szerint a Service Fabric konfiguráció szolgáltatója tartalmazza a csomag nevét, a szakasz nevét és a tulajdonság nevét. Ezek együtt a ASP.NET Core konfigurációs kulcsot alkotják, a következőképpen:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Ha például rendelkezik egy nevű `MyConfigPackage` konfigurációs csomaggal a következő tartalommal, akkor a konfigurációs érték ASP.net Core `IConfiguration` a *MyConfigPackage: MyConfigSection: MyParameter*használatával lesz elérhető.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric konfigurációs beállítások
A Service Fabric-konfiguráció szolgáltatója `ServiceFabricConfigurationOptions` szintén támogatja a kulcs-hozzárendelés alapértelmezett viselkedésének módosítását.

#### <a name="encrypted-settings"></a>Titkosított beállítások
Service Fabric támogatja a titkosított beállításokat, akárcsak a Service Fabric-konfigurációs szolgáltató. Alapértelmezés szerint a titkosított beállítások nem lettek `IConfiguration` visszafejtve ASP.net Corera. Ehelyett a titkosított értékeket tárolja a rendszer. Ha azonban a ASP.NET Core IConfiguration tárolni kívánt értéket kívánja visszafejteni, a *DecryptValue* jelzőt false értékre állíthatja a `AddServiceFabricConfiguration` bővítményben a következőképpen:

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
Service Fabric több konfigurációs csomagot is támogat. Alapértelmezés szerint a csomag neve szerepel a konfigurációs kulcsban. A `IncludePackageName` jelzőt azonban false (hamis) értékre állíthatja, a következőképpen:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Egyéni kulcs-hozzárendelés, érték kinyerése és adatpopuláció
A Service Fabric-konfiguráció szolgáltatója emellett fejlettebb forgatókönyveket is támogat, amelyekkel `ExtractKeyFunc` testre szabhatja a kulcs-hozzárendelést, és az értékeket kibonthatja a használatával `ExtractValueFunc`. Az adatok Service Fabric konfigurációból való feltöltésének teljes folyamatát akár ASP.NET Core konfigurációra is módosíthatja a használatával `ConfigAction`.

Az alábbi példák bemutatják, hogyan `ConfigAction` használható az adatpopulációk testreszabása:
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
A Service Fabric konfigurációs szolgáltató is támogatja a konfigurációs frissítéseket. A ASP.NET Core `IOptionsMonitor` a módosítási értesítések fogadására, majd a konfigurációs `IOptionsSnapshot` adatfrissítésre használható. További információ: [ASP.net Core beállítások](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Alapértelmezés szerint ezek a beállítások támogatottak. A konfigurációs frissítések engedélyezéséhez nincs szükség további kódolásra.

## <a name="scenarios-and-configurations"></a>Forgatókönyvek és konfigurációk
Ez a szakasz a webkiszolgáló, a port konfigurációja, a Service Fabric integrációs lehetőségek és a különböző beállítások együttes használatát ismerteti, amelyeket a következő esetekben ajánlott elhárítani:
 - Külsőleg elérhető ASP.NET Core állapot nélküli szolgáltatások
 - Csak belső ASP.NET Core állapot nélküli szolgáltatások
 - Csak belső ASP.NET Core állapot-nyilvántartó szolgáltatások

A **külsőleg kitett szolgáltatás** egy olyan végpont, amely a fürtön kívülről, általában egy terheléselosztó által meghívott végpontot tesz elérhetővé.

Egy csak **belső** szolgáltatás, amelynek végpontját csak a fürtön belül hívja meg a rendszer.

> [!NOTE]
> Az állapot-nyilvántartó szolgáltatási végpontok általában nem tehetők elérhetővé az interneten. A terheléselosztó mögötti fürtök, amelyek nem ismerik Service Fabric szolgáltatás-feloldást, például Azure Load Balancer, nem lesznek elérhető állapot-nyilvántartó szolgáltatások. Ennek oka, hogy a terheléselosztó nem fogja tudni megkeresni és irányítani a forgalmat a megfelelő állapot-nyilvántartó szolgáltatás replikájának. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Külsőleg elérhető ASP.NET Core állapot nélküli szolgáltatások
A vércse a javasolt webkiszolgáló az előtér-szolgáltatásokhoz, amelyek külső, Internetes elérésű HTTP-végpontokat tesznek elérhetővé. Windows rendszeren a HTTP. sys lehetővé teszi a portok megosztását, így több webszolgáltatást is tárolhat ugyanazon a csomópontokon ugyanazon a porton keresztül. Ebben az esetben a webszolgáltatások az állomásnév vagy az elérési út alapján különböztethetők meg, anélkül, hogy az előtér-proxyra vagy átjáróra kellene támaszkodnia a HTTP-útválasztás biztosításához.
 
Ha az internetre van kitéve, az állapot nélküli szolgáltatásnak olyan jól ismert és stabil végpontot kell használnia, amely egy terheléselosztó használatával érhető el. Ezt az URL-címet meg kell adnia az alkalmazás felhasználói számára. A következő konfigurációt javasoljuk:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Vércse | A vércse az előnyben részesített webkiszolgáló, mivel a Windows és a Linux rendszeren is támogatott. |
| Port konfigurációja | Statikus | Jól ismert statikus portot kell konfigurálni a `Endpoints` ServiceManifest. XML konfigurációjában, például: 80 for HTTP vagy 443 for HTTPS. |
| ServiceFabricIntegrationOptions | None | Használja a `ServiceFabricIntegrationOptions.None` Service Fabric Integration middleware konfigurálásának lehetőségét, hogy a szolgáltatás ne próbálja érvényesíteni a beérkező kéréseket egy egyedi azonosítóhoz. Az alkalmazás külső felhasználói nem fogják tudni, hogy a middleware milyen egyedi azonosító adatokat használ. |
| Példányszám | -1 | Tipikus használati esetekben a példányszám beállításának *-1*értékűnek kell lennie. Erre azért van szükség, hogy egy példány minden olyan csomóponton elérhető legyen, amely egy terheléselosztó által forgalmazott forgalmat fogad. |

Ha több külsőleg feltett szolgáltatás is ugyanazokat a csomópontokat használja, a HTTP. sys egyedi, de állandó URL-címmel is használható. Ezt a IWebHost konfigurálásakor megadott URL-cím módosításával végezheti el. Vegye figyelembe, hogy ez csak a HTTP. sys fájlra vonatkozik.

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
A csak a fürtön belül hívott állapot nélküli szolgáltatások egyedi URL-címeket és dinamikusan hozzárendelt portokat használnak a több szolgáltatás közötti együttműködés biztosításához. A következő konfigurációt javasoljuk:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Vércse | Habár a HTTP. sys-t a belső állapot nélküli szolgáltatásokhoz is használhatja, a vércse a legjobb kiszolgáló, amely lehetővé teszi, hogy több szolgáltatási példány ossza meg a gazdagépet.  |
| Port konfigurációja | dinamikusan hozzárendelve | Egy állapot-nyilvántartó szolgáltatás több replikája megoszthatja a gazdagép vagy a gazdagép operációs rendszerét, így egyedi portokra lesz szüksége. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | A dinamikus port hozzárendelésével ez a beállítás megakadályozza a korábban ismertetett, téves identitás problémáját. |
| InstanceCount | bármely | A példányok számának beállítása a szolgáltatás üzemeltetéséhez szükséges bármely értékre beállítható. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Csak belső állapot-nyilvántartó ASP.NET Core szolgáltatás
A csak a fürtön belül hívott állapot-nyilvántartó szolgáltatásoknak dinamikusan hozzárendelt portokat kell használniuk a több szolgáltatás közötti együttműködés biztosításához. A következő konfigurációt javasoljuk:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | Vércse | A `HttpSysCommunicationListener` nem olyan állapot-nyilvántartó szolgáltatások általi használatra készült, amelyekben a replikák megosztják a gazdagép folyamatát. |
| Port konfigurációja | dinamikusan hozzárendelve | Egy állapot-nyilvántartó szolgáltatás több replikája megoszthatja a gazdagép vagy a gazdagép operációs rendszerét, így egyedi portokra lesz szüksége. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | A dinamikus port hozzárendelésével ez a beállítás megakadályozza a korábban ismertetett, téves identitás problémáját. |

## <a name="next-steps"></a>További lépések
[Service Fabric-alkalmazás hibakeresése a Visual Studióval](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png

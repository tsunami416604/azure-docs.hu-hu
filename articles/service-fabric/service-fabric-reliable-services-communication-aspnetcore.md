---
title: "Az ASP.NET Core kommunikáció szolgáltatás |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az ASP.NET Core állapotmentes és állapotalapú Reliable Services."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: a98e9ad891fcfaf02ca7df5d10d5b310445c9d34
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>Az ASP.NET Core a Service Fabric megbízható szolgáltatások

Az ASP.NET Core egy új nyílt forráskódú és platformfüggetlen keretrendszer modern felhőalapú internetre kapcsolódó alkalmazások, például webalkalmazások, az IoT-alkalmazásokhoz és a mobil háttérkiszolgálókon. 

Ez a cikk egy részletes útmutató a Service Fabric Reliable Services használatával az ASP.NET Core szolgáltatásait üzemeltető a **Microsoft.ServiceFabric.AspNetCore.** * NuGet-csomagok készlete.

Egy bevezető oktatóanyag az ASP.NET Core a Service Fabric és a fejlesztési környezet beállítása történt utasításokat lásd: [egy webes előtér-, az ASP.NET Core segítségével alkalmazás felépítése](service-fabric-add-a-web-frontend.md).

Ez a cikk többi azt feltételezi, hogy ismeri az ASP.NET Core. Ha nem, azt javasoljuk olvasása a [ASP.NET Core – alapok](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Az ASP.NET Core a Service Fabric-környezetben

Az ASP.NET Core alkalmazásokat futtathat a .NET Core vagy a teljes .NET-keretrendszer, amíg a Service Fabric-szolgáltatás jelenleg csak futtathatja a teljes .NET-keretrendszer. Ez azt jelenti, hogy az ASP.NET Core Service Fabric-szolgáltatás építésekor, kell továbbra is célozhat meg a teljes .NET-keretrendszer.

Az ASP.NET Core a Service Fabric két különböző módon használható:
 - **És a Vendég végrehajtható fájlja üzemeltetett**. Ez elsősorban az ASP.NET Core meglévő alkalmazások futtatásához a Service Fabric módosítások nélküli kód.
 - **A megbízható szolgáltatás futhat**. Ez lehetővé teszi, hogy a Service Fabric-futtatókörnyezet jobb integrációt, és lehetővé teszi az állapotalapú ASP.NET Core szolgáltatások.

A többi Ez a cikk ismerteti, hogyan ASP.NET Core belül megbízható szolgáltatás az ASP.NET Core integrációs összetevők, küldje el a Service Fabric SDK-val. 

## <a name="service-fabric-service-hosting"></a>A Service Fabric-szolgáltatás üzemeltetéséhez

A Service Fabric egy vagy több példány, illetve a replikákat a szolgáltatás futtatásához egy *gazdafolyamat szolgáltatás*, egy végrehajtható fájl, amely a szolgáltatáskód hibáit futtatja. A szolgáltatás neve, a gazdagép-folyamat saját és a Service Fabric aktiválódik, és figyeli a meg.

IIS System.Web.dll keresztül szorosan csatlakoztatott hagyományos ASP.NET (max. MVC 5). Az ASP.NET Core biztosít a webkiszolgáló és a webes alkalmazás közötti kizárás. Ez lehetővé teszi, hogy a webes alkalmazások nem lesznek hordozható különböző kiszolgálók között, és is lehetővé teszi a webkiszolgálók *önállóan üzemel*, ami azt jelenti, hogy a webkiszolgáló elindíthatja a saját folyamatának figyelésekor dedikált tulajdonában lévő folyamat például az IIS Server szoftver. 

A Service Fabric-szolgáltatás és az ASP.NET, a Vendég végrehajtható fájl vagy egy megbízható szolgáltatásban egyesítéséhez ASP.NET indítását a szolgáltatás gazdafolyamat belül kell lennie. Az ASP.NET Core önálló üzemeltető ezt teszi lehetővé.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Az ASP.NET Core működnie a tároló
Általában önálló üzemeltetett ASP.NET Core alkalmazások létrehozása a Webállomás belépési pont egy alkalmazás, például a `static void Main()` metódus a `Program.cs`. Ebben az esetben a Webállomás életciklusát van kötve az életciklus a folyamat.

![Az ASP.NET Core üzemeltető folyamatban][0]

Azonban az alkalmazás belépési pont nincs a megfelelő helyen a Webállomás létrehozásához a megbízható szolgáltatás, az alkalmazás belépési pont csak használatával a Service Fabric-futtatókörnyezet, a szolgáltatás típusának regisztrálni, hogy az adott szolgáltatás típusú példányok létrehozhat. A Webállomás létre kell hozni egy megbízható szolgáltatás magát. A szolgáltatás gazdafolyamaton belül szolgáltatáspéldány és/vagy a replikák Lépkedjen végig több életciklusának. 

A megbízható példánya a származó osztály által képviselt `StatelessService` vagy `StatefulService`. A kommunikációs verem szolgáltatás szerepel egy `ICommunicationListener` végrehajtása a szolgáltatás osztályban. A `Microsoft.ServiceFabric.Services.AspNetCore.*` NuGet-csomagok tartalmaznak implementációja `ICommunicationListener` , indítsa el és kezelheti az ASP.NET Core WebHost vércse vagy egy megbízható szolgáltatás httpsys kiszolgálón.

![Az ASP.NET Core működnie a tároló][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Az ASP.NET Core ICommunicationListeners
A `ICommunicationListener` megvalósításait vércse és a httpsys kiszolgálón a `Microsoft.ServiceFabric.Services.AspNetCore.*` NuGet-csomagok hasonló felhasználási minták rendelkezik, de az adott minden webkiszolgálóhoz némileg eltérő műveletet végrehajtani. 

Mindkét kommunikációs figyelőket adja meg a következő argumentumok konstruktort:
 - **`ServiceContext serviceContext`**: A `ServiceContext` objektum, amely a futó szolgáltatás adatait tartalmazza.
 - **`string endpointName`**: a neve egy `Endpoint` ServiceManifest.xml konfiguráció. Ez a főként ha a két kommunikációs figyelőket eltérőek: httpsys kiszolgálón **szükséges** egy `Endpoint` konfigurációs, vércse azonban nem.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: egy lambda, amely hoz létre, és visszatérési megvalósító egy `IWebHost`. Ez lehetővé teszi, hogy konfigurálja `IWebHost` módja az ASP.NET Core alkalmazásban normális esetben tenné. A lambda biztosít egy URL-címnek, amely, attól függően, hogy a Service Fabric-integráció lehetőségek akkor jön létre, és a `Endpoint` konfigurációs megadnia. URL-cím majd kell módosítani vagy vettük-elindítása a webkiszolgálón.

## <a name="service-fabric-integration-middleware"></a>A Service Fabric-integráció köztes
A `Microsoft.ServiceFabric.Services.AspNetCore` NuGet-csomag magában foglalja a `UseServiceFabricIntegration` bővítmény metódusa `IWebHostBuilder` , amely a Service Fabric-kompatibilis köztes ad. A köztes megadható a vércse vagy a httpsys kiszolgálón `ICommunicationListener` regisztrálni egy egyedi URL-címe a Service Fabric-szolgáltatás majd összeveti az ügyfelek kéréseit az ügyfelek csatlakoznak a jobb szolgáltatás biztosítására. Erre akkor szükség a megosztott gazdagép-környezetben, például a Service Fabric, ahol több webalkalmazás az azonos fizikai vagy virtuális gép is futtathatók, de ne használja egyedi állomásnevek, hogy megakadályozza a felhasználókat a megfelelő szolgáltatást véletlenül csatlakozik. Ebben a forgatókönyvben a következő szakaszban részletesen ismertetett.

### <a name="a-case-of-mistaken-identity"></a>A gyorsítás esetében is téves identitás
Szolgáltatás replikákat protocol, függetlenül egyedi IP:port kombinációjából figyelése. Szolgáltatás replika megkezdése IP:port a végpont figyel, után, hogy végpontcím és jelentéseket küldeni a Service Fabric-szolgáltatás ahol, könnyen megtalálhatók legyenek az ügyfelek vagy egyéb szolgáltatásokat. Szolgáltatások alkalmazás dinamikusan hozzárendelt portok használatára, ha egy szolgáltatás replika helyezi használhat egy másik szolgáltatás, amely korábban a azonos fizikai vagy virtuális gép ugyanazon IP:port végpontja. Ennek hatására mistakely ügyfél csatlakozzon a megfelelő szolgáltatáshoz. Ez akkor fordulhat elő, ha az események az alábbi sorrendben történik:

 1. A szolgáltatás figyeli 10.0.0.1:30000 HTTP Protokollon keresztül. 
 2. Ügyfél szolgáltatás A feloldását, és lekérdezi a cím 10.0.0.1:30000
 3. A szolgáltatás áthelyezése egy másik csomópont.
 4. B szolgáltatás 10.0.0.1 helyezkedik el, és helyezi a 30000 ugyanazt a portot használja.
 5. Kapcsolódás a szolgáltatás A gyorsítótárazott cím 10.0.0.1:30000 az ügyfél megpróbálja.
 6. Ügyfél most már sikeresen csatlakozik-e a szolgáltatás nem megvalósító B csatlakozik-e a megfelelő szolgáltatást.

Ez azt okozhatja, hogy a hibák diagnosztizálása nehezen véletlenszerű időpontokban. 

### <a name="using-unique-service-urls"></a>Egyedi URL-címek használata
Ennek megelőzése érdekében szolgáltatások is végpont küldje el a szolgáltatás az egyedi azonosítóját, és ezután ellenőrizze, hogy egyedi azonosítója ügyfélkérelmek során. Ez a egy együttműködési művelet megbízható környezet nem rosszindulatú-bérlői szolgáltatások között. Ez nem biztosítja a biztonságos szolgáltatás hitelesítési rosszindulatú bérlői környezetben.

A köztes által hozzáadott megbízható környezetben a `UseServiceFabricIntegration` metódus automatikusan hozzáfűzi a cím, a szolgáltatás be, és ellenőrzi, hogy minden kérelemnél meg azonosító egyedi azonosítója. Ha az azonosító nem egyezik, akkor a köztes azonnal az egy HTTP 410 Megszűnt választ ad vissza.

A köztes felhasználása ellenőrizniük kell egy dinamikusan hozzárendelt portot használó szolgáltatások.

Egy rögzített egyedi portot használó szolgáltatások együttműködési környezetben nincs probléma. A rögzített egyedi port általában használatos külsőleg szolgáltatásaival, amelyeket egy jól ismert port ügyfél alkalmazásokhoz való csatlakozáshoz. A legtöbb Internet felé néző webes alkalmazások például használja a 80-as vagy 443-as port webes böngésző kapcsolatokhoz. Ebben az esetben az egyedi azonosító is engedélyeznie kell.

Az alábbi ábrán a áramlanak a engedélyezve köztes:

![Service Fabric ASP.NET Core integráció][2]

Vércse és a httpsys kiszolgálón `ICommunicationListener` megvalósítások használja a mechanizmus pontosan azonos módon. Bár a httpsys kiszolgálón belső megkülönböztetni azokat a kérelmeket a használatával az alapul szolgáló egyedi URL-cím elérési utak alapján *http.sys* port megosztása szolgáltatás, a funkció *nem* a httpsys kiszolgálón által használt `ICommunicationListener` végrehajtása, mert a korábban ismertetett forgatókönyvben HTTP 503-as és a HTTP 404-es hiba állapotkódok vezethet. Amely pedig rendkívül megnehezíti az ügyfelek számára az határozza meg a hiba, a célt a HTTP 503-as és a HTTP 404-es már gyakran használt egyéb hibákat. Emiatt vércse és httpsys kiszolgálón `ICommunicationListener` megvalósítások szabványosítására a a köztes által biztosított a `UseServiceFabricIntegration` kiterjesztésmetódus, hogy az ügyfelek csak kell elvégezni a szolgáltatásvégpont újra oldja meg a HTTP 410 válaszok művelet.

## <a name="httpsys-in-reliable-services"></a>A megbízható szolgáltatások httpsys kiszolgálón
Httpsys kiszolgálón használható egy megbízható szolgáltatás importálásával a **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet-csomagot. Ez a csomag tartalmaz `HttpSysCommunicationListener`, megvalósítása `ICommunicationListener`, ami lehetővé teszi az ASP.NET Core WebHost belül egy megbízható szolgáltatás létrehozásához httpsys kiszolgálón használja, mint a webkiszolgálón.

A httpsys kiszolgálón épül a [Windows HTTP-kiszolgáló API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Ez a *http.sys* a HTTP-kérelmek feldolgozásához, és a webalkalmazások futó folyamatok továbbítani az IIS által használt kernel-illesztőprogram. Ez lehetővé teszi több folyamatot, az azonos fizikai vagy virtuális gépet a webalkalmazások ugyanazt a portot, az egyedi URL-cím vagy állomásnév használatát. Ezek a funkciók hasznosak a Service Fabric ugyanazon fürt több webhely üzemeltetésére.

A következő diagram azt ábrázolja, hogyan használja a httpsys kiszolgálón a *http.sys* kernel illesztőprogramját a Windows-portmegosztás:

![a HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>Az állapotmentes szolgáltatások httpsys kiszolgálón
Használandó `HttpSys` az állapotmentes szolgáltatások, bírálja felül a `CreateServiceInstanceListeners` metódust, és térjen vissza a `HttpSysCommunicationListener` példány:

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

### <a name="httpsys-in-a-stateful-service"></a>Az állapotalapú service httpsys kiszolgálón

`HttpSysCommunicationListener`jelenleg nincs használatra szolgál az állapotalapú szolgáltatások miatt az alapul szolgáló, komplikációk *http.sys* port megosztása szolgáltatást. További információkért lásd a következő dinamikus port kiosztását a httpsys kiszolgálón. Állapotalapú szolgáltatások esetén vércse az az ajánlott webkiszolgáló.

### <a name="endpoint-configuration"></a>Végpont-konfiguráció

Egy `Endpoint` konfigurálni kell a webkiszolgálók, a Windows HTTP kiszolgáló API-t használó, beleértve a httpsys kiszolgálón. A Windows HTTP-kiszolgáló API használó webkiszolgálók először kell lefoglalni az URL-cím elé *http.sys* (Ez általában érhető el, és a [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) eszköz). Ehhez a művelethez emelt szintű jogosultságokkal, amely a szolgáltatások alapértelmezés szerint nem rendelkeznek. A "http" vagy "https" beállításait a `Protocol` tulajdonsága a `Endpoint` konfiguráció *ServiceManifest.xml* használt kifejezetten kérje meg a Service Fabric-futtatókörnyezet, az egy URL-cím regisztrálása  *a HTTP.sys* a nevében használatával a [ *erős helyettesítő* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL-előtagját.

Ahhoz például, hogy foglalási `http://+:80` egy szolgáltatás, a következő konfigurációs kell használni a ServiceManifest.xml:

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

A végpont nevének kell átadni és a `HttpSysCommunicationListener` konstruktor:

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

#### <a name="use-httpsys-with-a-static-port"></a>A statikus port httpsys kiszolgálón használata
A httpsys kiszolgálón egy statikus port használatára, adja meg a portszámot a `Endpoint` konfiguráció:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>A dinamikus port httpsys kiszolgálón használata
A dinamikusan kiosztott portot használja a httpsys kiszolgálón, hagyja ki ezt a `Port` tulajdonságot a `Endpoint` konfigurációs:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Vegye figyelembe, hogy a dinamikus port lefoglalta egy `Endpoint` a konfiguráció csak egyetlen port biztosít *állomás folyamatonként*. Az aktuális Service Fabric üzemeltetési modell lehetővé teszi, hogy több szolgáltatáspéldány és/vagy ugyanabban a folyamatban, ami azt jelenti, hogy mindegyik keresztül kiosztása során ugyanazt a portot is meg fogja osztani üzemeltetését replikák a `Endpoint` konfigurációs. Több httpsys kiszolgálón példány megoszthat egy portot használ, az alapul szolgáló *http.sys* port megosztása szolgáltatást, de, amely nem támogatja `HttpSysCommunicationListener` miatt a komplikációk az ügyféli kérelmek részére okozna. Dinamikus portok használatát vércse esetén ajánlott a webkiszolgálón.

## <a name="kestrel-in-reliable-services"></a>A megbízható szolgáltatások vércse
Vércse használható egy megbízható szolgáltatás importálásával a **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet-csomagot. Ez a csomag tartalmaz `KestrelCommunicationListener`, megvalósítása `ICommunicationListener`, ami lehetővé teszi az ASP.NET Core WebHost belül egy megbízható szolgáltatás létrehozásához vércse használja, mint a webkiszolgálón.

Vércse, az ASP.NET Core platformfüggetlen webkiszolgáló libuv, a platformok közötti aszinkron i/o-szalagtár egyik alapján. Eltérően httpsys kiszolgálón, vércse nem használ központi végponthoz vezető például *http.sys*. És httpsys kiszolgálón, eltérően vércse nem támogatja a port megosztása több folyamat között. Minden példánynak vércse egy egyedi portot kell használnia.

![vércse][4]

### <a name="kestrel-in-a-stateless-service"></a>Az állapotmentes szolgáltatások vércse
Használandó `Kestrel` az állapotmentes szolgáltatások, bírálja felül a `CreateServiceInstanceListeners` metódust, és térjen vissza a `KestrelCommunicationListener` példány:

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

### <a name="kestrel-in-a-stateful-service"></a>Az állapotalapú service vércse
Használandó `Kestrel` állapot-nyilvántartó szolgáltatásban, bírálja felül a `CreateServiceReplicaListeners` metódust, és térjen vissza a `KestrelCommunicationListener` példány:

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

Ebben a példában egy egypéldányos példánya `IReliableStateManager` valósul meg a Webállomás függőségi beszúrására szolgáló tárolóhoz. Ez nem feltétlenül szükséges, de lehetővé teszi használatát `IReliableStateManager` és az MVC-vezérlő műveletmetódusokhoz megbízható gyűjteményei.

Vegye figyelembe, hogy egy `Endpoint` -konfiguráció nevét **nem** megadott `KestrelCommunicationListener` állapot-nyilvántartó szolgáltatásban. Ennek a magyarázatát részletesebben az alábbi részben található.

### <a name="endpoint-configuration"></a>Végpont-konfiguráció
Egy `Endpoint` konfigurációs vércse használatához nem szükséges. 

Vércse egy egyszerű önálló webkiszolgáló; httpsys kiszolgálón (és HttpListener) eltérően nem kell egy `Endpoint` konfiguráció *ServiceManifest.xml* , mert nem szükséges URL-cím regisztrálása elkezdése előtt. 

#### <a name="use-kestrel-with-a-static-port"></a>A statikus port vércse használata
A statikus port konfigurálható a `Endpoint` ServiceManifest.xml konfigurációját vércse való használatra. Ez azonban nem feltétlenül szükséges, akkor két lehetséges előnyöket nyújtja:
 1. Ha a port nem esik az alkalmazás porttartományát, nyitja meg, az operációs rendszer tűzfalon keresztül a Service Fabric.
 2. Az URL-cím le a `KestrelCommunicationListener` ezt a portot fogja használni.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Ha egy `Endpoint` van konfigurálva, a nevét kell átadását a `KestrelCommunicationListener` konstruktor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Ha egy `Endpoint` nem használja a konfigurációját, hagyja ki ezt a nevet a `KestrelCommunicationListener` konstruktor. Ebben az esetben egy dinamikus portot fogja használni. További információ a következő szakaszban talál.

#### <a name="use-kestrel-with-a-dynamic-port"></a>A dinamikus port vércse használata
Vércse nem használhatja az automatikus port hozzárendelés a `Endpoint` konfigurációját a ServiceManifest.xml, mert automatikus port hozzárendelés egy `Endpoint` konfigurációs hozzárendel egy egyedi port / *folyamaton*, és egyetlen gazdafolyamat vércse több példánya is tartalmazhat. Vércse nem támogatja a port megosztása, mert az nem működik vércse feltünteti egy egyedi portot kell megnyitni.

Dinamikus portok hozzárendelés vércse használni, egyszerűen csak hagyja ki ezt a `Endpoint` ServiceManifest.xml konfiguráció teljesen, és nem továbbítja az végpont nevét, a `KestrelCommunicationListener` konstruktor:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Ebben a konfigurációban `KestrelCommunicationListener` automatikusan választja ki egy nem használt portot az alkalmazás porttartományát.

## <a name="scenarios-and-configurations"></a>A forgatókönyvek és konfigurációk
Ez a szakasz ismerteti a következő esetekben és a webkiszolgáló, port konfigurálása, a Service Fabric-integrációs beállítások és egyéb beállítások egy megfelelően működő szolgáltatás eléréséhez kombinációja javasolt:
 - Külsőleg kitett az ASP.NET Core állapotmentes szolgáltatások
 - Csak belső ASP.NET Core állapotmentes szolgáltatások
 - Csak belső ASP.NET Core állapotalapú szolgáltatás

Egy **külsőleg kitett** szolgáltatás egyike, amely elérhetővé teszi a végpont, általában egy terhelés-kiegyenlítő keresztül a fürtön kívüli elérhető.

Egy **csak belső** szolgáltatás az egyik amelynek-végpont esetében csak a fürtön belül elérhető.

> [!NOTE]
> Állapot-nyilvántartó Szolgáltatásvégpontok általában nem szabad felfedni az internethez. Állapotalapú szolgáltatások teszi közzé, mert a terheléselosztó nem lesz képes forgalom megfelelő irányításához, és keresse meg lehet mögötti terheléselosztókat, amelyekben nem tudnak a Service Fabric szolgáltatás megoldás, az Azure terheléselosztó, például a fürtök az állapotalapú szolgáltatás replika. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Külsőleg kitett az ASP.NET Core állapotmentes szolgáltatások
Vércse az előtér-szolgáltatásokat, amelyek külső, az internetre irányuló HTTP-végpontokról láthatóvá ajánlott webkiszolgálóját. A Windows a httpsys kiszolgálón port megosztási képességet, amely lehetővé teszi több webszolgáltatásokkal ugyanazokat a csomópontokat használja ugyanazt a portot, anélkül, hogy egy előtér-proxy vagy az átjáró a HTTP-útválasztást az állomásnév vagy elérési útja, szerint megkülönböztetett használható.
 
Ha kapcsolódik az internethez, állapotmentes szolgáltatások egy jól ismert és állandó végponttal, amely elérhető a terheléselosztót használja. Ez az az URL-címet, a felhasználók számára az alkalmazás biztosítja. A következő konfiguráció ajánlott:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | vércse | Vércse esetén az elsődleges webkiszolgálón, mert a Windows és Linux esetén támogatott. |
| Port konfigurálása | Statikus | A jól ismert statikus port lehet beállítani a `Endpoints` ServiceManifest.xml, például a 80-as HTTP vagy HTTPS-hez a 443-as konfigurációját. |
| ServiceFabricIntegrationOptions | None | A `ServiceFabricIntegrationOptions.None` beállítást kell használni, amikor a Service Fabric-integráció köztes konfigurálása, hogy a szolgáltatás nem próbálja meg egy egyedi azonosítót a bejövő kérelmeket. Az alkalmazás külső felhasználók nem fogja tudni az egyedi azonosító adatokat, a köztes használják. |
| A példányok száma | -1 | Tipikus használati esetekben a példányok száma beállítást meg kell-"1", hogy egy példány érhető el az összes olyan csomóponton, amelyet a forgalom fogadására a terheléselosztótól. |

Ha több külsőleg kitett szolgáltatás ugyanazokat a csomópontok megosztásához httpsys kiszolgálón egy stabil, de egyedi URL-cím használható. Ehhez a IWebHost konfigurálásakor megadott URL-cím módosításával. Megjegyzés: Ez kizárólag a httpsys kiszolgálón.

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
Csak a fürtön belül hívását állapotmentes szolgáltatások egyedi URL-eket kell használnia, és dinamikusan kiosztott portok több szolgáltatás együttműködésének biztosításához. A következő konfiguráció ajánlott:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | vércse | Bár a httpsys kiszolgálón belső állapotmentes szolgáltatásokhoz használható, vércse a javasolt kiszolgálót úgy, hogy több-gazdagépet megosztásához szolgáltatáspéldány.  |
| Port konfigurálása | dinamikusan kiosztott | Az állapotalapú szolgáltatás több replika megoszthatja a gazdafolyamat vagy a gazda operációs rendszer, és így kell egyedi portok. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | A dinamikus port-hozzárendelés Ez a beállítás megakadályozza a korábban leírt téves identitás probléma. |
| InstanceCount | bármely | A példányok száma beállítás állítható be értéket a szolgáltatás működtetéséhez szükséges. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Csak belső állapot-nyilvántartó ASP.NET Core szolgáltatás
Állapotalapú szolgáltatások, amelyek csak a fürtön belül hívását dinamikusan hozzárendelt portok használjon több szolgáltatás együttműködésének biztosításához. A következő konfiguráció ajánlott:

|  |  | **Megjegyzések** |
| --- | --- | --- |
| Webkiszolgáló | vércse | A `HttpSysCommunicationListener` több replikák osztozik egy állapotalapú szolgáltatások használatra nem szolgál. |
| Port konfigurálása | dinamikusan kiosztott | Az állapotalapú szolgáltatás több replika megoszthatja a gazdafolyamat vagy a gazda operációs rendszer, és így kell egyedi portok. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | A dinamikus port-hozzárendelés Ez a beállítás megakadályozza a korábban leírt téves identitás probléma. |

## <a name="next-steps"></a>Következő lépések
[A Service Fabric-alkalmazás hibakeresése a Visual Studio használatával](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png

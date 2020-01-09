---
title: Telemetria csatornák Az Azure Application Insightsban | Microsoft Docs
description: Telemetria-csatornák testreszabása az Azure Application Insights SDK-ban a .NET-hez és a .NET Core-hoz.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cef35ae5cb2b66385332a3b1f9ebe177ea26a3e4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406362"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetria csatornák Application Insights

A telemetria csatornák az [Azure Application Insights SDK](../../azure-monitor/app/app-insights-overview.md)-k szerves részét képezik. Kezelik a telemetria pufferelését és a Application Insights szolgáltatásba való átvitelét. Az SDK-k .NET-és .NET Core-verziói két beépített telemetria-csatornával rendelkeznek: `InMemoryChannel` és `ServerTelemetryChannel`. Ez a cikk részletesen ismerteti az egyes csatornákat, beleértve a csatornák viselkedésének testreszabását is.

## <a name="what-are-telemetry-channels"></a>Mik azok a telemetria-csatornák?

A telemetria csatornák felelősek a telemetria-elemek puffereléséhez és a Application Insights szolgáltatásba való küldéséhez, ahol lekérdezési és elemzési célból vannak tárolva. A telemetria-csatorna bármely olyan osztály, amely megvalósítja az [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) felületet.

A telemetria-csatorna `Send(ITelemetry item)` metódusát a rendszer az összes telemetria inicializáló és telemetria processzor hívása után hívja meg. Tehát a telemetria-feldolgozó által eldobott elemek nem érik el a csatornát. a `Send()` általában nem küldi el azonnal az elemeket a háttérbe. A szolgáltatás általában a memóriában, és kötegekben küldi el őket a hatékony átvitel érdekében.

A [élő metrikastream](live-stream.md) egy egyéni csatornát is tartalmaz, amely a telemetria élő közvetítését is felruházza. Ez a csatorna független a normál telemetria-csatornától, és ez a dokumentum nem vonatkozik rá.

## <a name="built-in-telemetry-channels"></a>Beépített telemetria csatornák

A Application Insights .NET és .NET Core SDK-k két beépített csatornával rendelkeznek:

* `InMemoryChannel`: egy könnyűsúlyú csatorna, amely a memóriában lévő elemeket a küldések végéig kihasználja. Az elemek a memóriában vannak puffereltek, és 30 másodpercenként kiürítve, vagy ha 500 elem van pufferelt. Ez a csatorna minimális megbízhatósági garanciákat biztosít, mivel a hiba után nem próbálkozik újra a telemetria küldésével. Ez a csatorna nem tartja meg a lemezen lévő elemeket is, így az összes el nem küldött elem véglegesen elvész az alkalmazás leállításakor (kecses vagy nem). Ez a csatorna egy `Flush()` módszert valósít meg, amely a memóriában lévő telemetria-elemek egyidejű kényszerítésére használható. Ez a csatorna kiválóan alkalmas a rövid ideig futó alkalmazások esetében, amelyeknél a szinkron ürítés ideális.

    Ez a csatorna a Microsoft. ApplicationInsights NuGet csomag részét képezi, és az az alapértelmezett csatorna, amelyet az SDK használ, ha nincs más konfigurálva.

* `ServerTelemetryChannel`: egy fejlettebb csatorna, amely újrapróbálkozási házirendeket és az adattárolási képességet helyi lemezen tárolja. Ez a csatorna újrapróbálkozik a telemetria küldésével, ha átmeneti hibák történnek. Ez a csatorna a helyi lemezes tárolást is használja a hálózati kimaradások vagy a nagy telemetria-kötetek esetében a lemezen lévő elemek megtartására. Az újrapróbálkozási mechanizmusok és a helyi lemezes tárolás miatt ez a csatorna megbízhatóbbnak minősül, és minden éles környezetben ajánlott. Ez a csatorna az alapértelmezett a [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) és a [ASP.net Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) a hivatalos dokumentációnak megfelelően konfigurált alkalmazások esetében. Ez a csatorna kiszolgálói forgatókönyvekhez van optimalizálva hosszan futó folyamatokkal. A csatorna által megvalósított [`Flush()`](#which-channel-should-i-use) metódus nem szinkronban van.

    Ez a csatorna a Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel NuGet csomagként érhető el, és a Microsoft. ApplicationInsights. web vagy a Microsoft. ApplicationInsights. AspNetCore NuGet használatakor automatikusan megtörténik. csomag.

## <a name="configure-a-telemetry-channel"></a>Telemetria-csatorna konfigurálása

Telemetria-csatornát úgy konfigurálhat, hogy az aktív telemetria-konfigurációra állítja be. A ASP.NET alkalmazások esetében a konfiguráció magában foglalja a telemetria-csatorna példányának beállítását `TelemetryConfiguration.Active`vagy a `ApplicationInsights.config`módosításával. ASP.NET Core alkalmazások esetében a konfiguráció magában foglalja a csatorna hozzáadását a függőségi injektálási tárolóhoz.

A következő részekben példákat talál a csatorna `StorageFolder` beállításának konfigurálására különböző típusú alkalmazásokban. `StorageFolder` csak a konfigurálható beállítások egyike. A konfigurációs beállítások teljes listájáért tekintse meg a cikk későbbi, [a beállítások című szakaszát](telemetry-channels.md#configurable-settings-in-channels) .

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfigurálás a ApplicationInsights. config használatával ASP.NET-alkalmazásokhoz

A [ApplicationInsights. config](configuration-with-applicationinsights-config.md) fájl következő szakasza a `StorageFolder` beállítású `ServerTelemetryChannel` csatornát mutatja be egy egyéni helyre:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguráció a kódban a ASP.NET-alkalmazásokhoz

A következő kód egy "ServerTelemetryChannel" példányt állít be, amelynek `StorageFolder` beállítása egyéni helyre. Adja hozzá ezt a kódot az alkalmazás elején, jellemzően `Application_Start()` metódusban a Global.aspx.cs-ben.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguráció a kódban ASP.NET Core alkalmazások számára

Módosítsa a `Startup.cs` osztály `ConfigureServices` metódusát az itt látható módon:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> A csatorna `TelemetryConfiguration.Active` használatával történő konfigurálása nem ajánlott ASP.NET Core alkalmazásokhoz.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfiguráció a .NET/.NET Core Console-alkalmazások kódjában

A konzolos alkalmazások esetében a kód ugyanaz, mint a .NET és a .NET Core esetében:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>A ServerTelemetryChannel működési részletei

a `ServerTelemetryChannel` a memóriában lévő pufferbe érkező elemeket tárolja. Az elemek szerializálása, tömörítése és tárolása egy `Transmission` példányba 30 másodpercenként egyszer, vagy ha az 500-es elemek pufferelése megtörtént. Az egyetlen `Transmission`-példány legfeljebb 500 elemet tartalmaz, és a Application Insights szolgáltatáshoz egyetlen HTTPS-híváson keresztül küldött telemetria-köteget jelöl.

Alapértelmezés szerint a rendszer legfeljebb 10 `Transmission` példányt küldhet párhuzamosan. Ha a telemetria gyorsabban érkezik, vagy ha a hálózat vagy a Application Insights háttér lassú, `Transmission` példányok tárolódnak a memóriában. A memóriában lévő `Transmission` puffer alapértelmezett kapacitása 5 MB. Ha túllépte a memórián belüli kapacitást, `Transmission` példányokat a rendszer a helyi lemezen tárolja, legfeljebb 50 MB-os korlátot. `Transmission` példányok a helyi lemezen tárolódnak, ha hálózati problémák vannak. Csak a helyi lemezen tárolt elemek maradnak meg az alkalmazás összeomlása miatt. A rendszer elküldi őket, amikor az alkalmazás újra elindul.

## <a name="configurable-settings-in-channels"></a>Konfigurálható beállítások a csatornákon

Az egyes csatornák konfigurálható beállításainak teljes listájáért lásd:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

A `ServerTelemetryChannel`leggyakrabban használt beállításai a következők:

1. `MaxTransmissionBufferCapacity`: a csatorna által a memóriában lévő pufferek továbbításához használt memória maximális mérete (bájt). Ha eléri ezt a kapacitást, a rendszer az új elemeket közvetlenül a helyi lemezen tárolja. Az alapértelmezett érték 5 MB. Ha nagyobb értéket ad meg, a lemez kevesebb használatot eredményez, de ne feledje, hogy a memóriában lévő elemek elvesznek, ha az alkalmazás összeomlik.

1. `MaxTransmissionSenderCapacity`: azon `Transmission` példányok maximális száma, amelyek egy időben lesznek elküldve a Application Insightsnak. Az alapértelmezett érték 10. Ez a beállítás nagyobb számra állítható be, ami akkor ajánlott, ha nagy mennyiségű telemetria generál. A nagy mennyiség általában a terheléses tesztelés során vagy a mintavétel kikapcsolásakor fordul elő.

1. `StorageFolder`: a csatorna által az elemek lemezre való tárolásához használt mappa szükség szerint. A Windows rendszerben a rendszer% LOCALAPPDATA% vagy% TEMP% értéket használ, ha nincs más elérési út megadva explicit módon. A Windowson kívüli környezetekben meg kell adnia egy érvényes helyet vagy telemetria, amely nem tárolódik a helyi lemezen.

## <a name="which-channel-should-i-use"></a>Melyik csatornát érdemes használni?

`ServerTelemetryChannel` javasolt a hosszú ideig futó alkalmazásokat érintő legtöbb éles környezetben. A `ServerTelemetryChannel` által megvalósított `Flush()` metódus nem szinkron, és nem garantálja az összes függőben lévő elem küldését a memóriából vagy a lemezből. Ha ezt a csatornát olyan helyzetekben használja, ahol az alkalmazás leáll, javasoljuk, hogy a `Flush()`meghívása után némi késleltetést vezessen be. A szükséges késleltetési időtartam nem kiszámítható. Olyan tényezőktől függ, mint a memóriában lévő elemek vagy `Transmission`-példányok száma, hány lemezről van szó, hányat továbbítanak a háttérbe, és hogy a csatorna exponenciális visszatartási forgatókönyvek közepén van-e.

Ha szinkron flöss szükséges, javasoljuk, hogy használja a `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>A Application Insights Channel garantálja a telemetria kézbesítését? Ha nem, milyen helyzetekben lehet elveszíteni a telemetria?

A rövid válasz az, hogy a beépített csatornák egyike sem nyújt tranzakció típusú garanciát a telemetria történő kézbesítéshez. a `ServerTelemetryChannel` az `InMemoryChannel` a megbízható kézbesítéshez képest fejlettebb, de csak a legjobb erőfeszítést tesz a telemetria küldéséhez. A telemetria több helyzetben is elvész, beleértve a gyakori forgatókönyveket:

1. Az alkalmazás összeomlása esetén a memóriában lévő elemek elvesznek.

1. A telemetria megszakadt a hálózati problémák hosszabb időtartama alatt. A telemetria helyi lemezre van tárolva a hálózati kimaradások során, vagy ha problémák merülnek fel a Application Insights háttérrel. Az 48 óránál régebbi elemek azonban el lesznek vetve.

1. A Windows telemetria tárolásának alapértelmezett helye a következő:% LOCALAPPDATA% vagy% TEMP%. Ezek a helyek általában helyiek a gépen. Ha az alkalmazás fizikailag áttelepíti az egyik helyről a másikra, az eredeti helyen tárolt összes telemetria elvész.

1. A Windows Web Apps esetében az alapértelmezett lemez-tárolási hely a D:\local\LocalAppData. Ezt a helyet nem őrzi meg a program. Az alkalmazás újraindításakor, kibővítve és más hasonló műveletekben törölhető, ami az ott tárolt telemetria elvesztését eredményezi. Felülbírálhatja az alapértelmezett értéket, és megtarthatja a tárterületet megőrzött helyre, például D:\home. Az ilyen megőrzött telephelyeket azonban a távoli tárterület szolgáltatja, így lassú lehet.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Működik a ServerTelemetryChannel a Windowson kívül más rendszereken?

Bár a csomagjának és névterének neve tartalmazza a "WindowsServer" nevet, ez a csatorna a Windows rendszeren kívül más rendszerek esetében is támogatott, a következő kivétellel. A Windowstól eltérő rendszereken a csatorna alapértelmezés szerint nem hoz létre helyi tároló mappát. Létre kell hoznia egy helyi tárolási mappát, és konfigurálnia kell a csatornát a használatára. A helyi tárterület konfigurálása után a csatorna ugyanúgy működik az összes rendszeren.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Az SDK ideiglenes helyi tárolót hoz létre? Titkosítottak-e az adattárolóban tárolt adatforgalom?

Az SDK hálózati problémák vagy a szabályozás során tárolja a helyi tárolóban lévő telemetria-elemeket. Ezek az adatforgalom nem helyileg titkosított.

Windows rendszerek esetében az SDK automatikusan létrehoz egy ideiglenes helyi mappát a (z)% TEMP% vagy% LOCALAPPDATA% könyvtárban, és korlátozza a hozzáférést a rendszergazdákhoz és az aktuális felhasználóhoz.

A Windows rendszertől eltérő rendszerek esetén az SDK nem hozza létre automatikusan a helyi tárterületet, így alapértelmezés szerint nem tárolja az adattárolást. Saját maga is létrehozhat egy tárolási könyvtárat, és beállíthatja a csatornát a használatára. Ebben az esetben Ön a felelős a címtár biztonságának biztosításáért.
További információ az adatvédelem [és az adatvédelem terén](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK
A Application Insights összes SDK-hoz hasonlóan a csatornák is nyílt forráskódúak. Olvassa el és járuljon hozzá a kód vagy a jelentéssel kapcsolatos problémákhoz [a hivatalos GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet)-tárházban.

## <a name="next-steps"></a>Következő lépések

* [Mintavételezés](../../azure-monitor/app/sampling.md)
* [SDK – hibaelhárítás](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)

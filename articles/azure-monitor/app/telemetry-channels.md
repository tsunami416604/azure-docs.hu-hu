---
title: Telemetriai csatornák az Azure Application Insightsban | Microsoft dokumentumok
description: Telemetriai csatornák testreszabása az Azure Application Insights SDK-kban .NET és .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275697"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetriai csatornák az Application Insightsban

A telemetriai csatornák az [Azure Application Insights SDK-k](../../azure-monitor/app/app-insights-overview.md)szerves részét képezik. Ők kezelik a pufferelés és a telemetriai adatok átvitelét az Application Insights szolgáltatásba. Az SDK-k .NET és .NET Core verziói két beépített `InMemoryChannel` `ServerTelemetryChannel`telemetriai csatornával rendelkeznek: és . Ez a cikk részletesen ismerteti az egyes csatornákat, többek között a csatorna viselkedésének testreszabását.

## <a name="what-are-telemetry-channels"></a>Mik azok a telemetriai csatornák?

Telemetriai csatornák felelősek a telemetriai elemek pufferelése, és elküldi őket az Application Insights szolgáltatás, ahol a lekérdezés és elemzés tárolja. A telemetriai csatorna minden olyan [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) osztály, amely megvalósítja a felületet.

A `Send(ITelemetry item)` telemetriai csatorna metódusa az összes telemetriai inicializáló és telemetriai processzorok neve. Így a telemetriai processzor által eldobott elemek nem jutnak el a csatornához. `Send()`általában nem küldi el azonnal az elemeket a háttérbe. Általában puffereli őket a memóriában, és elküldi őket kötegekben, a hatékony átvitel érdekében.

[Élő metrikák stream](live-stream.md) is rendelkezik egy egyéni csatorna, amely a telemetriai adatok élő streamelése. Ez a csatorna független a rendszeres telemetriai csatorna, és ez a dokumentum nem vonatkozik rá.

## <a name="built-in-telemetry-channels"></a>Beépített telemetriai csatornák

Az Application Insights .NET és .NET Core SDK-k két beépített csatornával rendelkeznek:

* `InMemoryChannel`: Könnyű csatorna, amely puffereli a memóriában lévő elemeket, amíg el nem küldik őket. Az elemek pufferelése a memóriában történik, és 30 másodpercenként egyszer, illetve 500 elem pufferelésekor kerülnek kiürítésre. Ez a csatorna minimális megbízhatósági garanciákat nyújt, mert nem próbálja meg újra a telemetriai adatok at egy hiba után. Ez a csatorna nem tart elemeket a lemezen, így az el nem küldött elemek véglegesen elvesznek az alkalmazás leállításakor (kecses vagy nem). Ez a csatorna `Flush()` olyan módszert valósít meg, amely a memóriában lévő telemetriai elemek szinkron kiürítésére használható. Ez a csatorna kiválóan alkalmas rövid ideig futó alkalmazásokhoz, ahol a szinkron öblítés ideális.

    Ez a csatorna a nagyobb Microsoft.ApplicationInsights NuGet csomag része, és az alapértelmezett csatorna, amelyet az SDK akkor használ, ha semmi más nincs konfigurálva.

* `ServerTelemetryChannel`: Fejlettebb csatorna, amely újrapróbálkozási házirendekkel rendelkezik, és képes adatokat tárolni a helyi lemezen. Ez a csatorna újramegpróbálja telemetriai adatokat küldeni, ha átmeneti hibák fordulnak elő. Ez a csatorna is használja a helyi lemeztároló tartani elemeket a lemezen hálózati kimaradások vagy a magas telemetriai kötetek. Az újrapróbálkozási mechanizmusok és a helyi lemezes tárolás miatt ez a csatorna megbízhatóbbnak tekinthető, és minden éles forgatókönyv esetén ajánlott. Ez a csatorna az alapértelmezett [a ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) és [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) alkalmazások, amelyek a hivatalos dokumentáció szerint konfigurálva. Ez a csatorna hosszú ideig futó folyamatokkal rendelkező kiszolgálói forgatókönyvekhez van optimalizálva. A [`Flush()`](#which-channel-should-i-use) csatorna által megvalósított módszer nem szinkron.

    Ez a csatorna microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet csomagként kerül kiszállításra, és a Microsoft.ApplicationInsights.Web vagy a Microsoft.ApplicationInsights.AspNetCore NuGet használatakor automatikusan bekobzódik. Csomag.

## <a name="configure-a-telemetry-channel"></a>Telemetriai csatorna konfigurálása

Telemetriai csatornát úgy konfigurálhat, hogy az aktív telemetriai konfigurációra állítja be. A ASP.NET alkalmazások esetében a konfiguráció magában foglalja `TelemetryConfiguration.Active`a telemetriai csatornapéldány beállítását a számára, vagy a módosításával. `ApplicationInsights.config` ASP.NET core alkalmazások konfiguráció magában foglalja a csatorna hozzáadása a függőségi injektálási tárolóhoz.

Az alábbi szakaszok példákat `StorageFolder` mutatnak be a csatorna beállításának konfigurálására a különböző alkalmazástípusokban. `StorageFolder`csak az egyik konfigurálható beállítás. A konfigurációs beállítások teljes listáját a cikk [későbbi, beállításokkal kapcsolatos szakaszában](telemetry-channels.md#configurable-settings-in-channels) találja.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfiguráció az ApplicationInsights.config használatával ASP.NET alkalmazásokhoz

[Az ApplicationInsights.config](configuration-with-applicationinsights-config.md) következő szakasza `ServerTelemetryChannel` az `StorageFolder` egyéni helyre beállított csatornát mutatja:

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

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfiguráció a ASP.NET alkalmazások kódjában

A következő kód beállítja a "ServerTelemetryChannel" példányt egy egyéni helyre `StorageFolder` állítva. Adja hozzá ezt a kódot az alkalmazás `Application_Start()` elején, általában a metódus Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfiguráció a kódban ASP.NET Core alkalmazásokhoz

Módosítsa `ConfigureServices` az osztály `Startup.cs` módszerét az itt látható módon:

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
> A csatorna használatával `TelemetryConfiguration.Active` történő konfigurálása nem ajánlott ASP.NET Core alkalmazásokhoz.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfiguráció a .NET/.NET Core konzolalkalmazások kódjában

Konzolalkalmazások esetén a kód megegyezik a .NET és a .NET Core esetében is:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>A ServerTelemetryChannel működési adatai

`ServerTelemetryChannel`memórián belüli pufferben tárolja az érkező elemeket. Az elemek szerializálása, tömörítése `Transmission` és tárolása 30 másodpercenként egyszer történik, vagy 500 elem pufferelése esetén. Egyetlen `Transmission` példány legfeljebb 500 elemet tartalmaz, és egy telemetriai egy köteget jelöl, amely egyetlen HTTPS-híváson keresztül kerül elküldésre az Application Insights szolgáltatásnak.

Alapértelmezés szerint legfeljebb 10 `Transmission` példány küldhető párhuzamosan. Ha a telemetriai adatok gyorsabb ütemben érkeznek, vagy ha `Transmission` a hálózat vagy az Application Insights háttértartalék a lassú, példányok a memóriában tárolják. A memórián `Transmission` belüli puffer alapértelmezett kapacitása 5 MB. A memórián belüli kapacitás túllépése `Transmission` esetén a példányok legfeljebb 50 MB-os helyi lemezen tárolódnak. `Transmission`a példányok hálózati problémák esetén is a helyi lemezen tárolódnak. Csak a helyi lemezen tárolt elemek élik túl az alkalmazásösszeomlást. A rendszer akkor küldi el őket, amikor a jelentkezés újraindul.

## <a name="configurable-settings-in-channels"></a>Konfigurálható beállítások a csatornákban

Az egyes csatornák konfigurálható beállításainak teljes listáját a következő témakörben találja:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Az alábbiak leggyakrabban használt beállításai: `ServerTelemetryChannel`

1. `MaxTransmissionBufferCapacity`: A csatorna által a memóriában lévő átvitelek pufferelésére használt maximális memóriamennyiség bájtban. Ha eléri ezt a kapacitást, az új elemek közvetlenül a helyi lemezre kerülnek. Az alapértelmezett érték 5 MB. A magasabb érték beállítása kevesebb lemezhasználatot eredményez, de ne feledje, hogy a memóriában lévő elemek elvesznek, ha az alkalmazás összeomlik.

1. `MaxTransmissionSenderCapacity`: Az Application `Transmission` Insights nak egyidejűleg küldött példányok maximális száma. Az alapértelmezett érték 10. Ez a beállítás nagyobb számra konfigurálható, amely akkor ajánlott, ha hatalmas mennyiségű telemetriai adatokjönnek létre. A nagy mennyiség általában a terheléstesztelés során vagy a mintavétel kikapcsolásakor jelentkezik.

1. `StorageFolder`: Az a mappa, amelyet a csatorna az elemek lemezre tárolására használ, ha szükséges. A Windows rendszerben a %LOCALAPPDATA% vagy a %TEMP% akkor használatos, ha nincs külön elérési út megadva. A Windows-tól eltérő környezetekben meg kell adnia egy érvényes helyet, vagy a telemetriai adatok nem tárolódnak a helyi lemezen.

## <a name="which-channel-should-i-use"></a>Melyik csatornát használjam?

`ServerTelemetryChannel`a legtöbb, hosszú ideig futó alkalmazásokkal kapcsolatos éles forgatókönyvhez ajánlott. A `Flush()` megvalósított `ServerTelemetryChannel` módszer nem szinkron, és nem garantálja az összes függőben lévő elem küldését a memóriából vagy a lemezről. Ha ezt a csatornát olyan esetekben használja, ahol az alkalmazás le állításra készül, javasoljuk, hogy a hívás `Flush()`után némi késést vezessen be. A pontos késés, hogy szükség lehet a nem kiszámítható. Ez olyan tényezőktől függ, `Transmission` mint például, hogy hány elem vagy példány van a memóriában, hány van a lemezen, hány van továbbítva a háttérrendszernek, és hogy a csatorna az exponenciális visszalépési forgatókönyvek közepén van-e.

Ha szinkron öblítést kell végeznie, javasoljuk, `InMemoryChannel`hogy használja a használatát.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Az Application Insights-csatorna garantálja a telemetriai adatok kézbesítését? Ha nem, milyen forgatókönyvekben veszhet el a telemetriai adatok?

A rövid válasz az, hogy a beépített csatornák egyike sem kínál tranzakció-típusú garancia telemetriai kézbesítés a háttérrendszerbe. `ServerTelemetryChannel`a megbízható kézbesítéshez képest `InMemoryChannel` fejlettebb, de csak a legjobb erőfeszítést teszi a telemetriai adatok küldésére. A telemetria továbbra is elveszhet számos helyzetben, többek között a következő gyakori forgatókönyvekben:

1. A memóriában lévő elemek elvesznek, amikor az alkalmazás összeomlik.

1. A telemetria idvanáns idő a hálózati problémák hosszabb időszakaiban vész el. Telemetriai tárolja a helyi lemezen hálózati kimaradások során, vagy ha problémák merülnek fel az Application Insights háttérrendszer. A 48 óránál régebbi elemeket azonban elveti a rendszer.

1. A telemetriai adatok Windows rendszerben való tárolásának alapértelmezett lemezhelyei a %LOCALAPPDATA% vagy a %TEMP%. Ezek a helyek általában a helyi a gépen. Ha az alkalmazás fizikailag áttelepíti az egyik helyről a másikra, az eredeti helyen tárolt telemetriai adatok elvesznek.

1. A Windows webalkalmazásaiban az alapértelmezett lemeztárolási hely a D:\local\LocalAppData. Ez a hely nem marad meg. Az alkalmazás újraindítása, a horizontális felskálázás és más hasonló műveletek eltörlik, ami az ott tárolt telemetriai adatok elvesztéséhez vezet. Felülbírálhatja az alapértelmezett értéket, és megadhatja a tárolót egy megőrzött helyre, például D:\home. Az ilyen megőrzött helyeket azonban távtároló szolgálja ki, így lassú lehet.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Működik a ServerTelemetryChannel a Windows-tól eltérő rendszereken is?

Bár a csomag és a névtér neve tartalmazza a "WindowsServer" nevet, ez a csatorna nem windowsos rendszereken támogatott, a következő kivétellel. A Windows rendszertől eltérő rendszereken a csatorna alapértelmezés szerint nem hoz létre helyi tárolómappát. Létre kell hoznia egy helyi tárolómappát, és konfigurálnia kell a csatornát annak használatára. A helyi tároló konfigurálása után a csatorna ugyanúgy működik az összes rendszeren.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Az SDK ideiglenes helyi tárolót hoz létre? Titkosítva vannak az adatok a tárolóban?

Az SDK tárolja telemetriai elemeket a helyi tárolóban hálózati problémák vagy szabályozás közben. Ezek az adatok nincsenek helyileg titkosítva.

Windows rendszerek esetén az SDK automatikusan létrehoz egy ideiglenes helyi mappát a %TEMP% vagy %LOCALAPPDATA% könyvtárban, és csak a rendszergazdák és az aktuális felhasználó számára korlátozza a hozzáférést.

A Windows-tól eltérő rendszerek esetében az SDK nem hoz létre automatikusan helyi tárolót, így alapértelmezés szerint nem tárol adatokat helyileg. Saját maga is létrehozhat egy tárolókönyvtárat, és beállíthatja a csatorna használatát. Ebben az esetben Ön felelős a címtár biztonságának biztosításáért.
További információ az [adatvédelemről és](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)az adatvédelemről.

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK
Mint minden SDK az Application Insights, csatornák nyílt forráskódú. Olvassa el a kódot, és járuljon hozzá a problémák hozadékához [a hivatalos GitHub-tárhivatalban.](https://github.com/Microsoft/ApplicationInsights-dotnet)

## <a name="next-steps"></a>További lépések

* [Mintavételezés](../../azure-monitor/app/sampling.md)
* [SDK hibaelhárítás](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)

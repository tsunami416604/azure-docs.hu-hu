---
title: Az Azure Application Insights telemetria csatornák |} A Microsoft Docs
description: A .NET és .NET Core az Azure Application Insights SDK-k telemetriai csatornák testreszabásának módját.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561350"
---
# <a name="telemetry-channels-in-application-insights"></a>Az Application Insights telemetria csatornák

Telemetria csatornák szerves részét képezik a [Azure Application Insights SDK-k](../../azure-monitor/app/app-insights-overview.md). Pufferelés és továbbítását a telemetria az Application Insights szolgáltatásba kezelnek. A .NET és .NET Core SDK-k verziói rendelkezik két beépített telemetriai csatornák: `InMemoryChannel` és `ServerTelemetryChannel`. Ez a cikk ismerteti az egyes csatornák részletesen, beleértve a csatorna viselkedés testreszabása.

## <a name="what-are-telemetry-channels"></a>Mik a telemetriai adatok csatornák?

Telemetria csatornák felelős telemetriai pufferelés, és elküldi azokat az Application Insights szolgáltatásnak, hol vannak tárolva a lekérdezését és elemzését. A telemetria csatorna egy olyan osztály, amely megvalósítja a [ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) felületet.

A `Send(ITelemetry item)` telemetriai csatorna módszert hívja meg az összes telemetriai inicializálók után és a telemetria processzorok nevezzük. Tehát egy telemetria-feldolgozó által kihagyott elemek nem érhető el a csatornát. `Send()` nem általában küldhet a cikkek a háttér azonnal. Általában őket a memóriában puffereli, és elküldi őket kötegekben, hatékony továbbítására.

[Élő metrikák Stream](live-stream.md) használja, a telemetriai adatok élő adatfolyamként, amelyen egyéni csatornán is rendelkezik. Ez a csatorna a normál telemetriai csatorna független, és ez a dokumentum nem vonatkozik rá.

## <a name="built-in-telemetry-channels"></a>Beépített telemetriai csatornák

Az Application Insights .NET és .NET Core SDK-k szállításra két beépített csatornák:

* `InMemoryChannel`: Könnyen használható csatornát, amely a memóriában puffereli elemek, mindaddig, amíg a rendszer azokat. A cikkek a memóriában puffereli és 30 másodpercenként kiürített, vagy minden alkalommal, amikor 500 elemet pufferelve van. Ez a csatorna kínál minimális megbízhatóságot garantálja, mert azt nem a telemetria küldését hiba után újra. Ez a csatorna is nem elemek megtartása a lemezen, így bármely elküldetlen elemek véglegesen alkalmazás leállásakor elvesznek (sikeres-e vagy sem). Ez a csatorna valósít meg egy `Flush()` kényszerített-kiürítés minden memórián belüli telemetriai elem szinkron módon segítségével módszert. Ez a csatorna kiválóan rövid ideig futó alkalmazások hol szinkron annak kiürítése ideális megoldást biztosít.

    Ez a csatorna nagyobb Microsoft.ApplicationInsights NuGet-csomag része, és az alapértelmezett csatornán, amely az SDK-t használja, ha semmi más van konfigurálva.

* `ServerTelemetryChannel`: Speciális csatorna, amely rendelkezik az újrapróbálkozási szabályzatok és a egy helyi lemezen lévő adatok tárolását. Ez a csatorna újrapróbálkozik a telemetria küldését, ha átmeneti hiba lép fel. Ez a csatorna is a helyi lemezes tárhelyért használ elemek megőrzése lemezen hálózati kimaradások vagy a magas telemetrikus kötet során. Ezek újrapróbálkozási mechanizmus és a helyi lemezes tárhelyért miatt ez a csatorna megbízhatóbb minősülnek, és az összes üzemi forgatókönyvek esetén ajánlott. Ez a csatorna nem az alapértelmezett [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) és [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) alkalmazásokat, amelyek a hivatalos dokumentáció alapján vannak konfigurálva. Ez a csatorna a hosszú futású folyamatok szituációk van optimalizálva. A [ `Flush()` ](#which-channel-should-i-use) módszert, amelyet ez a csatorna nem szinkron.

    Ez a csatorna tartalmazza a szükséges a Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet-csomagot, és automatikusan igényelve, a Microsoft.ApplicationInsights.Web vagy Microsoft.ApplicationInsights.AspNetCore NuGet használatakor a csomag.

## <a name="configure-a-telemetry-channel"></a>Telemetria csatorna konfigurálása

Az aktív telemetria konfigurációs beállításával konfigurálja a telemetriai adatok csatornát. Az ASP.NET-alkalmazások konfigurációja magában foglalja a beállítást a telemetriai adatokat a channel-példány `TelemetryConfiguration.Active`, vagy módosításával `ApplicationInsights.config`. ASP.NET Core-alkalmazások konfigurációs magában foglalja a csatorna ad hozzá a függőség injektálási tárolót.

A következő szakaszok bemutatják a példák konfigurálásának a `StorageFolder` a különféle alkalmazás-csatorna beállítását. `StorageFolder` Ez a konfigurálható beállítások csak egyike. A konfigurációs beállítások teljes listájáért lásd: [a beállítások szakaszban](telemetry-channels.md#configurable-settings-in-channels) a cikk későbbi részében.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Az applicationinsights.config fájlt az ASP.NET-alkalmazások konfigurálása

A következő szakasz a [ApplicationInsights.config](configuration-with-applicationinsights-config.md) jeleníti meg a `ServerTelemetryChannel` csatorna konfigurálva `StorageFolder` beállítása egy egyéni helyre:

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

### <a name="configuration-in-code-for-aspnet-applications"></a>A kód az ASP.NET-alkalmazások konfigurálása

A következő kódot állít be egy "ServerTelemetryChannel" példány `StorageFolder` beállítása egy egyéni helyre. Ez a kód, az alkalmazás elején általában hozzáadása a `Application_Start()` metódus a Global.aspx.cs osztályból.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Az ASP.NET Core-alkalmazások a kódban szereplő konfiguráció

Módosítsa a `ConfigureServices` módszere a `Startup.cs` osztályt itt látható módon:

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
> A csatorna konfigurálása használatával `TelemetryConfiguration.Active` ASP.NET Core-alkalmazások esetében nem javasolt.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfigurációs.NET/.NET Core-konzolalkalmazást a kódban szereplő

A konzol alkalmazások esetén a kód megegyezik a .NET és a .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel működési részletei

`ServerTelemetryChannel` a tároló egy memórián belüli pufferben lévő elemek érkező. Az elemek szerializált, tömörített és eltárolni egy `Transmission` példány 30 másodpercenként, vagy mikor lett 500 elemet pufferelt. Egyetlen `Transmission` -példány legfeljebb 500 elemet tartalmaz, és a telemetriai adatokból, hogy az Application Insights szolgáltatásba egy HTTPS-hívással keresztül küldött kötegelt jelöli.

Legfeljebb 10 alapértelmezés szerint `Transmission` példányok párhuzamosan elküldhető. Ha gyorsabb ütemben tölthetőek telemetriai adatokat, vagy ha a hálózat vagy az Application Insights biztonsági célból lassú, `Transmission` példányok memóriában tárolódnak. A memórián belüli alapértelmezett kapacitása `Transmission` puffer mérete 5 MB-ot. Amikor a rendszer túllépte a memórián belüli kapacitás, `Transmission` példányok legfeljebb 50 MB-os korlát érvényes helyi lemezen tárolja. `Transmission` példányok tárolása a helyi lemezre is hálózati probléma esetén. Csak a helyi lemezen tárolt elemek egy alkalmazás összeomlása stabilitást biztosít. Ezek még küldött, amikor az alkalmazás indul újra.

## <a name="configurable-settings-in-channels"></a>A csatorna konfigurálható beállítások

Az egyes csatorna konfigurálható beállítások teljes listájáért lásd:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

A leggyakrabban használt beállításai az alábbiakban `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: A maximális mennyisége (bájt), a puffer az átvitelt a memóriában csatornák által használt memória. Ez a kapacitás elérése után új elemek tárolódnak, közvetlenül a helyi lemezre. Az alapértelmezett érték: 5 MB-ot. Beállítás értéke kisebb lemezhasználat vezet, de ne feledje, hogy a memóriában elemek elvesznek, ha az alkalmazás összeomlik.

1. `MaxTransmissionSenderCapacity`: A maximális számát `Transmission` , amelyek küld az Application Insights egy időben. Az alapértelmezett értéke 10. Ez a beállítás nagyobb számot, akkor ajánlott, amikor egy hatalmas mennyiségű telemetriai jön létre, amely konfigurálható. Nagy adatmennyiség általában akkor fordul elő a terheléses tesztelés díjaival, vagy amikor a mintavételi ki van kapcsolva.

1. `StorageFolder`: A mappa, amelyet a csatorna elemek szükség szerint lemezre tárolására. Windows, a % LOCALAPPDATA % vagy % TEMP % akkor használatos, ha nincs más útvonal explicit módon van megadva. Windows eltérő környezetekben adjon meg egy érvényes helyet, vagy a telemetriai adatok nem tárolja a helyi lemezre.

## <a name="which-channel-should-i-use"></a>Mely csatorna használjam?

`ServerTelemetryChannel` ajánlott a hosszú ideig futó alkalmazások a legtöbb éles forgatókönyveket. A `Flush()` metódus által megvalósított `ServerTelemetryChannel` nem teljesen szinkron, és azt is nem garantálja a memória vagy lemezterület az összes függőben lévő elemek küldi. Ha használja ezt a csatornát forgatókönyvek készül, hogy állítsa le az alkalmazás esetén, azt javasoljuk, hogy némi késedelemmel hívása után vezessen be `Flush()`. Késleltetés, amelyekre szüksége lehet, hogy pontos mennyisége nem kiszámítható. Attól függ, például hogy hány elemek tényezők vagy `Transmission` példányai a memória, hány vannak a lemezen, hány vannak továbbított a háttérrendszerének, és hogy a csatorna exponenciális visszatartási forgatókönyvek közepén.

Ha kell tennie egy szinkron kiürítési, azt javasoljuk, hogy használja `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Az Application Insights-csatorna garantálja a telemetriai adatok kézbesítési? Ha nem, a forgatókönyvek, ahol telemetriai adatok elvesznek, melyek?

A rövid választ ki kell, hogy a beépített csatornák egyike biztosítunk tranzakció-típusa, telemetriai adatokat a háttérben történő továbbítását. `ServerTelemetryChannel` a speciális képest a `InMemoryChannel` a megbízható kézbesítést, de csak a legjobb kísérlet telemetria küldése révén is. Továbbra is elvész, több olyan helyzet, többek között ezek a gyakori forgatókönyvek a telemetriai adatokat is:

1. Memóriában lévő elemek elvesznek, ha az alkalmazás összeomlik.

1. Hálózati problémák hosszabb ideig telemetria történik adatvesztés. Telemetria során a hálózati kimaradások, vagy ha problémák merülnek fel a Application Insights-háttérrendszer helyi lemezre van tárolva. 24 óránál régebbi elemek azonban elvesznek.

1. Lemez alapértelmezett helyét a Windows telemetriai adatok tárolása a következők: % LOCALAPPDATA % vagy % TEMP %. Ezek a helyek egymástól általában helyi géphez. Ha az alkalmazás áttelepítést végez fizikailag egyik helyről a másikra, semmilyen telemetriai adatot az eredeti helyen tárolt elvész.

1. A Windows a Web Apps az alapértelmezett lemezterület hely D:\local\LocalAppData. Ezen a helyen nem megőrzött. Ez törli az alkalmazás újraindul, méretezési mintáit és más ilyen műveletek, és ott tárolt bármely telemetriájának. Az alapértelmezett beállítás felülbírálásával, és adja meg a storage például D:\home megőrzött helyre. Azonban az ilyen megőrzött helyeket távoli tároló által üzemeltetett, és így lassú lehet.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel eltérő Windows rendszereken működik?

A csomag és a névtér neve tartalmazza a "WindowsServer", bár ez a csatorna a következő kivétel miatt eltérő Windows, rendszereken támogatott. A nem Windows rendszerek esetében a csatorna nem hozzon létre egy helyi tároló alapértelmezés szerint. Hozzon létre egy helyi mappát és a csatorna használatára, konfigurálnia kell. Helyi tároló konfigurálása után a csatornát az összes rendszeren ugyanúgy működik.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Hozzon létre ideiglenes helyi tárhely az SDK-t? Tárolási titkosítja az adatokat?

Az SDK-t tárolja a telemetriai elemeket a helyi tárban hálózati problémák vagy a szabályozás során. Ezeket az adatokat a helyileg nem titkosított.

A Windows rendszerek esetében az SDK automatikusan ideiglenes helyi mappát hoz létre a % TEMP % vagy % LOCALAPPDATA % könyvtárban, és korlátozza a hozzáférést a rendszergazdák és csak az aktuális felhasználó.

Az eltérő Windows rendszeren helyi tárterület az SDK-ban automatikusan létrejön, és ezért nem tárol adatokat helyben alapértelmezés szerint. Hozzon létre egy storage-könyvtárat, saját magának, és használhatja azt a csatornát konfigurálni. Ebben az esetben Ön biztosításáért, hogy biztonságos-e a könyvtárban.
Tudjon meg többet [adatvédelem](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open-source SDK
Minden Application insights SDK, például a csatornák olyan nyílt forráskódú. Olvassa el, és hozzájárulnak a a kódot, vagy a problémákat, [a hivatalos GitHub-adattárat](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>További lépések

* [Mintavételezés](../../azure-monitor/app/sampling.md)
* [SDK hibáinak elhárítása](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)

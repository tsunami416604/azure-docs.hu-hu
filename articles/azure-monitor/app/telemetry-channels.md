---
title: Az Azure Application Insights telemetria csatornák |} A Microsoft Docs
description: Hogyan szabhatja testre az Azure Application Insights SDK-k telemetriai csatornák.NET/.NET Core.
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
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255813"
---
# <a name="telemetrychannel-in-application-insights"></a>Az Application Insights TelemetryChannel

TelemetryChannel szerves részét képezi [Azure Application Insights SDK-k](../../azure-monitor/app/app-insights-overview.md). Kezeli a pufferelés és továbbítását a telemetria az Application Insights szolgáltatásba. A .NET és .NET Core SDK-k verziói rendelkezik két beépített TelemetryChannels - `InMemoryChannel` és `ServerTelemetryChannel`. Ez a cikk ismerteti az egyes csatornák részletesen, beleértve a felhasználók hogyan testre csatorna viselkedését.

## <a name="what-is-a-telemetrychannel"></a>Mi az a TelemetryChannel?

`TelemetryChannel` feladata pufferelés, és ezek items telemetria az Application Insights szolgáltatásba, hol tárolják a lekérdezését és elemzését. Minden olyan felületet megvalósító osztályt [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

A `Send(ITelemetry item)` TelemetryChannel módja az összes ún `TelemetryInitializer`s és `TelemetryProcessor`s nevezzük. Ez azt jelenti, hogy minden elem által eldobott `TelemetryProcessor` a csatorna nem érhető el. `Send()` nem általában küld az elemek azonnal a háttérrendszerhez. Általában pufferelt a memóriában, és kötegekben, hatékony küldi el.

[LiveMetrics](live-stream.md) is rendelkezik, amely működteti a telemetriai adatok az élő streamelés egyéni csatornán. Ez a csatorna a normál telemetriai csatorna független, és ez a dokumentum nem vonatkozik a csatorna által használt `LiveMetrics`.

## <a name="built-in-telemetrychannels"></a>Beépített TelemetryChannels

Application Insights.NET/.NET Core SDK-t tartalmaz két beépített csatorna:

* **InMemoryChannel** 
 `InMemoryChannel` egy egyszerűsített csatorna, amely elemek a memóriában puffereli, amíg nem továbbítja. A cikkek a memóriában puffereli és 30 másodpercenként kiürített vagy, ha 500 elemet pufferelt rendelkezik. Ez a csatorna kínál a minimális megbízhatóságot garantálja, azt nem a telemetria küldését hibák esetén próbálkozzon újra. Ez a csatorna nem ne elemek lemezen, így bármely elküldetlen elemek véglegesen alkalmazás leállásakor elvesznek (szabályosan vagy sem). Van egy `Flush()` metódus által a csatornát, amely segítségével kényszerített-kiürítés minden memórián belüli telemetriai elem szinkron módon végrehajtani. Ez a rövid ideig futó alkalmazásokhoz való ahol szinkron annak kiürítése ideális megoldást biztosít.

    Ez a csatorna részeként tartalmazza a szükséges a `Microsoft.ApplicationInsights` nuget csomag magát, és az alapértelmezett csatornán, az SDK-t használja, amikor nincs más van konfigurálva.

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel` van speciális csatorna, amely rendelkezik az újrapróbálkozási szabályzatok és a helyi lemezen lévő adatok tárolását. Ez a csatorna újrapróbálkozik a telemetria küldését, ha átmeneti hiba lép fel. Ez a csatorna is a helyi lemezes tárhelyért használ elemek megőrzése lemezen hálózati kimaradások vagy a magas telemetrikus kötet során. Ezek újrapróbálkozási mechanizmus és a helyi lemezes tárhelyért miatt ez a csatorna megbízhatóbb számít, és az összes üzemi forgatókönyvek esetén ajánlott. Ez a csatorna nem az alapértelmezett [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) és [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) alkalmazások, amelyek a társított hivatalos docs megfelelően vannak konfigurálva. Ez a csatorna a hosszú ideig futó folyamatok szituációk van optimalizálva. A [ `Flush()` ](#which-channel-should-i-use) ezen a csatornán által megvalósított metódus nem szinkron.

    Ezt a csatornát, a NuGet-csomagot tartalmazza a szükséges `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`, és automatikusan állapotúra vagy a NuGet-csomagok használata esetén `Microsoft.ApplicationInsights.Web` vagy `Microsoft.ApplicationInsights.AspNetCore`.

## <a name="configuring-telemetrychannel"></a>TelemetryChannel konfigurálása

Telemetria csatorna konfigurálható a kívánt beállításával `TelemetryChannel` az aktív `TelemetryConfiguration`. Az Asp.Net-alkalmazások konfigurációja magában foglalja a beállítás `TelemetryChannel` a `TelemetryConfiguration.Active`, módosítása vagy `ApplicationInsights.config`. ASP.NET Core-alkalmazások a konfigurációs magában foglalja a kívánt csatornát ad hozzá a függőség injektálási tároló.

Következő egy példa látható felhasználói konfigurálja, a `StorageFolder` csatorna. `StorageFolder` Ez a konfigurálható beállítások csak egyike. A konfigurációs beállítások teljes listáját leírt [beállítások szakaszában](telemetry-channels.md#configurable-settings-in-channel).

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>ApplicationInsights.Config használata az ASP.NET-alkalmazások konfigurálása

A következő szakasz a [ApplicationInsights.config](configuration-with-applicationinsights-config.md) látható a konfigurált ServerTelemetryChannel `StorageFolder` beállítása egy egyéni helyre.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>A kód konfiguráció az ASP.NET-alkalmazások

A következő kódot állít be a ServerTelemetryChannel `StorageFolder` beállítása egy egyéni helyre. Ezt a kódot kell adni az alkalmazás általában a Application_Start() metódus az elején `Global.aspx.cs`

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

## <a name="configuration-in-code-for-aspnet-core-applications"></a>A kód konfiguráció az ASP.NET Core-alkalmazások

Módosítsa `ConfigureServices` metódusa `Startup.cs` osztály az alább látható módon.

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

> [!NOTE]
> Fontos megjegyezni, hogy a csatorna használatával konfigurálása `TelemetryConfiguration.Active` ASP.NET Core-alkalmazások esetében nem javasolt.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>TelemetryChannel.NET/.NET Core-Konzolalkalmazást a code konfigurálása

A konzol alkalmazások esetén a kód ugyanazt a .NET és .NET Core, és az alábbiakban látható.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel működési részletei

A `ServerTelemetryChannel` beérkező elemek puffereli egy memórián belüli puffer. Szerializált, tömöríti és tárolja az `Transmission` példány után minden 30 másodperc, vagy ha 500 elemet pufferelve van. Egyetlen `Transmission` -példány legfeljebb 500 elemet tartalmaz, és az Application Insights szolgáltatás egyetlen https hívása keresztül küldött telemetriai adatok kötegelt jelöli. Alapértelmezés szerint lehet legfeljebb 10 `Transmission`s küld a rendszer párhuzamosan. Ha gyorsabb ütemben tölthetőek telemetriai adatokat, vagy ha a hálózat és az Application Insights-háttérrendszer lassú, majd `Transmission`s első tárolja a memóriába. A memórián belüli átviteli puffer alapértelmezett kapacitása 5 MB-ot. Ha túllépi a memória-kapacitás, `Transmission`s legfeljebb 50 MB-ot a helyi lemezen tárolt. `Transmission`s tárolása a helyi lemezen, valamint a hálózati problémák esetén. Csak a helyi lemezen tárolt elemeket stabilitást biztosít az alkalmazás összeomlik, amely kapnak, amikor az alkalmazást újra elindult.

## <a name="configurable-settings-in-channel"></a>A csatorna konfigurálható beállítások

Minden egyes csatorna konfigurálható beállítások teljes listáját itt a következők:

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Leggyakrabban használt beállításait a `ServerTelemetryChannel` az alábbiak:

1. `MaxTransmissionBufferCapacity` – Memória mérete, a puffer az átvitelt a memóriában csatornák által használt maximális mennyisége. Ez a kapacitás elérésekor új elemek közvetlenül a helyi lemezen tárolja. Az alapértelmezett érték 5 MB-ot. Beállítás értéke érdeklődők alacsonyabb szintű lemezek használata terén, de fontos megjegyezni, hogy a memóriában elemek elvesznek alkalmazás összeomlik.

2. `MaxTransmissionSenderCapacity` – A maximális mennyisége `Transmission`, amelyek küld az Application Insights egy időben. Az alapértelmezett érték 10-es, de nagyobb konfigurálható. Ez javasolt, ha egy hatalmas mennyiségű telemetriai jön létre, általában amikor ezzel a terheléses tesztelés díjaival, illetve amikor a mintavételi ki van kapcsolva.

3. `StorageFolder` – A lemezre, igény szerint elemek tárolására a csatorna által használt mappába. Windows, a % LocalAppData % vagy % Temp % használatos, ha nincs explicit módon van-e konfigurálva. Nem-Windows-környezetekben felhasználói **kell** állítson be egy érvényes helyet, amely nélkül telemetriai nem tárolja a helyi lemezre.

## <a name="which-channel-should-i-use"></a>Mely csatorna használjam?

`ServerTelemetryChannel` hosszú ideig futó kérelmek legtöbb éles környezetben ajánlott. A `Flush()` metódus végrehajtásának `ServerTelemetryChannel` nem teljesen szinkron, és `Flush()` nem garantálja, hogy az összes függőben lévő elemek küldését memória/lemez. Ha ezt a csatornát használja forgatókönyvek, amelyben az alkalmazás arra készül, hogy állítsa le, akkor javasoljuk, hogy némi késedelemmel tegye hívása után `Flush()` ezen a csatornán. Késleltetés a szükséges pontos mennyisége a lehetőség nem kiszámítható, attól függ, például hogy hány elemek tényezők vagy `Transmissions` a memória, lemez, hány folyamatban küldött biztonsági, hány vannak, és ha a csatorna legyen, exponenciális visszatartási forgatókönyvek közepén. Ha nincs szükség szinkron kiürítési, majd `InMemoryChannel` ajánlott.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*Applicationinsights – csatorna telemetriai garantált kézbesítés vagy a forgatókönyvek, ahol telemetriai adatok elvesznek, melyek kínálnak?*

* Rövid válasz, a beépített csatornák egyike garanciát tranzakció típusa kapcsolatos telemetriai kézbesítési a háttérrendszerhez. Miközben `ServerTelemetryChannel` van speciális képest `InMemoryChannel` megbízható telemetriai közvetítésre is készít egy legjobb kísérlet, hogy telemetriát küldjön, telemetriai adatok továbbra is számos forgatókönyv elvész. A közös olyan forgatókönyvekben, ahol telemetriai elveszett a következők:

1. Memóriában lévő elemek elvesznek, ha az alkalmazás összeomlik.
1. Telemetriai adatokat a helyi lemezre memóriaállapota tárolva van során a hálózati kimaradások vagy a problémák az Application Insights-háttérrendszerrel. 24 óránál régebbi elemek azonban elvesznek. Ezért telemetriai közben megszakad a hálózati problémák hosszabb ideig.
1. Lemez alapértelmezett helyét a Windows telemetriai adatok tárolása a következők: % LocalAppData % vagy % Temp %. Ezek a helyek egymástól általában helyi géphez. Ha az alkalmazás áttelepítést végez fizikailag egyik helyről a másikra, semmilyen telemetriai adatot ezen a helyen tárolt elvész.
1. Az Azure Web Apps (Windows) az alapértelmezett lemez helye "D:\local\LocalAppData". Ezen a helyen nem maradnak meg, és törli az alkalmazás újraindul, a méretezési csoport mintáit és így tovább, és azokon a helyeken tárolt telemetriájának. Egy megőrzött helyen, például "D:\home" tároló a felhasználók felülbírálhatják, de a megőrzött helyek ez alatt a távtároló szolgáltatja, és a lassú lehet.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*A nem Windows rendszerek működik ServerTelemetryChannel?*

* Ez a csatorna annak ellenére, hogy folyamatban van a WindowsServer csomag/névtér nevét, a következő kivétel miatt nem Windows rendszerek használata támogatott. A nem-Windows a csatorna nem hozzon létre egy helyi tároló alapértelmezés szerint. Felhasználók kell hozzon létre egy helyi mappát, és használhatja azt a csatornát konfigurálni. Ha a helyi tároló úgy van konfigurálva, a csatorna ugyanúgy működik a Windows és a nem Windows rendszerek.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*Hozzon létre ideiglenes helyi tárhely az SDK-t? Tárolási titkosítja az adatokat?*

* SDK tárolja a telemetriai elemeket a helyi tárban hálózati problémák vagy a szabályozás során. Ezek az adatok helyileg nincsenek titkosítva.
A Windows rendszerek esetében az SDK automatikusan TEMP vagy APPDATA mappában hozza létre a helyi ideiglenes mappát, és korlátozza a hozzáférést a rendszergazdák és csak az aktuális felhasználó.
Nem-Windows, a helyi tárterület az SDK-ban automatikusan létrejön, és ezért nem tárol adatokat helyben alapértelmezés szerint. A felhasználók maguk tároló könyvtár létrehozása, és használhatja azt a csatornát konfigurálni. Ebben az esetben a felhasználó nem ez a könyvtár védett biztosításáért felelős.
Tudjon meg többet [adatvédelem](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open-source SDK
Minden Application Insights SDK-k, például a csatornák olyan is nyílt forráskódú. Olvassa el és járulnak hozzá a kódot, vagy a problémák jelentése [a hivatalos GitHub-adattárat](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>További lépések

* [Mintavételezés](../../azure-monitor/app/sampling.md)
* [SDK hibáinak elhárítása](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)

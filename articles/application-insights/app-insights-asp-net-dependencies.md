---
title: Az Azure Application Insights nyomkövetési függőségi |} Microsoft Docs
description: Az Application Insights segítségével elemezheti a használati adatokat, a rendelkezésre állást és a teljesítményt a helyszíni vagy Microsoft Azure webalkalmazásán.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: mbullwin
ms.openlocfilehash: 7023ce1c9d8a115ae791d40c5d40a5b5d1fabed9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598385"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Az Application Insights beállítása: függőségi nyomon követése
A *függőségi* az alkalmazás által külső összetevője. Általában le egy HTTP, vagy egy adatbázist vagy egy fájlrendszer nevű szolgáltatás. [Az Application Insights](app-insights-overview.md) méri, hogy az alkalmazás meddig függőségek, és milyen gyakran függőségi hívás sikertelen lesz. Vizsgálja meg az adott hívások, és összekapcsolhatja őket a kéréseket, és kivételeket.

![mintadiagramok](./media/app-insights-asp-net-dependencies/10-intro.png)

A ki-beépített a függőségi figyelő jelenleg az ilyen típusú függőségek hívásainak jelentések:

* Kiszolgáló
  * SQL Database-adatbázisok
  * ASP.NET webes és WCF-szolgáltatások HTTP-alapú kötések használó
  * Helyi vagy távoli HTTP-hívások
  * Az Azure Cosmos DB, a tábla, a blob-tároló és a várólista
* Weblapok
  * AJAX-hívások

Figyelés works segítségével [bájt kód instrumentation](https://msdn.microsoft.com/library/z9z62c29.aspx) kijelölt módszerek köré. Teljesítményigény minimális.

A saját SDK-hívásokat más függőségek, mind az ügyfél és kiszolgáló-kódban, figyelése is írhat használatával a [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

## <a name="set-up-dependency-monitoring"></a>A függőségi figyelés beállítása
Részleges függőségi szolgáltatás által gyűjtött információk automatikusan a [Application Insights SDK](app-insights-asp-net.md). Ahhoz, hogy a teljes adatokat, telepítse a megfelelő ügynököt, hogy a gazdagép-kiszolgálón.

| Platform | Telepítés |
| --- | --- |
| IIS-kiszolgálón |Vagy [Állapotmonitor telepítése a kiszolgálón](app-insights-monitor-performance-live-website-now.md) vagy [frissíteni az alkalmazást a .NET-keretrendszer 4.6-os vagy újabb](http://go.microsoft.com/fwlink/?LinkId=528259) és telepítse a [Application Insights SDK](app-insights-asp-net.md) az alkalmazásban. |
| Azure Web App |A webes alkalmazás Vezérlőpult [nyissa meg az Application Insights panelt a webes alkalmazás Vezérlőpult](app-insights-azure-web-apps.md) , és válassza a telepítés, ha a rendszer kéri. |
| Azure Cloud Service |[Használjon indítási tevékenységhez](app-insights-cloudservices.md) vagy [telepítse a .NET-keretrendszer 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Hol található a függőségi adatokat
* [Alkalmazás-hozzárendelés](#application-map) visualizes az alkalmazás és a szomszédos összetevők közötti függőségek.
* [Teljesítmény, a böngésző és a hiba paneleken](#performance-and-blades) kiszolgáló függőségi adatainak megjelenítése.
* [Böngészők panel](#ajax-calls) jeleníti meg a felhasználók böngészőjének az AJAX-hívások.
* [A lassú és a sikertelen kérelmek átkattintással](#diagnose-slow-requests) ellenőrizze a függőségi hívásokat.
* [Elemzés](#analytics) függőségi adatait is használható.

## <a name="application-map"></a>Alkalmazástérkép
Alkalmazás-hozzárendelés úgy működik, mint a visual támogatás felderítéséhez az alkalmazás összetevői közötti függőségek. Ez az alkalmazás a telemetriai adatok automatikusan jönnek létre. Ez a példa bemutatja az AJAX-hívások, a böngésző parancsfájlok és a két külső szolgáltatások a server App REST-hívások.

![Alkalmazástérkép](./media/app-insights-asp-net-dependencies/08.png)

* **Keresse meg a mezőkben** kapcsolódó függőségi és más típusú diagramokkal.
* **A térkép rögzítése** számára a [irányítópult](app-insights-dashboards.md), ahol azt teljesen működőképes lesz.

[További információk](app-insights-app-map.md).

## <a name="performance-and-failure-blades"></a>Teljesítmény és hibák paneleken
A teljesítmény panelen látható a server app függőségi hívások időtartama. Létrejön egy összegző diagram és hívása szegmentált tábla.

![Teljesítmény panel függőségi diagramjain](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

Kattintson az összegző diagram vagy a tábla elemek hívásokat a nyers előfordulását kereséséhez.

![Függőségi hívás példányok](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

**Hiba száma** jelennek meg a **hibák** panelen. Hiba az összes visszatérési kódot, amely nem szerepel a tartomány 200-399, vagy ismeretlen.

> [!NOTE]
> **100 %-os hibák?** -Ez valószínűleg azt jelzi, hogy csak kap a részleges függőségi adatokat. Kell [függőségi figyelés beállítása a platformhoz megfelelő](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>AJAX-hívások
A böngészők megjelenik az AJAX-hívások időtartama és sikertelen arányát [JavaScript a weblapok](app-insights-javascript.md). Szerint függőségeinek megtekinthető.

## <a name="diagnosis"></a> Lassú kérelmek diagnosztizálására
Minden egyes kérelem esemény társítva a függőségi hívások esetében, kivételeket és eseményeket, amelyek nyomon követi az alkalmazás a kérelem feldolgozása közben. Így bizonyos kérelmek rosszul hajtja végre, ha azt is megtudhatja, hogy van-e lassú válaszainak függőség miatt.

Bemutatjuk, például, hogy keresztül.

### <a name="tracing-from-requests-to-dependencies"></a>Nyomkövetés a kérelmek függőségek
A teljesítmény panel megnyitásához, és nézze meg a rács kérelmek:

![Lista átlagok és száma](./media/app-insights-asp-net-dependencies/02-reqs.png)

A felső egy a nagyon hosszú időt vesz igénybe. Nézzük meg, ha azt is megtudhatja, hol van töltött idő.

Kattintson az egyes eseményeket a sorhoz:

![A kérelem események listája](./media/app-insights-asp-net-dependencies/03-instances.png)

Kattintson a hosszan futó példány vizsgálja meg további, és görgessen le a távoli függőségi hívások esetében kapcsolódik ehhez a kérelemhez:

![Található távoli függőségekre irányuló hívás, azonosíthatja a szokatlan időtartama](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Úgy tűnik a legtöbb ehhez a kérelemhez telt helyi hívásában karbantartási idő.

Válassza ki a sorhoz további információkat:

![A probléma oka azonosításához távoli függőséget átkattintással](./media/app-insights-asp-net-dependencies/05-detail.png)

A jelek szerint ez az a probléma. Jelenleg a probléma már pinpointed, ezért most csak azt szeretné tudni, miért adott hívás tart sokáig.

### <a name="request-timeline"></a>Az idősor kérése
Más esetben nem függőségi hívás különösen hosszú van. De idősor nézetének váltásával láthatja Ha a késés a saját belső feldolgozása során:

![Található távoli függőségekre irányuló hívás, azonosíthatja a szokatlan időtartama](./media/app-insights-asp-net-dependencies/04-1.png)

Úgy tűnik, hogy nagy hiány után az első függőségi hívás, ezért úgy kell tekintünk kódban, hogy miért, a rendszer.

### <a name="profile-your-live-site"></a>Élő webhelyét profilját

Nem tudja, ahol a idő kerül? A [Application Insights Profilkészítő](app-insights-profiler.md) nyomkövetések HTTP meghívja az élő helyre, és megjeleníti a funkciókról a kódban a leghosszabb időt vett.

## <a name="failed-requests"></a>Sikertelen kérelmek
Lehet, hogy a sikertelen kérelmek is társítva függőségek hívásainak sikertelen. Ebben az esetben azt átkattintással is nyomon követheti a problémát.

![Kattintson a sikertelen kérelmek diagram](./media/app-insights-asp-net-dependencies/06-fail.png)

A sikertelen kérelmek előfordulása átkattintással, és nézze meg a vonatkozó események.

![Kattintson a kérés típusa, az beszerzése az ugyanazon példányának egy másik nézetre, kattintson rá a kivétel részletes-példányt.](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Elemzés
A függőségek nyomon követheti a [Log Analytics lekérdezési nyelv](https://docs.loganalytics.io/). Néhány példa:

* Keresse meg a sikertelen függőségi hívások esetében:

```

    dependencies | where success != "True" | take 10
```

* AJAX-hívások keresése:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Keresse meg a kérelmek társított függőségi hívások esetében:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Lapmegtekintések társított AJAX-hívások keresése:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Egyéni függőségi nyomon követése
A szabványos függőségi követése modul automatikusan észleli a külső függőségei, például adatbázisok és a REST API-k. De érdemes lehet néhány további összetevők azonos módon kell kezelni.

Írhat kódot, amely a függőségi adatokat küld azonos [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) , amely a globális modulok használják.

Ha saját maga nem írhatók szerelvény használjon a kódot, például sikerült minden hívást, hogy megtudja, milyen úgy a válaszidők hozzájárulás időt. Ahhoz, hogy az Application Insights függőségi diagramjain jelennek meg adatok, elküldi a használatával `TrackDependency`.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Ha azt szeretné, hogy kikapcsolja a szabványos függőségi követési modul, a DependencyTrackingTelemetryModule hivatkozásának eltávolítására [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Hibaelhárítás
*Függőségi sikerességét jelző mindig igaz vagy hamis értéket jeleníti meg.*

*Nem jelenik meg a teljes SQL-lekérdezésben.*

Tekintse át az alábbi táblázat, és biztosítását úgy döntött, hogy a megfelelő konfigurációját, és engedélyezze a függőségi figyelő alkalmazás.

| Platform | Telepítés |
| --- | --- |
| IIS-kiszolgálón |Vagy [Állapotmonitor telepítése a kiszolgálón](app-insights-monitor-performance-live-website-now.md). Vagy [frissíteni az alkalmazást a .NET-keretrendszer 4.6-os vagy újabb](http://go.microsoft.com/fwlink/?LinkId=528259) és telepítse a [Application Insights SDK](app-insights-asp-net.md) az alkalmazásban. |
| Azure Web App |A webes alkalmazás Vezérlőpult [nyissa meg az Application Insights panelt a webes alkalmazás Vezérlőpult](app-insights-azure-web-apps.md) , és válassza a telepítés, ha a rendszer kéri. |
| Azure Cloud Service |[Használjon indítási tevékenységhez](app-insights-cloudservices.md) vagy [telepítse a .NET-keretrendszer 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések
* [Kivételek](app-insights-asp-net-exceptions.md)
* [Felhasználók és Lapadatok](app-insights-javascript.md)
* [Rendelkezésre állás](app-insights-monitor-web-app-availability.md)

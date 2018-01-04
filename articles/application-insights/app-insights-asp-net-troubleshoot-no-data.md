---
title: "Adathiány hibaelhárítása – Application Insights .NET-hez"
description: "Nem jelennek meg adatok az Azure Application Insights? Próbálja meg itt."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 951a3217d795df6360cd3cfa2d47db08c11f978e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Adathiány hibaelhárítása – Application Insights .NET-hez
## <a name="some-of-my-telemetry-is-missing"></a>A telemetriai adatok némelyike hiányzik
*Az Application Insightsban az alkalmazás által létrehozott eseményeket töredéke alatt csak látható.*

* Ha egységesen azonos mekkora részét, ez oka feltehetőleg adaptív [mintavételi](app-insights-sampling.md). Ennek ellenőrzéséhez nyissa meg a keresési (a paneljéről áttekintése), és tekintse meg egy kérelem vagy más esemény példánya. A Tulajdonságok szakaszának alján kattintson a "...", teljes tulajdonság részletes. Ha a kérelmek száma > 1, majd mintavételi működik. 
* Ellenkező esetben, akkor előfordulhat, hogy Ön elérte van-e egy [sávszélesség-korlátjának](app-insights-pricing.md#limits-summary) a árképzési terv. Ezek a korlátozások érvényesek / perc.

## <a name="no-data-from-my-server"></a>Nincs adat a kiszolgálóról
*Saját alkalmazás a webkiszolgálón telepített, és be van kapcsolva, nem lát minden telemetriai belőle. A fejlesztői gépen eredménnyel járt OK gombra.*

* Valószínűleg a tűzfallal kapcsolatos probléma. [Állítsa be a tűzfal kivételeit az Application Insights adatküldés](app-insights-ip-addresses.md).
* IIS-kiszolgálón hiányoznak egyes Előfeltételek: a .NET-bővíthetőség 4.5 és ASP.NET 4.5.

*I [állapotfigyelő telepítve](app-insights-monitor-performance-live-website-now.md) a meglévő alkalmazások figyelése a webkiszolgálón. Nem szerepel az eredmények.*

* Lásd: [állapotfigyelő hibaelhárítási](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights). 

## <a name="q01"></a>Nincs "Application Insights hozzáadása" lehetőséget a Visual Studio
*Az egér jobb gombjával egy meglévő projektben a Megoldáskezelőre, nem szerepel az Application Insights beállításokat.*

* Nem minden típusú .NET projekt támogatja az eszközök által. Webes és WCF-projektek támogatottak. Más projekttípusok például asztali vagy service alkalmazások még [az Application Insights SDK manuális hozzáadása a projekthez](app-insights-windows-desktop.md).
* Győződjön meg arról, hogy [Visual Studio 2013 Update 3-as vagy újabb](http://go.microsoft.com/fwlink/?LinkId=397827). Előre telepített és a fejlesztői Analytics eszközök, amelyek az Application Insights SDK származik.
* Válassza ki **eszközök**, **bővítmények és frissítések** és ellenőrizze, hogy **Analytics Fejlesztőeszközök** van telepítve és engedélyezve van. Ha igen, kattintson a **frissítések** ha van egy frissítés.
* Az új projekt párbeszédpanel megnyitásához, és válassza ki az ASP.NET-webalkalmazás. Az Application Insights beállítás nem jelenik meg, ha az eszközök vannak telepítve. Ha nem, akkor távolítsa el, majd újból telepíti az Application Insights-eszközökkel.

## <a name="q02"></a>Az Application Insights hozzáadása nem sikerült
*Application Insights hozzáadása egy meglévő projektjébe, ha egy hibaüzenet jelenik meg.*

Valószínű okokat:

* Nem sikerült az Application Insights portáljáról kommunikációt; vagy
* Probléma lép fel az Azure-fiókja;
* Csak akkor kell [olvasási hozzáférés az előfizetés vagy csoportot próbált, ahol az új erőforrás létrehozása](app-insights-resources-roles-access-control.md).

Javítás:

* Ellenőrizze, hogy a jobb oldali Azure-fiók bejelentkezési hitelesítő adataival megadott. 
* A böngészőben, ellenőrizze, hogy van-e a hozzáférést a [Azure-portálon](https://portal.azure.com). Nyissa meg a beállításokat, és hogy van-e korlátozás.
* [Az Application Insights hozzáadása a meglévő projekthez](app-insights-asp-net.md): A Megoldáskezelőben kattintson a jobb gombbal a projekt, és válassza az "Application Insights hozzáadása."
* Ha az eszköz még nem működik, kövesse a [manuális eljárás](app-insights-windows-services.md) erőforrás hozzáadása a portálon, és az SDK hozzáadása a projekthez. 

## <a name="emptykey"></a>Hiba jelenik meg: "Instrumentation kulcs nem lehet üres"
A jelek szerint valami hiba történt, miközben telepíti a Application Insights vagy lehet, hogy a naplózás adapter.

A Megoldáskezelőben kattintson jobb gombbal a projektre, és válassza a **Application Insights > konfigurálja az Application Insights**. Olyan párbeszédpanel, amely felkéri, hogy jelentkezzen be az Azure-bA kap, és létrehozza az Application Insights-erőforrást, vagy használja ismét egy meglévőt.

## <a name="NuGetBuild"></a>"NuGet csomag hiányoznak a" saját build kiszolgálón
*Minden buildek OK I vagyok hibakeresés a fejlesztési számítógépen, de a NuGet hiba jelenik meg a build kiszolgálón.*

Ellenőrizze a [NuGet-csomagok visszaállításának](http://docs.nuget.org/Consume/Package-Restore) és [automatikus csomagok visszaállításának](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Hiányzó parancs Visual Studio Application Insights megnyitása
*Az egér jobb gombjával a projekt megoldáskezelő, nem szerepel az Application Insights parancsok, vagy nem szerepel egy olyan nyissa meg az Application Insights-parancsot.*

Valószínű okokat:

* Ha manuálisan hozza létre az Application Insights-erőforrást, vagy ha a projekt, amely az Application Insights-eszközök által nem támogatott típusú.
* A fejlesztői elemzőeszközök le vannak tiltva, a Visual Studio. 
* A Visual Studio 2013 Update 3-nál régebbi.

Javítás:

* Ellenőrizze, hogy a Visual Studio verziója 2013 update 3-as vagy újabb.
* Válassza ki **eszközök**, **bővítmények és frissítések** és ellenőrizze, hogy **fejlesztői elemzőeszközök** van telepítve és engedélyezve van. Ha igen, kattintson a **frissítések** ha van egy frissítés.
* Kattintson a jobb gombbal a projektben a Megoldáskezelőre. Ha a parancs **Application Insights > konfigurálja az Application Insights**, csatlakozzon a projekthez az Application Insights szolgáltatásban erőforrás segítségével.

Ellenkező esetben a projekttípus nem közvetlenül támogatja az Application Insights-eszközökkel. A telemetriai adatok megtekintéséhez jelentkezzen be a [Azure-portálon](https://portal.azure.com)Application Insights válassza a bal oldali navigációs sávon, és válassza ki az alkalmazást.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"A hozzáférés megtagadva" a Visual Studio Application Insights megnyitása
*A "Nyissa meg az Application Insights" parancs veszi át, me az Azure portálon, de "hozzáférés megtagadva" hiba jelenik meg.*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

A Microsoft-bejelentkezés az alapértelmezett böngésző legutóbb használt nem fér hozzá [készült Application Insights az alkalmazáshoz való hozzáadásakor az erőforrás](app-insights-asp-net.md). Két oka valószínűleg: 

* Egynél több Microsoft-fiók – lehet, hogy a munkahelyi és személyes Microsoft-fiókkal van? A bejelentkezés az alapértelmezett böngésző legutóbb használt egy másik fiókot, amely hozzáféréssel rendelkezik egy volt [Application Insights hozzáadása a projekthez](app-insights-asp-net.md). 
  
  * Javítás: Kattintson a nevére, a böngészőablak jobb felső, és jelentkezzen ki. Jelentkezzen be a fiók, amely hozzáféréssel rendelkezik. A bal oldali navigációs sávon, majd kattintson az Application Insights, és válassza ki az alkalmazást.
* Az Application Insights valaki más hozzáadni a projekthez, és Önnek elfelejtette [az erőforráscsoport elérésére](app-insights-resources-roles-access-control.md) a, amelyen létrehozták. 
  
  * Javítás: Azok a használatához szervezeti fiók azok adhat hozzá, a csapat; akkor is, egyes való hozzáférés engedélyezése vagy az erőforráscsoportot.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Eszköz nem található" a Visual Studio Application Insights megnyitása
*A "Nyissa meg az Application Insights" parancs veszi át, me az Azure portálon, de "az eszköz nem található" hiba jelenik meg.*

Valószínű okokat:

* Az Application Insights-erőforrást az alkalmazáshoz törölve lett; vagy
* A instrumentation kulcs lett beállítva, vagy applicationinsights.config módosítja a közvetlen szerkesztése a projektfájl frissítése nélkül. 

A rendszerállapot kulcs ApplicationInsights.config vezérlők, ahol a telemetriai adatok küldése. Egy sor a következő projektfájlban szabályozza, hogy melyik erőforrást meg van nyitva, ha a parancs a Visual Studióban. 

Javítás:

* A Megoldáskezelőben kattintson a jobb gombbal a projektre, és válassza ki az Application Insights, konfigurálja az Application Insights. A párbeszédpanelen választhat telemetriai adatokat küldeni a meglévő erőforrás, vagy hozzon létre egy újat. Vagy:
* Nyissa meg az erőforrás közvetlenül. Jelentkezzen be [az Azure-portálon](https://portal.azure.com), és a bal oldali navigációs sávon kattintson az Application Insights, majd válassza ki az alkalmazást.

## <a name="where-do-i-find-my-telemetry"></a>Hol található a telemetriai adatokat?
*Bejelentkezve szeretnék a [Microsoft Azure-portálon](https://portal.azure.com), és szeretnék vagyok megnézi a Azure otthoni irányítópult. Ezért hol található az Application Insights adataimat?*

* A bal oldali navigációs sávon kattintson az Application Insights, majd az alkalmazás neve. Ha bármely projektek nincs hiba, akkor [hozzáadásához vagy konfigurálásához az Application Insights a webes projekt](app-insights-asp-net.md).
  
    Van néhány összefoglaló diagramok megjelenik. Kattinthat, révén azokat a részletek megtekintéséhez.
* A Visual Studióban amíg az alkalmazás hibakeresése, kattintson a Application Insights gombra.

## <a name="q03"></a>Nincs server-adatok (vagy egyáltalán nem adatok)
*Szeretnék saját alkalmazás futott, és majd megnyitni az Application Insights szolgáltatás a Microsoft Azure-ban, de a diagram megjelenítése "Megtudhatja, hogyan gyűjtéséhez..." vagy "Nincs beállítva."* Másik lehetőségként *csak nézet és a felhasználói adatok, de nem a server-adatok.*

* Futtassa az alkalmazást hibakeresési módban, a Visual Studio (F5). Használja az alkalmazást úgy, hogy néhány telemetriai készítése. Ellenőrizze, hogy látja-e a Visual Studio kimeneti ablakában naplózott eseményeket. 
  
    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)
* Nyissa meg az Application Insights portáljáról [diagnosztikai keresési](app-insights-diagnostic-search.md). Adatok általában itt jelenik meg először.
* Kattintson a frissítés gombra. A panel rendszeres időközönként frissíti magát, de manuálisan is végrehajthatja. A frissítési időköz áll a nagyobb idő tartományoknál.
* Ellenőrizze, hogy a instrumentation kulcsok felel meg. Az Application Insights portálon, az alkalmazás fő panelen a a **Essentials** legördülő, nézze meg **Instrumentation kulcs**. Ezt követően a Visual Studio projekt, nyissa meg az ApplicationInsights.config és található a `<instrumentationkey>`. Ellenőrizze, hogy a két kulcs egyenlő. Ha nem:
  
  * A portálon kattintson az Application Insights, és keresse meg az alkalmazás-erőforrást a megfelelő kulccsal; vagy
  * A Visual Studio Solution Explorerben kattintson jobb gombbal a projektre, és válassza az Application Insights konfigurálása. Állítsa vissza az alkalmazásnak, hogy a megfelelő erőforrás a telemetriai adatokat küldhet.
  * Ha nem találja a megfelelő kulcsokat, ellenőrizze, hogy használja a bejelentkezési hitelesítő adatokkal és a Visual Studio a portálra.
    
    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
* Az a [Microsoft Azure otthoni irányítópult](https://portal.azure.com), nézze meg a szolgáltatás állapotát leképezés. Ha néhány riasztási jelzések, várjon, amíg azok vissza kellett volna OK majd zárja be és nyissa meg újra az Application Insights-alkalmazás paneljének.
* Ellenőrizze azt is [az állapot blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Adta meg a kód írása a [kiszolgálóoldali SDK](app-insights-api-custom-events-metrics.md) , előfordulhat, hogy módosítsa a rendszerállapot-kulcsot a `TelemetryClient` példányok vagy a `TelemetryContext`? Vagy adott ír egy [szűrő vagy mintavételi](app-insights-api-filtering-sampling.md) , előfordulhat, hogy szűrés kimenő túl sok?
* Ha módosította az ApplicationInsights.config, gondosan ellenőrizze konfigurációját [TelemetryInitializers és TelemetryProcessors](app-insights-api-filtering-sampling.md). Egy nem megfelelően nevű típus vagy paraméter okozhat az SDK nem adatküldéshez.

## <a name="q04"></a>Nincsenek a lapmegtekintések, böngészők, használati adatok
*Nézet Lapbetöltési idő, illetve a böngésző vagy a használati paneleken kiszolgálói válaszidő-és kiszolgálói kérelmek adatainak, azonban adatot nem láthatók.*

Az adatok a weblapok parancsfájlok származik. 

* Ha egy meglévő webes projektet hozzáadta az Application Insights [manuálisan adja hozzá azokat a parancsprogramokat, hogy](app-insights-javascript.md).
* Győződjön meg arról, hogy az Internet Explorer kompatibilitási módban nem jelennek meg a helyet.
* A böngésző hibakeresési szolgáltatás (F12 az egyes böngészők, majd válassza a hálózati) használatával győződjön meg arról, hogy adatokat küld a `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Adatot nem függőségi vagy kivétel
Lásd: [– függőségi telemetria](app-insights-asp-net-dependencies.md) és [kivételtelemetria](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Nincs teljesítményadatokat
Teljesítményadatok (CPU, IO sebessége, és így tovább) érhető el [Java web services](app-insights-java-collectd.md), [Windows asztali alkalmazások](app-insights-windows-desktop.md), [IIS webes alkalmazások és szolgáltatások telepítése állapotfigyelő](app-insights-monitor-performance-live-website-now.md), és [Azure Felhőszolgáltatások](app-insights-azure.md). a beállítások kiszolgálók találhat.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Nincs (kiszolgáló) adat, mivel az alkalmazás közzétett a kiszolgálóhoz
* Ellenőrizze, hogy ténylegesen másolta a Microsoft. A kiszolgálóhoz, és Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll ApplicationInsights dll-fájlok
* A tűzfal, lehetséges, hogy a [nyissa meg az egyes TCP-portok](app-insights-ip-addresses.md).
* Ha a vállalati hálózaton kívüli küldése használatához állítsa be a proxy használatát [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) a Web.config fájlban
* Windows Server 2008: Ellenőrizze, hogy telepítette a következő frissítéseket: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Adatok megjelenítéséhez használt, de leállt
* Ellenőrizze a [állapot blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Elérte a havi kvótát, az adatokat? Nyissa meg a beállítások/kvóta és az árazás megállapítása. Ha igen, frissítse a csomagot, vagy további kapacitást kell fizetnie. Tekintse meg a [séma árképzési](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>I vagyok várt adatok nem látható
Ha az alkalmazás nagy mennyiségű adatot küld, és az Application Insights SDK for ASP.NET verzió 2.0.0-beta3 vagy újabb, használja a [adaptív mintavételi](app-insights-sampling.md) szolgáltatás működtetésében, valamint csak százalékaként a telemetriai adatok küldése. 

Bármikor letilthatja, de ez nem ajánlott. Mintavételi célja, hogy a kapcsolódó telemetriai adat megfelelően továbbítani, diagnosztikai célokra. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>A felhasználó telemetriai helytelen földrajzi adatokat
A város, régió és ország dimenziók IP-címek alapján, és nem minden esetben pontosak.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>„A metódus nem található” kivétel az Azure Cloud Servicesben futó rendszeren
A .NET 4.6-os verziójára készítette el az alkalmazást? Az Azure Cloud Services szerepkörei nem támogatják automatikusan a 4.6-os verziót. [Telepítse a 4.6-os verziót mindegyik szerepkörön](../cloud-services/cloud-services-dotnet-install-dotnet.md), mielőtt futtatná az alkalmazást.

## <a name="still-not-working"></a>Még nem folyamatban...
* [Application Insights fórum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


---
title: Azure Application Insights Snapshot Debugger .NET-alkalmazások |} A Microsoft Docs
description: Hibakeresési pillanatképek automatikusan gyűjteni, amikor a .NET-alkalmazások éles kivételek
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: brahmnes
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: 669b4d65798a553188a2b99080b72ffc7cd9e898
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890576"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>A .NET-alkalmazások kivételeinek hibakeresési pillanatképei
Ha egy kivétel lép fel, automatikusan gyűjtheti hibakereső pillanatképek az élő webalkalmazások. A pillanatkép abban a pillanatban a kivétel történt a Forráskód és a változók állapotát mutatja. A pillanatkép-hibakereső (előzetes verzió) a [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) kivétel telemetriát a webalkalmazás figyeli. Ez adatokat gyűjt a pillanatképek a felső értesítő kivételek, hogy rendelkezik diagnosztizálhatja a problémákat, éles környezetben a szükséges információkat. Tartalmazza a [Snapshot collector NuGet-csomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) az alkalmazásban, és szükség esetén konfigurálja a gyűjtési paramétereket lévő [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). A pillanatképek jelennek meg [kivételek](../../azure-monitor/app/asp-net-exceptions.md) az Application Insights portálon.

A portálon a hibakeresési pillanatfelvételeket megtekintve láthatja a hívásvermet és megvizsgálhatja a változókat az egyes hívásveremkeretekre vonatkozóan. A hatékonyabb hibakeresési környezetben a forráskóddal lekéréséhez nyissa meg a Visual Studio 2017 Enterprise pillanatképeket. A Visual Studióban is [állítsa be az interaktív módon pillanatfelvételeket Snappoints](https://aka.ms/snappoint) kivétel nélkül.

Hibakeresési pillanatképek hét napig tárolja. A megőrzési házirend van beállítva a alkalmazásonként. Ha ez az érték növelése van szüksége, kérheti növelését az Azure Portalon nyissa meg egy támogatási esetet.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Az alkalmazás Application Insights Snapshot Debugger engedélyezése
Pillanatkép-gyűjtemény érhető el:
* .NET-keretrendszer és az ASP.NET alkalmazások futtatása a .NET-keretrendszer 4.5-ös vagy újabb.
* A Windows futó .NET core 2.0 és az ASP.NET Core 2.0 alkalmazásokat.

A következő környezetekben támogatottak:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 4 vagy újabb verziót futtató a operációsrendszer-család
* [Az Azure Service Fabric-szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) fut a Windows Server 2012 R2 vagy újabb
* [Az Azure virtuális gépek és virtuálisgép-méretezési csoportokban](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) futó Windows Server 2012 R2 vagy újabb
* [A helyi virtuális vagy fizikai gépeken](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) futó Windows Server 2012 R2 vagy újabb

> [!NOTE]
> Ügyfélalkalmazások (például a WPF, Windows Forms vagy UWP) használata nem támogatott.

Ha engedélyezve van a pillanatkép-hibakereső, de a pillanatképek nem lát, ellenőrizze, hogy [hibaelhárítási útmutatója](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Engedélyek megadása

A pillanatképek hozzáférést szerepköralapú hozzáférés-vezérlés (RBAC) védi. Vizsgálhatja meg a pillanatképet, meg kell először adni a megfelelő szerepkör előfizetés tulajdonosa.

> [!NOTE]
> Tulajdonosok és a közreműködőknek nem automatikusan rendelkezik ehhez a szerepkörhöz. Ha szeretne pillanatképek megtekintése, hozzá kell magukat a szerepkört.

Előfizetés-tulajdonost kell rendelni a `Application Insights Snapshot Debugger` szerepkör a felhasználók számára, akik pillanatképek vizsgálata. Ehhez a szerepkörhöz is hozzárendelhető az egyes felhasználók vagy csoportok szerint az előfizetés-tulajdonosokat a cél Application Insights-erőforrást, vagy a erőforráscsoportba vagy előfizetésbe.

1. Keresse meg az Application Insights-erőforrást az Azure Portalon.
1. Kattintson a **hozzáférés-vezérlés (IAM)**.
1. Kattintson a **+ szerepkör-hozzárendelés hozzáadása** gombra.
1. Válassza ki **Application Insights Snapshot Debugger** származó a **szerepkörök** legördülő listából.
1. Keresse meg, és adja meg a hozzáadni kívánt felhasználó nevét.
1. Kattintson a **mentése** gombra kattintva adja hozzá a felhasználót a szerepkörhöz.


> [!IMPORTANT]
> A pillanatképek potenciálisan tartalmazó változót és a paraméter értékét a személyes és egyéb bizalmas adatokat.

## <a name="view-snapshots-in-the-portal"></a>A pillanatképek megtekintése a portálon

Miután kivétel történt az alkalmazásban, és létrejött egy pillanatkép, rendelkeznie kell a pillanatképek megtekintése. Kivétel lépett fel a pillanatkép elkészítéséhez és megtekinthető a portálról származó 5-10 percet is igénybe vehet. Pillanatképek megtekintése a **hiba** panelen válassza a **műveletek** megtekintésekor gombra a **műveletek** lapon, vagy válassza ki a **kivételek**megtekintésekor gombra a **kivételek** lapon:

![Hibák lap](./media/snapshot-debugger/failures-page.png)

Válassza ki egy műveletet, vagy a kivétel megnyitásához a jobb oldali ablaktáblán a **végpontok közötti tranzakció részletei** ablaktáblán, majd válassza ki a kivétel esemény. Ha az adott kivételt érhető el pillanatkép egy **hibakeresési pillanatfelvétel megnyitása** gomb jelenik meg a részleteket a jobb oldali ablaktáblán a [kivétel](../../azure-monitor/app/asp-net-exceptions.md).

![Hibakeresési pillanatfelvétel megnyitása gombjára kivétel](./media/snapshot-debugger/e2e-transaction-page.png)

A hibakeresési pillanatfelvétel nézetben láthatja a hívási verem és a egy változók panelen. Keretek, a hívási veremben a hívási verem panelen válassza ki, ha helyi változók is megtekintheti, és a Változók panelen hívja a függvény paramétereit.

![Hibakeresési Pillanatfelvétel megtekintése a portálon](./media/snapshot-debugger/open-snapshot-portal.png)

A pillanatképek bizalmas információkat tartalmazhatnak, és alapértelmezés szerint nem látható. Pillanatképek megtekintése, rendelkeznie kell a `Application Insights Snapshot Debugger` Önhöz hozzárendelt szerepkörrel.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>A Visual Studio 2017 Enterprise vagy újabb pillanatképeinek megtekintése
1. Kattintson a **Pillanatfelvétel letöltése** gombra kattintva töltse le a `.diagsession` fájlt, amely a Visual Studio 2017 Enterprise nyithatja meg.

2. Megnyitásához a `.diagsession` fájl, rendelkeznie kell a pillanatkép-hibakereső VS összetevő telepítve van. A pillanatkép-hibakereső összetevő egy szükséges összetevő az ASP.net munkaterhelés a VS-ben és a VS-telepítő egyes összetevők listájából választható ki. Ha a Visual Studio 2017 15.5 előtti verziót használ, telepítenie kell a bővítmény a [VS marketplace](https://aka.ms/snapshotdebugger).

3. A pillanatkép-fájl megnyitása után az tömörített memóriaképet hibakeresés a Visual Studióban megjelenik. Kattintson a **felügyelt kód hibakeresése** a pillanatkép-hibakeresés elindításához. A pillanatkép megnyitása, ahol a kivétel lépett fel, hogy a folyamat aktuális állapota is hibakeresése kódsort.

    ![A Visual Studióban nézet hibakeresési pillanatkép](./media/snapshot-debugger/open-snapshot-visualstudio.png)

A letöltött pillanatképet tartalmaz szimbólum fájlokat, a webalkalmazás-kiszolgáló található. A szimbólum fájlok a pillanatkép adatainak társítandó forráskód szükségesek. Az App Service-alkalmazások esetén ügyeljen arra, hogy engedélyezi a szimbólum központi telepítést, a web apps közzétételekor.

## <a name="how-snapshots-work"></a>A pillanatképek működése

A pillanatképek adatgyűjtője van megvalósítva egy [Application Insights Telemetria processzor](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Az alkalmazás futtatásakor az alkalmazás telemetriai folyamat hozzáadódik a Snapshot Collector Telemetriai processzor.
Minden alkalommal, amikor az alkalmazás hívások [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), a pillanatképek adatgyűjtője kiszámítja a kivétel és a rtesítő metódus probléma azonosítója.
Minden alkalommal, amikor az alkalmazás meghívja a TrackException, a számláló értéke akkor nő, a megfelelő probléma azonosítóját. Ha a számláló elérte a `ThresholdForSnapshotting` érték, a probléma azonosítója bekerül a gyűjtési terv.

A Snapshot Collector is figyeli a kivételek, mivel azokat már a való feliratkozással lépett fel a [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) esemény. Az esemény akkor következik be, ha a probléma azonosítója, a kivétel számított, és összehasonlítja a gyűjtési tervben probléma azonosítóját.
Ha nincs egyezés, a futó folyamat egy pillanatkép jön létre. A pillanatkép van rendelve egy egyedi azonosítót, és a kivételt, hogy az azonosító van megjelölve. A FirstChanceException kezelő adja vissza, miután a thrown kivétel normál feldolgozása. Végül a kivétel eléri a TrackException metódus újra ahol, a pillanatkép azonosítója, valamint a rendszer jelenti az Application Insights.

A fő folyamat tovább fut, és a forgalmat a felhasználók számára a kis megszakítás szolgálja ki. Eközben a pillanatkép lesz továbbítva a pillanatkép Adatfeltöltő folyamat. A pillanatkép feltöltő tömörített memóriaképet hoz létre, és feltölti azt az Application insights bármely megfelelő szimbólum (.pdb) fájlokkal együtt.

> [!TIP]
> - Egy folyamat pillanatkép a futó folyamatot felfüggesztett klónja.
> - A pillanatkép létrehozása ezredmásodperc alatt körülbelül 10 és 20.
> - Az alapértelmezett érték a `ThresholdForSnapshotting` : 1. Ez egyben a minimális érték. Ezért az alkalmazás is ugyanazt a kivételt kiváltó **kétszer** előtt egy pillanatkép jön létre.
> - Állítsa be `IsEnabledInDeveloperMode` igaz értékre, ha szeretné létrehozni a pillanatképek hibakeresése a Visual Studióban során.
> - A pillanatkép létrehozása sebessége korlátozza a `SnapshotsPerTenMinutesLimit` beállítás. Alapértelmezés szerint a korlát a egy pillanatképet minden tíz perc.
> - Előfordulhat, hogy naponta legfeljebb 50 pillanatképek tölthető fel.

## <a name="limitations"></a>Korlátozások

Az alapértelmezett megőrzési időtartama befejezéséig 7 nap. Minden Application Insights-példány 50 pillanatképek maximális száma naponta engedélyezett.

### <a name="publish-symbols"></a>Szimbólumok közzététele
A pillanatkép-hibakereső van szükség, szimbólumfájlok való dekódolandó változók és a hibakeresés érdekében a Visual Studióban az üzemi kiszolgálón.
Verzió 15.2 (vagy újabb) a Visual Studio 2017 tesz közzé az szimbólumok, a kiadási alapértelmezés szerint hoz létre, amikor közzéteszi az App Service-ben. Az előzetes verziók, adja hozzá az alábbi sort a közzétételi profilt kell `.pubxml` fájlt úgy, hogy a kiadási módban közzétett szimbólumokat tartalmazhatja:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Az Azure Compute és más típusú, ellenőrizze, hogy a szimbólumfájlok a főalkalmazása .dll fájl ugyanabba a mappába (általában `wwwroot/bin`) vagy az aktuális elérési úton érhető el.

### <a name="optimized-builds"></a>Optimalizált buildek
Bizonyos esetekben helyi változók nem lehet megjeleníteni a kiadási buildek a fordító által alkalmazott optimalizálás miatt.
Azonban az Azure App Servicesben, a pillanatképek adatgyűjtője is deoptimize a gyűjtési terv részét képező rtesítő módszereket.

> [!TIP]
> Telepítse az Application Insights-Webhelybővítményt a deoptimization támogatás az App Service-ben.

## <a name="next-steps"></a>További lépések
Az alkalmazás Application Insights Snapshot Debugger engedélyezése:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure Service Fabric-szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure virtuális gépek és virtuálisgép-méretezési csoportokban](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [A helyi virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Túl az Application Insights Snapshot Debugger:
 
* [A kódban snappoints beállítása](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) beolvasni a pillanatképek kivétel nélkül.
* [A webalkalmazások kivételeinek diagnosztizálása](../../azure-monitor/app/asp-net-exceptions.md) azt ismerteti, hogyan további kivételek láthatóvá az Application Insightsba.
* [Intelligens detektálás](../../azure-monitor/app/proactive-diagnostics.md) automatikusan felderíti a teljesítményanomáliákat.

---
title: Azure Application Insights Snapshot Debugger .NET-alkalmazásokhoz
description: Hibakeresési pillanatképek automatikusan gyűjteni, amikor a .NET-alkalmazások éles kivételek
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 18f43ba90157d71ec9488b6858fa9f41b2ee42a5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275762"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>A .NET-alkalmazások kivételeinek hibakeresési pillanatképei
Ha egy kivétel lép fel, automatikusan gyűjtheti hibakereső pillanatképek az élő webalkalmazások. A pillanatkép abban a pillanatban a kivétel történt a Forráskód és a változók állapotát mutatja. Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) Snapshot Debugger figyeli a webalkalmazás kivétel-telemetria. Ez adatokat gyűjt a pillanatképek a felső értesítő kivételek, hogy rendelkezik diagnosztizálhatja a problémákat, éles környezetben a szükséges információkat. Adja meg a [Pillanatkép-gyűjtő NuGet csomagot](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) az alkalmazásban, és opcionálisan konfigurálja a gyűjtemény paramétereit a [ApplicationInsights. config fájlban](../../azure-monitor/app/configuration-with-applicationinsights-config.md). A pillanatképek a Application Insights portál [kivételei](../../azure-monitor/app/asp-net-exceptions.md) között jelennek meg.

A portálon a hibakeresési pillanatfelvételeket megtekintve láthatja a hívásvermet és megvizsgálhatja a változókat az egyes hívásveremkeretekre vonatkozóan. Ha hatékonyabb hibakeresési élményt szeretne kapni a forráskódtal, nyissa meg a pillanatképeket a Visual Studio 2019 Enterprise használatával. A Visual Studióban úgy is beállíthatja a Snappoints, hogy a rendszer kivétel nélkül is [interaktívan készítsen pillanatképeket](https://aka.ms/snappoint) .

A hibakeresési Pillanatképek tárolása 15 napig tart. A megőrzési házirend van beállítva a alkalmazásonként. Ha ez az érték növelése van szüksége, kérheti növelését az Azure Portalon nyissa meg egy támogatási esetet.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Application Insights Snapshot Debugger engedélyezése az alkalmazáshoz
Pillanatkép-gyűjtemény érhető el:
* .NET-keretrendszer és az ASP.NET alkalmazások futtatása a .NET-keretrendszer 4.5-ös vagy újabb.
* A Windows futó .NET core 2.0 és az ASP.NET Core 2.0 alkalmazásokat.

A következő környezetekben támogatottak:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* Az operációsrendszer-család 4-es vagy újabb verzióját futtató [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 vagy újabb rendszerű [Azure Service Fabric-szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Azure-Virtual Machines és Windows Server 2012 R2 vagy újabb rendszert futtató [virtuálisgép-méretezési készletek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 vagy újabb, vagy Windows 8,1 vagy újabb rendszert futtató helyszíni [virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> Ügyfélalkalmazások (például a WPF, Windows Forms vagy UWP) használata nem támogatott.

Ha engedélyezte Snapshot Debugger de nem lát pillanatképeket, tekintse meg a [hibaelhárítási útmutatót](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Engedélyek megadása

A pillanatképek hozzáférést szerepköralapú hozzáférés-vezérlés (RBAC) védi. Vizsgálhatja meg a pillanatképet, meg kell először adni a megfelelő szerepkör előfizetés tulajdonosa.

> [!NOTE]
> Tulajdonosok és a közreműködőknek nem automatikusan rendelkezik ehhez a szerepkörhöz. Ha szeretne pillanatképek megtekintése, hozzá kell magukat a szerepkört.

Az előfizetéshez tartozó tulajdonosoknak hozzá kell rendelniük a `Application Insights Snapshot Debugger` szerepkört azokhoz a felhasználókhoz, akik ellenőrizni fogják Ehhez a szerepkörhöz is hozzárendelhető az egyes felhasználók vagy csoportok szerint az előfizetés-tulajdonosokat a cél Application Insights-erőforrást, vagy a erőforráscsoportba vagy előfizetésbe.

1. Keresse meg az Application Insights-erőforrást az Azure Portalon.
1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.
1. Kattintson a **+ szerepkör-hozzárendelés hozzáadása** gombra.
1. Válassza ki **Application Insights Snapshot Debugger** a **szerepkörök** legördülő listából.
1. Keresse meg, és adja meg a hozzáadni kívánt felhasználó nevét.
1. Kattintson a **Save (Mentés** ) gombra, és adja hozzá a felhasználót a szerepkörhöz.


> [!IMPORTANT]
> A pillanatképek potenciálisan tartalmazó változót és a paraméter értékét a személyes és egyéb bizalmas adatokat.

## <a name="view-snapshots-in-the-portal"></a>Pillanatképek megtekintése a portálon

Ha kivétel történt az alkalmazásban, és egy pillanatkép lett létrehozva, pillanatképeket kell megtekintenie. Akár 5 – 10 percet is igénybe vehet, ha egy pillanatkép készen áll, és a portálról megtekinthető. A pillanatképek megtekintéséhez a **hiba** ablaktáblán kattintson a műveletek **gombra a** **műveletek** lap megtekintésekor, vagy válassza a **kivételek** gombot a **kivételek** lap megtekintésekor:

![Hibák lapja](./media/snapshot-debugger/failures-page.png)

Válasszon ki egy műveletet vagy kivételt a jobb oldali ablaktáblán a **végpontok közötti tranzakció részletei** panel megnyitásához, majd válassza ki a kivétel eseményt. Ha a megadott kivételhez pillanatkép áll rendelkezésre, a jobb oldali ablaktáblán megjelenik a [kivétel](../../azure-monitor/app/asp-net-exceptions.md)részleteit tartalmazó **nyitott hibakeresési pillanatkép** gomb.

![Hibakeresési pillanatfelvétel megnyitása gombjára kivétel](./media/snapshot-debugger/e2e-transaction-page.png)

A hibakeresési pillanatfelvétel nézetben láthatja a hívási verem és a egy változók panelen. Keretek, a hívási veremben a hívási verem panelen válassza ki, ha helyi változók is megtekintheti, és a Változók panelen hívja a függvény paramétereit.

![Hibakeresési Pillanatfelvétel megtekintése a portálon](./media/snapshot-debugger/open-snapshot-portal.png)

A pillanatképek bizalmas információkat tartalmazhatnak, és alapértelmezés szerint nem látható. A pillanatképek megtekintéséhez hozzá kell rendelnie a `Application Insights Snapshot Debugger` szerepkört.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Pillanatképek megtekintése a Visual Studio 2017 Enterprise vagy újabb verzióban
1. Kattintson a **Pillanatkép letöltése** gombra egy `.diagsession` fájl letöltéséhez, amelyet a Visual Studio Enterprise is megnyithat.

2. A `.diagsession` fájl megnyitásához telepítenie kell a Snapshot Debugger Visual Studio összetevőt. A Snapshot Debugger összetevő a Visual Studióban a ASP.net számítási feladatának szükséges összetevője, és a Visual Studio telepítő egyes összetevők listájában is kiválasztható. Ha a Visual Studio 2017-es 15,5 verziójának korábbi verzióját használja, akkor telepítenie kell a bővítményt a [Visual Studio piactérről](https://aka.ms/snapshotdebugger).

3. A pillanatkép-fájl megnyitása után az tömörített memóriaképet hibakeresés a Visual Studióban megjelenik. A pillanatkép hibakeresésének megkezdéséhez kattintson a **felügyelt kód hibakeresése** elemre. A pillanatkép megnyitása, ahol a kivétel lépett fel, hogy a folyamat aktuális állapota is hibakeresése kódsort.

    ![A Visual Studióban nézet hibakeresési pillanatkép](./media/snapshot-debugger/open-snapshot-visualstudio.png)

A letöltött pillanatképet tartalmaz szimbólum fájlokat, a webalkalmazás-kiszolgáló található. A szimbólum fájlok a pillanatkép adatainak társítandó forráskód szükségesek. Az App Service-alkalmazások esetén ügyeljen arra, hogy engedélyezi a szimbólum központi telepítést, a web apps közzétételekor.

## <a name="how-snapshots-work"></a>A pillanatképek működése

A Snapshot Collector [Application Insights telemetria processzorként](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet)van megvalósítva. Az alkalmazás futtatásakor az alkalmazás telemetriai folyamat hozzáadódik a Snapshot Collector Telemetriai processzor.
Minden alkalommal, amikor az alkalmazás meghívja a [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions)-t, a Snapshot Collector kiszámítja a probléma azonosítóját a dobott kivétel típusától és a throwing metódustól.
Minden alkalommal, amikor az alkalmazás meghívja a TrackException, a számláló értéke akkor nő, a megfelelő probléma azonosítóját. Ha a számláló eléri a `ThresholdForSnapshotting` értéket, a probléma azonosítója bekerül egy gyűjteményi tervbe.

A Snapshot Collector a kivételeket is figyeli a [alkalmazástartomány. CurrentDomain. FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) eseményre való feliratkozással. Az esemény akkor következik be, ha a probléma azonosítója, a kivétel számított, és összehasonlítja a gyűjtési tervben probléma azonosítóját.
Ha nincs egyezés, a futó folyamat egy pillanatkép jön létre. A pillanatkép van rendelve egy egyedi azonosítót, és a kivételt, hogy az azonosító van megjelölve. A FirstChanceException kezelő adja vissza, miután a thrown kivétel normál feldolgozása. Végül a kivétel eléri a TrackException metódus újra ahol, a pillanatkép azonosítója, valamint a rendszer jelenti az Application Insights.

A fő folyamat tovább fut, és a forgalmat a felhasználók számára a kis megszakítás szolgálja ki. Eközben a pillanatkép lesz továbbítva a pillanatkép Adatfeltöltő folyamat. A pillanatkép feltöltő tömörített memóriaképet hoz létre, és feltölti azt az Application insights bármely megfelelő szimbólum (.pdb) fájlokkal együtt.

> [!TIP]
> - Egy folyamat pillanatkép a futó folyamatot felfüggesztett klónja.
> - A pillanatkép létrehozása ezredmásodperc alatt körülbelül 10 és 20.
> - Az `ThresholdForSnapshotting` alapértelmezett értéke 1. Ez egyben a minimális érték. Ezért az alkalmazásnak ugyanezt a kivételt **kétszer** kell elindítania a pillanatkép létrehozása előtt.
> - Ha pillanatképeket szeretne létrehozni a Visual Studióban, a `IsEnabledInDeveloperMode` értéke TRUE (igaz) értékre van állítva.
> - A pillanatkép-létrehozási sebességet a `SnapshotsPerTenMinutesLimit` beállítás korlátozza. Alapértelmezés szerint a korlát a egy pillanatképet minden tíz perc.
> - Előfordulhat, hogy naponta legfeljebb 50 pillanatképek tölthető fel.

## <a name="limitations"></a>Korlátozások

Az alapértelmezett adatmegőrzési időszak 15 nap. Minden Application Insights-példány esetében naponta legfeljebb 50 pillanatkép engedélyezett.

### <a name="publish-symbols"></a>Szimbólumok közzététele
A pillanatkép-hibakereső van szükség, szimbólumfájlok való dekódolandó változók és a hibakeresés érdekében a Visual Studióban az üzemi kiszolgálón.
Verzió 15.2 (vagy újabb) a Visual Studio 2017 tesz közzé az szimbólumok, a kiadási alapértelmezés szerint hoz létre, amikor közzéteszi az App Service-ben. A korábbi verziók esetében a következő sort hozzá kell adnia a közzétételi profilhoz `.pubxml` fájlban, hogy a szimbólumok közzé legyenek téve a kiadási módban:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Az Azure-beli számítási és egyéb típusok esetén győződjön meg arról, hogy a szimbólumok a Main Application. dll fájljának (jellemzően `wwwroot/bin`) mappájában találhatók, vagy elérhetők az aktuális elérési úton.

> [!NOTE]
> Az elérhető különböző szimbólumokkal kapcsolatos további információkért tekintse meg a [Visual Studio dokumentációját](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). A legjobb eredmény érdekében javasoljuk a "teljes", a "hordozható" vagy a "Embedded" használatát.

### <a name="optimized-builds"></a>Optimalizált buildek
Bizonyos esetekben helyi változók nem lehet megjeleníteni a kiadási buildek a fordító által alkalmazott optimalizálás miatt.
Azonban az Azure App Servicesben, a pillanatképek adatgyűjtője is deoptimize a gyűjtési terv részét képező rtesítő módszereket.

> [!TIP]
> Telepítse az Application Insights-Webhelybővítményt a deoptimization támogatás az App Service-ben.

## <a name="next-steps"></a>Következő lépések
Application Insights Snapshot Debugger engedélyezése az alkalmazáshoz:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure-Virtual Machines és virtuálisgép-méretezési csoportok](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Helyszíni virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Snapshot Debuggeron túl:
 
* [Állítsa be a snappoints a kódban](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) a pillanatképek lekéréséhez, ha nem vár kivételt.
* [A webalkalmazásokban a kivételek diagnosztizálásakor](../../azure-monitor/app/asp-net-exceptions.md) megtudhatja, hogyan teheti elérhetővé a Application Insights további kivételeket.
* Az [intelligens észlelés](../../azure-monitor/app/proactive-diagnostics.md) automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket.

---
title: Azure Application Insights snapshot hibakereső .
description: A hibakeresési pillanatképek automatikusan összegyűjtésre kerülnek, amikor kivételek kerülnek előadáshoz az éles .NET alkalmazásokban
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 18f43ba90157d71ec9488b6858fa9f41b2ee42a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275762"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>A .NET-alkalmazások kivételeinek hibakeresési pillanatképei
Ha kivétel történik, automatikusan összegyűjtheti a hibakeresési pillanatképet az élő webalkalmazásból. A pillanatkép a kivétel pillanatképét mutatja a forráskód és a változók állapotáról a kivétel eldobásának pillanatában. Az Azure Application [Insights](../../azure-monitor/app/app-insights-overview.md) pillanatkép-hibakeresője a webalkalmazásból származó kivételtelemetriai adatokat figyeli. Pillanatképeket gyűjt a felső dobási kivételekről, így rendelkezik az éles környezetben felmerülő problémák diagnosztizálásához szükséges információkkal. A [Snapshot collector NuGet csomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) felvétele az alkalmazásba, és szükség esetén konfigurálási paraméterek et [az ApplicationInsights.config fájlban.](../../azure-monitor/app/configuration-with-applicationinsights-config.md) Pillanatképek jelennek meg a [kivételek](../../azure-monitor/app/asp-net-exceptions.md) az Application Insights portálon.

A portálon a hibakeresési pillanatfelvételeket megtekintve láthatja a hívásvermet és megvizsgálhatja a változókat az egyes hívásveremkeretekre vonatkozóan. Ha hatékonyabb hibakeresési élményt szeretne kapni a forráskóddal kapcsolatban, nyissa meg a pillanatképeket a Visual Studio 2019 Enterprise segítségével. A Visual Studio-ban [beállíthatja, hogy a Snappoints interaktívan készítsen pillanatképeket](https://aka.ms/snappoint) anélkül, hogy kivételre várna.

A hibakeresési pillanatképek 15 napig tárolódnak. Ez az adatmegőrzési szabály alkalmazásonként van beállítva. Ha növelnie kell ezt az értéket, kérheti a növekedést egy támogatási eset megnyitásával az Azure Portalon.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Az Application Insights snapshot debugger engedélyezése az alkalmazáshoz
Pillanatkép-gyűjtemény a következő célokra érhető el:
* ASP.NET A .
* A .NET Core 2.0 és ASP.NET Core 2.0 windowsos alkalmazásokat.

A következő környezetek támogatottak:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [4-es](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) vagy újabb operációsrendszer-családot futtató Azure Cloud Services
* Windows Server 2012 R2 vagy újabb rendszeren futó [Azure Service Fabric-szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* A Windows Server 2012 R2 vagy újabb rendszert futtató [Azure virtuális gépek és virtuálisgépek méretezési készletei](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Helyszíni virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) Windows Server 2012 R2 vagy újabb vagy Windows 8.1 vagy újabb rendszerű

> [!NOTE]
> Az ügyfélalkalmazások (például WPF, Windows Forms vagy UWP) nem támogatottak.

Ha engedélyezte a Snapshot Debugger t, de nem látja a pillanatképeket, olvassa el [a hibaelhárítási útmutatót.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)

## <a name="grant-permissions"></a>Engedélyek megadása

A pillanatképekhez való hozzáférést szerepköralapú hozzáférés-vezérlés (RBAC) védi. Egy pillanatkép vizsgálatához először hozzá kell adnia a szükséges szerepkört egy előfizetés tulajdonosa.

> [!NOTE]
> A tulajdonosok és a közreműködők nem rendelkeznek automatikusan ezzel a szerepkörvel. Ha meg szeretné tekinteni a pillanatképeket, hozzá kell adniuk magukat a szerepkörhöz.

Az előfizetés-tulajdonosoknak hozzá kell rendelniük a szerepkört azokhoz a felhasználókhoz, akik ellenőrzik a `Application Insights Snapshot Debugger` pillanatképeket. Ezt a szerepkört a cél Application Insights-erőforrás vagy annak erőforráscsoportja vagy előfizetése előfizetés-tulajdonosai egyéni felhasználókhoz vagy csoportokhoz rendelhetik hozzá.

1. Keresse meg az Application Insights-erőforrást az Azure Portalon.
1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.
1. Kattintson a **+Szerepkör-hozzárendelés hozzáadása** gombra.
1. Válassza ki **az Application Insights Snapshot Debugger** a **Szerepkörök** legördülő listából.
1. Keresse meg és írja be a hozzáadni a felhasználó nevét.
1. A **Mentés** gombra kattintva vegye fel a felhasználót a szerepkörbe.


> [!IMPORTANT]
> A pillanatképek potenciálisan tartalmazhatnak személyes és egyéb bizalmas információkat változó- és paraméterértékekben.

## <a name="view-snapshots-in-the-portal"></a>Pillanatképek megtekintése a portálon

Miután kivétel történt az alkalmazásban, és egy pillanatkép jött létre, meg kell tekintenie a pillanatképeket. A kivételtől 5–10 percet is igénybe vehet a portálról készített és megtekinthető pillanatképig. A pillanatképek megtekintéséhez a **Hiba** ablaktáblán válassza a **Műveletek** gombot a **Műveletek** lap megtekintésekor, vagy kattintson a **Kivételek** gombra a **Kivételek** lap megtekintésekor:

![Hibák lap](./media/snapshot-debugger/failures-page.png)

Válasszon ki egy műveletet vagy kivételt a jobb oldali ablaktáblában a **végpontok között a tranzakció részletei** ablaktábla megnyitásához, majd jelölje ki a kivételeseményt. Ha az adott kivételhez rendelkezésre áll pillanatkép, a jobb oldali ablaktáblán megjelenik egy **Hibakeresési pillanatkép** megnyitása gomb a [kivétel](../../azure-monitor/app/asp-net-exceptions.md)részleteivel.

![A Hibakeresési pillanatkép megnyitása gomb kivételkor](./media/snapshot-debugger/e2e-transaction-page.png)

A Hibakeresési pillanatkép nézetben megjelenik egy hívásverem és egy változóablak. Amikor a hívásverem kereteit választja ki a hívásverem ablaktáblában, a változók ablaktáblában megtekintheti az adott függvényhívás helyi változóit és paramétereit.

![A Debug Snapshot megtekintése a portálon](./media/snapshot-debugger/open-snapshot-portal.png)

A pillanatképek bizalmas információkat is tartalmazhatnak, és alapértelmezés szerint nem láthatók. A pillanatképek megtekintéséhez a `Application Insights Snapshot Debugger` szerepkörhöz hozzá van rendelve.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Pillanatképek megtekintése a Visual Studio 2017 Enterprise vagy újabb verzióiban
1. Kattintson a **Pillanatkép letöltése** gombra egy `.diagsession` fájl letöltéséhez, amelyet a Visual Studio Enterprise megnyithat.

2. A `.diagsession` fájl megnyitásához telepítve kell lennie a Snapshot Debugger Visual Studio összetevőnek. A Snapshot Debugger összetevő a Visual Studio ASP.net számítási feladatának szükséges összetevője, és a Visual Studio telepítőjének Egyéni összetevők listájából választható ki. Ha a Visual Studio 2017 15.5-ös verzióját megelőzően használja a Visual Studio egy verzióját, telepítenie kell a bővítményt a [Visual Studio Piactérről.](https://aka.ms/snapshotdebugger)

3. A pillanatképfájl megnyitása után megjelenik a Visual Studio Minidump hibakeresési lapja. Kattintson **a Felügyelt hibakód gombra** a pillanatkép hibakeresésének megkezdéséhez. A pillanatkép megnyílik arra a kódsorra, ahol a kivétel történt, így a folyamat aktuális állapotát hibakeresésre használhatja.

    ![Hibakeresési pillanatkép megtekintése a Visual Studióban](./media/snapshot-debugger/open-snapshot-visualstudio.png)

A letöltött pillanatkép tartalmazza a webalkalmazás-kiszolgálón található szimbólumfájlokat. Ezek a szimbólumfájlok szükségesek a pillanatképadatok forráskóddal való társításához. App Service-alkalmazások esetén győződjön meg arról, hogy engedélyezi a szimbólum-telepítést a webalkalmazások közzétételekor.

## <a name="how-snapshots-work"></a>A pillanatképek működésének megmunkálása

A Pillanatkép-kolókoló [egy Application Insights telemetriai processzorként](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet)van megvalósítva. Az alkalmazás futtatásakor a Pillanatkép-kolenitazonosító-feldolgozó hozzáadódik az alkalmazás telemetriai folyamatához.
Minden alkalommal, amikor az alkalmazás meghívja a [TrackException-et,](../../azure-monitor/app/asp-net-exceptions.md#exceptions)a Snapshot Collector kiszámítegy problémaazonosítót a kiváltott kivétel típusából és a dobási módszerből.
Minden alkalommal, amikor az alkalmazás meghívja a TrackException-et, a számláló a megfelelő problémaazonosítóhoz növekszik. Amikor a számláló `ThresholdForSnapshotting` eléri az értéket, a problémaazonosító hozzáadódik a beszedési tervhez.

A Pillanatkép-kololó is figyeli a kivételeket, ahogy azok az [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) eseményre való feliratkozással jelennek meg. Amikor ez az esemény aktiválódik, a kivétel problémaazonosítóját kiszámítja és összehasonlítja a begyűjtési tervben lévő problémaazonosítókkal.
Ha van egyezés, majd létrejön egy pillanatkép a futó folyamatról. A pillanatkép hez egyedi azonosító van rendelve, és a kivétel ezzel az azonosítóval van ellátva. Miután a FirstChanceException kezelő visszatér, a dobott kivétel feldolgozása a szokásos módon történik. Végül a kivétel ismét eléri a TrackException metódust, ahol a pillanatkép-azonosítóval együtt jelentik az Application Insightsnak.

A fő folyamat továbbra is fut, és a forgalmat a felhasználók számára kis megszakítással szolgálja ki. Eközben a pillanatkép átkerül a Snapshot Uploader folyamat. A Snapshot Uploader létrehoz egy minidump és feltölti azt Application Insights együtt minden releváns szimbólum (.pdb) fájlokat.

> [!TIP]
> - A folyamat pillanatképe a futó folyamat felfüggesztett klónja.
> - A pillanatkép létrehozása körülbelül 10–20 ezredmásodpercet vesz igénybe.
> - Az alapértelmezett `ThresholdForSnapshotting` érték 1. Ez a minimális érték is. Ezért az alkalmazásnak **kétszer** kell aktiválnia ugyanazt a kivételt a pillanatkép létrehozása előtt.
> - Állítsa `IsEnabledInDeveloperMode` igaz értékre, ha a Visual Studio hibakeresés közben szeretne pillanatképeket létrehozni.
> - A pillanatkép létrehozásának `SnapshotsPerTenMinutesLimit` sebességét a beállítás korlátozza. Alapértelmezés szerint a korlát tízpercenként egy pillanatkép.
> - Naponta legfeljebb 50 pillanatképet lehet feltölteni.

## <a name="limitations"></a>Korlátozások

Az alapértelmezett adatmegőrzési időszak 15 nap. Minden Application Insights-példány esetén naponta legfeljebb 50 pillanatkép engedélyezett.

### <a name="publish-symbols"></a>Szimbólumok közzététele
A Snapshot Debugger az éles kiszolgálón lévő szimbólumfájlokat igényel a változók dekódolásához és a Visual Studio hibakeresési élményének biztosításához.
A Visual Studio 2017 15.2-es verziója (vagy újabb) alapértelmezés szerint közzéteszi a kiadási buildek szimbólumait, amikor közzéteszi az App Service szolgáltatásban. A korábbi verziókban a következő sort kell `.pubxml` hozzáadnia a közzétételi profilfájlhoz, hogy a szimbólumok kiadási módban jelenjenek meg:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Az Azure Compute és más típusú, győződjön meg arról, hogy a szimbólum fájlok `wwwroot/bin`ugyanabban a mappában a fő alkalmazás .dll (általában), vagy elérhetők az aktuális elérési úton.

> [!NOTE]
> A rendelkezésre álló különböző szimbólumbeállításokkal kapcsolatos további információkért tekintse meg a [Visual Studio dokumentációját.](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
) A legjobb eredmény érdekében a "Teljes", "Hordozható" vagy "Beágyazott" kifejezést javasoljuk.

### <a name="optimized-builds"></a>Optimalizált buildek
Bizonyos esetekben a helyi változók nem tekinthetők meg a kiadási buildekben a JIT fordító által alkalmazott optimalizálások miatt.
Az Azure App Servicesben azonban a Pillanatkép-gyűjtő deoptimize-t biztosíthat a beszedési terv részét képezi dobási módszerektől.

> [!TIP]
> Telepítse az Application Insights webhelybővítményt az App Service-be az optimalizálási támogatás leértékeléshez.

## <a name="next-steps"></a>További lépések
Az Application Insights snapshot debugger engedélyezése az alkalmazáshoz:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-szolgáltatások](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure virtuális gépek és a virtuális gépek méretezési készletei](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Helyszíni virtuális vagy fizikai gépek](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Az Application Insights pillanatkép-hibakeresőn túl:
 
* [Állítsa be a snappoints a kódot,](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) hogy pillanatképek et anélkül, hogy egy kivételre várna.
* [A webalkalmazásokban található kivételek diagnosztizálása](../../azure-monitor/app/asp-net-exceptions.md) ismerteti, hogyan tehet további kivételeket az Application Insights számára.
* [Az intelligens detektálás](../../azure-monitor/app/proactive-diagnostics.md) automatikusan észleli a teljesítményanomáliákat.

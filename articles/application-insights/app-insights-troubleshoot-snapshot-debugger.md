---
title: Az Azure Application Insights pillanatkép-hibakereső hibaelhárítási útmutató |} A Microsoft Docs
description: Gyakori kérdések az Azure Application Insights Snapshot Debugger.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: ''
ms.service: application-insights
ms.workload: ''
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 285f42a6b52819077b92abce78c1f51756780604
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645551"
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Pillanatkép-hibakereső: Hibaelhárítási útmutató

Application Insights Snapshot Debugger lehetővé teszi, hogy automatikusan hibakereső pillanatképek gyűjtését élő webalkalmazások. A pillanatkép-kivétel keletkezett jelenleg forráskód és a változók állapotát mutatja. Ez a cikk bemutatja, hogyan a hibakeresőt működik-e, és biztosítja a gyakori problémák megoldása.

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Hogyan működik az Application Insights Snapshot Debugger

Application Insights Snapshot Debugger az Application Insights telemetria folyamat (ITelemetryProcessor példányát) részét képezi. A Snapshot Collector a kivételek, a kód (AppDomain.FirstChanceException) száma és a kivétel telemetriát meghaladhatja az Application Insights segítségével figyeli `TelemetryClient.TrackException`. Miután sikeresen hozzáadta a Snapshot Collector csomagot a projekthez, és a egy esemény észlelt az "AppInsightsSnapshotCollectorLogs" és az egyéni adatok "SnapshotCollectorEnabled" nevű egyéni esemény telemetriát folyamatban küldi. Egy időben, a "SnapshotUploader.exe" nevű folyamat elindul (1.2.0-s vagy annál újabb verziójú vagy újabb) vagy "MinidumpUploader.exe" (a következőnél régebbi verziók 1.2.0-s vagy annál újabb), feltölteni a gyűjtött pillanatkép-adatfájlok az Application Insightsba.  Az adatfeltöltő-folyamat indulásakor "UploaderStart" nevű egyéni eseményt küld. Ezt követően a snapshot collector adja meg a szokásos figyelési viselkedését.

A snapshot collector figyel az Application Insights – kivételtelemetria, míg a definiált paraméterek (például ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) használja a konfigurációs, hogy mikor pillanatfelvétel. Minden szabály teljesül, amikor a gyűjtő kérni fogja a pillanatképét a következő kivétel történt ugyanazon a helyen. Egyszerre egy egyéni eseményt az Application Insights 'AppInsightsSnapshotCollectorLogs' nevű és "RequestSnapshots" küldi el. A fordító "Release" kód optimalizálja, mivel a helyi változók nem lehet a gyűjtött pillanatkép látható. A snapshot collector megpróbálja a módszert, amelyet a kivételt okozott, pillanatképek kérés deoptimize. Ebben az időszakban az Application Insights egyéni esemény neve "AppInsightsSnapshotCollectorLogs" és "ProductionBreakpointsDeOptimizeMethod" az egyéni adatokat küldi el.  Ha az a következő kivételről készített pillanatfelvétel begyűjtését a helyi változók lesznek elérhetők. A pillanatfelvétel begyűjtését követően a kód azt fogja reoptimize. 

> [!NOTE]
> Deoptimization van szükség az Application Insights-webhelybővítményt kell telepíteni.

Egy pillanatképet egy adott kivétel van szükség, a snapshot collector megkezdi az alkalmazás a kivételkezelő (AppDomain.FirstChanceException) folyamat figyelése. A kivétel újra jelentkezik, amikor a gyűjtő egy pillanatképet (Application Insights egyéni eseményt 'AppInsightsSnapshotCollectorLogs' nevű és "SnapshotStart" egyéni adatok) indul el. Akkor jön létre egy árnyékmásolatából áll a futó folyamat (a lap tábla többszörözni). Ez általában tart 10 és 20 ezredmásodperc. Ezt követően egy egyéni eseményt az Application Insights 'AppInsightsSnapshotCollectorLogs' nevű és "SnapshotStop" egyéni adatok küldi el. Az elágaztatott folyamat jön létre, amikor a teljes lapozható memória is ugyanilyen mértékben csökken a lapozható memória, a futó alkalmazás (munkakészletének lesz sokkal kisebb) növelhető. Az alkalmazás folyamata normális esetben az árnyékmásolat futása közben a folyamat memóriájából kiírt lemezre, és az Application Insights feltölteni. A pillanatkép követően egy Application Insights "UploadSnapshotFinish" nevű egyéni eseményt küld.

## <a name="is-the-snapshot-collector-working-properly"></a>A pillanatképek adatgyűjtője megfelelően működik-e?

### <a name="how-to-find-snapshot-collector-logs"></a>Snapshot Collector naplók keresése
Snapshot collector naplók érkeznek az Application Insights-fiókhoz, ha a [Snapshot Collector NuGet csomagot](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) 1.1.0-s verzió vagy újabb. Győződjön meg arról, hogy a *ProvideAnonymousTelemetry* értéke nem hamis (az értéke igaz, alapértelmezés szerint).

* Keresse meg az Application Insights-erőforrást az Azure Portalon.
* Kattintson a *keresési* az Áttekintés szakaszban.
* Adja meg a következő karakterláncot a keresőmezőbe írja be:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Megjegyzés: módosítsa a *időtartomány* szükség esetén.

![Képernyőkép a keresési pillanatképek adatgyűjtője naplók](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Snapshot collector naplók vizsgálata
Snapshot Collector naplók keresése, kell "UploadSnapshotFinish" eseményeket a célként kijelölt időtartományban található. Ha továbbra sem látja a "Hibakeresési pillanatfelvétel megnyitása" gombra a pillanatképet, küldjön e-mailek snapshothelp@microsoft.com együtt az Application Insights-kialakítási kulcsot.

![Snapshot collector naplók vizsgálata képernyőképe](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Nem található egy pillanatképet nyitott
Ha nem segítségével az alábbi lépéseket a probléma megoldásához, küldjön e-mailek snapshothelp@microsoft.com együtt az Application Insights-kialakítási kulcsot.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>1. lépés: Győződjön meg arról, hogy az alkalmazás által küldött telemetriai adatok és a kivételek adatainak az Application Insightsba
Keresse meg az Application Insights-erőforrást, ellenőrizze, hogy nincs-e az alkalmazás által küldött adatokat.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>2. lépés: Győződjön meg arról, hogy a pillanatképek adatgyűjtője helyesen adnak hozzá az alkalmazás Application Insights Telemetria folyamat
Naplók találhatja meg a "Hogyan találhatja meg a Snapshot Collector naplók" lépést, ha a pillanatképek adatgyűjtője helyesen adnak hozzá a projekthez, és ez a lépés figyelmen kívül hagyhatja.

Ha nincsenek Snapshot collector naplók, ellenőrizze a következőket:
* Klasszikus ASP.NET-alkalmazások esetén keressen ezt a sort *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* a a *ApplicationInsights.config* fájlt.

* ASP.NET Core-alkalmazások, ellenőrizze, hogy a *ITelemetryProcessorFactory* a *SnapshotCollectorTelemetryProcessor* adnak hozzá *IServiceCollection* szolgáltatások .

* Emellett ellenőrizze, hogy a megfelelő kialakítási kulcsot használ a közzétett alkalmazásban.

* A pillanatkép-gyűjtő nem támogatja a több kialakítási kulcs az egy alkalmazásban, a kialakítási kulcsot az első kivétel azt látja, hogy pillanatképeket küld.

* Ha a *InstrmentationKey* manuálisan a kódban, frissítse a *InstrumentationKey* elemet a *ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>3. lépés: Győződjön meg arról, a tömörített memóriaképet feltöltő elindult
A snapshot collector naplók keresése *UploaderStart* (a keresési szövegmezőbe írja be a UploaderStart). Egy eseményt, amikor a snapshot collector figyeli az első kivétel kell lennie. Ha ez az esemény nem létezik, ellenőrizze a többi naplókat. A probléma megoldására szolgáló egyik lehetséges módja az alkalmazás újraindul.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>4. lépés: Győződjön meg arról, hogy Snapshot Collector kifejezett annak szándékát-pillanatképek gyűjtése
A snapshot collector naplók keresése *RequestSnapshots* (típus ```RequestSnapshots``` a keresési szövegmezőbe).  Ha nincs bármely, ellenőrizze a konfigurációt. Ha például *ThresholdForSnapshotting*, ami azt jelenti, hogy egy adott kivételt, amely akkor fordulhat elő, mielőtt elkezdené a pillanatképek összegyűjtése száma.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>5. lépés: Győződjön meg arról, hogy pillanatképet Memóriavédelem miatt nincs letiltva
Az alkalmazás teljesítményét védelme érdekében pillanatképet fog csak rögzíteni egy jó memóriapuffer esetén. A snapshot collector naplókban keressen a "CannotSnapshotDueToMemoryUsage". Az esemény egyéni adatokat részletes OK fog rendelkezni. Ha az alkalmazás fut az Azure Web Apps, a korlátozás szigorú lehet. Mivel az Azure Web App újraindul az alkalmazás egyes memória szabályok teljesülése esetén. Vertikális felskálázás a szolgáltatáscsomagot, több memóriával rendelkező gépek a probléma megoldása érdekében próbálkozzon.

### <a name="step-6-make-sure-snapshots-were-captured"></a>6. lépés: Győződjön meg arról, hogy a pillanatképek lettek rögzítve
A snapshot collector naplók keresése ```RequestSnapshots```.  Ha egyik sem található, ellenőrizze a konfigurációt. Ha például *ThresholdForSnapshotting*, ami azt jelenti, hogy a pillanatfelvétel begyűjtését előtt egy adott kivételesemények számát.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>7. lépés: Győződjön meg arról, hogy a pillanatképek megfelelően lesznek feltöltve
A snapshot collector naplók keresése ```UploadSnapshotFinish```.  Ha ez nem szerepel, küldjön e-mailek snapshothelp@microsoft.com együtt az Application Insights-kialakítási kulcsot. Ha ez az esemény létezik, nyisson meg egy olyan a naplókat, és másolja a "SnapshotId" értéket az egyéni adatok. Keressen rá az értéket. Ez a kivétel a pillanatkép megfelelő talál. Ezután kattintson a kivételek és hibakeresési pillanatfelvétel megnyitása. (Nincs megfelelő kivétel, ha a kivételtelemetria lehet mintát venni miatt nagy mennyiségű. Próbálkozzon egy másik snapshotId.)

![Keresés SnapshotId képernyőképe](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Képernyőkép a megnyitott kivétel](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Képernyőkép a megnyitott hibakeresési pillanatkép](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Pillanatkép nézet helyi értékek nem teljes

A helyi változókat némelyike hiányzik. Ha az alkalmazás kiadási kódja fut, a fordító optimalizálja az egyes változók azonnal. Példa:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Ha Ön esetében eltérő, hibajelentés jelezheti. E-mailben snapshothelp@microsoft.com a kódrészlet együtt az Application Insights-kialakítási kulcsot.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>A Pillanatképnézetet: Nem lehet megszerezni a helyi változó vagy argumentum értéke
Győződjön meg arról, hogy a [Application Insights webhelybővítmény](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) telepítve van. Ha a probléma továbbra is fennáll, küldjön e-mailek snapshothelp@microsoft.com együtt az Application Insights-kialakítási kulcsot.

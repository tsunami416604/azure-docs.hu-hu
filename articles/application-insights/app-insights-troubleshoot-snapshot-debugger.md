---
title: "Az Azure Application Insights pillanatkép-hibakereső hibaelhárítási útmutatója |} Microsoft Docs"
description: "Azure Application Insights pillanatkép hibakereső vonatkozó gyakran ismételt kérdések."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 
ms.service: application-insights
ms.workload: 
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 5d6a2fe4c3ee373172f5324b6c7a39e4f94f4652
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Pillanatkép hibakereső: Hibaelhárítási útmutatója

Application Insights pillanatkép hibakereső automatikusan pillanatfelvétel hibakeresési származó élő webalkalmazások teszi lehetővé. A pillanatkép forráskódja és változók állapotának megjelenítése az kivétel időpontjában. Ha probléma merül az Application Insights pillanatkép hibakereső első, és fut, ez a cikk végigvezeti a hibakereső működése, a gyakori hibaelhárítási forgatókönyveket megoldásokkal együtt. 

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Hogyan működik az Application Insights pillanatkép hibakereső

Application Insights pillanatkép hibakereső az Application Insights telemetria folyamat (ITelemetryProcessor példánya) része, a pillanatkép-gyűjtő figyeli a kódban (AppDomain.FirstChanceException) kivételek, mind a kivételek amely az Application Insights – Kivételtelemetria az adatcsatorna első nyomon. Miután sikeresen hozzáadta a pillanatkép-gyűjtő a projekthez, és azt egy kivételt észlelt az Application Insights telemetria-feldolgozási folyamat, az Application Insights egyéni esemény "AppInsightsSnapshotCollectorLogs" nevű és " SnapshotCollectorEnabled "az egyéni adatok küldi. Egy időben a folyamat nevű, "MinidumpUploader.exe", a gyűjtött pillanatkép-adatok fájlok feltöltése az Application Insights fog elindulni.  Ha a "MinidumpUploader.exe" feldolgozni elindul, "UploaderStart" nevű egyéni esemény küldi. Az előző lépést követően a pillanatkép-gyűjtő módba lép, a szokásos figyelési viselkedését.

A pillanatkép-gyűjtő által figyelt Application Insights – kivételtelemetria, amíg a definiált paraméterek (például ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) használja a konfigurációs, hogy pillanatfelvétel. Ha a szabályok mindegyike teljesül, a gyűjtő egy pillanatképet a következő kivétel lépett fel az ugyanazon a helyen fog kérni. Egy időben az Application Insights egyéni esemény "AppInsightsSnapshotCollectorLogs" nevű és "RequestSnapshots" küldi. Mivel a fordító optimalizálja a "Release" kód, helyi változók nem lehet a gyűjtött pillanatkép látható. A pillanatkép-gyűjtője megpróbálja deoptimize a módszert, amelyet a kivételt, a pillanatképek kérelem során. Ebben az időszakban egy Application Insights egyéni eseményre nevű "AppInsightsSnapshotCollectorLogs" és "ProductionBreakpointsDeOptimizeMethod" az egyéni adatokat küldi el.  A pillanatkép, a következő kivétel gyűjtése, a helyi változók lesznek elérhetők. A pillanatfelvétel begyűjtését követően azt fogja reoptimize a kódot, a teljesítmény biztosítása érdekében. 

> [!NOTE]
> Deoptimization igényel az Application Insights hely bővítmény telepíthető.

Pillanatkép egyedi kivételt van szükség, a pillanatkép-gyűjtő megkezdi az alkalmazás kivételkezelő (AppDomain.FirstChanceException) folyamat figyelése. A kivétel újra történik, amikor a gyűjtő (Application Insights egyéni esemény "AppInsightsSnapshotCollectorLogs" nevű és "SnapshotStart" az egyéni adatok) pillanatkép indul el. Ezután a futó folyamattal árnyékmásolatát történik (a lap tábla megkettőzése). Ez általában lépnek 10-20 ezredmásodperc. Ezt követően az Application Insights egyéni esemény "AppInsightsSnapshotCollectorLogs" nevű és "SnapshotStop" az egyéni adatok küldi. A villás folyamat létrehozásakor a teljes lapozható memória megnövelve akkora, mint a lapozható memória a futó alkalmazások (munkakészletének sokkal kisebb méretű lesz). Az alkalmazás folyamat általában az árnyékmásolat futása közben folyamat memória kiírt lemezre, és az Application Insights töltve. A pillanatkép feltöltése, után "UploadSnapshotFinish" nevű Application Insights egyéni esemény küldi el.

## <a name="is-the-snapshot-collector-working-properly"></a>A pillanatkép-gyűjtő megfelelően működik-e?

### <a name="how-to-find-snapshot-collector-logs"></a>Pillanatkép adatgyűjtő naplók keresése
Pillanatkép adatgyűjtő naplók küldött Application Insights-fiókjába, ha a [pillanatkép adatgyűjtő NuGet-csomag](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) 1.1.0-ás verzió vagy újabb verziója szükséges. Győződjön meg arról, hogy a *ProvideAnonymousTelemetry* értéke nem hamis (értéke true alapértelmezés szerint).

* Nyissa meg az Application Insights-erőforrást az Azure portálon.
* Kattintson a *keresési* az Áttekintés szakaszban.
* A keresőmezőbe írja be a következő karakterláncot:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Megjegyzés: módosítsa a *időtartománynak* szükség esetén.

![Képernyőfelvétel a keresési pillanatkép adatgyűjtő-naplók](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Vizsgálja meg a pillanatkép adatgyűjtő naplók
Pillanatkép adatgyűjtő naplók keresésekor kell "UploadSnapshotFinish" események a célként megadott időtartományba esik. Ha még nem látja a "Debug Snapshot nyissa meg a" a gombra kattintva megnyithatja a pillanatkép, küldjön egy e-mailek snapshothelp@microsoft.com az Application Insights Instrumentation kulccsal.

![Pillanatkép adatgyűjtő naplók megvizsgálja képernyőképe](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Nyissa meg az aktuális nem található
Ha nem segítségével a következőket a probléma megoldásához, küldjön egy e-mailek snapshothelp@microsoft.com az Application Insights Instrumentation kulccsal.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>1. lépés: Győződjön meg arról, hogy az alkalmazás által küldött telemetriai adatok és a kivételek adatainak az Application Insights
Navigáljon az Application Insights-erőforrást, ellenőrizze, hogy van-e az alkalmazás által küldött adatokat.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>2. lépés: Győződjön meg arról, hogy a pillanatkép-gyűjtő helyesen felvették az alkalmazás Application Insights Telemetria-feldolgozási folyamat
Ha találhatók naplók a "Hol találhatók a pillanatkép adatgyűjtő logs" lépés a pillanatkép-gyűjtő megfelelően hozzáadva a projekthez, figyelmen kívül hagyhatja ezt a lépést.

Ha nincsenek pillanatkép adatgyűjtő naplókat, ellenőrizze a következőket:
* Klasszikus ASP.NET-alkalmazások, ellenőrizze az erre a vonalra  *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">*  a a *ApplicationInsights.config* fájlt.

* Az ASP.NET Core alkalmazások, ellenőrizze, hogy a *ITelemetryProcessorFactory* rendelkező *SnapshotCollectorTelemetryProcessor* hozzáadódik *IServiceCollection* szolgáltatások .

* Is ellenőrizheti, hogy a közzétett alkalmazást a megfelelő instrumentation kulcsot használ.

* A pillanatkép-gyűjtő nem támogatja az egy alkalmazásban több instrumentation kulcs, pillanatképeket küld az első kivétel az billentyűzetbemenetet figyelő instrumentation kulcsa.

* Ha a *InstrmentationKey* manuálisan a kódban, frissítse a *InstrumentationKey* elem a *ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>3. lépés: Győződjön meg arról, hogy elindult-e a kis memóriakép feltöltése
A pillanatkép adatgyűjtő naplók, keresse meg a *UploaderStart* (írja be a keresési mezőbe UploaderStart). Egy eseményt, amikor a pillanatkép-gyűjtő az első kivétel figyelni kell. Ha ez az esemény nem létezik, ellenőrizze az egyéb részleteket naplóiban. A probléma megoldásához egyik lehetséges módja az alkalmazás újraindul.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>4. lépés: Győződjön meg arról, pillanatkép adatgyűjtő kifejezett a cél-pillanatképek gyűjtése
A pillanatkép adatgyűjtő naplók, keresse meg a *RequestSnapshots* (típus ```RequestSnapshots``` szöveget a keresőmezőbe).  Ha nincs bármely, ellenőrizze a konfigurációt, pl. *ThresholdForSnapshotting* ami azt jelenti, hogy egy bizonyos kivételek kapcsán, amik akkor léphetnek fel, mielőtt elkezdené a pillanatképek gyűjtése száma.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>5. lépés: Győződjön meg arról, hogy a pillanatkép nem Letiltva Memóriavédelem miatt
Védelme érdekében az alkalmazások teljesítményéről, pillanatkép fog csak rögzíthetők jó memóriapuffer esetén. A pillanatkép-gyűjtő naplófájlokban keressen a "CannotSnapshotDueToMemoryUsage". Az Egyéni eseményadatok akkor a részletes okát rendelkeznek. Ha az alkalmazás az Azure Web Apps fut, lehet, hogy a korlátozás szigorú. Mivel az Azure Web Apps egyes memória szabályok teljesülése esetén az alkalmazás újraindul. Vertikális felskálázás a service-csomag több memória gépekre a probléma megoldásához próbálkozzon.

### <a name="step-6-make-sure-snapshots-were-captured"></a>6. lépés: Győződjön meg arról, hogy a pillanatképek rögzítette a
A pillanatkép adatgyűjtő naplók, keresse meg a ```RequestSnapshots```.  Ha legyen jelen, ellenőrizze a beállításait, például ```ThresholdForSnapshotting``` Ez jelzi az adott kivételek számát előtt egy pillanatfelvétel begyűjtését.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>7. lépés: Győződjön meg arról, hogy a pillanatképek megfelelően feltöltése
A pillanatkép adatgyűjtő naplók, keresse meg a ```UploadSnapshotFinish```.  Ha ez nincs jelen, küldjön egy e-mailek snapshothelp@microsoft.com az Application Insights Instrumentation kulccsal. Ha ez az esemény létezik, nyissa meg az egyik a naplókat, és másolja át a "SnapshotId" értéket az egyéni adatok. Majd keresse meg az értéket. Ez a kivétel a pillanatkép megfelelő található. Kattintson a kivétel és a nyitott hibakeresési pillanatkép. (Ha nincs megfelelő kivétel, lehet, hogy a kivételtelemetria mintát, miatt nagy mennyiségű, próbálkozzon egy másik snapshotId.)

![Képernyőkép a keresés SnapshotId](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Képernyőkép a nyitott kivétel](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Pillanatfelvétel. hibakeresési képernyőkép megnyitása.](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Pillanatkép nézet helyi változók az alábbiak nem teljes

A helyi változók némelyike hiányzik. Ha az alkalmazás kiadási kód fut, a fordító optimalizálja az egyes változók számítógépnél. Példa:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Ha Ön esetében eltérő, annak oka lehet egy hiba. Küldjön egy e-mailek snapshothelp@microsoft.com együtt a kódrészletet az Application Insights Instrumentation kulccsal.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Pillanatkép nézet: Nem lehet megszerezni a lokális változó vagy argumentum értéke
Ellenőrizze, hogy a [Application Insights bővítményt hely](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) telepítve van. Ha a probléma továbbra is fennáll, küldjön egy e-mailek snapshothelp@microsoft.com az Application Insights Instrumentation kulccsal.

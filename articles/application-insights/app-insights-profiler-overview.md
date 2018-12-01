---
title: Az Application Insights Profiler az Azure-beli éles üzemi alkalmazások profil |} A Microsoft Docs
description: A web server kódban a gyakori elérésű útvonal egy kis erőforrásigénnyel Profilkészítő meghatározásában.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 7780c10233a0ce256ee6e9015f40ea789516c25b
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726899"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profil éles üzemi alkalmazások az Azure-ban az Application insights segítségével
## <a name="enable-profiler-for-your-application"></a>Profiler engedélyezése az alkalmazáshoz

Application Insights Profiler az éles környezetben az Azure-ban futó alkalmazások teljesítmény-nyomkövetés tartalmazza. Az adatok automatikusan ipari méretekben anélkül, hogy negatívan befolyásolná a végfelhasználók rögzíti. Profiler segítségével azonosíthatja a "Forró" kód útvonal, amely a leghosszabb idő, amikor egy bizonyos webes kérés. 

A profiler működik együtt a következő Azure-szolgáltatások üzembe .net-alkalmazásokban. Az egyes szolgáltatás fut. a profiler engedélyezésével tartozó utasításokat az alábbi hivatkozások találhatók.

* [App Services](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Felhőszolgáltatások](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-alkalmazások](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuális gépek és a virtuális gép Scalesets](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Ha a Profiler engedélyezve van, de nem kapta meg a nyomkövetéseket, ellenőrizze, hogy [hibaelhárítási útmutatója.](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

Profiler futó helyi hivatalosan nem támogatott, de van néhány [megpróbálhatja utasításokat.](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)

## <a name="view-profiler-data"></a>A profiler-adatok megtekintése

Ahhoz, hogy a profiler nyomkövetések feltölteni az alkalmazás aktívan kezeli a kérelmeket. Ha egy kísérlet, a web app használatával hozhat létre kérelmek [Application Insights Teljesítménytesztelés](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Ha engedélyezte a Profiler újonnan, egy rövid terhelési teszt is futtathatja. A terhelési teszt futása közben nyomja le az **profil most** gombra a [ **Profiler beállítások lapon**](app-insights-profiler-settings.md#profiler-settings-page). Ha profiler már fut, a rendszer véletlenszerűen profilt körülbelül óránként egyszer, és a egy időtartama két perc. Ha az alkalmazás olyan kérelmeket kezel, Profiler feltölti nyomkövetések óránként.

Miután az alkalmazás fogad a forgalom egy része, és a profiler időm van a trances feltölteni, rendelkeznie kell a nyomkövetések megtekintéséhez. Ez a folyamat 5-10 percet is igénybe vehet. Nyomkövetések megtekintéséhez nyissa meg a **teljesítmény** ablaktáblán válassza **igénybe műveletek** a profiler hívásláncai megtekintése, majd válassza a **Profiler nyomkövetések** gombra.

![Application Insights teljesítmény panelen előzetes Profiler nyomkövetéseket][performance-blade]

Válassza ki a megjelenítendő kód szintű információkat töltött időt a kérelem végrehajtása a mintát.

![Application Insights nyomkövetés explorer][trace-explorer]

A nyomkövetés-kezelő az alábbi információkat jeleníti meg:

* **Legaktívabb ág megjelenítése**: megnyílik a legnagyobb levél csomópont, vagy valami legalább bezárása. A legtöbb esetben ez a csomópont teljesítménybeli szűk keresztmetszetet közelében van.
* **Címke**: a függvény vagy az esemény nevét. A fa kódot és (például az SQL és a HTTP-események) bekövetkezett eseményeket jeleníti meg. Az első esemény a kérés teljes időtartama jelöli.
* **Eltelt idő**: a művelet megkezdése és a művelet közötti időintervallum.
* **Amikor**: az idő, amikor a függvény vagy esemény futott relációban át más függvényeknek.

## <a name="how-to-read-performance-data"></a>Teljesítményadatok olvasása

A Microsoft service profiler az mintavételi és a rendszerállapot olyan kombinációját használja, az alkalmazás teljesítményének elemzését. Ha részletes gyűjtemény van folyamatban, a service profilerben minták a utasítás mutató minden gép CPU minden ezredmásodperces. Mindegyik minta a szál, amelyek végrehajtása folyamatban van a teljes verem rögzíti. Mivel foglalkoznak az adott hozzászóláslánccal kapcsolatban volt, egyaránt magas szinten, és a egy alacsonyabb szintű absztrakciós részletes információkat biztosít. A service profilerben eseményeit is más tevékenység összefüggések keresésére és okozati, beleértve a helyi események, a feladat párhuzamos könyvtár (TPL) események és a szál készlet események nyomon követéséhez.

A hívási veremben megjelenő idősor nézetének a mintavétel és rendszerállapot eredményét. Mindegyik minta rögzíti a teljes hívási verem, amely a szál, mert az a Microsoft .NET-keretrendszer és más keretrendszerekhez, amelyet a kódot tartalmazza.

### <a id="jitnewobj"></a>Objektum lefoglalása (CLR-beli! Igény szerinti\_új és a CLR-beli! Igény szerinti\_Newarr1)

**CLR-beli! Igény szerinti\_új** és **CLR-beli! Igény szerinti\_Newarr1** segédfüggvények a .NET-keretrendszer, amely memóriát lefoglalni a kezelt halommemóriában vannak. **CLR-beli! Igény szerinti\_új** akkor aktiválódnak, ha az objektum le van foglalva. **CLR-beli! Igény szerinti\_Newarr1** akkor aktiválódnak, ha egy objektum tömb le van foglalva. Ezek a függvények két általában gyors és viszonylag kis mennyiségű időt igénybe. Ha látja **CLR-beli! Igény szerinti\_új** vagy **CLR-beli! Igény szerinti\_Newarr1** jelentős mennyiségű időt igénybe az idővonalon, az azt jelzi, hogy a kód előfordulhat, hogy, sok objektum lefoglalása és jelentős mennyiségű memóriát használ.

### <a id="theprestub"></a>Kód (CLR-beli betöltése! ThePreStub)

**CLR-beli! ThePreStub** a .NET-keretrendszer, amely előkészíti a kód végrehajtásához először segítő függvénye. Ez általában tartalmaz, de nem korlátozódik az összeállítása – igény (szerinti JIT). Minden egyes C# metódus **CLR-beli! ThePreStub** kell hívni a folyamat élettartama során legfeljebb egyszer.

Ha **CLR-beli! ThePreStub** vesz igénybe a nagy kérelmek időtartamát, az azt jelenti, a kérelmet, amely végrehajtja a módszer az elsőt. A betölteni az első metódus a .NET-keretrendszer futtatókörnyezete ideje jelentős. Érdemes lehet egy melegítési folyamattal, amely végrehajtja a szerelvények a része, amely a kódot, mielőtt a felhasználók hozzáférése, illetve natív Image Generator (ngen.exe) érdemes használni.

### <a id="lockcontention"></a>Zárolási versenyt (CLR-beli! JITutil\_MonContention vagy CLR-beli! JITutil\_MonEnterWorker)

**CLR-beli! JITutil\_MonContention** vagy **CLR-beli! JITutil\_MonEnterWorker** azt jelzi, hogy az aktuális szál várakozik a zárolás feloldására. Ezt a szöveget, gyakran akkor jelenik meg, ha végre egy C# **ZÁROLÁSI** utasítás, akkor a **Monitor.Enter** metódus, vagy ha egy metódus hívása a **MethodImplOptions.Synchronized** attribútum. Zárolási versenyt általában akkor fordul elő amikor hozzászóláslánc _A_ egy zárolást és a szál beszerzi _B_ azonos zárolást beszerezni a hozzászóláslánc előtt megpróbálja _A_ szabadítja.

### <a id="ngencold"></a>Betöltés kódot ([HIDEG])

Ha a metódus nevét tartalmazza **[HIDEG]**, mint például **mscorlib.ni! [ COLD]System.Reflection.CustomAttribute.IsDefined**, a .NET-keretrendszer futtatókörnyezete kód végrehajtása az első alkalommal a nem optimalizált <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">profil interaktív optimalizálási</a>. Minden módszer esetén azt üzenetnek kell megjelennie a folyamat élettartama során legfeljebb egyszer.

Ha jelentős mennyiségű időt kérelem betöltése során a kód tart, ez azt jelzi, hogy a kérelem az elsőt a nem optimalizált része a metódus végrehajtásához. Fontolja meg egy melegítési folyamat, amely végrehajtja a része, amely a kódot, mielőtt a felhasználók-e férni.

### <a id="httpclientsend"></a>HTTP-kérelem küldése

Módszerek, például **HttpClient.Send** azt jelzik, hogy a kód vár egy HTTP-kérelem hajtható végre.

### <a id="sqlcommand"></a>Adatbázis-művelet

Módszerek, például **SqlCommand.Execute** azt jelzik, hogy a kód vár egy adatbázis-művelet befejezéséhez.

### <a id="await"></a>Várakozás (AWAIT\_idő)

**AWAIT\_idő** azt jelzi, hogy a kód vár egy másik feladat befejeződik. Ez általában akkor fordul elő a C# használatával **AWAIT** utasítást. Ha a kódja egy C# **AWAIT**, a szál unwinds, és visszaadja az irányítást a szálkészlet, és nincs a nincs szál való várakozás blokkolja a **AWAIT** befejezéséhez. Azonban logikailag, a hozzászóláslánc, amely volt a **AWAIT** van "Letiltva", és arra vár, hogy a művelet befejeződik. A **AWAIT\_idő** utasítás a feladat befejezésére való várakozás letiltott idejét jelzi.

### <a id="block"></a>Letiltott idő

**BLOCKED_TIME** azt jelzi, hogy a kód vár egy másik erőforrás elérhető legyen. Például, előfordulhat, hogy lehet váró szinkronizálási objektumra, elérhető legyen egy adott hozzászóláslánccal kapcsolatban, vagy a kérés befejezéséhez.

### <a id="cpu"></a>CPU-idő

A Processzor elfoglalt végrehajtásakor az utasításokat.

### <a id="disk"></a>Lemezmeghajtó kihasználtsága

Az alkalmazás által végzett lemezes műveletek.

### <a id="network"></a>Hálózati idő

Az alkalmazás által végzett hálózati műveletekre.

### <a id="when"></a>Amikor oszlop

A **amikor** oszlop egy vizualizációt, hogyan eltérőek lehetnek a határokat is beleértve mintákat egy csomópont gyűjtött idővel. A kérelem teljes skáláját 32 idő gyűjtők oszlik. A csomóponton a határokat is beleértve minták összegzi a rendszer az ezeket a gyűjtőket 32. Az egyes gyűjtők sáv jelzi. A sáv magassága méretezett értéket jelöli. Csomópontok megjelölt **CPU_TIME** vagy **BLOCKED_TIME**, vagy ahol való felhasználása erőforrás (például Processzor, lemez vagy szál) egy nyilvánvaló kapcsolat van, a sávon jelenti az egyik a használatalapú az erőforrások gyűjtőbe az időszak alatt. A következő metrikákhoz az értéke nagyobb, mint 100 %-os beszerezni a több erőforrást használ. Például ha használ, az átlagos során időközt, két processzor kap 200 %-os.

## <a name="limitations"></a>Korlátozások

Az alapértelmezett megőrzési időtartama pedig öt nap. Napi betöltött adatok maximális 10 GB-os.

Nem számítunk fel díjat a Profiler szolgáltatás. A webalkalmazás kell, hogy a Profiler szolgáltatást, az üzemeltetett legalább az alapszintű csomag, Web Apps.

## <a name="overhead-and-sampling-algorithm"></a>Terhelés és a mintavételezési algoritmus

Profiler véletlenszerűen futtat minden virtuális gép, amelyen az alkalmazást, amely rendelkezik a Profiler engedélyezve nyomkövetés rögzítése a két perc minden órában. Profiler fut, amikor azt hozzáadja az 5 %-os 15 %-os Processzor terhelését a kiszolgálóhoz.

## <a name="next-steps"></a>További lépések
Application Insights Profiler engedélyezése az Azure-alkalmazásokat
* [App Services](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Felhőszolgáltatások](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-alkalmazások](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuális gépek és a virtuális gép Scalesets](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png

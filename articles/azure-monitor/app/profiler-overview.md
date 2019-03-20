---
title: Az Application Insights Profiler az Azure-beli éles üzemi alkalmazások profil |} A Microsoft Docs
description: A web server kódban a gyakori elérésű útvonal egy kis erőforrásigénnyel Profilkészítő meghatározásában.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: c42de8cf189c0ebaf5f13ef5971ad91d14d862fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850275"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profil éles üzemi alkalmazások az Azure-ban az Application insights segítségével
## <a name="enable-application-insights-profiler-for-your-application"></a>Application Insights Profiler engedélyezése az alkalmazáshoz

Az Azure Application Insights Profiler az Azure-beli éles környezetben futó alkalmazások teljesítmény-nyomkövetés biztosít. Profiler anélkül, hogy negatívan befolyásolná a felhasználók számára az adatok automatikusan méretezett rögzíti. Profiler segítségével azonosíthatja a "Forró" kód útvonal, amely a leghosszabb időt, amikor egy bizonyos webes kérés azt kezeli. 

Profiler együttműködik a telepített .NET-alkalmazások, a következő Azure-szolgáltatások. Az egyes service Profiler engedélyezése részletes útmutatást a következő hivatkozásokon találhatók.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure virtuális gépek és virtuálisgép-méretezési csoportokban](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**Előzetes verzió** ASP.NET Core Azure Linux-webalkalmazások](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Ha engedélyezve van a Profiler, de nem kapta meg a nyomkövetéseket, ellenőrizze, hogy [hibaelhárítási útmutatója](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Profiler-adatok megtekintése

A Profiler nyomkövetések feltölteni az alkalmazás kell lennie aktívan kezelési kérelem. Ha egy kísérletet, létrehozhat kéréseket a webalkalmazáshoz használatával [Application Insights Teljesítménytesztelés](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Ha engedélyezte a Profiler újonnan, egy rövid terhelési teszt is futtathatja. A terhelési teszt futtatásakor válassza ki a **profil most** gombot a [ **Profiler beállítások** panelen](profiler-settings.md#profiler-settings-pane). Profiler futtatásakor profilokat véletlenszerűen körülbelül óránként egyszer, és a egy időtartama két perc. Ha az alkalmazás olyan kérelmeket kezel, Profiler nyomkövetések óránként tölt fel.

Miután az alkalmazás fogad a forgalom egy része, és a Profiler időm van a nyomkövetések feltölteni, rendelkeznie kell a nyomkövetések megtekintéséhez. Ez a folyamat 5-10 percet is igénybe vehet. Megtekintéséhez a nyomokat, a a **teljesítmény** panelen válassza **igénybe műveletek**, majd válassza ki a **Profiler nyomkövetések** gomb.

![Application Insights teljesítmény panelen előzetes Profiler nyomkövetéseket][performance-blade]

Válassza ki a megjelenítendő kód szintű információkat töltött időt a kérelem végrehajtása a mintát.

![Application Insights nyomkövetés explorer][trace-explorer]

A nyomkövetés-kezelő az alábbi információkat jeleníti meg:

* **Legaktívabb ág megjelenítése**: Megnyílik a legnagyobb levél csomópont, vagy valami legalább bezárása. A legtöbb esetben ez a csomópont teljesítménybeli szűk keresztmetszetet közelében van.
* **Címke**: A függvény vagy esemény neve. A fa jeleníti meg a kódot és az eseményeket, például az SQL és a HTTP-esemény. Az első esemény a kérés teljes időtartama jelöli.
* **Eltelt idő**: A művelet megkezdése és a művelet közötti időintervallum.
* **Amikor**: Az idő, ha a függvény vagy esemény futott relációban át más függvényeknek.

## <a name="how-to-read-performance-data"></a>Teljesítményadatok olvasása

A Microsoft service profiler az mintavételi és a rendszerállapot olyan kombinációját használja, az alkalmazás teljesítményének elemzését. Ha részletes gyűjtemény van folyamatban, a service profilerben minták a utasítás mutató minden gép CPU minden ezredmásodperces. Mindegyik minta a szál, amelyek végrehajtása folyamatban van a teljes verem rögzíti. Mivel foglalkoznak az adott hozzászóláslánccal kapcsolatban volt, a magas és alacsony szintű absztrakció egyaránt részletes információkat biztosít. A service profilerben eseményeit is más tevékenység összefüggések keresésére és okozati, beleértve a helyi események, a feladat párhuzamos könyvtár (TPL) események és a szál készlet események nyomon követéséhez.

A hívási veremben megjelenő idősor nézetének a mintavétel és rendszerállapot eredményét. Mert valamennyi példa a teljes hívási verem, amely a szál rögzíti, és más keretrendszerekhez, amelyet a Microsoft .NET-keretrendszer származó kódot tartalmaz.

### <a id="jitnewobj"></a>Objektum lefoglalása (CLR-beli! Igény szerinti\_új és a CLR-beli! Igény szerinti\_Newarr1)

**CLR-beli! Igény szerinti\_új** és **CLR-beli! Igény szerinti\_Newarr1** segédfüggvények .NET-keretrendszer, amely memóriát lefoglalni a kezelt halommemóriában vannak. **CLR-beli! Igény szerinti\_új** akkor aktiválódnak, ha az objektum le van foglalva. **CLR-beli! Igény szerinti\_Newarr1** akkor aktiválódnak, ha egy objektum tömb le van foglalva. Ezek a függvények két általában gyors és viszonylag kis mennyiségű időt igénybe. Ha **CLR-beli! Igény szerinti\_új** vagy **CLR-beli! Igény szerinti\_Newarr1** veszi a sok időt az idővonalon, a kód előfordulhat, hogy sok objektum lefoglalása és jelentős mennyiségű memóriát használ.

### <a id="theprestub"></a>Kód (CLR-beli betöltése! ThePreStub)

**CLR-beli! ThePreStub** a .NET-keretrendszer, amely előkészíti a kód végrehajtásához először segítő funkciója. A végrehajtási általában tartalmaz, de nem korlátozódik az – igény (szerinti JIT) fordítási. Az egyes C# metódus **CLR-beli! ThePreStub** kell egyszer hívhatók meg legfeljebb egy folyamat során.

Ha **CLR-beli! ThePreStub** kérelmek, a kérelem egy hosszú időt vesz igénybe az elsőt a metódus végrehajtásához. A .NET-keretrendszer futtatókörnyezete betölteni az első módszer ideje jelentős. Érdemes lehet egy melegítési folyamattal, amely végrehajtja a szerelvények a része, amely a kódot, mielőtt a felhasználók hozzáférése, illetve natív Image Generator (ngen.exe) érdemes használni.

### <a id="lockcontention"></a>Zárolási versenyt (CLR-beli! JITutil\_MonContention vagy CLR-beli! JITutil\_MonEnterWorker)

**CLR-beli! JITutil\_MonContention** vagy **CLR-beli! JITutil\_MonEnterWorker** azt jelzi, hogy az aktuális szál várakozik a zárolás feloldására. Ezt a szöveget végrehajtásakor gyakran megjelenik egy C# **ZÁROLÁSI** utasítással, meghívása a **Monitor.Enter** metódus, vagy a metódus meghívása a **MethodImplOptions.Synchronized**attribútum. Zárolási versenyt általában akkor fordul elő amikor hozzászóláslánc _A_ egy zárolás és a szál beszerzi _B_ azonos zárolást beszerezni a hozzászóláslánc előtt megpróbálja _A_ szabadítja.

### <a id="ngencold"></a>Betöltés kódot ([HIDEG])

Ha a metódus nevét tartalmazza **[HIDEG]**, mint például **mscorlib.ni! [ COLD]System.Reflection.CustomAttribute.IsDefined**, .NET-keretrendszer futtatókörnyezete végrehajtása kód által nem optimalizált először [profil interaktív optimalizálási](https://msdn.microsoft.com/library/e7k32f4k.aspx). Minden módszer esetén azt üzenet legfeljebb egyszer a folyamat során.

Kérelem ideje jelentős mennyiségű kódot betöltése vesz igénybe, a kérelem-e az elsőt a nem optimalizált része a metódus végrehajtásához. Fontolja meg egy melegítési folyamat, amely végrehajtja a része, amely a kódot, mielőtt a felhasználók-e férni.

### <a id="httpclientsend"></a>HTTP-kérelem küldése

Például módszerek **HttpClient.Send** azt jelzik, hogy a kód vár egy HTTP-kérelem hajtható végre.

### <a id="sqlcommand"></a>Adatbázis-művelet

Például módszerek **SqlCommand.Execute** azt jelzik, hogy a kód vár egy adatbázis-művelet befejezéséhez.

### <a id="await"></a>Waiting (AWAIT\_TIME)

**AWAIT\_idő** azt jelzi, hogy a kód vár egy másik feladat befejeződik. Ez a késleltetés általában akkor fordul elő a a C# **AWAIT** utasítást. Ha a kódja egy C# **AWAIT**, a szál unwinds, és visszaadja az irányítást a szálkészlet, és nincs a nincs szál való várakozás blokkolja a **AWAIT** befejezéséhez. Azonban logikailag, a hozzászóláslánc, amely volt a **AWAIT** "zárolva van," és a művelet befejezésére vár. A **AWAIT\_idő** utasítás a feladat befejezésére való várakozás letiltott idejét jelzi.

### <a id="block"></a>Letiltott idő

**BLOCKED_TIME** azt jelzi, hogy a kód vár egy másik erőforrás elérhető legyen. Például, előfordulhat, hogy lehet váró szinkronizálási objektumra, elérhető legyen egy adott hozzászóláslánccal kapcsolatban, vagy a kérés befejezéséhez.

### <a name="unmanaged-async"></a>Unmanaged Async

.NET-keretrendszer ETW eseményeket bocsát ki, és adja át a tevékenységazonosítók szálak között, hogy az aszinkron hívásokat szálakon követhető nyomon. Nem felügyelt kód (natív), és néhány régebbi stílusok aszinkron kód hiányoznak az ezen események és a tevékenységazonosítók, így a profiler nem állapítható meg, hogy milyen szál, és mi függvények futnak a szálon. "Nem felügyelt aszinkron" Ez van jelölve, a hívási veremben. Ha az ETW-fájl letöltéséhez, előfordulhat, hogy tudják használni [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) a részletesebb elemzéseket kaphat, hogy mi történik.

### <a id="cpu"></a>CPU-idő

A Processzor elfoglalt végrehajtásakor az utasításokat.

### <a id="disk"></a>Lemezmeghajtó kihasználtsága

Az alkalmazás által végzett lemezes műveletek.

### <a id="network"></a>Hálózati idő

Az alkalmazás által végzett hálózati műveletekre.

### <a id="when"></a>Amikor oszlop

A **amikor** oszlop egy vizualizációt, hogyan eltérőek lehetnek a határokat is beleértve mintákat egy csomópont gyűjtött idővel. A kérelem teljes skáláját 32 idő gyűjtők oszlik. A csomóponton a határokat is beleértve minták összegzi a rendszer az ezeket a gyűjtőket 32. Az egyes gyűjtők sáv jelzi. A sáv magassága méretezett értéket jelöli. Csomópontok megjelölt **CPU_TIME** vagy **BLOCKED_TIME**, vagy ahol való felhasználása erőforrás (például Processzor, lemez vagy szál) egy nyilvánvaló kapcsolat van, a sávon jelenti az egyik a használatalapú az erőforrások során a gyűjtőhöz. A következő metrikákhoz az értéke nagyobb, mint 100 %-os beszerezni a több erőforrást használ. Például ha használ, az átlagos során időközt, két processzor kap 200 %-os.

## <a name="limitations"></a>Korlátozások

Az alapértelmezett megőrzési időtartama pedig öt nap. Napi betöltött adatok maximális 10 GB-os.

Nem számítunk fel díjat a Profiler szolgáltatás. Meg vele, a webes alkalmazásnak rendelkeznie kell legalább az alapszintű csomag, az Azure App Service Web Apps funkciójával üzemeltetett.

## <a name="overhead-and-sampling-algorithm"></a>Terhelés és a mintavételezési algoritmus

Profiler véletlenszerűen futtat minden virtuális gép, amelyen az alkalmazást, amely rendelkezik a Profiler engedélyezve nyomkövetés rögzítése a két perc minden órában. Profiler fut, amikor azt hozzáadja az 5. 15 %-os Processzor terhelését a kiszolgálóhoz.

## <a name="next-steps"></a>További lépések
Application Insights Profiler engedélyezése az Azure-alkalmazásokat. Is látható:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure virtuális gépek és virtuálisgép-méretezési csoportokban](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png

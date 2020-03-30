---
title: Az Azure-beli éles alkalmazások profilkészítési alkalmazásai az Application Insights Profiler segítségével
description: Azonosítsa a webkiszolgáló kódjában lévő elérési utat egy alacsony helyigényű profilkészítővel.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ce952bd248640d03fcff43284707614577df8469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671647"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Az Azure-beli éles alkalmazások profilkészítési alkalmazásai az Application Insights segítségével
## <a name="enable-application-insights-profiler-for-your-application"></a>Az Application Insights Profiler engedélyezése az alkalmazáshoz

Az Azure Application Insights Profiler teljesítménynyomkövetéseket biztosít az Azure-ban éles környezetben futó alkalmazásokhoz. A Profiler automatikusan rögzíti az adatokat a felhasználók negatív befolyásolása nélkül. Profiler segít azonosítani a "forró" kód elérési útja, amely a leghosszabb ideig tart, amikor egy adott webes kérelmet kezel. 

A Profiler a következő Azure-szolgáltatásokban telepített . A Profiler engedélyezésére vonatkozó konkrét utasítások az alábbi linkeken találhatók.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure virtuális gépek és a virtuális gépek méretezési készletei](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**ELŐNÉZET** ASP.NET Core Azure Linux webalkalmazások](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Ha engedélyezte a Profiler-t, de nem látja a nyomkövetéseket, olvassa el [a Hibaelhárítási útmutatót.](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

## <a name="view-profiler-data"></a>Profilozó adatainak megtekintése

Ahhoz, hogy a Profiler feltöltheti a nyomkövetéseket, az alkalmazásnak aktívan kell kezelnie a kérelmeket. Ha kísérletet végez, az [Application Insights teljesítménytesztelésével](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)kéréseket hozhat létre a webalkalmazáshoz. Ha újonnan engedélyezte a Profiler-t, futtathat egy rövid terhelési tesztet. A betöltési teszt futása közben válassza a **Profil most** gombot a [ **Profilkezelő beállításai** ablaktáblán](profiler-settings.md). Amikor profiler fut, véletlenszerűen profilt körülbelül óránként egyszer, és időtartama két percig. Ha az alkalmazás a kérelmek folyamatos adatfolyamát kezeli, a Profiler óránként feltölti a nyomkövetéseket.

Miután az alkalmazás kap némi forgalmat, és Profiler volt ideje feltölteni a nyomkövetéseket, akkor meg kell tekinteni. Ez a folyamat 5-10 percet is igénybe vehet. A nyomkövetések megtekintéséhez a **Teljesítmény** ablaktáblán válassza a **Műveletek művelet lehetőséget,** majd kattintson a **Profilkezelő nyomkövetései** gombra.

![Az Application Insights teljesítményablakának előnézeti előzményei][performance-blade]

Válasszon ki egy mintát a kérés végrehajtásával töltött idő kódszintű bontásának megjelenítéséhez.

![Az Application Insights nyomkövetési kezelője][trace-explorer]

A nyomkövetési kezelő a következő információkat jeleníti meg:

* **Hot Path**megjelenítése: Megnyitja a legnagyobb levélcsomópontot, vagy legalábbis valami közelit. A legtöbb esetben ez a csomópont közel van a teljesítmény szűk keresztmetszetéhez.
* **Címke**: A függvény vagy esemény neve. A fa kód és események, például SQL- és HTTP-események kombinációját jeleníti meg. A legfelső esemény a kérelem teljes időtartamát jelöli.
* **Eltelt**: A művelet kezdete és a művelet vége közötti időintervallum.
* **Mikor**: Az az időpont, amikor a függvény vagy esemény más függvényekhez képest futott.

## <a name="how-to-read-performance-data"></a>A teljesítményadatok olvasása

A Microsoft szolgáltatásprofilozó mintavételi módszerek és műszerek kombinációját használja az alkalmazás teljesítményének elemzéséhez. Amikor részletes gyűjtemény van folyamatban, a szolgáltatás profiler minták a utasítás mutató -ból mindegyik gép CPU mind ezredmásodperc. Minden minta rögzíti a szál teljes hívási vermét, amely jelenleg hajt végre. Részletes tájékoztatást ad arról, hogy mit csinált ez a téma, mind magas szinten, mind alacsony szintű absztrakcióban. A szolgáltatásprofilozó más eseményeket is gyűjt a tevékenység korrelációjának és ok-okozati összefüggésének nyomon követéséhez, beleértve a környezetváltási eseményeket, a Feladat párhuzamos könyvtár (TPL) eseményeit és a szálkészlet-eseményeket.

Az idővonal nézetben megjelenített hívásverem a mintavételés és a műszerezés eredménye. Mivel minden minta rögzíti a szál teljes hívási vermét, a Microsoft .

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Objektumkiosztás (clr! JIT\_Új vagy clr! JIT\_Newarr1)

**Clr! JIT\_Új** és **clr! A\_JIT Newarr1** a .NET Framework segítő függvénye, amely memóriát foglal le egy felügyelt halommemóriából. **clr! A\_JIT New** meghívása egy objektum lefoglalásakor történik. **clr! A\_JIT Newarr1 meghívása** objektumtömb lefoglalásakor történik. Ez a két funkció általában gyors, és viszonylag kis mennyiségű időt vesz igénybe. Ha **clr! JIT\_Új** vagy **clr! JIT\_Newarr1** vesz igénybe sok időt az idővonalon, a kód lehet, hogy sok objektumot, és fogyasztása jelentős mennyiségű memóriát.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Betöltési kód (clr! ThePreStub)

**clr! A ThePreStub** egy segítő függvény a .NET Framework rendszerben, amely előkészíti a kódot az első alkalommal végrehajtandó. Ez a végrehajtás általában magában foglalja, de nem kizárólagosan, just-in-time (JIT) összeállítása. Minden C# módszerhez **clr! A PreStub ot** legkorábban egyszer kell meghívni a folyamat során.

Ha **clr! A PreStub** hosszú időt vesz igénybe a kérelem, a kérelem az első, amely végrehajtja a metódust. A . Érdemes lehet olyan bemelegedési folyamatot használni, amely végrehajtja a kód adott részét, mielőtt a felhasználók elérnék azt, vagy fontolja meg a Natív képgenerátor (ngen.exe) futtatását a szerelvényeken.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Lock viszálykodás (clr! JITutil\_MonContention vagy clr! JITutil\_MonEnterWorker)

**Clr! JITutil\_MonContention** vagy **clr! A JITutil\_MonEnterWorker** azt jelzi, hogy az aktuális szál a zárolás feloldására vár. Ez a szöveg gyakran jelenik meg, amikor c# **LOCK** utasítást hajt végre, meghívja a **Monitor.Enter** metódust, vagy meghív egy metódust a **MethodImplOptions.Synchronized** attribútummal. A zárolási versengés általában akkor fordul elő, amikor _az A_ szál zárolást szerez, a _B_ szál pedig megpróbálja megszerezni ugyanazt a zárolást, mielőtt az _A_ szál felszabadítja azt.

### <a name="loading-code-cold"></a><a id="ngencold"></a>Betöltési kód ([HIDEG])

Ha a metódus neve **[HIDEG]** nevet tartalmaz, például **mscorlib.ni![ COLD]System.Reflection.CustomAttribute.IsDefined**, . [profile-guided optimization](/cpp/build/profile-guided-optimizations) Minden egyes módszer esetében legkell, hogy legkorábban egyszer jelenjen meg a folyamat során.

Ha a kód betöltése jelentős időt vesz igénybe a kérelemhez, a kérelem az első, amely végrehajtja a metódus nem optimalizált részét. Fontolja meg egy bemelegedési folyamat, amely végrehajtja a kód adott részét, mielőtt a felhasználók elérése.

### <a name="send-http-request"></a><a id="httpclientsend"></a>HTTP-kérelem küldése

Az olyan módszerek, mint a **HttpClient.Send,** azt jelzik, hogy a kód egy HTTP-kérelem teljesítésére vár.

### <a name="database-operation"></a><a id="sqlcommand"></a>Adatbázis-művelet

Az **sqlcommand.execute** metódusok azt jelzik, hogy a kód egy adatbázis-művelet befejezésére vár.

### <a name="waiting-await_time"></a><a id="await"></a>Várakozás (AWAIT\_TIME)

**Az\_AWAIT TIME** azt jelzi, hogy a kód egy másik feladat befejezésére vár. Ez a késleltetés általában a C# **AWAIT** utasítással történik. Amikor a kód c# **await-et**végez, a szál leold, és visszaadja az irányítást a szálkészletnek, és nincs blokkolva szál, amely az **AWAIT befejezésére** vár. Logikusan azonban az **AWAIT-et elvégzett** szál "blokkolva" van, és a művelet befejezésére vár. Az **AWAIT\_TIME** utasítás a feladat befejezésére váró blokkolt időt jelzi.

### <a name="blocked-time"></a><a id="block"></a>Zárolt idő

**BLOCKED_TIME** azt jelzi, hogy a kód egy másik erőforrásra vár. Előfordulhat például, hogy egy szinkronizálási objektumra, egy szál elérhetővé vagy egy kérés befejezésére vár.

### <a name="unmanaged-async"></a>Nem felügyelt Async

A .NET keretrendszer ETW-eseményeket bocsát ki, és tevékenységazonosítókat ad át a szálak között, hogy az aszinkron hívások nyomon követhetők legyenek a szálak között. A nem felügyelt kód (natív kód) és az aszinkron kód néhány régebbi stílusából hiányoznak ezek az események és tevékenységazonosítók, így a profilozó nem tudja megmondani, hogy milyen szál és milyen függvények futnak a szálon. Ez a címzés "Nem felügyelt async" a hívásveremben. Ha letölti az ETW fájlt, akkor a [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) segítségével több betekintést nyerhet abba, hogy mi történik.

### <a name="cpu-time"></a><a id="cpu"></a>PROCESSZOR idő

A processzor az utasítások végrehajtásával van elfoglalva.

### <a name="disk-time"></a><a id="disk"></a>Lemezidő

Az alkalmazás lemezműveleteket hajt végre.

### <a name="network-time"></a><a id="network"></a>Hálózati idő

Az alkalmazás hálózati műveleteket hajt végre.

### <a name="when-column"></a><a id="when"></a>Amikor oszlop

A **Mikor** oszlop annak vizualizálása, hogy a csomóponthoz gyűjtött INKLUZív minták hogyan változnak az idő múlásával. A kérelem teljes tartománya 32 időgyűjtőre van felosztva. Az adott csomópontra vonatkozó inkluzív minták ebben a 32 vödörben halmozódnak fel. Minden vödör sávként jelenik meg. A sáv magassága méretezett értéket jelöl. A **CPU_TIME** vagy **BLOCKED_TIME**megjelölt csomópontok esetében, vagy ahol nyilvánvaló kapcsolat áll fenn egy erőforrás (például processzor, lemez vagy szál) felhasználásával, a sáv az egyik erőforrás felhasználását jelöli a gyűjtő alatt. Ezek a metrikák esetében több erőforrás felhasználásával 100 százaléknál nagyobb értéket kaphat. Ha például egy időköz alatt átlagosan két processzort használ, akkor 200 százalékot kap.

## <a name="limitations"></a>Korlátozások

Az alapértelmezett adatmegőrzési időszak öt nap. A naponta betöltött maximális adat 10 GB.

A Profiler szolgáltatás használatáért nem számítunk fel díjat. Ahhoz, hogy használni, a webapp kell üzemeltetni legalább az azure App Service webalkalmazások szolgáltatásának alapszintjén.

## <a name="overhead-and-sampling-algorithm"></a>Általános és mintavételi algoritmus

Profiler véletlenszerűen fut két perc óránként minden virtuális gépen, amely üzemelteti az alkalmazást, amely profiler engedélyezve van a nyomkövetések rögzítésére. Amikor a Profiler fut, 5-ről 15 százalékos CPU-terheléssel növeli a kiszolgálót.

## <a name="next-steps"></a>További lépések
Engedélyezze az Application Insights Profiler-t az Azure-alkalmazásához. Lásd még:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure virtuális gépek és a virtuális gépek méretezési készletei](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png

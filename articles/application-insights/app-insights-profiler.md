---
title: Élő webes alkalmazásprofilok szerkesztése az Application Insights Profiler az Azure-on |} A Microsoft Docs
description: A web server kódban a gyakori elérésű útvonal egy kis erőforrásigénnyel Profilkészítő meghatározásában.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 6048a17bf50ecac691c7cf687f87e454c54ee9d9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521883"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profil élő Azure-webalkalmazások az Application insights segítségével

Ez a funkció az Azure Application Insights szolgáltatás általánosan elérhető az Azure App Service Web Apps funkcióját, és az Azure-beli számítási erőforrások előzetes verzióban érhető el. Információ kapcsolatos [helyi használatának profiler](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).

Ez a cikk ismerteti az egyes módszerek az élő webalkalmazásának használatakor töltött idő mennyiségét [Application Insights](app-insights-overview.md). Az Application Insights Profiler eszközt, amely az alkalmazás által kiszolgált is élő kérelmek részletes profilok jeleníti meg. Profiler kiemeli a *gyakori elérésű útvonal* , amely a legtöbb időt használja. Különböző válaszidők a kérelmek mintavételi alapon profilkészítés során létrehozott. Különböző technikák használatával, minimálisra csökkentheti a társított az alkalmazás terhelését.

Profiler az ASP.NET és az ASP.NET Core web Apps, Web Apps szolgáltatást futtató jelenleg működik. Az alapszintű szolgáltatásszintet vagy újabb verziója szükséges Profiler használatára.

## <a id="installation"></a> A Web Apps számára Profiler engedélyezése

Miután telepített egy webalkalmazást, függetlenül attól, ha az App Insights SDK forráskódját szerepel, akkor tegye a következőket:

1. Nyissa meg a **App Services** panel az Azure Portalon.
1. Navigáljon a **beállítások > figyelés** ablaktáblán.

   ![Az App Insights engedélyezése az App Services portálon](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Lehet, kövesse az utasításokat a panelen hozzon létre egy új erőforrást, vagy válasszon egy meglévő App Insights-erőforrás a webes alkalmazás figyelésére. Fogadja el az összes alapértelmezett beállításokat. **Kódszintű diagnosztika** alapértelmezés szerint be van kapcsolva, és lehetővé teszi, hogy a Profiler.

   ![App Insights-webhelybővítményt hozzáadása][Enablement UI]

1. Profiler az App Insights-webhelybővítményt a most már telepítve van, és engedélyezve van az App Services Alkalmazásbeállítás használatával.

    ![A Profiler Alkalmazásbeállítás][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Profiler engedélyezése az Azure-beli számítási erőforrások (előzetes verzió)

További információ: a [előzetes verzióját Profiler az Azure-beli számítási erőforrások](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>A profiler-adatok megtekintése

Ellenőrizze, hogy az alkalmazás forgalmat fogad. Akkor használatos, ha kísérlet, a web app használatával hozhat létre kérelmek [Application Insights Teljesítménytesztelés](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Ha engedélyezte a Profiler újonnan, egy rövid terheléstesztje körülbelül 15 perc, amely kell létrehoznia a profiler hívásláncai futtathatja. Ha a Profiler egy ideje már engedélyezve van, vegye figyelembe, hogy a Profiler véletlenszerűen két alkalommal óránként fut, és a egy időtartama két perc, a rendszer minden alkalommal, tartsa futtatja. Azt javasoljuk, hogy a profiler hívásláncai minta kap egy órát a terhelési teszt először futtassa.

Miután az alkalmazás megkapja a forgalom egy része, nyissa meg a **teljesítmény** panelen válassza **műveletek is igénybe** a profiler hívásláncai megtekintése, majd válassza a **Profiler nyomkövetések** gombra.

![Application Insights teljesítmény panelen előzetes Profiler nyomkövetéseket][performance-blade-v2-examples]

Válassza ki a megjelenítendő kód szintű információkat töltött időt a kérelem végrehajtása a mintát.

![Application Insights nyomkövetés explorer][trace-explorer]

A nyomkövetés-kezelő az alábbi információkat jeleníti meg:

* **Legaktívabb ág megjelenítése**: megnyílik a legnagyobb levél csomópont, vagy valami legalább bezárása. A legtöbb esetben ez a csomópont teljesítménybeli szűk keresztmetszetet mellé.
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

Ha **CLR-beli! ThePreStub** eltart kérelmek időtartamát, az azt jelenti, hogy a kérelem, amely végrehajtja a módszer az elsőt. A betölteni az első metódus a .NET-keretrendszer futtatókörnyezete ideje jelentős. Érdemes lehet egy melegítési folyamattal, amely végrehajtja a szerelvények a része, amely a kódot, mielőtt a felhasználók hozzáférése, illetve natív Image Generator (ngen.exe) érdemes használni.

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

A további kiszolgálók, amelyek érhetők el az kevésbé befolyásolja az alkalmazás általános teljesítménye rendelkezik Profiler az alkalmazás futtatásához. Ennek az oka a mintavételezési algoritmus futó kiszolgálók csak 5 %-os bármikor Profiler eredményez. További kiszolgálók érhetők el kívánja eltolni a kiszolgáló terhelését a Profiler futtatásával okozott webes kérések kiszolgálására.

## <a name="disable-profiler"></a>Profiler letiltása

Profiler alatt egy adott webalkalmazás-példány újraindítása vagy leállítása **Webjobs-feladatok**, nyissa meg a Web Apps erőforrás. Profiler törléséhez lépjen a **bővítmények**.

![Profiler letiltása a webjobs-feladat][disable-profiler-webjob]

Azt javasoljuk, hogy a Profiler engedélyezve a web apps a lehető legkorábbi felderítéséhez okoznak teljesítményproblémákat.

WebDeploy használatával a webes alkalmazások üzembe helyezése módosításokat, győződjön meg arról, hogy törölhető üzembe helyezés során zárja ki az App_Data mappában. Ellenkező esetben a Profiler szerepkörbővítmény-fájlok törlődnek a következő alkalommal, amikor telepíti a webalkalmazás az Azure-bA.


## <a id="troubleshooting"></a>Hibaelhárítás

### <a name="too-many-active-profiling-sessions"></a>Túl sok aktív profilkészítési munkamenetek

Engedélyezheti a Profiler jelenleg legfeljebb négy Azure web apps és az azonos service-csomag futó üzembe helyezési pontok. A Profiler webjobs-feladat túl sok aktív profilkészítési munkamenetek jelent, ha néhány webes alkalmazás áthelyezése egy másik service-csomagot.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hogyan állapítható meg, hogy fut-e az Application Insights Profiler?

Profiler fut egy folyamatos webjobs-feladat a webalkalmazásban. Megnyithatja a webes alkalmazás-erőforrást a [az Azure portal](https://portal.azure.com). Az a **WebJobs** ablaktáblán állapotának ellenőrzéséhez **ApplicationInsightsProfiler**. Ha ez nem fut, nyissa meg a **naplók** további információért.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Miért nem található stack tudnák példákkal szemléltetni, annak ellenére, hogy a Profiler fut-e?

Az alábbiakban néhány dolgot, amelyeket megtekinthet:

* Győződjön meg arról, hogy a web app service-csomag alapszintű csomag vagy újabb verziója.
* Győződjön meg arról, hogy a webes alkalmazás Application Insights SDK 2.2-es Beta vagy később engedélyezni.
* Győződjön meg arról, hogy rendelkezik-e a webalkalmazást, a **állítani az APPINSIGHTS_INSTRUMENTATIONKEY** a azonos kialakítási kulcsot, amelyet az Application Insights SDK-beállítások.
* Győződjön meg arról, hogy a webalkalmazás fut-e a .NET-keretrendszer 4.6.
* Ha a webalkalmazás egy ASP.NET Core-alkalmazást, futnia kell, hogy legalább az ASP.NET Core 2.0.

Miután elindult a Profiler, van egy rövid melegítési időszak, amely során Profiler aktívan gyűjt több teljesítmény-nyomkövetés. Ezt követően Profiler két percig óránként gyűjti a teljesítmény-nyomkövetés.

> [!NOTE]
> A profiler-ügynök, amely megakadályozza, hogy átveszi a futó ASP.NET Core 2.1-es alkalmazások nyomkövetések feltöltése programhiba van. Azt is dolgozunk a javítást, és fog rendelkezni, készen áll hamarosan.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Az Azure Service profiler volt használata Mi történt, a?

Application Insights Profiler engedélyezése esetén az Azure Service profiler-ügynök le van tiltva.

### <a id="double-counting"></a>A párhuzamos szálak számbavételi dupla

Bizonyos esetekben a teljes időmetrika a stack megjelenítőben több, mint a kérelem időtartama.

Ez a helyzet akkor fordulhat elő, amikor két vagy több szál társítva egy kérelmet, és párhuzamosan működnek. Ebben az esetben a szálak teljes ideje több, mint az eltelt idő. Egy szálat a másik hajtható végre, előfordulhat, hogy lehet vár. A megjelenítő észlelés ez, és kihagyja a kevésbé fontos Várakozás, de azt hibák oldalán túl sok információt jelenít meg, hanem hagyja ki, mit lehet, hogy kritikus fontosságú információkhoz.

Amikor párhuzamos szálak látható a nyomkövetés, határozza meg, mely beszélgetések várnak, hogy meg tudja állapítani, hogy a kritikus fontosságú a kérelem elérési útját. A legtöbb esetben a gyors állapotba kerül szál egyszerűen a más szálak vár. A más szálak összpontosít, és figyelmen kívül hagyja a várakozó szál az az idő.

### <a id="issue-loading-trace-in-viewer"></a>Nincsenek profilkészítési adatok

Az alábbiakban néhány dolgot, amelyeket megtekinthet:

* Ha a megtekinteni kívánt adatok régebbi, mint egy pár hétig, próbálkozzon az Időszűrő korlátozása, és próbálkozzon újra.
* Győződjön meg arról, hogy proxyk vagy a tűzfal nem letiltotta a hozzáférést https://gateway.azureserviceprofiler.net.
* Győződjön meg arról, hogy az Application Insights kialakítási kulcsot használja az alkalmazásban megegyezik az Application Insights-erőforrást, engedélyezze a profilkészítés során használt. A kulcs általában az ApplicationInsights.config fájlban, de a web.config vagy az app.config fájlban is lehet.

### <a name="error-report-in-the-profiling-viewer"></a>Hiba történt a jelentés a profilkészítési megjelenítőben

A portál támogatási jegyet is küldhet. Győződjön meg arról, a korrelációs Azonosítót, a hibaüzenet tartalmazza.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Központi telepítési hiba: nem üres könyvtár "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok

Ha az a Web Apps erőforrás-webalkalmazás újraalkalmaz a Profiler engedélyezve van, a következőhöz hasonló üzenetet láthatja:

*Könyvtár nem üres "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok*

Ez a hiba akkor fordul elő, ha futtatja a Web Deploy parancsfájlokat, illetve a Visual Studio Team Services üzembe helyezési folyamat. A megoldás a következő további telepítési paramétereket adhat hozzá a Web Deploy feladathoz:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ezeket a paramétereket törölje a mappát, amely az Application Insights Profiler használja, és az ismételt üzembehelyezési folyamat feloldása. A Profiler-példányt, amely jelenleg fut, nem befolyásolják.

## <a name="manual-installation"></a>Manuális telepítés

Profiler konfigurálásakor a webalkalmazás-beállítások végrehajtott frissítéseket. Ha a környezet számára szükséges, manuálisan alkalmazhatja a frissítések. Például lehet, hogy az alkalmazás a Web Apps környezetben fut a powerapps szolgáltatásra.

1. Az a **webes alkalmazás-vezérlő** ablaktáblán nyissa meg **beállítások**.
1. Állítsa be **.Net Framework version** való **v4.6**.
1. Állítsa be **Always On** való **a**.
1. Adja hozzá a **állítani az APPINSIGHTS_INSTRUMENTATIONKEY** beállítást, és állítsa az értékét a azonos kialakítási kulcsot, az SDK által használt alkalmazás.
1. Nyissa meg **speciális eszközök**.
1. Válassza ki **Go** a Kudu-webhely megnyitásához.
1. Válassza ki a Kudu webhelyen **Webhelybővítményekkel**.
1. Telepítés **az Application Insights** az Azure Web Apps katalógusából.
1. Indítsa újra a webalkalmazást.

## <a id="profileondemand"></a> Profiler aktiválása manuálisan

Profiler manuálisan egy gombra kattintással is elindítható. Tegyük fel, hogy egy webes teljesítményt tesztet futtatja. Szüksége lesz a nyomkövetések segítségével megismerheti, hogyan működik a webes alkalmazás terhelés alatt. Szabályozhatja, mikor rögzítve lesznek a nyomkövetések kellene azért rendkívül fontos, mert a terhelési teszt fog futni, de a véletlenszerű mintavételi időköz előfordulhat, hogy hagyja ki tudja.
A következő lépések bemutatják, hogyan működik a ebben a forgatókönyvben:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Nem kötelező) 1. lépés: A webalkalmazás-forgalom létrehozása egy webes teljesítménytesztelési elindításával

Ha a webalkalmazás már van a bejövő forgalmat, vagy ha csak át szeretné manuálisan létrehozni a forgalmat, kihagyhatja ezt a szakaszt, és folytassa a 2. lépés.

Application Insights portálon lépjen **konfigurálása > Teljesítménytesztelés**. Kattintson az új gombra egy új teljesítményteszt elindításához.
![Hozzon létre új teljesítményteszt][create-performance-test]

Az a **új teljesítményteszt** ablaktáblán, a teszt cél URL-cím konfigurálása. Fogadja el az összes alapértelmezett beállítást, és indítsa el a terhelési teszt.

![Terheléses teszt beállítása][configure-performance-test]

Látni fogja az új vizsgálat van először a várólistára, a folyamatban lévő állapotát követi.

![terheléses teszt elküldve, és várólistára helyezve][load-test-queued]

![terheléses teszt folyamatban fut.][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>2. lépés: Indítsa el a profiler igény szerinti

Ha a terhelési teszt már fut, a profiler rögzíthesse a hívásláncokat a webalkalmazásban fogadja betöltése közben is kezdődik.
Keresse meg a Profiler konfigurálása panelen:

![a profiler ablaktábla-bejegyzés konfigurálása][configure-profiler-entry]

Az a **Profiler konfigurálása panelen**, van egy **profil most** gomb aktiválásához profiler társított web Apps-alkalmazás összes példánya. Emellett vannak kapott látható-e a profiler futott az elmúlt.

![Profiler igény szerinti][profiler-on-demand]

Értesítés és módosítsa a futtatási állapot a profiler állapot jelenik meg.

### <a name="step-3-view-traces"></a>3. lépés: Nézet nyomkövetések

A profiler a lejáratot követően újrainduljon, ha kövesse a teljesítmény-nyomkövetés panel és a nézet értesítésben.

### <a name="troubleshooting-on-demand-profiler"></a>Igény szerinti profiler hibaelhárítása

Néha láthatja el egy igény szerinti munkamenet után Profiler időtúllépés hibaüzenet jelenik meg:

![Profiler időtúllépési hiba][profiler-timeout]

Miért érdemes ezt a hibaüzenetet két oka lehet:

1. A profiler igény szerinti munkamenet sikeres volt, de az Application Insights az összegyűjtött adatok feldolgozásához hosszabb időt vett igénybe. Nem fejeződött be adatok feldolgozása folyamatban 15 percet vesz igénybe, ha a portálon egy időtúllépési üzenet jelenik meg. Bár idővel Profiler nyomkövetések jelennek meg. Ha ez történik, csak mellőzi a hibaüzenet most. Aktívan dolgozunk a javítás

1. A webalkalmazás, amely nem rendelkezik az igény szerinti szolgáltatás Profiler-ügynök egy korábbi verziója van. Ha korábban engedélyezte az Application Insights-profilban, valószínűleg a Profiler-ügynök a igény szerinti funkció használatához frissítenie kell.
  
Kövesse az alábbi lépéseket, és telepítse a legújabb Profiler:

1. App Services alkalmazás beállításaiban, és ellenőrizze, hogy ha a következő beállítást:
    * **Állítani az APPINSIGHTS_INSTRUMENTATIONKEY**: cserélje le a megfelelő rendszerállapotkulcsot az Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0 Ha ezen beállítások bármelyike nincs beállítva, nyissa meg az Application Insights engedélyezését ablaktábla a legújabb webhelybővítmény telepítése.

1. Nyissa meg az Application Insights paneljén App Services portálon.

    ![App Services portálon az Application Insights engedélyezése][enable-app-insights]

1. Ha egy "Frissítés" gombot a következő oldal jelenik meg, kattintson az Application Insights-webhelybővítményt is telepíti a legújabb Profiler-ügynök frissítését.
![Webhelybővítmény frissítése][update-site-extension]

1. Kattintson a **módosítása** való gondoskodik róla, hogy a Profiler bekapcsolt, és válassza ki **OK** menti a módosításokat.

    ![Módosítsa és mentse az app insights][change-and-save-appinsights]

1. Lépjen vissza a **Alkalmazásbeállítások** lapon ellenőrizze a következő alkalmazás beállítások elemeinek az App Service vannak beállítva:
    * **Állítani az APPINSIGHTS_INSTRUMENTATIONKEY**: cserélje le a megfelelő rendszerállapotkulcsot az application insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![a profiler App beállításai][app-settings-for-profiler]

1. Szükség esetén ellenőrizze a bővítmény verziója, és gondoskodik róla, hogy nincs elérhető frissítés.

    ![Ellenőrizze a bővítmény frissítése][check-for-extension-update]

## <a name="next-steps"></a>További lépések

* [Az Application Insights a Visual Studio használata](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png

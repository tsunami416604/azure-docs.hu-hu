---
title: "Élő webalkalmazások Azure Application Insights Profilkészítő a profil |} Microsoft Docs"
description: "A web server kód gyors elérési út egy kis erőforrásigénnyel Profilkészítő azonosításához."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: e66dc2af18785c6c8e83815129c8bca5b877d25b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profil élő Azure-webalkalmazásokban az Application insights szolgáltatással

*Ez a szolgáltatás az Application Insights szolgáltatás általánosan elérhető az Azure App Service szolgáltatásban, és jelenleg előzetes verzióban érhető az Azure számítási erőforrásokat.*

Annak megállapítása, mennyi idő telhet el az egyik módszer az élő webes alkalmazás használatával [Application Insights Profilkészítő](app-insights-overview.md). Az Application Insights adatgyűjtési eszköz jeleníti meg, hogy az alkalmazás által kiszolgált volt élő kérelem részletes profilokat, és kiemeli a *gyakran használt adatok elérési útja* , amely a legtöbb időt használja. A Profilkészítő automatikusan kiválasztja a példák, amelyek különböző válaszidejét, és különböző módszerek használatával terhek minimalizálása érdekében.

A Profilkészítő jelenleg ASP.NET web Apps rendszeren futó Azure App Service-ben legalább alapszintű rétegben működik.

## <a id="installation"></a>A Profilkészítő engedélyezése

[Telepítse az Application Insights](app-insights-asp-net.md) a kódban. Ha már telepítve van, győződjön meg arról, hogy a legújabb verzióra. A legújabb verzióra, a Megoldáskezelőben kereséséhez kattintson jobb gombbal a projektre, majd válassza ki **kezelése NuGet-csomagok** > **frissítések** > **frissíti az összes csomagok**. Ezután telepítse újra az alkalmazást.

*ASP.NET Core segítségével? Első [további információt](#aspnetcore).*

A [az Azure-portálon](https://portal.azure.com), nyissa meg a webalkalmazás az Application Insights-erőforrást. Válassza ki **teljesítmény** > **engedélyezze az Application Insights Profilkészítő**.

![Válassza ki az engedélyezés Profilkészítő szalagcím][enable-profiler-banner]

Választhatja azt is megteheti, **konfigurálása** állapotának megtekintése és engedélyezze vagy tiltsa le a Profilkészítő.

![A teljesítmény a beállítás választása][performance-blade]

Az Application insights szolgáltatással konfigurált webalkalmazások találhatók **konfigurálása**. Kövesse az utasításokat a Profilkészítő ügynök telepítése, ha szükséges. Ha nincsenek webes alkalmazások konfigurálása az Application insights szolgáltatással **csatolt alkalmazások felvétele**.

A csatolt web apps a Profilkészítő lévő vezérlése érdekében a **konfigurálása** ablaktáblán válassza előbb **engedélyezni Profilkészítő** vagy **tiltsa le a Profilkészítő**.

![Ablaktábla beállítások konfigurálása][linked app services]

Ellentétben a web apps keresztül App Service csomagokban üzemeltetett alkalmazásokhoz, amelyek az Azure számítási erőforrásokat (például Azure virtuális gép, virtuálisgép-méretezési csoportok, Azure Service Fabric vagy Azure Cloud Services) nem közvetlenül felügyeli Azure. Ebben az esetben nincs egyetlen webalkalmazás csatolása. Helyett összekapcsolása egy alkalmazást, válassza ki a **engedélyezni Profilkészítő** gombra.

## <a name="disable-the-profiler"></a>A Profilkészítő letiltása
A leállítani, vagy indítsa újra a Profilkészítő egyedi App Service-példány a **webes feladatok**, keresse fel az App Service-erőforrást. A Profilkészítő törléséhez lépjen a **bővítmények**.

![Egy webes feladatok Profilkészítő letiltása][disable-profiler-webjob]

Azt javasoljuk, hogy rendelkezik-e a profilkészítőt a lehető legkorábban bármely teljesítményproblémák felderítéséhez a webalkalmazásokban engedélyezett.

WebDeploy használatával telepíti a módosításokat a webalkalmazáshoz, ha győződjön meg arról, hogy az App_Data mappájában kizárása törlése folyamatban a telepítés során. Ellenkező esetben a Profilkészítő szerepkörbővítmény-fájlokat az Azure a webes alkalmazást telepít központilag a következő alkalommal törlődnek.

### <a name="using-profiler-with-azure-vms-and-azure-compute-resources-preview"></a>Profilkészítő használata Azure virtuális gépek és az Azure számítási erőforrásokat (előzetes verzió)

Amikor Ön [Application Insights engedélyezése az Azure App Service-futásidőben](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights), automatikusan érhető el Application Insights Profiler. Ha már engedélyezte az Application Insights az erőforrás, szükség lehet konfigurálása varázsló segítségével a legújabb verzióra frissíteni.

Információ jelenik meg egy [előzetes verzióját, az Azure számítási erőforrásokat Profilkészítő](https://go.microsoft.com/fwlink/?linkid=848155).


## <a name="limitations"></a>Korlátozások

Az alapértelmezett az adatmegőrzés öt nap. A maximális száma naponta okozhatnak adata 10 GB-os.

Nincsenek nincsenek terhelések a Profilkészítő szolgáltatásunkat használja. A Profilkészítő szolgáltatás használatához a webalkalmazás kell lennie az App Service alapszintű rétegben legalább tárolva.

## <a name="overhead-and-sampling-algorithm"></a>Munkaterhet és a mintavételi algoritmus

A Profilkészítő két perc minden órában véletlenszerűen futtató minden egyes virtuális gépen, amelyen az alkalmazás nyomok rögzítését engedélyezni Profilkészítő. A Profilkészítő fut, amikor hozzáadja 5 – 15 %-CPU-terhelést a kiszolgálóra.
A további kiszolgálók, amelyek elérhetők a kisebb mértékű befolyásolása mellett a Profilkészítő rendelkezik-e a az alkalmazás általános teljesítménye az alkalmazás üzemeltetésére. Ennek az az oka a mintavételi algoritmus a Profilkészítő bármikor csak 5 %-a kiszolgálókon futó eredményez. Több kiszolgáló használatával ellensúlyozza a kiszolgáló terhelését okozta. a Profilkészítő futó webes kérelem kiszolgálására érhetők el.


## <a name="view-profiler-data"></a>Profilkészítő adatok megtekintése

Lépjen a **teljesítmény** ablaktáblán, majd görgessen le a műveletek listája.

![Application Insights teljesítmény ablaktábla példák oszlop][performance-blade-examples]

A műveletek táblához ezeket az oszlopokat:

* **COUNT**: ezek időtartománya kérelmek száma a **száma** ablaktáblán.
* **Medián**: A tipikus időben válaszol a kérelemre a app vesz igénybe. Minden válasz felének volt gyorsabb, mint ez az érték.
* **95. percentilis**: 95 %-a válaszok gyorsabb, mint ez az érték volt. Ha ez az érték jelentősen eltér a középérték, előfordulhat, hogy az alkalmazás egy időszakos probléma lehet. (Vagy egy Tervező szolgáltatás, például a gyorsítótárazás előfordulhat, hogy viszonylag.)
* **Szolgáltatásprofil-ELEMZŐI adat**: ikon jelzi, hogy a Profilkészítő rendelkezik captured híváslánc megjelenik az ezt a műveletet.

Válassza ki **nézet** a nyomkövetési explorer megnyitásához. A explorer több mintáit tartalmazza, hogy a Profilkészítő rendelkezik captured, minősített válaszidő alapján.

Használatakor a **előzetes verzió teljesítményt** ablaktáblán, keresse fel a **érvénybe műveletek** megtekintése a szolgáltatásprofil-elemzői adat lap részében. Válassza ki a **szolgáltatásprofil-elemzői adat** gombra.

![Application Insights teljesítmény ablaktábla preview szolgáltatásprofil-elemzői adat][performance-blade-v2-examples]

Válassza ki a megjelenítendő kód szintű információkat töltött időt a kérelem végrehajtása a minta.

![Application Insights nyomkövetési explorer][trace-explorer]

A nyomkövetés-kezelő az alábbi információkat jeleníti meg:

* **Működés közbeni elérési megjelenítése**: megnyílik a legnagyobbnak levél csomópont, vagy valami legalább bezárása. A legtöbb esetben ez a csomópont a teljesítménybeli szűk keresztmetszetek szomszédos.
* **Címke**: a függvény vagy az esemény nevét. A listáját jeleníti meg a kódot és az események (például az SQL és a HTTP-esemény), kombinációját. Az első esemény a kérelem összesített időtartamot jelöli.
* **Eltelt**: a művelet a kezdő- és a művelet közötti időközt.
* **Amikor**: Ha a függvény vagy esemény futott kapcsolatos egyéb funkciók.

## <a name="how-to-read-performance-data"></a>Teljesítményadatok olvasása

A Microsoft szolgáltatásprofil-elemzőben mintavételi és instrumentation kombinációját használja az alkalmazás teljesítményének elemzését. Ha részletes gyűjtemény van folyamatban, a szolgáltatásprofil-elemzőben minták a gép CPU minden ezredmásodperces az egyes utasítás mutató. A mintákat a szál éppen végrehajtás alatt, a teljes verem rögzíti. Adott szálon lett tevékenységeit, mind absztrakciós alacsony szintű magas szintű részletes és hasznos információkat biztosít. A szolgáltatásprofil-elemzőben eseményeit is más tevékenység korrelációs és okozatiságának, beleértve az eseményeket, a feladat párhuzamos könyvtár (TPL) események és a szál készlet események környezetben nyomon követéséhez.

A hívási verem, az Ütemterv nézetben látható a mintavételi és instrumentation eredménye. A mintákat a teljes hívási verem, a szál írja le, mert az a Microsoft .NET-keretrendszer és egyéb keretrendszerekre, amelyet a kód magában foglalja.

### <a id="jitnewobj"></a>Objektum foglalási (clr! Igény szerinti\_új vagy CLR-beli! Igény szerinti\_Newarr1)
**CLR! Igény szerinti\_új** és **clr! Igény szerinti\_Newarr1** segítő függvények a .NET-keretrendszer memóriát lefoglalni a kezelt halommemóriában. **CLR! Igény szerinti\_új** nyílik meg, ha az objektum le van foglalva. **CLR! Igény szerinti\_Newarr1** nyílik meg, ha egy objektum tömb le van foglalva. E két funkciók általában nagyon gyorsan, és viszonylag kis mennyiségű időt igénybe vehet. Ha látja **clr! Igény szerinti\_új** vagy **clr! Igény szerinti\_Newarr1** jelentős mennyiségű időt igénybe az ütemterven, hogy az arra utal, hogy lehet-e fel jelentős mennyiségű memóriát és sok objektum lefoglalása a kódot.

### <a id="theprestub"></a>Kód (clr betöltése! ThePreStub)
**CLR! ThePreStub** a .NET-keretrendszer, amely a kód végrehajtásához először előkészíti a segítő funkciója. Ez általában tartalmaz, de nincs korlátozva, közvetlenül az igény (szerinti JIT) fordítás. Az egyes C# módszerek **clr! ThePreStub** akkor szabadna meghívni, legfeljebb egyszer a folyamat élettartama során.

Ha **clr! ThePreStub** eltart egy kérelem idő, ez azt jelzi, hogy a kérelmet, amely végrehajtja a Ez a módszer az elsőt. Ez a módszer betöltése a .NET-keretrendszer futtatókörnyezete ideje jelentős. Érdemes lehet egy melegítési folyamattal, amely végrehajtja a szerelvényekre része, amely a kódot ahhoz, hogy a felhasználók hozzáférése, illetve natív vonalaskép-készítő (ngen.exe) futtatja.

### <a id="lockcontention"></a>Zárolási versenyt (clr! JITutil\_MonContention vagy CLR-beli! JITutil\_MonEnterWorker)
**CLR! JITutil\_MonContention** vagy **clr! JITutil\_MonEnterWorker** azt jelzi, hogy az aktuális szál várakozik a zárolás feloldására. Ez általában megjelenik a végrehajtásakor a C# **ZÁROLÁSI** utasítást, ha a meghívás a **Monitor.Enter** metódus, vagy a metódus hívásakor a **MethodImplOptions.Synchronized**attribútum. Zárolási versenyt rendszerint azért fordul elő, ha szál A zárolás, és a B szál megpróbál a azonos zárolás megszerzésére előtt A szál szabadítja.

### <a id="ngencold"></a>([COLD]) kód betöltésekor
Ha a metódus nevét tartalmazza **[COLD]**, például a **mscorlib.ni! [ COLD]System.Reflection.CustomAttribute.IsDefined**, a .NET-keretrendszer futtatókörnyezete végrehajtja az kód által nem optimalizált először <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">profil interaktív optimalizálási</a>. Az egyes módszerek azt kell jelenik meg, legfeljebb egyszer a folyamat élettartama során.

Ha kérelem ideje jelentős mennyiségű kód betöltése időbe telik, ez azt jelzi, hogy a kérelem az elsőt a nem optimalizált része a metódus végrehajtásához. Érdemes lehet a melegítési folyamat, amely végrehajtja a része, amely a kódot, mielőtt a felhasználók hozzáférési jogosultsága.

### <a id="httpclientsend"></a>HTTP-kérelem küldése
Módszerek, például **HttpClient.Send** jelzi, hogy a kód egy HTTP-kérelem végrehajtása vár.

### <a id="sqlcommand"></a>Adatbázis-művelet
Módszerek, például **SqlCommand.Execute** jelzi, hogy a kód arra vár, hogy egy adatbázis-művelet befejezéséhez.

### <a id="await"></a>Várakozás (AWAIT\_idő)
**AWAIT\_idő** azt jelzi, hogy a kód arra vár, hogy egy másik feladat befejeződik. Ez általában akkor fordul elő a C# **AWAIT** utasítást. Amikor a kód does egy C# **AWAIT**, a szál unwinds, és visszaadja a szálkészlet, és jelenleg nem vár blokkolt szál a **AWAIT** befejezéséhez. Azonban logikailag, a szál, amelyek adott a **AWAIT** blokkolva van,"", és arra vár, hogy a művelet befejezéséhez. A **AWAIT\_idő** utasítást a tevékenység befejezése Várakozás letiltott idejét jelzi.

### <a id="block"></a>Letiltott idő
**BLOCKED_TIME** azt jelzi, hogy a kód arra vár, hogy egy másik erőforrás elérhető legyen. Például akkor lehet, hogy várni szinkronizálási objektumra, a szál elérhető legyen, vagy a kérelem befejezésének.

### <a id="cpu"></a>CPU-idő
A Processzor nem az utasításokat.

### <a id="disk"></a>Lemezmeghajtó kihasználtsága
Az alkalmazás lemez műveleteket hajt végre.

### <a id="network"></a>Hálózati idő
Az alkalmazás hálózati műveleteket hajt végre.

### <a id="when"></a>Ha oszlop
A **amikor** oszlop, a határokat is beleértve mintákat egy csomópont gyűjtése hogyan eltérők lehetnek időbeli képi megjelenítés. A kérelem teljes skáláját 32 idő gyűjtők van osztva. Az adott csomópont a határokat is beleértve minták összegzi a rendszer az ezen 32 gyűjtők. Minden egyes gyűjtő sáv jelzi. A sáv magasságának egy méretezett értékét jelöli. Csomópontok megjelölve **CPU_TIME** vagy **BLOCKED_TIME**, ahol fel egy erőforrást (Processzor, lemez- vagy szál) egy nyilvánvaló kapcsolata van, a sáv jelöli fel ezeket az erőforrásokat egyikét az időtartam, vagy a gyűjtő idején. A metrikák jelenhet meg több erőforrást kötött által a 100 %-nál nagyobb érték. Például ha két processzor átlagosan egy időszakban használja, kapott 200 %.


## <a id="troubleshooting"></a>Hibaelhárítás

### <a name="too-many-active-profiling-sessions"></a>Túl sok az aktív profilkészítési munkamenet

Jelenleg legfeljebb négy Azure web apps és az azonos service-csomag futó üzembe helyezési a Profilkészítő engedélyezheti. A Profilkészítő webes projekt túl sok az aktív profilkészítési munkamenet jelez, ha néhány webalkalmazások áthelyezése egy másik service-csomagot.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hogyan állapítható meg, hogy fut-e az Application Insights Profiler?

A Profilkészítő a webalkalmazásban folyamatosan webes feladatként fut. Megnyithatja a webes alkalmazás-erőforrást az [az Azure-portálon](https://portal.azure.com). Az a **WebJobs** ablaktáblában tekintse meg a **ApplicationInsightsProfiler**. Ha a számítógépen nem fut, nyissa meg a **naplók** további információkat.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Miért nem található a verem példák, annak ellenére, hogy fut-e a Profilkészítő?

Az alábbiakban néhány dolog, ellenőrizheti:

* Győződjön meg arról, hogy a web app service-csomag az alapszintű csomag vagy újabb.
* Győződjön meg arról, hogy a webalkalmazás Application Insights SDK 2.2 Beta vagy újabb nincs engedélyezve.
* Győződjön meg arról, hogy rendelkezik-e a webalkalmazás a **APPINSIGHTS_INSTRUMENTATIONKEY** az Application Insights SDK által használt azonos instrumentation kulccsal rendelkező beállítást.
* Győződjön meg arról, hogy a webalkalmazás fut-e a .NET-keretrendszer 4.6.
* Ha a webalkalmazás az ASP.NET Core kérelmet, [a szükséges függőségek](#aspnetcore).

A Profilkészítő az elindítása után van egy rövid melegítési időszak, amely alatt a Profilkészítő aktívan nyomkövetését több teljesítményét. Ezt követően a Profilkészítő nyomkövetését teljesítmény óránként két percig.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Azure Service Profilkészítő lett használata Mi történt azt?  

Ha engedélyezi az Application Insights Profilkészítő, az Azure szolgáltatásprofil-elemzőben ügynök le van tiltva.

### <a id="double-counting"></a>A párhuzamos szálak számbavételi dupla

A teljes időmetrika a verem megjelenítőben egyes esetekben ez több, mint a kérelem ideje alatt.

Ez akkor fordulhat elő, amikor a kérelemhez társított két vagy több szál, és párhuzamosan működnek. Ebben az esetben az összes szál ideje több, mint az eltelt idő. Egy szál előfordulhat, hogy arra vár, a további végrehajtani. A megjelenítő megpróbálja észleli ezt, és kihagyja az érdektelen várakozási, de azt errs jelenít meg ahelyett, hogy mi lehet a kritikus információk kihagyásával túl sok oldalán.  

A nyomkövetések párhuzamos jelenik meg, amikor határozza meg, melyik szálak várnak, így ellenőrizheti, hogy a kritikus fontosságú a kérelem elérési útja. A legtöbb esetben a gyorsan várakozási állapotba kerül, a szál egyszerűen a más szálak vár. A más szálak összpontosít, és figyelmen kívül hagyja az idő a várakozó szál.

### <a id="issue-loading-trace-in-viewer"></a>Profilkészítési adatok

Az alábbiakban néhány dolog, ellenőrizheti:

* Ha a megtekinteni kívánt adatok néhány hétre régebbi, próbálkozzon a időszűrője korlátozása, és próbálkozzon újra.
* Ellenőrizze, hogy proxyk és a tűzfalon nem letiltott https://gateway.azureserviceprofiler.net elérésére.
* Ellenőrizze, hogy az Application Insights instrumentation kulcsot használ az alkalmazás ugyanaz, mint az Application Insights-erőforrást, amelyen engedélyezett profilkészítési. A kulcs általában applicationinsights.config, de is lehet, hogy található a web.config vagy az app.config fájlban.

### <a name="error-report-in-the-profiling-viewer"></a>Hibajelentés a profilkészítési megjelenítőben

Küldje el a portál egy támogatási jegy. Ne felejtse el feltüntetni a korrelációs azonosító, a hibaüzenet.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Központi telepítési hiba: könyvtár nem üres "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok

Ha a webalkalmazás az App Service-erőforrás való engedélyezve vannak újratelepíteni, megjelenhet egy üzenetet, amely a következőképpen néznek:

Könyvtár nem üres "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok

Ez a hiba akkor fordul elő, ha futtatja a Web Deploy a parancsfájlok vagy a Visual Studio Team Services telepítési folyamatot. A megoldás, hogy adja meg a következő további telepítési paramétereit a Web Deploy feladat:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ezek a paraméterek Application Insights Profilkészítő használja, és helyezze üzembe újra folyamat feloldása mappa törlése. Nincsenek hatással a jelenleg futó Profilkészítő példányok.


## <a name="manual-installation"></a>Manuális telepítés

A Profilkészítő konfigurálásakor frissítések válnak, a webes alkalmazás beállításai. Ha a környezet elő, manuálisan alkalmazhatja a frissítéseket. Ha például az alkalmazás fut, az App Service Environment-környezet a powerapps segítségével.

1. Nyissa meg a webes alkalmazás Vezérlőpulton **beállítások**.
2. Állítsa be **.Net keretrendszer** való **v4.6**.
3. Állítsa be **Always On** való **a**.
4. Adja hozzá a __APPINSIGHTS_INSTRUMENTATIONKEY__ app beállítást, és állítsa be az értéket az SDK által használt azonos instrumentation kulccsal.
5. Nyissa meg **speciális eszközök**.
6. Válassza ki **Ugrás** a Kudu webhely megnyitásához.
7. Válassza ki a Kudu webhelyen **bővítmények hely**.
8. Telepítés __az Application Insights__ az Azure Web Apps gyűjteményből.
9. A webalkalmazás újraindítása.

## <a id="aspnetcore"></a>Az ASP.NET Core támogatása

Az ASP.NET Core alkalmazás kell telepíteni a Microsoft.ApplicationInsights.AspNetCore NuGet csomag 2.1.0-beta6 vagy újabb való működéséhez. 2017. június 27. frissítésétől korábbi verziói nem támogatottak.

## <a name="next-steps"></a>Következő lépések

* [A Visual Studio Application Insights használata](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

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

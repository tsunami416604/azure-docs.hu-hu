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
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: c65ef9141898369b8fcadd4c52972b767aca7cfe
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profil élő Azure-webalkalmazásokban az Application insights szolgáltatással

*Ez a szolgáltatás az Azure Application Insights szolgáltatás általánosan elérhető az Azure App Service Web Apps szolgáltatása, és jelenleg előzetes verzióban érhető az Azure számítási erőforrásokat.*

Ez a cikk ismerteti, amelyek használatakor az egyik módszer az élő webes alkalmazás töltött idő mennyiségét [Application Insights](app-insights-overview.md). Az Application Insights Profilkészítő eszközt jelenít meg részletes profilok volt az alkalmazás által szolgáltatott élő kérelmek. Profilkészítő kiemeli a *gyakran használt adatok elérési útja* , amely a legtöbb időt használja. Különböző válaszidők rendelkező kérelmek esetében a mintavétel alapján vannak csatolást. Módszerek segítségével minimalizálhatja a terhelést a alkalmazással van társítva.

Profilkészítő ASP.NET és az ASP.NET Core web Apps a webalkalmazások futó jelenleg működik. A Basic szolgáltatásréteget, vagy a Profilkészítő használata szükséges.

## <a id="installation"></a> A Web Apps webalkalmazás Profilkészítő engedélyezése
Ha már van egy közzétett alkalmazás, de nem volna az Application Insights használatához a forráskód semmit, tegye a következőket:
1. Lépjen a **alkalmazásszolgáltatások** ablaktáblán az Azure portálon.
2. A **figyelés**, jelölje be **Application Insights**, és vagy kövesse az utasításokat a ablaktáblán hozzon létre egy új erőforrást, vagy válasszon ki egy meglévő Application Insights-erőforrás a webes figyelése alkalmazás.

   ![Alkalmazásszolgáltatások Portal App Insights engedélyezése][appinsights-in-appservices]

3. Ha Ön férhessen hozzá a projekt forráskód [telepítse az Application Insights](app-insights-asp-net.md).  
   Ha már telepítve van, győződjön meg arról, hogy a legújabb verzióra. A legújabb verzióra, a Megoldáskezelőben kereséséhez kattintson jobb gombbal a projektre, majd válassza ki **kezelése NuGet-csomagok** > **frissítések** > **frissíti az összes csomagok**. Ezután helyezze üzembe az alkalmazást.

Az ASP.NET Core alkalmazásoknak a telepítését a Microsoft.ApplicationInsights.AspNetCore NuGet csomag 2.1.0-beta6 vagy újabb Profilkészítő együttműködni. 2017. június 27. frissítésétől korábbi verziói nem támogatottak.

1. A [az Azure-portálon](https://portal.azure.com), nyissa meg a webalkalmazás az Application Insights-erőforrást. 
2. Válassza ki **teljesítmény** > **engedélyezze az Application Insights Profilkészítő**.

   ![Válassza ki az engedélyezés Profilkészítő szalagcím][enable-profiler-banner]

3. Azt is megteheti, kiválaszthatja a **Profilkészítő** konfigurációs állapotának megtekintése és engedélyezze vagy tiltsa le a Profilkészítő.

   ![Válassza ki a Profilkészítő konfiguráció][performance-blade]

   Az Application insights szolgáltatással konfigurált webalkalmazások jelennek meg a **Profilkészítő** konfigurációs ablaktáblán. Ha követte a fenti lépéseket, a Profilkészítő ügynököt kell telepíteni. 

4. Az a **Profilkészítő** konfigurációs ablaktáblán válassza előbb **engedélyezni Profilkészítő**.

5. Ha szükséges, kövesse a Profilkészítő ügynök telepítéséhez. Ha nincsenek webes alkalmazások konfigurálása az Application insights szolgáltatással **csatolt alkalmazások felvétele**.

   ![Ablaktábla beállítások konfigurálása][linked app services]

Webalkalmazások tervek keresztül tárolt web apps – ellentétben, amelyek az Azure számítási erőforrásokat (például Azure virtuális gép, virtuálisgép-méretezési csoportok, Azure Service Fabric vagy Azure Cloud Services) nem közvetlenül kezeli az alkalmazásokat Azure. Ebben az esetben nincs egyetlen webalkalmazás csatolása. Helyett összekapcsolása egy alkalmazást, válassza ki a **engedélyezni Profilkészítő** gombra.

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Profilkészítő engedélyezése az Azure számítási erőforrások (előzetes verzió)

További információ: a [előzetes verzióját Profilkészítő Azure számítási erőforrások](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Profilkészítő adatok megtekintése

Győződjön meg arról, hogy az alkalmazás fogad-forgalmat. Ha a kísérlet, a web app használatával hozhat létre kérelmek [Application Insights Teljesítménytesztelés](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test). Ha engedélyezte a Profilkészítő újonnan, egy rövid terheléstesztet mintegy 15 percre leáll, amely kell létrehozni a szolgáltatásprofil-elemzői adat is futtathatja. Ha egy ideje már engedélyezni Profilkészítő korábban, tartsa szem előtt tartva futtató Profilkészítő véletlenszerűen kétszer minden órában, és a minden egyes alkalommal két perces időtartamot futtatja. Azt javasoljuk, hogy először a teszt a terhelés egy órán keresztül minta szolgáltatásprofil-elemzői adat kapja.

Miután az alkalmazás egyes forgalom érkezik, lépjen a **teljesítmény** ablaktáblán válassza előbb **érvénybe műveletek** megtekintése a szolgáltatásprofil-elemzői adat, és válassza a **szolgáltatásprofil-elemzői adat** gombra.

![Application Insights teljesítmény ablaktábla preview Profilkészítő követi][performance-blade-v2-examples]

Válassza ki a megjelenítendő kód szintű információkat töltött időt a kérelem végrehajtása a minta.

![Application Insights nyomkövetési explorer][trace-explorer]

A nyomkövetés-kezelő az alábbi információkat jeleníti meg:

* **Működés közbeni elérési megjelenítése**: megnyílik a legnagyobbnak levél csomópont, vagy valami legalább bezárása. A legtöbb esetben ez a csomópont a teljesítménybeli szűk keresztmetszetek szomszédos.
* **Címke**: a függvény vagy az esemény nevét. A fa kódot és az események (például az SQL és a HTTP-esemény), jeleníti meg. Az első esemény a kérelem összesített időtartamot jelöli.
* **Eltelt**: a művelet a kezdő- és a művelet közötti időközt.
* **Amikor**: az idő, amikor a függvény vagy esemény futott kapcsolatos egyéb funkciók.

## <a name="how-to-read-performance-data"></a>Teljesítményadatok olvasása

A Microsoft szolgáltatásprofil-elemzőben mintavételi és instrumentation kombinációját használja az alkalmazás teljesítményének elemzését. Ha részletes gyűjtemény van folyamatban, a szolgáltatásprofil-elemzőben minden gép CPU utasítás mutató minden ezredmásodperces – minták. A mintákat a szál éppen végrehajtás alatt áll, a teljes verem rögzíti. Az adott szálon lett tevékenységeit, mind absztrakciós alacsony szintű magas szintű részletes tájékoztatást nyújt. A szolgáltatásprofil-elemzőben eseményeit is más tevékenység korrelációs és okozatiságának, beleértve az eseményeket, a feladat párhuzamos könyvtár (TPL) események és a szál készlet események környezetben nyomon követéséhez.

A hívási verem, amely az Ütemterv nézetben jelenik meg a mintavételi és instrumentation eredménye. A mintákat a teljes hívási verem, a szál írja le, mert tartalmazza a Microsoft .NET-keretrendszer, illetve egyéb keretrendszerekre, amelyet a kódot.

### <a id="jitnewobj"></a>Objektum foglalási (clr! Igény szerinti\_új vagy CLR-beli! Igény szerinti\_Newarr1)
**CLR! Igény szerinti\_új** és **clr! Igény szerinti\_Newarr1** segítő függvények a .NET-keretrendszer memóriát lefoglalni a kezelt halommemóriában. **CLR! Igény szerinti\_új** nyílik meg, ha az objektum le van foglalva. **CLR! Igény szerinti\_Newarr1** nyílik meg, ha egy objektum tömb le van foglalva. E két funkciók általában gyors, és viszonylag kis mennyiségű időt igénybe vehet. Ha látja **clr! Igény szerinti\_új** vagy **clr! Igény szerinti\_Newarr1** jelentős mennyiségű időt igénybe az ütemterven, azt jelzi, hogy lehet-e fel jelentős mennyiségű memóriát és sok objektum lefoglalása a kódot.

### <a id="theprestub"></a>Kód (clr betöltése! ThePreStub)
**CLR! ThePreStub** a .NET-keretrendszer, amely a kód végrehajtásához először előkészíti a segítő funkciója. Ez általában tartalmaz, de nincs korlátozva, közvetlenül az igény (szerinti JIT) fordítás. Az egyes C# módszerek **clr! ThePreStub** akkor szabadna meghívni, legfeljebb egyszer a folyamat élettartama során.

Ha **clr! ThePreStub** eltart egy kérelem idő, ez azt jelzi, hogy a kérelmet, amely végrehajtja a Ez a módszer az elsőt. A .NET-keretrendszer futtatókörnyezete betölteni az első módszer a ideje jelentős. Érdemes lehet egy melegítési folyamattal, amely végrehajtja a szerelvényekre része, amely a kódot ahhoz, hogy a felhasználók hozzáférése, illetve natív vonalaskép-készítő (ngen.exe) futtatja.

### <a id="lockcontention"></a>Zárolási versenyt (clr! JITutil\_MonContention vagy CLR-beli! JITutil\_MonEnterWorker)
**CLR! JITutil\_MonContention** vagy **clr! JITutil\_MonEnterWorker** azt jelzi, hogy az aktuális szál várakozik a zárolás feloldására. Ezt a szöveget, általában akkor jelenik meg, ha a végrehajtás egy C# **ZÁROLÁSI** utasítás, ha a meghívás a **Monitor.Enter** módszert, vagy a metódus hívásakor a **MethodImplOptions.Synchronized** attribútum. Zárolási versenyt általában akkor fordul elő, ha a szál _A_ szerez be a zárolást és a szál _B_ azonos zár szál előtt megpróbálja _A_ szabadítja.

### <a id="ngencold"></a>([COLD]) kód betöltésekor
Ha a metódus nevét tartalmazza **[COLD]**, például a **mscorlib.ni! [ COLD]System.Reflection.CustomAttribute.IsDefined**, a .NET-keretrendszer futtatókörnyezete végrehajtja az kód által nem optimalizált először <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">profil interaktív optimalizálási</a>. Az egyes módszerek az üzenet legfeljebb egyszer a folyamat élettartama során.

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
A **amikor** oszlop, a határokat is beleértve mintákat egy csomópont gyűjtése hogyan eltérők lehetnek időbeli képi megjelenítés. A kérelem teljes skáláját 32 idő gyűjtők van osztva. Az adott csomópont a határokat is beleértve minták összegzi a rendszer az ezen 32 gyűjtők. Minden egyes gyűjtő sáv jelzi. A sáv magasságának egy méretezett értékét jelöli. A kijelölt csomópontok **CPU_TIME** vagy **BLOCKED_TIME**, vagy amennyiben fel egy erőforrás (például Processzor, lemez vagy szál) egy nyilvánvaló kapcsolattal, a sáv jelenti. a felhasználás az egyik az erőforrásokat, hogy a gyűjtő időtartama alatt. A fenti metrikák több erőforrást kötött érték nagyobb, mint 100 százalék kérhet. Például ha használja, átlagosan egy időszakban, két processzor kap 200 százalék.

## <a name="limitations"></a>Korlátozások

Az alapértelmezett megőrzési időtartama öt nap. Az adatok maximális keresztül a szervezetbe naponta 10 GB-os.

Nincsenek nincsenek terhelések a Profilkészítő szolgáltatásunkat használja. Ahhoz, hogy a Profilkészítő szolgáltatást használja, a webalkalmazás kell szolgáltatásban üzemeltetett legalább az alapszintű rétegben azokból a Webalkalmazásokból.

## <a name="overhead-and-sampling-algorithm"></a>Munkaterhet és a mintavételi algoritmus

Profilkészítő két perc minden órában véletlenszerűen futtató minden egyes virtuális gépen, amelyen az alkalmazás nyomok rögzítését engedélyezni Profilkészítő. Profilkészítő futtatásakor hozzáadása 5 százalékból 15 százaléka Processzor terhelésének a kiszolgálóra.

A további kiszolgálók, amelyek elérhetők a kisebb mértékű befolyásolása Profilkészítő rendelkezik-e a az alkalmazás általános teljesítménye az alkalmazás üzemeltetésére. Ennek az az oka a mintavételi algoritmus bármikor kiszolgálók csak 5 százalékát futó Profilkészítő eredményez. Több kiszolgáló használatával ellensúlyozza a kiszolgáló terhelését Profilkészítő futtatásával okozott webes kérelem kiszolgálására érhetők el.

## <a name="disable-profiler"></a>Tiltsa le a Profilkészítő
Állítsa le, vagy indítsa újra a Profilkészítő egyedi web apps példánya a **webes feladatok**, keresse fel a Web Apps erőforrás. Profilkészítő törléséhez lépjen a **bővítmények**.

![A webes projekt Profilkészítő letiltása][disable-profiler-webjob]

Azt javasoljuk, hogy rendelkezik-e engedélyezve a webalkalmazások a lehető leghamarabb felderítéséhez a teljesítménnyel kapcsolatos problémák Profiler.

WebDeploy használatával telepíti a módosításokat a webalkalmazáshoz, ha győződjön meg arról, hogy az App_Data mappájában kizárása törlése folyamatban a telepítés során. Ellenkező esetben a Profilkészítő szerepkörbővítmény-fájlokat az Azure a webes alkalmazást telepít központilag a következő alkalommal törlődnek.


## <a id="troubleshooting"></a>Hibaelhárítás

### <a name="too-many-active-profiling-sessions"></a>Túl sok az aktív profilkészítési munkamenet

Jelenleg Profilkészítő engedélyezhető legfeljebb négy Azure web apps és az azonos service-csomag futó üzembe helyezési pontok. A Profilkészítő webes projekt túl sok az aktív profilkészítési munkamenet jelez, ha néhány webalkalmazások áthelyezése egy másik service-csomagot.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hogyan állapítható meg, hogy fut-e az Application Insights Profiler?

Profilkészítő a webalkalmazásban folyamatosan webes feladatként fut. A webes alkalmazás-erőforrást is megnyithatja a [Azure-portálon](https://portal.azure.com). Az a **WebJobs** ablaktáblában tekintse meg a **ApplicationInsightsProfiler**. Ha a számítógépen nem fut, nyissa meg a **naplók** további információkat.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Miért nem található a verem példák, annak ellenére, hogy a Profilkészítő fut?

Az alábbiakban néhány dolog, ellenőrizheti:

* Győződjön meg arról, hogy a web app service-csomag az alapszintű csomag vagy magasabb.
* Győződjön meg arról, hogy a webalkalmazás Application Insights SDK 2.2 Beta vagy újabb nincs engedélyezve.
* Győződjön meg arról, hogy rendelkezik-e a webalkalmazás a **APPINSIGHTS_INSTRUMENTATIONKEY** az Application Insights SDK által használt azonos instrumentation kulccsal rendelkező beállítást.
* Győződjön meg arról, hogy a webalkalmazás fut-e a .NET-keretrendszer 4.6.
* Ha a webalkalmazás az ASP.NET Core kérelmet, [a szükséges függőségek](#aspnetcore).

Profilkészítő az elindítása után van egy rövid melegítési időszak során, ami Profilkészítő aktívan nyomkövetését több teljesítmény. Ezt követően Profilkészítő nyomkövetését teljesítmény óránként két percig.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Azure Service Profilkészítő lett használata Mi történt azt?

Ha engedélyezi az Application Insights Profilkészítő, az Azure szolgáltatás Profilkészítő ügynök le van tiltva.

### <a id="double-counting"></a>A párhuzamos szálak számbavételi dupla

A teljes időmetrika a verem megjelenítőben egyes esetekben ez több, mint a kérelem ideje alatt.

Ez a helyzet akkor fordulhat elő, ha két vagy több szál társítva van egy kérelmet, és párhuzamosan működnek. Ebben az esetben az összes szál ideje több, mint az eltelt idő. Egy szál előfordulhat, hogy arra vár, a további végrehajtani. A megjelenítő megpróbálja észleli ezt, és kihagyja az érdektelen várja meg, de azt errs oldalán túl sok információk megjelenítése helyett hagyja el, mi lehet a fontos adataikat.

A nyomkövetések párhuzamos jelenik meg, amikor határozza meg, melyik szálak várnak, így meg tudja állapítani, hogy a kritikus fontosságú a kérelem elérési útja. A legtöbb esetben a gyorsan várakozási állapotba kerül, a szál egyszerűen a más szálak vár. A más szálak összpontosít, és figyelmen kívül hagyja az idő a várakozó szál.

### <a id="issue-loading-trace-in-viewer"></a>Profilkészítési adatok

Az alábbiakban néhány dolog, ellenőrizheti:

* Ha a megtekinteni kívánt adatok néhány hétre régebbi, próbálkozzon a időszűrője korlátozása, és próbálkozzon újra.
* Győződjön meg arról, hogy proxyk és a tűzfalon nem letiltott https://gateway.azureserviceprofiler.net elérésére.
* Győződjön meg arról, hogy az Application Insights instrumentation kulcsot használ az alkalmazás ugyanaz, mint az Application Insights-erőforrást, amelyen engedélyezett profilkészítési. A kulcs általában az ApplicationInsights.config fájlban, de a web.config vagy az app.config fájlban is lehet.

### <a name="error-report-in-the-profiling-viewer"></a>Hibajelentés a profilkészítési megjelenítőben

Küldje el a portál egy támogatási jegy. Ne felejtse el feltüntetni a korrelációs azonosító, a hibaüzenet.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Központi telepítési hiba: könyvtár nem üres "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok

Webalkalmazások erőforrás webalkalmazás a Profilkészítő engedélyezve vannak újratelepíteni, ha a következőhöz hasonló üzenetet láthatja:

*Könyvtár nem üres "D:\\otthoni\\hely\\wwwroot\\App_Data\\feladatok*

Ez a hiba akkor fordul elő, ha futtatja a Web Deploy parancsfájlok vagy a Visual Studio Team Services telepítési folyamat. A megoldás, hogy adja meg a következő további telepítési paramétereit a Web Deploy feladat:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ezek a paraméterek Application Insights Profilkészítő használja, és helyezze üzembe újra folyamat feloldása mappa törlése. Nincsenek hatással a jelenleg futó Profilkészítő példányok.


## <a name="manual-installation"></a>Manuális telepítés

Profilkészítő konfigurálásakor frissítések válnak, a webes alkalmazás beállításai. Ha a környezet elő, manuálisan alkalmazhatja a frissítéseket. Például előfordulhat, hogy az alkalmazás fut a Web Apps környezetekben a powerapps segítségével.

1. Az a **Web App Control** ablaktábláján nyissa meg **beállítások**.
2. Állítsa be **.Net keretrendszer** való **v4.6**.
3. Állítsa be **Always On** való **a**.
4. Adja hozzá a **APPINSIGHTS_INSTRUMENTATIONKEY** app beállításával, és állítsa be az értéket az SDK által használt azonos instrumentation kulccsal.
5. Nyissa meg **speciális eszközök**.
6. Válassza ki **Ugrás** a Kudu webhely megnyitásához.
7. Válassza ki a Kudu webhelyen **bővítmények hely**.
8. Telepítés **az Application Insights** az Azure Web Apps gyűjteményből.
9. A webalkalmazás újraindítása.

## <a id="profileondemand"></a> Manuálisan eseményindító Profilkészítő
Kidolgoztunk Profilkészítő, ha hozzáadott egy parancssori felületet, hogy az app Services sikerült teszteljük Profiler. Az azonos felhasználói felületének használatával felhasználók is testre Profilkészítő indításának. Magas szinten Profilkészítő a Web Apps Kudu rendszer kezelésére használ a háttérben profilkészítési. Az Application Insights-bővítmény telepítésekor létrehozhatunk egy folyamatos web feladat futtató Profiler. Ez a technológia segítségével hozzon létre egy új webes projektet, szükség szerint testre szabható.

Ez a szakasz azt ismerteti, hogyan:

* Hozzon létre egy webes feladat indítható Profilkészítő két percig a nyomja meg a gomb.
* Hozzon létre egy webes feladatot, amely Profilkészítő futtatásra is ütemezheti.
* Állítsa be a Profilkészítő argumentumai.


### <a name="set-up"></a>Beállítás
Első lépésként Ismerkedjen meg a webes projekt irányítópultján. A **beállítások**, jelölje be a **WebJobs** fülre.

![webjobs-feladatok panel](./media/app-insights-profiler/webjobs-blade.png)

Ahogy látja, ez az irányítópult a helyen aktuálisan telepített összes webes feladatok jeleníti meg. A Profilkészítő futó feladat rendelkezik ApplicationInsightsProfiler2 webes feladat tekintheti meg. Ez azért, ahol a manuális és ütemezett profilkészítési létrehozhatunk új webes feladatok.

Ahhoz, hogy a bináris fájlok van szüksége, tegye a következőket:

1.  A Kudu helyen a a **Fejlesztőeszközök** lapon jelölje be a **speciális eszközök** a Kudu emblémával lapra, majd válassza ki **Ugrás**.  
   Megnyílik egy új helyet, és be van jelentkezve automatikusan.
2.  A Profilkészítő bináris fájlok letöltéséhez nyissa meg a Fájlkezelőt keresztül **Debug konzol** > **CMD**, amely itt található: a lap tetején.
3.  Válassza ki **hely** > **wwwroot** > **App_Data** > **feladatok**  >   **Folyamatos**.  
   Nevű mappa kell megjelennie *ApplicationInsightsProfiler2*. 
4. A bal oldali a mappa, válassza ki a **letöltése** ikonra.  
   Ez a művelet letölti a *ApplicationInsightsProfiler2.zip* fájlt. Azt javasoljuk, hogy hozzon létre egy helyezhető át a zip-archívum a könyvtár törlése.

### <a name="setting-up-the-web-job-archive"></a>A feladat webarchívum beállítása
Ha hozzáad egy új webes projektet az Azure webhelyén, lényegében létrehozta a zip-archívum létrehozása egy *run.cmd* fájlon belül. A *run.cmd* fájl közli a webes projekt rendszer Mi a teendő, ha a webes.

1.  Hozzon létre egy új mappát (például *RunProfiler2Minutes*).
2.  A fájlok másolását a kibontott *ApplicationInsightProfiler2* mappa az új mappába.
3.  Hozzon létre egy új *run.cmd* fájlt.  
    Kényelmi célokat szolgál megnyithatja a munkamappa a Visual Studio Code megkezdése előtt.
4.  A fájlban adja hozzá a parancs `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120`. A parancsok az alábbiak szerint:

    * `start`: Közli a Profilkészítő elindításához.  
    * `--engine-mode immediate`: Közli a Profilkészítő profilkészítési azonnali megkezdéséhez.  
    * `--single`: Közli a Profilkészítő futtatásához, és állíthatja le automatikusan.  
    * `--immediate-profiling-duration 120`: Közli a Profilkészítő 120 másodperc, vagy 2 percet futtatásához.

5.  Mentse a módosításokat.
6.  Ehhez kattintson a jobb gombbal, és jelölje be a mappa archiválására **küldése** > **tömörített mappa**.  
   Ez a művelet létrehoz egy .zip fájlt, amely annak a mappának a nevét használja.

![Profilkészítő parancs indítását.](./media/app-insights-profiler/start-profiler-command.png)

Most létrehozott webes feladat .zip fájlt, amely segítségével a hely webes projektek beállítása.

### <a name="add-a-new-web-job"></a>Új webszolgáltatás hozzáadása
Ebben a szakaszban egy új webes projektet a webhely hozzáadása. A következő példa bemutatja, hogyan manuálisan indított webes feladat hozzáadásához. A manuálisan indított webes projekt hozzáadása után a folyamat megegyezik majdnem webes ütemezett feladat.

1.  Lépjen a **webalkalmazás-feladatok** irányítópult.
2.  Az eszköztáron válassza **Hozzáadás**.
3.  Adjon meg egy nevet a webes projekt.  
    Az átláthatóság érdekében segíthet az archívum neve megfelelő, és nyissa meg az különböző verzióiból szolgáltatáshoz a *run.cmd* fájlt.
4.  Az a **fájlfeltöltés** területén az űrlapot, jelölje be a **nyitott fájlok** ikonra, és keressen az előző szakaszban létrehozott .zip fájl.

    a.  Az a **típus** mezőben válassza **indított**.  
    b.  Az a **eseményindítók** mezőben válassza **manuális**.

5.  Kattintson az **OK** gombra.

![Profilkészítő parancs indítását.](./media/app-insights-profiler/create-webjob.png)

### <a name="run-profiler"></a>Profilkészítő futtatása

Most, hogy egy új web feladat, manuálisan is elindíthatja, megpróbálhatja ebben a szakaszban foglalt utasítások futtatásához.

Úgy lett kialakítva, hogy lehet *ApplicationInsightsProfiler.exe* egy adott időpontban a számítógépen futó folyamat. Ezért, mielőtt elkezdené, tiltsa le a *folyamatos* webes projekt erről az irányítópultról. 
1. Válassza ki az új webes projektet, és válassza ki **leállítása**. 
2. Az eszköztáron válassza **frissítése**, és győződjön meg arról, hogy az állapot azt jelzi, hogy a feladat leállt.
3. Válassza ki az új webes projektet, és válassza ki **futtatása**.
4. Jelöljön ki van jelölve, kattintson az eszköztáron a sort a **naplók** parancsot.  
    Ez a művelet megnyitja az új webes projekt webes feladatok irányítópultot, és felsorolja a legutóbbi fut, és az eredményeiket.
5. Válassza ki a példány akkor kezdte futtat.  
    Az új webes projekt kiváltása sikerült is, ha néhány győződjön meg arról, hogy elindult a profilkészítési diagnosztikai naplók Profilkészítő érkező tekintheti meg.

### <a name="things-to-consider"></a>Megfontolandó szempontok

Bár ez a módszer viszonylag egyszerű, vegye figyelembe a következőket:

* A szolgáltatás nem kezeli a web feladat, mert jelenleg semmilyen módon nem lehet frissíteni az ügynök bináris fájljait a webes projekt áll. Jelenleg nincs stabil letöltési oldal a bináris fájljai, a, a csak a legújabb bináris fájlok eléréséhez, a bővítmény frissítése és grabbing azt a *folyamatos* mappát, ha Ön volt az előző lépésben.

* Ezt a folyamatot, amely a végfelhasználók helyett a fejlesztők az eredetileg parancssori argumentumokat használja, mert az argumentumok a későbbiekben változhat. Vegye figyelembe az esetleges módosításai frissítésekor. Nagy részét a problémák annak nem lehet, hogy egy webes projekt hozzáadása, majd futtassa, és ellenőrizze, hogy működik. Végül azt fog létrehozni a felhasználói Felületet ennek kezelése nélkül a manuális eljáráshoz.

* A Web Apps webes feladatok használata egyedi. A webes projekt futtatott biztosítja, hogy a folyamat rendelkezik-e az azonos környezeti változókat és, hogy a webhely Alkalmazásbeállítások. Ez azt jelenti, hogy nem kell átadni a instrumentation billentyűt a parancssor használatával Profiler. Profilkészítő a környezet instrumentation kulccsal kell átvételéhez. Azonban ha azt szeretné, a Profilkészítő a fejlesztői mezőben vagy a Web Apps kívül gépen fut, kell megadnia egy rendszerállapot-kulcsot. Ehhez úgy, hogy egy argumentum `--ikey <instrumentation-key>`. Ezt az értéket meg kell egyeznie a instrumentation kulcs, amely az alkalmazás használja. Napló kimenetét a Profilkészítő megtudhatja, mely ikey Profilkészítő használatába és során azt is profilkészítési e észlelte instrumentation kulcs tevékenységre.

* Manuálisan indított webes feladatok is elindítható a webes Hook keresztül. Az URL-CÍMEN kaphat kattintson a jobb gombbal a web feladat, az irányítópult és a Tulajdonságok megtekintése. Vagy az eszköztáron válassza **tulajdonságok** a tábla a webes projekt kiválasztása után. Ez a megközelítés korlátlan lehetőségek, például a Profilkészítő időt. a CI/CD folyamatot (például VSTS) vagy a Microsoft Flow (https://flow.microsoft.com/en-us/) hasonlót megnyílik. Végső soron a választott attól függ, milyen összetett kívánja tenni a *run.cmd* fájl (is lehet, amely egy *run.ps1* fájl), de rugalmasan van-e.

## <a name="next-steps"></a>További lépések

* [A Visual Studio Application Insights használata](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
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

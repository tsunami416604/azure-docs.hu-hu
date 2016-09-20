<properties
    pageTitle="Trendek elemzése a Visual Studio segítségével | Microsoft Azure"
    description="Elemezze, ábrázolja és ismerje meg részletesebben a trendeket a Visual Studio Application Insights Telemetria funkciója segítségével."
    services="application-insights"
    documentationCenter=".net"
    authors="numberbycolors"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="daviste"/>

# Trendek elemzése a Visual Studio segítségével

Az Application Insights Trends eszköze segítségével vizuálisan is megtekintheti az alkalmazásai fontos telemetriai eseményeiben adott időtartam alatt bekövetkező változásokat, amely segít a problémák és rendellenességek gyors azonosításában. A Trends részletesebb diagnosztikai adatokhoz nyújt hozzáférést, amelyek révén javíthat alkalmazásai teljesítményén, megkeresheti a kivételek forrását, és mélyebb betekintést nyerhet az egyéni eseményekbe is.

![Példa a Trends ablakára](./media/app-insights-trends/app-insights-trends-hero-750.png)

> [AZURE.NOTE] Az Application Insights Trends szolgáltatása a Visual Studio 2015 3. frissítésétől kezdve, illetve a [Developer Analytics Tools bővítmény](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) 5.209-es verziójától kezdve érhető el.

## Az Application Insights Trends szolgáltatás megnyitása

Az Application Insights Trends ablakot a következő módszerekkel nyithatja meg:

* Az Application Insights eszköztár gombjánál válassza az **Explore Telemetry Trends** (Telemetriai trendek felderítése) lehetőséget.
* A projekt helyi menüjében válassza az **Application Insights > Explore Telemetry Trends** (Telemetriai trendek felderítése) lehetőséget.
* A Visual Studio menüsávjából válassza a **View (Nézet) > Other Windows (Egyéb ablakok) > Application Insights Trends** lehetőséget.

A rendszer felszólíthatja, hogy válasszon erőforrást. Ha ez történik, kattintson a **Select a resource** (Erőforrás kiválasztása) lehetőségre, jelentkezzen be Azure-előfizetésébe, majd válassza ki az Application Insights-erőforrást, amelynek telemetriai trendjeit elemezni szeretné.

## Trendelemzés kiválasztása

![A trendelemzések gyakran használt típusait tartalmazó menü](./media/app-insights-trends/app-insights-trends-1-750.png)

Első lépésként válasszon az öt gyakran használt trendelemzési típus közül, amelyek az elmúlt 24 óra adatait fogják elemezni:

* **Investigate performance issues with your server requests** (A kiszolgálókérelmekkel kapcsolatos teljesítményproblémák kivizsgálása): a szolgáltatás felé irányuló kérelmek, válaszidő szerint csoportosítva.
* **Analyze errors in your server requests** (A kiszolgálókérelmekkel kapcsolatos hibák kivizsgálása): a szolgáltatás felé irányuló kérelmek, HTTP-válaszkód szerint csoportosítva.
* **Examine the exceptions in your application** (Az alkalmazásban fellépő kivételek kivizsgálása): a szolgáltatásban fellépő kivételek, kivételtípus szerint csoportosítva.
* **Check the performance of your application's dependencies** (Az alkalmazás függőségei teljesítményének ellenőrzése): a szolgáltatás által meghívott szolgáltatások, válaszidő szerint csoportosítva.
* **Inspect your custom events** (Egyéni események kivizsgálása): a szolgáltatáshoz Ön által beállított egyéni események, eseménytípus szerint.

Ezek az előre kialakított elemzések később, a Trends ablakának bal felső sarkában található **View common types of telemetry analysis** (A telemetriai elemzés gyakran használt típusainak megtekintése) gombra kattintva is elérhetőek.

## Az alkalmazásra vonatkozó trendek vizuális megjelenítése

Az Application Insights Trends szolgáltatása idősoron jeleníti meg az alkalmazás telemetriai adatait. Az egyes idősorok egy-egy telemetriai típus adott időtartamra vonatkozó adatait tartalmazzák, a telemetria egy konkrét tulajdonsága szerint csoportosítva.

Előfordulhat például, hogy az elmúlt 24 órában érkező kiszolgálókérelmeket szeretné megtekinteni, mindezt az azokat küldő fél országa szerint csoportosítva. A példában a képen látható körök azt jelzik, hogy egy óra alatt hány kiszolgálókérelem érkezett az adott országból.

Az ablak felső részén található vezérlőeszközök segítségével beállíthatja, hogy a telemetriai adatok mely típusait szeretné megtekinteni. Először válassza ki azokat a telemetriai adatok azon típusait, amelyeknek az adataira kíváncsi:

* **Telemetry Type** (Telemetriai adatok típusa): kiszolgálókérelmek, kivételek, függőségek és egyéni események.
* **Time Range** (Időintervallum): az elmúlt 30 perc és az elmúlt 3 nap közötti értéket választhat.
* **Group By** (Csoportosítás alapja): kivétel típusa, probléma azonosítója, ország/régió és más lehetőségek közül választhat.

A beállítások megadását követően a lekérdezés elvégzéséhez kattintson az **Analyze Telemetry** (Telemetriai adatok elemzése) gombra.

A vizualizáción látható körök közötti navigáció:

* Kattintással válassza ki a kívánt kört. Ekkor frissülnek az ablak alján látható szűrők, amelyek összefoglalják az adott időintervallumban történt eseményeket.
* Kattintson duplán a körre a Search (Keresés) eszköz megnyitásához. Itt az adott időintervallumban bekövetkezett összes telemetriai esemény megtekinthető.
* Nyomja le a **Ctrl** billentyűt, majd kattintson a körre a kijelölés megszüntetéséhez.

> [AZURE.TIP] A Trends és a Search eszköz segítségével pontosan megállapíthatja, hogy melyik telemetriai esemény okozott zavart a szolgáltatásban. A Trends segítségével könnyen megtalálhatja a megoldást, ha például ügyfelei jelzik, hogy az alkalmazás egy adott délután során lassabban reagált. Elemezze válaszidő szerint csoportosítva a szolgáltatás felé irányuló kérelmeket az elmúlt néhány órából. Figyelje meg, hogy látja-e a hosszabb időt igénylő kérelmek nagyobb csoportját. Ha megtalálta, kattintson duplán erre a körre, megnyitva ezzel a keresőeszközt, amely már a kérdéses kérelemeseményekre megszűrve jelenik meg. A Search (Keresés) ablakban tekintse át a kérések tartalmát, és keresse meg az érintett kódot, ami jelentősen megkönnyíti a probléma megoldását.

## Szűrés konkrét trendekre

Az ablak alján található szűrők segítségével konkrétabb trendeket is azonosíthat. Kattintson az alkalmazni kívánt szűrő nevére. A szűrők között váltogatva felfedezheti a trendeket, amelyeket a telemetriai adatok különböző dimenziói rejtenek. Attól, hogy az egyik dimenzióban, például az Exception Type (Kivétel típusa) dimenzióban bekapcsol egy szűrőt, továbbra is rákattinthat a többi dimenzióban található szűrőkre, függetlenül attól, hogy szürkén jelennek meg. A szűrés visszavonásához kattintson ismét a szűrőre. Ha egy dimenzióra több szűrőt is alkalmazni szeretne, nyomja le a **Ctrl** billentyűt, és így kattintson rá a kívánt szűrőkre.

![Trendszűrők](./media/app-insights-trends/TrendsFiltering-750.png)

Mit kell tennem, ha több szűrőt szeretnék használni?

1. Alkalmazza az első szűrőt.
2. Kattintson az első szűrő dimenziójának neve melletti **Apply selected filters and query again** (Kiválasztott szűrők alkalmazása, majd ismételt lekérdezés) gombra. Ekkor a rendszer ismét lekérdezi a telemetriai adatokat, ezúttal kifejezetten az első szűrőnek megfelelő eseményeket keresve.
3. Alkalmazza a második szűrőt.
4. Ismételje addig a folyamatot, amíg nem sikerül azonosítani a kívánt trendeket a telemetriai adatok különböző részhalmazaiban. Például megkeresheti azokat a „GET Home/Index” nevű kiszolgálókérelmeket, amelyek Németország területéről érkeztek, és amelyek az 500-as állapotkódot kapták.

A szűrés törléséhez kattintson az adott dimenzióhoz tartozó **Remove selected filters and query again** (Kiválasztott szűrők törlése, majd ismételt lekérdezés) gombra.

![Több szűrő](./media/app-insights-trends/TrendsFiltering2-750.png)

## Rendellenességek keresése

A Trends eszköz arra is képes, hogy megmutassa, mely eseménykörök számítanak rendellenesnek az adott idősorban szereplő többi körhöz képest. A **View Type** (Nézet típusa) legördülő menüben válassza a **Counts in time bucket (highlight anomalies)** (Események száma az ellenőrzési időtartamban (rendellenességek kiemelése)) vagy a **Percentages in time bucket (highlight anomalies)** (Százalékos arány az ellenőrzési időtartamban (rendellenességek kiemelése)) lehetőséget. A rendszer pirossal jelöli a rendellenesként értékelt köröket.

Rendellenesek azok a körök, amelyek eseményszáma/százalékos aránya több mint 2,1-szerese az elmúlt két időintervallumban (azaz például az elmúlt 48 órában, ha az elmúlt 24 óra adatait nézi) bekövetkezett eseményszámok/százalékos arányok szórásának.

![A színes pontok rendellenességeket jelölnek.](./media/app-insights-trends/TrendsAnomalies-750.png)

> [AZURE.TIP] A rendellenességek kiemelése különösen hasznos lehet, ha az idősor egyébként hasonlónak látszó kisebb körei között keresi a rendellenes elemeket.  

## <a name="next"></a>Következő lépések


[Munkavégzés az Application Insights szolgáltatással a Visual Studióban](app-insights-visual-studio.md): keressen telemetriai adatokat, tekintse meg az adatokat a CodeLensben, és konfigurálja az Application Insights szolgáltatást közvetlenül a Visual Studióból.

[További adatok hozzáadása](app-insights-asp-net-more.md): kövesse figyelemmel a használatot, a rendelkezésre állást, a függőségeket és a kivételeket. Integrálhatja a nyomkövetéseket naplózási keretrendszerekből. Egyéni telemetriát írhat.

[Munkavégzés az Application Insights-portállal](app-insights-dashboards.md): irányítópultok, hatékony diagnosztikai és elemzőeszközök, riasztások, valamint élő függőségi térkép az alkalmazáshoz, és a telemetria exportálása.



<!--HONumber=sep16_HO1-->



---
title: "Webalkalmazás-elemzés beállítása az ASP.NET-hez az Azure Application Insights segítségével | Microsoft Docs"
description: "Konfigurálhatja a helyszínen vagy az Azure-ban üzemeltetett ASP.NET-webhely teljesítményét, rendelkezésre állását és használatelemzését."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: 8a3789d293a93aab19eab92ffefddf0b3d2f9242
ms.contentlocale: hu-hu
ms.lasthandoff: 05/19/2017


---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Az Application Insights beállítása az ASP.NET-webhelyhez

Ez az eljárás beállítja, hogy az ASP.NET webapp telemetriát küldjön az [Azure Application Insights](app-insights-overview.md) szolgáltatásnak. Az eljárás a saját IIS kiszolgálón vagy a felhőben futtatott ASP.NET alkalmazásokon használható. Diagramokat és hatékony lekérdezési nyelvet biztosít, amelyek révén jobban megismerheti az alkalmazás működését, és hogy a felhasználók minként használják, valamint automatikus riasztásokat kaphat a meghibásodásokkal és a teljesítményproblémákkal kapcsolatban. Számos fejlesztő már így is rendkívül hasznosnak tartja ezeket a funkciókat, azonban a telemetria szükség esetén bővíthető és testre is szabható.

A telepítés mindössze néhány kattintással végrehajtható a Visual Studióban. A szolgáltatás díjmentesen is használható, ha a telemetria mennyiségét korlátozza. Így használható például kísérletezéshez és hibaelhárításhoz, vagy kis felhasználószámú webhelyek figyeléséhez. Ha úgy dönt, hogy belevág, és az éles webhelyét is figyelni fogja, a későbbiekben könnyen emelhető a korlát.

## <a name="before-you-start"></a>Előkészületek
A következők szükségesek:

* Visual Studio 2013 3. frissítés vagy újabb. Az újabb jobb.
* Egy [Microsoft Azure](http://azure.com)-előfizetés. Ha a csapata vagy a szervezete rendelkezik Azure-előfizetéssel, a tulajdonosa Önt is hozzáadhatja a [Microsoft-fiókja](http://live.com) segítségével.

További témaköröket is elolvashat, ha a következők érdeklik:

* [Webalkalmazás beállítása futási időben](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> 1. lépés: Az Application Insights SDK hozzáadása

Kattintson a jobb gombbal a webalkalmazás-projektre a Solution Explorer (Megoldáskezelő) területén, és válassza az **Add** (Hozzáadás),  > **Application Insights Telemetry** (Application Insights-telemetria) vagy a **Configure Application Insights** (Application Insights konfigurálása) elemet.

![A Solution Explorer (Megoldáskezelő) képernyőképe, a kiemelt Add Application Insights Telemetry (Application Insights telemetria hozzáadása) elemmel](./media/app-insights-asp-net/appinsights-03-addExisting.png)

(A Visual Studio 2015-ben emellett lehetőség van az Application Insights hozzáadására a New Project (Új projekt) párbeszédpanelen.)

Lépjen tovább az Application Insights konfigurációs oldalára:

![Képernyőkép az alkalmazásregisztrációs szakaszról az Application Insights oldalon](./media/app-insights-asp-net/visual-studio-register-dialog.png)

**a.** Válassza ki az Azure eléréséhez használt fiókot és előfizetést.

**b.** Válassza ki azt az Azure-beli erőforrást, amelyben látni szeretné az alkalmazásából származó adatokat. Általában:

* [Egyetlen erőforrást használjon](app-insights-monitor-multi-role-apps.md) egy alkalmazás különböző összetevőihez. 
* Különálló erőforrásokhoz hozzon létre az egymáshoz nem kapcsolódó alkalmazásokhoz.
 
Ha szeretné beállítani az erőforráscsoportot vagy az adatok tárolásának helyét, kattintson a **Configure settings** (Beállítások konfigurálása) lehetőségre. Az erőforráscsoportok az adatokhoz való hozzáférés szabályozásához használhatóak. Ha például több alkalmazása is van, amelyek egy adott rendszer részét képezik, azok Application Insights-adatait ugyanabba az erőforráscsoportba helyezheti.

**c.** A költségek elkerülése érdekében állítson be egy, az ingyenes adatmennyiség korlátjával egyező korlátot. Az Application Insights egy bizonyos telemetriamennyiségig ingyenes. Az erőforrás létrehozása után módosíthatja a választást a portálon a **Szolgáltatások + díjszabás** > **Adatmennyiség kezelése** > **Napi mennyiségkorlát** lehetőség kiválasztásával.

**d.** A folytatáshoz és az Application Insights a webapphoz való konfigurálásához kattintson a **Regisztrálás** gombra. A telemetria az [Azure Portalra](https://portal.azure.com) lesz küldve a hibakeresés során és az alkalmazás közzététele után is.

**e.** Ha a hibakeresés során nem szeretne telemetriát küldeni a portálra, adja hozzá az Application Insights SDK-t az alkalmazáshoz, de ne konfiguráljon erőforrást a portálon. A telemetria a hibakeresés során a Visual Studióban lesz megtekinthető. Később visszatérhet erre a konfigurációs oldalra, vagy megvárhatja az alkalmazás üzembe helyezését, és [bekapcsolhatja a telemetriát a futtatás során](app-insights-monitor-performance-live-website-now.md).


## <a name="run"></a> 2. lépés: Az alkalmazás futtatása
Futtassa az alkalmazást az F5 billentyűvel. Nyisson meg több lapot, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja a naplózott események számát.

![A Visual Studio képernyőképe. Megjelenik az Application Insights gomb a hibakeresés alatt.](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry"></a>3. lépés: A telemetria megtekintése
A telemetriát a Visual Studióban vagy az Application Insights webportálon tekintheti meg. A telemetria keresése a Visual Studióban segíti az alkalmazás hibakeresését. A teljesítményt és a használatot a webes portálon figyelheti, amikor a rendszer élesben működik. 

### <a name="see-your-telemetry-in-visual-studio"></a>Telemetria megtekintése a Visual Studióban

A Visual Studióban nyissa meg az Application Insights ablakot. Kattintson az **Application Insights** gombra, vagy kattintson a jobb gombbal a projektre a Solution Explorer (Megoldáskezelő) felületén, válassza az **Application Insights** elemet, majd kattintson a **Search Live Telemetry** (Élő telemetria keresése) gombra.

A Visual Studio Application Insights keresőablakában a **Data from Debug session** (Hibakeresési munkamenetből származó adatok) nézetben tekintheti meg az alkalmazás kiszolgálói oldalán létrehozott telemetriát. Kísérletezzen a szűrőkkel, és kattintson valamely eseményre további részletek megtekintéséhez.

![A Data from debug session (Hibakeresési munkamenetből származó adatok) nézet képernyőképe az Application Insights ablakban.](./media/app-insights-asp-net/55.png)

> [!NOTE]
> Ha nem jelennek meg adatok, ellenőrizze, hogy megfelelő-e az időtartomány, majd kattintson a Search (Keresés) ikonra.

[További tudnivalók az Application Insights-eszközökről a Visual Studióban](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Telemetria megtekintése a webportálon

A telemetriát az Application Insights webportálon is megtekintheti (ha a telepítéskor nem csak az SDK-t telepítette). A portálon a Visual Studiónál több diagram, elemzőeszköz és összetevőket megjelenítő nézet található. A portál riasztásokat is biztosít.

Nyissa meg az Application Insights-erőforrást. Bejelentkezhet az [Azure Portalra](https://portal.azure.com/), és itt megkeresheti, vagy a jobb gombbal kattinthat a projektre a Visual Studióban, amely odavezeti.

![A Visual Studio képernyőképe, amelyen az Application Insights portál megnyitásának módja látható](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> Ha hibaüzenetet kapott: lehetséges, hogy több microsoftos hitelesítőadat-készlettel is rendelkezik, és nem a megfelelővel jelentkezett be? Jelentkezzen ki, majd be a portálon.

A portál az alkalmazásából származó telemetriai adatok nézetével nyílik meg.

![Az Application Insights áttekintési oldalának képernyőképe](./media/app-insights-asp-net/66.png)

A portálon az egyik csempére vagy diagramra kattintva további részleteket tekinthet meg.

[További tudnivalók az Application Insights használatáról az Azure Portalon](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>4. lépés: Az alkalmazás közzététele
Tegye közzé alkalmazását az IIS-kiszolgálón vagy az Azure-on. Az [Élő mérőszámok streammel](app-insights-metrics-explorer.md#live-metrics-stream) ellenőrizheti, hogy minden rendben működik-e.

A telemetria az Application Insights portálon épül fel, ahol figyelheti a mérőszámokat, kereshet a telemetriára és [irányítópultokat](app-insights-dashboards.md) állíthat be. Használhatja a nagy teljesítményű [Analytics lekérdezési nyelvet](app-insights-analytics.md) a használat és a teljesítmény elemzéséhez, vagy adott események megtalálásához.

Folytathatja a telemetria elemzését a [Visual Studióban](app-insights-visual-studio.md) olyan eszközökkel, mint például a diagnosztikai keresés és a [trendek](app-insights-visual-studio-trends.md).

> [!NOTE]
> Ha az alkalmazása elég telemetriát küld a [szabályozási korlát](app-insights-pricing.md#limits-summary) eléréséhez, az automatikus [mintavételezés](app-insights-sampling.md) bekapcsol. A mintavételezés csökkenti az alkalmazásból küldött telemetria mennyiségét, míg a korrelatív adatokat diagnosztikai célból megőrzi.
>
>

## <a name="land"></a>Készen is van.

Gratulálunk! Telepítette az Application Insights csomagot az alkalmazásba, és konfigurálta, hogy telemetriát küldjön az Application Insights szolgáltatásba az Azure-ban.

![A telemetria mozgásának ábrája](./media/app-insights-asp-net/01-scheme.png)

Az alkalmazás telemetriáját fogadó Azure-erőforrást egy *kialakítási kulcs* azonosítja. A kulcs az ApplicationInsights.config fájlban található.


## <a name="upgrade-to-future-sdk-versions"></a>Frissítés a jövőbeli SDK-verziókra
Ha frissíteni szeretne egy [új SDK-kiadásra](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), nyissa meg ismét a **NuGet-csomagkezelőt**, és szűréssel keresse meg a telepített csomagokat. Jelölje ki a **Microsoft.ApplicationInsights.Web** lehetőséget, és válassza a **Frissítés** elemet.

Ha az ApplicationInsights.config fájlt testreszabta, mentse el egy példányát a frissítés előtt. Ezután egyesítse a módosításait az új verzióval.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Következő lépések

### <a name="more-telemetry"></a>További telemetria

* **[Böngésző és oldalbetöltési adatok](app-insights-javascript.md)** – Kódrészlet beszúrása a weboldalakra.
* **[Részletesebb függőség- és kivételfigyelés](app-insights-monitor-performance-live-website-now.md)** – Állapotfigyelő telepítése a kiszolgálón.
* **[Kódoljon egyéni eseményeket](app-insights-api-custom-events-metrics.md)** a felhasználói műveletek számlálásához, időzítéséhez és méréséhez.
* **[Naplóadatok lekérése](app-insights-asp-net-trace-logs.md)** – Naplóadatok összevetése a telemetriával.

### <a name="analysis"></a>Elemzés

* **[Az Application Insights használata a Visual Studióban](app-insights-visual-studio.md)**<br/>A telemetriával végzett hibakereséssel, diagnosztikai kereséssel és a kódig való részletezés lefúrással kapcsolatos információkat tartalmaz.
* **[Az Application Insights-portál használata](app-insights-dashboards.md)**<br/> Az irányítópultokkal, a hatékony diagnosztikai és elemzési eszközökkel, riasztásokkal, az alkalmazás élő függőségi térképével, valamint a telemetria exportálásával kapcsolatos információkat tartalmaz.
* **[Elemzés](app-insights-analytics-tour.md)** – Erőteljes lekérdezési nyelv.

### <a name="alerts"></a>Riasztások

* [Rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md): Hozzon létre teszteket, hogy megbizonyosodjon róla, oldala látható a weben.
* [Intelligens diagnosztika](app-insights-proactive-diagnostics.md): Ezek a tesztek automatikusan futnak, a beállításukhoz semmit sem kell tennie. Értesítést kap, ha az alkalmazásában szokatlanul magas a meghiúsult kérelmek száma.
* [Metrikariasztások](app-insights-alerts.md): Állítsa be ezeket, hogy figyelmeztetést kapjon, ha egy metrika átlépi a küszöbértéket. Az alkalmazás kódjába beépített egyedi metrikákhoz is állíthat be riasztásokat.

### <a name="automation"></a>Automatizálás

* [Application Insights-erőforrások létrehozásának automatizálása](app-insights-powershell.md)


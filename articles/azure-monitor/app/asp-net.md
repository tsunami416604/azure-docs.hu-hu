---
title: Webalkalmazás-elemzés beállítása az ASP.NET-hez az Azure Application Insights segítségével | Microsoft Docs
description: Konfigurálhatja a helyszíni vagy az Azure-ban üzemeltetett ASP.NET-webhely teljesítmény-, rendelkezésre állási és felhasználói viselkedési elemzési eszközeit.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: acfba63cba520631831888a1480929be3b1897f0
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475532"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Az Application Insights beállítása az ASP.NET-webhelyhez

Ez az eljárás beállítja, hogy az ASP.NET webapp telemetriát küldjön az [Azure Application Insights](./app-insights-overview.md) szolgáltatásnak. Az eljárás a saját helyszíni IIS-kiszolgálón vagy a felhőben futtatott ASP.NET-alkalmazásokon használható. Diagramokat és hatékony lekérdezési nyelvet biztosít, amelyek révén jobban megismerheti az alkalmazás működését, és hogy a felhasználók minként használják, valamint automatikus riasztásokat kaphat a meghibásodásokkal és a teljesítményproblémákkal kapcsolatban. Számos fejlesztő már így is rendkívül hasznosnak tartja ezeket a funkciókat, azonban a telemetria szükség esetén bővíthető és testre is szabható.

A telepítés mindössze néhány kattintással végrehajtható a Visual Studióban. A szolgáltatás díjmentesen is használható, ha a telemetria mennyiségét korlátozza. Ez a funkció lehetővé teszi a kísérletezést és hibakeresést, illetve a nem sok felhasználóval rendelkező helyek figyelését. Ha úgy dönt, hogy belevág, és az éles webhelyét is figyelni fogja, a későbbiekben könnyen emelhető a korlát.

## <a name="prerequisites"></a>Előfeltételek
Ha hozzá kívánja adni az Application Insights megoldást ASP.NET-webhelyéhez, tegye a következőket:

- Telepítse a [Windowshoz készült Visual Studio 2019](https://www.visualstudio.com/downloads/) alkalmazást a következő számítási feladatokkal:
    - ASP.NET és webes fejlesztés (ne törölje a választható összetevőket)
    - Azure-fejlesztés

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a> 1. lépés: Az Application Insights SDK hozzáadása

> [!IMPORTANT]
> Az ebben a példában szereplő Képernyőképek a Visual Studio 2017 15.9.9 és újabb verzióján alapulnak. A Application Insights hozzáadásának élménye a Visual Studio és a ASP.NET-sablon típusának különböző verzióiban is változhat. Előfordulhat, hogy a régebbi verziók helyettesítő szöveggel rendelkeznek, például "configure Application Insights".

Kattintson a jobb gombbal a webalkalmazás nevére a megoldáskezelő, majd válassza a **Hozzáadás**  >  **Application Insights telemetria**

![A Solution Explorer (Megoldáskezelő) képernyőképe, a kiemelt Configure Application Insights (Application Insights konfigurálása) elemmel](./media/asp-net/add-telemetry-new.png)

(Az Application Insights SDK verziójától függően a rendszer felkérheti, hogy frissítsen az SDK legújabb kiadására. Ebben az esetben válassza az **Update SDK** (SDK frissítése) lehetőséget.)

![Képernyőkép: Elérhető a Microsoft Application Insights SDK egy új verziója. Az SDK frissítési lehetőség kiemelve](./media/asp-net/0002-update-sdk.png)

Az Application Insights konfigurációs képernyőjén:

Válassza az első **lépések**lehetőséget.

![Képernyőkép az alkalmazásregisztrációs szakaszról az Application Insights oldalon](./media/asp-net/00004-start-free.png)

Ha szeretné beállítani az erőforráscsoportot vagy az adatok tárolásának helyét, kattintson a **Configure settings** (Beállítások konfigurálása) lehetőségre. Az erőforráscsoportok az adatokhoz való hozzáférés szabályozásához használhatóak. Ha például több alkalmazása is van, amelyek egy adott rendszer részét képezik, azok Application Insights-adatait ugyanabba az erőforráscsoportba helyezheti.

 Válassza a **Regisztráció** lehetőséget.

![Képernyőkép az alkalmazásregisztrációs szakaszról az Application Insights oldalon](./media/asp-net/00005-register-ed.png)

 Válassza a **Project**  >  **NuGet-csomagok kezelése**  >  **csomag forrása: nuget.org** > ellenőrizze, hogy az Application Insights SDK legújabb stabil kiadása van-e.

 A telemetria az [Azure Portalra](https://portal.azure.com) lesz küldve a hibakeresés során és az alkalmazás közzététele után is.
> [!NOTE]
> Ha a hibakeresés során nem szeretne telemetriát küldeni a portálra, adja hozzá az Application Insights SDK-t az alkalmazáshoz, de ne konfiguráljon erőforrást a portálon. A telemetria a hibakeresés során a Visual Studióban lesz megtekinthető. Később visszatérhet erre a konfigurációs oldalra, vagy megvárhatja az alkalmazás üzembe helyezését, és [bekapcsolhatja a telemetriát a futtatás során](./status-monitor-v2-overview.md).

## <a name="step-2-run-your-app"></a><a name="run"></a> 2. lépés: Az alkalmazás futtatása
Futtassa az alkalmazást az F5 billentyűvel. Nyisson meg több lapot, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja a naplózott események számát.

![A Visual Studio képernyőképe. Megjelenik az Application Insights gomb a hibakeresés alatt.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>3. lépés: A telemetria megtekintése
A telemetriát a Visual Studióban vagy az Application Insights webportálon tekintheti meg. A telemetria keresése a Visual Studióban segíti az alkalmazás hibakeresését. A teljesítményt és a használatot a webes portálon figyelheti, amikor a rendszer élesben működik. 

### <a name="see-your-telemetry-in-visual-studio"></a>Telemetria megtekintése a Visual Studióban

A Visual Studióban az Application Insights-adatok megtekintéséhez tegye a következőket.  Válassza **Solution Explorer**a  >  **csatlakoztatott szolgáltatások** megoldáskezelő > kattintson a jobb gombbal a **Application Insights**, majd az **élő telemetria keresése**elemre.

A Visual Studio Application Insights keresőablakában az alkalmazásából származó adatok között látni fogja az alkalmazás kiszolgálói oldalán létrejött telemetriai adatokat. Kísérletezzen a szűrőkkel, és kattintson valamely eseményre további részletek megtekintéséhez.

![A Data from debug session (Hibakeresési munkamenetből származó adatok) nézet képernyőképe az Application Insights ablakban.](./media/asp-net/55.png)

> [!Tip]
> Ha nem jelennek meg adatok, ellenőrizze, hogy megfelelő-e az időtartomány, majd kattintson a Search (Keresés) ikonra.

[További tudnivalók az Application Insights-eszközökről a Visual Studióban](./visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Telemetria megtekintése a webportálon

A telemetriát az Application Insights webportálon is megtekintheti (ha a telepítéskor nem csak az SDK-t telepítette). A portálon a Visual Studiónál több diagram, elemzőeszköz és összetevőket megjelenítő nézet található. A portál riasztásokat is biztosít.

Nyissa meg az Application Insights-erőforrást. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) és keresse meg ott, vagy válassza a **Solution Explorer** > **Connected Services** (Csatlakoztatott szolgáltatások) lehetőséget > kattintson a jobb gombbal az **Application Insights** > **Open Application Insights Portal** (Application Insights-portál megnyitása) elemre, amely odavezeti.

A portál az alkalmazásából származó telemetriai adatok nézetével nyílik meg.

![Az Application Insights áttekintési oldalának képernyőképe](./media/asp-net/007.png)

A portálon az egyik csempére vagy diagramra kattintva további részleteket tekinthet meg.

## <a name="step-4-publish-your-app"></a>4. lépés: Az alkalmazás közzététele
Tegye közzé alkalmazását az IIS-kiszolgálón vagy az Azure-on. Az [Élő mérőszámok streammel](./live-stream.md) ellenőrizheti, hogy minden rendben működik-e.

A telemetria a Application Insights portálon épül fel, ahol nyomon követheti a metrikákat, és megkeresheti a telemetria. A hatékony [Kusto lekérdezési nyelvet](/azure/kusto/query/) használhatja a használat és a teljesítmény elemzéséhez, vagy adott események megtalálásához is.

Továbbra is elemezheti a telemetria a [Visual Studióban](./visual-studio.md), olyan eszközökkel, mint a diagnosztikai keresés és a [trendek](./visual-studio-trends.md).

> [!NOTE]
> Ha az alkalmazása elég telemetriát küld a [szabályozási korlát](./pricing.md#limits-summary) eléréséhez, az automatikus [mintavételezés](./sampling.md) bekapcsol. A mintavételezés csökkenti az alkalmazásból küldött telemetria mennyiségét, míg a korrelatív adatokat diagnosztikai célból megőrzi.
>
>

## <a name="youre-all-set"></a><a name="land"></a>Készen áll

Gratulálunk! Telepítette az Application Insights csomagot az alkalmazásba, és konfigurálta, hogy telemetriát küldjön az Application Insights szolgáltatásba az Azure-ban.

Az alkalmazás telemetriáját fogadó Azure-erőforrást egy *kialakítási kulcs* azonosítja. A kulcs az ApplicationInsights.config fájlban található.


## <a name="upgrade-to-future-sdk-versions"></a>Frissítés a jövőbeli SDK-verziókra

* [Kibocsátási megjegyzések](./release-notes.md)

Az SDK új verziójára való frissítéshez nyissa meg a **NuGet csomagkezelő eszközt**, és szűrje a telepített csomagokat. Válassza a **Microsoft. ApplicationInsights. Web**lehetőséget, és válassza a **frissítés**lehetőséget.

Ha az ApplicationInsights.config fájlt testreszabta, mentse el egy példányát a frissítés előtt. Ezután egyesítse a módosításait az új verzióval.

## <a name="next-steps"></a>További lépések

További témaköröket is elolvashat, ha a következők érdeklik:

* [Webalkalmazás beállítása futási időben](./monitor-performance-live-website-now.md)
* [Azure Cloud Services](./cloudservices.md)

### <a name="more-telemetry"></a>További telemetria

* **[Böngésző és oldalbetöltési adatok](./javascript.md)** – Kódrészlet beszúrása a weboldalakra.
* **[Részletesebb függőség- és kivételfigyelés](./monitor-performance-live-website-now.md)** – Állapotfigyelő telepítése a kiszolgálón.
* A felhasználói műveletek számlálására, időpontjára vagy mérésére szolgáló **[Egyéni események kódolása](./api-custom-events-metrics.md)** .
* **[Naplóadatok lekérése](./asp-net-trace-logs.md)** – Naplóadatok összevetése a telemetriával.

### <a name="analysis"></a>Elemzés

* **[Az Application Insights használata a Visual Studióban](./visual-studio.md)**<br/>A telemetriával végzett hibakereséssel, diagnosztikai kereséssel és a kódig való részletezés lefúrással kapcsolatos információkat tartalmaz.
* **[Elemzés](../log-query/get-started-portal.md)** – Erőteljes lekérdezési nyelv.

### <a name="alerts"></a>Riasztások

* [Rendelkezésre állási tesztek](./monitor-web-app-availability.md): Hozzon létre teszteket, hogy megbizonyosodjon róla, oldala látható a weben.
* [Intelligens diagnosztika](./proactive-diagnostics.md): Ezek a tesztek automatikusan futnak, a beállításukhoz semmit sem kell tennie. Értesítést kap, ha az alkalmazásában szokatlanul magas a meghiúsult kérelmek száma.
* [Metrikai riasztások](../platform/alerts-log.md): riasztások beállítása, amely figyelmezteti, ha egy metrika átlépi a küszöbértéket. Az alkalmazás kódjába beépített egyedi metrikákhoz is állíthat be riasztásokat.

### <a name="automation"></a>Automation

* [Application Insights-erőforrások létrehozásának automatizálása](./powershell.md)


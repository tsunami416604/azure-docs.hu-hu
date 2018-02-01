---
title: "Futásidejű kivételek diagnosztizálása az Azure Application Insights használatával | Microsoft Docs"
description: "Oktatóanyag az alkalmazásában előforduló futásidejű kivételek észleléséhez és diagnosztizálásához az Azure Application Insights használatával."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 115611c5d4eeffb0f0600dd0a792ee9f80247e36
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Futásidejű kivételek észlelése és diagnosztizálása az Azure Application Insights segítségével

Az Azure Application Insights telemetriát gyűjt az alkalmazásából a futásidejű kivételek azonosításához és diagnosztizálásához.  Ez az oktatóanyag ismerteti a folyamatot az alkalmazására vonatkozóan.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A projekt módosítása a kivételek nyomon követésének engedélyezéséhez
> * Az alkalmazás különböző összetevőihez tartozó kivételek azonosítása
> * Egy kivétel részleteinek megtekintése
> * A kivételről készített pillanatfelvétel letöltése a Visual Studio alkalmazásba a hibakereséshez
> * A sikertelen kérelmek részleteinek elemzése a lekérdezési nyelv segítségével
> * Egy új munkaelem létrehozása a hibás kód kijavításához


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
- Töltse le és telepítse a [Visual Studio Snapshot Debugger](http://aka.ms/snapshotdebugger) alkalmazást.
- Engedélyezze a [Visual Studio Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) alkalmazást.
- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](app-insights-asp-net.md)-t. 
- Ez az oktatóanyag az alkalmazásban történt kivétel azonosításának módszerét ismerteti, ezért módosítsa a kódot a fejlesztési vagy a tesztelési környezetben, hogy létrehozzon egy kivételt. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.


## <a name="analyze-failures"></a>Hibák elemzése
Az Application Insights összegyűjti az alkalmazásában felmerült hibákat, és szemlélteti a gyakoriságukat a különböző műveleteknél, hogy Ön a legnagyobb problémát okozó hibákra koncentrálhasson.  Ezután részletesen elemezheti a hibákat, hogy megtalálja a probléma okát.   

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.  
2. A **Hibák** panel megnyitásához válassza a **Hibák** elemet a **Vizsgálat** menüben, vagy kattintson a **Sikertelen kérelmek** gráfra.

    ![Sikertelen kérelmek](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

3. A **Sikertelen kérelmek** panel megmutatja a sikertelen kérelmek és az érintett felhasználók számát, az alkalmazás minden egyes műveletére vonatkozóan.  Ha ezeket az információkat felhasználók szerint rendezi, megtalálhatja azokat a hibákat, amelyek a legnagyobb hatással vannak a felhasználókra.  Ebben a példában a **GET Employees/Create** és a **GET Customers/Details** elemeket érdemes megvizsgálni a hibák és az érintett felhasználók nagy száma miatt.  Egy művelet kiválasztásával további információkat tekinthet meg a műveletről a jobb oldali panelen.

    ![Sikertelen kérelmek panel](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

4. Az időtartomány csökkentésével ráközelíthet arra az időszakra, amelyben a hibák száma kiugróan magas.

    ![Sikertelen kérelmek ablak](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

5. Kattintson a **Részletek megtekintése** elemre a művelet részleteinek megtekintéséhez.  Itt talál egy Gantt-diagramot is két olyan sikertelen függőséggel, amelyek befejezéséhez majdnem fél másodperc kellett összesen.  Többet megtudhat a teljesítménybeli problémák elemzéséről, ha elolvassa a [Teljesítménybeli problémák észlelése és diagnosztizálása az Application Insights segítségével](app-insights-tutorial-performance.md) című oktatóanyagot.

    ![Sikertelen kérelmek részletei](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

6. A művelet részleteiben egy FormatException elem is látható, amely valószínűleg a hibát okozta.  Kattintson a kivételre vagy a **3 leggyakoribb kivételtípus** elemre a részletek megtekintéséhez.  Láthatja, hogy az ok egy érvénytelen irányítószám volt.

    ![Kivétel részletei](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)

> [!NOTE]
Engedélyezze az „Egyesített részletek: Végpontok közötti tranzakció diagnosztikája” [előnézeti felület](app-insights-previews.md) elemet, hogy az összes kapcsolódó kiszolgálóoldali telemetriát, például a kéréseket, a függőségeket, a kivételeket, a nyomokat, az eseményeket stb. egyetlen teljes képernyős nézetben tekinthesse meg. 

Ha az előnézet engedélyezve van, egy egységes felületen tekintheti meg a függőségi hívásokkal eltöltött idő mennyiségét, valamint az egyesített élményben előforduló hibákat és kivételeket. A több összetevőt érintő tranzakciók esetében a Gantt-diagram és a részletek panel segítenek, hogy gyorsan megtalálja a problémát okozó összetevőt, függőséget vagy kivételt. Az alsó szakaszt kibontva megtekintheti a kiválasztott összetevő-művelethez összegyűjtött nyomok vagy események időrendjét. [További információk az új felületről](app-insights-transaction-diagnostics.md)  

![Tranzakció diagnosztikája](media/app-insights-tutorial-runtime-exceptions/e2e-transaction-preview.png)

## <a name="identify-failing-code"></a>Sikertelen kód azonosítása
A Snapshot Debugger az alkalmazásában leggyakrabban előforduló kivételekről gyűjt pillanatfelvételeket, hogy segítsen éles környezetben diagnosztizálni azok alapvető okát.  A portálon a hibakeresési pillanatfelvételeket megtekintve láthatja a hívásvermet és megvizsgálhatja a változókat az egyes hívásveremkeretekre vonatkozóan. Ezután a pillanatfelvételt letöltve és Visual Studio 2017 alkalmazásban megnyitva hibakeresést végezhet a forráskódon.

1. A kivétel tulajdonságaiban kattintson a **Hibakeresési pillanatfelvétel megnyitása** elemre.
2. A **Hibakeresési pillanatfelvétel** panel a kérés hívásvermével nyílik meg.  Az egyes metódusokra kattintva megtekintheti az összes helyi változónak a kérés időpontjában rögzített értékeit.  Ebben a példában a legfelső metódustól kezdve olyan változókat láthatunk, amelyeknek nincs értéke.

    ![Hibakeresési pillanatkép](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. A **ValidZipCode** az első hívás, amely érvényes értékkel rendelkezik, és láthatjuk, hogy egy olyan, betűkkel megadott irányítószámot kaptunk, amelyet nem lehet egész számra fordítani.  Úgy tűnik, ez az a hiba, amelyet ki kell javítani.

    ![Hibakeresési pillanatkép](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Ahhoz, hogy letöltse ezt a pillanatfelvételt a Visual Studióba, ahol megkereshetjük a javításra szoruló kódot, kattintson a **Pillanatfelvétel letöltése** elemre.
6. A rendszer betölti a pillanatfelvételt a Visual Studióba.
7. Most már elindíthat egy hibakeresési munkamenetet, amely gyorsan azonosítja a kivételt okozó kódsort.

    ![Kivétel a kódban](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Elemzési adatok használata
Az Application Insights által gyűjtött minden adatot az Azure Log Analytics tárol, amely egy részletes lekérdezési nyelvet biztosít, lehetővé téve az adatok különféle módon történő elemzését.  Arra használhatjuk ezeket az adatokat, hogy elemezzük azokat a kéréseket, amelyek a vizsgált kivételt létrehozták. 

8. Kattintson a CodeLens információra a kód felett az Application Insights által biztosított telemetria megtekintéséhez.

    ![Kód](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Kattintson a **Hatás elemzése** elemre az Application Insights Analytics megnyitásához.  Több lekérdezés is található itt, amelyek részleteket biztosítanak a sikertelen kérésekről, például az érintett felhasználókról, böngészőkről és régiókról.<br><br>![Elemzés](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Munkaelem hozzáadása
Ha az Application Insights alkalmazást egy követőrendszerhez csatlakoztatja, például a Visual Studio Team Serviceshez vagy a GitHubhoz, létrehozhat egy munkaelemet közvetlenül az Application Insightsból.

1. Térjen vissza a **Kivétel tulajdonságai** panelhez az Application Insightsban.
2. Kattintson az **Új munkaelem** elemre.
3. A megnyíló **Új munkaelem** panelen automatikusan megjelennek a kivétel részletei.  Bármilyen egyéb információt hozzáadhat mentés előtt.

    ![Új munkaelem](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>További lépések
Most már megtanulta, hogyan azonosíthatja a futásidejű kivételeket. Térjen át a következő oktatóanyagra, hogy megtanulja, hogyan azonosíthatja és diagnosztizálhatja a teljesítménybeli problémákat.

> [!div class="nextstepaction"]
> [Teljesítménybeli problémák azonosítása](app-insights-tutorial-performance.md)
---
title: "Futásidejű kivételek Azure Application Insights segítségével diagnosztizálhatja |} Microsoft Docs"
description: "Az oktatóanyagban található, és diagnosztizálhatja az alkalmazás használatával Azure Application Insights futásidejű kivételek."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 11e0f2f19acc843f1c558b5d0cfe84291035a6a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Keresse meg és diagnosztizálhatja a futásidejű kivételek az Azure Application insights szolgáltatással

Azure Application Insights telemetria gyűjti az azonosításához, és diagnosztizálhatja a futásidejű kivételek az alkalmazásból.  Ez az oktatóanyag végigvezeti a folyamat az alkalmazással.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Módosítsa a projekt kivétel követése engedélyezése
> * Az alkalmazás különböző összetevők kivételek azonosítása
> * Egy kivétel részleteinek megtekintése
> * A kivétel Pillanatfelvétel letöltése a Visual Studio hibakeresési
> * Részleteket a sikertelen kérelmek lekérdezési nyelv használatával
> * Hozzon létre egy új munkaelemet javítsa ki a hibás kódot


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
- Töltse le és telepítse a [Visual Studio pillanatkép hibakereső](http://aka.ms/snapshotdebugger).
- Engedélyezése [Visual Studio hibakereső pillanatképet készíteni.](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-snapshot-debugger)
- Az Azure-bA egy .NET-alkalmazás központi telepítése és [engedélyezze az Application Insights SDK](app-insights-asp-net.md). 
- Az oktatóanyag azt követi nyomon az alkalmazásban kivétel azonosítása, így módosíthatja a fejlesztési vagy tesztelési környezetben kivétel létrehozásához a kódot. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Hibák elemzése
Az Application Insights gyűjti az esetleges hibákat az alkalmazásban, és megtekintheti az gyakoriságát segítséget nyújtanak a próbálkozások összpontosítani a legnagyobb mértékben rendelkező különböző műveletek között.  Majd részletezve részletei ilyen hibához okának azonosításához.   

1. Válassza ki **Application Insights** és az előfizetés majd.  
1. Megnyitásához a **hibák** panelen válassza ki **hibák** alatt a **vizsgálat** menüből, vagy kattintson a **sikertelen kérelmek** grafikon.

    ![Sikertelen kérelmek](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

2. A **sikertelen kérelmek** panel jeleníti meg. a sikertelen kérelmek és az érintett felhasználók számát mutatja az alkalmazás minden művelethez.  A felhasználó által az adatok rendezése azonosíthatja azokat, hogy a legtöbb felhasználóit érintő hibák.  Ebben a példában a **beolvasása az alkalmazottak/létrehozása** és **beolvasása ügyfelek/részletek** valószínűleg-zel vizsgálja meg a hibákat és azokat az érintett felhasználók nagy száma miatt.  Egy művelet kiválasztása információkat jeleníti meg további ezt a műveletet a jobb oldali panelen.

    ![Sikertelen kérelmek panel](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

3. Csökkentse a időszak időszak nagyítása, amely a hibaaránya mutatja egy csúcs.

    ![Sikertelen kérelmek ablak](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

4. Kattintson a **részleteinek megtekintése** a művelet a részletek megtekintéséhez.  Ez magában foglalja a Gantt két sikertelen függőség végrehajtani egy második felében szinte együttesen hozó megjelenítő diagram.  További információk a teljesítménnyel kapcsolatos problémák elemzése az oktatóanyag végrehajtásával található [található, és az Azure Application insights szolgáltatással a teljesítménnyel kapcsolatos problémák diagnosztizálásához](app-insights-tutorial-performance.md).

    ![Sikertelen kérelmek részletei](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

5. A műveletek részletei, megjelenítheti a FormatException megjelenő okozta a hibát.  Kattintson a kivétel vagy a **felső 3 kivétel típusok** számát, hogy a részletek megtekintéséhez.  Láthatja, hogy a rendszer egy érvénytelen irányítószám miatt.

    ![A kivétel részletei](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)



## <a name="identify-failing-code"></a>Kód hiányában azonosítása
A pillanatkép-hibakereső gyűjti a pillanatképek készítése a leggyakoribb kivételeket az alapvető ok éles környezetben azonosításában segít az alkalmazásban.  A portálon, hogy a hívási verem, és vizsgálja meg a változókat, minden egyes Hívásiverem-keret hibakeresési pillanatképek tekintheti meg. A forráskód Ha letölti a pillanatképet, és nyissa meg a Visual Studio 2017 majd hibakeresési.

1. Kattintson a tulajdonságok a kivétel **nyitott hibakeresési pillanatkép**.
2. A **Debug pillanatkép** panel nyílik meg a kérelem a hívási veremben.  Kattintson bármelyik módszert az összes helyi változók értékeinek megtekintése a kérés idején.  Ebben a példában a felső metódus-től kezdődő láthatja helyi változók érték nélküli.

    ![Pillanatkép hibakeresése](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. Az első hívás, amely rendelkezik az érvényes értékek **ValidZipCode**, és láthatja, hogy a zip-kód, amely nem fordítható le egy egész számot tudni meghajtóbetűjellel rendelkező lett megadva.  Ez valószínűleg a kódot, amely ki kell javítani a hibát.

    ![Pillanatkép hibakeresése](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. A pillanatkép letöltése a Visual Studio, ahol azt keresheti meg a tényleges kód, amely ki kell javítani a **letöltése pillanatkép**.
6. A pillanatkép betöltődik, a Visual Studio.
7. A Visual Studio, amely gyorsan azonosítja a kivételt okozó kódsort most futtathatja a hibakeresési munkamenetben.

    ![Kivétel fordult elő a kódot](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Analitikai adatok használata
Az Application Insights által gyűjtött összes adat Azure Naplóelemzés, amely egy részletes lekérdező nyelv, amely lehetővé teszi az adatok elemzése az sokféleképpen biztosít tárolja.  Az adatokat a kérelmeket, azt még tart a kivételt okozó elemzéséhez használhatja azt. 

8. Kattintson a CodeLens információk megtekintése az Application Insights által biztosított telemetriai kód fent.

    ![Kód](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Kattintson a **hatás elemzése** Application Insights Analytics megnyitásához.  A telepítéskor több lekérdezések sikertelen kérések, például az érintett felhasználók, a böngésző támogatja, és a régiók részletekkel szolgálnak.<br><br>![Elemzés](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Munkaelem hozzáadása
Ha például a Visual Studio Team Services, GitHub vagy a követési rendszerünk Application Insights csatlakozni, a munkaelem közvetlenül az Application Insights is létrehozhat.

1. Lépjen vissza a **kivétel tulajdonságok** az Application Insightsban panel.
2. Kattintson a **új munkaelem**.
3. A **új munkaelem** panel nyílik meg a már fel kivételhiba adatait.  Hozzáadhat további adatok mentése előtt.

    ![Új munkaelem](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a futásidejű kivételek azonosítása, hogy értékről a következő oktatóanyag áttekintésével megismerheti, hogyan azonosíthatja és teljesítménnyel kapcsolatos problémák diagnosztizálásához.

> [!div class="nextstepaction"]
> [Teljesítménnyel kapcsolatos problémákat azonosítása](app-insights-tutorial-performance.md)
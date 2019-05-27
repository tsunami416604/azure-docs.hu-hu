---
title: Az Azure Application Insights gyors üzembe helyezése | Microsoft Docs
description: Útmutatás az gyorsan beállíthat az ASP.NET Core-webalkalmazás figyelés az Application insights segítségével
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/21/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: e6afd342a27fa13ae79e037e6e42e582dd9856cc
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015292"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Az ASP.NET Core-webalkalmazás monitorozásának indítása

Az Azure Application Insights segítségével egyszerűen monitorozhatja webalkalmazása rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat. 

Ez a rövid útmutató végigvezeti az Application Insights SDK hozzáadása egy meglévő ASP.NET Core-webalkalmazás. Konfigurálásáról további információt az Application Insights a Visual Studio kivétel nélkül ez [cikk](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

- [Telepítse a Visual Studio 2019](https://www.visualstudio.com/downloads/) a következő számítási feladatokkal:
  - ASP.NET és webfejlesztés
  - Azure-fejlesztés
- [A .NET Core 2.0 SDK telepítése](https://www.microsoft.com/net/core)
- Szüksége lesz egy Azure-előfizetésre és egy meglévő .NET Core-webalkalmazásra.

Ha nem rendelkezik egy ASP.NET Core-webalkalmazás, használhatja a részletes útmutatóban [ASP.NET Core-alkalmazás létrehozása és az Application Insights hozzáadása.](../../azure-monitor/app/asp-net-core.md)

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az Application Insights bármely, az internethez csatlakozó alkalmazásról képes telemetriaadatokat gyűjteni, függetlenül attól, hogy az a helyszínen vagy a felhőben fut-e. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Válassza az **Erőforrás létrehozása** > **Fejlesztői eszközök** > **Application Insights** elemet.

   ![Application Insights-erőforrások hozzáadása](./media/dotnetcore-quick-start/1createresourceappinsight.png)

    Megjelenik egy konfigurációs mező. Az adatbeviteli mezők kitöltéséhez használja az alábbi táblát.

    | Beállítások        |  Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name (Név)**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Alkalmazás típusa** | ASP.NET-es webalkalmazás | A figyelt alkalmazás típusa |
   | **Erőforráscsoport**     | myResourceGroup      | Az App Insights-adatokat futtató új erőforráscsoport neve |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

2. Kattintson a **Create** (Létrehozás) gombra.

## <a name="configure-app-insights-sdk"></a>Az App Insights SDK konfigurálása

1. Nyissa meg az ASP.NET Core-webalkalmazás **projektet** a Visual Studióban, majd kattintson a jobb gombbal az alkalmazás nevére a **Megoldáskezelőben**, és válassza a **Hozzáadás** > **Application Insights Telemetria** elemet.

    ![Application Insights Telemetria hozzáadása](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Kattintson a **Ismerkedés** gomb

3. Válassza ki a fiókot és előfizetést > Válassza ki a **meglévő erőforrást** az Azure Portalon létrehozott > kattintson **regisztrálása**.

4. Az alkalmazás indításához válassza a **Hibakeresés** > **Indítás hibakeresés nélkül** (Ctrl+F5) elemet

    ![Az Application Insights áttekintése menü](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> 3–5 percet is igénybe vehet, mire az adatok elkezdenek megjelenni a portálon. Ha az alkalmazás egy alacsony forgalmú tesztalkalmazás, vegye figyelembe, hogy a legtöbb metrika rögzítése csak akkor történik, ha aktív kérések és műveletek vannak folyamatban.

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Ismét az Application Insights **áttekintése** lapon válassza az Azure Portalon **kezdőlap** és a legutóbbi erőforrások válassza ki a korábban, a jelenleg futó részleteinek a megtekintéséhez létrehozott erőforrás az alkalmazás.

   ![Az Application Insights áttekintése menü](./media/dotnetcore-quick-start/4overview.png)

2. Kattintson az **Alkalmazástérkép** elemre az alkalmazás-összetevők függőségi viszonyait mutató vizuális elrendezés megjelenítéséhez. Minden egyes összetevőnél megjelennek a KPI-k, például a terhelés, a teljesítmény, a hibák és a riasztások.

   ![Alkalmazástérkép](./media/dotnetcore-quick-start/5appmap.png)

3. Kattintson a **Alkalmazáselemzés** ikon ![Alkalmazáselemzés ikon](./media/dotnetcore-quick-start/006.png) **megtekintés az Analyticsben**. Megnyílik az **Application Insights Analytics**, amely egy részletes lekérdezési nyelvet biztosít az Application Insights által gyűjtött adatok elemzéséhez. Esetünkben most egy lekérdezés jön létre, amely a kérések számát egy diagramon jeleníti meg. A további adatok elemzéséhez írhat saját lekérdezéseket is.

   ![Az adott időtartamon belüli felhasználói kéréseket mutató elemzési diagram](./media/dotnetcore-quick-start/6analytics.png)

4. Lépjen vissza a **áttekintése** lapon és a KPI-irányítópultok részletesebb vizsgálatához.  Ezen az irányítópulton az alkalmazás állapotával kapcsolatos statisztikák jelennek meg, köztük a bejövő kérések száma, az egyes kérések időtartama, valamint az esetleges hibák. 

   ![Az Állapotáttekintési időegyenes diagramjai](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Kattintson a bal oldali **metrikák**. A metrikaböngésző használatával állapotának és az Erőforrás kihasználtsága vizsgálata. Az **Új diagram hozzáadása** gombra kattintva további egyéni nézeteket adhat hozzá, a **Szerkesztés** gombra kattintva pedig módosíthatja a meglévő diagramok típusát, magasságát, színpalettáját, csoportosításait és metrikáit. Például hogy megjelenítő diagram az átlagos böngészőlap-betöltési ideje kiválasztásával a "Böngésző lapbetöltési idő" a metrikák legördülő lista és a "Átlagos", összesítési. További információ az Azure Metrikaböngésző látogasson el [Ismerkedés az Azure Metrikaböngésző](../../azure-monitor/platform/metrics-getting-started.md).

     ![Metrikák lap: Diagram átlagos böngésző oldal betöltése](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Videó

- Külső részletes videó [Application Insights konfigurálása a .NET Core és a Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) sablon nélkül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Amikor végzett tesztelése, törölheti az erőforráscsoportot, és az összes kapcsolódó erőforrás. Ezért kövesse az alábbi lépéseket.

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a **myResourceGroup** elemre.
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Futásidejű kivételek észlelése és diagnosztizálása](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)

---
title: Rövid útmutató ASP.NET Core - Azure Monitor Application Insights
description: Utasításokat tartalmaz egy ASP.NET Core Web App gyors beállításához az Azure Monitor Application Insights segítségével történő figyeléshez
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ef46b86186d1f5e26360de891b3a090ab0ece66b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78894812"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Az ASP.NET Core-webalkalmazás monitorozásának indítása

Az Azure Application Insights segítségével egyszerűen monitorozhatja webalkalmazása rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat. 

Ez a rövid útmutató végigvezeti az Application Insights SDK hozzáadása egy meglévő ASP.NET Core webalkalmazás. Az Application Insights Visual Studio nélkül történő konfigurálásáról a [cikk](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)ben olvashat.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- [Telepítse a Visual Studio 2019-et](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) az alábbi munkaterhelésekkel:
  - ASP.NET és webfejlesztés
  - Azure-fejlesztés
- [A .NET Core 2.0 SDK telepítése](https://dotnet.microsoft.com/download)
- Szüksége lesz egy Azure-előfizetésre és egy meglévő .NET Core-webalkalmazásra.

Ha nem rendelkezik ASP.NET Core webalkalmazással, lépésről lépésre útmutatónk segítségével [létrehozhat egy ASP.NET Core alkalmazást, és hozzáadhat alkalmazáselemzési adatokat.](../../azure-monitor/app/asp-net-core.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az Application Insights bármely, az internethez csatlakozó alkalmazásról képes telemetriaadatokat gyűjteni, függetlenül attól, hogy az a helyszínen vagy a felhőben fut-e. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Válassza az > **Erőforrás-fejlesztői eszközök** >  **létrehozása****Alkalmazáselemzési**lehetőséget.

   > [!NOTE]
   >Ha ez az első alkalom, hogy egy Application Insights-erőforrást hoz létre, további információkat kaphat az [Application Insights-erőforrás létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) doc ellátogatva.

    Megjelenik egy konfigurációs mező. Az adatbeviteli mezők kitöltéséhez használja az alábbi táblát.

   | Beállítások        |  Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az App Insights-adatok üzemeltetéséhez az új erőforráscsoport neve. Létrehozhat új erőforráscsoportot, vagy használhat egy meglévőt. |
   | **Helyen** | USA keleti régiója | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

2. Kattintson **a Létrehozás gombra.**



## <a name="configure-app-insights-sdk"></a>Az App Insights SDK konfigurálása

1. Nyissa meg az ASP.NET Core-webalkalmazás **projektet** a Visual Studióban, majd kattintson a jobb gombbal az alkalmazás nevére a **Megoldáskezelőben**, és válassza a **Hozzáadás** > **Application Insights Telemetria** elemet.

    ![Application Insights Telemetria hozzáadása](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Kattintson az **Első lépések** gombra

3. Válassza ki fiókját és előfizetését > Válassza ki az Azure Portalon létrehozott **meglévő erőforrást,** > kattintson a **Regisztráció gombra.**

4. Válassza a **Project** > **Manage NuGet Packages** > **Package source: nuget.org** > **Frissítse** az Application Insights SDK-csomagokat a legújabb stabil kiadásra.

5. Az alkalmazás elindításához válassza a **Hibakeresés** > **indítása hibakeresés nélkül** lehetőséget (Ctrl+F5)

    ![Az Application Insights áttekintése menü](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> 3–5 percet is igénybe vehet, mire az adatok elkezdenek megjelenni a portálon. Ha az alkalmazás egy alacsony forgalmú tesztalkalmazás, vegye figyelembe, hogy a legtöbb metrika rögzítése csak akkor történik, ha aktív kérések és műveletek vannak folyamatban.

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Nyissa meg újra az Application Insights **Áttekintés** lapot az Azure Portalon a **Kezdőlap** lehetőséget választva, és a legutóbbi erőforrások alatt válassza ki a korábban létrehozott erőforrást az éppen futó alkalmazás részleteinek megtekintéséhez.

   ![Az Application Insights áttekintése menü](./media/dotnetcore-quick-start/4overview.png)

2. Kattintson az **Alkalmazástérkép** elemre az alkalmazás-összetevők függőségi viszonyait mutató vizuális elrendezés megjelenítéséhez. Minden egyes összetevőnél megjelennek a KPI-k, például a terhelés, a teljesítmény, a hibák és a riasztások.

   ![Alkalmazástérkép](./media/dotnetcore-quick-start/5appmap.png)

3. Kattintson az **App Analytics** ikonra ![Application Map](./media/dotnetcore-quick-start/006.png) ikonra Nézet az **Analytics-ben**. Megnyílik az **Application Insights Analytics**, amely egy részletes lekérdezési nyelvet biztosít az Application Insights által gyűjtött adatok elemzéséhez. Esetünkben most egy lekérdezés jön létre, amely a kérések számát egy diagramon jeleníti meg. A további adatok elemzéséhez írhat saját lekérdezéseket is.

   ![Az adott időtartamon belüli felhasználói kéréseket mutató elemzési diagram](./media/dotnetcore-quick-start/6analytics.png)

4. Térjen vissza az **Áttekintés lapra,** és vizsgálja meg a Főteljesítménymutató-irányítópultokat.  Ezen az irányítópulton az alkalmazás állapotával kapcsolatos statisztikák jelennek meg, köztük a bejövő kérések száma, az egyes kérések időtartama, valamint az esetleges hibák. 

   ![Az Állapotáttekintési időegyenes diagramjai](./media/dotnetcore-quick-start/7kpidashboards.png)

5. A bal oldalon kattintson a **Metrikák**. A metrikakezelő segítségével vizsgálja meg az erőforrás állapotát és kihasználtságát. Az **Új diagram hozzáadása** gombra kattintva további egyéni nézeteket adhat hozzá, a **Szerkesztés** gombra kattintva pedig módosíthatja a meglévő diagramok típusát, magasságát, színpalettáját, csoportosításait és metrikáit. Létrehozhat például egy olyan diagramot, amely a böngésző oldalának átlagos betöltési idejét jeleníti meg, ha a "Böngészőoldal betöltési idejét" választja ki a legördülő számokból és az "Avg" értéket az összesítésből. Ha többet szeretne megtudni az Azure Metrics Explorer ről, látogasson el [az Azure Metrics Explorer – első lépések](../../azure-monitor/platform/metrics-getting-started.md).

     ![Mérőszámok lap: A böngésző oldalának átlagos betöltési idődiagramja](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Videó

- Az [Application Insights .NET Core és Visual Studio szolgáltatással való konfigurálásáról](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) szóló, részletes videó.
- Az [Application Insights .NET Core és Visual Studio-kóddal való konfigurálásáról](https://youtu.be/ygGt84GDync) szóló, részletes videó a semmiből.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha végzett a teszteléssel, törölheti az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez kövesse az alábbi lépéseket.

> [!NOTE]
> Ha egy meglévő erőforráscsoportot használt, az alábbi utasítások nem fognak működni, és csak törölnie kell az egyes Application Insights-erőforrást. Ne feledje, hogy bármikor töröl egy erőforráscsoportot, minden alászbontó erőforrás, amely a csoport tagja, törlődik.

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a **myResourceGroup** elemre.
2. Az erőforráscsoport lapján kattintson a **Törlés**gombra, írja be a **myResourceGroup** parancsot a szövegmezőbe, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Futásidejű kivételek észlelése és diagnosztizálása](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)

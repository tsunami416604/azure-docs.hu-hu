---
title: Teljesítménybeli problémák diagnosztizálása az Azure Application Insights használatával | Microsoft Docs
description: Oktatóanyag az alkalmazásában előforduló teljesítménybeli problémák észleléséhez és diagnosztizálásához az Azure Application Insights használatával.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/13/2019
ms.custom: mvc
ms.openlocfilehash: 98d7c1552a7b1f2b02ae4df1cad24e20f7ac76e1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239594"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Teljesítménybeli problémák észlelése és diagnosztizálása az Application Insights segítségével

Az Azure Application Insights telemetriát gyűjt az alkalmazásából a teljesítmény és a működés elemzésének segítése érdekében.  Ezt az információt arra használhatja, hogy azonosítsa a potenciális problémákat, vagy hogy megtalálja azokat a fejlesztési lehetőségeket az alkalmazásban, amelyek a legnagyobb hatással lennének a felhasználókra.  Ez az oktatóanyag az alkalmazás kiszolgáló-összetevőinek, valamint az ügyfél teljesítményének elemzési módszereit ismerteti.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kiszolgálóoldali műveletek teljesítményének azonosítása
> * Kiszolgálói műveletek elemzése a lassú teljesítmény okának megállapítása érdekében
> * A leglassabb ügyféloldali művelet azonosítása
> * Az oldalmegtekintések részleteinek elemzése a lekérdezési nyelv segítségével


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2019-et](https://www.visualstudio.com/downloads/) az alábbi munkaterhelésekkel:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../../azure-monitor/app/asp-net.md)-t.
- [Engedélyezze az Application Insights profilkészítőt](../../azure-monitor/app/profiler.md#installation) az alkalmazásához.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a rendszerbe.

## <a name="identify-slow-server-operations"></a>Lassú kiszolgálói műveletek azonosítása
Az Application Insights teljesítményadatokat gyűjt az alkalmazás különböző műveleteiről. A leghosszabb időtartamú műveletek azonosításával diagnosztizálhatja a potenciális problémákat vagy hatékonyabbá teheti a folyamatban lévő fejlesztéseit, ezáltal pedig javíthat az alkalmazás általános teljesítményén.

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.  
1. A **Teljesítmény** panel megnyitásához válassza a **Teljesítmény** elemet a **Vizsgálat** menüben, vagy kattintson a **Kiszolgáló válaszideje** gráfra.

    ![Teljesítmény](media/tutorial-performance/1-overview.png)

2. A **Teljesítmény** panel megjeleníti az alkalmazás egyes műveleteinek számát és átlagos időtartamát.  Ezt az információt arra használhatja, hogy azonosítsa azokat a műveleteket, amelyek a legnagyobb hatással vannak a felhasználókra. Ebben a példában a **GET Customers/Details** és a **GET Home/Index** elemeket érdemes megvizsgálni a viszonylagosan hosszú időtartamuk és a hívások magas száma miatt.  Más műveleteknek hosszabb időtartamuk lehet, viszont ritkán kaptak hívást, ezért a javításuk minimális hatást eredményezne.  

    ![Teljesítménykiszolgáló panel](media/tutorial-performance/2-server-operations.png)

3. Ez a grafikon jelenleg a kijelölt műveletek átlagos időtartamát mutatja. A teljesítményproblémákat a 95. percentilisre váltva találhatja meg. Adja hozzá azokat a műveleteket, amelyekre kíváncsi. Ehhez rögzítse őket a gráfhoz.  Itt látszik, hogy van néhány csúcsérték, amelyeket érdemes megvizsgálni.  Tovább szűkítheti az eredmények körét, ha csökkenti a gráf időkeretét.

    ![Műveletek rögzítése](media/tutorial-performance/3-server-operations-95th.png)

4.  A jobb oldali teljesítmény panelen a kiválasztott művelet különböző kéréseihez tartozó időtartamok eloszlását láthatja.  Szűkítse a keretet úgy, hogy a 95. percentilisnél kezdődjön. A „3 legnagyobb függőség” megállapításkártya egyszerűen megmutatja, hogy a külső függőségek valószínűleg hozzájárulnak a tranzakciók lassúságához.  Kattintson a minták számát tartalmazó gombra a minták listájának megtekintéséhez. A listáról kiválaszthatja bármelyik mintát, és megtekintheti a tranzakciók részleteit.

5.  Első ránézésre is megállapítható, hogy a Fabrikamaccount Azure-tábla a felelős a tranzakció teljes időtartamának legnagyobb részéért. Láthatja továbbá, hogy egy kivétel miatt meghiúsult. Ha a lista bármelyik elemére kattint, a jobb oldalon megjelennek az elem részletei. [További információ a tranzakciódiagnosztikai felületről](../../azure-monitor/app/transaction-diagnostics.md)

    ![A művelet végpontok utáni részletei](media/tutorial-performance/4-end-to-end.png)
    

6.  A **Profilkészítő** segít a kódszintű diagnosztikával való előrehaladásban azzal, hogy megmutatja a műveletnél futó kódot és az egyes lépésekhez szükséges időtartamot. Előfordulhat, hogy bizonyos műveletek nem hagynak nyomot, mivel a profilkészítő időszakosan fut.  Idővel több műveletnek lehet nyoma.  Kattintson a **Profilkészítői adatok** elemre a profilkészítő a műveleten való futtatásának elindításához.
5.  A nyom megjeleníti az egyes műveletekhez tartozó eseményeket, így diagnosztizálhatja a művelet időtartamáért felelős kiváltó okot.  Kattintson az egyik leghosszabb időtartamot megjelenítő példára.
6.  Kattintson a **Hot Path gombra** a művelet teljes időtartamához leginkább hozzájáruló események konkrét útvonalának kiemeléséhez.  Ebben a példában azt láthatja, hogy a leglassabb hívás a *FabrikamFiberAzureStorage.GetStorageTableData* metódusból származik. A legtöbb időt igénybe vevő metódus a *CloudTable.CreateIfNotExist*. Ha ez a kódsor lefut minden alkalommal, amikor a függvény meghívása megtörténik, az felesleges hálózati hívásokat és processzorhasználatot eredményez. A kód kijavításának legjobb módja az, hogy ha ezt a sort egy olyan indítási metódusba helyezi, amely csak egyszer fut le.

    ![Profilkészítő részletei](media/tutorial-performance/5-hot-path.png)

7.  A **Teljesítménnyel kapcsolatos tipp** a képernyő tetején megerősíti azt a következtetést, hogy a hosszú időtartamot a várakozás okozza.  Kattintson a **várakozás** hivatkozásra a különböző események értelmezéséről szóló dokumentáció megnyitásáért.

    ![Teljesítménnyel kapcsolatos tipp](media/tutorial-performance/6-perf-tip.png)

8.   További elemzéshez a **Nyomkövetés letöltéséhez** kattintson a Nyomkövetés letöltéséhez. Ezeket az adatokat a [PerfView segítségével](https://github.com/Microsoft/perfview#perfview-overview)tekintheti meg.

## <a name="use-logs-data-for-server"></a>Naplóadatok használata a kiszolgálóhoz
 A naplók gazdag lekérdezési nyelvet biztosít, amely lehetővé teszi az Application Insights által gyűjtött összes adat elemzését. Ezzel a funkcióval részletes elemzéseket végezhet a kérések és a teljesítmény adatairól.

1. Visszatérés a művelet részletei ![panelre, és kattintson a Naplók ikon](media/tutorial-performance/app-viewinlogs-icon.png)**nézete naplókban (Analytics) parancsra.**

2. A naplók a panel minden nézetére vonatkozó lekérdezéssel nyílnak meg.  Ezeket a lekérdezéseket futtathatja alapértelmezetten, vagy módosíthat rajtuk az igényei szerint.  Az első lekérdezés megjeleníti ennek a műveletnek a teljes időtartamát.

    ![naplók lekérdezés](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Lassú ügyfélműveletek azonosítása
Amellett, hogy azonosítja a kiszolgálói folyamatokat az optimalizálás érdekében, az Application Insights az ügyfélböngészők szempontjából is képes elemzést végezni.  Így megtalálhatja a javítási lehetőségeket az ügyfél összetevői számára, és különböző böngészők vagy helyek problémáit is megtalálhatja.

1. Válassza a **Böngésző** lehetőséget a **Vizsgálat csoportban,** majd kattintson a **Böngésző teljesítménye** gombra, vagy válassza a **Teljesítmény** lehetőséget a **Vizsgálat csoportban,** és váltson a **Böngésző** lapra a jobb felső sarokban található kiszolgáló/böngésző váltógombra kattintva a böngésző teljesítményösszegzésének megnyitásához. Ez egy vizuális összefoglalást nyújt az alkalmazása különböző telemetriáiról a böngésző szempontjából.

    ![Böngésző összefoglalása](media/tutorial-performance/8-browser.png)

2. Jelölje ki az egyik műveletnevet, majd kattintson a kék minták gombra a jobb alsó sarokban, és válasszon ki egy műveletet. Ez a végpontok között a tranzakció részleteit tartalmazza, és a jobb oldalon megtekintheti az **Oldalnézet tulajdonságai .** Ez lehetővé teszi az oldalt kérő ügyfél adatainak megtekintését, beleértve a böngésző típusát és helyét. Ez az információ segítheti abban, hogy megállapítsa, vannak-e az adott ügyféltípusokra jellemző teljesítményproblémák.

    ![Oldalmegtekintés](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Naplóadatok használata az ügyfélhez
A kiszolgáló teljesítményéhez gyűjtött adatokhoz hasonlóan az Application Insights is elérhetővé teszi az összes ügyféladatot a Naplók használatával végzett mélyelemzéshez.

1. Visszatérés a böngésző összegzéséhez,](media/tutorial-performance/app-viewinlogs-icon.png) és kattintson a Naplók ikon ![ **nézete naplókban (Analytics) parancsra.**

2. A naplók a panel minden nézetére vonatkozó lekérdezéssel nyílnak meg. Az első lekérdezés a különböző oldalmegtekintések időtartamát jeleníti meg az idő függvényében.

    ![Naplók lekérdezés](media/tutorial-performance/10-page-view-logs.png)

3.  Az intelligens diagnosztika a Naplók egyedi mintákat azonosít az adatokban. Ha rákattint az Intelligens Diagnosztika pontjára a vonaldiagramon, ugyanaz a lekérdezés fut le az anomáliát okozó rekordok nélkül. Ezeknek a rekordoknak a részletei a lekérdezés megjegyzéseket tartalmazó részében láthatók, így azonosíthatja azoknak az oldalmegtekintéseknek a tulajdonságait, amelyek a hosszú időtartamért felelősek.

    ![Naplók intelligens diagnosztikával](media/tutorial-performance/11-page-view-logs-dsmart.png)


## <a name="next-steps"></a>További lépések
Most már megtanulta, hogyan azonosíthatja a futásidejű kivételeket. Térjen át a következő oktatóanyagra, hogy megtanulja, hogyan hozhat létre riasztásokat a hibákhoz.

> [!div class="nextstepaction"]
> [Alkalmazásállapot-riasztás](../../azure-monitor/learn/tutorial-alert.md)

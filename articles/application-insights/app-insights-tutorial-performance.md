---
title: Teljesítménybeli problémák diagnosztizálása az Azure Application Insights használatával | Microsoft Docs
description: Oktatóanyag az alkalmazásában előforduló teljesítménybeli problémák észleléséhez és diagnosztizálásához az Azure Application Insights használatával.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: b870383634412c5b095738e2a7f79b9cf4dcf78a
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018211"
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

- Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../azure-monitor/app/asp-net.md)-t.
- [Engedélyezze az Application Insights profilkészítőt](../azure-monitor/app/profiler.md#installation) az alkalmazásához.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.

## <a name="identify-slow-server-operations"></a>Lassú kiszolgálói műveletek azonosítása
Az Application Insights teljesítményadatokat gyűjt az alkalmazás különböző műveleteiről. A leghosszabb időtartamú műveletek azonosításával diagnosztizálhatja a potenciális problémákat vagy hatékonyabbá teheti a folyamatban lévő fejlesztéseit, ezáltal pedig javíthat az alkalmazás általános teljesítményén.

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.  
1. A **Teljesítmény** panel megnyitásához válassza a **Teljesítmény** elemet a **Vizsgálat** menüben, vagy kattintson a **Kiszolgáló válaszideje** gráfra.

    ![Teljesítmény](media/app-insights-tutorial-performance/performance.png)

2. A **Teljesítmény** panel megjeleníti az alkalmazás egyes műveleteinek számát és átlagos időtartamát.  Ezt az információt arra használhatja, hogy azonosítsa azokat a műveleteket, amelyek a legnagyobb hatással vannak a felhasználókra. Ebben a példában a **GET Customers/Details** és a **GET Home/Index** elemeket érdemes megvizsgálni a viszonylagosan hosszú időtartamuk és a hívások magas száma miatt.  Más műveleteknek hosszabb időtartamuk lehet, viszont ritkán kaptak hívást, ezért a javításuk minimális hatást eredményezne.  

    ![Teljesítménypanel](media/app-insights-tutorial-performance/performance-blade.png)

3. Ez a grafikon jelenleg a kijelölt műveletek átlagos időtartamát mutatja. A teljesítményproblémákat a 95. percentilisre váltva találhatja meg. Adja hozzá azokat a műveleteket, amelyekre kíváncsi. Ehhez rögzítse őket a gráfhoz.  Itt látszik, hogy van néhány csúcsérték, amelyeket érdemes megvizsgálni.  Tovább szűkítheti az eredmények körét, ha csökkenti a gráf időkeretét.

    ![Műveletek rögzítése](media/app-insights-tutorial-performance/pin-operations.png)

4.  A jobb oldali teljesítmény panelen a kiválasztott művelet különböző kéréseihez tartozó időtartamok eloszlását láthatja.  Szűkítse a keretet úgy, hogy a 95. percentilisnél kezdődjön. A „3 legnagyobb függőség” megállapításkártya egyszerűen megmutatja, hogy a külső függőségek valószínűleg hozzájárulnak a tranzakciók lassúságához.  Kattintson a minták számát tartalmazó gombra a minták listájának megtekintéséhez. A listáról kiválaszthatja bármelyik mintát, és megtekintheti a tranzakciók részleteit.

    ![Időtartamok eloszlása](media/app-insights-tutorial-performance/duration-distribution.png)

5.  Első ránézésre is megállapítható, hogy a Fabrikamaccount Azure-tábla a felelős a tranzakció teljes időtartamának legnagyobb részéért. Láthatja továbbá, hogy egy kivétel miatt meghiúsult. Ha a lista bármelyik elemére kattint, a jobb oldalon megjelennek az elem részletei. [További információ a tranzakciódiagnosztikai felületről](../azure-monitor/app/transaction-diagnostics.md)

    ![Művelet részletei](media/app-insights-tutorial-performance/operation-details.png)
    

6.  A **Profilkészítő** segít a kódszintű diagnosztikával való előrehaladásban azzal, hogy megmutatja a műveletnél futó kódot és az egyes lépésekhez szükséges időtartamot. Előfordulhat, hogy bizonyos műveletek nem hagynak nyomot, mivel a profilkészítő időszakosan fut.  Idővel több műveletnek lehet nyoma.  Kattintson a **Profilkészítői adatok** elemre a profilkészítő a műveleten való futtatásának elindításához.
5.  A nyom megjeleníti az egyes műveletekhez tartozó eseményeket, így diagnosztizálhatja a művelet időtartamáért felelős kiváltó okot.  Kattintson az egyik leghosszabb időtartamot megjelenítő példára.
6.  Kattintson a **Működő elérési út megjelenítése** elemre azon események elérési útjának kiemeléséhez, amelyek a leginkább növelték a művelet teljes időtartamát.  Ebben a példában azt láthatja, hogy a leglassabb hívás a *FabrikamFiberAzureStorage.GetStorageTableData* metódusból származik. A legtöbb időt igénybe vevő metódus a *CloudTable.CreateIfNotExist*. Ha ez a kódsor lefut minden alkalommal, amikor a függvény meghívása megtörténik, az felesleges hálózati hívásokat és processzorhasználatot eredményez. A kód kijavításának legjobb módja az, hogy ha ezt a sort egy olyan indítási metódusba helyezi, amely csak egyszer fut le. 

    ![Profilkészítő részletei](media/app-insights-tutorial-performance/profiler-details.png)

7.  A **Teljesítménnyel kapcsolatos tipp** a képernyő tetején megerősíti azt a következtetést, hogy a hosszú időtartamot a várakozás okozza.  Kattintson a **várakozás** hivatkozásra a különböző események értelmezéséről szóló dokumentáció megnyitásáért.

    ![Teljesítménnyel kapcsolatos tipp](media/app-insights-tutorial-performance/performance-tip.png)

8.  További elemzésért kattintson az **.etl nyom letöltése** elemre, amellyel a nyomot letöltheti a Visual Studióba.

## <a name="use-analytics-data-for-server"></a>Elemzési adatok használata a kiszolgálóra vonatkozóan
Az Application Insights Analytics egy részletes lekérdezési nyelvet biztosít, amellyel minden, az Application Insights által gyűjtött adatot elemezhet.  Ezzel a funkcióval részletes elemzéseket végezhet a kérések és a teljesítmény adatairól.

1. Térjen vissza a művelet részleteinek paneljéhez, majd kattintson az Elemzés gombra.

    ![Elemzés gomb](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Az Application Insights Analytics megnyitásakor megjelenik egy lekérdezés a panelen látható nézetek mindegyikéhez.  Ezeket a lekérdezéseket futtathatja alapértelmezetten, vagy módosíthat rajtuk az igényei szerint.  Az első lekérdezés megjeleníti ennek a műveletnek a teljes időtartamát.

    ![Elemzés](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Lassú ügyfélműveletek azonosítása
Amellett, hogy azonosítja a kiszolgálói folyamatokat az optimalizálás érdekében, az Application Insights az ügyfélböngészők szempontjából is képes elemzést végezni.  Így megtalálhatja a javítási lehetőségeket az ügyfél összetevői számára, és különböző böngészők vagy helyek problémáit is megtalálhatja.

1. Válassza a **Böngésző** elemet a **Vizsgálat** menüpont alatt, hogy megnyissa a böngésző összegzését.  Ez egy vizuális összefoglalást nyújt az alkalmazása különböző telemetriáiról a böngésző szempontjából.

    ![Böngésző összefoglalása](media/app-insights-tutorial-performance/browser-summary.png)

2.  Görgessen le a **Melyek a leglassabb oldalaim?** területhez.  Itt egy listát találhat az alkalmazása azon oldalairól, amelyek betöltése a legtöbb időt vette igénybe az ügyfelek esetében.  Ezen információk használatával azokra az oldalakra összpontosíthat, amelyek a legnagyobb hatással vannak a felhasználóra.
3.  Kattintson az egyik oldalra az **Oldalmegtekintés** panel megnyitásához.  Ebben a példában a **/FabrikamProd** oldal túl hosszú átlagidőt mutat.  Az **Oldalmegtekintés** panel részleteket nyújt erről az oldalról, többek között a különböző időtartományok lebontását is tartalmazza.

    ![Oldalmegtekintés](media/app-insights-tutorial-performance/page-view.png)

4.  Kattintson a leghosszabb időtartamra a kérések részleteinek megvizsgálásához.  Ezután kattintson az adott kérésre, hogy megtekintse az oldalt kérő ügyféllel kapcsolatos részleteket, például a böngésző típusát és a helyét.  Ez az információ segítheti abban, hogy megállapítsa, vannak-e az adott ügyféltípusokra jellemző teljesítményproblémák.

    ![Lekérdezés részletei](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Elemzési adatok használata az ügyfélre vonatkozóan
A kiszolgálói teljesítményről gyűjtött adatokhoz hasonlóan az Application Insights az összes ügyféladatról is képes mélyreható elemzést biztosítani az Analytics segítségével.

1. Térjen vissza a böngésző összegzési területéhez, majd kattintson az Analytics ikonra.

    ![Elemzés ikon](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Az Application Insights Analytics megnyitásakor megjelenik egy lekérdezés a panelen látható nézetek mindegyikéhez. Az első lekérdezés a különböző oldalmegtekintések időtartamát jeleníti meg az idő függvényében.

    ![Elemzés](media/app-insights-tutorial-performance/client-analytics.png)

3.  Az Intelligens diagnosztika az Application Insights Analytics egyik funkciója, amely egyedi mintákat azonosít az adatokban.  Ha rákattint az Intelligens Diagnosztika pontjára a vonaldiagramon, ugyanaz a lekérdezés fut le az anomáliát okozó rekordok nélkül.  Ezeknek a rekordoknak a részletei a lekérdezés megjegyzéseket tartalmazó részében láthatók, így azonosíthatja azoknak az oldalmegtekintéseknek a tulajdonságait, amelyek a hosszú időtartamért felelősek.

    ![Intelligens diagnosztika](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>További lépések
Most már megtanulta, hogyan azonosíthatja a futásidejű kivételeket. Térjen át a következő oktatóanyagra, hogy megtanulja, hogyan hozhat létre riasztásokat a hibákhoz.

> [!div class="nextstepaction"]
> [Alkalmazásállapot-riasztás](app-insights-tutorial-alert.md)

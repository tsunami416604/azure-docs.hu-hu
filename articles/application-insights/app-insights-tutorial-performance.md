---
title: "Azure Application Insights segítségével teljesítmény eseményadatokat |} Microsoft Docs"
description: "Az oktatóanyagban található, és diagnosztizálhatja az alkalmazás használatával Azure Application Insights teljesítményével kapcsolatos problémákat."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 1176e6ac33db5b9428a323c3a6271818807afc72
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Keresse meg és teljesítmény eseményadatokat az Azure Application insights szolgáltatással

Azure Application Insights telemetria gyűjti össze az alkalmazás a művelet és teljesítményének elemzése érdekében.  Ez az információ is használhatja, azonosíthatja a problémákat, amely felmerülhet vagy az alkalmazáshoz, a hatás felhasználók nevezne továbbfejlesztési lehetőségeinek azonosítása érdekében.  Ez az oktatóanyag végigvezeti a kiszolgáló összetevőket egyaránt az alkalmazás és az ügyfél szempontjából teljesítményének elemzése során.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kiszolgálóoldali műveletek teljesítményének azonosítása
> * Kiszolgáló-műveletek teljesítménycsökkenést okának meghatározásához elemzése
> * A leghosszabb ügyféloldali műveletek azonosítása
> * Részleteket a lapmegtekintések lekérdezési nyelv használatával


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
- Az Azure-bA egy .NET-alkalmazás központi telepítése és [engedélyezze az Application Insights SDK](app-insights-asp-net.md).
- [Engedélyezze az Application Insights Profilkészítő](app-insights-profiler.md#enable-the-profiler) az alkalmazáshoz.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Azonosítsa a lassú kiszolgálói műveletek
Az Application Insights az alkalmazásban a különböző műveletek teljesítményadatok gyűjti.  Ezek a műveletek a leghosszabb időtartam azonosításával potenciális problémák diagnosztizálásához, vagy a legjobb cél a folyamatos fejlesztését, az alkalmazás teljesítményének növelése.

1. Válassza ki **Application Insights** és majd jelölje ki az előfizetését.  
1. Megnyitásához a **teljesítmény** panelen válassza ki **teljesítmény** alatt a **vizsgálat** menüből, vagy kattintson a **kiszolgáló válaszideje** diagramhoz .

    ![Teljesítmény](media/app-insights-tutorial-performance/performance.png)

2. A **teljesítmény** panelen látható az alkalmazás egyes műveletek számának és átlagos időtartama.  Ezek az információk segítségével azonosíthatja, hogy a legtöbb felhasználóit érintő műveletek. Ebben a példában a **beolvasása ügyfelek/részletek** és **GET otthoni/Index** valószínűleg-zel vizsgálja meg a viszonylag nagy időtartamát és a hívások száma miatt.  Más műveletek magasabb időtartama rendelkezhet, de ritkán nevezik, így azok fokozása hatásának minimális volna.  

    ![Teljesítmény panel](media/app-insights-tutorial-performance/performance-blade.png)

3. A diagram jelenleg megjeleníti az adott idő alatt az összes művelet átlagos időtartama.  Adja hozzá őket a diagramhoz rögzítési által éppen érdekelt műveletek.  Ez azt jelenti, hogy vannak-e bizonyos csúcsait érdemes vizsgál.  Különítse el a további a gráf időkerete csökkentésével.

    ![PIN-kód műveletek](media/app-insights-tutorial-performance/pin-operations.png)

4.  Kattintson a teljesítmény Vezérlőpult megtekintéséhez kattintson a jobb művelet. Ez a különböző kérelmek időtartamok eloszlását mutatja.  Felhasználók általában figyelje meg, csökkentheti a teljesítményt, körülbelül fél másodpercig, csökkentése érdekében az ablak kérelmekre 500 ezredmásodperc felett.  

    ![Időtartam terjesztési](media/app-insights-tutorial-performance/duration-distribution.png)

5.  Ebben a példában láthatja, hogy kérelmek jelentős számú keresztül egy második végzése feldolgozni. Ez a művelet részleteit láthatja kattintva **művelet részletei**.

    ![Művelet részletei](media/app-insights-tutorial-performance/operation-details.png)

6.  A korábban összegyűjtött információk csak eddig megerősíti, hogy lassú teljesítményre, akkor viszont igen kevés az alapvető OK gombra.  A **Profilkészítő** jelenít meg a tényleges kód, amely futtatta a művelet és az egyes lépéseihez szükséges idő ennek segítségével. Néhány művelet, mert a Profilkészítő rendszeres időközönként fut nincs nyomkövetés.  Adott idő alatt további műveleteket nyomkövetési adatokat kell rendelkeznie.  A művelet a Profilkészítő elindításához kattintson **szolgáltatásprofil-elemzői adat**.
5.  A nyomkövetés minden művelethez egyes eseményeket jeleníti meg, így felderítheti az alapvető ok a ideje alatt a teljes műveletet.  Kattintson a felső példák, amelyek a leghosszabb időtartam.
6.  Kattintson a **kiemelt elérési megjelenítése** jelölje ki a megadott elérési útja az eseményeket, amelyek a legtöbb hozzájárulnak a művelet időtartama.  Ebben a példában láthatja, hogy van-e a leglassabb hívás a *FabrikamFiberAzureStorage.GetStorageTableData* metódust. A legtöbb időt vesz igénybe, része a *CloudTable.CreateIfNotExist* metódust. Ez kódsort végrehajtja a rendszer minden alkalommal, amikor a függvény meghívása megtörténik, ha szükségtelen hálózati hívás- és CPU-erőforrást fog használni. Hárítsa el a kódot a legjobb módszer helyezése a sor néhány indítási metódus, amely csak az ilyen egyszer futnak. 

    ![Profilkészítő részletei](media/app-insights-tutorial-performance/profiler-details.png)

7.  A **teljesítmény tipp** a képernyő tetején támogatja az értékelés várakozási okozza-e a túlzott időtartama.  Kattintson a **Várakozás** hivatkozás dokumentációja a különböző típusú események értelmezése.

    ![Teljesítmény tipp](media/app-insights-tutorial-performance/performance-tip.png)

8.  További elemzés céljából, rákattinthat **töltse le az .etl nyomkövetési** letölti a nyomkövetés a Visual Studio.

## <a name="use-analytics-data-for-server"></a>Analitikai adatok használ a kiszolgálóhoz
Application Insights Analytics egy részletes lekérdező nyelv, amely lehetővé teszi, hogy az Application Insights által gyűjtött összes adat elemzése biztosít.  Ezzel a kérelem és teljesítményadatokat mélyreható elemzéseket végrehajtásához.

1. Térjen vissza a művelet részletei panel, és az elemzés gombra.

    ![Elemzés gomb](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Application Insights Analytics egy lekérdezést az egyes nézetek a panel nyílik meg.  Ezeket a lekérdezéseket is futtathatja, ahogy vannak, vagy módosíthatja a követelményeket.  Az első lekérdezés ehhez a művelethez adott idő alatt az időtartam látható.

    ![Elemzés](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Lassú Ügyfélműveletek azonosítása
Mellett optimalizálása érdekében a kiszolgáló folyamatok azonosítása, az Application Insights elemezheti az ügyfélböngészők szempontjából.  Ez segít az ügyfél összetevők esetleges továbbfejlesztési lehetőségeinek azonosítása, és akkor is azonosítani a problémákat a különböző böngészők vagy más helyre.

1. Válassza ki **böngésző** alatt **vizsgálat** összefoglaló böngésző megnyitásához.  Ez az alkalmazás szempontjából a böngésző különböző telemetries visual összegzését tartalmazza.

    ![A böngésző összefoglaló](media/app-insights-tutorial-performance/browser-summary.png)

2.  Görgessen le a **Mik a leglassabb lapok?**.  A lapok listája látható az alkalmazáshoz, amely a leghosszabb idő betöltése ügyfelek készített.  Ezeket az információkat, amelyek a legjelentősebb mértékben a felhasználó a weblapokat meglátogató rangsorolására használhatja.
3.  A lap megnyitásához kattintson a **nézet lapon** panel.  A példában a **/FabrikamProd** lap egy túlzott átlagos időtartam látható.  A **nézet lapon** panel ezen a lapon, beleértve a különböző időtartam címtartományok részletes információkat ismerteti.

    ![Lapmegtekintés](media/app-insights-tutorial-performance/page-view.png)

4.  Kattintson a legmagasabb időtartama, hogy mi ezek a kérelmek részleteit.  Kattintson az egyes megtekintésére vonatkozó kérés el a lapot, beleértve a böngésző és a helyére kérő ügyfél részleteit.  Ez az információ meghatározásához, hogy vannak-e teljesítmény problémák bizonyos különböző típusú ügyféltől nyújt segítséget.

    ![Kérelem adatai](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Az ügyfél analitikai adatok használata
Az a kiszolgáló teljesítményének összegyűjtött adatok, például az Application Insights elérhetővé összes ügyféladatot mélyreható elemzéseket Analytics segítségével.

1. Térjen vissza a böngészőhöz összefoglaló, majd kattintson az Analytics ikonra.

    ![Elemzés ikon](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Application Insights Analytics egy lekérdezést az egyes nézetek a panel nyílik meg. Az első lekérdezés megjeleníti az időtartamot, a másik oldalon nézetek adott idő alatt.

    ![Elemzés](media/app-insights-tutorial-performance/client-analytics.png)

3.  Intelligens diagnosztika lehetővé teszi az Application Insights Analytics, amely azonosítja az adatok egyedi kombinációját.  Az intelligens diagnosztika pont vonaldiagram gombra a azonos lekérdezés futtatása nélkül a azt jelzi, hogy az anomáliadetektálási okozott.  Rekordok részleteit a lekérdezés a Megjegyzés szakaszban jelennek meg, hogy azonosíthassa azokat, amelyek miatt a túlzott időtartama Lapmegtekintések tulajdonságait.

    ![Intelligens diagnosztika](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a futásidejű kivételek azonosítása, hogy értékről a következő oktatóanyag áttekintésével megismerheti, hogyan hibák válaszul riasztások létrehozásához.

> [!div class="nextstepaction"]
> [Alkalmazás állapotának riasztás](app-insights-tutorial-alert.md)

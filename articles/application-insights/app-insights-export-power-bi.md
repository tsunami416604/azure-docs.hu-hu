---
title: Az Azure Application Insights szolgáltatásból a Power bi-bA exportálása |} A Microsoft Docs
description: Analytics-lekérdezések a Power bi-ban is megjeleníthetők.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: mbullwin
ms.openlocfilehash: b4d87ab62503bee1ff54ba9713f7c49d09a2e547
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015066"
---
# <a name="feed-power-bi-from-application-insights"></a>Az Application Insights szolgáltatásból a Power BI hírcsatorna
[Power bi-ban](https://www.powerbi.com/) üzleti eszközök együttese, amely segít az adatok elemzése és elemzéseket oszthat meg. Gazdag irányítópultok érhetők el a minden eszközön. Számos más forrásból, beleértve az elemzési lekérdezések az adatok is kombinálhatók [Azure Application Insights](app-insights-overview.md).

Az Application Insights-adatok exportálása Power bi-hoz három módszer van:

* [**Exportálja az elemzési lekérdezések**](#export-analytics-queries). Ez az előnyben részesített módszere. Minden írási lekérdezés szeretne, majd exportálhatja, és a Power bi-ban. Ez a lekérdezés állíthat be egy irányítópultot, valamint minden egyéb adatot.
* [**A folyamatos exportálás és az Azure Stream Analytics**](../azure-monitor/app/export-stream-analytics.md). Ez a módszer akkor hasznos, ha azt szeretné, hogy hosszú ideig tárolja az adatait. Ha nem rendelkezik egy kibővített adatok megőrzési követelmény, exportálás analytics lekérdezési módszert használja. A folyamatos exportálás és a Stream Analytics magában foglalja a több munkahelyi beállításához és további indextárolási terheléssel jár.
* [**A Power BI adapter**](#power-pi-adapter). A diagramok készletét parancspéldány előre definiált, de a más forrásokból is hozzáadhat a saját lekérdezéseket.

> [!NOTE]
> A Power BI-adaptert már **elavult**. Ez a megoldás az előre definiált diagramok statikus szakaszához lekérdezések fel van töltve. Nem rendelkezik a lekérdezések szerkesztése és az adatok bizonyos tulajdonságait függően lehetséges, a Power bi-ba, a sikeres kapcsolat, de nincs adat nem üres. Kizárási feltétel szoftveresen kötött a lekérdezés beállított okozza. Amíg ez a megoldás lehet, hogy továbbra is működni egyes ügyfelek számára, az adapter flexiblity hiánya miatt az ajánlott megoldás az, hogy használja a [ **exportálja az elemzési lekérdezés** ](#export-analytics-queries) funkciót.

## <a name="export-analytics-queries"></a>Analytics-lekérdezések exportálása
Ezt az útvonalat, vagy használati tölcsérek exportálhat bármely Analytics-lekérdezés írása, és teszi majd exportálja, amelyek a Power BI-irányítópultra. (Is hozzáadhat az irányítópulthoz, a csatoló által létrehozott.)

### <a name="one-time-install-power-bi-desktop"></a>Egy alkalommal: Power BI Desktop telepítése
Az Application Insights-lekérdezés importálásához a Power BI asztali verzióját használja. Ezután közzéteheti a weben vagy a Power BI-felhő munkaterületre. 

Telepítés [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Analytics-lekérdezések exportálása
1. [Nyissa meg a Analytics és a lekérdezés írása](../azure-monitor/log-query/get-started-portal.md).
2. Tesztelje, és pontosítsa a lekérdezést, mindaddig, amíg az eredmények elégedett. Győződjön meg arról, hogy a lekérdezés megfelelően fut az Analyticsben exportálás előtt.
3. Az a **exportálása** menüben válassza a **Power bi-ban (M)**. Mentse a fájlt.
   
    ![Képernyőkép, Analytics, az Exportálás menü kiemelésével](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. A Power BI Desktopban, válassza ki a **adatok lekérése** > **üres lekérdezés**. Ezt követően a Lekérdezésszerkesztő alatt **nézet**válassza **speciális szerkesztő**.

    Az exportált M nyelvi parancsfájl illessze be a speciális szerkesztő.

    ![Képernyőkép a Power BI Desktopban, a speciális szerkesztő kiemelésével](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

5. Ahhoz, hogy a Power BI Azure eléréséhez, akkor előfordulhat, hogy adja meg hitelesítő adatait. Használat **szervezeti fiók** kattintva jelentkezhet be Microsoft-fiókjával.
   
    ![A Power BI lekérdezés beállításai képernyőkép párbeszédpanel](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Ha a hitelesítő adatok ellenőrzése van szüksége, használja a **adatforrás-beállítások** menüparancs a Lekérdezésszerkesztő. Győződjön meg róla, Azure-t, amely eltérhet a hitelesítő adatait a Power bi-ban használt hitelesítő adatok megadásához.
6. Válasszon olyan vizualizációt, a lekérdezés, és jelölje ki a mezőket az x tengely, az y tengely és a dimenzió szegmentálja.
   
    ![Képernyőkép a Power BI Desktopban Vizualizációk beállítások](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
7. A jelentés közzététele a Power BI-felhő munkaterületre. Itt egy szinkronizált verziót is beágyazása más weblapok.
   
    ![Képernyőkép a Power BI Desktop közzététel gomb kiemelésével](./media/app-insights-export-power-bi/publish-power-bi.png)
8. A beállítások lapon egy ütemezett frissítés beállítása, illetve manuálisan frissítse a jelentést, időközönként.

### <a name="export-a-funnel"></a>A tölcsér exportálása
1. [Győződjön meg arról, a tölcsér](usage-funnels.md).
2. Válassza ki **Power bi-ban**.

   ![Képernyőfelvétel: a Power BI gomb](./media/app-insights-export-power-bi/button.png)

3. A Power BI Desktopban, válassza ki a **adatok lekérése** > **üres lekérdezés**. Ezt követően a Lekérdezésszerkesztő alatt **nézet**válassza **speciális szerkesztő**.

   ![Képernyőfelvétel: a Power BI Desktopban, üres lekérdezés gomb kiemelésével](./media/app-insights-export-power-bi/blankquery.png)

   Az exportált M nyelvi parancsfájl illessze be a speciális szerkesztő. 

   ![Képernyőkép a Power BI Desktopban, a speciális szerkesztő kiemelésével](./media/app-insights-export-power-bi/advancedquery.png)

4. Válassza ki az elemeket a lekérdezésből, és válassza ki a tölcsér Vizualizációk.

   ![Képernyőkép a Power BI Desktopban Vizualizációk beállítások](./media/app-insights-export-power-bi/selectsequence.png)

5. Módosítsa a címet a értelmezhető legyen, és tegye közzé a jelentését a Power BI felhőalapú munkaterületén. 

   ![Képernyőkép a Power BI Desktopban az kiemelt cím módosítása](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Hitelesítő adatok vagy az adatkészlet méretének kapcsolatos hibák léphetnek fel. Íme néhány információt, mi a teendő kapcsolatos ezeket a hibákat.

### <a name="unauthorized-401-or-403"></a>Jogosulatlan (401-es vagy 403-as)
Ez akkor fordulhat elő, ha a frissítési token nem lett frissítve. Próbálja ki ezeket a lépéseket, győződjön meg arról, hogy továbbra is hozzáférhetnek a:

1. Jelentkezzen be az Azure Portalra, és győződjön meg arról, hogy az erőforrás eléréséhez.
2. Próbálja meg frissíteni a hitelesítő adatok az irányítópulton.

 Ha rendelkezik hozzáféréssel, és a hitelesítő adatok frissítése nem működik, nyisson egy támogatási jegyet.

### <a name="bad-gateway-502"></a>Hibás átjáró (502)
Ez általában okozza az Analytics-lekérdezések, amelyek túl sok adatot adja vissza. Próbáljon kisebb időtartományt a lekérdezés. 

Ha az adatkészlethez megadott elemzési lekérdezésből származó csökkentése nem felel meg a követelményeknek, fontolja meg a [API](https://dev.applicationinsights.io/documentation/overview) lekérni egy nagyobb adatkészletet. Íme az M-lekérdezés Exportálás az API-val való konvertálása.

1. Hozzon létre egy [API-kulcs](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Frissítse a Power BI M szkriptet, és cserélje le az Azure Resource Manager URL-címet az Application Insights API által exportált Analytics.
   * Cserélje le  **https://management.azure.com/subscriptions/...**
   *  **https://api.applicationinsights.io/beta/apps/...**
3. Végül frissítse a hitelesítő adatok alapszintű, és az API-kulcsot használja.

**Meglévő parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Frissített parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Tudnivalók a mintavételezésről
Az alkalmazás által küldött adatok mennyiségétől függően előfordulhat, hogy használni kívánt az adaptív mintavételezés funkció, amely csak a telemetria százalékát küldi. Ugyanez igaz akkor is, ha manuálisan állította mintavételi az SDK vagy betöltéskor. [További tudnivalók a mintavételezésről](../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>(Elavult) a Power BI-adapter
Ez a módszer létrehoz egy teljes irányítópultot telemetria. A kezdeti adatkészlet parancspéldány előre definiált, de további adatokat adhat.

### <a name="get-the-adapter"></a>Az adapter lekérése
1. Jelentkezzen be a [Power bi-ban](https://app.powerbi.com/).
2. Nyissa meg **adatok lekérése** ![képernyőképe a GetData ikonra, a bal alsó sarokban](./media/app-insights-export-power-bi/001.png), **szolgáltatások**.

    ![Képernyőképek az Application Insights-adatforrás lekérése](./media/app-insights-export-power-bi/002.png)

3. Válassza ki **Letöltés most** mellett az Application Insights.

   ![Képernyőképek az Application Insights-adatforrás lekérése](./media/app-insights-export-power-bi/003.png)
4. Adja meg az Application Insights-erőforrás adatait, majd **bejelentkezési**.

    ![Képernyőkép az Application Insights-adatforrás lekérése](./media/app-insights-export-power-bi/005.png)

     Ez az információ az Application Insights áttekintés ablaktábláján található:

     ![Képernyőkép az Application Insights-adatforrás lekérése](./media/app-insights-export-power-bi/004.png)

5. Nyissa meg az újonnan létrehozott Application Insights Power BI alkalmazás.

6. Várjon néhány percig is importálja az adatokat.

    ![Képernyőfelvétel: a Power BI-adapter](./media/app-insights-export-power-bi/010.png)

Módosíthatja az irányítópulton, az Application Insights-diagramok kombinálásával más forrásokból, valamint az elemzési lekérdezések. Megjelenik a további diagramokat, a Vizualizációk katalógusban, és minden egyes diagram is megadhatja a paraméterrel rendelkezik.

Az első importálás után az irányítópult és a jelentések továbbra is naponta frissülnek. Szabályozhatja, hogy az adatkészlet frissítési ütemezését.

## <a name="next-steps"></a>További lépések
* [Power bi-ban – ismerje meg,](https://www.powerbi.com/learning/)
* [Elemzések – oktatóanyag](../azure-monitor/log-query/get-started-portal.md)


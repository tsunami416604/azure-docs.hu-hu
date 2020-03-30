---
title: Exportálás a Power BI-ba az Azure Application Insightsból | Microsoft dokumentumok
description: Az Analytics-lekérdezések megjelenhetnek a Power BI-ban.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e17ca6e07ec76f0a7a1cb04f7aa13619fb9970c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663997"
---
# <a name="feed-power-bi-from-application-insights"></a>Power BI becsatornázása az Application Insightsból
[A Power BI](https://www.powerbi.com/) üzleti eszközök gyűjteménye, amely segít az adatok elemzésében és az elemzések megosztásában. Gazdag irányítópultok minden eszközön elérhetők. Számos forrásból származó adatokat egyesíthet, például az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)analytics-lekérdezéseit.

Az Application Insights-adatok Power BI-ba történő exportálásának három módja van:

* [**Analytics-lekérdezések exportálása**](#export-analytics-queries). Ez az elsődleges módszer. Írjon bármilyen lekérdezést, és exportálja a Power BI-ba. Ezt a lekérdezést irányítópulton helyezheti el, más adatokkal együtt.
* [**Folyamatos exportálás és Az Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). Ez a módszer akkor hasznos, ha az adatokat hosszú ideig szeretné tárolni. Ha nem rendelkezik kiterjesztett adatmegőrzési követelménnyel, használja az exportelemzési lekérdezési módszert. A folyamatos exportálás és a Stream Analytics több munkát igényel a beállításhoz és a további tárolási többletterheléshez.
* **Power BI-adapter**. A diagramok készlete előre definiált, de más forrásokból is hozzáadhat saját lekérdezéseket.

> [!NOTE]
> A Power BI-adapter elavulttá **vált.** A megoldás előre definiált diagramjait statikus, nem szerkeszthető lekérdezések töltik fel. Nem tudja ezeket a lekérdezéseket szerkeszteni, és az adatok bizonyos tulajdonságaitól függően lehetséges, hogy a Power BI-val való kapcsolat sikeres legyen, de az adatok nem vannak feltöltve. Ez a kódolt lekérdezésben beállított kizárási feltételeknek köszönhető. Bár ez a megoldás továbbra is működhet egyes ügyfelek számára, az adapter rugalmasságának hiánya miatt az ajánlott megoldás az [**export Analytics lekérdezési**](#export-analytics-queries) funkció használata.

## <a name="export-analytics-queries"></a>Elemzési lekérdezések exportálása
Ez az útvonal lehetővé teszi, hogy bármilyen Önnek megfelelő Analytics-lekérdezést írjon, vagy exportáljon a Használati csatornákból, majd exportálja azt egy Power BI-irányítópultra. (Hozzáadhatja az adapter által létrehozott irányítópulthoz.)

### <a name="one-time-install-power-bi-desktop"></a>Egyszeri: a Power BI Desktop telepítése
Az Application Insights-lekérdezés importálásához a Power BI asztali verzióját használja. Ezután közzéteheti azt az interneten vagy a Power BI felhőbeli munkaterületén. 

A [Power BI Desktop telepítése](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Analytics-lekérdezés exportálása
1. [Nyissa meg az Analytics szolgáltatást, és írja meg a lekérdezést.](../../azure-monitor/log-query/get-started-portal.md)
2. Tesztelje és finomítsa a lekérdezést, amíg meg nem elégedett az eredménnyel. Az exportálás előtt győződjön meg arról, hogy a lekérdezés megfelelően fut az Analytics szolgáltatásban.
3. Az **Exportálás** menüben válassza a **Power BI (M)** parancsot. Mentse a szövegfájlt.
   
    ![Képernyőkép az Analytics programról, kiemelve az Exportálás menüvel](./media/export-power-bi/analytics-export-power-bi.png)
4. A Power BI Desktopban válassza az Üres adatok > **lekérdezésének** **beszereznie**lehetőséget. Ezután a lekérdezésszerkesztő **Nézet**területén válassza a **Speciális szerkesztő**lehetőséget.

    Illessze be az exportált M Language parancsfájlt a Speciális szerkesztőbe.

    ![Képernyőkép a Power BI Desktopról, kiemelt Speciális szerkesztővel](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Ahhoz, hogy a Power BI hozzáférhessen az Azure-hoz, előfordulhat, hogy meg kell adnia a hitelesítő adatokat. A **Microsoft-fiókkal** való bejelentkezéshez szervezeti fiókkal jelentkezz be.
   
    ![Képernyőkép a Power BI lekérdezési beállítások párbeszédpaneléről](./media/export-power-bi/power-bi-import-sign-in.png)

    Ha ellenőriznie kell a hitelesítő adatokat, használja a lekérdezésszerkesztő **Adatforrás beállításai** menüparancsát. Ügyeljen arra, hogy adja meg az Azure-hoz használt hitelesítő adatokat, amelyek eltérhetnek a Power BI hitelesítő adataitól.
6. Válasszon egy vizualizációt a lekérdezéshez, és jelölje ki az x tengely, az y tengely és a szegmentálásdimenzió mezőit.
   
    ![Képernyőkép a Power BI Asztali képi megjelenítési beállításairól](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Tegye közzé jelentését a Power BI felhőbeli munkaterületén. Innen beágyazhat egy szinkronizált verziót más weboldalakba.
   
    ![Képernyőkép a Power BI Desktop ról, kiemelve a Közzététel gombbal](./media/export-power-bi/publish-power-bi.png)
8. Frissítse a jelentést időközönként manuálisan, vagy állítson be egy ütemezett frissítést a beállítások lapon.

### <a name="export-a-funnel"></a>Csatorna exportálása
1. [Készítsd el a tölcsér](../../azure-monitor/app/usage-funnels.md).
2. Válassza a **Power BI**lehetőséget.

   ![Képernyőkép a Power BI gombról](./media/export-power-bi/button.png)

3. A Power BI Desktopban válassza az Üres adatok > **lekérdezésének** **beszereznie**lehetőséget. Ezután a lekérdezésszerkesztő **Nézet**területén válassza a **Speciális szerkesztő**lehetőséget.

   ![Képernyőkép a Power BI Desktop ról, kiemelve az Üres lekérdezés gomb](./media/export-power-bi/blankquery.png)

   Illessze be az exportált M Language parancsfájlt a Speciális szerkesztőbe. 

   ![Képernyőkép a Power BI Desktopról, kiemelt Speciális szerkesztővel](./media/export-power-bi/advancedquery.png)

4. Jelöljön ki elemeket a lekérdezésből, és válasszon csatornamegjelenítést.

   ![Képernyőkép a Power BI Asztali képi megjelenítési beállításairól](./media/export-power-bi/selectsequence.png)

5. Módosítsa a címet, hogy az tartalmas legyen, és tegye közzé a jelentést a Power BI felhőbeli munkaterületén. 

   ![Képernyőkép a Power BI Desktopról, kiemelve a címmódosítást](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Előfordulhat, hogy hibák at hitelesítő adatokkal vagy az adatkészlet méretével kapcsolatos hibák. Íme néhány információ arról, hogy mi a teendő ezekkel a hibákkal kapcsolatban.

### <a name="unauthorized-401-or-403"></a>Jogosulatlan (401 vagy 403)
Ez akkor fordulhat elő, ha a frissítési jogkivonat nem lett frissítve. Próbálkozzon az alábbi lépésekkel, hogy továbbra is rendelkezik-e hozzáféréssel:

1. Jelentkezzen be az Azure Portalon, és győződjön meg arról, hogy az erőforrás eléréséhez.
2. Próbálja meg frissíteni az irányítópult hitelesítő adatait.
3. Próbálja meg törölni a gyorsítótárat a PowerBI Desktopról.


   Ha van hozzáférése, és a hitelesítő adatok frissítése nem működik, kérjük, nyisson meg egy támogatási jegyet.

### <a name="bad-gateway-502"></a>Rossz átjáró (502)
Ezt általában egy Analytics-lekérdezés okozza, amely túl sok adatot ad vissza. Próbáljon kisebb időtartományt használni a lekérdezéshez. 

Ha az Analytics-lekérdezésből származó adatkészlet csökkentése nem felel meg az Ön igényeinek, fontolja meg az [API](https://dev.applicationinsights.io/documentation/overview) használatát egy nagyobb adatkészlet lehívásához. Az M-Query exportálását az alábbiak szerint konvertálhatja az API használatára.

1. Hozzon létre egy [API-kulcsot.](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)
2. Frissítse az Analytics szolgáltatásból exportált Power BI M parancsfájlt úgy, hogy lecseréli az Azure Resource Manager URL-címét az Application Insights API-ra.
   * Https **cseréje:\//management.azure.com/subscriptions/...**
   * **-val,\/https: /api.applicationinsights.io/beta/apps/...**
3. Végül frissítse a hitelesítő adatokat alapszintűre, és használja az API-kulcsot.

**Meglévő parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Frissített parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Mintavétel – be
Az alkalmazás által küldött adatok mennyiségétől függően érdemes lehet használni az adaptív mintavételi funkciót, amely csak a telemetriai adatok egy százalékát küldi. Ugyanez igaz, ha manuálisan beállította a mintavételezést az SDK-ban vagy a betöltéskor. [További információ a mintavételezésről.](../../azure-monitor/app/sampling.md)

## <a name="power-bi-adapter-deprecated"></a>Power BI-adapter (elavult)
Ez a módszer létrehoz egy teljes irányítópultot a telemetriai adatok az Ön számára. A kezdeti adatkészlet előre definiált, de további adatokat adhat hozzá.

### <a name="get-the-adapter"></a>Az adapter beszereznie
1. Jelentkezzen be a [Power BI-ba](https://app.powerbi.com/).
2. Az ![ **Adatok beszerezése** képernyőkép](./media/export-power-bi/001.png)megnyitása a GetData ikonról a Bal alsó sarokban , **Szolgáltatások**.

    ![Az Application Insights adatforrásból való betekintés képernyőképei](./media/export-power-bi/002.png)

3. Válassza **a Beszerezni most** lehetőséget az Application Insights csoportban.

   ![Az Application Insights adatforrásból való betekintés képernyőképei](./media/export-power-bi/003.png)
4. Adja meg az Application Insights-erőforrás részleteit, majd **jelentkezzen be.**

    ![Képernyőkép: Get from Application Insights adatforrás](./media/export-power-bi/005.png)

     Ez az információ az Application Insights áttekintése ablaktáblán található:

     ![Képernyőkép: Get from Application Insights adatforrás](./media/export-power-bi/004.png)

5. Nyissa meg az újonnan létrehozott Application Insights Power BI alkalmazást.

6. Várjon egy-két percet az adatok importálásához.

    ![Képernyőkép a Power BI adapterről](./media/export-power-bi/010.png)

Szerkesztheti az irányítópultot, kombinálva az Application Insights-diagramokat más források éval, valamint az Analytics-lekérdezésekkel. A vizualizációs galériában több diagramot is megkaphat, és minden diagramhoz beállítható paraméterek is találhatók.

A kezdeti importálás után az irányítópult és a jelentések naponta frissülnek. Szabályozhatja az adatkészlet frissítési ütemezését.

## <a name="next-steps"></a>További lépések
* [Power BI – Tanulás](https://www.powerbi.com/learning/)
* [Elemzési oktatóanyag](../../azure-monitor/log-query/get-started-portal.md)


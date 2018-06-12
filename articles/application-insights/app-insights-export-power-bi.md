---
title: A Power bi-bA az Azure Application Insights exportálása |} Microsoft Docs
description: A Power BI elemzési lekérdezések olvasható.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: dee3313082fbe75d76bf27105979cf7e869fafad
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294122"
---
# <a name="feed-power-bi-from-application-insights"></a>Az Application Insights hírcsatorna a Power bi-ban
[A Power BI](http://www.powerbi.com/) üzleti eszközök szükséges, amely segít az adatok elemezheti és megoszthatja az elemzések csomagja. Gazdag az irányítópultok olyan rendelkezésre álljanak minden eszközön. Számos más forrásból, beleértve az elemzési lekérdezések adatok kombinálhatja [Azure Application Insights](app-insights-overview.md).

Az Application Insights adatainak exportálása a Power bi-ban három javasolt módszer áll rendelkezésre. Használhatja őket külön-külön vagy együtt.

* [**A Power BI adapter**](#power-pi-adapter). Állítsa be egy teljes irányítópult telemetriai adatot az alkalmazásból. Előre definiált diagramok készletét, de más forrásból is hozzáadhat a saját lekérdezések.
* [**Elemzési lekérdezések exportálásáról**](#export-analytics-queries). Írás a lekérdezés szeretne, és exportálja a Power bi-bA. A lekérdezés írás Analytics segítségével, vagy a használati tölcsérek a írja azokat. Ez a lekérdezés elhelyezheti az irányítópulton, egyéb adatokat.
* [**A folyamatos exportálás és az Azure Stream Analytics**](app-insights-export-stream-analytics.md). Ez a módszer akkor hasznos, ha az adatokat hosszú ideig megtartja. Ha ezt elmulasztja, a más módszerek valamelyikével, mert a további munkahelyi beállítása magában foglalja.

## <a name="power-bi-adapter"></a>A Power BI-adapter
Ez a módszer létrehoz egy teljes irányítópult telemetriai adatot. A kezdeti adatkészlet előre definiált, de további adatokat adhat.

### <a name="get-the-adapter"></a>Az adapter beolvasása
1. Jelentkezzen be [a Power BI](https://app.powerbi.com/).
2. Nyissa meg **adatok**, **szolgáltatások**, majd **az Application Insights**.
   
    ![Képernyőképeket az beszerzése az Application Insights-adatforrás](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Adja meg az Application Insights-erőforrás adatait.
   
    ![Képernyőkép az beszerzése az Application Insights-adatforrás](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Várjon egy percet, amíg az adatok, importálandók.
   
    ![Képernyőfelvétel a Power BI-adapter](./media/app-insights-export-power-bi/010.png)

Az irányítópult az Application Insights diagramok kombinálásának más forrásokból, valamint elemzési lekérdezések szerkesztheti. Kaphat további diagramok a képi megjelenítés gyűjteményben, és minden egyes diagram beállítható paraméterrel rendelkezik.

A kezdeti importálás után az irányítópulton és a jelentések is naponta. Szabályozhatja, hogy a frissítési ütemezés az adatkészlettel.

## <a name="export-analytics-queries"></a>Elemzési lekérdezések exportálása
Ez az útvonal írás Analytics lekérdezés, vagy használati tölcsérek exportálása, és teszi majd exportálja, amelyek a Power BI-irányítópultot. (Adhat hozzá a csatoló által létrehozott irányítópult.)

### <a name="one-time-install-power-bi-desktop"></a>Egy alkalommal: telepítse a Power BI Desktop
Az Application Insights lekérdezés importálásához a Power BI asztali verzióját használja. Majd közzéteheti azt az interneten vagy a Power BI felhő munkaterületet. 

Telepítés [a Power BI Desktopban](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Az elemzési lekérdezések exportálása
1. [Nyissa meg a elemzés és a lekérdezés írása](app-insights-analytics-tour.md).
2. Tesztelje, pontosítsa a lekérdezést, amíg megfelelőnek találja az eredményeket. Győződjön meg arról, hogy a lekérdezés megfelelően fut Analytics exportálás előtt.
3. Az a **exportálása** menüben válasszon **Power BI (M)**. Mentse a fájlt.
   
    ![Képernyőkép az elemzés, exportálás menüvel kiemelve](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. Válassza ki a Power BI Desktopban, **adatok beolvasása** > **üres lekérdezés**. Ezt követően a a Lekérdezésszerkesztő alatt **nézet**, jelölje be **speciális szerkesztő**.

    Illessze be az exportált M nyelvi parancsfájlt a speciális szerkesztőbe.

    ![Képernyőfelvétel a Power BI Desktop, speciális szerkesztővel kiemelve](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Ahhoz, hogy a Power BI Azure eléréséhez, lehetséges, hogy hitelesítő adatok. Használjon **szervezeti fiók** a Microsoft-fiókkal bejelentkezni.
   
    ![A Power BI lekérdezés beállításai képernyőkép párbeszédpanel](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Ha a hitelesítő adatok ellenőrzéséhez szükség, akkor a **adatforrás-beállítások** menüparancs a lekérdezés-szerkesztő. Ne használja az Azure-ba, elképzelhető, hogy a hitelesítő adatait a Power BI eltérő hitelesítő adatok megadása.
2. Válassza ki a lekérdezést a képi megjelenítés, és válassza ki azokat a mezőket az x tengely, y tengely és szegmentálja a dimenzió.
   
    ![Képernyőfelvétel a Power BI Desktop vizualization beállítások](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. A jelentés közzététele a Power BI felhő munkaterületen. Ott egy szinkronizált verziót is beágyazása más weblapokat.
   
    ![Képernyőfelvétel a Power BI Desktop, a kiemelt Publish gombbal](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Manuálisan frissítse a jelentést, időközönként, vagy állítson be egy ütemezett frissítés a beállítások lapon.

### <a name="export-a-funnel"></a>A tölcsér exportálása
1. [Ellenőrizze a tölcsér](usage-funnels.md).
2. Válassza ki **a Power BI**. 

   ![Képernyőfelvétel a Power BI gomb](./media/app-insights-export-power-bi/button.png)
   
3. Válassza ki a Power BI Desktopban, **adatok beolvasása** > **üres lekérdezés**. Ezt követően a a Lekérdezésszerkesztő alatt **nézet**, jelölje be **speciális szerkesztő**.

   ![Képernyőfelvétel a Power BI Desktop, az üres lekérdezés gomb](./media/app-insights-export-power-bi/blankquery.png)

   Illessze be az exportált M nyelvi parancsfájlt a speciális szerkesztőbe. 

   ![Képernyőfelvétel a Power BI Desktop, speciális szerkesztővel kiemelve](./media/app-insights-export-power-bi/advancedquery.png)

4. Válassza ki az elemeket a lekérdezésből, és válassza a tölcsér képi megjelenítés.

   ![Képernyőfelvétel a Power BI Desktop vizualization beállítások](./media/app-insights-export-power-bi/selectsequence.png)

5. Módosítsa a címet abba, hogy a jelentéssel bíró, és a jelentés közzététele a Power BI felhő munkaterületen. 

   ![Képernyőfelvétel a Power BI Desktop, a kiemelt címmódosítás](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Hitelesítő adatok vagy az adatkészlet méretének vonatkozó hibák jelentkezhetnek. Íme néhány Mi a teendő, ezek a hibák kapcsolatos információt.

### <a name="unauthorized-401-or-403"></a>Jogosulatlan (401-es vagy 403-as)
Ez akkor fordulhat elő, ha a frissítési jogkivonat nem lett frissítve. Próbálja meg ezeket a lépéseket, győződjön meg arról, hogy továbbra is hozzáfér a:

1. Jelentkezzen be az Azure-portálra, és győződjön meg arról, hogy az erőforrás végezheti el.
2. Próbálja meg frissíteni az irányítópult hitelesítő adatait.

 Ha Ön rendelkezik hozzáféréssel, és a hitelesítő adatok frissítése nem működik, nyisson egy támogatási jegy.

### <a name="bad-gateway-502"></a>Hibás átjáró (502-es)
Ezt általában az Analytics-lekérdezés túl sok adat visszaadó okozza. Próbálja meg a lekérdezés egy kisebb időtartományt használatával. 

Ha csökkenti az Analytics-lekérdezés érkező adatkészlet nem felel meg a követelményeknek, érdemes lehet a [API](https://dev.applicationinsights.io/documentation/overview) való lekérésére nagyobb adatkészletet. Ez az API-t használó M-lekérdezés exportálás konvertálása.

1. Hozzon létre egy [API-kulcs](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. A Power BI M parancsfájlt, amely azáltal, hogy az Azure Resource Manager URL-címet az Application Insights API Analytics exportált frissítése.
   * Cserélje le  **https://management.azure.com/subscriptions/...**
   * a  **https://api.applicationinsights.io/beta/apps/...**
3. Végül frissítse a hitelesítő adatokat egyszerű, és az API-kulcsot használ.
  

**Meglévő parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Frissített parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Mintavételi kapcsolatos
Ha az alkalmazás nagy mennyiségű adatot küld, előfordulhat, hogy használni kívánt az adaptív mintavételi funkció, amely csak a telemetriai adatok százaléka küld. Ugyanez igaz Ha állított mintavételi manuálisan vagy az SDK-t vagy a adatfeldolgozást. [További információ a mintavételi](app-insights-sampling.md).


## <a name="next-steps"></a>További lépések
* [A Power BI - információ](http://www.powerbi.com/learning/)
* [Elemzés oktatóanyag](app-insights-analytics-tour.md)


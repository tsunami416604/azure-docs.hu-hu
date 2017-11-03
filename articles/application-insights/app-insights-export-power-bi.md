---
title: "Az Application Insights Power bi-bA exportálása |} Microsoft Docs"
description: "A Power BI elemzési lekérdezések olvasható."
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: fe708b14fac971d18d95fd1619907023ec35af89
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="feed-power-bi-from-application-insights"></a>Az Application Insights hírcsatorna a Power bi-ban
[A Power BI](http://www.powerbi.com/) üzleti analytics eszközöket tartalmazza, amelyek segítséget nyújtanak a csomagok elemzéséhez és insights megosztani. Gazdag az irányítópultok olyan rendelkezésre álljanak minden eszközön. Számos más forrásból, beleértve az elemzési lekérdezések adatok kombinálhatja [Azure Application Insights](app-insights-overview.md).

Az Application Insights adatainak exportálása a Power bi-ban három javasolt módszer áll rendelkezésre. Használhatja őket külön-külön vagy együtt.

* [**A Power BI adapter** ](#power-pi-adapter) -állítson be egy teljes irányítópult telemetriai adatot az alkalmazásból. Előre definiált diagramok készletét, de más forrásból is hozzáadhat a saját lekérdezések.
* [**Elemzési lekérdezések exportálásáról** ](#export-analytics-queries) -ír lekérdezés Analytics segítségével, és exportálja a Power bi-bA. Ez a lekérdezés elhelyezheti az egyéb adatokat irányítópulton.
* [**A folyamatos exportálás és a Stream Analytics** ](app-insights-export-stream-analytics.md) -ebbe beletartozik a további munkahelyi beállítása. Ez akkor hasznos, ha az adatokat hosszú ideig megtartja. Ellenkező esetben a többi módszer használata ajánlott.

## <a name="power-bi-adapter"></a>A Power BI-adapter
Ez a módszer létrehoz egy teljes irányítópult telemetriai adatot. A kezdeti adatkészlet előre definiált, de további adatokat adhat.

### <a name="get-the-adapter"></a>Az adapter beolvasása
1. Jelentkezzen be [a Power BI](https://app.powerbi.com/).
2. Nyissa meg **adatok**, **szolgáltatások**, **Application insights szolgáltatással**
   
    ![Az Application Insights adatforrás beszerzése](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Adja meg az Application Insights-erőforrás adatait.
   
    ![Az Application Insights adatforrás beszerzése](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Várjon egy percet, amíg az adatok, importálandók.
   
    ![A Power BI-adapter](./media/app-insights-export-power-bi/010.png)

Az irányítópult az Application Insights diagramok kombinálásának más forrásokból, valamint elemzési lekérdezések szerkesztheti. Nincs a képi megjelenítés gyűjteménye, ahol kaphat további diagramokat, és minden egyes diagram egy beállítható paraméterrel rendelkezik.

A kezdeti importálás után az irányítópulton és a jelentések is naponta. Szabályozhatja, hogy a frissítési ütemezés az adatkészlettel.

## <a name="export-analytics-queries"></a>Elemzési lekérdezések exportálása
Ez az útvonal írás Analytics lekérdezés tetszés, és teszi majd exportálja, amelyek a Power BI-irányítópultot. (Adhat hozzá a csatoló által létrehozott irányítópult.)

### <a name="one-time-install-power-bi-desktop"></a>Egy alkalommal: telepítse a Power BI Desktop
Az Application Insights lekérdezés importálásához a Power BI asztali verzióját használja. De majd közzéteheti azt az interneten vagy a Power BI felhő munkaterületet. 

Telepítés [a Power BI Desktopban](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Az elemzési lekérdezések exportálása
1. [Nyissa meg a elemzés és a lekérdezés írása](app-insights-analytics-tour.md).
2. Tesztelje, pontosítsa a lekérdezést, amíg megfelelőnek találja az eredményeket.

   **Győződjön meg arról, hogy a lekérdezés megfelelően fut Analytics exportálás előtt.**
3. Az a **exportálása** menüben válasszon **Power BI (M)**. Mentse a fájlt.
   
    ![A Power BI lekérdezés exportálása](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. A Power BI Desktop select **adatok beolvasása, üres lekérdezés** , majd a lekérdezés-szerkesztő, alatt **nézet** kiválasztása **speciális lekérdezés-szerkesztő**.

    Illessze be az exportált M nyelvi parancsfájl azokat a speciális lekérdezés-szerkesztő.

    ![Speciális lekérdezés-szerkesztő](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Előfordulhat, hogy meg kell adnia hitelesítő adatait ahhoz, hogy a Power BI Azure eléréséhez. "Szervezeti fiókkal" a Microsoft-fiókkal bejelentkezni.
   
    ![Engedélyezze a Power BI az Application Insights-lekérdezés futtatható Azure szükséges hitelesítő adatok megadására](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (Ellenőrizze a hitelesítő adatokat kell, ha a paranccsal az adatforrás-beállítások menü a lekérdezés-szerkesztő. Ügyeljen arra, használhatja az Azure-ba, elképzelhető, hogy a hitelesítő adatait a Power BI eltérő hitelesítő adatok megadása.)
2. Válassza ki a lekérdezést a képi megjelenítés, és válassza ki azokat a mezőket az x tengely, y tengely és szegmentálja a dimenzió.
   
    ![Válassza ki a képi megjelenítés](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. A jelentés közzététele a Power BI felhő munkaterületen. Ott egy szinkronizált verziót is beágyazása más weblapokat.
   
    ![Válassza ki a képi megjelenítés](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Manuálisan frissítse a jelentést, időközönként, vagy állítson be egy ütemezett frissítés a beállítások lapon.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="401-or-403-unauthorized"></a>401-es vagy 403-as nem engedélyezett 
Ez akkor fordulhat elő, ha a refesh token nem lett frissítve. Próbálja meg ezeket a lépéseket, hogy továbbra is hozzáfér. Ha rendelkezik hozzáféréssel és refershing a hitelesítő adatok nem működik, nyisson egy támogatási jegy.

1. Jelentkezzen be az Azure portálra, és győződjön meg arról, hogy van-e hozzáférési az erőforrás
2. Próbálja meg frissíteni a hitelesítő adatokat az irányítópult

### <a name="502-bad-gateway"></a>502 Hibás átjáró
Ezt általában az Analytics-lekérdezés túl sok adat visszaadó okozza. T érdemes kipróbálni! kisebb időtartomány használata vagy a [ezelőtt](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#ago) vagy [startofweek/startofmonth](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#startofweek) funkciót csak [projekt](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#project-operator) szükséges mezők.

Ha csökkenti az Analytics-lekérdezés érkező adatkészlet nem felel meg a követelményeknek, érdemes lehet használni a [API](https://dev.applicationinsights.io/documentation/overview) való lekérésére nagyobb adatkészletet. Az alábbiakban az API-t használja az M-lekérdezés exportálás konvertálása utasításokat.

1. Hozzon létre egy [API-kulcs](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)
2. A Power BI M parancsfájlt, amely az ARM URL-cím helyett a AI API Analytics exportált frissítése (lásd az alábbi példa)
   * Cserélje le **https://management.azure.com/subscriptions/...**
   * a **https://api.applicationinsights.io/beta/apps/...**
3. Végül frissítse a hitelesítő adatokat egyszerű, és az API-kulcs használata
  

**Meglévő parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Frissített parancsfájl**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Mintavételi kapcsolatos
Az alkalmazás nagy mennyiségű adatot küld, ha az adaptív mintavételi funkció fog működni, és csak százalékaként a telemetriai adatok küldése. Ugyanez igaz Ha állított mintavételi manuálisan vagy az SDK-t vagy a adatfeldolgozást. [További tudnivalók a mintavételezésről.](app-insights-sampling.md)


## <a name="next-steps"></a>Következő lépések
* [A Power BI - információ](http://www.powerbi.com/learning/)
* [Elemzés oktatóanyag](app-insights-analytics-tour.md)


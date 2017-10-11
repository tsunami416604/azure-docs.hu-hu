---
title: "Azure Application Insights-folyamatok automatizálása a Logic Apps segítségével."
description: "Ismerje meg, hogyan gyorsan automatizálhatja ismételhető folyamatokat a Logic Apps alkalmazást az Application Insights-összekötő hozzáadásával."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: bwren
ms.openlocfilehash: 36df5bc0a019f4197d40fd6fa5a2a9957820c8b4
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Az Application Insights-folyamatok automatizálása a Logic Apps segítségével

Tegye veszi észre magát a azonos lekérdezések ismételten futó ellenőrizze, hogy a szolgáltatás megfelelően működik-e a telemetriai adatok? Van szüksége a lekérdezéseket, a trendek és rendellenességeket kereséshez automatizálását, és majd a felhasználókat ezekbe a csoportokba a saját munkafolyamatok építhetők fel? Az Azure Application Insights-összekötő (előzetes verzió) Logic Apps az ideális eszközt erre a célra.

Ezt az integrációt a automatizálhat egysoros kód írása nélkül számos folyamatokat. Egy logikai alkalmazást az Application Insights-összekötővel gyorsan a bármely Application Insights folyamat automatizálásához hozhat létre. 

Hozzáadhat további műveleteket is. A Logic Apps szolgáltatás az Azure App Service elérhetővé több száz műveletek. Például a logikai alkalmazás, automatikusan küldjön e-mailben értesítést vagy hozzon létre egy hibajelentést a Visual Studio Team Services. Is használhatja az egyik rendelkezésre álló több [sablonok](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) Ha szeretné felgyorsítani a logikai alkalmazás létrehozásának folyamatán. 

## <a name="create-a-logic-app-for-application-insights"></a>Az Application Insights logikai alkalmazás létrehozása

Ebben az oktatóanyagban elsajátíthatja, hogy a csoport attribútumok Analytics autocluster algoritmust használ az adatok webalkalmazás logikai alkalmazás létrehozása. A folyamat automatikusan elküldi az eredményeket e-mailben, hogyan használhatja az alkalmazásokhoz kapcsolódó elemzések elemzés és a Logic Apps együtt egy példát. 

### <a name="step-1-create-a-logic-app"></a>1. lépés: Logikai alkalmazás létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az a **új** ablaktáblán válassza előbb **Web + mobil**, majd válassza ki **logikai alkalmazás**.

    ![Új logikai alkalmazás ablak](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>2. lépés: A Logic Apps alkalmazást eseményindító létrehozása
1. Az a **Logic App Designer** ablakban, a **indítsa el az általános eseményindító**, jelölje be **ismétlődési**.

    ![Logic App-Tervező ablak](./media/automate-with-logic-apps/logicapp2.png)

2. Az a **gyakoriság** mezőben válassza **nap** , majd a a **időköz** mezőbe írja be **1**.

    ![Logic App Designer "Ismétlődési" ablak](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>3. lépés: Új Application Insights művelet
1. Kattintson a **új lépés**, és kattintson a **művelet hozzáadása**.

2. Az a **művelet kiválasztását** keresési mezőbe, írja be **Azure Application Insights**.

3. A **műveletek**, kattintson a **Azure Application Insights – megjelenítése Analytics lekérdezés előnézeti**.

    ![Logic App Designer "Művelet kiválasztása" ablak](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4. lépés: Csatlakozás az Application Insights-erőforrás

E lépés elvégzése után az alkalmazás Azonosítóját és API-kulcs az erőforrás kell. Helyreállíthatók Azure-portálról, az alábbi ábrán látható módon:

![Alkalmazás azonosítója az Azure-portálon](./media/automate-with-logic-apps/appid.png) 

Adjon meg egy nevet a kapcsolatot, az alkalmazás Azonosítóját és az API-kulcsot.

![Logic App Tervező folyamata kapcsolat ablak](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5. lépés: Adja meg a Analytics lekérdezés és a diagram típusát
A következő példában a lekérdezés a sikertelen kérelmek kiválasztja a legutóbbi napon belül, és korrelálja őket a kivételeket, amelyek a művelet részeként történt. Elemzés felel meg a sikertelen kérelmek, operation_Id azonosítója alapján. A lekérdezés eredménye majd felosztja a autocluster algoritmus használatával. 

A saját lekérdezések létrehozása, ellenőrizze, hogy megfelelően működnek az Analytics csak a folyamat hozzáadni azt.

1. Az a **lekérdezés** mezőben adja meg a következő Analytics-lekérdezés: 

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

2. Az a **diagramtípus** mezőben válassza **Html tábla**.

    ![Elemzés lekérdezés konfigurációs ablak](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>6. lépés: az e-mail üzenetek küldéséhez a logikai alkalmazás konfigurálása

1. Kattintson a **új lépés**, majd válassza ki **művelet hozzáadása**.

2. Írja be a keresőmezőbe, **Office 365 Outlook**.

3. Kattintson a **Office 365 Outlook – az e-mailek küldése**.

    ![Az Office 365 Outlook kiválasztása](./media/automate-with-logic-apps/flow2b.png)

4. Az a **egy e-mailek küldése** ablakban tegye a következőket:

   a. Írja be a címzett e-mail címét.

   b. Írja be az e-mail tárgyát.

   c. Kattintson bárhova a **törzs** mezőbe, majd válassza a dinamikus tartalom megnyíló menüben jobb **törzs**.

   d. Kattintson a **speciális beállítások megjelenítése**.

      ![Az Office 365 Outlook konfiguráció](./media/automate-with-logic-apps/flow5.png)

5. A dinamikus tartalom menü tegye a következőket:

    a. Válassza ki **melléklet neve**.

    b. Válassza ki **melléklet tartalom**.
    
    c. Az a **HTML** mezőben válassza **Igen**.

      ![Az Office 365 e-mailek konfigurálására szolgáló képernyőn](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>7. lépés: Mentéséhez, és tesztelje a Logic Apps alkalmazást
* Kattintson a **mentése** menti a módosításokat.

A logikai alkalmazás működéséhez megvárhatja, vagy futtassa a logikai alkalmazás azonnal kiválasztásával **futtatása**.

![Logikai alkalmazás létrehozása képernyő](./media/automate-with-logic-apps/step7.png)

Amikor a Logic Apps alkalmazást futtat, a címzettek, az e-mailek listában megadott kap egy e-mailt, amely a következőképpen néznek:

![Logic app e-mailt](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Következő lépések

- További tudnivalók: létrehozása [elemzési lekérdezések](app-insights-analytics-using.md).
- További információ [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->






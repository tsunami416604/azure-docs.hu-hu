---
title: "A Microsoft Flow Azure Application Insights-folyamatok automatizálása"
description: "Ismerje meg, hogyan használhatja a Microsoft Flow gyorsan automatizálása ismételhető az Application Insights-összekötő használatával."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: a1d2787626ed8fa71e3e4e9921ffb8a4680014cb
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Az összekötővel Azure Application Insights-folyamatok automatizálása a Microsoft Flow

Tegye veszi észre magát a azonos lekérdezések ismételten futó ellenőrizze, hogy a szolgáltatás megfelelően működik-e a telemetriai adatok? Van szüksége a lekérdezéseket, a trendek és rendellenességeket kereséshez automatizálását, és majd a felhasználókat ezekbe a csoportokba a saját munkafolyamatok építhetők fel? Az Azure Application Insights-összekötő (előzetes verzió) Microsoft Flow az ideális eszközt ezekből a célokból.

Ez az integráció mostantól automatizálhatja számos folyamatok egysoros kód írása nélkül. Az Application Insights műveletet hoz létre egy folyamatot, miután a folyamat automatikusan futtatja az Application Insights Analytics lekérdezés. 

Hozzáadhat további műveleteket is. Microsoft Flow elérhetővé több száz műveletek. Használhatja például a Microsoft Flow automatikusan e-mailben értesítést küldeni, vagy hozzon létre egy hibajelentést a Visual Studio Team Services. Is használhatja az egyik a több [sablonok](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) , amelyek a Microsoft Flow-összekötő. Ezek a sablonok felgyorsíthatja a folyamat létrehozásának folyamatán. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Hozzon létre egy folyamatot az Application Insights

Ebben az oktatóanyagban elsajátíthatja egy folyamatot, amely a csoport attribútumok Analytics automatikus-fürt algoritmust használ az adatok egy webalkalmazás létrehozása lesz. A folyamat automatikusan elküldi az eredményeket e-mailben, hogyan használhatja a Microsoft Flow és az Application Insights Analytics együtt egy példát. 

### <a name="step-1-create-a-flow"></a>1. lépés: A folyamat létrehozása
1. Jelentkezzen be [Microsoft Flow](http://flow.microsoft.com), majd válassza ki **saját Forgalomáramlás**.
2. Kattintson a **a folyamat létrehozása üres**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>2. lépés: A folyamat eseményindító létrehozása
1. Válassza ki **ütemezés**, majd válassza ki **ütemezés - ismétlődési**.
2. Az a **gyakorisága** mezőben válassza **nap**, majd a a **időköz** adja meg a **1**.

    ![Microsoft Flow eseményindító párbeszédpanel](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>3. lépés: Új Application Insights művelet
1. Kattintson a **új lépés**, és kattintson a **művelet hozzáadása**.
2. Keresse meg **Azure Application Insights**.
3. Kattintson a **Azure Application Insights – megjelenítése Analytics lekérdezés előnézeti**.

    ![Analytics lekérdezési ablakban futtassa](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4. lépés: Csatlakozás az Application Insights-erőforrás

E lépés elvégzése után az alkalmazás Azonosítóját és API-kulcs az erőforrás kell. Helyreállíthatók Azure-portálról, az alábbi ábrán látható módon:

![Alkalmazás azonosítója az Azure-portálon](./media/app-insights-automate-with-flow/appid.png) 

- Adjon meg egy nevet a hálózati kapcsolatot, valamint az alkalmazás Azonosítóját és API-kulcsát.

    ![Microsoft Flow kapcsolat ablak](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5. lépés: Adja meg a Analytics lekérdezés és a diagram típusát
Ez a példa lekérdezés a sikertelen kérelmek kiválasztása a legutóbbi napon belül, és korrelálja őket a kivételeket, amelyek a művelet részeként történt. Elemzés korrelálja őket operation_Id azonosítója alapján. A lekérdezés eredménye majd felosztja a autocluster algoritmus használatával. 

A saját lekérdezések létrehozása, ellenőrizze, hogy megfelelően működnek az Analytics csak a folyamat hozzáadni azt.

- Adja hozzá a következő Analytics-lekérdezés, és válassza a HTML-tábla diagram típusát. 

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
    
    ![Elemzés lekérdezés konfigurációs ablak](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>6. lépés: az e-mail üzenetek küldéséhez a folyamat konfigurálása

1. Kattintson a **új lépés**, és kattintson a **művelet hozzáadása**.
2. Keresse meg **Office 365 Outlook**.
3. Kattintson a **Office 365 Outlook – az e-mailek küldése**.

    ![Az Office 365 Outlook kiválasztási ablaka](./media/app-insights-automate-with-flow/flow2b.png)

4. Az a **egy e-mailek küldése** ablakban tegye a következőket:

   a. Írja be a címzett e-mail címét.

   b. Írja be az e-mail tárgyát.

   c. Kattintson bárhova a **törzs** mezőbe, majd válassza a dinamikus tartalom megnyíló menüben jobb **törzs**.

   d. Kattintson a **speciális beállítások megjelenítése**.

    ![Az Office 365 Outlook konfiguráció](./media/app-insights-automate-with-flow/flow5.png)

5. A dinamikus tartalom menü tegye a következőket:

    a. Válassza ki **melléklet neve**.

    b. Válassza ki **melléklet tartalom**.
    
    c. Az a **HTML** mezőben válassza **Igen**.

    ![Az Office 365 e-mailek konfigurációs ablak](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>7. lépés: Mentéséhez és a folyamat tesztelése
- Az a **folyamat nevének** mezőbe, vegye fel a folyamat nevét, majd kattintson **folyamat létrehozása**.

    ![Adatfolyam-létrehozási ablak](./media/app-insights-automate-with-flow/flow8.png)

Megvárhatja működéséhez, ez a művelet, vagy a folyamat, azonnal futtathatja [fut az eseményindítót az igény szerinti](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Ha a folyamat fut, a címzettek, az e-mailek listában megadott kapnak e-mailt, amely a következőképpen néznek:

![Példa e-mailre](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Következő lépések

- További tudnivalók: létrehozása [elemzési lekérdezések](app-insights-analytics-using.md).
- További információ [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->






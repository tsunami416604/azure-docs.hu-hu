---
title: Az Azure Application Insights Microsoft Flow-folyamatok automatizálása
description: Ismerje meg, hogyan használhatja fel a Microsoft Flow megismételhető folyamatok gyors automatizálása az Application Insights-összekötő használatával.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: cbb9cceef68febec0af991c3d8d5df3e3404f1c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834050"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Az összekötő Azure Application Insights folyamatok automatizálása a Microsoft Flow

Tegye észre magát ismételten ugyanazon lekérdezéseket futtat, ellenőrizze, hogy a szolgáltatás megfelelően működik-e a telemetriai adatok? Kíváncsi, ezeket a lekérdezéseket, a trendek és rendellenességek keresése automatizálását, és ezután hozzon létre a saját munkafolyamatokat őket? Az Azure Application Insights-összekötő a Microsoft Flow egy a megfelelő eszköz ezekből a célokból.

Ez az integráció, mostantól automatizálhatja számos olyan folyamatokat egyetlen kódsor megírása nélkül. Az Application Insights művelet használatával hoz létre egy folyamatot, miután a folyamat automatikusan futtatja az Application Insights-elemzési lekérdezés. 

További műveleteket is hozzáadhat. Microsoft Flow elérhetővé több száz műveleteket. Használhatja például a Microsoft Flow automatikusan e-mail-értesítés küldése vagy hibajelentés létrehozása az Azure DevOps. Használhatja egy a többhöz [sablonok](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) , melyek a Microsoft Flow-összekötő érhetőek el. Ezek a sablonok gyorsítsa fel a folyamat létrehozásának folyamatán. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Folyamat létrehozása az Application Insights

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy folyamatot, amely a csoport attribútumok Analytics az auto-fürt algoritmust használja az adatok egy webalkalmazás. A folyamat automatikusan elküldi az eredményeket e-mailhez, csupán egy példa, hogyan használhatja a Microsoft Flow és az Application Insights-elemzési együtt. 

### <a name="step-1-create-a-flow"></a>1. lépés: Folyamat létrehozása
1. Jelentkezzen be a [Microsoft Flow](https://flow.microsoft.com), majd válassza ki **saját folyamatok**.
2. Kattintson a **új** majd **üres folyamat létrehozása**.

    ![Új folyamat létrehozása az alapoktól](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>2. lépés: A folyamat eseményindító létrehozása
1. A Build modulban lapon válassza **ütemezés**, majd válassza ki **ütemezés – ismétlődés**.

    ![Válassza ki az ütemezési területen hozhat létre](./media/automate-with-flow/2schedule.png)

1. Az a a **időköz** mezőbe írja be **1**, majd a a **gyakorisága** jelölje ki **nap**.
2. Kattintson a **új lépés**

    ![Állítsa be a belépő gyakorisággal és időközzel ütemezése ismétlődésének](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>3. lépés: Az Application Insights művelet hozzáadása
1. Keresse meg **Azure Application Insights**.
2. Kattintson a **Azure Application Insights - megjelenítése elemzési lekérdezés**.
 
    ![Válasszon ki egy műveletet: Az Azure Application Insights megjelenítése Analytics-lekérdezés](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4. lépés: Csatlakozás az Application Insights-erőforrás

E lépés elvégzése után szükség van egy Alkalmazásazonosítót és a egy API-kulcsot az erőforrás. Kérheti le azokat az Azure Portalról, az alábbi ábrán látható módon:

![Alkalmazás azonosítója az Azure Portalon](./media/automate-with-flow/5apiaccess.png)

![API-kulcsot az Azure Portalon](./media/automate-with-flow/6apikey.png)

- Adjon meg egy nevet a hálózati kapcsolatot, valamint az alkalmazás-azonosító és API-kulcsát.

    ![Microsoft Flow-kapcsolat ablak](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5. lépés: Adja meg az elemzési lekérdezés és a diagram típusát
Ez a példa lekérdezés a sikertelen kérelmek kiválasztja az elmúlt napon belül, és azokat utal. a művelet részeként előforduló kivételek. Analytics hátterében őket műveletazonosítója azonosítója alapján. A lekérdezés és a szegmensek az eredményeket a autocluster algoritmus használatával. 

A saját lekérdezések létrehozásakor győződjön meg arról, hogy megfelelően működnek az Analyticsben előtt hozzáadása a folyamathoz.

- Adja hozzá a következő elemzési lekérdezés, és válassza ki a HTML-táblázat diagram típusát. Válassza ki **új lépés**.

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
    
    ![Elemzési lekérdezés konfigurációs ablaka](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>6. lépés: E-mail küldése a flow konfigurálása

1. Keresse meg **az Office 365 Outlook**.
2. Kattintson a **Office 365 Outlook – e-mail küldése**.

    ![Az Office 365 Outlook-kiválasztási ablaka](./media/automate-with-flow/9outlookaction.png)

1. Az a **e-mail küldése** ablakban tegye a következőket:

   a. Írja be a címzett e-mail-címét.

   b. Írja be az e-mail tárgyát.

   c. Kattintson bárhová a **törzs** mezőbe, majd ezt követően válassza a dinamikus tartalom menü, a jobb oldalon megnyíló **törzse**.

   d. Kattintson a **speciális beállítások megjelenítése**.

    ![Az Office 365 Outlook-konfiguráció](./media/automate-with-flow/10sendemailbody.png)

1. A dinamikus tartalom menü tegye a következőket:

    a. Válassza ki **melléklet neve**.

    b. Válassza ki **melléklet tartalma**.
    
    c. Az a **HTML** jelölje ki **Igen**.

    ![Az Office 365 e-mailek konfigurációs ablaka](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>7. lépés: Mentés és a folyamat tesztelése
- Az a **Folyamatnév** mezőben, adja hozzá a folyamat nevét, és kattintson **mentése**.

    ![Folyamat elnevezése és mentése](./media/automate-with-flow/12nameflow.png)

Várja meg az eseményindító, futtassa ezt a műveletet, vagy azonnal, futtathatja a folyamatot [a trigger futtatása igény szerinti](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

A folyamat futtatásakor a címzettek e-mailek listájában megadott kap egy e-mail-üzenet, amely a következőhöz hasonlóan néz ki:

![Példa e-mailre](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>További lépések

- További információ a létrehozásával [elemzési lekérdezések](../../azure-monitor/log-query/get-started-queries.md).
- Tudjon meg többet [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->






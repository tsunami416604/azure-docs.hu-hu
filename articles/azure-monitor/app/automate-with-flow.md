---
title: Az Azure Application Insights folyamatainak automatizálása Microsoft Flow
description: Megtudhatja, hogyan használhatja a Microsoft Flow az ismételhető folyamatok gyors automatizálására az Application Insights-összekötő használatával.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mbullwin
ms.openlocfilehash: 541d5b70ee56d62831f0947e64b9522e17a07dd9
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194942"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Az Azure Application Insights folyamatainak automatizálása a Microsoft Flow-összekötővel

A telemetria-adatain többször is futtatja ugyanazokat a lekérdezéseket, hogy ellenőrizze, hogy a szolgáltatás megfelelően működik-e? Szeretné automatizálni ezeket a lekérdezéseket a trendek és rendellenességek felderítése érdekében, majd saját munkafolyamatokat felépíteni róluk? Az Azure Application Insights-összekötő a Microsoft Flow számára a megfelelő eszköz ezekhez a célokhoz.

Ezzel az integrációval számos folyamat automatizálható egyetlen sor kód írása nélkül is. Miután egy Application Insights művelettel létrehozott egy folyamatot, a folyamat automatikusan futtatja a Application Insights Analytics-lekérdezést.

További műveleteket is hozzáadhat. Microsoft Flow több száz műveletet tesz elérhetővé. A Microsoft Flow használatával például automatikusan küldhet e-mailes értesítést, vagy létrehozhat egy hibát az Azure DevOps. Használhatja a Microsoft Flow-összekötőhöz elérhető számos [sablon](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) egyikét is. Ezek a sablonok felgyorsítják a folyamatok létrehozásának folyamatát.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Folyamat létrehozása a Application Insightshoz

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan folyamatot, amely az analitikai autocluster algoritmus használatával csoportosítja az attribútumokat egy webalkalmazásban. A folyamat automatikusan e-mailben küldi el az eredményeket, csupán egy példát arra, hogyan használhatja a Microsoft Flow és a Application Insights elemzéseket együtt.

### <a name="step-1-create-a-flow"></a>1\. lépés: Folyamat létrehozása

1. Jelentkezzen be [Microsoft Flowba](https://flow.microsoft.com), majd válassza **a saját folyamatok**lehetőséget.
2. Kattintson az új **, majd ütemezett – üres**lehetőségre.

    ![Új folyamat létrehozása ütemezett üresből](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>2\. lépés: Trigger létrehozása a folyamathoz

1. Az előugró ablakban **hozzon létre egy ütemezett folyamatot**, adja meg a folyamat nevét, és azt, hogy milyen gyakran szeretné futtatni a folyamatot.

    ![Ütemterv ismétlődésének beállítása a gyakoriság és az intervallum beírásával](./media/automate-with-flow/2-schedule.png)

1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="step-3-add-an-application-insights-action"></a>3\. lépés: Application Insights művelet hozzáadása

1. **Application Insights**keresése.
2. Kattintson az **Azure Application Insights – elemzési lekérdezés megjelenítése**lehetőségre.

    ![Válasszon műveletet: Azure Application Insights az elemzési lekérdezés megjelenítése](./media/automate-with-flow/3-visualize.png)

3. Válassza az **Új lépés** lehetőséget.

### <a name="step-4-connect-to-an-application-insights-resource"></a>4\. lépés: Kapcsolódás Application Insights erőforráshoz

A lépés elvégzéséhez szüksége lesz egy alkalmazás-AZONOSÍTÓra és egy API-kulcsra az erőforráshoz. A Azure Portal a következő ábrán látható módon kérheti le:

![Alkalmazás azonosítója a Azure Portal](./media/automate-with-flow/5apiaccess.png)

![API-kulcs a Azure Portal](./media/automate-with-flow/6apikey.png)

Adja meg a kapcsolatok nevét, valamint az alkalmazás AZONOSÍTÓját és az API-kulcsot.

   ![Microsoft Flow a kapcsolatok ablak](./media/automate-with-flow/4-connection.png)

Ha a kapcsolati mező nem jelenik meg azonnal, hanem közvetlenül a lekérdezés beírására kerül, kattintson a mező jobb felső sarkában található három pontra. Ezután válassza a saját kapcsolatok lehetőséget, vagy használjon egy meglévőt.

Kattintson a **Create** (Létrehozás) gombra.

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5\. lépés: Az elemzési lekérdezés és a diagram típusának megadása
Ez a példában szereplő lekérdezés kiválasztja a sikertelen kérelmeket az elmúlt napon, és korrelálja azokat a művelet részeként előforduló kivételekkel. Az elemzések a Műveletazonosítója-azonosító alapján korrelálnak. A lekérdezés ezután az autocluster algoritmus használatával csoportosítja az eredményeket.

Amikor létrehoz egy saját lekérdezést, ellenőrizze, hogy megfelelően működnek-e az Analyticsben, mielőtt hozzáadja azt a folyamathoz.

- Adja hozzá a következő elemzési lekérdezést, és válassza ki a HTML-táblázat típusú diagramot. Ezután válassza az **új lépés**lehetőséget.

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
    
    ![Elemzési lekérdezés konfigurációs ablaka](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>6\. lépés: A folyamat konfigurálása e-mailek küldéséhez

1. Keresse meg az **Office 365 Outlook alkalmazást**.
2. Kattintson **az Office 365 Outlook – E-mail küldése**elemre.

    ![Office 365 Outlook kiválasztási ablak](./media/automate-with-flow/6-outlook.png)

1. Az **E-mail küldése** ablakban:

   a. Adja meg a címzett e-mail-címét.

   b. Írja be az e-mail tárgyát.

   c. Kattintson bárhová a **törzs** mezőben, majd a jobb oldalon megnyíló dinamikus tartalom menüben válassza a **törzs**lehetőséget.

   e. Válassza a **Speciális beállítások megjelenítése** lehetőséget.

1. A dinamikus tartalom menüben:

    a. Válassza ki a **melléklet nevét**.

    b. Válassza ki a **melléklet tartalmát**.
    
    c. A **HTML** mezőben válassza az **Igen**lehetőséget.

    ![Office 365 Outlook-konfiguráció](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>7\. lépés: A folyamat mentése és tesztelése

Kattintson a **Save** (Mentés) gombra.

Megvárhatja, hogy az trigger futtassa ezt a műveletet, vagy kattintson ![a felső részen a fõzõpohárba test Icon test (főzőpohár tesztelése ikon ](./media/automate-with-flow/testicon.png) tesztelése) lehetőségre.

A **teszt**kiválasztása után:

1. Jelölje be **az trigger művelet végrehajtása műveletet**.
2. Válassza a **folyamat futtatása**lehetőséget.

A folyamat futásakor az e-mail-listán megadott címzettek egy e-mail-üzenetet kapnak, amely az alábbihoz hasonló.

![Minta e-mail](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>További lépések

- További információ az [elemzési lekérdezések](../../azure-monitor/log-query/get-started-queries.md)létrehozásáról.
- További információ a [Microsoft Flowról](https://ms.flow.microsoft.com).

<!--Link references-->
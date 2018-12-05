---
title: Az Azure Application Insights-folyamatok automatizálása a Logic Apps használatával.
description: Ismerje meg, milyen gyorsan megismételhető folyamatokat automatizálhat az Application Insights-összekötő hozzáadásával a logikai alkalmazáshoz.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: df7d709b51c864c38b9603d2e8ed02d41217bcfc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879699"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Az Application Insights-folyamatok automatizálása a Logic Apps használatával

Tegye észre magát ismételten a telemetriai adatok, ellenőrizze, hogy a szolgáltatás megfelelően működik-e az azonos adatlekérdezések futtatását? Kíváncsi, ezeket a lekérdezéseket, a trendek és rendellenességek keresése automatizálását, és ezután hozzon létre a saját munkafolyamatokat őket? Az Azure Application Insights-összekötő (előzetes verzió) a Logic Apps egy a megfelelő eszköz erre a célra.

Ez az integráció számos olyan folyamatokat egyetlen kódsor megírása nélkül automatizálható. Létrehozhat egy logikai alkalmazást az Application Insights-összekötővel gyorsan automatizálhatja az Application Insights-folyamatokhoz. 

További műveleteket is hozzáadhat. Az Azure App Service Logic Apps funkcióját elérhetővé több száz műveleteket. Ha például egy logikai alkalmazás használatával automatikusan e-mail-értesítés küldése vagy hibajelentés létrehozása az Azure DevOps. Is használhatja az egyik rendelkezésre álló számos [sablonok](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) felgyorsítani a logikai alkalmazás létrehozásának folyamatán. 

## <a name="create-a-logic-app-for-application-insights"></a>Logikai alkalmazás létrehozása az Application Insights

Ebben az oktatóanyagban elsajátíthatja, hogyan hozhat létre egy logikai alkalmazást, amely az adatok attribútumokat az Analytics autocluster algoritmust használja a webalkalmazás. A folyamat automatikusan elküldi az eredményeket e-mailhez, csupán egy példa a használatáról Application Insights-elemzési és a Logic Apps együtt. 

### <a name="step-1-create-a-logic-app"></a>1. lépés: A logikai alkalmazás létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a **erőforrás létrehozása**válassza **Web + mobil**, majd válassza ki **logikai alkalmazás**.

    ![Új logikai alkalmazás ablakának](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>2. lépés: A logikai alkalmazás eseményindító létrehozása
1. A a **Logikaialkalmazás-Tervező** időszak alatt **kezdje egy gyakori eseményindítóval**, jelölje be **ismétlődési**.

    ![Logic App Designerben ablak](./media/automate-with-logic-apps/logicapp2.png)

1. Az a **gyakorisága** jelölje ki **nap** , majd a **időköz** mezőbe írja be **1**.

    !["Ismétlődés" logikaialkalmazás-Tervező ablak](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>3. lépés: Az Application Insights művelet hozzáadása
1. Kattintson a **új lépés**, és kattintson a **művelet hozzáadása**.

1. Az a **válasszon ki egy műveletet** írja be a keresőmezőbe **Azure Application Insights**.

1. A **műveletek**, kattintson a **Azure Application Insights - elemzési megjelenítése lekérdezés előnézeti**.

    ![Logic App Designerben "Művelet kiválasztása" ablak](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4. lépés: Csatlakozás az Application Insights-erőforrás

E lépés elvégzése után szükség van egy Alkalmazásazonosítót és a egy API-kulcsot az erőforrás. Kérheti le azokat az Azure Portalról, az alábbi ábrán látható módon:

![Alkalmazás azonosítója az Azure Portalon](./media/automate-with-logic-apps/appid.png) 

Adjon meg egy nevet a kapcsolat, az alkalmazás azonosítója és API-kulcsot.

![Logic App Designerben folyamat csatlakozási ablak](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5. lépés: Adja meg az elemzési lekérdezés és a diagram típusa
A következő példában a lekérdezés a sikertelen kérelmek kiválasztja az elmúlt napon belül, és azokat utal. a művelet részeként előforduló kivételek. Analytics utal. a sikertelen kérelmek, műveletazonosítója azonosítója alapján. A lekérdezés és a szegmensek az eredményeket a autocluster algoritmus használatával. 

A saját lekérdezések létrehozásakor győződjön meg arról, hogy megfelelően működnek az Analyticsben előtt hozzáadása a folyamathoz.

1. Az a **lekérdezés** adjon hozzá a következő elemzési lekérdezés: 

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

1. Az a **diagramtípus** jelölje ki **Html-táblázat**.

    ![Elemzési lekérdezés konfigurációs ablaka](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>6. lépés: Konfigurálja a logikai alkalmazás e-mailek küldése

1. Kattintson a **új lépés**, majd válassza ki **művelet hozzáadása**.

1. A Keresés mezőbe írja be a **Office 365 Outlook**.

1. Kattintson a **Office 365 Outlook – e-mail küldése**.

    ![Az Office 365 Outlook kiválasztása](./media/automate-with-logic-apps/flow2b.png)

1. Az a **e-mail küldése** ablakban tegye a következőket:

   a. Írja be a címzett e-mail-címét.

   b. Írja be az e-mail tárgyát.

   c. Kattintson bárhová a **törzs** mezőbe, majd ezt követően válassza a dinamikus tartalom menü, a jobb oldalon megnyíló **törzse**.

   d. Kattintson a **speciális beállítások megjelenítése**.

      ![Az Office 365 Outlook-konfiguráció](./media/automate-with-logic-apps/flow5.png)

1. A dinamikus tartalom menü tegye a következőket:

    a. Válassza ki **melléklet neve**.

    b. Válassza ki **melléklet tartalma**.
    
    c. Az a **HTML** jelölje ki **Igen**.

      ![Az Office 365 e-mailek konfigurációs képernyőjén](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>7. lépés: Mentse, és tesztelje a logikai alkalmazást
* Kattintson a **mentése** a módosítások mentéséhez.

Várja meg az eseményindító a logikai alkalmazás futtatását, vagy futtassa a logikai alkalmazás azonnal kiválasztásával **futtatása**.

![Logikai alkalmazás létrehozás képernyő](./media/automate-with-logic-apps/step7.png)

Amikor a logikai alkalmazás fut, a címzetteket, hogy az e-mailek listában megadott kapnak egy e-mailt, amely a következőhöz hasonlóan néz ki:

![Logikai alkalmazás e-mailt](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>További lépések

- További információ a létrehozásával [elemzési lekérdezések](../azure-monitor/log-query/get-started-queries.md).
- További tudnivalók a [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) szolgáltatásról



<!--Link references-->






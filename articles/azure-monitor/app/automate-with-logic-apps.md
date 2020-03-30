---
title: Az Azure Application Insights-folyamatok automatizálása a Logic Apps használatával
description: Megtudhatja, hogyan automatizálhatja gyorsan az ismételhető folyamatokat az Application Insights-összekötő logikai alkalmazáshoz való hozzáadásával.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 4a0944e661932d86fac75f78c4faf5be751806c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473166"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Az Application Insights-folyamatok automatizálása a Logic Apps használatával

Találja magát többször fut ugyanazt a lekérdezéseket a telemetriai adatok at, hogy ellenőrizze, hogy a szolgáltatás megfelelően működik-e? Ezeket a lekérdezéseket szeretné automatizálni a trendek és anomáliák megkereséséhez, majd saját munkafolyamatokat szeretne köréjük építeni? Az Azure Application Insights-összekötő logic apps a megfelelő eszköz erre a célra.

> [!NOTE]
> Az Azure Application Insights-összekötő tváltotta fel az [Azure Monitor-összekötő,](../platform/logicapp-flow-connector.md) amely integrálva van az Azure Active Directory helyett egy API-kulcsot, és lehetővé teszi, hogy adatokat a Log Analytics-munkaterületről.

Ezzel az integrációval számos folyamatot automatizálhat anélkül, hogy egyetlen kódsort írna. Létrehozhat egy logikai alkalmazást az Application Insights-összekötővel, amely gyorsan automatizálhatja az Application Insights-folyamatokat. 

További műveleteket is hozzáadhat. Az Azure App Service Logic Apps szolgáltatása több száz műveletet tesz elérhetővé. Például egy logikai alkalmazás használatával automatikusan küldhet egy e-mail értesítést, vagy hozzon létre egy hibát az Azure DevOps-ban. A számos rendelkezésre álló [sablon](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) egyikével is felgyorsíthatja a logikai alkalmazás létrehozásának folyamatát. 

## <a name="create-a-logic-app-for-application-insights"></a>Logikai alkalmazás létrehozása az Application Insights számára

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy logikai alkalmazást, amely az Analytics autocluster algoritmust használja a webes alkalmazások adatainak csoportosításához. A folyamat automatikusan elküldi az eredményeket e-mailben, csak egy példa arra, hogyan használhatja együtt az Application Insights Analytics és a Logic Apps alkalmazást. 

### <a name="step-1-create-a-logic-app"></a>1. lépés: Logikai alkalmazás létrehozása
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Kattintson **az Erőforrás létrehozása**gombra, válassza a Web + **Mobil**lehetőséget, majd a **Logikai alkalmazás**lehetőséget.

    ![Új logikai alkalmazásablak](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>2. lépés: Eseményindító létrehozása a logikai alkalmazáshoz
1. A **Logic App Designer** ablakban a **Kezdés egy közös eseményindítóval**csoportban válassza **az Ismétlődés lehetőséget.**

    ![A Logic App Designer ablaka](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Az **Intervallum** mezőbe írja be az **1** mezőt, majd a**Gyakoriság** mezőbe válassza a **Nap**lehetőséget.

    ![Logikai alkalmazástervező "Ismétlődés" ablaka](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>3. lépés: Application Insights-művelet hozzáadása
1. Kattintson **az Új lépés gombra.**

1. A **Művelet** keresőmezőjének beírása az **Azure Application Insights**mezőbe.

1. A **Műveletek csoportban**kattintson az **Azure Application Insights – Visualize Analytics lekérdezés elemre.**

    ![Logikai alkalmazástervező "Művelet kiválasztása" ablak](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4. lépés: Csatlakozás egy Application Insights-erőforráshoz

A lépés végrehajtásához szüksége van egy alkalmazásazonosítóra és egy API-kulcsra az erőforráshoz. Az alábbi ábrán látható módon lekérheti őket az Azure Portalon:

![Alkalmazásazonosító az Azure Portalon](./media/automate-with-logic-apps/5apiaccess.png)

![Alkalmazásazonosító az Azure Portalon](./media/automate-with-logic-apps/6apikey.png)

Adja meg a kapcsolat nevét, az alkalmazásazonosítót és az API-kulcsot.

![Logic App Designer folyamatkapcsolat-ablak](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5. lépés: Az Analytics-lekérdezés és a diagramtípus megadása
A következő példában a lekérdezés kiválasztja a sikertelen kérelmeket az utolsó napon, és korrelálja azokat a művelet részeként történt kivételekkel. Az Analytics a operation_Id azonosító alapján korrelálja a sikertelen kérelmeket. A lekérdezés ezután szegmentálja az eredményeket az autocluster algoritmus használatával. 

Amikor saját lekérdezéseket hoz létre, ellenőrizze, hogy megfelelően működnek-e az Analytics szolgáltatásban, mielőtt hozzáadja a folyamathoz.

1. A **Lekérdezés** mezőbe adja hozzá a következő Analytics-lekérdezést:

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

1. A **Diagramtípus** mezőben válassza a **Html-táblázat elemet.**

    ![Analytics-lekérdezés konfigurációs ablaka](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>6. lépés: A logikai alkalmazás beállítása e-mail küldésére

1. Kattintson **az Új lépés gombra.**

1. A keresőmezőbe írja be az **Office 365 Outlook**kifejezést.

1. Kattintson **az Office 365 Outlook – E-mail küldése**elemre.

    ![Az Office 365 Outlook kiválasztása](./media/automate-with-logic-apps/9sendemail.png)

1. Az **E-mail küldése ablakban** tegye a következőket:

   a. Írja be a címzett e-mail címét.

   b. Írja be az e-mail tárgyát.

   c. Kattintson bárhová a **Törzs** mezőben, majd a jobb oldalon megnyíló dinamikus tartalom menüben válassza a **Törzs**lehetőséget.
    
   d. Kattintson az **Új paraméter hozzáadása** legördülő menüre, és válassza a Mellékletek és HTML lehetőséget.

      ![Az Office 365 Outlook konfigurációja](./media/automate-with-logic-apps/10emailbody.png)

      ![Az Office 365 Outlook konfigurációja](./media/automate-with-logic-apps/11emailparameter.png)

1. A dinamikus tartalom menüben tegye a következőket:

    a. Válassza a **Melléklet neve lehetőséget.**

    b. Válassza a **Melléklet tartalom lehetőséget**.
    
    c. Az **Is HTML (HTML)** mezőben válassza az **Igen**lehetőséget.

      ![Az Office 365 levelezési konfigurációs képernyője](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>7. lépés: A logikai alkalmazás mentése és tesztelése
* A módosítások mentéséhez kattintson a **Mentés** gombra.

Megvárhatja, amíg az eseményindító futtatja a logikai alkalmazást, vagy azonnal futtathatja a logikai alkalmazást a **Futtatás**lehetőség kiválasztásával.

![Logikai alkalmazás létrehozási képernyője](./media/automate-with-logic-apps/13save.png)

Amikor a logikai alkalmazás fut, az e-mail listában megadott címzettek a következő nek tűnő e-mailt kapnak:

![Logikai alkalmazás e-mail üzenete](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>További lépések

- További információ az [Analytics-lekérdezések](../../azure-monitor/log-query/get-started-queries.md)létrehozásáról.
- További tudnivalók a [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) szolgáltatásról



<!--Link references-->






---
title: Az Azure Application Insights folyamatainak automatizálása Logic Apps használatával
description: Megtudhatja, hogyan automatizálható a megismételhető folyamatok gyors automatizálásához, ha hozzáadja a Application Insights-összekötőt a logikai alkalmazáshoz.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/11/2019
ms.openlocfilehash: 95a3577d352b6bc4b56d36b5b79658571a6be1ff
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407522"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Application Insights folyamatok automatizálása Logic Apps használatával

A telemetria-adataiban többször is futtatja ugyanazokat a lekérdezéseket, így ellenőrizhető, hogy a szolgáltatás megfelelően működik-e? Szeretné automatizálni ezeket a lekérdezéseket a trendek és rendellenességek felderítése érdekében, majd saját munkafolyamatokat felépíteni róluk? Az Azure Application Insights-összekötő a Logic Apps a megfelelő eszköz erre a célra.

Ezzel az integrációval számos folyamat automatizálható egyetlen sor kód írása nélkül. Létrehozhat egy logikai alkalmazást az Application Insights-összekötővel, amellyel gyorsan automatizálhatja a Application Insights folyamatokat. 

További műveleteket is hozzáadhat. Azure App Service Logic Apps funkciója több száz műveletet tesz elérhetővé. Egy logikai alkalmazás használatával például automatikusan küldhet e-mail-értesítést, vagy létrehozhat egy hibát az Azure DevOps-ben. Használhatja a számos elérhető [sablon](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) egyikét is, hogy felgyorsítsa a logikai alkalmazás létrehozásának folyamatát. 

## <a name="create-a-logic-app-for-application-insights"></a>Logikai alkalmazás létrehozása Application Insightshoz

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre olyan logikai alkalmazást, amely az analitikai autocluster algoritmus használatával csoportosítja az attribútumokat egy webalkalmazásban. A folyamat automatikusan e-mailben küldi el az eredményeket, csupán egy példát arra, hogyan használhatja Application Insights elemzéseket, és hogyan Logic Apps együtt. 

### <a name="step-1-create-a-logic-app"></a>1\. lépés: logikai alkalmazás létrehozása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Kattintson **az erőforrás létrehozása**elemre, válassza a **web és mobil**lehetőséget, majd válassza a **logikai alkalmazás**lehetőséget.

    ![Új logikai alkalmazás ablak](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>2\. lépés: trigger létrehozása a logikai alkalmazáshoz
1. A **Logic app Designer** ablakban az **Indítás általános eseményindítóval**területen válassza az **Ismétlődés**lehetőséget.

    ![Logic app Designer ablak](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Az **intervallum** mezőbe írja be az **1** , majd a**frekvencia** mezőt, és válassza a **nap**lehetőséget.

    ![Logic app Designer "Ismétlődés" ablak](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>3\. lépés: Application Insights művelet hozzáadása
1. Kattintson az **új lépés**gombra.

1. A **válasszon műveletet** keresőmezőbe írja be az **Azure Application Insights**kifejezést.

1. A **műveletek**területen kattintson az **Azure Application Insights – elemzési lekérdezés megjelenítése**lehetőségre.

    ![Logic app Designer "válasszon egy műveletet" ablak](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4\. lépés: Kapcsolódás Application Insights erőforráshoz

A lépés elvégzéséhez szüksége lesz egy alkalmazás-AZONOSÍTÓra és egy API-kulcsra az erőforráshoz. A Azure Portal a következő ábrán látható módon kérheti le:

![Alkalmazás azonosítója a Azure Portal](./media/automate-with-logic-apps/5apiaccess.png)

![Alkalmazás azonosítója a Azure Portal](./media/automate-with-logic-apps/6apikey.png)

Adja meg a kapcsolatok nevét, az alkalmazás AZONOSÍTÓját és az API-kulcsot.

![A Logic app Designer folyamatának összekapcsolási ablaka](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5\. lépés: az elemzési lekérdezés és a diagram típusának megadása
A következő példában a lekérdezés kijelöli a sikertelen kérelmeket az elmúlt napon belül, és korrelálja azokat a művelet részeként előforduló kivételekkel. Az elemzés a operation_Id azonosítója alapján korrelálja a sikertelen kérelmeket. A lekérdezés ezután az autocluster algoritmus használatával csoportosítja az eredményeket. 

Amikor létrehoz egy saját lekérdezést, ellenőrizze, hogy megfelelően működnek-e az Analyticsben, mielőtt hozzáadja azt a folyamathoz.

1. A **lekérdezés** mezőben adja hozzá a következő elemzési lekérdezést:

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

1. A **diagram típusa** mezőben válassza a **HTML-táblázat**lehetőséget.

    ![Elemzési lekérdezés konfigurációs ablaka](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>6\. lépés: a logikai alkalmazás konfigurálása e-mailek küldéséhez

1. Kattintson az **új lépés**gombra.

1. A keresőmezőbe írja be az **Office 365 Outlook**kifejezést.

1. Kattintson **az Office 365 Outlook – E-mail küldése**elemre.

    ![Office 365 Outlook kiválasztása](./media/automate-with-logic-apps/9sendemail.png)

1. Az **E-mail küldése** ablakban tegye a következőket:

   a. Adja meg a címzett e-mail-címét.

   b. Írja be az e-mail tárgyát.

   c. Kattintson bárhová a **törzs** mezőben, majd a jobb oldalon megnyíló dinamikus tartalom menüben válassza a **törzs**lehetőséget.
    
   d. Kattintson az **új paraméter hozzáadása** legördülő listára, és válassza a mellékletek lehetőséget, és a HTML.

      ![Office 365 Outlook-konfiguráció](./media/automate-with-logic-apps/10emailbody.png)

      ![Office 365 Outlook-konfiguráció](./media/automate-with-logic-apps/11emailparameter.png)

1. A dinamikus tartalom menüben tegye a következőket:

    a. Válassza ki a **melléklet nevét**.

    b. Válassza ki a **melléklet tartalmát**.
    
    c. A **HTML** mezőben válassza az **Igen**lehetőséget.

      ![Office 365 e-mail konfigurációs képernyő](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>7\. lépés: a logikai alkalmazás mentése és tesztelése
* Kattintson a **Mentés** gombra a módosítások mentéséhez.

Megvárhatja, hogy a trigger futtassa a logikai alkalmazást, vagy azonnal futtathatja a logikai alkalmazást a **Futtatás**lehetőség kiválasztásával.

![Logikai alkalmazás létrehozása képernyő](./media/automate-with-logic-apps/13save.png)

A logikai alkalmazás futtatásakor az e-mail-listán megadott címzettek a következőhöz hasonló e-mailt kapnak:

![Logikai alkalmazás e-mail-üzenete](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Következő lépések

- További információ az [elemzési lekérdezések](../../azure-monitor/log-query/get-started-queries.md)létrehozásáról.
- További tudnivalók a [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) szolgáltatásról



<!--Link references-->





